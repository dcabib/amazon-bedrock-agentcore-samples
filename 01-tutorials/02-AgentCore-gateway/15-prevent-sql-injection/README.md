# Prevenindo Ataques de Injeção SQL com Interceptores do Amazon Bedrock AgentCore Gateway

> [!CAUTION]
> Os exemplos fornecidos neste repositório são apenas para fins experimentais e educacionais. Eles demonstram conceitos e técnicas, mas não são destinados ao uso direto em ambientes de produção. Certifique-se de ter o Amazon Bedrock Guardrails configurado para proteção contra [injeção de prompt](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html).

## Visão Geral

Sistemas modernos de agentes de IA frequentemente interagem com bancos de dados para recuperar, atualizar ou analisar dados. Quando agentes geram consultas dinamicamente ou aceitam entradas de usuários que influenciam operações de banco de dados, vulnerabilidades de injeção SQL se tornam uma preocupação crítica de segurança. Os riscos tradicionais de injeção SQL são amplificados em sistemas de IA porque agentes podem construir dinamicamente instruções SQL, a manipulação de prompts pode influenciar argumentos de ferramentas, e contratos de ferramentas podem permitir entradas flexíveis ou de formato livre que contornam a validação tradicional de entrada.

O Amazon Bedrock AgentCore Gateway aborda este desafio através de interceptores REQUEST, que são funções AWS Lambda que analisam e validam argumentos de ferramentas antes que alcancem as ferramentas de banco de dados backend. Isso fornece aplicação determinística no nível da ferramenta na fronteira de execução, garantindo que tentativas maliciosas de injeção SQL sejam bloqueadas antes que qualquer consulta ao banco de dados seja executada.

![Arquitetura de Prevenção de Injeção SQL](images/sql-injection-prevention.png)

### Proteção contra Injeção SQL no Nível da Ferramenta

O interceptor do Gateway fornece proteção no nível da ferramenta analisando argumentos de ferramentas antes da execução no banco de dados, detectando padrões comuns de injeção SQL através de correspondência de padrões e bloqueando requisições maliciosas com comportamento fail-closed. Quando uma injeção SQL é detectada, a função AWS Lambda bloqueia requisições antes que alcancem a ferramenta de banco de dados e retorna avisos de segurança genéricos sem expor detalhes do ataque. Isso garante aplicação determinística na fronteira da ferramenta sem depender do julgamento do modelo.

### Proteção no Nível do Agente vs Nível da Ferramenta

O Amazon Bedrock Guardrails protege contra ataques de injeção de prompt no próprio agente. No entanto, uma vez que o agente decide chamar uma ferramenta, o prompt já passou pela camada do agente. O interceptor REQUEST do Gateway fornece uma segunda linha de defesa analisando os argumentos reais da ferramenta (parâmetros de consulta) antes que alcancem o banco de dados. Esta separação garante que, mesmo se um agente for manipulado para chamar uma ferramenta de banco de dados, os argumentos da ferramenta são validados independentemente na camada do Gateway.

Esta abordagem abrangente de segurança de banco de dados oferece vários benefícios chave, incluindo aplicação determinística no nível da ferramenta, defesa em profundidade para sistemas de agentes de IA, detecção rápida baseada em padrões sem chamadas de API externas, política de segurança centralizada em todas as ferramentas de banco de dados e registro detalhado de auditoria para análise de segurança. A implementação garante que tentativas de injeção SQL sejam bloqueadas antes da execução no banco de dados, mantendo um ambiente empresarial seguro e escalável.

### Detalhes do Tutorial

## Detalhes do Tutorial

| Informação             | Detalhes                                                                     |
|:-----------------------|:-----------------------------------------------------------------------------|
| Tipo de tutorial       | Interativo                                                                   |
| Componentes AgentCore  | Amazon Bedrock AgentCore Gateway, Interceptores do Gateway                   |
| Tipo de Alvo do Gateway| Servidor MCP (ferramenta de banco de dados baseada em Lambda)                |
| Tipos de interceptor   | AWS Lambda (REQUEST)                                                         |
| IdP de Auth de Entrada | Amazon Cognito (autorizador CUSTOM_JWT)                                      |
| Padrão de segurança    | Detecção de injeção SQL usando correspondência de padrões                    |
| Componentes do tutorial| Amazon Bedrock AgentCore Gateway, Interceptor AWS Lambda, Amazon Cognito, ferramentas MCP |
| Vertical do tutorial   | Cross-vertical (aplicável a qualquer agente de IA com acesso a banco de dados) |
| Complexidade do exemplo| Intermediário                                                                |
| SDK utilizado          | boto3                                                                        |

## Principais Funcionalidades do Tutorial

* Prevenção de injeção SQL com interceptores REQUEST do Amazon Bedrock AgentCore Gateway usando correspondência de padrões para fins de demonstração.

> **Nota:** Esta implementação usa correspondência de padrões integrada para demonstração. Interceptores AWS Lambda podem se integrar com quaisquer ferramentas de segurança de terceiros, APIs externas ou serviços AWS (como Amazon Bedrock Guardrails, AWS WAF, feeds de inteligência de ameaças ou detecção baseada em ML). Sistemas de produção devem usar consultas parametrizadas e templates de consulta estruturados para eliminar SQL bruto completamente.

## Visão Geral do Tutorial

Neste tutorial, cobriremos a seguinte funcionalidade:

- [Prevenindo Injeção SQL com Interceptor do Gateway](01-prevent-sql-injection-with-interceptor.ipynb)
