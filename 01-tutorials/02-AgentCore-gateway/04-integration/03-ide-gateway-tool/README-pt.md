# VS Code + AgentCore Gateway: Proxy OAuth Serverless

## Visão Geral

Este documento explica como conectar o **Visual Studio Code com Copilot** ao **Amazon Bedrock AgentCore Gateway** usando um **proxy OAuth serverless** implantado na AWS, com **Atlassian Confluence** como ferramenta backend usando **OAuth 2.0 Authorization Code Grant (3LO)** para acesso delegado pelo usuário.

**Benefício Principal**: Nenhum servidor local necessário. O proxy OAuth e o manipulador de callback rodam como funções Lambda atrás do API Gateway na AWS, permitindo que desenvolvedores conectem o VS Code diretamente a um endpoint na nuvem.

**Nota**: Este exemplo requer `MCP-Protocol-Version: 2025-11-25` que adiciona elicitação de URL, configurado no `mcp.json` do VS Code usando o campo `headers`.

## Arquitetura

![VS Code + AgentCore Gateway Proxy OAuth Serverless](generated-diagrams/vscode-agentcore-serverless-proxy.png)

**Resumo do Fluxo:**
1. VS Code conecta ao Amazon API Gateway (endpoint público) via MCP/HTTP
2. Lambda do Proxy MCP lida com descoberta de metadados OAuth e interceptação de callback
3. Usuário autentica com Cognito via navegador
4. Lambda do Proxy encaminha requisições autenticadas ao AgentCore Gateway com JWT
5. AgentCore Gateway retorna elicitação 3LO quando acesso ao Confluence é necessário
6. Usuário concede consentimento no navegador via OAuth da Atlassian
7. Lambda de Callback recebe código de autorização e chama `CompleteResourceTokenAuth`
8. AgentCore Gateway agora pode chamar a API do Confluence em nome do usuário (tokens cacheados pelo AgentCore Identity)

## Dois Fluxos OAuth

| Fluxo | Propósito | Direção | Quando |
|-------|-----------|---------|--------|
| **Inbound Auth** | VS Code autentica no AgentCore Gateway | VS Code → Cognito → AgentCore Gateway | Na conexão do servidor MCP |
| **Outbound Auth (3LO)** | AgentCore Gateway acessa Confluence em nome do usuário | AgentCore Gateway → Atlassian → Consentimento do usuário | Na primeira chamada de ferramenta do Confluence |

### Tempo de Vida do Token e Persistência do Consentimento

**Com que frequência os usuários serão solicitados para consentimento do Confluence?**

O AgentCore Identity gerencia tokens 3LO automaticamente:
- Quando o usuário completa o 3LO, o AgentCore armazena tanto o token de acesso quanto o token de atualização
- Em chamadas de ferramentas subsequentes, o AgentCore usa o token armazenado — sem interação do usuário
- Quando o token de acesso expira, o AgentCore o atualiza automaticamente usando o token de atualização

**O tempo de vida do consentimento é controlado pela Atlassian (o provedor OAuth), não pelo AgentCore:**
- Tokens de atualização da Atlassian são de longa duração (~90 dias de inatividade)
- Enquanto o token for usado periodicamente, os usuários não serão solicitados novamente
- Novo consentimento é necessário se: (a) o usuário revogar o acesso nas configurações da Atlassian, (b) o token de atualização expirar por inatividade, ou (c) os escopos solicitados pelo aplicativo mudarem

**O escopo `offline_access`** (configurado no notebook) é o que habilita tokens de atualização. Sem ele, os usuários precisariam reautenticar toda vez que o token de acesso expirasse (tipicamente 1 hora).

**Nota**: O Cognito lida apenas com a inbound auth (VS Code → AgentCore Gateway). Os tokens 3LO para o Confluence são gerenciados inteiramente pelo AgentCore Identity.

## Serverless vs Proxy Local

Este exemplo também poderia ser implantado com servidores de callback e proxy locais. Tê-los na nuvem AWS oferece vantagens, detalhadas abaixo.

