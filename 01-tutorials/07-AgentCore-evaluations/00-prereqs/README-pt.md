# Pré-requisitos: Criando Agentes de Exemplo

## Visão Geral

Antes de podermos avaliar agentes, precisamos de um agente para avaliar. Este tutorial configura dois agentes de exemplo que usaremos ao longo dos tutoriais de avaliação restantes: um usando o [Strands Agents SDK](https://strandsagents.com/) e o outro usando [LangGraph](https://www.langchain.com/langgraph).

## Os Agentes
Os agentes criados são essencialmente os mesmos, apenas usando dois frameworks diferentes para demonstrar a proposta "qualquer framework" do AgentCore.

Os agentes criados possuem duas capacidades principais:

**Execução de Código**
- Usa o AgentCore Code Interpreter para executar código Python
- Lida com cálculos matemáticos e análise de dados

**Memória**
- Armazena fatos e preferências do usuário
- Recupera contexto relevante para respostas personalizadas

Ambos os agentes usam o Anthropic Claude Haiku 4.5 do Amazon Bedrock como modelo LLM, mas com o AgentCore você pode usar qualquer modelo de sua preferência.

A arquitetura é a seguinte:

![Arquitetura](../images/agent_architecture.png)

## Pré-requisitos
Antes de implantar o agente, você precisa de:
* Python 3.10+
* Acesso à AWS

## Próximos Passos
Agora que você tem todos os pré-requisitos necessários, vamos percorrer os tutoriais individuais de avaliação:

- **[Tutorial 01](../01-creating-custom-evaluators)**: Criar avaliadores personalizados
- **[Tutorial 02](../02-running-evaluations)**: Executar avaliações sob demanda e online
- **[Tutorial 03](../03-advanced)**: Técnicas avançadas e dashboards
