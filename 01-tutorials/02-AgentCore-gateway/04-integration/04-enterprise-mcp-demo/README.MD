# Infraestrutura MCP Empresarial

Infraestrutura AWS de nível empresarial para integração do Model Context Protocol (MCP) com o Amazon Bedrock AgentCore, projetada para integração segura com IDEs (VS Code) com autenticação OAuth 2.0 e controle de acesso baseado em políticas.

![Diagrama de Arquitetura](./assets/architecture.png)

## Visão Geral

Esta infraestrutura CDK implanta uma solução completa de gateway MCP que permite que o VS Code e outros IDEs se conectem de forma segura ao Amazon Bedrock AgentCore através do Model Context Protocol. A solução oferece recursos empresariais incluindo:

- **Autenticação OAuth 2.0** via Amazon Cognito
- **Controle de Acesso Baseado em Políticas** com políticas Cedar personalizadas
- **Interceptação de Requisições/Respostas** para registro e transformação
- **Proteção de PII** usando Bedrock Guardrails
- **Opções Flexíveis de Implantação** (ALB ou API Gateway)
- **Suporte a Domínio Personalizado** com SSL/TLS

## Arquitetura

### Componentes Principais

#### 1. **Camada de Autenticação**
- **Amazon Cognito User Pool**: Gerencia a autenticação de usuários e fluxos OAuth 2.0
- **Lambda de Geração de Token Pré-Autenticação**: Injeta claims personalizados (tags de usuário) nos tokens JWT para avaliação de políticas
- **Clientes OAuth**:
  - Cliente VS Code (Authorization Code Grant com PKCE)

#### 2. **Camada de API Gateway**
Escolha entre duas opções de implantação:
- **Application Load Balancer (ALB)**: Nível de produção com domínios personalizados e SSL/TLS
- **API Gateway HTTP API**: Serverless, econômico para desenvolvimento/testes

#### 3. **Lambda de Proxy MCP**
Componente central que gerencia:
- Endpoints de metadados OAuth (`/.well-known/oauth-*`)
- Fluxo de autorização (`/authorize`, `/callback`, `/token`)
- Registro Dinâmico de Cliente (`/register`)
- Proxy do protocolo MCP para o AgentCore Gateway
- Reescrita de requisições/respostas para integração transparente

#### 4. **Amazon Bedrock AgentCore Gateway**
- Manipulador do protocolo MCP com capacidades de busca semântica
- Suporta múltiplas versões do MCP (2025-03-26, 2025-06-18, 2025-11-25)
- Integra-se com servidores MCP baseados em Lambda
- Aplica autenticação via Cognito
- Aplica decisões do motor de políticas

#### 5. **Lambdas de Servidor MCP**
Funções Lambda de exemplo que implementam ferramentas MCP:
- **Lambda de Clima**: Fornece ferramentas de informações meteorológicas (`get_weather`)
- **Lambda de Inventário**: Fornece ferramentas de gerenciamento de inventário (`get_inventory`)
- **Lambda de Detalhes do Usuário**: Fornece ferramentas de informações do usuário (`get_user_email`, `get_user_cc_number`)

#### 6. **Lambda Interceptor**
- Intercepta requisições e respostas de/para o Gateway
- Permite registro, transformação e lógica de negócios personalizada
- Recebe cabeçalhos de requisição para processamento contextual

#### 7. **Motor de Políticas**
- Motor de políticas personalizado baseado em Cedar implantado via Lambda
- Avalia permissões de usuários com base em claims JWT
- Políticas de exemplo:
  - Usuários administradores podem acessar todas as ferramentas (clima + inventário)
  - Usuários regulares podem acessar apenas ferramentas de clima

