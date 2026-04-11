# AgentCore Memory: Estratégias de Memória de Longo Prazo

## Visão Geral

A memória de longo prazo no Amazon Bedrock AgentCore permite que agentes de IA mantenham informações persistentes em múltiplas conversas e sessões. Diferente da memória de curto prazo que foca no contexto imediato, a memória de longo prazo extrai, processa e armazena informações significativas que podem ser recuperadas e aplicadas em interações futuras, criando experiências de agentes verdadeiramente personalizadas e inteligentes.

## O que é Memória de Longo Prazo?

A memória de longo prazo fornece:

- **Persistência Entre Sessões**: Informações que sobrevivem além de conversas individuais
- **Extração Inteligente**: Identificação e armazenamento automáticos de fatos, preferências e padrões importantes
- **Compreensão Semântica**: Armazenamento baseado em vetores que permite recuperação por linguagem natural
- **Personalização**: Informações específicas do usuário que permitem experiências sob medida
- **Acúmulo de Conhecimento**: Aprendizado contínuo e construção de informações ao longo do tempo

## Como as Estratégias de Memória de Longo Prazo Funcionam

A memória de longo prazo opera através de **Estratégias de Memória** que definem quais informações extrair e como processá-las. O sistema funciona automaticamente em segundo plano:

### Pipeline de Processamento

1. **Análise de Conversa**: Conversas salvas são analisadas com base nas estratégias configuradas
2. **Extração de Informações**: Dados importantes (fatos, preferências, resumos) são extraídos usando modelos de IA
3. **Armazenamento Estruturado**: Informações extraídas são organizadas em namespaces para recuperação eficiente
4. **Indexação Semântica**: Informações são vetorizadas para capacidades de busca por linguagem natural
5. **Consolidação**: Informações similares são mescladas e refinadas ao longo do tempo

**Tempo de Processamento**: Tipicamente leva ~1 minuto após as conversas serem salvas, sem necessidade de código adicional.

### Nos Bastidores

- **Extração com IA**: Usa modelos de base para entender e extrair informações relevantes
- **Embeddings Vetoriais**: Cria representações semânticas para recuperação baseada em similaridade
- **Organização por Namespace**: Estrutura informações usando hierarquias configuráveis do tipo caminho
- **Consolidação Automática**: Mescla e refina informações similares para evitar duplicação
- **Aprendizado Incremental**: Melhora continuamente a qualidade da extração com base em padrões de conversa

## Tipos de Estratégia de Memória de Longo Prazo

O AgentCore Memory suporta quatro tipos distintos de estratégia para armazenamento de informações de longo prazo:

### 1. Estratégia de Memória Semântica

Armazena informações factuais extraídas de conversas usando embeddings vetoriais para busca por similaridade.

```python
{
    "semanticMemoryStrategy": {
        "name": "FactExtractor",
        "description": "Extrai e armazena informações factuais",
        "namespaces": ["support/user/{actorId}/facts/"]
    }
}
```

**Melhor para**: Armazenar informações de produtos, detalhes técnicos ou quaisquer dados factuais que precisem ser recuperados através de consultas em linguagem natural.

### 2. Estratégia de Memória de Resumo

Cria e mantém resumos de conversas para preservar contexto em interações longas.

```python
{
    "summaryMemoryStrategy": {
        "name": "ConversationSummary",
        "description": "Mantém resumos de conversas",
        "namespaces": ["support/summaries/{sessionId}/"]
    }
}
```

**Melhor para**: Fornecer contexto em conversas de acompanhamento e manter continuidade em interações longas.

### 3. Estratégia de Memória de Preferências do Usuário

Rastreia preferências e configurações específicas do usuário para personalizar interações.

```python
{
    "userPreferenceMemoryStrategy": {
        "name": "UserPreferences",
        "description": "Captura preferências e configurações do usuário",
        "namespaces": ["support/user/{actorId}/preferences"/]
    }
}
```

**Melhor para**: Armazenar preferências de comunicação, preferências de produtos ou quaisquer configurações específicas do usuário.

### 4. Estratégia de Memória Customizada

Permite customização de prompts para extração e consolidação, fornecendo flexibilidade para casos de uso especializados.

```python
{
    "customMemoryStrategy": {
        "name": "CustomExtractor",
        "description": "Lógica de extração de memória customizada",
        "namespaces": ["user/custom/{actorId}/"],
        "configuration": {
            "semanticOverride": { # Você também pode sobrescrever Summary ou User Preferences.
                "extraction": {
                    "appendToPrompt": "Extrair informações específicas baseadas em critérios customizados",
                    "modelId": "global.anthropic.claude-haiku-4-5-20251001-v1:0",
                },
                "consolidation": {
                    "appendToPrompt": "Consolidar informações extraídas em um formato específico",
                    "modelId": "global.anthropic.claude-haiku-4-5-20251001-v1:0",
                }
            }
        }
    }
}
```

