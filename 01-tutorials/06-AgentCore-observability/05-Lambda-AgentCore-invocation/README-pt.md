# Invocação do AgentCore via Lambda com Observabilidade do CloudWatch

Este tutorial demonstra como invocar agentes Strands hospedados no Amazon Bedrock AgentCore Runtime a partir de funções AWS Lambda, com observabilidade completa do CloudWatch Gen AI habilitada.

## Visão Geral

Aprenda a construir uma arquitetura serverless onde funções Lambda invocam agentes habilitados com MCP executando no AgentCore Runtime, com visibilidade completa tanto da execução do Lambda quanto do comportamento do agente através do CloudWatch.

## Estrutura do Projeto
```
05-Lambda-AgentCore-invocation/
├── agentcore_observability_lambda.ipynb  # Notebook principal do tutorial
├── lambda_agentcore_invoker.py           # Código da função Lambda
├── mcp_agent_multi_server.py             # Agente com múltiplos servidores MCP
├── requirements.txt                      # Dependências Python
├── .gitignore                            # Padrões de ignorar do Git
└── README.md                             # Este arquivo

Nota: O Dockerfile é gerado dinamicamente no notebook e não é rastreado no git.
```

## Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:-------------------|:----------------------------------------------------------------------------------|
| Tipo de tutorial   | Conversacional                                                                   |
| Tipo de agente     | Único                                                                            |
| Framework agêntico | Strands Agents                                                                   |
| Modelo LLM         | Anthropic Claude Haiku 4.5                                                      |
| Componentes        | Invocação Lambda, AgentCore Runtime, servidores MCP, Observabilidade CloudWatch  |
| Complexidade       | Avançado                                                                         |
| SDK utilizado      | Amazon BedrockAgentCore Python SDK, boto3, AWS Lambda                           |

## Arquitetura
```
┌─────────┐      ┌────────────────┐      ┌──────────────────┐      ┌─────────────────┐
│   API   │─────>│  AWS Lambda    │─────>│  AgentCore       │─────>│  Strands Agent  │
│  /User  │      │  (Invoker)     │      │  Runtime         │      │  + MCP Servers  │
└─────────┘      └────────────────┘      └──────────────────┘      └─────────────────┘
                        │                         │                          │
                        ▼                         ▼                          ▼
                 ┌─────────────────────────────────────────────────────────────┐
                 │            Observabilidade CloudWatch                       │
                 │       • Traces Gen AI     • Métricas     • Logs            │
                 └─────────────────────────────────────────────────────────────┘
```

## Principais Recursos

* Integração de múltiplos servidores MCP (AWS Documentation + AWS CDK) com Strands Agents
* Hospedagem de agentes no Amazon Bedrock AgentCore Runtime
* Invocação de agentes hospedados a partir de funções AWS Lambda
* Configuração da Observabilidade Gen AI do CloudWatch para monitoramento abrangente de agentes
* Visualização de traces, spans e métricas no console do CloudWatch

## O Que Você Vai Aprender

1. Como implantar um agente habilitado com MCP no AgentCore Runtime
2. Como criar uma função Lambda que invoca o agente do runtime
3. Como habilitar a Observabilidade Gen AI do CloudWatch para seus agentes
4. Como visualizar e analisar traces mostrando o fluxo de execução do agente

## Pré-requisitos

* Python 3.10+
* Credenciais AWS configuradas com permissões apropriadas
* SDK do Amazon Bedrock AgentCore
* Permissões para criar funções Lambda e roles IAM
* CloudWatch Transaction Search habilitado (veja o tutorial para instruções de configuração)

## Primeiros Passos

1. Instale os pacotes necessários:
```bash
   pip install -r requirements.txt
```

2. Habilite o CloudWatch Transaction Search (configuração única por conta AWS via console)

3. Abra e execute o notebook Jupyter:
```bash
   jupyter notebook agentcore_observability_lambda.ipynb
```

4. Siga as instruções passo a passo no notebook para:
   - Criar e implantar o agente MCP
   - Construir e implantar a função Lambda
   - Testar a integração
   - Visualizar traces no CloudWatch

## Componentes

### Função Lambda (`lambda_agentcore_invoker.py`)
Função serverless que recebe prompts de usuários e invoca o agente do AgentCore Runtime. Inclui tratamento de erros e logging abrangente.

### Agente MCP (`mcp_agent_multi_server.py`)
Agente Strands configurado com múltiplos servidores MCP (AWS Documentation e AWS CDK) e instrumentação OpenTelemetry para observabilidade.

## Uso

A função Lambda espera o seguinte formato de evento:
```json
{
  "prompt": "Sua pergunta aqui",
  "sessionId": "id-de-sessao-opcional"
}
```

Formato de resposta:
```json
{
  "statusCode": 200,
  "body": {
    "response": "Resposta do agente",
    "sessionId": "id-de-sessao"
  }
}
```

## Recursos de Observabilidade

* **Traces Gen AI**: Visualize o fluxo de trabalho completo do agente com linhas do tempo de spans
* **Logs do CloudWatch**: Logging detalhado da execução do Lambda e do agente
* **Métricas de Desempenho**: Acompanhe o uso de tokens, duração e taxas de erro
* **Transaction Search**: Consulte e analise traces em toda a sua aplicação

## Limpeza

Após concluir o tutorial, exclua os seguintes recursos para evitar cobranças desnecessárias:

1. Função Lambda e roles IAM associadas
2. Agente e endpoint do AgentCore Runtime
3. Grupos de logs do CloudWatch
4. Imagens de container no ECR (se aplicável)

## Recursos Adicionais

- [Documentação do Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock/latest/userguide/agentcore.html)
- [Guia de Observabilidade Gen AI do CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/GenAI-observability.html)

## Licença

Este projeto é licenciado sob os termos especificados no repositório.
