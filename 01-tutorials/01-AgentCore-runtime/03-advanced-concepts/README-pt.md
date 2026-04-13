# Amazon Bedrock AgentCore Runtime

## Visão Geral
Amazon Bedrock AgentCore Runtime é um runtime seguro e serverless projetado para implantar e escalar agentes de IA e ferramentas. 
Ele suporta quaisquer frameworks, modelos e protocolos, permitindo que desenvolvedores transformem protótipos locais em soluções prontas para produção com mudanças mínimas no código.

O Amazon BedrockAgentCore Python SDK fornece um wrapper leve que ajuda você a implantar suas funções de agente como serviços HTTP compatíveis com o Amazon Bedrock. Ele lida com todos os detalhes do servidor HTTP para que você possa se concentrar na funcionalidade principal do seu agente.

Tudo que você precisa fazer é decorar sua função com o decorador `@app.entrypoint` e usar as capacidades `configure` e `launch` do SDK para implantar seu agente no AgentCore Runtime. Sua aplicação então pode invocar este agente usando o SDK ou qualquer uma das ferramentas de desenvolvedor da AWS, como boto3, AWS SDK para JavaScript ou AWS SDK para Java.

![Visão Geral do Runtime](images/runtime_overview.png)

## Principais Funcionalidades

### Flexibilidade de Framework e Modelo

- Implante agentes e ferramentas de qualquer framework (como Strands Agents, LangChain, LangGraph, CrewAI) 
- Usando qualquer modelo (no Amazon Bedrock ou não)

### Integração

O Amazon Bedrock AgentCore Runtime integra-se com outras capacidades do Amazon Bedrock AgentCore através de um SDK unificado, incluindo:

- Amazon Bedrock AgentCore Memory
- Amazon Bedrock AgentCore Gateway
- Amazon Bedrock AgentCore Observability
- Amazon Bedrock AgentCore Tools

Esta integração visa simplificar o processo de desenvolvimento e fornecer uma plataforma abrangente para construir, implantar e gerenciar agentes de IA.

### Casos de Uso

O runtime é adequado para uma ampla gama de aplicações, incluindo:

- Agentes de IA interativos em tempo real
- Fluxos de trabalho de IA complexos e de longa duração
- Processamento de IA multi-modal (texto, imagem, áudio, vídeo)

## Visão geral dos tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [Hospedagem de agentes](01-hosting-agent)
- [Hospedagem de Servidores MCP](02-hosting-MCP-server)
- [Conceitos Avançados](03-advanced-concepts)
