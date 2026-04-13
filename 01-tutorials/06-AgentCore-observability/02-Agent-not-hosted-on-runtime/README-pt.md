# Observabilidade de Agentes Auto-Hospedados usando AgentCore

Esta seção demonstra a Observabilidade do AgentCore para frameworks de agentes open-source populares **não** hospedados no Amazon Bedrock AgentCore Runtime. Aprenda a adicionar observabilidade abrangente aos seus agentes existentes usando OpenTelemetry e Amazon CloudWatch.

## Frameworks Disponíveis

### CrewAI
- **Notebook**: `CrewAI_Observability.ipynb`
- **Descrição**: Agentes de IA autônomos trabalhando em equipes
- **Funcionalidades**: Colaboração multi-agente com instrumentação personalizada

### LangGraph
- **Notebook**: `Langgraph_Observability.ipynb`
- **Descrição**: Aplicações LLM stateful e multi-ator
- **Funcionalidades**: Sistemas de raciocínio complexo com visualização de traces

### LlamaIndex
- **Notebook**: `LlamaIndex_Observability.ipynb`
- **Descrição**: Agentes alimentados por LLM sobre dados
- **Funcionalidades**: Function agents com rastreamento de sessão
- **Adicional**: README detalhado com diagramas de arquitetura

### Strands Agents
- **Notebook**: `Strands_Observability.ipynb`
- **Descrição**: Desenvolvimento agêntico orientado por modelos
- **Funcionalidades**: Agentes de workflow complexo com spans personalizados

## Primeiros Passos

1. Escolha o diretório do seu framework
2. Instale as dependências: `pip install -r requirements.txt`
3. Configure as credenciais AWS
4. Copie `.env.example` para `.env` e atualize as variáveis
5. Habilite o CloudWatch Transaction Search
6. Execute o Jupyter notebook


## Pré-requisitos

- Conta AWS com acesso ao Bedrock e CloudWatch com as permissões corretas
- Python 3.10+
- AWS CloudWatch Transaction Search habilitado
- Dependências específicas do framework

## Limpeza

Após concluir os exemplos:
1. Exclua os log groups do CloudWatch
2. Remova quaisquer recursos AWS criados
3. Limpe os arquivos de ambiente locais
