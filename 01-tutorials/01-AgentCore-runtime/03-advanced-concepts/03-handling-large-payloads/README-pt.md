# Manipulação de Payloads Multi-Modais Grandes no AgentCore Runtime

## Visão Geral

Este tutorial demonstra como o Amazon Bedrock AgentCore Runtime manipula payloads grandes de até 100MB, incluindo conteúdo multi-modal como arquivos Excel e imagens. O AgentCore Runtime é projetado para processar conteúdo de mídia rica e grandes conjuntos de dados de forma transparente.

### Detalhes do Tutorial

| Informação          | Detalhes                                                      |
|:--------------------|:-------------------------------------------------------------|
| Tipo de tutorial    | Processamento de Payload Grande e Multi-Modal                |
| Tipo de agente      | Único                                                        |
| Framework Agêntico  | Strands Agents                                               |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                   |
| Componentes do tutorial | Processamento de Arquivos Grandes, Análise de Imagens, Processamento de Dados Excel |
| Vertical do tutorial | Análise de Dados e IA Multi-Modal                           |
| Complexidade do exemplo | Intermediário                                             |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK                           |

### Principais Funcionalidades

* **Suporte a Payload Grande**: Processar arquivos de até 100MB de tamanho
* **Processamento Multi-Modal**: Manipular arquivos Excel, imagens e texto simultaneamente
* **Análise de Dados**: Extrair insights de dados estruturados e conteúdo visual
* **Codificação Base64**: Transmissão segura de dados binários através de payloads JSON
