# Agente Assíncrono de Análise de Dados com Amazon Bedrock AgentCore

## Visão Geral

Neste tutorial aprenderemos como construir um agente de análise de dados assíncrono que pode realizar tarefas de análise de longa duração em segundo plano enquanto mantém uma conversa responsiva com o usuário. Isso demonstra como aproveitar as capacidades assíncronas do Amazon Bedrock AgentCore com Strands para criar agentes que lidam com operações demoradas de forma elegante.

Neste exemplo criaremos:

1. Um agente primário que orquestra interações do usuário e delega tarefas de análise
2. Um agente de codificação que gera código Python para tarefas de análise de dados
3. Um sistema de tarefas assíncronas que executa código no Code Interpreter enquanto mantém o agente responsivo

Combinando esses componentes você obtém uma configuração de agente assíncrono que pode lidar com operações de análise de dados intensivas em computação enquanto mantém uma conversa responsiva com os usuários.

Este tutorial aproveita o [**Amazon Bedrock AgentCore Runtime**](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) para hospedar e gerenciar agentes com suporte integrado para operações assíncronas, e o [**Amazon Bedrock Code Interpreter**](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/code-interpreter-tool.html) para execução segura de código Python gerado dinamicamente em ambientes isolados. O AgentCore Runtime fornece infraestrutura escalável para implantar agentes de IA conversacional, enquanto o Code Interpreter permite que agentes escrevam e executem código com segurança para tarefas de análise de dados. [Saiba mais sobre o Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-agentcore.html).

## Detalhes do Tutorial

| Informação          | Detalhes                                                               |
| :------------------ | :-------------------------------------------------------------------- |
| Tipo de tutorial    | Conversacional                                                         |
| Tipo de agente      | Multi-Agente (Agente Orquestrador com Agente de Geração de Código como ferramenta) |
| Framework Agêntico  | Strands Agents                                                         |
| Modelos LLM         | Anthropic Claude Sonnet 4 (agente primário) e Haiku 4.5 (agente de codificação)  |
| Componentes do tutorial | AgentCore Runtime, Tarefas Assíncronas, Code Interpreter, Integração S3      |
| Vertical do tutorial | Análise de Dados                                                      |
| Complexidade do exemplo | Intermediário                                                      |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                            |

## Arquitetura do Tutorial

<div style="text-align:left">
    <img src="architecture.png" width="100%"/>
</div>

## Começando

Siga as instruções neste notebook [async_data_analysis_tutorial.ipynb](async_data_analysis_tutorial.ipynb)
