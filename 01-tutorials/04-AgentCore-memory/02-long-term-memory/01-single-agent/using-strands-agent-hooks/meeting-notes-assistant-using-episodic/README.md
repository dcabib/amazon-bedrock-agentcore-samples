# AgentCore Memory: Estratégia de Memória Episódica

| Informação          | Detalhes                                                     |
|:--------------------|:-------------------------------------------------------------|
| Tipo de tutorial    | Longo prazo Episódica                                        |
| Tipo de agente      | Assistente de Notas de Reunião                               |
| Framework Agêntico  | Strands Agents                                               |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                   |
| Componentes do tutorial | Memória Episódica AgentCore com Reflexões, Hooks         |
| Complexidade do exemplo | Intermediária                                            |

## Visão Geral

A memória episódica captura fatias significativas de interações entre usuário e sistema para que aplicações possam recuperar contexto de uma forma focada e relevante. Em vez de armazenar cada evento bruto, ela identifica momentos importantes, os resume em registros compactos e os organiza para que o sistema possa recuperar o que importa sem ruído.

**Reflexões** se baseiam em registros episódicos analisando episódios passados para revelar insights, padrões e conclusões de nível superior. Elas transformam experiência bruta em orientação que a aplicação pode usar imediatamente.

## O que é Memória Episódica?

A memória episódica fornece:

- **Detecção de Episódios**: Identifica automaticamente quando sequências de interação significativas são completadas
- **Captura Estruturada**: Registra situação, intenção, avaliação, justificativa e reflexão no nível do episódio
- **Aprendizado Entre Episódios**: Gera reflexões que identificam padrões em múltiplos episódios
- **Recuperação Contextual**: Permite que agentes aprendam com experiências passadas e evitem repetir erros

## Como a Memória Episódica Difere de Outras Estratégias

| Estratégia | Foco | Melhor Para |
|------------|------|-------------|
| **Semântica** | Fatos e conhecimento | Recuperação de informações estáticas |
| **Preferência do Usuário** | Configurações e preferências do usuário | Personalização |
| **Resumo** | Condensação de conversas | Contexto de conversas longas |
| **Episódica** | Sequências de interação + reflexões | Aprender com a experiência |

A memória episódica é única porque:
1. Captura a **sequência** de ações, não apenas fatos
2. Gera **reflexões** que identificam padrões entre episódios
3. Ajuda agentes a entender **por que** certas abordagens funcionaram ou falharam

## Quando Usar Memória Episódica

Casos de uso ideais incluem:

- **Assistentes de reunião**: Rastrear decisões, itens de ação e acompanhamentos entre reuniões
- **Conversas de suporte ao cliente**: Aprender com padrões de resolução bem-sucedidos
- **Fluxos de trabalho orientados por agentes**: Lembrar quais combinações de ferramentas funcionam melhor
- **Ferramentas de produtividade pessoal**: Adaptar-se aos padrões de trabalho do usuário ao longo do tempo
- **Gerenciamento de projetos**: Identificar bloqueios recorrentes e estratégias bem-sucedidas

## Etapas da Estratégia

A estratégia de memória episódica inclui três etapas:

1. **Extração**: Analisa o episódio em andamento e determina se está completo
2. **Consolidação**: Combina extrações em um único episódio quando completo
3. **Reflexão**: Gera insights em múltiplos episódios

## Organização de Namespaces

Episódios e reflexões são armazenados em namespaces configuráveis:

```python
# Armazenar episódios no nível do ator (recomendado para a maioria dos casos de uso)
"namespaces": ["meetings/actor/{actorId}/episodes"]

# Reflexões devem ser o mesmo ou prefixo do namespace episódico
"reflectionConfiguration": {
    "namespaces": ["meetings/actor/{actorId}"]  # Prefixo do namespace de episódios
}
```

**Importante**: O namespace de reflexão deve ser o mesmo ou um prefixo do namespace episódico. Por exemplo, se os episódios estão em `meetings/actor/{actorId}/episodes`, as reflexões devem estar em `meetings/actor/{actorId}` (prefixo).

