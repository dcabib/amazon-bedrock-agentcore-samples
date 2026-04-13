# Hospedando Strands Agents com modelos OpenAI no Amazon Bedrock AgentCore Runtime

## Visão Geral

Neste tutorial, aprenderemos como hospedar seu agente existente usando o Amazon Bedrock AgentCore Runtime. 

Focaremos em um exemplo de Strands Agents com modelo OpenAI. Para Strands Agents com modelo do Amazon Bedrock, consulte [aqui](../01-strands-with-bedrock-model) e 
para LangGraph com modelo do Amazon Bedrock, consulte [aqui](../02-langgraph-with-bedrock-model)


### Detalhes do tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Tipo de agente      | Único                                                                            |
| Framework Agêntico  | Strands Agents                                                                   |
| Modelo LLM          | GPT 4.1 mini                                                                     |
| Componentes         | Hospedagem de agente no AgentCore Runtime. Usando Strands Agent e Modelo OpenAI |
| Vertical do tutorial| Multisetorial                                                                    |
| Complexidade        | Fácil                                                                            |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |

### Arquitetura do Tutorial

Neste tutorial, descreveremos como implantar um agente existente no AgentCore runtime. 

Para fins de demonstração, usaremos um Strands Agent usando modelos do Amazon Bedrock

Em nosso exemplo, usaremos um agente muito simples com duas ferramentas: `get_weather` e `get_time`. 

<div style="text-align:left">
    <img src="images/architecture_runtime.png" width="100%"/>
</div>

### Principais Recursos do tutorial

* Hospedando Agentes no Amazon Bedrock AgentCore Runtime
* Usando modelos OpenAI
* Usando Strands Agents
