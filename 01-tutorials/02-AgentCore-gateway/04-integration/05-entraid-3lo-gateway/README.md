# AgentCore MCP Gateway com Autenticação EntraID

Configuração automatizada para um AgentCore MCP Gateway com autenticação de entrada EntraID e 3LO (three-legged OAuth) de saída para acesso delegado pelo usuário a uma API downstream. Inclui um SPA de onboarding de autenticação baseado em navegador para pré-autorizar o acesso.

## Pré-requisitos

- AWS CLI v2 configurado com credenciais para a conta de destino
- Azure CLI (`az`): `brew install azure-cli` (macOS) ou [guia de instalação](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- Node.js 18+ e npm
- CDK CLI: `npm install -g aws-cdk`
- `jq` instalado
- Acesso a um tenant Microsoft Entra ID (CIAM/External ID ou padrão)

## Início Rápido (Automatizado)

O script de configuração automatiza tudo: registros de aplicativos EntraID, provedor de credenciais OAuth, implantação CDK, configuração de URIs de redirecionamento, configuração da especificação OpenAPI e geração de um arquivo env para reimplantações subsequentes.

### 1. Faça login em ambas as nuvens

```bash
# Azure — use sua conta de administrador EntraID
az login --tenant <your-tenant-id> --allow-no-subscriptions

# AWS — certifique-se de que as credenciais estão configuradas
export CDK_DEFAULT_ACCOUNT=<your-aws-account-id>
export CDK_DEFAULT_REGION=<your-region>
```

> `--allow-no-subscriptions` é necessário para tenants CIAM que não possuem uma assinatura Azure.

### 2. Execute o script de configuração

Para um tenant CIAM (External ID):
```bash
cd 05-entraid-3lo-gateway
./scripts/setup.sh \
  --tenant-id <your-tenant-id> \
  --tenant-type ciam \
  --ciam-domain <your-ciam-domain> \
  --region <aws-region> \
  --stack-name <stack-name>
```

Para um tenant EntraID padrão:
```bash
cd 05-entraid-3lo-gateway
./scripts/setup.sh \
  --tenant-id <your-tenant-id> \
  --tenant-type standard \
  --region <aws-region> \
  --stack-name <stack-name>
```

O script irá:
1. Criar o App A (SPA, cliente público) e o App B (Web, cliente confidencial) no EntraID via Graph API
2. Expor os escopos `gateway.access` e `weather.read`
3. Criar o provedor de credenciais OAuth no AgentCore
4. Registrar a URL de callback do provedor de credenciais no App B
5. Implantar a stack CDK
6. Registrar as URIs de redirecionamento do API Gateway no App A
7. Atualizar as URLs de retorno da identidade de carga de trabalho
8. Atualizar a especificação OpenAPI com o endpoint real da API e reimplantar
9. Gerar `.env.<StackName>` para uso com `redeploy-cdk.sh`

### 3. Teste

Abra a URL de onboarding de autenticação exibida ao final da configuração:
```
https://<api-endpoint>/auth
```

## Reimplantação Durante o Desenvolvimento

Após a configuração inicial, use `redeploy-cdk.sh` para iterar sobre o código Lambda, alterações na stack CDK ou atualizações na especificação OpenAPI. Ele lê o arquivo `.env.<StackName>` gerado pela configuração e executa `cdk deploy` com todas as variáveis de contexto corretas.

```bash
# Detecta automaticamente o arquivo env se houver apenas um
./scripts/redeploy-cdk.sh

# Ou especifique explicitamente
./scripts/redeploy-cdk.sh .env.MyStack
```

Não é necessário executar novamente o script de configuração completo para alterações de código.

## Múltiplas Implantações

Para implantar uma segunda instância independente (por exemplo, para testes), adicione `--suffix`:

```bash
./scripts/setup.sh \
  --tenant-id <tenant-id> \
  --tenant-type ciam \
  --ciam-domain <domain> \
  --region eu-west-1 \
  --stack-name MySecondStack \
  --suffix v2
```

O sufixo garante nomes de recursos únicos (IAM roles, API Gateway, Gateway, provedor de credenciais). O provedor OIDC é automaticamente reutilizado se já existir um para o mesmo tenant, e o novo client ID do App A é adicionado à sua lista de audiências.

---

## Tipos de Tenant

A solução suporta ambos os tipos de tenant EntraID:

| Tipo | Host de Autoridade | Quando usar |
|------|---------------------|-------------|
| `ciam` | `<domain>.ciamlogin.com` | Tenants External ID / CIAM |
| `standard` | `login.microsoftonline.com` | Tenants EntraID regulares (corporativos) |

Para tenants CIAM, você deve fornecer `--ciam-domain` (o subdomínio antes de `.ciamlogin.com`).

A diferença principal: tenants CIAM usam `ciamlogin.com` para endpoints de descoberta/token. Usar o host errado causa erros `authorizationCode must not be null` porque o AgentCore tenta trocar o código de autorização no endpoint de token errado.

---

## Criando Usuários de Demonstração

```bash
./scripts/create-demo-user.sh \
  --tenant-id <tenant-id> \
  --domain <tenant-domain> \
  <username> [password]
```

O script de configuração exibe um comando pronto para copiar com tenant-id e domain pré-preenchidos.

O script usa credenciais do `az login` (não são necessárias permissões de aplicativo). A conta logada deve ter a função de User Administrator ou Global Admin.

---

## Testando o Fluxo de Autenticação

### Onboarding de Autenticação (navegador)

1. Abra `https://<api-endpoint>/auth` em uma janela anônima
2. Faça login com a Microsoft (EntraID)
3. O SPA chama `tools/call` com `_meta.rawElicitation` — exibe "Authorization needed" se ainda não estiver autorizado
4. Clique em "Authorize" → página de consentimento do EntraID → aceite
5. A página de callback chama `CompleteResourceTokenAuth` via SigV4 (no navegador)
6. Exibe "Authorization Successful"

### VS Code MCP

Adicione ao `.vscode/mcp.json`:
```json
{
  "servers": {
    "agentcore-weather-entraid": {
      "type": "http",
      "url": "https://<api-endpoint>/mcp"
    }
  }
}
```

Quando um usuário chama uma ferramenta antes de autorizar, o interceptor de resposta reescreve a elicitação em uma mensagem amigável direcionando-o ao SPA de onboarding de autenticação. Após autorizar via SPA, as chamadas de ferramentas funcionam sem elicitação.

---

## Interceptor de Resposta

O Gateway possui um Lambda interceptor de resposta que trata respostas de elicitação (-32042):

- Para VS Code: reescreve a elicitação em uma mensagem amigável "visite o app de onboarding de autenticação", já que o VS Code não consegue lidar com o redirecionamento de consentimento 3LO
- Para o SPA de onboarding de autenticação: passa a elicitação bruta para que o SPA possa extrair a URL de autorização e conduzir o fluxo de consentimento

O SPA sinaliza sua intenção incluindo `_meta: { rawElicitation: true }` na requisição JSON-RPC. O interceptor verifica essa flag e ignora a reescrita.

O interceptor também trata particularidades do Gateway:
- `body: null` em respostas de notificação 202 → retorna dict vazio
- `body` como string JSON → faz parse de volta para dict antes de retornar

---

## O que o Script de Configuração Cria

### No EntraID

| Recurso | Finalidade |
|---------|------------|
| App A (SPA, cliente público) | Identidade compartilhada para VS Code + SPA de onboarding de autenticação. Expõe o escopo `gateway.access`. |
| App B (Web, cliente confidencial) | Servidor de recursos downstream. Expõe o escopo `weather.read`. Segredo do cliente armazenado no AgentCore. |

### Na AWS

| Recurso | Finalidade |
|---------|------------|
| API Gateway HTTP API | Endpoint público para proxy, API de clima e SPA de autenticação |
| Proxy Lambda | Intermediário OAuth, proxy MCP, serve o HTML do SPA |
| Weather API Lambda | Endpoint de dados meteorológicos simulados |
| Elicitation Interceptor Lambda | Reescreve elicitação para VS Code, passa adiante para o SPA |
| AgentCore Gateway | Tratamento do protocolo MCP, autenticação JWT customizada, roteamento de ferramentas |
| Gateway Target (weather-api) | Alvo OpenAPI com provedor de credenciais 3LO |
| OAuth Credential Provider | Armazena credenciais do cliente do App B, gerencia troca de tokens |
| IAM OIDC Provider | Habilita federação STS com JWTs do EntraID |
| IAM Role (auth-onboarding-web-role) | Credenciais temporárias do navegador para chamadas à API do AgentCore |

### Arquivos Gerados

| Arquivo | Finalidade |
|---------|------------|
| `.env.<StackName>` | Variáveis de contexto CDK para `redeploy-cdk.sh` |
| `openapi/weather-api-<StackName>.json` | Especificação OpenAPI específica da implantação |

---

## Solução de Problemas

### `authorizationCode failed to satisfy constraint: Member must not be null`

O provedor de credenciais está usando a URL de descoberta errada. Tenants CIAM requerem `ciamlogin.com`, não `login.microsoftonline.com`. Recrie com `--tenant-type ciam`.

### `Invalid Bearer token` do Gateway

O autorizador do Gateway usa apenas `allowedAudience` (não `allowedClients`). Tokens EntraID v2.0 usam `azp` para o client ID, não `client_id`. O AgentCore valida `allowedClients` contra `client_id`, que não existe em tokens EntraID.

### `Incorrect token audience` na página de callback

A lista de audiências do IAM OIDC provider não inclui o client ID do App A desta implantação. O script de configuração trata isso automaticamente para implantações paralelas, mas se você criou o provedor OIDC manualmente, adicione o client ID: `aws iam add-client-id-to-open-id-connect-provider`.

### `InterceptorException - Received invalid response from interceptor`

O interceptor retornou uma resposta que o Gateway não conseguiu interpretar. Causas comuns: `body` era `null` (deve ser `{}` para respostas vazias), ou `body` era uma string JSON em vez de um dict.

### `AADSTS9010010` durante a troca de tokens

O Lambda proxy remove o parâmetro `resource` das requisições de token. O EntraID v2.0 usa escopos, não recursos. O VS Code envia `resource` conforme RFC 9728, mas o EntraID o rejeita.

### `AADSTS9002327: Tokens issued for the 'Single-Page Application' client-type may only be redeemed via cross-origin requests`

O Lambda proxy adiciona um cabeçalho `Origin` ao chamar o endpoint de token do EntraID. Necessário para resgate de token de SPA (cliente público).

### `tools/list` funciona mas `tools/call` aciona elicitação

Esperado. `tools/list` não requer o token downstream. Apenas `tools/call` força o Gateway a buscar o token de clima, acionando a elicitação se estiver ausente.

### A página de callback exibe "Missing session data"

O JWT não foi encontrado no `sessionStorage`. Inicie o fluxo novamente a partir de `/auth`.

### `EntityAlreadyExistsException` para o provedor OIDC durante o deploy CDK

O IAM OIDC provider é único por URL de emissor por conta. Se estiver implantando múltiplas stacks para o mesmo tenant, o script de configuração detecta e reutiliza automaticamente o provedor existente.

### O servidor MCP do VS Code apresenta erros de autenticação após reimplantações

Credenciais de autenticação de entrada acumuladas de sessões anteriores podem causar erros de estado obsoleto. Abra a paleta de comandos (`F1` ou `⇧⌘P`) e execute "Remove Dynamic Authentication Providers" para limpá-las, depois reconecte.

---

## Referência de Arquitetura

Consulte [docs/out-of-band-outbound-auth.md](./docs/out-of-band-outbound-auth.md) para diagramas de sequência detalhados de todos os fluxos.
