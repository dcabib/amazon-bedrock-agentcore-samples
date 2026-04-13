## Introdução ao A2A no AgentCore Runtime

### Visão geral

O Amazon Bedrock AgentCore Runtime é um runtime seguro e serverless projetado para implantar e escalar agentes de IA e ferramentas. 
Ele suporta qualquer framework, modelo e protocolo, permitindo que desenvolvedores transformem protótipos locais em soluções prontas para produção com mudanças mínimas de código.

[Strands Agents](https://strandsagents.com/latest/) é um framework simples de usar, orientado a código, para construir agentes.

Recentemente, a AWS anunciou [suporte a A2A](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-a2a.html) para o AgentCore Runtime.

Neste exemplo, construiremos um sistema multi-agente usando Amazon Bedrock AgentCore e Strands Agents.

Este tutorial navegará pela criação de 3 agentes. O primeiro é um especialista em documentação AWS, que consumirá AWS Docs usando MCP. O segundo pesquisará na web os últimos blogs e notícias AWS e o terceiro será um orquestrador, que invocará os anteriores usando MCP.

<img src="images/architecture.png" style="width: 80%;">

### Visão geral dos tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [1 - Introdução ao A2A com Strands e Bedrock AgentCore](01-a2a-getting-started-agentcore-strands-pt.ipynb)
- [2 - Criar um orquestrador, que invoca sub-agentes usando A2A](02-a2a-deploy-orchestrator-pt.ipynb)
- [3 - Limpeza](03-a2a-cleanup-pt.ipynb)