**Melhor para**: Necessidades de extração especializadas que não se encaixam nas estratégias padrão.

## Entendendo Namespaces

Namespaces organizam registros de memória dentro de estratégias usando uma estrutura do tipo caminho. Eles podem incluir variáveis que são dinamicamente substituídas:

- `support/facts/{sessionId}`: Organiza fatos por sessão
- `user/{actorId}/preferences`: Armazena preferências do usuário por ID de ator
- `meetings/{memoryId}/summaries/{sessionId}`: Agrupa resumos por memória

As variáveis `{actorId}`, `{sessionId}` e `{memoryId}` são automaticamente substituídas por valores reais ao armazenar e recuperar memórias.

## Exemplo: Como Funciona na Prática

Digamos que um usuário diga ao seu agente de suporte ao cliente: _"Sou vegetariano e gosto muito de culinária italiana. Por favor, não me ligue depois das 18h."_

Depois que você salvar essa conversa, as estratégias configuradas automaticamente:

**Estratégia Semântica** extrai:

- "Usuário é vegetariano"
- "Usuário gosta de culinária italiana"

**Estratégia de Preferências do Usuário** captura:

- "Preferência alimentar: vegetariano"
- "Preferência culinária: italiana"
- "Preferência de contato: sem ligações após 18h"

**Estratégia de Resumo** cria:

- "Usuário discutiu restrições alimentares e preferências de contato"

Tudo isso acontece automaticamente em segundo plano - você só precisa salvar a conversa e as estratégias cuidam do resto.

## Notebooks de Exemplo Disponíveis

Explore estes exemplos práticos para aprender a implementação de estratégias de memória de longo prazo:

| Método de Integração              | Caso de Uso              | Descrição                                                                                               | Notebook                                                                                                       | Arquitetura                                                                                |
| --------------------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Hooks do Strands Agent            | Suporte ao Cliente       | Sistema de suporte completo com estratégias de memória semântica e de preferências                      | [customer-support.ipynb](./01-single-agent/using-strands-agent-hooks/customer-support/customer-support.ipynb)  | [Ver](./01-single-agent/using-strands-agent-hooks/customer-support/architecture.png)       |
| Hooks do Strands Agent            | Assistente de Matemática | Assistente tutor de matemática que lembra preferências de aprendizagem e progresso do usuário           | [math-assistant.ipynb](./01-single-agent/using-strands-agent-hooks/simple-math-assistant/math-assistant.ipynb) | [Ver](./01-single-agent/using-strands-agent-hooks/simple-math-assistant/architecture.png)  |
| Hooks do LangGraph Agent          | Assistente de Nutrição   | Consultor de nutrição que salva preferências alimentares e metas de saúde do usuário para recomendações personalizadas | [nutrition-assistant-with-user-preference-saving.ipynb](./01-single-agent/using-langgraph-agent-hooks/nutrition-assistant-with-user-preference-saving.ipynb) | [Ver](./01-single-agent/using-langgraph-agent-hooks/architecture.png) |
| Ferramenta de Memória do Strands Agent | Assistente Culinário | Agente de recomendação de alimentos que aprende preferências alimentares e estilos de culinária         | [culinary-assistant.ipynb](./01-single-agent/using-strands-agent-memory-tool/culinary-assistant.ipynb)         | [Ver](./01-single-agent/using-strands-agent-memory-tool/architecture.png)                  |
| Multi-Agente                      | Colaboração de Agentes   | Assistente de Viagens com múltiplos agentes compartilhando e utilizando estratégias de memória de longo prazo | [travel-booking-assistant.ipynb](./02-multi-agent/with-strands-agent/travel-booking-assistant.ipynb)      | [Ver](./02-multi-agent/with-strands-agent/architecture.png)                                |

## Primeiros Passos

1. Escolha um exemplo que corresponda ao seu caso de uso
2. Navegue até a pasta do exemplo
3. Instale os requisitos: `pip install -r requirements.txt`
4. Abra o Jupyter notebook e siga a implementação passo a passo

## Melhores Práticas

1. **Seleção de Estratégia**: Escolha estratégias apropriadas com base nos requisitos do seu caso de uso
2. **Design de Namespace**: Planeje hierarquias de namespace para organização eficiente de informações
3. **Ajuste de Extração**: Customize prompts de extração para informações específicas do domínio
4. **Monitoramento de Performance**: Acompanhe a qualidade da extração de memória e a performance de recuperação
5. **Considerações de Privacidade**: Implemente políticas apropriadas de retenção de dados e privacidade

## Próximos Passos

Após dominar as estratégias de memória de longo prazo, explore:

- Combinar memória de curto e longo prazo para experiências abrangentes de agentes
- Configurações avançadas de estratégias customizadas
- Padrões de compartilhamento de memória multi-agente
- Considerações para implantação em produção
