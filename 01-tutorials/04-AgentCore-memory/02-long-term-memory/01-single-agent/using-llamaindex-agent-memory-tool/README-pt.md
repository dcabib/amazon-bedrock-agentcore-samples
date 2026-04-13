# LlamaIndex com Integração AWS Bedrock AgentCore Memory

Este projeto apresenta agentes de IA de nível empresarial com capacidades de memória persistente, demonstrando como o framework ReAct do LlamaIndex se integra perfeitamente com o AWS Bedrock AgentCore Memory para criar sistemas inteligentes que aprendem, se adaptam e evoluem ao longo do tempo. Diferente de agentes tradicionais sem estado, estas implementações mantêm consciência contextual entre sessões, permitindo análise longitudinal sofisticada, capacidades de referência cruzada e construção cumulativa de conhecimento que transformam como agentes de IA operam em ambientes profissionais.

## 🚀 Funcionalidades Principais

- **Integração Nativa com LlamaIndex**: Passagem direta de memória com `agent.run(message, memory=agentcore_memory)`
- **Exemplos Específicos por Domínio**: Pesquisa Acadêmica, Análise de Documentos Jurídicos, Conhecimento Médico, Gestão de Portfólio de Investimentos
- **Testes Abrangentes**: 8-10 casos de teste sistemáticos por exemplo com validação esperada
- **Memória de Curto e Longo Prazo**: Cobertura completa de ambos os tipos de memória
- **Pronto para Empresas**: APIs simples e explícitas adequadas para ambientes de produção

## 📁 Estrutura do Projeto

```
├── 01-short-term-memory/
│   ├── academic-research-assistant-short-term-memory-tutorial.ipynb
│   ├── legal-document-analyzer-short-term-memory-tutorial.ipynb
│   ├── medical-knowledge-assistant-short-term-memory-tutorial.ipynb
│   └── investment-portfolio-advisor-short-term-memory-tutorial.ipynb
├── 02-long-term-memory/
│   ├── academic-research-assistant-long-term-memory-tutorial.ipynb
│   ├── legal-document-analyzer-long-term-memory-tutorial.ipynb
│   ├── medical-knowledge-assistant-long-term-memory-tutorial.ipynb
│   └── investment-portfolio-advisor-long-term-memory-tutorial.ipynb
└── requirements.txt
```

## 🎯 Casos de Uso

### Assistente de Pesquisa Acadêmica
- **Curto prazo**: Análise de artigos, síntese de pesquisa dentro de uma única sessão
- **Longo prazo**: Evolução de pesquisa entre sessões, suporte a propostas de financiamento ao longo de meses
- **Inteligência de Memória**: Rastreia temas de pesquisa, redes de citação e evolução de metodologia
- **Testes**: 8 testes abrangentes incluindo raciocínio contextual e validação de referência cruzada

### Analisador de Documentos Jurídicos
- **Curto prazo**: Análise de contratos, avaliação de riscos, verificação de conformidade
- **Longo prazo**: Rastreamento de precedentes multi-caso, acúmulo de conhecimento jurídico (retenção de 12 meses)
- **Inteligência de Memória**: Constrói banco de dados de jurisprudência, rastreia mudanças regulatórias, mantém histórico de clientes
- **Testes**: 9 testes sistemáticos incluindo aplicação de precedentes e conformidade regulatória

### Assistente de Conhecimento Médico
- **Curto prazo**: Consulta de pacientes, interações medicamentosas, diretrizes clínicas
- **Longo prazo**: Cuidado longitudinal de pacientes, resultados de tratamentos, tendências de saúde populacional
- **Inteligência de Memória**: Mantém históricos de pacientes, rastreia eficácia de tratamentos, aprende com resultados
- **Testes**: 10 testes abrangentes incluindo raciocínio clínico e planejamento de tratamento

### Consultor de Portfólio de Investimentos
- **Curto prazo**: Perfil do cliente, análise de portfólio, recomendações de investimento
- **Longo prazo**: Acompanhamento de desempenho multi-trimestral (T1→T2→T3→T4), inteligência de mercado, gestão de patrimônio
- **Inteligência de Memória**: Rastreia evolução de portfólio de $3,2M→$3,45M, decisões de timing de mercado, adaptação de teses
- **Testes**: 10 testes sistemáticos incluindo atribuição de desempenho trimestral e análise de jornada de investimento multi-ano

## 🏗️ Arquitetura do Sistema

*Diagrama de arquitetura será adicionado aqui*

## 🛠️ Pré-requisitos

- Python 3.10+
- Conta AWS com permissões do Bedrock AgentCore Memory
- AWS CLI configurado com credenciais apropriadas
- Acesso ao perfil de inferência Claude 3.7 Sonnet (`us.anthropic.claude-3-7-sonnet-20250219-v1:0`)

## 📦 Instalação

```bash
# Instalar todas as dependências incluindo Jupyter
pip install -r requirements.txt

# Alternativa: Instalar Jupyter separadamente
pip install jupyter ipykernel
```

## 🚀 Início Rápido

1. **Configure as credenciais AWS:**
   ```bash
   aws configure
   ```

