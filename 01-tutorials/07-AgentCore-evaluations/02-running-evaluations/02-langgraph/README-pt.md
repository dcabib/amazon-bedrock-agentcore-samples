# Executando Avaliações com LangGraph

## Visão Geral

Este tutorial demonstra como usar o AgentCore Evaluations com agentes construídos usando [LangGraph](https://www.langchain.com/langgraph). Você aprenderá a executar avaliações sob demanda e online para avaliar e monitorar o desempenho do seu agente LangGraph usando avaliadores integrados e personalizados.

## O Que Você Vai Aprender

- Executar avaliações sob demanda em traces específicos do agente LangGraph
- Configurar avaliações online para monitoramento contínuo de agentes LangGraph
- Usar o AgentCore Starter Toolkit para gerenciar avaliações
- Analisar resultados de avaliação para melhorar a qualidade do agente

## Pré-requisitos

Antes de iniciar estes tutoriais, certifique-se de ter:

- Completado o [Tutorial 00: Pré-requisitos](../../00-prereqs) e criado o agente LangGraph (`eval_agent_langgraph.py`)
- Completado o [Tutorial 01: Criando Avaliadores Personalizados](../../01-creating-custom-evaluators) e criado um avaliador personalizado
- Seu agente LangGraph implantado no AgentCore Runtime
- Gerado pelo menos uma sessão com traces invocando seu agente
- Python 3.10+ instalado
- Credenciais AWS configuradas com permissões apropriadas

## Estrutura do Tutorial

### [01-on-demand-eval.ipynb](01-on-demand-eval-pt.ipynb)

**Tipo de Tutorial:** Avaliando agente LangGraph com avaliadores sob demanda (integrados e personalizados)

**O Que Você Vai Aprender:**

- Como recuperar informações de sessão e trace do seu agente LangGraph implantado
- Inicializar o cliente AgentCore Evaluations usando o Starter Toolkit
- Executar avaliações sob demanda em traces ou sessões específicas
- Usar avaliadores integrados (ex.: `Builtin.Correctness`, `Builtin.Helpfulness`) e avaliadores personalizados
- Interpretar resultados de avaliação incluindo pontuações, explicações e uso de tokens

**Conceitos-Chave:**

- **Avaliação Direcionada**: Avalie interações específicas fornecendo IDs de sessão ou trace
- **Execução Síncrona**: Obtenha resultados imediatos para suas solicitações de avaliação
- **Seleção Flexível de Avaliadores**: Aplique múltiplos avaliadores ao mesmo trace
- **Ferramenta de Investigação**: Perfeita para analisar interações específicas ou validar correções

### [02-online-eval.ipynb](02-online-eval-pt.ipynb)

**Tipo de Tutorial:** Avaliando agente LangGraph com avaliadores online (integrados e personalizados)

**O Que Você Vai Aprender:**

- Criar configurações de avaliação online para seu agente LangGraph
- Configurar taxas de amostragem e regras de filtragem
- Configurar avaliação contínua com avaliadores integrados e personalizados
- Monitorar resultados de avaliação em dashboards do CloudWatch
- Gerenciar configurações de avaliação online (habilitar, desabilitar, atualizar, excluir)

**Conceitos-Chave:**

- **Monitoramento Contínuo**: Avalie automaticamente o desempenho do agente conforme as interações ocorrem
- **Baseado em Amostragem**: Configure amostragem baseada em porcentagem (ex.: avaliar 10% das sessões)
- **Insights em Tempo Real**: Acompanhe tendências de qualidade e detecte regressões precocemente
- **Pronto para Produção**: Projetado para escala com impacto mínimo no desempenho

## Arquitetura do Agente LangGraph

O agente LangGraph usado nestes tutoriais inclui:

**Ferramentas:**

- Ferramenta de matemática para cálculos básicos
- Ferramenta de clima para informações meteorológicas

**Modelo:**

- Anthropic Claude Haiku 4.5 do Amazon Bedrock

**Observabilidade:**

- Instrumentação OTEL automática via AgentCore Runtime
- Traces disponíveis no Dashboard de Observabilidade GenAI do CloudWatch

## Como as Avaliações Funcionam com Agentes LangGraph

1. **Invocação do Agente**: Seu agente LangGraph processa solicitações do usuário
2. **Geração de Traces**: O AgentCore Observability captura traces OTEL automaticamente
3. **Armazenamento de Traces**: Os traces são armazenados em grupos de logs do CloudWatch
4. **Avaliação**:
   - **Sob demanda**: Você seleciona sessões/traces específicos para avaliar
   - **Online**: O AgentCore amostra e avalia automaticamente com base na sua configuração
5. **Análise de Resultados**: Visualize pontuações, explicações e tendências no CloudWatch

## Usando o AgentCore Starter Toolkit

Ambos os notebooks usam o **AgentCore Starter Toolkit** para simplificar os fluxos de trabalho de avaliação:

```python
from bedrock_agentcore_starter_toolkit import Evaluations

# Inicializar o cliente de avaliações
evaluations = Evaluations()

# Avaliação sob demanda
result = evaluations.evaluate_session(
    session_id="your-session-id",
    evaluator_ids=["Builtin.Correctness", "your-custom-evaluator-id"]
)

# Avaliação online
config = evaluations.create_online_evaluation(
    config_name="your-config-name",
    sampling_percentage=100,
    evaluator_ids=["Builtin.Helpfulness", "your-custom-evaluator-id"]
)
```

## Resultados Esperados

Após completar estes tutoriais, você será capaz de:

- Avaliar interações específicas do agente LangGraph usando avaliações sob demanda
- Configurar monitoramento contínuo de qualidade para agentes LangGraph em produção
- Analisar resultados de avaliação para identificar áreas de melhoria
- Usar avaliadores integrados e personalizados de forma eficaz
- Monitorar tendências de qualidade do agente ao longo do tempo

## Próximos Passos

Após completar estes tutoriais específicos do LangGraph:

- Explore os [exemplos com Strands](../01-strands/) para ver como as avaliações funcionam com diferentes frameworks
- Prossiga para o [Tutorial 03: Avançado](../../03-advanced) para técnicas avançadas de avaliação
- Revise seus resultados de avaliação no Dashboard de Observabilidade GenAI do CloudWatch
