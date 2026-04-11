# Amazon Bedrock AgentCore Memory

## Visão Geral

A memória é um componente crítico da inteligência de um Agente. Modelos de Linguagem de Grande Escala (LLMs) não possuem memória persistente entre conversas. O Amazon Bedrock AgentCore Memory resolve isso fornecendo um serviço gerenciado que permite que agentes de IA mantenham contexto relevante entre sessões, entreguem experiências personalizadas e ajudem o agente a aprender ao longo do tempo.

## Capacidades Principais

- **Infraestrutura Central**: Configuração serverless com criptografia e observabilidade integradas
- **Armazenamento de Eventos**: Armazenamento bruto de eventos (histórico de conversas/checkpointing) com suporte a ramificação
- **Gerenciamento de Estratégias**: Estratégias de extração configuráveis (SEMANTIC, SUMMARY, USER_PREFERENCES, EPISODIC, SELF_MANAGED)
- **Extração de Registros de Memória**: Extração automática de fatos, preferências e resumos com base nas estratégias configuradas
- **Busca Semântica**: Recuperação baseada em vetores de memórias relevantes usando consultas em linguagem natural

## Como o AgentCore Memory Funciona

![fluxo_de_trabalho_alto_nível](./images/high_level_memory.png)

O AgentCore Memory opera em dois níveis:

### Memória de Curto Prazo

Contexto de conversa imediato e informações baseadas em sessão que fornecem continuidade dentro de uma única interação ou sessões intimamente relacionadas.

### Memória de Longo Prazo

Informações persistentes extraídas e armazenadas ao longo de múltiplas conversas, incluindo fatos, preferências e resumos que permitem experiências personalizadas ao longo do tempo.

## Arquitetura de Memória

1. **Armazenamento de Conversas**: Conversas completas são salvas em formato bruto para acesso imediato
2. **Processamento de Estratégias**: Estratégias configuradas analisam automaticamente as conversas em segundo plano
3. **Extração de Informações**: Dados importantes são extraídos com base nos tipos de estratégia (tipicamente leva ~1 minuto)
4. **Armazenamento Organizado**: Informações extraídas são armazenadas em namespaces estruturados para recuperação eficiente
5. **Recuperação Semântica**: Consultas em linguagem natural podem recuperar memórias relevantes usando similaridade vetorial

## Tipos de Estratégia de Memória

O AgentCore Memory suporta cinco tipos de estratégia:

- **Memória Semântica**: Armazena informações factuais usando embeddings vetoriais para busca por similaridade
- **Memória de Resumo**: Cria e mantém resumos de conversas para preservação de contexto
- **Memória de Preferências do Usuário**: Rastreia preferências e configurações específicas do usuário
- **Memória Episódica**: Captura sequências de interação significativas com detecção automática de episódios, consolidação e geração de reflexões
- **Memória Autogerenciada**: Permite customização da lógica de extração e consolidação

## Estrutura de Pastas

```
04-AgentCore-memory/
├── 01-short-term-memory/          # Memória baseada em sessão e gerenciamento de contexto
│   ├── 01-single-agent/
│   │   ├── with-strands-agent/    # Exemplos com Strands SDK + checkpointing
│   │   ├── with-langgraph-agent/  # Exemplos com LangGraph + checkpointing + human-in-the-loop
│   │   └── with-llamaindex-agent/ # Exemplos com LlamaIndex em múltiplos domínios
│   └── 02-multi-agent/
│       └── with-strands-agent/    # Planejamento de viagens multi-agente
├── 02-long-term-memory/           # Memória persistente entre conversas
│   ├── 01-single-agent/
│   │   ├── using-strands-agent-hooks/         # Integração com hooks de ciclo de vida do Strands
│   │   ├── using-strands-agent-memory-tool/   # Integração com ferramenta de memória do Strands
│   │   ├── using-langgraph-agent-hooks/       # Integração com hooks do LangGraph
│   │   └── using-llamaindex-agent-memory-tool/ # Integração com ferramenta de memória do LlamaIndex
│   └── 02-multi-agent/
│       └── with-strands-agent/    # Reserva de viagens + saúde multi-agente
├── 03-advanced-patterns/          # Integrações avançadas e ferramentas
│   ├── 01-guardrails-integration/ # Memória com Amazon Bedrock Guardrails
│   ├── 02-memory-runtime-integration/          # Memória + AgentCore Runtime
│   ├── 03-memory-identity-runtime-integration/ # Memória + Identidade + Runtime
│   ├── 04-memory-browser/         # Interface Web para navegação em armazenamentos de memória
│   └── 05-memory-streaming/       # Extração de registros de memória via streaming
├── 04-memory-branching/           # Ramificação de conversas e execução paralela
└── 05-memory-security-patterns/   # Políticas IAM e integração com identidades Cognito
    ├── 01-memory-iam-policies/
    └── 02-memory-iam-cognito-identities/
```

## Notebooks de Exemplo

### Memória de Curto Prazo

