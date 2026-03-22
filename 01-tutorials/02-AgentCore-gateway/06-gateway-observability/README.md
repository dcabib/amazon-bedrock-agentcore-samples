Tutorial de Observabilidade do AgentCore Gateway
# Configurar Observabilidade para o AgentCore Gateway com Amazon CloudWatch e AWS CloudTrail

## Visão Geral

A observabilidade é uma capacidade fundamental para o AgentCore Gateway porque fornece insights abrangentes em tempo real sobre o funcionamento e desempenho dos agentes de IA implantados através do gateway. Ao capturar e exibir métricas chave como volumes de requisições, taxas de sucesso, padrões de erro, latência para invocações de ferramentas e eventos de autenticação, os recursos de observabilidade permitem que desenvolvedores e operadores monitorem continuamente a saúde e eficiência de seus fluxos de trabalho de agentes. Este nível de monitoramento ajuda a identificar rapidamente anomalias ou gargalos que poderiam afetar a experiência do usuário ou a confiabilidade do sistema, permitindo solução proativa de problemas e ajuste de desempenho.

Além das métricas de alto nível, a observabilidade do AgentCore Gateway oferece rastreamento detalhado do fluxo de trabalho de cada agente. Cada ação — desde a invocação de ferramentas até chamadas de modelo e recuperação de memória — é registrada como spans e traces em conformidade com os padrões OpenTelemetry. Estes dados ricos de telemetria fornecem aos desenvolvedores uma visão transparente dos processos internos de tomada de decisão dos agentes, incluindo como cada etapa foi executada e sua duração. Tal rastreabilidade granular é inestimável para depurar falhas complexas ou comportamentos inesperados, pois permite que engenheiros investiguem o ponto exato de erro ou ineficiência. Além disso, ao integrar-se com plataformas de monitoramento amplamente utilizadas como o Amazon CloudWatch, esses recursos de observabilidade permitem uma visão operacional unificada e acessível.

Além disso, a observabilidade suporta requisitos de conformidade e governança ao oferecer trilhas de auditoria da atividade dos agentes, o que é crítico para ambientes empresariais. Também facilita a otimização ao revelar padrões de uso e ajudar a ajustar fluxos de trabalho de agentes para reduzir custos ou melhorar a velocidade. Em última análise, essas capacidades de observabilidade transformam o AgentCore Gateway de uma interface caixa-preta em um sistema transparente, gerenciável, que suporta implantação confiável, escalável e performática de agentes de IA em ambientes de produção.

## Observabilidade com Amazon CloudWatch e AWS CloudTrail

* O Amazon CloudWatch foca no monitoramento de desempenho em tempo real e solução de problemas operacionais para o AgentCore Gateway, fornecendo métricas e logs detalhados para latência, taxas de erro e padrões de uso.
* O AWS CloudTrail foca em segurança, conformidade e auditoria ao registrar um histórico completo de chamadas de API e ações de usuários relacionadas ao gateway.

Juntos, eles oferecem um framework holístico de observabilidade e governança para gerenciar o AgentCore Gateway em produção.

![images/1-agentcore-gw-architecture.png]

#### Métricas do CloudWatch do AgentCore Gateway

O Gateway publica as seguintes métricas no Amazon CloudWatch. Elas fornecem informações sobre invocações de API, desempenho e erros.

* **Invocations:** O número total de requisições feitas para cada API do Data Plane. Cada chamada de API conta como uma invocação independentemente do status da resposta.

* **Throttles:** O número de requisições limitadas (código de status 429) pelo serviço.

* **SystemErrors:** O número de requisições que falharam com código de status 5xx.

* **UserErrors:** O número de requisições que falharam com código de status 4xx, exceto 429.

* **Latency:** O tempo decorrido entre quando o serviço recebe a requisição e quando começa a enviar o primeiro token de resposta. Em outras palavras, o tempo de resposta inicial.

* **Duration:** O tempo total decorrido entre o recebimento da requisição e o envio do último token de resposta. Representa o tempo completo de processamento ponta a ponta da requisição.

* **TargetExecutionTime:** O tempo total gasto para executar o alvo via Lambda / OpenAPI / etc. Isso ajuda a determinar a contribuição do alvo para a Latência total.

* **TargetType:** O número total de requisições atendidas por cada tipo de alvo (MCP, Lambda, OpenAPI).

#### Logs Fornecidos pelo CloudWatch do AgentCore Gateway

O AgentCore registra as seguintes informações para recursos do gateway:

* Início e conclusão do processamento de requisições do gateway
* Mensagens de erro para configurações de alvos
* Requisições MCP com cabeçalhos de autorização ausentes ou incorretos
* Requisições MCP com parâmetros de requisição incorretos (ferramentas, método)

O AgentCore pode enviar logs para Amazon CloudWatch, Amazon S3 ou stream do Firehose. Este tutorial foca no CloudWatch.

