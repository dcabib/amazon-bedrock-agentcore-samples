# Amazon Bedrock AgentCore Gateway - Busca semântica

## Arquitetura do Tutorial

O Amazon Bedrock AgentCore Gateway fornece conectividade unificada entre agentes e as ferramentas e recursos com os quais eles precisam interagir. O Gateway desempenha múltiplos papéis nesta camada de conectividade:

1. **Guarda de Segurança**: O Gateway gerencia a autorização OAuth para garantir que apenas usuários/agentes válidos acessem ferramentas/recursos.
2. **Tradutor**: O Gateway traduz requisições de agentes feitas usando protocolos populares como o Model Context Protocol (MCP) em requisições de API e invocações Lambda. Isso significa que os desenvolvedores não precisam hospedar servidores, gerenciar integração de protocolos, suporte a versões, patches de versão, etc.
3. **Compositor**: O Gateway permite que desenvolvedores combinem perfeitamente múltiplas APIs, funções e ferramentas em um único endpoint MCP que um agente pode usar.
4. **Chaveiro**: O Gateway lida com a injeção das credenciais corretas para usar com a ferramenta certa, garantindo que os agentes possam utilizar ferramentas que requerem diferentes conjuntos de credenciais de forma transparente.
5. **Pesquisador**: O Gateway permite que agentes busquem em todas as suas ferramentas para encontrar apenas aquelas que são mais adequadas para um determinado contexto ou pergunta. Isso permite que agentes utilizem milhares de ferramentas em vez de apenas algumas. Também minimiza o conjunto de ferramentas que precisa ser fornecido no prompt do LLM do agente, reduzindo latência e custo.
6. **Gerenciador de Infraestrutura**: O Gateway é completamente serverless e vem com observabilidade e auditoria integradas, eliminando a necessidade de os desenvolvedores gerenciarem infraestrutura adicional para integrar seus agentes e ferramentas.

![Como funciona](images/gw-arch-overview.png)

## O AgentCore Gateway ajuda a resolver o desafio de servidores MCP com grande número de ferramentas

Em um cenário empresarial típico, construtores de agentes encontram servidores MCP que possuem centenas ou até milhares de ferramentas MCP. Este volume de ferramentas apresenta desafios para agentes de IA, incluindo baixa precisão na seleção de ferramentas, aumento de custo e maior latência causada pelo maior uso de tokens devido ao excesso de metadados de ferramentas.
Isso pode acontecer ao conectar seus agentes a serviços de terceiros (por exemplo, Zendesk, Salesforce, Slack, JIRA, ...) ou a serviços REST empresariais existentes. O AgentCore Gateway fornece uma busca semântica integrada entre ferramentas, que melhora a latência, o custo e a precisão do agente, enquanto ainda fornece aos agentes as ferramentas de que precisam. Dependendo do seu caso de uso, modelo LLM e framework de agentes, você pode obter até 3x melhor latência mantendo um agente focado em ferramentas relevantes versus fornecer o conjunto completo de centenas de ferramentas de um servidor MCP típico.

![Como funciona](images/gateway_tool_search.png)

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- Criação de Amazon Bedrock AgentCore Gateways com alvos baseados em AWS Lambda
- Uso da busca semântica do AgentCore Gateway
- Uso do Strands Agents para mostrar como a busca do AgentCore Gateway melhora a latência

- [Amazon Bedrock AgentCore Gateway - Busca semântica](./01-gateway-search.ipynb)
