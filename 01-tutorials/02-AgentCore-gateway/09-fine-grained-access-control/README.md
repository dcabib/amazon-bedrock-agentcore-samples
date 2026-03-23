# Controle de Acesso Granular com Interceptores do AgentCore Gateway usando escopos JWT

## Visão Geral
Sistemas modernos de agentes empresariais frequentemente expõem múltiplas ferramentas — busca, recuperação, sistemas de pedidos, analytics, pipelines de documentos e mais. Nem todos os usuários devem ter permissão para acessar todas as ferramentas, e diferentes papéis (Analista, Gerente, Auditor, Contratado, etc.) podem requerer diferentes níveis de acesso. O Controle de Acesso Granular (FGAC) no AgentCore Gateway aborda este desafio através de Interceptores do Gateway, que são funções Lambda personalizadas que processam requisições em dois pontos críticos: antes de alcançar o destino alvo (Interceptor de Requisição) e antes de retornar o resultado ao agente chamador (Interceptor de Resposta).

![Como funciona](images/fgac.png)

### Implementando Controle de Acesso Granular para Invocação de Ferramentas
O interceptor do Gateway lida com o controle de invocação de ferramentas utilizando escopos JWT para validar permissões de usuários, implementando verificações de autorização antes da execução da ferramenta e suportando tanto acesso total ao alvo quanto permissões específicas por ferramenta. Quando um acesso não autorizado é tentado, o lambda bloqueia requisições antes que alcancem o alvo e retorna erros MCP estruturados. Isso garante o gerenciamento seguro de acesso em todas as interações com ferramentas.

### Filtragem Dinâmica de Ferramentas
Para descoberta e filtragem de ferramentas, o interceptor do Gateway gerencia o acesso através de dois métodos principais: Listar Ferramentas e Busca Semântica. Ao processar operações tools/list, o interceptor de resposta filtra as ferramentas disponíveis com base nos escopos JWT do usuário, garantindo que apenas ferramentas autorizadas sejam retornadas ao agente solicitante. Da mesma forma, para operações de busca semântica, o interceptor de resposta processa os resultados da busca antes de retorná-los aos agentes, removendo quaisquer ferramentas não autorizadas e aplicando a mesma lógica de permissão das operações de listagem. Esta abordagem mantém atualizações dinâmicas de permissão sem cache e garante controle de acesso consistente em todos os métodos de descoberta.

Esta abordagem abrangente de controle de acesso oferece vários benefícios chave, incluindo acesso seguro a ferramentas baseado em papéis e permissões de usuários, filtragem dinâmica sem cache de estado de permissão, autorização consistente em todos os métodos de descoberta de ferramentas, integração simplificada com sistemas de autenticação existentes e riscos de segurança reduzidos através da validação antecipada de requisições. A implementação garante que os usuários só possam descobrir e acessar ferramentas apropriadas para seu papel, mantendo um ambiente empresarial seguro e escalável.

### Detalhes do Tutorial


| Informação           | Detalhes                                                                        |
|:---------------------|:-----------------------------------------------------------------------         |
| Tipo de tutorial     | Interativo                                                                      |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity, AgentCore Runtime, Interceptores do Gateway |
| Framework de Agentes | Strands Agents                                                                  |
| Tipo de Alvo do Gateway | Servidor MCP                                                                 |
| Inbound Auth IdP | Amazon Cognito, mas pode usar outros                                          |
| Outbound Auth        | Amazon Cognito, mas pode usar outros                                            |
| Componentes do tutorial | Controle de Acesso Granular através de Interceptores do AgentCore Gateway    |
| Vertical do tutorial | Cross-vertical                                                                  |
| Complexidade do exemplo | Fácil-intermediário                                                          |
| SDK utilizado        | boto3                                                                           |

## Principais Funcionalidades do Tutorial

* Controle de Acesso Granular com Interceptores do AgentCore Gateway usando escopos personalizados para ações MCP chave.

## Visão Geral do Tutorial

Nestes tutoriais, cobriremos a seguinte funcionalidade:

- [Controle de Acesso Granular com Interceptores do AgentCore Gateway usando escopos personalizados](01-fine-grained-access-control-using-custom-scopes.ipynb).