Se você adicionar Amazon CloudWatch Logs em Entrega de Logs do AgentCore Gateway no console da AWS, esses logs são armazenados no grupo de logs padrão **/aws/vendedlogs/bedrock-agentcore/gateway/APPLICATION_LOGS/{gateway_id}**. Você também pode configurar seu grupo de logs personalizado começando com /**aws/vendedlogs/**.

#### Rastreamento do CloudWatch do AgentCore Gateway

Habilitar o rastreamento no gateway do Amazon Bedrock AgentCore fornece insights profundos sobre o comportamento e desempenho dos seus agentes de IA e as ferramentas com as quais eles interagem. Ele captura o caminho completo de execução de uma requisição conforme ela se move pelo gateway, o que é essencial para depuração eficaz, otimização e auditoria de fluxos de trabalho agênticos complexos.

* **Traces - Contêiner de Nível Superior**

  * Representa o contexto completo de interação
  * Captura o caminho completo de execução começando de uma invocação de agente
  * Pode incluir múltiplas chamadas de agente ao longo da interação
  * Fornece a visão mais ampla de todo o fluxo de trabalho

* **Requests - Invocações Individuais de Agente**

  * Representa um único ciclo de requisição-resposta dentro de um trace
  * Cada invocação de agente cria uma nova requisição
  * Captura uma chamada completa a um agente e sua resposta
  * Múltiplas requisições podem existir dentro de um único trace

* **Spans - Unidades Discretas de Trabalho**

  * Representa operações específicas e mensuráveis dentro de uma requisição
  * Captura etapas granulares como:
    * Inicialização de componentes
    * Execuções de ferramentas
    * Chamadas de API
    * Etapas de processamento
  * Possui timestamps precisos de início/fim para análise de duração

O relacionamento entre esses três componentes de observabilidade pode ser visualizado como:

  Traces (nível mais alto) - Representam conversas completas de usuários ou contextos de interação

  Requests (nível intermediário) - Representam ciclos individuais de requisição-resposta dentro de um Trace

  Spans (nível mais baixo) - Representam operações ou etapas específicas dentro de uma Request

          Trace 1
          ├── Request 1.1
          │   ├── Span 1.1.1
          │   ├── Span 1.1.2
          │   └── Span 1.1.3
          ├── Request 1.2
          │   ├── Span 1.2.1
          │   ├── Span 1.2.2
          │   └── Span 1.2.3
          └── Request 1.N

          Trace 2
          ├── Request 2.1
          │   ├── Span 2.1.1
          │   ├── Span 2.1.2
          │   └── Span 2.1.3
          ├── Request 2.2
          │   ├── Span 2.2.1
          │   ├── Span 2.2.2
          │   └── Span 2.2.3
          └── Request 2.N



#### CloudTrail do AgentCore Gateway

O AgentCore Gateway é totalmente integrado com o AWS CloudTrail, que fornece capacidades abrangentes de registro e monitoramento para **rastrear atividade de API** e eventos operacionais dentro da sua infraestrutura de gateway.

O CloudTrail captura dois tipos distintos de eventos para o AgentCore Gateway:
* Eventos de gerenciamento são registrados automaticamente e capturam operações do plano de controle, como criação, atualização ou exclusão de recursos do gateway
* Eventos de dados, que fornecem informações sobre operações de recursos realizadas no ou dentro de um gateway (também conhecidas como operações do plano de dados), são atividades de alto volume que devem ser explicitamente habilitadas, pois não são registradas por padrão

O CloudTrail captura todas as chamadas de API para o Gateway como eventos, incluindo chamadas do console do Gateway e chamadas de código para as APIs do Gateway. Usando as informações coletadas pelo CloudTrail, você pode determinar a requisição que foi feita ao Gateway, quem fez a requisição, quando foi feita e detalhes adicionais [3]. Eventos de gerenciamento fornecem informações sobre operações de gerenciamento realizadas em recursos na sua conta AWS, também conhecidas como operações do plano de controle.

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos a observabilidade do AgentCore Gateway.


| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway, Amazon CloudWatch, AWS CloudTrail      |
| Framework de Agentes | Strands Agents                                            |
| Tipo de Alvo do Gateway | AWS Lambda                                             |
| IdP de Auth de Entrada | Amazon Cognito                                          |
| Auth de Saída        | AWS IAM                                                   |
| Modelo LLM           | Anthropic Claude Sonnet 4.0                               |
| Componentes do tutorial | Observabilidade do AgentCore Gateway com CloudWatch, CloudTrail |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Fácil                                                  |
| SDK utilizado        | boto3                                                     |

#### Detalhes do Tutorial

* Neste tutorial, criaremos o Bedrock AgentCore Gateway e adicionaremos lambda como tipo de alvo com duas ferramentas: get_order e update_order.
* Criaremos o grupo de entrega de logs com destino CloudWatch e observaremos os logs fornecidos.
* Habilitaremos o Rastreamento do Amazon CloudWatch e conectaremos o ID de trace encontrado nos logs fornecidos com os Traces / Spans para investigar mais profundamente.
* Criaremos o AgentCore Runtime com Strands Agent e percorreremos os Spans.
* Configuraremos Eventos de Gerenciamento e Dados do CloudTrail e verificaremos alguns exemplos.

### Recursos

* [Dados de observabilidade gerados pelo gateway AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-gateway-metrics.html)
* [Habilitar destinos de log e rastreamento para o gateway AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-configure.html#observability-configure-cloudwatch)
* [Registrando chamadas de API do AgentCore Gateway com CloudTrail](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-cloudtrail.html)
* [Configurando Métricas e Alarmes do CloudWatch do AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-advanced-observability-metrics.html)
* [Registrando chamadas de API do Gateway com CloudTrail](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-cloudtrail.html)
* [Conceitos de Observabilidade](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-telemetry.html)
