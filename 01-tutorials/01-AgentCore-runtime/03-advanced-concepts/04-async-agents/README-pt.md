# Gerador de Relatório de Status Semanal com Amazon Bedrock AgentCore Runtime

## Visão Geral

Neste tutorial aprenderemos como construir e implantar um gerador automatizado de relatórios de status semanal usando o Amazon Bedrock AgentCore Runtime. O agente coleta dados de múltiplas fontes (atualizações da equipe, notas de reuniões, métricas, rastreadores de bugs), realiza análises, gera visualizações e faz upload de relatórios abrangentes para o S3.

### Detalhes do Tutorial

| Informação          | Detalhes                                                                          |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Análise de Dados e Relatórios                                                     |
| Tipo de agente      | Único                                                                            |
| Framework Agêntico  | Strands Agents                                                                   |
| Modelo LLM          | Anthropic Claude Sonnet 4                                                        |
| Componentes do tutorial | Agente multi-ferramenta, análise de dados, visualização, integração S3, AgentCore Runtime|
| Vertical do tutorial | Operações de Negócio e Relatórios                                                |
| Complexidade do exemplo | Intermediário                                                                 |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK, boto3, matplotlib, scikit-learn              |

### Arquitetura do Tutorial

Este tutorial demonstra como implantar um agente de relatórios no runtime AgentCore. O agente usa múltiplas ferramentas para:
- Ler e analisar dados de várias fontes (arquivos CSV, JSON, Markdown)
- Realizar análise de sentimento e pontuação de risco
- Gerar visualizações de dados (gráficos)
- Construir modelos de previsão usando aprendizado de máquina
- Fazer upload de relatórios e visualizações para o S3

O agente orquestra 16 ferramentas diferentes para criar relatórios de status semanal abrangentes automaticamente.

![Diagrama de Arquitetura](01_weekly_report_generator_async/images/architecture.png)

### Principais Funcionalidades do Tutorial

* Hospedagem de um agente assíncrono multi-ferramenta no Amazon Bedrock AgentCore Runtime
* Uso de modelos Amazon Bedrock (Claude Sonnet 4)
* Uso do framework Strands Agents


## Pré-requisitos

- Conta AWS com acesso ao Amazon Bedrock AgentCore
- Python 3.12+
- AWS CLI configurado com credenciais apropriadas
- Bucket S3 para armazenar dados de demonstração e relatórios

## Estrutura do Projeto

```
├── README.md                              # Este arquivo
└── 01_weekly_report_generator_async/     # Código do agente e dados
    ├── weekly_update_agentcore_deploy.ipynb  # Notebook de implantação
    ├── images/                            # Diagramas de arquitetura
    ├── agent/                             # Implementação do agente
    │   ├── agent.py                     # Definição principal do agente
    │   ├── tools.py                     # Todas as funções de ferramentas (16 ferramentas)
    │   ├── requirements.txt             # Dependências Python
    │   └── .dockerignore                # Padrões de ignore do Docker
    ├── demo_data/                       # Diretório de dados de exemplo
    │   ├── team_updates/                # Atualizações de membros da equipe (Markdown)
    │   ├── meeting_notes/               # Notas de reuniões (Markdown)
    │   ├── metrics/                     # Métricas KPI (CSV)
    │   ├── issues/                      # Dados do rastreador de bugs (JSON)
    │   └── project_status/              # Status do projeto (CSV)
    └── update_demo_dates.py             # Script de gerenciamento de dados de demonstração
```



## O que o Agente Faz

Quando invocado, o agente:

1. **Coleta Dados** de múltiplas fontes:
   - Atualizações de membros da equipe (5 membros da equipe)
   - Notas de reuniões (3 reuniões)
   - Métricas KPI (históricas e atuais)
   - Dados do rastreador de bugs
   - Informações de status do projeto

2. **Analisa Dados**:
   - Valida qualidade dos dados
   - Faz referência cruzada de bugs mencionados nas atualizações
   - Realiza análise de sentimento nas atualizações da equipe
   - Calcula pontuações de risco para projetos

3. **Gera Visualizações**:
   - Gráfico de pizza de severidade de bugs
   - Gráficos de barras de status de métricas
   - Gráfico de cronograma de projeto
   - Gráfico de velocidade da equipe
   - Gráfico de previsão de métricas (com previsões ML)

4. **Cria Relatório**:
   - Sintetiza todas as informações em um relatório markdown abrangente
   - Inclui resumo executivo, destaques da equipe, KPIs, riscos e itens de ação

5. **Faz Upload para o S3**:
   - Faz upload do relatório markdown
   - Faz upload de todos os gráficos gerados
   - Organiza por ano e semana: `s3://bucket/weekly_reports/2026/week_09_2026-02-23/`
