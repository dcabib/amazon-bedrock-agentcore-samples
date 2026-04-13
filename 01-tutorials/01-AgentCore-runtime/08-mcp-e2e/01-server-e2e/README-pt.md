# Exemplo Completo de Servidor MCP Stateless

## Visão Geral

Este tutorial demonstra como construir um servidor MCP (Model Context Protocol) completo com todas as três capacidades principais e implantá-lo no Amazon Bedrock AgentCore Runtime.

A [especificação MCP](https://modelcontextprotocol.io/specification/2025-11-25/server) define os blocos de construção para adicionar contexto a modelos de linguagem via MCP. Essas operações permitem interações ricas entre clientes, servidores e modelos de linguagem:
- **Prompts**: Templates ou instruções pré-definidas que orientam interações com modelos de linguagem
- **Resources**: Dados estruturados ou conteúdo que fornece contexto adicional ao modelo
- **Tools**: Funções executáveis que permitem que modelos executem ações ou recuperem informações

## Pré-requisitos

Antes de iniciar este tutorial, certifique-se de ter:
- AWS CLI configurada com permissões apropriadas
- Python 3.13+ instalado
- Ambiente Jupyter Notebook configurado
- Acesso ao Amazon Bedrock AgentCore

## Começando

Para começar com este tutorial, abra e siga o guia passo a passo no notebook Jupyter:

**[📓 01_full_mcp_server_e2e](01_full_mcp_server_e2e.ipynb)**

O notebook contém todos os exemplos de código, configurações e instruções detalhadas necessárias para concluir este tutorial.

## O Que Você Aprenderá

Neste tutorial, você aprenderá:

* Como criar um servidor MCP com tools, prompts e resources
* Como implantar no AgentCore Runtime
* Como invocar seu servidor implantado

### Detalhes do Tutorial

| Informação         | Detalhes                                                   |
|:--------------------|:----------------------------------------------------------|
| Tipo de tutorial       | Hospedagem de Tools, Prompts e Resources no Runtime           |
| Tipo de ferramenta           | Servidor MCP                                                |
| Componentes do tutorial | Hospedagem no AgentCore Runtime, Criando um servidor MCP      |
| Vertical do tutorial   | Cross-vertical                                            |
| Complexidade do exemplo  | Médio                                                    |
| SDK usado            | SDK Python Amazon BedrockAgentCore e MCP Client         |

### Arquitetura do Tutorial

Neste tutorial, descreveremos como implantar este exemplo no AgentCore Runtime.

<img src="img/architecture.png" style="width: 80%;">

Neste notebook tutorial, você construirá um agente. Primeiro, você implantará o agente no AgentCore Runtime com quatro ferramentas. Em seguida, você o atualizará para adicionar prompts. Finalmente, você o atualizará novamente para implantar resources.


### Principais Recursos do Tutorial

* Hospedagem de Servidor MCP Completo (Stateless)
* Usando Tools, Resources e Prompts da Especificação MCP