## Arquitetura

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Assistente de Notas de Reunião                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────────┐     ┌──────────────────────────────────────────────────┐  │
│  │  Participante│     │              Strands Agent                        │  │
│  │  da Reunião  │────▶│  ┌─────────────────────────────────────────────┐  │  │
│  │              │     │  │           Prompt do Sistema                 │  │  │
│  │  "Vamos      │     │  │  "Você é um assistente de reunião que      │  │  │
│  │  discutir    │     │  │   rastreia decisões e itens de ação..."    │  │  │
│  │  metas do T3"│     │  └─────────────────────────────────────────────┘  │  │
│  └──────────────┘     │                      │                            │  │
│                       │                      ▼                            │  │
│                       │  ┌─────────────────────────────────────────────┐  │  │
│                       │  │         EpisodicMemoryHooks                 │  │  │
│                       │  │  ┌───────────────┐  ┌───────────────────┐   │  │  │
│                       │  │  │ MessageAdded  │  │ AfterInvocation   │   │  │  │
│                       │  │  │    Hook       │  │      Hook         │   │  │  │
│                       │  │  │ (recuperar)   │  │ (salvar eventos)  │   │  │  │
│                       │  │  └───────┬───────┘  └─────────┬─────────┘   │  │  │
│                       │  └──────────┼────────────────────┼─────────────┘  │  │
│                       │             │                    │                │  │
│                       │  ┌──────────┴────────────────────┴─────────────┐  │  │
│                       │  │              Ferramentas                    │  │  │
│                       │  │  capture_action | identify_decision |       │  │  │
│                       │  │  summarize_discussion | track_followup      │  │  │
│                       │  └─────────────────────────────────────────────┘  │  │
│                       └──────────────────────────────────────────────────┘  │
│                                          │                                   │
│                                          ▼                                   │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                    Serviço AgentCore Memory                            │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │                   Estratégia Episódica                          │  │  │
│  │  │                                                                  │  │  │
│  │  │   ┌──────────────┐   ┌───────────────┐   ┌─────────────────┐   │  │  │
│  │  │   │  Extração    │──▶│ Consolidação  │──▶│   Reflexão      │   │  │  │
│  │  │   │              │   │               │   │                 │   │  │  │
│  │  │   │ Detectar     │   │ Combinar em   │   │ Gerar insights  │   │  │  │
│  │  │   │ fim reunião  │   │ registro único│   │ entre reuniões  │   │  │  │
│  │  │   └──────────────┘   └───────────────┘   └─────────────────┘   │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                        │  │
│  │  ┌─────────────────────────────┐  ┌─────────────────────────────────┐ │  │
│  │  │        Episódios            │  │         Reflexões               │ │  │
│  │  │ /meetings/actor/{id}/episodes│  │/meetings/actor/{id}/reflections │ │  │
│  │  │                             │  │                                 │ │  │
│  │  │  • Propósito da reunião     │  │  • Padrões eficazes de reunião  │ │  │
│  │  │  • Decisões tomadas         │  │  • Taxa de conclusão de itens   │ │  │
│  │  │  • Itens de ação atribuídos │  │  • Preferências dos participantes│ │  │
│  │  │  • Status de acompanhamento │  │  • Bloqueios comuns             │ │  │
│  │  └─────────────────────────────┘  └─────────────────────────────────┘ │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