#### 8. **Bedrock Guardrails**
- Anonimiza PII em requisições e respostas
- Tipos de entidades PII configurados:
  - **ADDRESS**: Anonimizado na entrada e saída
  - **NAME**: Anonimizado na entrada e saída
  - **EMAIL**: Anonimizado na entrada e saída
  - **CREDIT_DEBIT_CARD_NUMBER**: Bloqueado na entrada e saída
- Aplicado via Lambda interceptor no método `tools/call`
- Aplica políticas de conteúdo com mensagens personalizadas

## Stack Tecnológico

### Infraestrutura
- **AWS CDK** 2.232.2+ (TypeScript)
- **Node.js** 24.x com TypeScript 5.9
- **AWS Bedrock AgentCore** (Alpha) 2.238.0-alpha.0+

### Runtime
- **Python** 3.12 (todas as funções Lambda)
- **Boto3** (AWS SDK para Python)

### Serviços AWS
| Serviço | Finalidade |
|---------|---------|
| Amazon Cognito | Autenticação OAuth 2.0 e gerenciamento de usuários |
| AWS Lambda | Computação serverless para proxy, servidores MCP e motor de políticas |
| Amazon Bedrock AgentCore | Gateway MCP e manipulação de protocolo |
| Application Load Balancer | Roteamento de produção com domínios personalizados (opcional) |
| API Gateway HTTP API | Endpoint de API serverless (opcional) |
| Amazon VPC | Isolamento de rede para implantação com ALB |
| AWS IAM | Gerenciamento de identidade e acesso |
| Amazon Route53 | Gerenciamento de DNS para domínios personalizados |
| AWS Certificate Manager | Certificados SSL/TLS |
| Bedrock Guardrails | Filtragem de conteúdo e proteção de PII |

## Pré-requisitos

1. **Conta AWS** com permissões apropriadas
2. **AWS CLI** configurado com credenciais
3. **Node.js** 18.x ou posterior
4. **AWS CDK** CLI instalado: `npm install -g aws-cdk`
5. **Python** 3.12 (para funções Lambda)

### Para Configuração de Domínio Personalizado (Opcional)
- Zona hospedada no Route53
- Certificado ACM na mesma região
- Nome de domínio disponível

## Implantação

### 1. Instalar Dependências

```bash
cd enterprise-mcp-infra/cdk
npm install
```

### 2. Bootstrap do CDK (Apenas na Primeira Vez)

```bash
cdk bootstrap aws://ACCOUNT-ID/REGION
```

### 3. Configurar Parâmetros de Implantação

Edite `cdk/cdk.context.json` para configurar sua implantação:

#### Opção A: Implantação com ALB e Domínio Personalizado

```json
{
  "deploymentType": "ALB",
  "domainName": "enterprise-mcp",
  "hostedZoneName": "example.com",
  "hostedZoneId": "Z1234567890ABC",
  "certificateArn": "arn:aws:acm:region:account:certificate/xxx"
}
```

#### Opção B: Implantação com API Gateway (URL Padrão)

```json
{
  "deploymentType": "API_GATEWAY",
  "domainName": "",
  "hostedZoneName": "",
  "hostedZoneId": "",
  "certificateArn": ""
}
```

#### Opção C: API Gateway com Domínio Personalizado

```json
{
  "deploymentType": "API_GATEWAY",
  "domainName": "enterprise-mcp.example.com",
  "hostedZoneName": "example.com",
  "hostedZoneId": "Z1234567890ABC",
  "certificateArn": "arn:aws:acm:region:account:certificate/xxx"
}
```

**Parâmetros de Configuração:**

| Parâmetro | Descrição | Obrigatório | Padrão |
|-----------|-----------|-------------|--------|
| `deploymentType` | Tipo de implantação: `ALB` ou `API_GATEWAY` | Sim | `ALB` |
| `domainName` | Nome de domínio personalizado (ex.: `enterprise-mcp` para ALB, ou domínio completo para API Gateway) | Não (apenas API Gateway) | `""` |
| `hostedZoneName` | Nome da zona hospedada no Route53 (ex.: `example.com`) | Apenas com domínio personalizado | `""` |
| `hostedZoneId` | ID da zona hospedada no Route53 (ex.: `Z1234567890ABC`) | Apenas com domínio personalizado | `""` |
| `certificateArn` | ARN do certificado ACM para HTTPS | Apenas com domínio personalizado | `""` |

