# Integrar seu API Gateway como um Alvo do AgentCore Gateway

## Visão Geral

À medida que as organizações exploram as possibilidades de aplicações agênticas, elas continuam a enfrentar desafios de usar dados empresariais como contexto em requisições de invocação para modelos de linguagem grandes (LLMs) de maneira segura e alinhada com as políticas empresariais. Para ajudar a padronizar e proteger essas interações, muitas organizações estão usando a especificação do Model Context Protocol (MCP), que define como aplicações agênticas podem se conectar de forma segura a fontes de dados e ferramentas.

Embora o MCP tenha sido vantajoso para novos casos de uso, as organizações também enfrentam desafios ao trazer seu patrimônio de APIs existente para a era agêntica. O MCP certamente pode encapsular APIs existentes, mas requer trabalho adicional: traduzir requisições de MCP para APIs RESTful, garantir que a segurança seja mantida em todo o fluxo de requisição e aplicar a observabilidade padrão necessária para implantações em produção.

O [Amazon Bedrock AgentCore Gateway](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html) agora suporta o [Amazon API Gateway](https://aws.amazon.com/api-gateway/) como alvo, traduzindo requisições MCP para o AgentCore Gateway (ACGW) em requisições RESTful para o API Gateway (APIGW). Agora você pode expor endpoints de API novos e existentes do APIGW para aplicações agênticas via MCP, com segurança e observabilidade integradas. Este notebook cobre esta nova capacidade e mostra como implementá-la.

## O que há de novo

O AgentCore Gateway já suporta múltiplos tipos de alvo, por exemplo, funções Lambda, schemas OpenAPI, modelos Smithy, servidores MCP, e agora suporta API Gateway.


![](Images/agent-core-gateway-targets.png)


**Nossos clientes construíram com sucesso ecossistemas extensivos de APIs usando o API Gateway, conectando backends em inúmeras aplicações.** À medida que as empresas avançam em direção a aplicações agênticas de próxima geração, a evolução natural é expor essas APIs existentes e ferramentas de backend para sistemas alimentados por IA, permitindo integração perfeita entre infraestrutura estabelecida e agentes inteligentes modernos.

Hoje, os clientes seguem um fluxo de trabalho manual onde exportam suas APIs do APIGW como especificação OpenAPI 3 e depois adicionam ao ACGW como um alvo OpenAPI. Esta integração visa simplificar este processo automatizando a conexão entre APIGW e ACGW.

Com esta integração, os clientes não precisarão mais gerenciar este processo de exportação/importação por conta própria. Um novo tipo de alvo API_GATEWAY será adicionado ao ACGW. Proprietários de APIs REST podem adicionar sua API como um alvo do ACGW com alguns cliques no console ou um único comando CLI, expondo assim seus métodos de API REST existentes como ferramentas MCP via ACGW. Consumidores de API podem então conectar agentes de IA com essas APIs REST através do Model Context Protocol (MCP) e potencializar seus fluxos de trabalho com integração de IA. Suas aplicações agênticas agora podem se conectar à sua API APIGW nova ou existente. Hoje, esta integração entre ACGW e APIGW suporta autorização IAM e autorização por chave de API.

![](Images/agent-core-apigw-target.png)

### Detalhes do Tutorial


| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity                     |
| Framework de Agentes | Strands Agents                                            |
| Tipo de Alvo do Gateway | API Gateway                                            |
| Agente               | Strands                                                   |
| Inbound Auth IdP | Amazon Cognito, mas pode usar outros                    |
| Outbound Auth        | Autorização IAM e Chave de API                            |
| Modelo LLM           | Anthropic Claude Sonnet 4                                 |
| Componentes do tutorial | Invocando API Gateway via alvo do AgentCore Gateway    |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Fácil                                                  |
| SDK utilizado        | boto3                                                     |

## Arquitetura do tutorial

Este tutorial serve como um exemplo prático do desafio empresarial mais amplo: **Como integrar a API do API Gateway em uma arquitetura de Gateway centralizada para suas aplicações agênticas de próxima geração.**
Comece o [tutorial aqui](01-api-gateway-target.ipynb).
