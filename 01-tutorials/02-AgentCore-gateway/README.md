# Amazon Bedrock AgentCore Gateway

## Visão Geral
O Bedrock AgentCore Gateway oferece aos clientes uma maneira de transformar suas APIs e funções Lambda existentes em servidores MCP totalmente gerenciados, sem a necessidade de gerenciar infraestrutura ou hospedagem. Os clientes podem trazer especificações OpenAPI ou modelos Smithy para suas APIs existentes, ou adicionar funções Lambda que servem como front-end para suas ferramentas. O Gateway fornece uma interface uniforme do Model Context Protocol (MCP) para todas essas ferramentas. O Gateway emprega um modelo de autenticação dupla para garantir o controle de acesso seguro tanto para requisições de entrada quanto para conexões de saída aos recursos de destino. O framework consiste em dois componentes principais: Autenticação de Entrada (Inbound Auth), que valida e autoriza usuários que tentam acessar os alvos do gateway, e Autenticação de Saída (Outbound Auth), que permite ao gateway conectar-se de forma segura aos recursos de backend em nome dos usuários autenticados. Juntos, esses mecanismos de autenticação criam uma ponte segura entre os usuários e seus recursos de destino, suportando tanto credenciais IAM quanto fluxos de autenticação baseados em OAuth. O Gateway suporta conexão de transporte Streamable HTTP do MCP.

![Como funciona](images/gateway-end-end-overview.png)

## Definição de conceitos

Antes de começar, vamos definir alguns conceitos importantes para iniciar com o Amazon Bedrock AgentCore Gateway:

* **Amazon Bedrock AgentCore Gateway**: endpoint HTTP que os clientes podem chamar com um cliente MCP para executar as operações padrão do MCP (ou seja, listTools e invokeTool). Os clientes também podem invocar este AgentCore Gateway usando um SDK da AWS como o boto3.
* **Bedrock AgentCore Gateway Target**: um recurso que o cliente usa para anexar alvos ao seu AgentCore Gateway. Atualmente, os seguintes tipos são suportados como alvos para o AgentCore Gateway:
    * ARNs de Lambda
    * Especificações de API → OpenAPI, Smithy
* **MCP Transport**: mecanismo que define como as mensagens se movem entre clientes (aplicações que usam LLMs) e os servidores MCP. Atualmente, o AgentCore Gateway suporta apenas `conexões Streamable HTTP` como transporte.

## Como funciona

![Como funciona](images/gateway_how_does_it_work.png)

## Autorização de entrada e saída
O Bedrock AgentCore Gateway fornece conexões seguras via autenticação de entrada e saída. Para a autenticação de entrada, o AgentCore Gateway analisa o token OAuth passado durante a invocação para decidir permitir ou negar o acesso a uma ferramenta no gateway. Se uma ferramenta precisa acessar recursos externos, o AgentCore Gateway pode usar autenticação de saída via Chave de API, IAM ou Token OAuth para permitir ou negar o acesso ao recurso externo.

Durante o fluxo de autorização de entrada, um agente ou o cliente MCP chama uma ferramenta MCP no AgentCore Gateway adicionando um token de acesso OAuth (gerado a partir do IdP do usuário). O AgentCore Gateway então valida o token de acesso OAuth e realiza a autorização de entrada.

Se a ferramenta executando no AgentCore Gateway precisa acessar recursos externos, o OAuth recuperará as credenciais dos recursos downstream usando o provedor de credenciais de recurso para o alvo do Gateway. O AgentCore Gateway passa as credenciais de autorização ao chamador para obter acesso à API downstream.

![Acesso seguro](images/gateway_secure_access.png)

### Autorização MCP e Gateway

O Amazon Bedrock AgentCore Gateway está em conformidade com a [especificação de autorização MCP](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization) para a autorização de chamadas de ferramentas MCP de entrada.

![Acesso seguro](images/oauth-flow-gateway.png)