### 4. Implantar a Stack

```bash
cdk deploy
```

Você também pode sobrescrever valores de contexto via linha de comando:

```bash
cdk deploy -c deploymentType=API_GATEWAY
```

### 5. Salvar as Saídas do CDK

Após a conclusão da implantação, o CDK exibirá valores de configuração importantes. **Copie toda a saída** - você precisará dela para o próximo passo.

Exemplo de saída:
```
EnterpriseMcpInfraStack.UserPoolId = us-east-1_XXXXXXXXX
EnterpriseMcpInfraStack.VSCodeClientId = xxxxxxxxxxxxxxxxxxxx
EnterpriseMcpInfraStack.EndpointUrl = https://enterprise-mcp.example.com
EnterpriseMcpInfraStack.VSCodeMcpConfig = {...}
EnterpriseMcpInfraStack.Gateway = agentcore-mcp-gateway-xxxxx
...
```

## Configuração

### Criar Usuários no Cognito

#### Usando o Script Automatizado (Recomendado)

1. **Edite** `enterprise-mcp-infra/scripts/script.py`:
   - Substitua o conteúdo da variável `output` com as saídas reais do CDK (do passo 4)
   - Personalize a lista de usuários com os endereços de e-mail e senhas desejados

2. **Execute o script** para criar os usuários:
```bash
cd enterprise-mcp-infra/scripts
python script.py
```

O script irá:
- Analisar as saídas do CDK para extrair o User Pool ID
- Criar dois usuários padrão: `vscode-admin@example.com` e `vscode-user@example.com`
- Definir senhas permanentes (sem necessidade de redefinição de senha no primeiro login)
- Ignorar usuários que já existem

**Usuários Padrão Criados:**
- `vscode-admin@example.com` / `TempPassword123!`
- `vscode-user@example.com` / `TempPassword1234!`

#### Criação Manual de Usuários (Alternativa)

Se preferir a criação manual:

```bash
aws cognito-idp admin-create-user \
  --user-pool-id <UserPoolId> \
  --username admin@example.com \
  --user-attributes Name=email,Value=admin@example.com Name=email_verified,Value=true \
  --message-action SUPPRESS

aws cognito-idp admin-set-user-password \
  --user-pool-id <UserPoolId> \
  --username admin@example.com \
  --password 'YourSecurePassword123!' \
  --permanent
```

**Nota**: A Lambda de Geração de Token Pré-Autenticação injeta automaticamente o claim `user_tag` tanto nos tokens de ID quanto nos tokens de acesso com base no endereço de e-mail do usuário:
- `vscode-admin@example.com` recebe `user_tag: "admin_user"`
- Todos os outros usuários recebem `user_tag: "regular_user"`

Este claim é usado pelo Motor de Políticas para aplicar regras de autorização para acesso às ferramentas do gateway.

### Configuração do VS Code

Crie ou atualize `.vscode/mcp.json` no seu workspace:

```json
{
  "servers": {
    "enterprise-mcp-server": {
      "type": "http",
      "url": "https://enterprise-mcp.example.com/mcp",
      "headers": {
        "MCP-Protocol-Version": "2025-11-25"
      }
    }
  }
}
```

## Uso

### 1. Iniciar o VS Code
A extensão MCP detectará automaticamente a configuração e solicitará autenticação.

### 2. Autenticar
- Clique em "Sign In" quando solicitado
- Insira suas credenciais do Cognito
- Complete o fluxo OAuth

