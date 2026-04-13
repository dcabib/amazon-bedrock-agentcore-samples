# Solução Multi-agente Distribuída usando Amazon Bedrock AgentCore

## Visão Geral

Neste tutorial, aprenderemos como hospedar agentes de forma independente, cada um em seu próprio Bedrock AgentCore Runtime e construídos com diferentes Frameworks Agênticos. Em seguida, habilitaremos a comunicação entre eles para uma solução multi-agente distribuída.

Neste exemplo, criaremos:
1. Um agente técnico (tech_agent) especializado em responder perguntas técnicas sobre programação e solução de problemas técnicos.
2. Um agente de RH (hr_agent) especializado em benefícios da empresa.
3. Um agente orquestrador (orchestrator_agent) que roteia perguntas para o agente técnico ou de RH.

Reunindo esses três agentes, você obtém uma configuração multi-agente com um supervisor, que pode rotear perguntas do usuário para o subagente apropriado. Este sistema é capaz de responder a uma série de perguntas que um funcionário pode ter em uma empresa.

## Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Tipo de agente      | Multi-Agente (Supervisor chamando agentes como ferramentas)                     |
| Framework Agêntico  | Strands Agents & LangGraph                                                       |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                                       |
| Componentes         | Hospedagem de agentes no AgentCore Runtime e colaboração multi-agente            |
| Vertical            | Cross-vertical                                                                   |
| Complexidade        | Médio                                                                            |
| SDK usado           | Amazon BedrockAgentCore Python SDK e boto3                                       |

## Arquitetura do Tutorial

<div style="text-align:left">
    <img src="architecture.png" width="100%"/>
</div>

## Primeiros Passos

Siga as instruções neste notebook [distributed_agents_with_agentcore.ipynb](distributed_agents_with_agentcore.ipynb)