| Framework  | Tipo de Agente | Caso de Uso                              | Notebook                                                                                                                                                         |
| ---------- | -------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Strands    | Único          | Agente Pessoal                           | [personal-agent.ipynb](./01-short-term-memory/01-single-agent/with-strands-agent/personal-agent.ipynb)                                                           |
| Strands    | Único          | Agente Pessoal (Gerenciador de Memória)  | [personal-agent-memory-manager.ipynb](./01-short-term-memory/01-single-agent/with-strands-agent/personal-agent-memory-manager.ipynb)                             |
| LangGraph  | Único          | Coach de Fitness Pessoal                 | [personal-fitness-coach.ipynb](./01-short-term-memory/01-single-agent/with-langgraph-agent/personal-fitness-coach.ipynb)                                         |
| LangGraph  | Único          | Agente de Matemática com Checkpointing   | [math-agent-with-checkpointing.ipynb](./01-short-term-memory/01-single-agent/with-langgraph-agent/math-agent-with-checkpointing.ipynb)                           |
| LangGraph  | Único          | Agente de Suporte (Human-in-the-Loop)    | [support-agent-human-in-the-loop.ipynb](./01-short-term-memory/01-single-agent/with-langgraph-agent/support-agent-human-in-the-loop.ipynb)                       |
| LlamaIndex | Único          | Assistente de Pesquisa Acadêmica         | [academic-research-assistant.ipynb](./01-short-term-memory/01-single-agent/with-llamaindex-agent/academic-research-assistant-short-term-memory-tutorial.ipynb)   |
| LlamaIndex | Único          | Consultor de Portfólio de Investimentos  | [investment-portfolio-advisor.ipynb](./01-short-term-memory/01-single-agent/with-llamaindex-agent/investment-portfolio-advisor-short-term-memory-tutorial.ipynb) |
| LlamaIndex | Único          | Analisador de Documentos Jurídicos       | [legal-document-analyzer.ipynb](./01-short-term-memory/01-single-agent/with-llamaindex-agent/legal-document-analyzer-short-term-memory-tutorial.ipynb)           |
| LlamaIndex | Único          | Assistente de Conhecimento Médico        | [medical-knowledge-assistant.ipynb](./01-short-term-memory/01-single-agent/with-llamaindex-agent/medical-knowledge-assistant-short-term-memory-tutorial.ipynb)   |
| Strands    | Multi          | Agente de Planejamento de Viagens        | [travel-planning-agent.ipynb](./01-short-term-memory/02-multi-agent/with-strands-agent/travel-planning-agent.ipynb)                                              |
| Strands    | Multi          | Planejamento de Viagens (Gerenciador de Memória) | [travel-planning-agent-memory-manager.ipynb](./01-short-term-memory/02-multi-agent/with-strands-agent/travel-planning-agent-memory-manager.ipynb)                |

### Memória de Longo Prazo

