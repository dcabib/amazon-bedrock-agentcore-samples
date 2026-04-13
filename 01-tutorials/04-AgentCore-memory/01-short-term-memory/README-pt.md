# AgentCore Memory: Memória de Curto Prazo

## Visão Geral

A memória de curto prazo no Amazon Bedrock AgentCore fornece contexto de conversa imediato e gerenciamento de informações baseado em sessão. Ela permite que agentes de IA mantenham continuidade dentro de uma única interação ou sessões intimamente relacionadas, garantindo respostas coerentes e contextualmente conscientes ao longo de uma conversa.

## O que é Memória de Curto Prazo?

A memória de curto prazo foca em:

- **Continuidade de Sessão**: Manter o contexto dentro de uma única sessão de conversa
- **Contexto Imediato**: Preservar o histórico recente da conversa para respostas coerentes
- **Estado Temporário**: Gerenciar informações transitórias relevantes para a interação atual
- **Fluxo de Conversa**: Garantir transições suaves entre tópicos dentro de uma sessão

## Como a Memória de Curto Prazo Funciona no AgentCore

### Armazenamento de Eventos

O AgentCore Memory armazena eventos completos de conversa em formato bruto, fornecendo acesso imediato a:

- Últimas `k` mensagens do usuário e respostas do agente
- Metadados da conversa (timestamps, IDs de sessão, IDs de ator)
- Caminhos de conversa ramificados para interações complexas

### Gerenciamento de Sessão

A memória de curto prazo opera no nível da sessão:

- Cada sessão de conversa mantém seu próprio contexto
- Sessões relacionadas podem compartilhar contexto através de agrupamento de sessões
- Limpeza automática de dados de sessão expirados (baseado no TTL configurado)

### Acesso em Tempo Real

Diferente das estratégias de memória de longo prazo que processam em segundo plano, a memória de curto prazo fornece:

- Recuperação imediata do histórico recente da conversa
- Continuação da conversa quando uma sessão é descontinuada ou o agente falha
- Atualizações de contexto em tempo real conforme as conversas progridem
- Acesso de baixa latência a informações específicas da sessão

## Melhores Práticas

1. **Gerenciamento da Janela de Contexto**: Monitore o uso de contexto para evitar estouro
2. **Limites de Sessão**: Defina claramente quando as sessões começam e terminam
3. **Limpeza de Memória**: Implemente políticas de limpeza apropriadas para sessões expiradas
4. **Tratamento de Erros**: Trate falhas de recuperação de memória de forma elegante
5. **Otimização de Performance**: Use padrões de consulta eficientes (ex.: via Estratégia de Resumo em longo prazo) para grandes históricos de conversa

## Integração com Frameworks

A memória de curto prazo se integra perfeitamente com frameworks agênticos populares:

- **Strands Agent**: Integração nativa com hooks de conversa
- **LangGraph**: Integração com gerenciamento de estado
- **Frameworks Customizados**: Acesso direto à API para implementação flexível

## Notebooks de Exemplo Disponíveis

Explore estes exemplos práticos para aprender a implementação de memória de curto prazo:

| Framework     | Caso de Uso              | Descrição                                                                                              | Notebook                                                                                                                   | Arquitetura                                                            |
| ------------- | ------------------------ | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Strands Agent | Agente Pessoal           | Assistente de IA que mantém contexto de conversa e lembra interações do usuário dentro de uma sessão   | [personal-agent.ipynb](./01-single-agent/with-strands-agent/personal-agent-pt.ipynb)                                          | [Ver](./01-single-agent/with-strands-agent/architecture.png)           |
| LangGraph     | Coach de Fitness         | Coach de fitness pessoal que acompanha progresso de treino e mantém contexto ao longo das sessões      | [personal-fitness-coach.ipynb](./01-single-agent/with-langgraph-agent/personal-fitness-coach-pt.ipynb)                        | [Ver](./01-single-agent/with-langgraph-agent/images/architecture.png)  |
| LangGraph     | Agente de Suporte        | Agente de suporte ao cliente com capacidades human-in-the-loop para resolução de problemas complexos   | [support-agent-human-in-the-loop.ipynb](./01-single-agent/with-langgraph-agent/support-agent-human-in-the-loop-pt.ipynb)      | [Ver](./01-single-agent/with-langgraph-agent/images/architecture.png)  |
| LangGraph     | Agente de Matemática     | Agente de resolução de problemas matemáticos com persistência multi-etapa para cálculos complexos      | [math-agent-with-multi-step-persistence.ipynb](./01-single-agent/with-langgraph-agent/math-agent-with-checkpointing-pt.ipynb) | [Ver](./01-single-agent/with-langgraph-agent/images/architecture.png)  |
| Strands Agent | Planejamento de Viagens  | Agentes colaborativos que compartilham contexto ao planejar itinerários de viagem complexos             | [travel-planning-agent.ipynb](./02-multi-agent/with-strands-agent/travel-planning-agent-pt.ipynb)                             | [Ver](./02-multi-agent/with-strands-agent/architecture.png)            |

## Primeiros Passos

1. Escolha um exemplo que corresponda ao seu caso de uso
2. Navegue até a pasta do exemplo
3. Instale os requisitos: `pip install -r requirements.txt`
4. Abra o Jupyter notebook e siga a implementação passo a passo

## Próximos Passos

Quando estiver confortável com a memória de curto prazo, explore a [Memória de Longo Prazo](../02-long-term-memory/) para aprender sobre estratégias de memória persistente que funcionam em múltiplas conversas e sessões.
