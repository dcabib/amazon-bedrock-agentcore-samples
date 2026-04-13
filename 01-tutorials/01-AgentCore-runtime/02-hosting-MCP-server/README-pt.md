# Hospedando servidor MCP no AgentCore Runtime

## Visão Geral

Nesta sessão discutiremos como hospedar ferramentas MCP no Amazon Bedrock AgentCore Runtime.

Usaremos o SDK Python do Amazon Bedrock AgentCore para encapsular as funções do agente como um servidor MCP compatível com o Amazon Bedrock AgentCore.
Ele cuidará dos detalhes do servidor MCP para que você possa se concentrar na funcionalidade principal do seu agente.

O SDK Python do Amazon Bedrock AgentCore prepara seu código de agente ou ferramenta para ser executado no AgentCore Runtime. 

Ele transformará seu código nos contratos padronizados do protocolo HTTP ou protocolo MCP do AgentCore para permitir comunicação direta via endpoint REST API para um padrão tradicional de requisição/resposta (protocolo HTTP) ou Model Context Protocol para servidores de ferramentas e agentes (Protocolo MCP).

Quando você está hospedando ferramentas, o SDK Python do Amazon Bedrock AgentCore implementará o protocolo de transporte [Stateless Streamable HTTP] com o cabeçalho `MCP-Session-Id` para [isolamento de sessão](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#session-management), os servidores devem suportar operação stateless para não rejeitar o cabeçalho Mcp-Session-Id gerado pela plataforma.
Seu servidor MCP será então hospedado na porta `8000` e fornecerá um caminho de invocação: o `mcp-POST`. Este endpoint de interação receberá as mensagens RPC do MCP e as processará através das capacidades da sua ferramenta. Ele suporta tanto application/json quanto text/event-stream como content-types de resposta.

Quando você define seu protocolo AgentCore como MCP, o AgentCore Runtime esperará que o contêiner do servidor MCP esteja no caminho `0.0.0.0:8000/mcp`, pois esse é o caminho padrão suportado pela maioria dos SDKs oficiais do servidor MCP.

O AgentCore Runtime requer que você hospede servidores streamable-http stateless porque ele fornece isolamento de sessão por padrão e adiciona automaticamente um cabeçalho Mcp-Session-Id para qualquer requisição sem ele, para que os clientes MCP possam ter continuidade de conexão com o mesmo ID de sessão do Bedrock AgentCore Runtime. 

O payload da API `InvokeAgentRuntime` é completamente pass through, então mensagens RPC de protocolos como MCP podem facilmente ser proxiadas.

Neste tutorial você aprenderá:

* Como criar um servidor MCP com ferramentas
* Como testar seu servidor localmente
* Como implantar seu servidor na AWS
* Como invocar seu servidor implantado

### Detalhes do Tutorial

| Informação          | Detalhes                                                  |
|:--------------------|:----------------------------------------------------------|
| Tipo de tutorial    | Hospedagem de Ferramentas                                 |
| Tipo de ferramenta  | Servidor MCP                                              |
| Componentes         | Hospedagem de ferramenta no AgentCore Runtime. Criação de servidor MCP |
| Vertical            | Cross-vertical                                            |
| Complexidade        | Fácil                                                     |
| SDK usado           | SDK Python do Amazon BedrockAgentCore e Cliente MCP       |

### Arquitetura do Tutorial
Neste tutorial descreveremos como implantar um servidor MCP existente no AgentCore runtime. 

Para fins de demonstração, usaremos um servidor MCP muito simples com 3 ferramentas: `add_numbers`, `multiply_numbers` e `greet_users`

![Arquitetura MCP](images/hosting_mcp_server.png)

### Recursos Principais do Tutorial

* Hospedagem de Servidor MCP
