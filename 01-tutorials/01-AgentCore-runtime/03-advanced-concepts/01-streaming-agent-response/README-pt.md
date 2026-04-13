# Respostas em Streaming com Strands Agents e modelos Amazon Bedrock no Amazon Bedrock AgentCore Runtime

## Visão Geral

Neste tutorial aprenderemos como implementar respostas em streaming usando o Amazon Bedrock AgentCore Runtime com seus agentes existentes.

Vamos focar em um exemplo de Strands Agents com modelo Amazon Bedrock que demonstra capacidades de streaming em tempo real.

### Detalhes do Tutorial

| Informação          | Detalhes                                                                          |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional com Streaming                                                     |
| Tipo de agente      | Único                                                                            |
| Framework Agêntico  | Strands Agents                                                                   |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                                       |
| Componentes do tutorial | Respostas em streaming com AgentCore Runtime. Usando Strands Agent e modelo Amazon Bedrock |
| Vertical do tutorial | Cross-vertical                                                                   |
| Complexidade do exemplo | Fácil                                                                        |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |

### Arquitetura do Tutorial

Neste tutorial descreveremos como implantar um agente com streaming no runtime AgentCore.

Para fins de demonstração, usaremos um Strands Agent usando modelos Amazon Bedrock com capacidades de streaming.

Em nosso exemplo usaremos um agente simples com três ferramentas: `get_weather`, `get_time` e `calculator`, mas aprimorado com capacidades de resposta em streaming em tempo real.

<div style="text-align:left">
    <img src="images/architecture_runtime.png" width="100%"/>
</div>

### Principais Funcionalidades do Tutorial

* Implementação de respostas em streaming no Amazon Bedrock AgentCore Runtime
* Entrega de resultados parciais em tempo real usando Server-Sent Events (SSE)
* Uso de modelos Amazon Bedrock com capacidades de streaming
* Uso de Strands Agents com suporte a streaming assíncrono
* Experiência de usuário aprimorada com exibição progressiva de respostas