| Aspecto | Proxy Local (notebook 02) | Proxy Serverless (notebook 03) |
|---------|---------------------------|--------------------------------|
| **Setup** | Executar 2 servidores Python locais | Implantar uma vez via notebook |
| **Experiência do Desenvolvedor** | Iniciar servidores antes de cada sessão | Apenas configurar o VS Code |
| **Endpoint** | `http://127.0.0.1:8080` | `https://<api-id>.execute-api.<region>.amazonaws.com` |
| **Escalabilidade** | Desenvolvedor único | Implantação para toda a equipe |
| **Custo** | Gratuito (local) | Pague por uso (Lambda + API Gateway) |

## Componentes

| Componente | Propósito |
|-----------|---------|
| **Amazon API Gateway** | Endpoint HTTPS público para VS Code (HTTP API) |
| **Lambda do Proxy MCP** | Metadados OAuth, interceptação de callback, proxy de tokens, encaminhamento MCP |
| **Lambda de Callback** | Callbacks OAuth 3LO, `CompleteResourceTokenAuth` |
| **Cognito User Pool** | Tokens JWT para inbound auth |
| **AgentCore Gateway** | Servidor MCP gerenciado pela AWS com alvo Confluence |

**Nota sobre terminologia**: Esta arquitetura usa dois "gateways" diferentes:
- **Amazon API Gateway**: A HTTP API que expõe funções Lambda como endpoint público
- **AgentCore Gateway**: O servidor MCP gerenciado pela AWS que roteia chamadas de ferramentas para o Confluence

## Detalhes Técnicos Principais

### Por que a Arquitetura de Proxy é Necessária

Esta solução usa Amazon API Gateway + funções AWS Lambda como camada de proxy entre o VS Code e o AgentCore Gateway. Esta arquitetura é necessária por duas razões:

1. **Fachada do Servidor de Autorização OAuth**: O cliente MCP do VS Code espera interagir com endpoints OAuth (`/authorize`, `/token`) na URL do servidor MCP. O AgentCore Gateway valida Inbound JWTs, mas não atua como um Servidor de Autorização OAuth. O Lambda do Proxy MCP fornece esta fachada, fazendo proxy de requisições OAuth para o Cognito enquanto lida com interceptação de redirecionamento e gerenciamento de estado. O proxy também serve seus próprios Metadados de Recurso Protegido RFC 9728 (`/.well-known/oauth-protected-resource`) porque o identificador `resource` deve corresponder à URL à qual o cliente se conecta (a URL do proxy), não a URL do Gateway subjacente.

2. **Manipulação de Callback 3LO com Vinculação de Sessão**: Quando um usuário completa o consentimento 3LO (por exemplo, concedendo acesso ao Confluence), o callback OAuth deve ser recebido e a API `CompleteResourceTokenAuth` deve ser chamada com a identidade do usuário para vincular o token. O Lambda de Callback lida com este fluxo. Embora um endpoint de callback 3LO gerenciado não seja atualmente suportado nativamente no AgentCore Gateway, este Lambda fornece a manipulação de callback e vinculação de sessão.

### Cabeçalho MCP-Protocol-Version

O cabeçalho `MCP-Protocol-Version: 2025-11-25` é necessário para elicitação 3LO. Configure no `mcp.json` do VS Code:

```json
{
  "servers": {
    "agentcore-confluence": {
      "type": "http",
      "url": "https://<api-gateway-url>",
      "headers": {
        "MCP-Protocol-Version": "2025-11-25"
      }
    }
  }
}
```

### Resposta de Elicitação 3LO

Quando uma ferramenta requer consentimento OAuth do usuário, o gateway retorna o código de erro `-32042`:

```json
{
  "error": {
    "code": -32042,
    "message": "This request requires more information.",
    "data": {
      "elicitations": [{
        "mode": "url",
        "elicitationId": "...",
        "url": "https://bedrock-agentcore.us-west-2.amazonaws.com/identities/oauth2/authorize?...",
        "message": "Please login to this URL for authorization."
      }]
    }
  }
}
```

## Configuração

### Pré-requisitos
- Python 3.10+
- Credenciais AWS configuradas com permissões para Lambda, API Gateway, Cognito, IAM e Bedrock AgentCore
- Conta Atlassian Cloud com Confluence
- VS Code 1.107+ com GitHub Copilot - Esta versão adiciona suporte para elicitação de URL 3LO