### AgentCore Gateway e integração com AgentCore Identity

![AgentCore Identity com Gateway](images/end-end-auth-gateway.png)

### Busca de Ferramentas
O Amazon Bedrock AgentCore Gateway também inclui uma poderosa capacidade de busca semântica integrada que ajuda agentes e
desenvolvedores a encontrar as ferramentas mais relevantes através de consultas em linguagem natural, **reduzindo o contexto** passado ao seu agente para seleção de ferramentas. Esta funcionalidade de busca é implementada como uma ferramenta pré-construída que utiliza embeddings vetoriais para correspondência semântica. Os usuários podem habilitar este recurso durante a criação do Gateway optando pela API CreateGateway. Uma vez habilitado, quaisquer operações CreateTarget subsequentes acionam automaticamente a geração de embeddings vetoriais para as ferramentas do alvo. Durante este processo, o campo STATUS da resposta do CreateTarget indicará "UPDATING" enquanto os embeddings estão sendo gerados.

![busca_ferramentas](images/gateway_tool_search.png)

### Detalhes do Tutorial


| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity                     |
| Framework de Agentes | Strands Agents                                            |
| Modelo LLM           | Anthropic Claude Haiku 4.5, Amazon Nova Pro              |
| Componentes do tutorial | Criação do AgentCore Gateway e Invocação do AgentCore Gateway |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Fácil                                                  |
| SDK utilizado        | boto3                                                     |

## Arquitetura do Tutorial

### Principais Funcionalidades do Tutorial

#### Acesso Seguro a Ferramentas

O Amazon Bedrock AgentCore Gateway está em conformidade com a [especificação de autorização MCP](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization)
para a autorização de chamadas de ferramentas MCP de entrada.
O Amazon Bedrock AgentCore Gateway também oferece 2 opções para suportar a autorização das chamadas de saída do Gateway:
* usando chave de API ou
* usando tokens de acesso OAuth
Você pode configurar a autorização usando a API de provedor de credenciais do Amazon Bedrock AgentCore Identity e
anexá-los ao Alvo do AgentCore Gateway.
Cada Alvo (AWS Lambda, Smithy e OpenAPI) pode ser anexado a um provedor de credenciais.

#### Integração

O Bedrock AgentCore Gateway integra-se com
* Bedrock AgentCore Identity
* Bedrock AgentCore Runtime

### Casos de uso

* Agentes interativos em tempo real chamando ferramentas MCP
* Autorização de entrada e saída usando diferentes IdPs
* Transformação de funções AWS Lambda, APIs Open e modelos Smithy em MCP
* Descoberta de ferramentas MCP

### Benefícios

* O Gateway oferece vários benefícios importantes que simplificam o desenvolvimento e a implantação de agentes de IA: Sem gerenciamento de infraestrutura
* Serviço totalmente gerenciado sem preocupações com hospedagem. O Amazon Bedrock AgentCore cuida de toda a infraestrutura automaticamente.
* Interface unificada: Um único protocolo MCP para todas as ferramentas elimina a complexidade de gerenciar múltiplos formatos de API e mecanismos de autenticação no código do seu agente.
* Autenticação integrada: O gerenciamento de OAuth e credenciais cuida do ciclo de vida dos tokens, renovação e armazenamento seguro sem esforço adicional de desenvolvimento.
* Escalabilidade automática: Escala automaticamente com base na demanda para lidar com cargas de trabalho variáveis sem intervenção manual ou planejamento de capacidade.
* Segurança empresarial: Recursos de segurança de nível empresarial, incluindo criptografia, controles de acesso e registro de auditoria, garantem acesso seguro às ferramentas.

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [Transformando funções AWS Lambda em ferramentas MCP](01-transform-lambda-into-mcp-tools)
- [Transformando APIs em ferramentas MCP](02-transform-apis-into-mcp-tools)
- [Descobrindo ferramentas MCP](03-discover-mcp-tools)
