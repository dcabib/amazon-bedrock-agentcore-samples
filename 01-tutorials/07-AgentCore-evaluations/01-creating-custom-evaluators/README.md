# Criando Avaliadores

## Visão Geral
Neste tutorial, você aprenderá sobre as métricas integradas e personalizadas do AgentCore Evaluations.
Você aprenderá quando usar cada tipo e como criar avaliadores personalizados adaptados às suas necessidades específicas.

## O Que Você Vai Aprender
- Entender os avaliadores integrados e seus casos de uso
- Criar avaliadores personalizados para requisitos especializados
- Selecionar a abordagem de avaliação correta para seus agentes

## Tipos de Avaliadores

### Avaliadores Integrados
Os avaliadores integrados são avaliadores pré-configurados que usam Modelos de Linguagem de Grande Escala (LLMs) como juízes para avaliar o desempenho do agente.

**Características Principais:**
- **Pré-configurados**: Vêm com templates de prompt cuidadosamente elaborados, modelos avaliadores selecionados e critérios de pontuação padronizados
- **Prontos para uso**: Nenhuma configuração adicional necessária — comece a avaliar imediatamente
- **Consistentes**: Configurações fixas garantem confiabilidade e consistência nas avaliações
- **Abrangentes**: Cobrem 13 dimensões críticas de avaliação incluindo correção, utilidade e segurança

**Quando Usar Avaliadores Integrados:**
- Você precisa implementar avaliações de qualidade rapidamente
- Você quer métricas de avaliação padronizadas entre equipes ou projetos
- Suas necessidades de avaliação se alinham com dimensões comuns de qualidade
- Você prioriza consistência e confiabilidade sobre personalização


Os seguintes avaliadores integrados estão disponíveis para seus casos de uso:
* Métricas de qualidade de resposta:
  * **Builtin.Correctness**: Avalia se as informações na resposta do agente são factualmente precisas
  * **Builtin.Faithfulness**: Avalia se as informações na resposta são suportadas pelo contexto/fontes fornecidos
  * **Builtin.Helpfulness**: Avalia, da perspectiva do usuário, quão útil e valiosa é a resposta do agente
  * **Builtin.ResponseRelevance**: Avalia se a resposta aborda adequadamente a consulta do usuário
  * **Builtin.Conciseness**: Avalia se a resposta é adequadamente breve sem perder informações-chave
  * **Builtin.Coherence**: Avalia se a resposta é logicamente estruturada e coerente
  * **Builtin.InstructionFollowing**: Mede quão bem o agente segue as instruções do sistema fornecidas
  * **Builtin.Refusal**: Detecta quando o agente evita perguntas ou se recusa diretamente a responder
* Métricas de conclusão de tarefas:
  * **Builtin.GoalSuccessRate**: Avalia se a conversa atende com sucesso aos objetivos do usuário
* Métricas de nível de ferramenta:
  * **Builtin.ToolSelectionAccuracy**: Avalia se o agente selecionou a ferramenta apropriada para a tarefa
  * **Builtin.ToolParameterAccuracy**: Avalia quão precisamente o agente extrai parâmetros das consultas do usuário
* Métricas de segurança:
  * **Builtin.Harmfulness**: Avalia se a resposta contém conteúdo prejudicial
  * **Builtin.Stereotyping**: Detecta conteúdo que faz generalizações sobre indivíduos ou grupos

**Nota:** As configurações dos avaliadores integrados não podem ser modificadas para manter a consistência e confiabilidade da avaliação entre todos os usuários, mas você pode criar seu próprio avaliador usando um integrado como base.

### Avaliadores Personalizados
Os avaliadores personalizados fornecem máxima flexibilidade, permitindo que você defina cada aspecto do seu processo de avaliação enquanto utiliza LLMs como juízes subjacentes.

**Opções de Personalização:**
- **Modelo avaliador**: Escolha o LLM que melhor se adapta às suas necessidades de avaliação
- **Prompts de avaliação**: Elabore instruções de avaliação específicas para seu caso de uso
- **Esquema de pontuação**: Projete sistemas de pontuação alinhados com as métricas da sua organização

**Quando Usar Avaliadores Personalizados:**
- Você está avaliando agentes de domínio específico (ex.: saúde, finanças, jurídico)
- Você tem padrões de qualidade únicos ou requisitos de conformidade
- Você precisa de sistemas de pontuação especializados alinhados com KPIs organizacionais
- Os avaliadores integrados não capturam suas dimensões específicas de avaliação

**Exemplos de Casos de Uso:**
- Agentes de saúde que requerem avaliação de conformidade HIPAA
- Agentes financeiros que precisam de pontuação de aderência regulatória
- Agentes de atendimento ao cliente avaliados contra padrões de qualidade específicos da marca
- Agentes de suporte técnico avaliados na metodologia de resolução de problemas

## Próximos Passos
Após completar este tutorial, prossiga para [Usando Avaliação Sob Demanda](../01-setting-evaluations) para aprender como aplicar esses avaliadores aos traces do seu agente.