### Passo 1: Criar Aplicação OAuth da Atlassian
1. Acesse https://developer.atlassian.com/console/myapps/
2. Criar → Integração OAuth 2.0
3. Em **Permissões**, adicione estes **escopos granulares** para Confluence:
   - `read:space:confluence`
   - `read:page:confluence`
4. Copie o Client ID e Client Secret
5. Adicione a URL de callback do AgentCore (mostrada após executar o notebook)

**Nota**: O escopo `offline_access` (para tokens de atualização) é um escopo OAuth padrão — você não o configura no console. Ele é automaticamente solicitado pelo AgentCore ao fazer a requisição de autorização.

### Passo 2: Executar o Notebook de Configuração

Execute `01_vscode_agentcore_confluence_serverless.ipynb` para criar:
- API Gateway com integrações Lambda
- Função Lambda do Proxy MCP
- Função Lambda de Callback
- Cognito User Pool com cliente de aplicação
- AgentCore Gateway com auth JWT do Cognito
- Provedor de credenciais da Atlassian
- Alvo Confluence com OAuth 3LO

O notebook exibe a URL do API Gateway e a configuração do VS Code.

### Passo 3: Configurar o VS Code

Adicione ao `.vscode/mcp.json` (valores da saída do notebook):

```json
{
  "servers": {
    "agentcore-confluence": {
      "type": "http",
      "url": "https://<api-gateway-id>.execute-api.<region>.amazonaws.com",
      "headers": {
        "MCP-Protocol-Version": "2025-11-25"
      }
    }
  }
}
```

### Passo 4: Conectar e Usar
1. Recarregue o VS Code
2. Complete o OAuth do Cognito quando solicitado (usuário: `vscode-user`, senha: `TempPassword123!`)
3. Use as ferramentas do Confluence - o consentimento 3LO será acionado no primeiro uso
4. Após conceder o consentimento da Atlassian, tente a chamada da ferramenta novamente

## Solução de Problemas

### "Cannot initiate authorization code grant flow"
**Causa**: O Gateway não está recebendo o cabeçalho `MCP-Protocol-Version: 2025-11-25`.
**Solução**: Adicione `"headers": {"MCP-Protocol-Version": "2025-11-25"}` à sua configuração mcp.json.

### "Client is not enabled for OAuth2.0 flows"
**Causa**: Cliente de aplicação do Cognito sem `AllowedOAuthFlowsUserPoolClient=True`.
**Solução**: Execute novamente o notebook para recriar os recursos.

### "redirect_mismatch" do Cognito
**Causa**: URL de callback não registrada no Cognito.
**Solução**: Certifique-se de que a URL de callback do API Gateway está registrada. Execute novamente o notebook se necessário.

### Erros de timeout do Lambda
**Causa**: Função Lambda expirando durante o encaminhamento MCP.
**Solução**: Aumente o timeout do Lambda no Console AWS ou reimplante com timeout maior.

### 3LO completado mas a ferramenta ainda falha
**Causa**: O VS Code não tenta automaticamente após a conclusão do 3LO.
**Solução**: Invoque a ferramenta novamente após completar o fluxo 3LO no navegador.

## Arquivos

| Arquivo | Descrição |
|---------|-----------|
| `01_vscode_agentcore_confluence_serverless.ipynb` | Notebook de configuração para implantação serverless |
| `lambda/mcp_proxy_lambda.py` | Código fonte do Lambda do Proxy MCP |
| `lambda/callback_lambda.py` | Código fonte do Lambda de Callback 3LO |

## Limpeza

Execute a célula de limpeza no final do notebook, ou execute o Passo 1b em uma nova execução do notebook para excluir:
- API Gateway
- Funções Lambda
- AgentCore Gateway e alvos
- Provedores de credenciais
- Cognito User Pool
- Roles IAM

## Referências

- [Especificação MCP 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25)
- [Documentação MCP do VS Code](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [Documentação do AgentCore Gateway](https://docs.aws.amazon.com/bedrock-agentcore/)
- [Documentação do AWS Lambda](https://docs.aws.amazon.com/lambda/)
- [Documentação do Amazon API Gateway](https://docs.aws.amazon.com/apigateway/)
- [API REST do Confluence v2](https://developer.atlassian.com/cloud/confluence/rest/v2/)
