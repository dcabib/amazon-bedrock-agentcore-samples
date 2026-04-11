# Sistemas Multi-Agente com Observabilidade

## Visão Geral

Este tutorial demonstra como construir **sistemas multi-agente** com observabilidade completa usando Amazon Bedrock AgentCore Runtime e Observability. Você aprenderá dois padrões para coordenar múltiplos agentes mantendo rastreamento de ponta a ponta através do CloudWatch GenAI Observability.

###  Padrões

```
┌───────────────────────────────────────────────────────────────────────────┐
│                         MULTI-AGENT Patterns                              │
├─────────────────────────────────┬─────────────────────────────────────────┤
│     PART 1: SINGLE RUNTIME      │      PART 2: MULTI-RUNTIME              │
│                                 │                                         │
│  ┌───────────────────────────┐  │  ┌───────────────────────────────────┐  │
│  │   AgentCore Runtime       │  │  │      ORCHESTRATOR (Strands)       │  │
│  │                           │  │  │      AgentCore Runtime #1         │  │
│  │  ┌─────────────────────┐  │  │  └──────────────┬────────────────────┘  │
│  │  │    ORCHESTRATOR     │  │  │                 │                       │
│  │  │      (Strands)      │  │  │         ┌──────┴──────┐                 │
│  │  │         │           │  │  │         ▼             ▼                 │
│  │  │    ┌────┴────┐      │  │  │  ┌────────────┐ ┌────────────┐          │
│  │  │    ▼         ▼      │  │  │  │  TRAVEL    │ │  WEATHER   │          │
│  │  │ TRAVEL    WEATHER   │  │  │  │  (Strands) │ │ (LangGraph)│          │
│  │  │(Strands)  (Strands) │  │  │  │ Runtime #2 │ │ Runtime #3 │          │
│  │  └─────────────────────┘  │  │  └────────────┘ └────────────┘          │
│  └───────────────────────────┘  │                                         │
│                                 │                                         │
│  - Single unified trace         │  - Linked traces via session ID         │
│  - Simple deployment            │  - Mix frameworks (Strands + LangGraph  │
└─────────────────────────────────┴─────────────────────────────────────────┘
```


## Pré-requisitos

1. AWS CLI configurado (`aws configure`) com permissões necessárias
2. Acesso ao modelo Amazon Bedrock habilitado para `global.anthropic.claude-haiku-4-5-20251001-v1:0`
3. CloudWatch Transaction Search habilitado ([Guia de Configuração](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Transaction-Search-getting-started.html))

## Estrutura do Projeto

```
03-multi-runtimes-with-observability/
├── multi_agent_observability.ipynb   # Notebook principal do tutorial
├── utils.py                          # Funções auxiliares
├── requirements.txt                  # Dependências
│
├── single_runtime/                   # Parte 1: Todos os agentes em um runtime
│   ├── multi_agent.py               # Orchestrator + Travel + Weather agents
│   └── requirements.txt
│
├── travel_agent/                     # Parte 2: Agente de viagens baseado em Strands
│   ├── main.py                      # Capacidades de busca na web
│   └── requirements.txt
│
├── weather_agent/                    # Parte 2: Agente de clima baseado em LangGraph
│   ├── main.py                      # Capacidades de consulta de clima
│   └── requirements.txt
│
└── orchestrator_agent/               # Parte 2: Agente coordenador
    ├── main.py                      # Roteia consultas para sub-agentes
    └── requirements.txt
```

## Início Rápido

```bash
# Instalar dependências
pip install -r requirements.txt

# Iniciar o notebook do tutorial
jupyter notebook multi_agent_observability.ipynb
```

## Parte 1: Arquitetura Single Runtime

Todos os agentes executam em um único AgentCore Runtime com chamadas diretas de função entre eles.

```
┌─────────────────────────────────────────────────────────────┐
│                   AgentCore Runtime                          │
│                                                             │
│    User Query ──► ORCHESTRATOR                              │
│                        │                                    │
│                   ┌────┴────┐                               │
│                   ▼         ▼                               │
│              TRAVEL      WEATHER                            │
│              AGENT       AGENT                              │
│                │           │                                │
│                ▼           ▼                                │
│           web_search   get_weather                          │
│                                                             │
│    Telemetry:  CloudWatch GenAI Observability Dashboard     │
└─────────────────────────────────────────────────────────────┘
```

**Pontos Principais:**
- Implantação única, IAM role única
- Árvore de trace unificada no CloudWatch
- Melhor para: agentes fortemente acoplados, propriedade de um único time

## Parte 2: Arquitetura Multi-Runtime

Cada agente executa em seu próprio AgentCore Runtime, comunicando-se via invocação direta com propagação de session ID.

```
┌─────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR                              │
│                 AgentCore Runtime #1                         │
│                   (Strands Agent)                            │
└──────────────────────────┬──────────────────────────────────┘
                           │
            invoke_agent_runtime() + session_id
                           │
           ┌───────────────┴───────────────┐
           ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐
│     TRAVEL AGENT        │     │     WEATHER AGENT       │
│  AgentCore Runtime #2   │     │  AgentCore Runtime #3   │
│     (Strands)           │     │     (LangGraph)         │
│                         │     │                         │
│  Tool: web_search       │     │  Tool: get_weather      │
│  (DuckDuckGo)           │     │  (Mock data)            │
└─────────────────────────┘     └─────────────────────────┘
           │                               │
           └───────────────┬───────────────┘
                           ▼
              CloudWatch GenAI Observability
                   (Linked Traces)
```

**Pontos Principais:**
- Frameworks diferentes por agente (Strands + LangGraph)
- Traces vinculados


## Visualizar Traces

Após executar os agentes, visualize Traces, Sessions, logs, vended logs e métricas nos Dashboards do CloudWatch e monitore os logs e métricas no CloudWatch.


## Próximos Passos

Após concluir este tutorial, explore [Multi runtimes com A2A](https://github.com/awslabs/amazon-bedrock-agentcore-samples/tree/main/02-use-cases/A2A-multi-agent-incident-response)
