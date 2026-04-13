# Exemplos MCP Stateful

Tutoriais end-to-end demonstrando recursos de servidor e cliente MCP (Model Context Protocol) no Amazon Bedrock AgentCore Runtime.

## Suporte a Recursos MCP no AgentCore Runtime

<table>
  <thead>
    <tr>
      <th>Categoria</th>
      <th>Recurso</th>
      <th>Métodos da Spec</th>
      <th align="center">Runtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="4"><strong>Recursos do Servidor MCP</strong></td>
      <td>Tools</td>
      <td><code>tools/list</code>, <code>tools/call</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Tools (output schema)</td>
      <td><code>output schema</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Resources</td>
      <td><code>resources/list</code>, <code>resources/read</code>, <code>resources/subscribe</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Prompts</td>
      <td><code>prompts/list</code>, <code>prompts/get</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td rowspan="3"><strong>Recursos do Cliente MCP</strong></td>
      <td>Sampling</td>
      <td><code>sampling/createMessage</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Roots</td>
      <td><code>roots/list</code></td>
      <td align="center">TBD</td>
    </tr>
    <tr>
      <td>Elicitation</td>
      <td><code>elicitation/create</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td rowspan="2"><strong>Protocolo Base MCP</strong></td>
      <td>Lifecycle</td>
      <td><code>initialize</code>, <code>initialized</code>, <code>ping</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Transports</td>
      <td><code>response streaming</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td rowspan="4"><strong>Utilitários MCP</strong></td>
      <td>Progress</td>
      <td><code>notifications/progress</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Cancellation</td>
      <td><code>notifications/cancelled</code></td>
      <td align="center">TBD</td>
    </tr>
    <tr>
      <td>Logging</td>
      <td><code>logging/setLevel</code></td>
      <td align="center">✅</td>
    </tr>
    <tr>
      <td>Tasks</td>
      <td><code>tasks/list</code>, <code>tasks/cancel</code></td>
      <td align="center">✅</td>
    </tr>
  </tbody>
</table>

> **Legenda:** ✅ Suportado &nbsp;|&nbsp; TBD A Ser Determinado

## Estrutura do Projeto

```
Stateful/
├── 1-server-e2e/          # Recursos do Servidor MCP (Tools, Resources, Prompts)
├── 2-client-e2e/          # Recursos do Cliente MCP (Elicitation, Sampling, Roots)
├── 3-utilities-e2e/       # Utilitários MCP (Notificações de Progresso)
└── helpers/               # Utilitários compartilhados para serviços AWS e implantação
```

### 1. Recursos do Servidor MCP (`1-server-e2e/`)

Tutorial completo demonstrando como construir e implantar um servidor MCP com todas as três capacidades principais:

- **Tools**: Funções executáveis para rastreamento de despesas (adicionar, listar, obter transações)
- **Resources**: Relatórios dinâmicos de despesas expostos como recursos legíveis
- **Prompts**: Templates pré-definidos para análise e categorização de despesas

**Tutorial:** [📓 mcp_server_features_e2e.ipynb](./01-server-e2e/mcp_server_features_e2e.ipynb)

**Inclui:**
- Implantação no AgentCore Runtime
- Integração com DynamoDB para armazenamento persistente
- Configuração de autenticação com Cognito
- Exemplo real de rastreamento de despesas

### 2. Recursos do Cliente MCP (`2-client-e2e/`)

Demonstra capacidades MCP do lado do cliente para interações stateful avançadas:

- **Elicitation**: Coleta interativa de entrada do usuário em múltiplas etapas (por exemplo, entrada guiada de despesas)
- **Sampling**: Servidor delega inferência LLM ao cliente para análise com IA
- **Roots**: Cliente expõe raízes do sistema de arquivos ao servidor (suporte limitado no Runtime)

**Tutorial:** [📓 mcp_client_features_e2e.ipynb](./02-client-e2e/mcp_client_features_e2e.ipynb)


### 3. Utilitários MCP (`3-utilities-e2e/`)

Tutoriais para recursos utilitários MCP que melhoram a experiência do usuário:

- **Notificações de Progresso**: Atualizações de execução em tempo real durante operações de longa duração

**Tutorial:** [📓 01_progress.ipynb](./03-utilities-e2e/01_progress.ipynb)

**Demonstra:**
- Atualizações de progresso fire-and-forget (vs requisição/resposta como elicitation/sampling)
- Relatório financeiro mensal em 5 etapas com barra de progresso ao vivo
- `ctx.report_progress()` para streaming de status de execução
- Callback `progress_handler` personalizado no cliente

### 4. Utilitários Compartilhados (`helpers/`)

Utilitários comuns usados nos tutoriais:

- `utils.py`: Auxiliares de serviços AWS (Cognito, IAM, DynamoDB)
- `dynamo_utils.py`: Operações DynamoDB para rastreamento financeiro

**Uso nos notebooks:**
```python
import sys
from pathlib import Path
sys.path.insert(0, str(Path.cwd().parent))

from helpers.utils import get_or_create_cognito_pool
from helpers.dynamo_utils import FinanceDB
```

## Pré-requisitos

- AWS CLI configurada com permissões apropriadas
- Python 3.12+ (3.13 recomendado para implantações no Runtime)
- Ambiente Jupyter Notebook
- Acesso ao Amazon Bedrock AgentCore Runtime
- Serviços AWS: DynamoDB, Cognito, IAM


**AgentCore Runtime:**
- Autenticação completa com Cognito
- Infraestrutura gerenciada e escalonamento


## Recursos

- [Especificação MCP](https://modelcontextprotocol.io/specification/2025-11-25/server)
- [Documentação AWS Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/)
