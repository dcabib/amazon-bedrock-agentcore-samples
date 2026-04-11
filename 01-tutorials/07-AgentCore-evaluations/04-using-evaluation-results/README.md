# Analisador de Avaliações

**Escale sua análise de avaliação de agentes de IA de dias/semanas para minutos.**

<p align="center">
  <img src="assets/improvement_loop.svg" alt="Loop de melhoria contínua para agentes de IA" width="700">
</p>

## O Problema

Quando você avalia um agente de IA em escala, você obtém centenas de explicações de LLM-como-Juiz. Cada uma contém raciocínio detalhado sobre por que uma pontuação foi dada. Nenhum humano consegue ler todas elas para encontrar padrões.

## O Que Faz

1. **Carrega** seus arquivos JSON de avaliação
2. **Filtra** avaliações com pontuação baixa (limite configurável)
3. **Analisa** padrões de falha usando IA
4. **Gera** correções específicas para o prompt do sistema

## O Que Você Obtém

- **Top 3 problemas** com citações de evidências do juiz LLM
- **Tabela antes/depois** mostrando mudanças exatas no prompt
- **Prompt do sistema atualizado completo** pronto para copiar e colar

Veja [`example_agent_output.md`](example_agent_output.md) para um relatório de exemplo.

## Início Rápido

```bash
# 1. Instalar dependências
pip install -r requirements.txt

# 2. Adicionar seus dados
#    - Coloque JSONs de avaliação em eval_data/
#    - Edite system_prompt.txt com o prompt do seu agente

# 3. Executar o notebook
jupyter notebook evaluation_analyzer.ipynb
```

## Requisitos

- Python 3.9+
- Credenciais AWS configuradas para o Amazon Bedrock
- Dados de avaliação do [Strands Evals](https://github.com/strands-agents/strands-evals) ou [AWS AgentCore](https://docs.aws.amazon.com/agentcore/)

---

**[Abra o notebook](evaluation_analyzer.ipynb) para o passo a passo completo e documentação.**
