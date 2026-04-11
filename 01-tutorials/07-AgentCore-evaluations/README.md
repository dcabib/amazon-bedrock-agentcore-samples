# Visão Geral

O Amazon Bedrock AgentCore Evaluations ajuda você a otimizar a qualidade do seu agente com base em interações do mundo real.

## Principais Funcionalidades

Enquanto o AgentCore Observability fornece insights operacionais sobre a saúde do agente, o AgentCore Evaluations foca na qualidade das decisões do agente e nos resultados de desempenho.

Ele oferece avaliadores integrados e personalizados com capacidades de avaliação sob demanda e online.

### Avaliadores Integrados e Personalizados

O AgentCore Evaluations oferece 13 avaliadores integrados para dimensões críticas como correção, utilidade e segurança, além da capacidade de criar avaliadores personalizados para requisitos específicos do negócio.

Teste seus agentes durante o desenvolvimento e implantação usando a API de avaliações sob demanda, ou monitore agentes em produção com a API de avaliações online.

### Avaliações Sob Demanda

Execute avaliações síncronas e sob demanda usando métricas integradas e personalizadas em traces individuais.

O sistema usa traces OpenTelemetry (OTEL) para realizar a pontuação e retorna uma resposta que inclui:
- Valor da pontuação
- Explicação da pontuação
- Uso de tokens

Avaliações Online

Em produção, você precisa de monitoramento contínuo de desempenho em todas as interações sem avaliar manualmente cada trace. Uma amostra estatística geralmente é suficiente para gerar métricas de desempenho significativas.

As capacidades online do AgentCore Evaluations permitem amostragem e avaliação automáticas:

- Defina o tamanho da amostra e os critérios de seleção de traces
- Escolha suas métricas de avaliação (integradas ou personalizadas)
- O AgentCore Evaluations cuida do resto, gerando os dados de desempenho necessários para monitorar seu agente em escala

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:
- [Pré-requisitos](00-prereqs): Criando um agente de exemplo para usar durante os tutoriais de avaliação
- [Criar um avaliador personalizado](01-creating-custom-evaluators): Aprenda sobre métricas integradas e personalizadas, e crie uma métrica personalizada para avaliar seus agentes
- [Usando avaliações sob demanda e online](02-running-evaluations): Aprenda como usar avaliações sob demanda e online para construir, otimizar e monitorar seu agente em escala
- [Avançado](03-advanced): Explore capacidades avançadas incluindo o uso do SDK boto3 para consultar logs do Amazon CloudWatch para avaliação sob demanda, e criação de dashboards locais para visualizar experimentos com diferentes configurações de agente
