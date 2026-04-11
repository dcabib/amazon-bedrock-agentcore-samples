# Avaliação Offline Multi-Sessão

Avalie sessões de agentes de IA implantados usando traces históricos do AgentCore Observability. Esta ferramenta busca traces dos logs de observabilidade do seu agente, converte-os para o formato Strands Evals, executa avaliações e registra os resultados de volta no AgentCore Observability com os IDs de trace originais para correlação no dashboard.

## Caso de Uso

Quando você tem um agente de IA implantado com instrumentação do AgentCore Observability, esta ferramenta permite que você:

- Execute avaliações offline em interações históricas do agente
- Reavalie sessões que tiveram pontuação baixa com rubricas atualizadas
- Teste novas configurações de avaliadores contra traces existentes
- Compare saídas do agente contra ground truth (respostas esperadas de especialistas)
- Realize testes de regressão para garantir que mudanças no agente não quebrem comportamentos conhecidos como bons
- Correlacione resultados de avaliação com traces originais no dashboard do AgentCore Observability

## Como Funciona

1. **Descoberta de Sessões**: Consulte o AgentCore Observability para encontrar sessões de agentes por intervalo de tempo ou por pontuações de avaliação existentes
2. **Busca de Traces**: Recupere spans para cada sessão usando CloudWatch Logs Insights
3. **Conversão de Formato**: Mapeie spans do AgentCore Observability para o formato Session do Strands Evals (chamadas de ferramentas, respostas do agente, trajetórias)
4. **Avaliação**: Execute avaliadores usando uma de duas abordagens:
   - **Baseada em rubrica**: Pontue contra critérios que você define (flexível, qualitativa)
   - **Ground Truth**: Compare contra saídas esperadas (baseada em referência, testes de regressão)
5. **Registro de Resultados**: Envie resultados de avaliação no formato EMF com IDs de trace originais para correlação no dashboard

## Fluxo de Trabalho dos Notebooks

![Fluxo de Trabalho dos Notebooks](images/notebook_workflow.svg)

## Entendendo a Avaliação de Agentes

A avaliação de agentes vai além dos testes de software tradicionais. Enquanto testes unitários verificam saídas determinísticas, agentes produzem respostas variáveis que requerem avaliação qualitativa. A avaliação sistemática ajuda a identificar padrões de falha, medir melhorias ao longo do tempo e garantir qualidade consistente conforme você itera em prompts e ferramentas.

### Duas Abordagens Complementares

**AgentCore Evaluations** e **Strands Evals** trabalham juntos perfeitamente para fornecer gerenciamento abrangente de qualidade de agentes:

| | AgentCore Evaluations | Strands Evals |
|---|---|---|
| **Propósito** | Monitoramento contínuo de qualidade em tempo real | Avaliação offline em lote e experimentação |
| **Caso de Uso** | Monitoramento em produção, alertas sobre quedas de qualidade | Testes, análise de regressão, desenvolvimento de rubricas |
| **Execução** | Totalmente gerenciado, amostra interações ao vivo | Sob demanda, executa em traces históricos |
| **Avaliadores Integrados** | Correção, utilidade, precisão de seleção de ferramentas, segurança, taxa de sucesso de objetivos, relevância de contexto | Saída, trajetória, utilidade, fidelidade, taxa de sucesso de objetivos, precisão de ferramentas |
| **Avaliadores Personalizados** | Pontuação baseada em modelo com prompts personalizados | Qualquer avaliador baseado em código ou LLM |

**AgentCore Evaluations** é um serviço totalmente gerenciado que monitora continuamente o desempenho do agente com base no comportamento do mundo real. Ele amostra interações ao vivo, pontua-as contra avaliadores integrados ou personalizados e visualiza resultados no CloudWatch junto com insights de observabilidade. Configure alertas quando métricas de qualidade caírem abaixo dos limites — como satisfação diminuindo ou pontuações de polidez caindo — para detectar e resolver problemas mais rapidamente.