2. **Escolha um tutorial e abra o notebook:**
   ```bash
   jupyter notebook 01-short-term-memory/academic-research-assistant-short-term-memory-tutorial.ipynb
   ```

3. **Siga o tutorial passo a passo** com testes abrangentes

## 🏗️ Benefícios Principais

- ✅ **Controle Explícito**: Parâmetro de memória direto vs automação oculta
- ✅ **Depuração Fácil**: Operações de memória visíveis vs hooks em segundo plano
- ✅ **API Simples**: `agent.run(message, memory=memory)` vs configuração complexa
- ✅ **Testes Abrangentes**: Validação sistemática com resultados esperados
- ✅ **Especialização por Domínio**: Casos de uso especializados vs exemplos genéricos

## 📊 Metodologia de Testes

Cada notebook inclui **8-10 testes sistemáticos** com validação clara:

### Categorias de Teste
- **Teste 1-2: Armazenamento de Memória** - Verificar persistência de informações e integração de ferramentas
- **Teste 3-4: Recall de Contexto** - Validar identidade, métricas e recuperação de informações detalhadas
- **Teste 5-6: Raciocínio e Síntese** - Testar capacidades de referência cruzada e síntese de conhecimento
- **Teste 7-8: Aplicação Prática** - Validação de cenários do mundo real (propostas de financiamento, análise de casos)
- **Teste 9-10: Limites de Sessão** - Verificação de isolamento de memória e comportamento entre sessões

### Abordagem de Validação
- **✅ Resultados Esperados**: Cada teste mostra saídas esperadas para comparação
- **🎯 Critérios de Sucesso**: Indicadores claros de aprovação/reprovação com métricas específicas
- **📊 Complexidade Progressiva**: Testes evoluem de recall básico a raciocínio avançado
- **🔍 Testes de Casos Limite**: Limites de sessão, limites de memória e tratamento de erros

### Exemplo de Padrão de Teste
```python
# Teste 4: Recall de Métricas Detalhadas
response = await agent.run("What were the exact accuracy percentages?", memory=memory)
print("📊 Result:", response)
print("✅ Expected: Zhang et al - CNNs 95.2%, Johnson et al - BERT 89.1%")
# Usuários podem verificar: A resposta contém ambos os números de precisão?
```

## 🔧 Visão Técnica

**Componentes Principais de Memória de Longo Prazo:**
1. **Configuração de Estratégia Semântica**: Usa SemanticStrategy para extração automática de insights com retenção de 365 dias
2. **Persistência Entre Sessões**: Mesmo actor_id + memory_id, session_id diferente por período permite continuidade do conhecimento
3. **Ferramenta de Busca de Memória Customizada**: Encapsula o search_long_term_memories() nativo do AgentCore em FunctionTool do LlamaIndex
4. **Pipeline de Processamento Semântico**: Espera de 90-120 segundos para conversão de eventos conversacionais → memórias semânticas
5. **Gerenciamento Dinâmico de Sessão**: Usa memory.context.session_id para manipulação flexível de sessões

## 🔧 Configuração de Memória

### Memória de Curto Prazo
```python
context = AgentCoreMemoryContext(
    actor_id="user-id",
    memory_id=memory_id,
    session_id="session-id",
    namespace="/domain-specific/"
)
agentcore_memory = AgentCoreMemory(context=context)
```

### Memória de Longo Prazo (Retenção de 12 Meses)
```python
# Persistência entre sessões com estratégia semântica
memory = memory_manager.get_or_create_memory(
    name='DomainSpecificLongTerm',
    strategies=[SemanticStrategy(name="domainLongTermMemory")],
    event_expiry_days=365  # Retenção de 12 meses
)

# Mesmo contexto entre sessões para persistência
context = AgentCoreMemoryContext(
    actor_id="advisor-id",      # Mesmo ator entre sessões
    memory_id=memory_id,        # Mesmo armazenamento de memória
    session_id="q1-session",    # Diferente por interação
    namespace="/domain-specific/"
)
```

### Exemplos de Inteligência de Memória
- **Consultor de Investimentos**: Rastreia desempenho trimestral (T1: +8,2% → T2: -2,1% → T3: recuperação)
- **Analisador Jurídico**: Mantém banco de dados de precedentes entre casos e mudanças regulatórias
- **Assistente Médico**: Constrói registros longitudinais de cuidados e resultados de tratamentos
- **Assistente de Pesquisa**: Evolui temas de pesquisa e insights de metodologia ao longo de meses

## 🤝 Contribuição

Este projeto demonstra melhores práticas para integração LlamaIndex + AgentCore Memory. Contribuições são bem-vindas para:

- Exemplos de domínios adicionais
- Metodologias de teste aprimoradas
- Otimizações de performance
- Melhorias na documentação

## 📄 Licença

Este projeto está licenciado sob a Licença MIT.

## 🙋‍♂️ Suporte

Para perguntas sobre:
- **Integração LlamaIndex**: Consulte os notebooks específicos por domínio
- **AgentCore Memory**: Verifique a documentação do AWS Bedrock
- **Padrões de Teste**: Revise os exemplos de testes abrangentes
