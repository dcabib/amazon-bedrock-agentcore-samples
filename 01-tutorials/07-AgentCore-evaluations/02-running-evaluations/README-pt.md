# Executando Avaliações

## Visão Geral

Neste tutorial, você aprenderá como usar o AgentCore Evaluations para avaliar o desempenho do seu agente usando abordagens de avaliação sob demanda e online. Você aplicará avaliadores integrados e personalizados para analisar interações do agente e monitorar a qualidade em escala.

## O Que Você Vai Aprender

- Executar avaliações sob demanda para avaliação direcionada de interações específicas
- Configurar avaliações online para monitoramento contínuo em produção
- Analisar resultados de avaliação para melhorar a qualidade do agente
- Usar traces do AgentCore Observability como entradas para avaliações

## Pré-requisitos

Antes de iniciar este tutorial, você deve ter:
- Completado o [Tutorial 00: Pré-requisitos](../00-prereqs) - Criado agentes de exemplo (Strands e/ou LangGraph)
- Completado o [Tutorial 01: Criando Avaliadores Personalizados](../01-creating-custom-evaluators) - Criado um avaliador personalizado
- Seu agente implantado no AgentCore Runtime com observabilidade habilitada
- Gerado pelo menos uma sessão com traces no AgentCore Observability

## Tipos de Avaliação

### Avaliações Sob Demanda

A avaliação sob demanda fornece uma maneira flexível de avaliar interações específicas do agente, analisando diretamente um conjunto escolhido de spans, traces ou sessões.

**Características Principais:**
- **Avaliação direcionada**: Avalie interações específicas fornecendo IDs de span, trace ou sessão
- **Execução síncrona**: Obtenha resultados imediatos para suas solicitações de avaliação
- **Escopo flexível**: Avalie spans individuais, traces completos ou sessões inteiras
- **Ferramenta de investigação**: Perfeita para analisar interações específicas de clientes ou validar correções

**Quando Usar Avaliações Sob Demanda:**
- Investigar interações específicas de clientes ou problemas reportados
- Validar correções para problemas identificados
- Analisar dados históricos para melhorias de qualidade
- Testar avaliadores antes de implantá-los em produção
- Realizar análise aprofundada em casos extremos

**Como Funciona:**

![Avaliações Sob Demanda](../images/on_demand_evaluations.png)

1. Seu agente gera traces no AgentCore Observability
2. Os traces são mapeados para sessões e armazenados em grupos de logs do CloudWatch
3. Você seleciona sessões ou traces específicos para avaliar
4. Você especifica quais avaliadores (integrados ou personalizados) aplicar
5. O AgentCore Evaluations processa os traces selecionados e retorna resultados detalhados

### Avaliações Online

A avaliação online permite o monitoramento contínuo de qualidade de agentes implantados em ambientes de produção com base no tráfego em tempo real.

**Características Principais:**
- **Monitoramento contínuo**: Avalia automaticamente o desempenho do agente conforme as interações ocorrem
- **Baseada em amostragem**: Configure amostragem baseada em porcentagem ou filtros condicionais
- **Insights em tempo real**: Acompanhe tendências de qualidade e detecte regressões precocemente
- **Pronta para produção**: Projetada para escala com impacto mínimo no desempenho

**Quando Usar Avaliações Online:**
- Monitorar o desempenho do agente em produção continuamente
- Detectar regressões de qualidade antes que impactem os usuários
- Identificar padrões nas interações dos usuários em escala
- Manter desempenho consistente do agente ao longo do tempo
- Testes A/B de diferentes configurações de agente

**Como Funciona:**

![Avaliações Online](../images/online_evaluations.png)

1. Seu agente gera traces no AgentCore Observability
2. Você cria uma configuração de avaliação online especificando:
   - Fonte de dados (grupo de logs do CloudWatch ou endpoint do AgentCore Runtime)
   - Taxa de amostragem (ex.: avaliar 10% de todas as sessões)
   - Avaliadores a aplicar (integrados e/ou personalizados)
3. O AgentCore Evaluations processa continuamente os traces recebidos com base nas suas regras
4. Os resultados são enviados ao CloudWatch para visualização em dashboards e análise
5. Você monitora pontuações agregadas, acompanha tendências e investiga sessões com baixa pontuação

## Integração com AgentCore Observability

Ambos os tipos de avaliação dependem do **AgentCore Observability** para capturar o comportamento do agente através de traces OpenTelemetry (OTEL).

**Como a Observabilidade Funciona:**

![Traces de Observabilidade](../images/observability_traces.png)

O AgentCore depende do **AWS Distro for OpenTelemetry (ADOT)** para instrumentar diferentes tipos de traces OTEL em vários frameworks de agentes:

**Para agentes hospedados no AgentCore Runtime** (como os agentes nestes tutoriais):
- A instrumentação é automática com configuração mínima
- Simplesmente inclua `aws-opentelemetry-distro` no seu `requirements.txt`
- O AgentCore Runtime lida com a configuração OTEL automaticamente
- Os traces aparecem no Dashboard de Observabilidade GenAI do CloudWatch

**Para agentes fora do Runtime:**
- Configure variáveis de ambiente para direcionar a telemetria ao CloudWatch
- Execute seu agente com instrumentação OpenTelemetry
- Veja a [documentação do AgentCore Observability](../../06-AgentCore-observability) para detalhes

## Estrutura do Tutorial

Este tutorial fornece exemplos para os frameworks **Strands Agents** e **LangGraph** para demonstrar as capacidades agnósticas de framework do AgentCore:

### [01-strands](01-strands/)
Exemplos usando o Strands Agents SDK:
- **01-on-demand-eval.ipynb**: Execute avaliações direcionadas em traces específicos
- **02-online-eval.ipynb**: Configure monitoramento contínuo em produção

### [02-langgraph](02-langgraph/)
Exemplos usando o framework LangGraph:
- **01-on-demand-eval.ipynb**: Execute avaliações direcionadas em traces específicos
- **02-online-eval.ipynb**: Configure monitoramento contínuo em produção

Ambas as implementações demonstram os mesmos conceitos de avaliação e produzem resultados equivalentes, mostrando como o AgentCore Evaluations funciona consistentemente em diferentes frameworks de agentes.

## Próximos Passos

Após completar este tutorial:
- Prossiga para o [Tutorial 03: Avançado](../03-advanced) para explorar capacidades avançadas incluindo:
  - Uso do SDK boto3 para consultar logs do CloudWatch para avaliação sob demanda
  - Criação de dashboards locais para visualizar experimentos com diferentes configurações de agente
  - Estratégias avançadas de filtragem e amostragem para avaliações online
