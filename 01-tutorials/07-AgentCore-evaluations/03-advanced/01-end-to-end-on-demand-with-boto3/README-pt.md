# Utilitário de Avaliação do AgentCore

Utilitário Python para extrair dados de traces do CloudWatch e avaliar sessões de agentes usando a API DataPlane de Avaliação do AgentCore.

## Instalação

```bash
pip install -r requirements.txt
```

## Configuração

Configure as credenciais AWS com acesso ao CloudWatch Logs e à API de Avaliação do AgentCore:

```bash
aws configure
```

Ou defina variáveis de ambiente:

```bash
export AWS_ACCESS_KEY_ID="sua-chave"
export AWS_SECRET_ACCESS_KEY="seu-segredo"
export AWS_DEFAULT_REGION="us-east-1"
```

## Uso

```python
from utils import EvaluationClient

# Inicializar cliente
client = EvaluationClient(region="us-east-1")

# Avaliar uma sessão
results = client.evaluate_session(
    session_id="seu-session-id",
    evaluator_ids=["Builtin.Helpfulness"],
    agent_id="seu-agent-id",
    region="us-east-1"
)

# Imprimir resultados
for result in results.results:
    print(f"{result.evaluator_name}: {result.value} - {result.label}")
    print(f"Explicação: {result.explanation}")
```

## Suporte a Múltiplos Avaliadores

Avalie com múltiplos avaliadores em uma única chamada:

```python
results = client.evaluate_session(
    session_id="session-id",
    evaluator_ids=["Builtin.Helpfulness", "Builtin.Accuracy", "Builtin.Harmfulness"],
    agent_id="agent-id",
    region="us-east-1"
)
```

## Salvamento Automático e Metadados

Salve arquivos de entrada/saída e rastreie experimentos:

```python
results = client.evaluate_session(
    session_id="session-id",
    evaluator_ids=["Builtin.Helpfulness"],
    agent_id="agent-id",
    region="us-east-1",
    auto_save_input=True,   # Salva em evaluation_input/
    auto_save_output=True,  # Salva em evaluation_output/
    auto_create_dashboard=True,  # gera dados para dashboard HTML disponível localmente
    metadata={  # passe literalmente qualquer coisa
        "experiment": "baseline",
        "description": "Execução inicial de avaliação"
    }
)
```

Os arquivos de entrada contêm apenas os spans enviados à API para replay exato. Os arquivos de saída contêm resultados completos com metadados.

## Detalhes de Implementação

O utilitário consulta o CloudWatch Logs para spans OpenTelemetry e logs de runtime, filtra dados relevantes (atributos gen_ai e logs de conversa) e envia para a API de avaliação. A janela de retrospectiva padrão é de 7 dias com um máximo de 1000 itens por avaliação.