| Framework  | Tipo de Agente | Integração        | Caso de Uso                                       | Notebook                                                                                                                                                                                                     |
| ---------- | -------------- | ----------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Strands    | Único          | Hooks             | Suporte ao Cliente (Estratégia Integrada)         | [customer-support-inbuilt-strategy.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/customer-support/customer-support-inbuilt-strategy.ipynb)                                          |
| Strands    | Único          | Hooks             | Suporte ao Cliente (Estratégia Personalizada)     | [customer-support-override-strategy.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/customer-support/customer-support-override-strategy.ipynb)                                        |
| Strands    | Único          | Hooks             | Assistente de Matemática                          | [math-assistant.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/simple-math-assistant/math-assistant.ipynb)                                                                           |
| Strands    | Único          | Hooks             | Notas de Reunião (Episódica)                      | [meeting-notes-assistant.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/meeting-notes-assistant-using-episodic/meeting-notes-assistant.ipynb)                                        |
| Strands    | Único          | Hooks             | Assistente Culinário (Autogerenciada)             | [agentcore_self_managed_memory_demo.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/culinary-assistant-self-managed-strategy/agentcore_self_managed_memory_demo.ipynb)                |
| Strands    | Único          | Hooks             | Assistente Culinário (Autogerenciada + Citações)  | [agentcore_self_managed_memory_demo.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-hooks/culinary-assistant-self-managed-strategy-with-citations/agentcore_self_managed_memory_demo.ipynb) |
| Strands    | Único          | Ferramenta de Memória | Assistente Culinário                          | [culinary-assistant.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-memory-tool/culinary-assistant.ipynb)                                                                                   |
| Strands    | Único          | Ferramenta de Memória | Assistente de Depuração (Episódica)           | [debugging_assistant_episodic_memory.ipynb](./02-long-term-memory/01-single-agent/using-strands-agent-memory-tool/debugging-agent/debugging_assistant_episodic_memory.ipynb)                                 |
| LangGraph  | Único          | Hooks             | Assistente de Nutrição (Preferências do Usuário)  | [nutrition-assistant-with-user-preference-saving.ipynb](./02-long-term-memory/01-single-agent/using-langgraph-agent-hooks/custom-user-preferences/nutrition-assistant-with-user-preference-saving.ipynb)     |
| LangGraph  | Único          | Hooks             | Assistente de Nutrição (Episódica)                | [nutrition-assistant-with-episodic-memory.ipynb](./02-long-term-memory/01-single-agent/using-langgraph-agent-hooks/episodic-memory/nutrition-assistant-with-episodic-memory.ipynb)                           |
| LlamaIndex | Único          | Ferramenta de Memória | Assistente de Pesquisa Acadêmica              | [academic-research-assistant.ipynb](./02-long-term-memory/01-single-agent/using-llamaindex-agent-memory-tool/academic-research-assistant-long-term-memory-tutorial.ipynb)                                    |
| LlamaIndex | Único          | Ferramenta de Memória | Consultor de Portfólio de Investimentos       | [investment-portfolio-advisor.ipynb](./02-long-term-memory/01-single-agent/using-llamaindex-agent-memory-tool/investment-portfolio-advisor-long-term-memory-tutorial.ipynb)                                  |
| LlamaIndex | Único          | Ferramenta de Memória | Analisador de Documentos Jurídicos            | [legal-document-analyzer.ipynb](./02-long-term-memory/01-single-agent/using-llamaindex-agent-memory-tool/legal-document-analyzer-long-term-memory-tutorial.ipynb)                                            |
| LlamaIndex | Único          | Ferramenta de Memória | Assistente de Conhecimento Médico             | [medical-knowledge-assistant.ipynb](./02-long-term-memory/01-single-agent/using-llamaindex-agent-memory-tool/medical-knowledge-assistant-long-term-memory-tutorial.ipynb)                                    |
| Strands    | Multi          | Hooks             | Assistente de Reserva de Viagens                  | [travel-booking-assistant.ipynb](./02-long-term-memory/02-multi-agent/with-strands-agent/travel-booking-agent/travel-booking-assistant.ipynb)                                                                |
| Strands    | Multi          | Hooks             | Assistente de Dados de Saúde (Episódica)          | [healthcare-data-assistant.ipynb](./02-long-term-memory/02-multi-agent/with-strands-agent/healthcare-assistant-using-episodic/healthcare-data-assistant.ipynb)                                               |

### Padrões Avançados

| Padrão                             | Descrição                                                  | Notebook                                                                                                                                             |
| ---------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Integração com Guardrails          | Combinar memória com Amazon Bedrock Guardrails             | [guardrails-memory.ipynb](./03-advanced-patterns/01-guardrails-integration/guardrails-memory.ipynb)                                                  |
| Memória + Runtime                  | Integrar memória com AgentCore Runtime                     | [runtime_memory_integration.ipynb](./03-advanced-patterns/02-memory-runtime-integration/runtime_memory_integration.ipynb)                            |
| Memória + Identidade + Runtime     | Integrar memória, resolução de identidade e runtime        | [runtime_memory_identity_integration.ipynb](./03-advanced-patterns/03-memory-identity-runtime-integration/runtime_memory_identity_integration.ipynb) |
| Navegador de Memória               | Interface Web para explorar e gerenciar armazenamentos de memória | [README](./03-advanced-patterns/04-memory-browser/README.md)                                                                                  |
| Streaming de Memória               | Transmitir resultados de extração de registros de memória  | [memory_record_streaming.ipynb](./03-advanced-patterns/05-memory-streaming/memory_record_streaming.ipynb)                                            |

### Ramificação de Memória

| Caso de Uso                                            | Notebook                                                                                                                                       |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Planejamento de Viagens com Ramificação de Memória     | [travel-planning-agent-with-memory-branching.ipynb](./04-memory-branching/travel-planning-agent-with-memory-branching.ipynb)                   |
| Execução Paralela Multi-Agente com Ramificação         | [multi-agent-parallel-execution-with-memory-branching.ipynb](./04-memory-branching/multi-agent-parallel-execution-with-memory-branching.ipynb) |

### Padrões de Segurança de Memória

| Padrão                                            | Notebook                                                                                                                                                                  |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Políticas IAM para Controle de Acesso à Memória   | [runtime_memory_identity_integration.ipynb](./05-memory-security-patterns/01-memory-iam-policies/runtime_memory_identity_integration.ipynb)                               |
| IAM + Identidades Federadas Cognito               | [runtime_memory_federated_identity_integration.ipynb](./05-memory-security-patterns/02-memory-iam-cognito-identities/runtime_memory_federated_identity_integration.ipynb) |

## Recursos

- [Documentação do Amazon Bedrock AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)
- [Vídeo de Aprofundamento](https://www.youtube.com/live/-N4v6-kJgwA)

## Pré-requisitos

- Python 3.10 ou superior
- Conta AWS com acesso ao Amazon Bedrock
- Ambiente Jupyter Notebook
- Pacotes Python necessários (consulte os arquivos `requirements.txt` individuais de cada exemplo)