### 3. Usar Ferramentas MCP
Uma vez autenticado, você pode usar as ferramentas MCP através do VS Code Copilot ou da extensão MCP:
- Informações meteorológicas: Pergunte sobre o clima atual (todos os usuários autenticados)
- Consultas de inventário: Verifique o inventário de produtos (apenas usuários administradores)
- Detalhes do usuário: Recupere e-mail e informações de cartão de crédito do usuário (todos os usuários autenticados com user_tag)

## Gerenciamento de Políticas

### Entendendo o Motor de Políticas

O motor de políticas usa políticas Cedar para controlar o acesso às ferramentas MCP com base em atributos de usuário injetados nos tokens JWT.

**Políticas de Exemplo** (implantadas por padrão):

```cedar
// Usuários administradores podem acessar ferramentas de inventário e clima
permit (
  principal is AgentCore::OAuthUser,
  action in [AgentCore::Action::"inventory-tool", AgentCore::Action::"weather-tool"],
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  principal.hasTag("user_tag") &&
  principal.getTag("user_tag") == "admin_user"
};

// Usuários regulares podem acessar apenas a ferramenta de clima
permit (
  principal is AgentCore::OAuthUser,
  action in [AgentCore::Action::"weather-tool"],
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  principal.hasTag("user_tag") &&
  principal.getTag("user_tag") == "regular_user"
};

// Todos os usuários com user_tag podem acessar a ferramenta de detalhes do usuário
permit (
  principal is AgentCore::OAuthUser,
  action in [AgentCore::Action::"user-details-tool"],
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  principal.hasTag("user_tag")
};
```

### Personalizando Políticas

A stack implanta três políticas Cedar por padrão. Para personalizar, edite as declarações de política em `cdk/lib/enterprise-mcp-infra-stack.ts`:

```typescript
// Exemplo: Modificar a política de usuário administrador
const policyEngineStatementInventoryTool = `
  permit (
    principal is AgentCore::OAuthUser,
    action in [AgentCore::Action::"inventory-tool", AgentCore::Action::"weather-tool", AgentCore::Action::"user-details-tool"],
    resource == AgentCore::Gateway::"${gateway.gatewayArn}"
  ) when {
    principal.hasTag("user_tag") &&
    principal.getTag("user_tag") == "admin_user"
  };
`;

// Exemplo: Adicionar uma nova política para uma ferramenta específica
const customPolicy = `
  permit (
    principal is AgentCore::OAuthUser,
    action in [AgentCore::Action::"user-details-tool"],
    resource == AgentCore::Gateway::"${gateway.gatewayArn}"
  ) when {
    principal.hasTag("department") &&
    principal.getTag("department") == "finance"
  };
`;

// Adicionar a política personalizada
agentCorePolicyEngine.addPolicy(
  "finance_user_details_policy",
  "Policy for finance users to access user details",
  customPolicy
);
```

Em seguida, reimplante:

```bash
cdk deploy
```

**Importante**: Os nomes das tags de política devem corresponder aos nomes dos claims injetados pela Lambda de Geração de Token Pré-Autenticação (ex.: `user_tag` e não `custom:user_tag`).

## Monitoramento e Depuração

### Logs do Lambda

```bash
# Lambda de Proxy MCP
aws logs tail /aws/lambda/<ProxyLambdaName> --follow

# Lambda do Motor de Políticas
aws logs tail /aws/lambda/<PolicyEngineLambdaName> --follow

# Lambda Interceptor
aws logs tail /aws/lambda/<InterceptorLambdaName> --follow
```

### Consultas do CloudWatch Insights

**Rastreamento do Fluxo OAuth:**
```
fields @timestamp, @message
| filter @message like /HANDLE_AUTHORIZE|HANDLE_CALLBACK/
| sort @timestamp desc
```

**Requisições ao Gateway:**
```
fields @timestamp, method, path, statusCode
| filter @message like /proxy_to_gateway/
| sort @timestamp desc
```

## Considerações de Segurança

