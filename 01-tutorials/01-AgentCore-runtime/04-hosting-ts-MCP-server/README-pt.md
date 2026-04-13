# Servidor MCP TypeScript no Amazon Bedrock AgentCore

## Visão Geral

Este tutorial demonstra como hospedar um servidor MCP (Model Context Protocol) baseado em TypeScript usando o ambiente de runtime do Amazon Bedrock AgentCore.


### Detalhes do Tutorial

| Informação          | Detalhes                                                  |
|:--------------------|:----------------------------------------------------------|
| Tipo de tutorial    | Hospedagem de servidor MCP em TypeScript                  |
| Tipo de ferramenta  | Servidor MCP                                              |
| Componentes         | Hospedagem de servidor MCP em TypeScript no AgentCore Runtime |
| Vertical            | Cross-vertical                                            |
| Complexidade        | Fácil                                                     |
| SDK utilizado       | SDK TypeScript da Anthropic para MCP                      |

## Pré-requisitos

- Node.js v22 ou superior  
- Docker (para containerização)  
- Amazon ECR (Elastic Container Registry) para armazenar imagens Docker  
- Conta AWS com acesso ao Bedrock AgentCore  

---

## Contrato de Serviço do AgentCore Runtime

Consulte a [documentação oficial do contrato de serviço](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-service-contract.html).

**Configuração do Runtime:**
- **Host:** `0.0.0.0`  
- **Porta:** `8000`  
- **Transporte:** `streamable-http` stateless  
- **Caminho do Endpoint:** `POST /mcp`  

## Desenvolvimento Local

1. Instalar dependências

```
npm install
```

2. Configurar credenciais AWS
```
aws configure
export AWS_ACCESS_KEY_ID=your_access_key
export AWS_SECRET_ACCESS_KEY=your_secret_key
export AWS_REGION=us-east-1
```

3. Iniciar servidor
```
npm run start
```

4. Testar localmente usando o [MCP inspector](https://github.com/modelcontextprotocol/inspector)

```
npx @modelcontextprotocol/inspector
```

## Implantação com Docker

1. Criar Repositório ECR
```
aws ecr create-repository --repository-name mcp-server --region us-east-1
```
2. Build e Push da Imagem para ECR
```
# Obter token de login
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin [account-id].dkr.ecr.us-east-1.amazonaws.com

docker buildx --platform linux/arm64 \
  -t [account-id].dkr.ecr.us-east-1.amazonaws.com/mcp-server:latest --push .
```

3. Implantar no Bedrock AgentCore

    - Vá para AWS Console → Bedrock → AgentCore → Create Agent
    - Escolha MCP como protocolo
    - Configure o Agent Runtime:
        - Image URI: [account-id].dkr.ecr.us-east-1.amazonaws.com/mcp-server:latest
        - Configure as permissões IAM para acesso ao modelo Bedrock
        - Implante e teste no Agent Sandbox


4. Construir a URL MCP com ARN Codificado

```
echo "agent_arn" | sed 's/:/%3A/g; s/\//%2F/g'
```

```
https://bedrock-agentcore.{region}.amazonaws.com/runtimes/{encoded_arn}/invocations?qualifier=DEFAULT
```

5. Use a URL MCP com o [MCP inspector](https://github.com/modelcontextprotocol/inspector).

## Referências
- https://aws.amazon.com/bedrock/agentcore/
- https://github.com/modelcontextprotocol/typescript-sdk
- https://github.com/modelcontextprotocol/inspector