Fluxo de Dados:
1. Participante da reunião discute tópicos
2. Hook MessageAdded recupera episódios e reflexões relevantes de reuniões passadas
3. Agente processa a discussão com contexto histórico
4. Agente usa ferramentas (capture_action, identify_decision, summarize_discussion, track_followup)
5. Hook AfterInvocation salva a interação como evento
6. AgentCore extrai episódios quando a reunião é completada (~1 min)
7. Reflexões são geradas em múltiplas reuniões (em segundo plano)
```

## Notebooks de Exemplo Disponíveis

| Framework | Caso de Uso | Descrição | Notebook |
|-----------|-------------|-----------|----------|
| Strands Agent | Notas de Reunião | Assistente de reunião que rastreia decisões, itens de ação e aprende com reuniões passadas | [meeting-notes-assistant.ipynb](./meeting-notes-assistant.ipynb) |

## Primeiros Passos

1. Navegue até esta pasta
2. Instale os requisitos: `pip install -r requirements.txt`
3. Abra o Jupyter notebook e siga a implementação passo a passo

## Prompts de Exemplo

Experimente estes cenários de reunião para testar o aprendizado da memória episódica:

### 1. Acompanhamento de Decisão Anterior
**Prompt**: "Vamos revisitar o orçamento de marketing do T3 que discutimos na semana passada"

**Comportamento Esperado**: O agente recupera o episódio passado com a discussão do orçamento, recupera decisões anteriores e referencia o contexto daquela reunião.

### 2. Verificação de Item de Ação
**Prompt**: "Nós designamos alguém para cuidar do redesign do site?"

**Comportamento Esperado**: O agente recupera episódios passados onde o redesign do site foi discutido, identifica itens de ação atribuídos e responsável.

### 3. Padrão de Reunião Recorrente
**Prompt**: "Precisamos planejar a reunião de revisão do sprint semanal"

**Comportamento Esperado**: O agente aplica padrões aprendidos de revisões de sprint passadas (ex.: "A equipe prefere formato de 30 min" ou "Sempre incluir tempo para demo").

### 4. Nova Reunião com Contexto
**Prompt**: "Vamos ter um alinhamento rápido sobre o cronograma de lançamento do produto. Precisamos finalizar as datas."

**Comportamento Esperado**: Facilitação de reunião multi-etapa usando ferramentas para capturar decisões, identificar itens de ação e rastrear acompanhamentos.

### 5. Reconhecimento de Preferência do Participante
**Prompt**: "Sarah quer discutir a arquitetura técnica para o novo recurso"

**Comportamento Esperado**: O agente reconhece as preferências de Sarah de reuniões passadas (ex.: "Sarah prefere diagramas detalhados" ou "Reuniões técnicas com Sarah tipicamente precisam de 1 hora").

### 6. Novo Tópico
**Prompt**: "Precisamos discutir a iniciativa de sustentabilidade da empresa pela primeira vez"

**Comportamento Esperado**: O agente reconhece que este é um tópico novo sem episódios passados, fornece estrutura geral de reunião, captura decisões e itens de ação para referência futura.

## Conceitos Principais

### Episódios vs Reflexões

**Episódios** capturam sequências de interação individuais:
- Uma reunião de planejamento de projeto onde decisões foram tomadas
- Uma retrospectiva de sprint com itens de ação atribuídos
- Uma discussão de revisão de orçamento com resultados específicos

**Reflexões** analisam padrões entre episódios:
- Quais formatos de reunião funcionam melhor para diferentes equipes
- Bloqueios comuns que surgem repetidamente
- Taxas de conclusão de itens de ação por membro da equipe
- Preferências de comunicação dos participantes

### Melhores Práticas de Recuperação

1. **Consultar por intenção**: Episódios são indexados por "intenção", reflexões por "caso de uso"
2. **Incluir resultados de ferramentas**: Ao criar eventos, inclua resultados `TOOL` para extração ideal
3. **Usar reflexões proativamente**: Consulte reflexões no início da tarefa para evitar armadilhas conhecidas
4. **Linearizar episódios bem-sucedidos**: Alimente turnos de episódios bem-sucedidos para focar o agente

## Próximos Passos

Após dominar a memória episódica:
- Combine com memória semântica para experiências abrangentes de agentes
- Implemente compartilhamento de reflexões entre agentes para aprendizado em equipe
- Construa loops de feedback para melhorar a detecção de episódios

## Resolução de Problemas

### Episódios Não Aparecem
**Problema**: Nenhum episódio encontrado após executar os testes

**Solução**: A extração de episódios leva aproximadamente 1 minuto após uma conversa ser completada. Aguarde e tente a recuperação novamente. Os episódios são extraídos de forma assíncrona em segundo plano.

### Erros de Permissão
**Problema**: `AccessDeniedException` ao criar memória ou salvar eventos

**Solução**: Certifique-se de que suas credenciais AWS possuem as permissões necessárias:
- Política: `BedrockAgentCoreFullAccess` (política gerenciada)
- Ou política customizada com permissões `bedrock-agentcore:*`

### Erros de Acesso ao Modelo
**Problema**: Não é possível acessar o modelo Claude Haiku 4.5

**Solução**: Habilite o acesso ao modelo no console do AWS Bedrock:
1. Navegue até Console AWS → Bedrock → Acesso ao modelo
2. Solicite acesso para "Anthropic Claude Haiku 4.5"
3. Aguarde aprovação (geralmente instantânea para modelos padrão)

### Resultados de Reflexão Vazios
**Problema**: O namespace de reflexões não retorna resultados

**Solução**: Reflexões são geradas após múltiplos episódios serem coletados. Execute sessões adicionais de reunião com cenários variados para acumular episódios. A geração de reflexões acontece em segundo plano e pode levar vários minutos.

### Criação de Memória Falha com "Já Existe"
**Problema**: Recurso de memória com o mesmo nome já existe

**Solução**: O código trata isso automaticamente reutilizando a memória existente. Se quiser começar do zero, delete a memória antiga primeiro usando `client.delete_memory_and_wait(memory_id=memory_id)`.

## Limpeza

Após completar o tutorial, delete o recurso de memória para evitar cobranças contínuas:

```python
try:
    client.delete_memory_and_wait(memory_id=memory_id)
    print(f"✅ Recurso de memória deletado: {memory_id}")
except Exception as e:
    print(f"❌ Erro ao deletar memória: {e}")
```

**Nota**: Isso deleta permanentemente todos os episódios e reflexões armazenados para este recurso de memória. Certifique-se de exportar quaisquer dados que deseje manter antes da exclusão.

**Considerações de Custo**: O preço do AgentCore Memory é baseado em armazenamento e recuperação. A limpeza regular de recursos de memória de desenvolvimento/teste ajuda a controlar custos.
