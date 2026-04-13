# Registro Dinâmico de Cliente com AgentCore Runtime e Auth0

## Visão Geral

Nesta sessão, discutiremos como hospedar ferramentas MCP no Amazon Bedrock AgentCore Runtime. Este MCP será integrado com o recurso de Registro Dinâmico de Cliente do Auth0.

Usaremos o SDK Python do Amazon Bedrock AgentCore para encapsular as funções do agente como um servidor MCP compatível com o Amazon Bedrock AgentCore. Ele tratará dos detalhes do servidor MCP para que você possa se concentrar na funcionalidade principal do seu agente.

O SDK Python do Amazon Bedrock AgentCore prepara seu código de agente ou ferramenta para ser executado no AgentCore Runtime.

## Começando

Para começar com este tutorial, abra e siga o guia passo a passo no notebook Jupyter:

**[📓 deploy_dcr_mcp_agentcore.ipynb](deploy_dcr_mcp_agentcore.ipynb)**

O notebook contém todos os exemplos de código, configurações e instruções detalhadas necessárias para concluir este tutorial.

## O Que Você Aprenderá

Neste tutorial, você aprenderá:

* Como criar um servidor MCP com ferramentas
* Como testar seu servidor localmente
* Como configurar seu tenant Auth0 para suportar DCR e adicionar uma API e um aplicativo
* Como implantar seu servidor na AWS, integrado com DCR no Auth0
* Como invocar seu servidor implantado

### Detalhes do Tutorial

| Informação         | Detalhes                                                   |
|:-------------------|:----------------------------------------------------------|
| Tipo de tutorial       | Hospedagem de Ferramentas + DCR no Auth0                             |
| Tipo de ferramenta           | Servidor MCP                                                |
| Componentes do tutorial | Hospedando ferramenta no AgentCore Runtime, Criando um servidor MCP |
| Vertical do tutorial   | Cross-vertical                                            |
| Complexidade do exemplo  | Médio                                                    |
| SDK usado            | SDK Python Amazon BedrockAgentCore e MCP Client        |

### Arquitetura do Tutorial

Neste tutorial, descreveremos como implantar este exemplo no AgentCore Runtime.

Para fins de demonstração, usaremos um servidor MCP muito simples com 3 ferramentas: `add_numbers`, `multiply_numbers` e `greet_users`.

<img src="images/architecture.png" width="80%">

### Principais Recursos do Tutorial

* Hospedagem de Servidor MCP
* Registro Dinâmico de Cliente (DCR)
* Auth0
