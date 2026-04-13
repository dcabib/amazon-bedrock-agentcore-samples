# Agente CrewAI com Amazon Bedrock AgentCore Runtime e Observabilidade

Este tutorial demonstra como implantar um agente de viagens [CrewAI](https://www.crewai.com/) no Amazon Bedrock AgentCore Runtime com observabilidade através do Amazon CloudWatch.

## Visão Geral

Aprenda a hospedar um agente CrewAI usando modelos Amazon Bedrock com observabilidade abrangente com instrumentação AWS OpenTelemetry e monitoramento Amazon CloudWatch.

## Pré-requisitos

* Python 3.10+
* Credenciais AWS configuradas com permissões apropriadas
* Amazon Bedrock AgentCore SDK
* Framework CrewAI
* Acesso ao Amazon CloudWatch
* Habilitar [transaction search](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Enable-TransactionSearch.html) no Amazon CloudWatch

## Primeiros Passos

1. Instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```

2. Abra o Jupyter notebook: `runtime-with-crewai-and-bedrock-models.ipynb`

3. Siga o tutorial para:
   - Criar e testar um agente CrewAI localmente
   - Implantar o agente no AgentCore Runtime
   - Habilitar observabilidade com OpenTelemetry
   - Monitorar o desempenho no CloudWatch

## Funcionalidades Principais

* Agente de viagens CrewAI com capacidades de busca na web
* Modelos Amazon Bedrock (Anthropic Claude Haiku 4.5)
* Hospedagem no AgentCore Runtime
* Observabilidade e rastreamento via CloudWatch

## Limpeza

Após concluir o tutorial:
1. Remova os deployments do AgentCore Runtime
2. Limpe os repositórios ECR
