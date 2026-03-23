# Integrar seu Servidor MCP com o AgentCore Gateway

## Visão Geral
O Amazon Bedrock AgentCore Gateway agora suporta servidores MCP como alvos nativos, juntamente com APIs REST e funções AWS Lambda existentes. Esta melhoria permite que organizações integrem suas implementações de servidores MCP através de uma interface unificada, eliminando a necessidade de escrever código de cliente personalizado por servidor MCP. O Gateway aborda desafios empresariais chave na escalabilidade de implantações de agentes de IA entre múltiplas equipes e servidores, centralizando o gerenciamento de ferramentas, autenticação e roteamento.

O Gateway emprega um framework de gerenciamento centralizado que simplifica a descoberta de ferramentas, padroniza protocolos de segurança e reduz a complexidade operacional ao escalar de dezenas para centenas de servidores MCP. Esta abordagem unificada permite que empresas mantenham padrões consistentes de segurança e operação enquanto gerenciam eficientemente sua infraestrutura de agentes de IA através de uma única interface, eliminando a necessidade de múltiplos gateways separados e reduzindo a carga geral de manutenção.

![Como funciona](images/mcp-server-target.png)

### Atualizando definições de ferramentas dos seus servidores MCP no AgentCore Gateway
A API SynchronizeGateway permite a sincronização sob demanda de ferramentas de alvos de servidores MCP através de uma sequência de etapas cuidadosamente orquestradas. Um Administrador de Operações inicia o processo fazendo uma chamada à API SynchronizeGateway para o AgentCore Gateway, lançando uma operação assíncrona para atualizar as definições de ferramentas. Este controle é particularmente valioso após modificar configurações de servidores MCP.

Para alvos autenticados via OAuth, o AgentCore Gateway primeiro se comunica com o serviço AgentCore Identity para obter e validar credenciais. O serviço Identity atua como um provedor de credenciais de recurso OAuth, retornando os tokens necessários. Se a validação de credenciais falhar nesta etapa, o processo de sincronização é imediatamente encerrado e o alvo transiciona para o estado FAILED.

Após autenticação bem-sucedida (ou imediatamente para alvos configurados sem autenticação), o Gateway inicializa uma sessão com o servidor MCP, estabelecendo uma conexão segura. O Gateway então faz chamadas paginadas usando a capacidade tools/list, processando ferramentas em lotes eficientes de 100 para otimizar desempenho e utilização de recursos.

Conforme as ferramentas são recuperadas, o Gateway normaliza suas definições adicionando prefixos específicos do alvo para prevenir conflitos de nomenclatura com outros alvos. Este processo de normalização mantém a consistência enquanto preserva metadados essenciais das definições originais do servidor MCP. Durante todo o processo, o Gateway impõe um limite estrito de 10.000 ferramentas por alvo para garantir a estabilidade do sistema. A API implementa bloqueio otimista durante a sincronização para prevenir modificações concorrentes que poderiam levar a estados inconsistentes. As definições de ferramentas em cache garantem desempenho consistente e alto para operações ListTools entre sincronizações.

![Como funciona](images/mcp-server-target-explicit-sync.png)

### Sincronização implícita do schema de ferramentas
Durante as operações CreateGatewayTarget e UpdateGatewayTarget, o AgentCore Gateway sincroniza automaticamente os schemas de ferramentas, diferindo da API explícita SynchronizeGateway. Esta sincronização integrada garante que alvos MCP novos ou atualizados estejam prontos para uso imediato e mantém a consistência dos dados. Embora isso torne as operações de criação/atualização mais lentas em comparação com outros tipos de alvo, garante que alvos marcados como READY tenham definições de ferramentas válidas e previne problemas com alvos com definições de ferramentas não validadas.

![Como funciona](images/mcp-server-target-implicit-sync.png)

### Detalhes do Tutorial


| Informação           | Detalhes                                                               |
|:---------------------|:-----------------------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                             |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity, AgentCore Runtime               |
| Framework de Agentes | Strands Agents                                                         |
| Tipo de Alvo do Gateway | Servidor MCP                                                        |
| Inbound Auth IdP | Amazon Cognito, mas pode usar outros                                 |
| Outbound Auth        | Amazon Cognito, mas pode usar outros                                   |
| Modelo LLM           | Anthropic Claude Sonnet 4                                              |
| Componentes do tutorial | Criação do AgentCore Gateway com Alvo MCP e sincronização das ferramentas |
| Vertical do tutorial | Cross-vertical                                                         |
| Complexidade do exemplo | Fácil                                                               |
| SDK utilizado        | boto3                                                                  |

## Arquitetura do Tutorial

### Principais Funcionalidades do Tutorial

* Integrar o Servidor MCP com o AgentCore Gateway
* Realizar sincronização explícita e implícita para atualizar definições de ferramentas

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [Integrar o Servidor MCP com o AgentCore Gateway](01-mcp-server-target.ipynb)
- [Realizar sincronização explícita e implícita para atualizar definições de ferramentas](02-mcp-target-synchronization.ipynb)
