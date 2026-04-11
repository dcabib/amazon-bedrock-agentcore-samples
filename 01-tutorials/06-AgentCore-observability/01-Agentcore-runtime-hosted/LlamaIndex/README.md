# Agente LlamaIndex com Amazon Bedrock AgentCore Runtime e Observabilidade

Este tutorial demonstra como implantar um [agente LlamaIndex](https://developers.llamaindex.ai/python/framework/use_cases/agents/) no Amazon Bedrock AgentCore Runtime com observabilidade abrangente e coleta de telemetria.

## Visão Geral

Aprenda como:
- Criar um LlamaIndex FunctionAgent com ferramentas aritméticas
- Implantar o agente no AgentCore Runtime com observabilidade automática
- Capturar dados detalhados de telemetria incluindo fluxos de trabalho do agente, chamadas de ferramentas e interações com LLM
- Visualizar traces e métricas no dashboard Amazon CloudWatch GenAI Observability

## O Que Você Vai Construir

Um agente aritmético LlamaIndex que pode:
- Realizar operações de adição e multiplicação usando function tools
- Executar no Amazon Bedrock AgentCore Runtime com escalabilidade integrada
- Gerar automaticamente dados abrangentes de observabilidade
- Ser monitorado através de dashboards CloudWatch com informações detalhadas de trace

## Funcionalidades Principais

- **Integração com LlamaIndex**: Usa LlamaIndex FunctionAgent com workflows assíncronos
- **Observabilidade Automática**: Coleta de telemetria integrada com instrumentação LlamaIndex OpenTelemetry
- **Integração com CloudWatch**: Visualize o desempenho do agente no dashboard GenAI Observability

## Pré-requisitos

- Conta AWS com permissões apropriadas
- Acesso ao modelo Amazon Bedrock (Claude Haiku)
- Python 3.10+
- Credenciais AWS configuradas
- Habilitar [transaction search](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Enable-TransactionSearch.html) no Amazon CloudWatch

## Início Rápido

1. Instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```

2. Execute o notebook:
   ```bash
   jupyter notebook runtime_with_llamaindex_and_bedrock_models.ipynb
   ```

3. Siga o tutorial passo a passo para implantar seu agente com observabilidade

## Arquitetura

O tutorial abrange:
- Desenvolvimento e testes locais com instrumentação LlamaIndex
- Implantação no AgentCore Runtime com observabilidade automática
- Acesso ao dashboard CloudWatch para análise de traces
- Criação manual de spans para telemetria aprimorada

## Arquivos

- `runtime_with_llamaindex_and_bedrock_models.ipynb` - Notebook principal do tutorial
- `requirements.txt` - Dependências Python incluindo observabilidade LlamaIndex
- `README.md` - Esta documentação

## Funcionalidades de Observabilidade

- **Traces do Fluxo de Trabalho do Agente**: Fluxo completo de execução do LlamaIndex FunctionAgent
- **Monitoramento de Chamadas de Ferramentas**: Rastreie invocações de funções aritméticas
- **Traces de Interação com LLM**: Chamadas ao modelo Bedrock com rastreamento de entrada/saída

## Próximos Passos

Após concluir este tutorial, você pode:
- Adicionar ferramentas e fluxos de trabalho mais complexos ao seu agente LlamaIndex
- Implementar arquiteturas multi-agente com observabilidade detalhada
- Configurar alertas personalizados e monitoramento baseado em dados de trace
- Escalar seu agente para cargas de trabalho de produção com visibilidade total
