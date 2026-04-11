# LlamaIndex Function Agent com AWS Bedrock e OpenTelemetry

Este projeto demonstra como criar um agente aritmético simples usando LlamaIndex, hospedado no AWS Bedrock com instrumentação OpenTelemetry para rastreamento de observabilidade do AgentCore.

## Visão Geral do Projeto

Este projeto implementa:
- Um padrão Function agent usando o FunctionAgent do LlamaIndex.core
- Integração com o modelo Claude do AWS Bedrock para o backend LLM
- Instrumentação OpenTelemetry para observabilidade com AWS CloudWatch
- Ferramentas aritméticas simples (adição e multiplicação)
- Capacidades de rastreamento de sessão para correlacionar traces entre múltiplas execuções do agente

## Diagrama de Arquitetura

O diagrama a seguir ilustra a arquitetura desta implementação do agente LlamaIndex com AWS Bedrock e OpenTelemetry:

![Diagrama de Arquitetura LlamaIndex AgentCore](images/llamaindex_agentcore_arch_diagram.png)

## Pré-requisitos

- Python 3.9+
- Conta AWS com acesso ao serviço Bedrock (especificamente modelos Claude)
- Credenciais AWS configuradas localmente
- Permissões IAM apropriadas para AWS Bedrock e CloudWatch
- CloudWatch Transaction Search habilitado (para visualização de traces)

## Instalação

1. Se você clonou o repositório completo do Amazon Bedrock AgentCore Samples:
```bash
git clone https://github.com/aws-samples/amazon-bedrock-agentcore-samples.git
cd amazon-bedrock-agentcore-samples/01-tutorials/06-AgentCore-observability/02-Agent-not-hosted-on-runtime/LlamaIndex
```

2. Crie e ative um ambiente virtual:
```bash
# Crie um ambiente virtual
python -m venv venv

# Ative o ambiente virtual
# No Windows
venv\Scripts\activate
# No macOS/Linux
source venv/bin/activate
```

3. Instale as dependências:
```bash
pip install -r requirements.txt
```

4. Ao abrir o notebook no Jupyter ou VS Code:
   - Selecione o kernel "venv" no seletor de kernel
   - Se o kernel não aparecer na lista, reinicie o Jupyter ou VS Code

## Configuração

### Credenciais AWS

Certifique-se de que suas credenciais AWS estão corretamente configuradas com acesso ao AWS Bedrock e CloudWatch:

Execute ```aws configure``` no seu CLI para configurar corretamente suas credenciais Amazon. Não é necessário armazená-las no seu arquivo .env

### Configuração do OpenTelemetry

O projeto utiliza as seguintes variáveis de ambiente do OpenTelemetry, que devem ser definidas em um arquivo `.env` (use `.env.example` como modelo):

```bash
# Configuração do Agente
AGENT_ID=llama-index-function-agent
SERVICE_NAME=llama-index-bedrock-agent
BEDROCK_MODEL_ID=global.anthropic.claude-haiku-4-5-20251001-v1:0

# Configuração do OpenTelemetry
AGENT_OBSERVABILITY_ENABLED=true
OTEL_PYTHON_DISTRO=aws_distro
OTEL_PYTHON_CONFIGURATOR=aws_configurator
OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
OTEL_TRACES_EXPORTER=otlp
```

### Configuração do Log Group do CloudWatch

Antes de executar o agente, você precisa criar um log group e um log stream no CloudWatch:

```python
import boto3

cloudwatch_client = boto3.client("logs")
cloudwatch_client.create_log_group(logGroupName='agents/llama-index-agent-logs')
cloudwatch_client.create_log_stream(logGroupName='agents/llama-index-agent-logs', logStreamName='default')
```

Em seguida, atualize seu arquivo `.env` para incluir:

```bash
OTEL_EXPORTER_OTLP_LOGS_HEADERS=x-aws-log-group=agents/llama-index-agent-logs,x-aws-log-stream=default,x-aws-metric-namespace=bedrock-agentcore
OTEL_RESOURCE_ATTRIBUTES=service.name=agentic-llamaindex-agentcore
```

## Uso

### Agente Básico

Para executar o agente aritmético básico com instrumentação OpenTelemetry:

```bash
opentelemetry-instrument python llama_index_agent.py
```

Isso executará uma tarefa aritmética simples: `What is (121 + 2) * 5?`

### Agente com Rastreamento de Sessão

Para executar o agente com rastreamento de sessão para correlação de traces:

```bash
opentelemetry-instrument python llama_index_agent_with_session.py --session-id "your-session-id"
```

Esta versão permite correlacionar traces entre múltiplas execuções do agente usando um session ID consistente.

## Tutorial em Jupyter Notebook

O repositório inclui um Jupyter notebook (`LlamaIndex_Observability.ipynb`) que demonstra:

1. Configuração do ambiente e pré-requisitos
2. Criação dos log groups necessários no CloudWatch
3. Configuração das variáveis de ambiente
4. Execução do agente com e sem rastreamento de sessão
5. Compreensão dos traces no dashboard do AWS CloudWatch

O notebook serve como um tutorial interativo para configurar e executar o agente com observabilidade adequada.

## Detalhes da Instrumentação OpenTelemetry

Este projeto utiliza o AWS Distro for OpenTelemetry (ADOT) para enviar dados de telemetria ao AWS CloudWatch. A instrumentação é configurada usando a classe `LlamaIndexOpenTelemetry` do `llama_index.observability.otel`.

Pontos-chave de instrumentação:
- Inicialização do agente
- Chamadas LLM ao AWS Bedrock
- Execução de ferramentas (cada ferramenta possui seu próprio span)
- Processamento de queries do agente

### Visualização de Traces

Para visualizar os traces:
1. Certifique-se de que o CloudWatch Transaction Search está habilitado
2. Navegue até o console do CloudWatch
3. Vá para GenAI Observability
4. Procure traces com o nome de serviço do seu agente (padrão: `agentic-llamaindex-agentcore`)

## Solução de Problemas

### Problemas Comuns

1. **Credenciais AWS Não Encontradas**
   - Certifique-se de que as credenciais AWS estão corretamente definidas no seu ambiente
   - Verifique se seu usuário IAM possui as permissões apropriadas para Bedrock e CloudWatch

2. **Traces do OpenTelemetry Não Aparecem**
   - Verifique se o CloudWatch Transaction Search está habilitado
   - Confirme que o log group especificado em `OTEL_EXPORTER_OTLP_LOGS_HEADERS` existe
   - Certifique-se de que a região AWS está corretamente configurada

3. **Acesso ao Modelo Bedrock**
   - Verifique se você tem acesso ao modelo Bedrock especificado em `BEDROCK_MODEL_ID`
   - Confira as cotas de throughput do modelo Bedrock para sua conta

4. **Avisos do OpenTelemetry em Jupyter Notebooks**
   - Ao executar `opentelemetry-instrument` em células de Jupyter notebook, você pode ver avisos como:
     ```
     WARNING:opentelemetry.trace:Overriding of current TracerProvider is not allowed
     ```
     ou mensagens sobre `SpanDropEvent` e spans finalizando com erros.
   - Esses avisos são esperados em ambientes de notebook e não afetam a funcionalidade do agente ou a coleta de dados de observabilidade
   - Eles ocorrem porque o Jupyter possui seu próprio contexto de instrumentação, e executar células múltiplas vezes pode fazer o OpenTelemetry tentar se registrar novamente
   - Você pode ignorar esses avisos com segurança, desde que seu agente execute corretamente e os traces apareçam no CloudWatch

### Log Groups do CloudWatch

Os traces do OpenTelemetry são enviados para um log group do CloudWatch especificado nas suas variáveis de ambiente:
```
agents/llama-index-agent-logs
```

Se os traces não estiverem aparecendo, certifique-se de que este log group existe e está corretamente configurado no seu arquivo `.env`.


## Recursos Adicionais

- [Documentação do LlamaIndex](https://docs.llamaindex.ai/)
- [Documentação do AWS Bedrock](https://docs.aws.amazon.com/bedrock)
- [Documentação do OpenTelemetry](https://opentelemetry.io/docs/)
- [AWS Distro for OpenTelemetry (ADOT)](https://aws.amazon.com/otel/)
- [Documentação do CloudWatch Transaction Search](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Enable-TransactionSearch.html)
