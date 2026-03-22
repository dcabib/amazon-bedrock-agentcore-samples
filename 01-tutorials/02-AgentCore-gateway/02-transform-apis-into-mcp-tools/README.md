# Implementar ferramentas MCP a partir das suas APIs usando o Amazon Bedrock AgentCore Gateway

## Visão Geral
O Bedrock AgentCore Gateway oferece aos clientes uma maneira de transformar suas APIs existentes (OpenAPI e Smithy) em servidores MCP totalmente gerenciados, sem a necessidade de gerenciar infraestrutura ou hospedagem. Os clientes podem trazer suas especificações OpenAPI e Smithy existentes para converter suas ferramentas. O Gateway fornece uma interface uniforme do Model Context Protocol (MCP) para todas essas ferramentas. O Gateway emprega um modelo de autenticação dupla para garantir o controle de acesso seguro tanto para requisições de entrada quanto para conexões de saída aos recursos de destino. O framework consiste em dois componentes principais: Autenticação de Entrada (Inbound Auth), que valida e autoriza usuários que tentam acessar os alvos do gateway, e Autenticação de Saída (Outbound Auth), que permite ao gateway conectar-se de forma segura às APIs de backend em nome dos usuários autenticados usando chave de API, token OAuth e role IAM da AWS.

![Como funciona](images/apis-into-mcp-gateway.png)


## Definição de conceitos

Antes de começar, vamos definir alguns conceitos importantes para iniciar com o Amazon Bedrock AgentCore Gateway:
Você pode agrupar suas APIs OpenAPI ou Smithy e criar um Bedrock AgentCore Gateway Target. Um target é um recurso que você usa para agrupar logicamente suas APIs e anexar ao seu AgentCore Gateway.

## Agrupando suas APIs em alvos do Gateway

Abaixo estão as melhores práticas para agrupar suas APIs em alvos do Gateway:
* Agrupe suas ferramentas MCP com base no domínio de negócio da aplicação agêntica, similar aos princípios de Domain Driven Design aplicáveis ao paradigma de microsserviços.
* Você pode anexar apenas um provedor de credenciais de recurso para autorização de saída por alvo do Gateway. Agrupe as ferramentas com base no autorizador de saída.
* Agrupe suas APIs com base no tipo das APIs, ou seja, OpenAPI, Smithy ou AWS Lambda servindo como ponte para outras APIs empresariais.

![Agrupando as ferramentas de API em alvos](images/api-groups-targets.png)

## Melhores práticas

1. Diretrizes de Qualidade da Documentação
- Escreva resumos claros e descritivos para cada endpoint e recurso da API
- Use descrições em linguagem natural que expliquem o propósito e a funcionalidade
- Inclua casos de uso do mundo real nas descrições
- Evite jargão técnico, a menos que necessário
- Garanta terminologia consistente em toda a documentação

2. Documentação de Schema
- Forneça descrições detalhadas para todos os campos
- Inclua restrições e regras de validação dos campos
- Documente os tipos de dados com precisão
- Adicione exemplos para estruturas de dados complexas
- Explique os relacionamentos entre diferentes schemas

3. Melhores práticas para especificação OpenAPI
- Valide as especificações usando linters OpenAPI
- Garanta versionamento semântico adequado
- Inclua exemplos completos de requisição/resposta
- Documente respostas e códigos de erro
- Adicione definições de esquema de segurança

4. Otimização de busca de ferramentas
- Inclua palavras-chave relevantes naturalmente nas descrições
- Forneça contexto sobre quando usar cada API
- Documente abordagens alternativas ou endpoints relacionados
- Inclua terminologia do domínio de negócio

5. Diretrizes de extração de API
- Identifique a funcionalidade principal necessária para as tarefas do agente
- Crie subconjuntos focados de API com base nos casos de uso
- Mantenha os relacionamentos semânticos entre as APIs extraídas
- Preserve as definições de segurança e schemas comuns
- Documente as dependências entre os componentes extraídos

6. Processo de Extração de API Monolítica:
- Revise a especificação OpenAPI completa
- Mapeie os casos de uso do agente para endpoints específicos e requisitos de autenticação
- Extraia os caminhos e schemas relevantes
- Mantenha as dependências dos componentes
- Valide a especificação extraída
- Teste a eficácia da busca semântica

Lembre-se de revisar e atualizar regularmente a documentação conforme as APIs evoluem, mantendo a qualidade e precisão dos agentes.

## Autorização de entrada e saída
O Bedrock AgentCore Gateway fornece conexões seguras via autenticação de entrada e saída. Para a autenticação de entrada, o AgentCore Gateway analisa o token OAuth passado durante a invocação para decidir permitir ou negar o acesso a uma ferramenta no gateway. Se uma ferramenta precisa acessar recursos externos, o AgentCore Gateway pode usar autenticação de saída via Chave de API, IAM ou Token OAuth para permitir ou negar o acesso ao recurso externo.

Durante o fluxo de autorização de entrada, um agente ou o cliente MCP chama uma ferramenta MCP no AgentCore Gateway adicionando um token de acesso OAuth (gerado a partir do IdP do usuário). O AgentCore Gateway então valida o token de acesso OAuth e realiza a autorização de entrada.

Se a ferramenta executando no AgentCore Gateway precisa acessar recursos externos, o OAuth recuperará as credenciais dos recursos downstream usando o provedor de credenciais de recurso para o alvo do Gateway. O AgentCore Gateway passa as credenciais de autorização ao chamador para obter acesso à API downstream.

![Acesso seguro](../images/gateway_secure_access.png)

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

* Converter APIs OpenAPI em ferramentas MCP
* Converter modelos Smithy em ferramentas MCP

### Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [Transformar suas OpenAPIs em ferramentas MCP](01-transform-openapi-into-mcp-tools)
- [Transformar seus modelos Smithy em ferramentas MCP](02-transform-smithyapis-into-mcp-tools)
