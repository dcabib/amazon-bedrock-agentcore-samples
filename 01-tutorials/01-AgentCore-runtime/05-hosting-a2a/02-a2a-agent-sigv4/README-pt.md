# Exemplo de AgentCore A2A com autenticação IAM

Este exemplo demonstra como implantar um agente A2A (Agent-to-Agent) no Amazon Bedrock AgentCore Runtime usando AWS IAM para autenticação de entrada. Ele combina o protocolo A2A com autenticação baseada em IAM, fornecendo uma maneira segura de implantar agentes que se comunicam usando credenciais AWS.

## Arquitetura

```
┌─────────────┐         IAM Auth          ┌──────────────────┐
│   Client    │ ────────────────────────> │  A2A Agent       │
│  (SigV4)    │                           │  (AgentCore)     │
└─────────────┘                           └──────────────────┘
```

## Principais recursos

* Protocolo A2A para comunicação agente-a-agente
* Autenticação AWS IAM (SigV4)
* Framework Strands para implementação do agente
* Implantação no AgentCore Runtime

## Pré-requisitos

* Python 3.10+
* AWS CLI configurada com credenciais
* Docker em execução
* pip instalado

## Instalação

```bash
pip install -r requirements.txt
```

## Início rápido

### Opção 1: Usando Jupyter Notebook (Recomendado)

```bash
jupyter notebook hosting_a2a_iam_auth.ipynb
```

Siga as instruções passo a passo no notebook.

### Opção 2: Implantação manual

#### Passo 1: Testar localmente (Opcional)

```bash
# Terminal 1: Iniciar o agente
python agent.py

# Terminal 2: Testar o agent card
curl http://localhost:9000/.well-known/agent-card.json | jq .

# Terminal 2: Enviar uma mensagem de teste
curl -X POST http://localhost:9000 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": "req-001",
    "method": "message/send",
    "params": {
      "message": {
        "role": "user",
        "parts": [{
          "kind": "text",
          "text": "Olá! O que você pode fazer?"
        }],
        "messageId": "test-001"
      }
    }
  }' | jq .
```

#### Passo 2: Implantar no AgentCore Runtime

```bash
python deploy.py
```

O script irá:
1. Construir e fazer push de uma imagem Docker para ECR
2. Criar uma função de execução com permissões necessárias
3. Implantar o agente no AgentCore Runtime
4. Mostrar o ARN do agente

#### Passo 3: Testar agente implantado

```bash
# Definir o ARN do agente da saída de implantação
export AGENT_ARN="arn:aws:bedrock-agentcore:us-east-1:..."

# Executar o cliente de teste
python client.py
```

## Saída esperada

```
INFO:__main__:Usando região AWS: us-east-1
INFO:__main__:Testando agente: arn:aws:bedrock-agentcore:...
INFO:__main__:ID da sessão: ...
INFO:__main__:Buscando agent card...
INFO:__main__:Agente: A2A IAM Auth Agent
INFO:__main__:Descrição: Um agente A2A simples demonstrando autenticação IAM...

============================================================
INFO:__main__:Enviando mensagem: Olá! O que você pode fazer?

INFO:__main__:Resposta do agente:
Eu sou um agente A2A implantado no Amazon Bedrock AgentCore Runtime...
```

## Solução de problemas

### Docker não está em execução

```
Erro: Não é possível conectar ao daemon Docker
Solução: Inicie o Docker Desktop ou daemon Docker
```

### Credenciais AWS não configuradas

```
Erro: Não é possível localizar credenciais
Solução: Execute 'aws configure' ou defina AWS_PROFILE
```

### Erros de permissão

A implantação requer estas permissões IAM:
- `bedrock-agentcore:*` - Operações do AgentCore
- `ecr:*` - Registro de container
- `iam:CreateRole`, `iam:PutRolePolicy` - Criação de função de execução
- `codebuild:*` - Construção de imagens de container
- `logs:*` - Acesso aos logs do CloudWatch

A função de execução precisa de:
- `ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, `ecr:GetDownloadUrlForLayer` - Acesso ao ECR
- `bedrock:InvokeModel`, `bedrock:InvokeModelWithResponseStream` - Acesso ao modelo Bedrock
- `logs:*` - Logs do CloudWatch
- `bedrock-agentcore:GetWorkloadAccessToken*` - Identidade de workload

Veja `execution-role-policy.json` para a política completa da função de execução.

## Limpeza

```python
from bedrock_agentcore_starter_toolkit.operations.runtime import destroy_bedrock_agentcore
from pathlib import Path

destroy_bedrock_agentcore(
    config_path=Path(".bedrock-agentcore-config.yaml"),
    region="us-east-1"
)
```

## Arquivos

* `agent.py` - Implementação do agente A2A com ferramentas
* `client.py` - Cliente para testar o agente implantado com autenticação IAM
* `deploy.py` - Script de implantação
* `requirements.txt` - Dependências Python
* `execution-role-policy.json` - Política IAM para a função de execução
* `hosting_a2a_iam_auth.ipynb` - Notebook tutorial passo a passo

## Referências

* [Documentação do AgentCore Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html)
* [Especificação do protocolo A2A](https://a2a-protocol.org/dev/specification/)
* [Framework Strands Agents](https://strandsagents.com/latest/)
