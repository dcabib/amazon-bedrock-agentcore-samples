# Executar Comandos no Bedrock AgentCore Runtime

Este tutorial demonstra como executar comandos do sistema diretamente no ambiente do Amazon Bedrock AgentCore Runtime usando a API `invoke_agent_runtime_command`. Aprenda como implantar um agente e executar comandos shell em seu runtime containerizado com saída em tempo real via streaming.

## Pré-requisitos

Antes de iniciar este tutorial, certifique-se de ter:

- **Conta AWS** com permissões apropriadas para Bedrock AgentCore
- **AWS CLI** configurada com credenciais
- **Python 3.12+** instalado
- **Jupyter Notebook** ou JupyterLab
- **Acesso ao Amazon Bedrock AgentCore** na sua região AWS

Pacotes Python necessários:
```bash
pip install -r requirements.txt
```

## Começando

1. **Clone ou baixe este repositório**

2. **Abra o notebook: [01_exec_command.ipynb](./01_exec_command.ipynb)**


3. **Siga as células do notebook sequencialmente**
   - O notebook contém instruções passo a passo com comentários detalhados
   - Lembre-se de reiniciar o kernel após criar os arquivos do agente (Etapa 2)

## O Que Você Aprenderá

Ao completar este tutorial, você irá:

1. **Implantar um Agente Bedrock AgentCore** usando implantação direta de código Python (sem Docker necessário)
2. **Invocar agentes** usando métodos SDK de alto nível e chamadas diretas boto3
3. **Executar comandos shell** no ambiente runtime do agente com `invoke_agent_runtime_command`
4. **Transmitir saída de comando** em tempo real com tratamento adequado de eventos

## Detalhes do Tutorial

| **Atributo**         | **Detalhes**                                          |
|-----------------------|------------------------------------------------------|
| **Tipo de Tutorial**     | Execução de Comando no Runtime do Agente                   |
| **Tipo de Ferramenta**         | Bedrock AgentCore Runtime                            |
| **Componentes**        | Implantação de agente, Execução de comando, Streaming de eventos |
| **Nível de Complexidade**  | Médio                                               |
| **SDKs Usados**         | boto3, bedrock-agentcore-starter-toolkit             |


## Principais Recursos do Tutorial

### 1. Implantação Direta de Código
- Sem Docker necessário
- Implante código Python diretamente no runtime
- Empacotamento automático de dependências

### 2. Métodos de Invocação de Agente
- **SDK de alto nível**: Invocação simplificada com toolkit
- **boto3 direto**: Controle total com AWS SDK

### 3. Execução de Comando (⭐ Recurso Principal)
Execute comandos shell arbitrários no runtime do agente:

```python
response = client.invoke_agent_runtime_command(
    agentRuntimeArn=agent_arn,
    body={
        'command': '/bin/bash -c "ls -l /tmp"',
        'timeout': 300
    }
)
```

### 4. Tratamento de Fluxo de Eventos
Processe saídas de comando em tempo real.

## Casos de Uso

Este recurso de execução de comando é valioso para:

- **Depuração**: Inspecionar o ambiente runtime
- **Operações com Arquivos**: Gerenciar arquivos no contêiner do agente
- **Testes de Integração**: Executar testes dentro do ambiente do agente
- **Processamento de Dados**: Executar scripts e processar resultados
- **Diagnóstico do Sistema**: Verificar configuração e recursos do runtime

## Estrutura do Projeto

```
.
├── 01_exec_command.ipynb        # Notebook tutorial principal
├── agents/
│   ├── agent.py                 # Ponto de entrada do agente
│   └── requirements.txt         # Dependências do agente
└── README.md                    # Este arquivo
```

## Limpeza

Para evitar cobranças contínuas, use a seção de limpeza no notebook (Etapa 7):

```python
from bedrock_agentcore_starter_toolkit.operations.runtime.destroy import destroy_bedrock_agentcore

destroy_bedrock_agentcore(
    config_path=Path(".bedrock_agentcore.yaml"),
    agent_name="exec_cmd_sample"
)
```

## Recursos Adicionais

- [Documentação Bedrock AgentCore](https://docs.aws.amazon.com/bedrock/)
- [API Boto3 para executar um comando em um contêiner de sessão runtime](https://docs.aws.amazon.com/boto3/latest/reference/services/bedrock-agentcore/client/invoke_agent_runtime_command.html)