**Strands Evals** é um framework de avaliação abrangente que fornece múltiplos tipos de avaliação, simuladores dinâmicos para conversas multi-turno, avaliação baseada em traces via OpenTelemetry, geração automatizada de experimentos e uma arquitetura extensível que suporta avaliadores personalizados de qualquer biblioteca. Veja a [documentação do Strands Evals](https://strandsagents.com/latest/documentation/docs/user-guide/evals-sdk/quickstart/) para capacidades completas.

### Este Projeto

Este projeto usa **Strands Evals para avaliação offline** de traces coletados pelo **AgentCore Observability**, demonstrando dois padrões comuns:

- **Qualidade da Saída**: A resposta do agente aborda correta e completamente a solicitação do usuário? Avalia a resposta final independentemente de como foi produzida.

- **Qualidade da Trajetória**: O agente usou suas ferramentas de forma eficaz? Avalia se o agente selecionou ferramentas apropriadas, usou-as eficientemente e seguiu uma sequência lógica.

Os resultados são registrados de volta no AgentCore Observability com IDs de trace originais, permitindo correlação no dashboard junto com seus resultados do AgentCore Evaluations.

## Conceitos do Strands Evals

Esta ferramenta usa [Strands Evals](https://github.com/strands-agents/strands-evals), um framework de avaliação de propósito geral para agentes de IA. O Strands Evals usa LLMs como juízes para pontuar o comportamento do agente contra critérios definidos por humanos. O framework lida com a variabilidade inerente nas respostas dos agentes quantificando a qualidade em uma escala de 0.0 a 1.0 com explicações.

**Insight principal**: Agentes não produzem respostas "corretas" ou "incorretas" — eles produzem respostas melhores ou piores. O Strands Evals transforma a avaliação subjetiva de qualidade em métricas mensuráveis e consistentes.

Entender seus conceitos principais ajuda você a personalizar avaliações de forma eficaz.

**Sessão**: Representa uma conversa completa do usuário, potencialmente contendo múltiplas trocas de ida e volta. No AgentCore Observability, uma sessão agrupa interações relacionadas por `session.id`.

**Trace**: Uma única solicitação do usuário e a resposta completa do agente, incluindo todas as chamadas de ferramentas feitas para atender àquela solicitação. Cada trace tem um `trace_id` único que se correlaciona com o AgentCore Observability.

**Caso**: Um caso de teste para avaliação contendo a entrada (prompt do usuário), a saída real (resposta do agente) e metadados (trace_id, trajetória de ferramentas). Casos são o que os avaliadores pontuam.

**Experimento**: Uma coleção de casos pareados com um ou mais avaliadores. Executar um experimento produz pontuações e explicações para cada caso.

## Abordagens de Avaliação

O Strands Evals é um framework de avaliação extensível baseado em LLM que suporta múltiplas abordagens de avaliação. Em vez de correspondência exata de strings, ele usa LLMs como juízes para pontuar saídas de agentes. O framework é projetado para flexibilidade — você pode implementar virtualmente qualquer tipo de avaliação.

**Duas abordagens fundamentais de avaliação:**

| Abordagem | Descrição | Use Quando |
|----------|-------------|----------|
| **Baseada em Rubrica** | LLM julga contra critérios que você define | Você quer avaliação flexível e qualitativa |
| **Ground Truth** | Compare contra respostas conhecidas como corretas | Você tem saídas esperadas para medir |

Este projeto demonstra ambas as abordagens em notebooks separados.

### Avaliação Baseada em Rubrica (Notebook 02)

Defina critérios de avaliação em uma rubrica, e o LLM julga cada resposta contra seus critérios. Esta abordagem é ideal quando as respostas podem variar mas ainda ser "boas" de maneiras diferentes.

**OutputEvaluator**: Avalia a qualidade da resposta do agente. Você fornece uma rubrica descrevendo o que faz uma boa resposta (relevância, precisão, completude), e o avaliador usa um LLM para pontuar a saída de 0.0 a 1.0 com uma explicação.

**TrajectoryEvaluator**: Avalia como o agente usou suas ferramentas. Você fornece uma rubrica descrevendo bons padrões de uso de ferramentas (seleção apropriada, eficiência, sequência lógica), e o avaliador pontua a trajetória de ferramentas de 0.0 a 1.0.

### Avaliação Ground Truth (Notebook 03)

Compare saídas reais do agente contra respostas esperadas predefinidas. Esta abordagem é ideal para testes de regressão, benchmarking e casos onde você tem respostas conhecidas como corretas.

O avaliador compara real vs esperado e pontua quão bem a saída do agente corresponde ao que um Especialista no Assunto (SME) definiu como a resposta correta. Veja a seção [Avaliação Ground Truth](#avaliação-ground-truth) para detalhes.

### Extensibilidade

O framework Strands Evals suporta avaliadores personalizados além do que este projeto demonstra. Qualquer avaliação que possa ser expressa como critérios de pontuação — precisão factual, segurança, verificações de qualidade específicas de domínio, requisitos de conformidade — pode ser implementada usando a abordagem LLM-como-juiz.

**Como as rubricas funcionam**: Sua rubrica é enviada a um LLM junto com a saída do agente. O LLM atua como juiz, aplicando seus critérios para produzir uma pontuação e explicação. Rubricas bem escritas com orientação clara de pontuação produzem avaliações mais consistentes.

## Avaliação Ground Truth

A avaliação ground truth compara saídas do agente contra respostas esperadas predefinidas. Isso é útil quando você tem respostas conhecidas como corretas para consultas específicas e quer medir quão próximo seu agente corresponde a elas.

![Fluxo Ground Truth](images/ground_truth_flow.svg)

**Conceitos-chave:**
- **session_id**: Agrupa todos os traces de uma única sessão de usuário
- **trace_id**: Identifica cada interação individual (prompt do usuário + resposta do agente) dentro de uma sessão

**Abordagem de dois arquivos**: O notebook de ground truth usa dois arquivos que compartilham o mesmo `session_id`:

1. **Arquivo de Traces** (`demo_traces.json`): Contém saídas reais do agente do CloudWatch
   ```json
   {
     "session_id": "5B467129-E54A-4F70-908D-CB31818004B5",
     "traces": [
       {
         "trace_id": "693cb6c4e931",
         "user_prompt": "What is the best route for a NZ road trip?",
         "actual_output": "Based on the search results...",
         "actual_trajectory": ["web_search"]
       },
       {
         "trace_id": "693cb6fa87aa",
         "user_prompt": "Should I visit North or South Island?",
         "actual_output": "Here's how the islands compare...",
         "actual_trajectory": ["web_search"]
       }
     ]
   }
   ```

2. **Arquivo Ground Truth** (`demo_ground_truth.json`): Saídas esperadas criadas pelo SME
   ```json
   {
     "session_id": "5B467129-E54A-4F70-908D-CB31818004B5",
     "ground_truth": [
       {
         "trace_id": "693cb6c4e931",
         "expected_output": "Response should mention Milford Road, Southern Scenic Route...",
         "expected_trajectory": ["web_search"]
       },
       {
         "trace_id": "693cb6fa87aa",
         "expected_output": "Response should compare both islands...",
         "expected_trajectory": ["web_search"]
       }
     ]
   }
   ```

**Como funciona:**
1. O notebook busca traces do CloudWatch (ou carrega arquivo de demonstração)
2. O SME cria arquivo ground truth com saídas esperadas para cada `trace_id`
3. O notebook mescla por `trace_id`, pareando real vs esperado
4. Os avaliadores pontuam cada par

**Modo de demonstração**: Execute com `USE_DEMO_MODE = True` para testar usando os arquivos de exemplo fornecidos antes de conectar aos seus próprios dados do CloudWatch.

## Fluxo de Dados

O pipeline de avaliação transforma traces do AgentCore Observability em resultados pontuados:

![Pipeline de Avaliação](images/evaluation_pipeline.svg)

## Estrutura do Projeto

```
01_session_discovery.ipynb        - Notebook 1: Descobrir sessões
02_multi_session_analysis.ipynb   - Notebook 2: Avaliar com rubricas personalizadas
03_ground_truth_evaluation.ipynb  - Notebook 3: Avaliar contra ground truth
demo_traces.json                  - Dados de trace de exemplo (para modo de demonstração)
demo_ground_truth.json            - Expectativas ground truth de exemplo (para modo de demonstração)
config.py                         - Configuração centralizada
requirements.txt                  - Dependências Python
utils/
  __init__.py                     - Exportações do módulo
  cloudwatch_client.py            - Cliente de consultas CloudWatch Logs Insights
  constants.py                    - Constantes e configurações de avaliadores
  evaluation_cloudwatch_logger.py - Logger EMF preservando IDs de trace originais
  models.py                       - Modelos de dados (Span, TraceData, SessionInfo)
  session_mapper.py               - Mapeador de spans do AgentCore Observability para Session do Strands Evals
```

## Início Rápido

### 1. Configurar

Edite `config.py` com suas configurações AWS:

```python
AWS_REGION = "us-east-1"
AWS_ACCOUNT_ID = "123456789012"
SOURCE_LOG_GROUP = "seu-grupo-de-log-do-agente"
EVAL_RESULTS_LOG_GROUP = "seu-grupo-de-log-de-avaliacao"
EVALUATION_CONFIG_ID = "seu-id-de-configuracao-de-avaliacao"
SERVICE_NAME = "seu-nome-de-servico"
```

### 2. Descobrir Sessões

Execute `01_session_discovery.ipynb`:
- Escolha descoberta baseada em tempo (todas as sessões em janela de tempo) ou descoberta baseada em pontuação (sessões por pontuação de avaliação)
- Visualize as sessões descobertas
- Salve em JSON para os notebooks de avaliação

### 3. Avaliar Sessões (Escolha Um Caminho)

**Opção A: Rubricas Personalizadas** - Execute `02_multi_session_analysis.ipynb`:
- Carregue sessões descobertas (ou forneça IDs de sessão personalizados)
- Personalize rubricas de avaliadores para seu caso de uso
- Execute avaliações e visualize resultados
- Os resultados são registrados no AgentCore Observability com IDs de trace originais

**Opção B: Ground Truth** - Execute `03_ground_truth_evaluation.ipynb`:
- Compare saídas do agente contra respostas esperadas predefinidas
- Útil quando você tem respostas conhecidas como corretas para avaliar
- Suporta modo de demonstração com arquivos de exemplo (`demo_traces.json`, `demo_ground_truth.json`)
- Mescle seus traces com ground truth por `trace_id`

## Referência de Configuração

Todas as configurações estão em `config.py`. Edite os valores diretamente.

| Variável | Descrição |
|----------|-------------|
| `AWS_REGION` | Região AWS (ex.: us-east-1) |
| `AWS_ACCOUNT_ID` | Seu ID de conta AWS |
| `SOURCE_LOG_GROUP` | Nome do grupo de log do AgentCore Observability |
| `EVAL_RESULTS_LOG_GROUP` | Nome do grupo de log de resultados de avaliação |
| `EVALUATION_CONFIG_ID` | ID de configuração de avaliação do AgentCore Observability |
| `SERVICE_NAME` | Nome do serviço para logging no CloudWatch |
| `EVALUATOR_NAME` | Nome do avaliador para descoberta baseada em pontuação |
| `LOOKBACK_HOURS` | Horas para retrospectiva de sessões (padrão: 72) |
| `MAX_SESSIONS` | Máximo de sessões a descobrir (padrão: 100) |
| `MIN_SCORE` / `MAX_SCORE` | Filtros de pontuação para descoberta baseada em pontuação |
| `MAX_CASES_PER_SESSION` | Máximo de traces a avaliar por sessão (padrão: 10) |

## Personalização

### Rubricas de Avaliadores

No notebook de análise, personalize as rubricas para corresponder aos seus critérios de avaliação:

```python
output_rubric = """
Avalie a resposta do agente com base em:
1. Relevância: Ela aborda a pergunta do usuário?
2. Precisão: As informações estão corretas?
...
"""
```

### Nomes de Avaliadores

Defina nomes personalizados de avaliadores para métricas do CloudWatch:

```python
OUTPUT_EVALUATOR_NAME = "Custom.SeuAvaliadorDeSaida"
TRAJECTORY_EVALUATOR_NAME = "Custom.SeuAvaliadorDeTrajetoria"
```

### ID de Configuração de Avaliação

Defina o ID de configuração de avaliação em `config.py` para corresponder à sua configuração de avaliação do AgentCore Observability:

```python
EVALUATION_CONFIG_ID = "seu-id-de-configuracao-de-avaliacao"
```

## Requisitos

- Python 3.9+
- Credenciais AWS com acesso ao CloudWatch Logs
- Pacote `strands-evals`
- `boto3`
