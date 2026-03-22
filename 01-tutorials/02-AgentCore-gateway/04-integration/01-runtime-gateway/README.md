# Integrar o Amazon Bedrock AgentCore Gateway com o Amazon Bedrock AgentCore Runtime

O [Amazon Bedrock AgentCore Gateway](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html) oferece aos clientes uma maneira de transformar suas funções AWS Lambda e APIs existentes (OpenAPI e Smithy) em servidores MCP totalmente gerenciados, sem a necessidade de gerenciar infraestrutura ou hospedagem. O [Amazon Bedrock AgentCore Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) fornece um ambiente de hospedagem seguro, serverless e construído especificamente para implantar e executar agentes ou ferramentas de IA. Neste tutorial, integraremos o Amazon Bedrock AgentCore Gateway com o AgentCore Runtime e [Strands Agents](https://strandsagents.com/latest/).

## Detalhes do Tutorial

| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity, AgentCore Runtime  |
| Framework de Agentes | Strands Agents                                            |
| Tipo de Alvo do Gateway | AWS Lambda, alvo OpenAPI                               |
| IdP de Auth de Entrada | AWS IAM                                                 |
| Auth de Saída        | AWS IAM (AWS Lambda), Chave de API (alvo OpenAPI)         |
| Modelo LLM           | Anthropic Claude Haiku 4.5, Amazon Nova Pro              |
| Componentes do tutorial | Criação do AgentCore Gateway e Invocação do AgentCore Gateway |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Médio                                                  |
| SDK utilizado        | boto3                                                     |

## Arquitetura do Tutorial

Neste tutorial, transformaremos operações definidas em funções AWS Lambda e APIs RESTful em ferramentas MCP e as hospedaremos no Bedrock AgentCore Gateway. Demonstraremos a autenticação de entrada usando credenciais AWS IAM no formato AWS Sigv4. Implantaremos um Agente Strands utilizando ferramentas do AgentCore Gateway no AgentCore Runtime.

Para fins de demonstração, usaremos um Agente Strands usando modelos do [Amazon Bedrock](https://aws.amazon.com/bedrock/).

![runtime gateway](./images/runtime_gateway.png)
