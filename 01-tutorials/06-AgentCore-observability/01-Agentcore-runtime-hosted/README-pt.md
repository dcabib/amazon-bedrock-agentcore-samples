# Observabilidade do AgentCore no Amazon CloudWatch para Agentes do Bedrock AgentCore Runtime

Este repositório contém exemplos para demonstrar a Observabilidade do AgentCore para agentes Strands, CrewAI e LlamaIndex hospedados no Amazon Bedrock AgentCore Runtime usando Amazon OpenTelemetry Python Instrumentation e Amazon CloudWatch. A observabilidade ajuda desenvolvedores a rastrear, depurar e monitorar o desempenho de agentes em produção por meio de dashboards operacionais unificados. Com suporte para telemetria compatível com OpenTelemetry e visualizações detalhadas de cada etapa do fluxo de trabalho do agente, o Amazon CloudWatch GenAI Observability permite que desenvolvedores obtenham facilmente visibilidade sobre o comportamento dos agentes e mantenham padrões de qualidade em escala.

## Exemplos de Frameworks

### Strands Agents
[Strands](https://strandsagents.com/latest/) fornece um framework para construção de aplicações LLM com fluxos de trabalho complexos, focando no desenvolvimento agêntico orientado por modelos.

**Localização**: `Strands Agents/`
- Tutorial: `runtime_with_strands_and_bedrock_models.ipynb`
- Funcionalidades: Ferramentas de clima e calculadora com modelos Amazon Bedrock

### CrewAI
[CrewAI](https://www.crewai.com/) permite colaboração multi-agente com orquestração de agentes baseada em papéis.

**Localização**: `CrewAI/`
- Tutorial: `runtime-with-crewai-and-bedrock-models.ipynb`
- Funcionalidades: Padrões de agentes colaborativos

### LlamaIndex
[LlamaIndex](https://www.llamaindex.ai/) fornece um framework de dados para aplicações LLM com capacidades avançadas de recuperação e raciocínio.

**Localização**: `LlamaIndex/`
- Tutorial: `runtime_with_llamaindex_and_bedrock_models.ipynb`
- Funcionalidades: FunctionAgent com ferramentas aritméticas e observabilidade abrangente

## Primeiros Passos

Cada pasta de framework contém:
- Um notebook Jupyter demonstrando a implantação no AgentCore Runtime e observabilidade com CloudWatch
- Um arquivo requirements.txt listando as dependências necessárias
- Um README.md com instruções específicas do framework

## Uso

1. Navegue até o diretório do framework que deseja explorar
2. Instale os requisitos: `pip install -r requirements.txt`
3. Configure suas credenciais AWS
4. Abra e execute o notebook Jupyter

## Funcionalidades Principais

- **Observabilidade Automática**: Coleta de telemetria integrada quando agentes são executados no AgentCore Runtime
- **Integração com CloudWatch**: Visualize traces e métricas no dashboard GenAI Observability
- **Flexibilidade de Framework**: Suporte para múltiplos frameworks agênticos

## Limpeza

Após concluir os exemplos:

1. Remova as implantações do AgentCore Runtime
2. Limpe quaisquer repositórios ECR criados
3. Exclua os grupos de logs do CloudWatch se não forem mais necessários