### Autenticação
- OAuth 2.0 com PKCE (Proof Key for Code Exchange)
- Tokens JWT com claims personalizados
- Armazenamento seguro de tokens no VS Code

### Autorização
- Controle de acesso baseado em políticas usando Cedar
- Injeção de atributos de usuário via triggers Lambda
- Aplicação de autorização no nível do Gateway

### Proteção de Dados
- Criptografia SSL/TLS em trânsito
- Anonimização de PII via Bedrock Guardrails
- Isolamento VPC para implantações com ALB

### Gerenciamento de Segredos
- Segredos de cliente armazenados em variáveis de ambiente
- Tokens OAuth nunca expostos em logs
- Acesso baseado em roles IAM para funções Lambda

## Solução de Problemas

### Problema: "Failed to authenticate"
**Solução**: Verifique as credenciais do usuário no Cognito e certifique-se de que o usuário possui os atributos personalizados corretos.

### Problema: "Tool not available"
**Solução**: Verifique se o atributo `custom:user_tag` do usuário corresponde aos requisitos da política.

### Problema: "Gateway timeout"
**Solução**: Verifique as configurações de timeout do Lambda (padrão: 300 segundos) e a conectividade do Gateway.

### Problema: "Invalid redirect_uri"
**Solução**: Certifique-se de que as URLs de callback estão devidamente registradas na configuração do cliente Cognito.

### Problema: Falha no health check do ALB
**Solução**: O endpoint `/ping` deve retornar 200. Verifique os logs do Lambda para erros.

## Limpeza

Para remover todos os recursos:

```bash
cdk destroy
```

**Aviso**: Isso excluirá todos os recursos, incluindo usuários do Cognito, funções Lambda e configurações.

## Decisões de Arquitetura

### Por que Duas Opções de Implantação?

- **ALB**: Ambientes de produção que requerem domínios personalizados, SSL/TLS e roteamento refinado
- **API Gateway**: Desenvolvimento/testes, preferência por serverless, otimização de custos

### Por que Lambda para Servidores MCP?

- Serverless, auto-escalável
- Precificação por uso
- Fácil integração com o AgentCore Gateway
- Sem gerenciamento de infraestrutura

### Por que um Motor de Políticas Personalizado?

- Controle de acesso flexível baseado em atributos
- Linguagem de políticas Cedar para lógica de autorização complexa
- Avaliação de políticas em tempo de execução sem reimplantação
- Integração com claims personalizados JWT

## Estendendo a Solução

### Adicionando Novas Ferramentas MCP

1. Crie uma nova função Lambda em `cdk/lambda/mcp-servers/`
2. Defina o esquema da ferramenta
3. Adicione o alvo Lambda ao Gateway em `enterprise-mcp-infra-stack.ts`
4. Atualize as políticas se necessário

### Interceptores Personalizados

Modifique `cdk/lambda/interceptor/interceptor.py` para adicionar lógica personalizada:
- Validação de requisições
- Transformação de respostas
- Registro e métricas
- Integração com sistemas externos

### Servidores MCP Externos

Configure o Gateway para conectar-se a servidores MCP externos via HTTP/HTTPS com provedores de credenciais.

## Referências

- [Documentação do AWS CDK](https://docs.aws.amazon.com/cdk/)
- [Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock/)
- [Especificação do Model Context Protocol](https://modelcontextprotocol.io/)
- [OAuth 2.0 RFC 6749](https://tools.ietf.org/html/rfc6749)
- [Linguagem de Políticas Cedar](https://www.cedarpolicy.com/)

## Licença

Este projeto é fornecido como está para fins de demonstração.

## Suporte

Para problemas e dúvidas:
1. Verifique os Logs do CloudWatch para funções Lambda
2. Revise os eventos da stack do CloudFormation
3. Verifique as permissões IAM
4. Consulte a documentação do AWS Bedrock AgentCore
