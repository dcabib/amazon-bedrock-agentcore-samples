# Hospedando agente LangGraph com modelos do Amazon Bedrock no Amazon Bedrock AgentCore Runtime

## Visão Geral

Neste tutorial, aprenderemos como hospedar seu agente existente usando o Amazon Bedrock AgentCore Runtime. 

Focaremos em um exemplo de LangGraph com modelo do Amazon Bedrock. Para Strands Agents com modelo do Amazon Bedrock, consulte [aqui](../01-strands-with-bedrock-model)
e para Strands Agents com um modelo OpenAI, consulte [aqui](../03-strands-with-openai-model).

### Detalhes do Tutorial

| Informação          | Detalhes                                                                             |
|:--------------------|:-------------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                       |
| Tipo de agente      | Único                                                                                |
| Framework Agêntico  | LangGraph                                                                            |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                                           |
| Componentes         | Hospedagem de agente no AgentCore Runtime. Usando LangGraph e Modelo Amazon Bedrock |
| Vertical do tutorial| Multisetorial                                                                        |
| Complexidade        | Fácil                                                                                |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                           |

### Arquitetura do Tutorial

Neste tutorial, descreveremos como implantar um agente existente no AgentCore runtime. 

Para fins de demonstração, usaremos um agente LangGraph usando modelos do Amazon Bedrock

Em nosso exemplo, usaremos um agente muito simples com duas ferramentas: `get_weather` e `get_time`. 

<div style="text-align:left">
    <img src="images/architecture_runtime.png" width="100%"/>
</div>

### Principais Recursos do Tutorial

* Hospedando Agentes no Amazon Bedrock AgentCore Runtime
* Usando modelos do Amazon Bedrock
* Usando LangGraph
