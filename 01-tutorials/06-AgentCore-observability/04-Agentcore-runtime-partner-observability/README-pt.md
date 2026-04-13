# Integração de Observabilidade com Terceiros

Esta seção demonstra como integrar agentes hospedados no Amazon Bedrock AgentCore Runtime com plataformas de observabilidade de terceiros. Aprenda a utilizar ferramentas de monitoramento especializadas mantendo os benefícios do AgentCore Runtime.

## Integrações Disponíveis

A pasta publish contém:
- Um notebook Jupyter demonstrando o AgentCore Runtime com várias soluções de observabilidade
- Um arquivo requirements.txt listando as dependências necessárias

## Primeiros Passos

1. Escolha sua plataforma de observabilidade
2. Crie uma conta na respectiva plataforma
3. Obtenha chaves de API e detalhes de configuração
4. Instale os requisitos: `pip install -r requirements.txt`
5. Configure as variáveis de ambiente no notebook
6. Implante seu agente no AgentCore Runtime
7. Execute o notebook para ver a observabilidade integrada


## Suporte a Frameworks

O Amazon Bedrock AgentCore suporta qualquer framework agêntico e modelo de sua escolha:
- CrewAI
- LangGraph
- LlamaIndex
- Strands Agents

### Strands Agents
O [Strands](https://strandsagents.com/latest/) oferece suporte integrado a telemetria, sendo ideal para demonstrar integrações com terceiros.

## Requisitos de Configuração

Cada plataforma requer configuração específica:

### Arize
- Chave de API e Space ID do painel Arize
- Configuração do projeto

### Braintrust
- Chave de API do painel Braintrust
- Configuração do projeto

### Instana
- Chave do Instana
- Configuração do projeto

### Langfuse
- Chaves pública e secreta
- Configuração do projeto

## Limpeza

Após concluir os exemplos:
1. Exclua as implantações do AgentCore Runtime
2. Remova os repositórios ECR
3. Limpe os recursos específicos da plataforma
4. Revogue as chaves de API se não forem mais necessárias

## Recursos Adicionais

- [Documentação Arize](https://arize.com/docs/ax)
- [Documentação Braintrust](https://www.braintrust.dev/docs)
- [Documentação Instana](https://www.ibm.com/docs/en/instana-observability/1.0.308?topic=overview)
- [Documentação Langfuse](https://langfuse.com/docs)
- [Guia do AgentCore Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/userguide/runtime.html)

# Observabilidade de Terceiros para Agentes do Amazon Bedrock AgentCore

Este repositório contém exemplos de uso de agentes hospedados no Amazon Bedrock AgentCore Runtime com ferramentas de observabilidade de terceiros como Arize, Braintrust, Instana, Langfuse e outros. Estes exemplos demonstram a integração com OpenTelemetry para monitoramento de desempenho de agentes, rastreamento de interações com LLM e depuração de fluxos de trabalho.
