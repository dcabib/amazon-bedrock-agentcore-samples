# Observabilidade do AgentCore

Este repositório demonstra como implementar a observabilidade do AgentCore para Agentes usando o Amazon CloudWatch e outros provedores. Ele fornece exemplos tanto para agentes hospedados no Amazon Bedrock AgentCore Runtime quanto para agentes hospedados fora do runtime com frameworks populares de agentes open-source.



Para mais detalhes sobre a Observabilidade do AgentCore, consulte este [post](https://aws.amazon.com/blogs/machine-learning/build-trustworthy-ai-agents-with-amazon-bedrock-agentcore-observability/) no blog.
## Estrutura do Projeto

```
06-AgentCore-observability/
├── 01-Agentcore-runtime-hosted/
│   ├── CrewAI/
│   │   ├── images/
│   │   ├── requirements.txt
│   │   └── runtime-with-crewai-and-bedrock-models.ipynb
│   ├── LlamaIndex/
│   │   ├── images/
│   │   ├── requirements.txt
│   │   ├── runtime_with_llamaindex_and_bedrock_models.ipynb
│   │   └── README.md
│   ├── Strands Agents/
│   │   ├── images/
│   │   ├── requirements.txt
│   │   └── runtime_with_strands_and_bedrock_models.ipynb
│   └── README.md
├── 02-Agent-not-hosted-on-runtime/
│   ├── CrewAI/
│   │   ├── .env.example
│   │   ├── CrewAI_Observability.ipynb
│   │   └── requirements.txt
│   ├── Langgraph/
│   │   ├── .env.example
│   │   ├── Langgraph_Observability.ipynb
│   │   └── requirements.txt
│   ├── LlamaIndex/
│   │   ├── images/
│   │   ├── .env.example
│   │   ├── LlamaIndex_Observability.ipynb
│   │   ├── README.md
│   │   └── requirements.txt
│   ├── Strands/
│   │   ├── images/
│   │   ├── .env.example
│   │   ├── requirements.txt
│   │   └── Strands_Observability.ipynb
│   └── README.md
├── 03-advanced-concepts/
│   ├── 01-custom-span-creation/
│   │   ├── .env.example
│   │   ├── Custom_Span_Creation.ipynb
│   │   └── requirements.txt
│   └── README.md
├── 04-Agentcore-runtime-partner-observability/
│   ├── Arize/
│   │   ├── requirements.txt
│   │   └── runtime_with_strands_and_arize.ipynb
│   ├── Braintrust/
│   │   ├── requirements.txt
│   │   └── runtime_with_strands_and_braintrust.ipynb
│   ├── Instana/
│   │   ├── requirements.txt
│   │   └── runtime_with_strands_and_instana.ipynb
│   ├── Langfuse/
│   │   ├── requirements.txt
│   │   └── runtime_with_strands_and_langfuse.ipynb
│   ├── images/
│   └── README.md
├── 05-Lambda-AgentCore-invocation/
│   ├── .gitignore
│   ├── agentcore_observability_lambda.ipynb
│   ├── lambda_agentcore_invoker.py
│   ├── mcp_agent_multi_server.py
│   ├── README.md
│   └── requirements.txt
└── README.md
```

## Visão Geral

Este repositório fornece exemplos e ferramentas para ajudar desenvolvedores a implementar observabilidade para aplicações GenAI. A Observabilidade do AgentCore ajuda desenvolvedores a rastrear, depurar e monitorar o desempenho de agentes em produção por meio de dashboards operacionais unificados. Com suporte para telemetria compatível com OpenTelemetry e visualizações detalhadas de cada etapa do fluxo de trabalho do agente, o Amazon CloudWatch GenAI Observability permite que desenvolvedores obtenham facilmente visibilidade sobre o comportamento dos agentes e mantenham padrões em escala.

## Conteúdo

Demonstra exemplos usando os frameworks populares de desenvolvimento de Agentes:

- **Strands Agents**: Construa aplicações LLM com fluxos de trabalho complexos usando desenvolvimento agêntico orientado por modelos
- **CrewAI**: Crie agentes de IA autônomos que trabalham juntos em papéis para realizar tarefas
- **LangGraph**: Estenda o LangChain com aplicações stateful e multi-ator para sistemas de raciocínio complexos
- **LlamaIndex**: Agentes alimentados por LLM sobre dados com workflows


### 1. Hospedado no Bedrock AgentCore Runtime (01-Agentcore-runtime-hosted)

Exemplos demonstrando observabilidade para Agentes hospedados no Amazon Bedrock AgentCore Runtime usando Amazon OpenTelemetry Python Instrumentation e Amazon CloudWatch.

### 2. Agente Não Hospedado no Runtime (02-Agent-not-hosted-on-runtime)

Exemplos mostrando observabilidade para frameworks populares de agentes open-source não hospedados no Amazon Bedrock AgentCore Runtime:

### 3. Conceitos Avançados (03-advanced-concepts)

Padrões e técnicas avançadas de observabilidade:

- **Custom Span Creation**: Aprenda como criar custom spans para rastreamento detalhado e monitoramento de operações específicas dentro dos fluxos de trabalho do seu agente

### 4. Observabilidade com Parceiros (04-Agentcore-runtime-partner-observability)

Exemplos de uso de agentes hospedados no Amazon Bedrock AgentCore Runtime com ferramentas de observabilidade de terceiros:

- **Arize**: Plataforma de engenharia de IA e Agentes
- **Braintrust**: Plataforma de avaliação e monitoramento de IA
- **Instana**: Plataforma de APM e Observabilidade em Tempo Real
- **Langfuse**: Observabilidade e analytics para LLM

### 5. Invocação do AgentCore via Lambda (05-Lambda-AgentCore-invocation)

Aprenda como invocar agentes do AgentCore Runtime a partir de funções AWS Lambda com observabilidade completa no CloudWatch:

- **Integração com Lambda**: Implante funções serverless que invocam agentes hospedados
- **MCP Multi-Server**: Use múltiplos servidores MCP (AWS Docs + CDK) em um único agente
- **CloudWatch GenAI Observability**: Monitore o comportamento e o desempenho dos agentes em produção

## Primeiros Passos

1. Navegue até o diretório do framework que deseja explorar
2. Instale os requisitos.
3. Configure suas credenciais AWS
4. Copie o arquivo `.env.example` para `.env` e atualize as variáveis
5. Abra e execute o Jupyter notebook

## Pré-requisitos

- Conta AWS com permissões apropriadas
- Python 3.10+
- Ambiente Jupyter notebook
- AWS CLI configurado com suas credenciais
- Habilitar Transaction Search

## Limpeza

Por favor, exclua os Log groups e recursos associados criados no Amazon CloudWatch após concluir os exemplos para evitar cobranças desnecessárias.

## Licença

Este projeto é licenciado sob os termos especificados no repositório.
