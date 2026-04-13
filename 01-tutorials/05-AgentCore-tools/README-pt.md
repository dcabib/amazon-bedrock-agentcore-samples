# Amazon Bedrock AgentCore Tools

## Visão Geral
Amazon Bedrock AgentCore Tools fornecem capacidades de nível empresarial que aprimoram a habilidade dos agentes de IA de realizar tarefas complexas de forma segura e eficiente. Este conjunto inclui duas ferramentas principais:

- Amazon Bedrock AgentCore Code Interpreter e
- Amazon Bedrock AgentCore Browser Tool

## Amazon Bedrock AgentCore Code Interpreter

### Principais Recursos

1. **Execução Segura de Código**: Execute código em ambientes sandbox isolados, garantindo segurança ao acessar fontes de dados internas.

2. **Solução Totalmente Gerenciada e Nativa da AWS**: Integra-se perfeitamente com frameworks como Strands Agents, LangGraph e CrewAI.

3. **Suporte de Configuração Avançada**: Inclui suporte para arquivos grandes tanto para entrada quanto para saída, e acesso à internet.

4. **Suporte para Múltiplas Linguagens**: Modos de runtime pré-construídos para várias linguagens de programação incluindo JavaScript, TypeScript e Python.

### Benefícios

- **Precisão Aprimorada do Agente**: Permite que os agentes realizem cálculos complexos e processamento de dados.
- **Segurança de Nível Empresarial**: Atende requisitos rigorosos de segurança com ambientes isolados.
- **Processamento Eficiente de Dados**: Capaz de lidar com dados em escala de gigabytes referenciando arquivos no Amazon S3.

## Amazon Bedrock AgentCore Browser Tool

### Principais Recursos

1. **Flexibilidade Agnóstica de Modelo**: Suporta várias sintaxes de comando de diferentes modelos de IA, incluindo Claude da Anthropic, modelos da OpenAI e modelos Nova da Amazon.

2. **Segurança de Nível Empresarial**: Fornece isolamento em nível de VM, conectividade VPC e integração com sistemas SSO empresariais.

3. **Capacidades Abrangentes de Auditoria**: Inclui registro CloudTrail de todos os comandos do navegador e recursos de gravação de sessão.

### Benefícios

- **Automação de Ponta a Ponta**: Permite que agentes de IA automatizem fluxos de trabalho web complexos que anteriormente exigiam intervenção manual.
- **Segurança Aprimorada**: Atende requisitos empresariais com recursos extensivos de segurança e auditoria.
- **Monitoramento em Tempo Real**: Oferece Live View para intervenção imediata e Session Replay para depuração e auditoria.

## Casos de Uso

- Análise e visualização de dados complexos em ambientes seguros
- Interações web automatizadas para preenchimento de formulários, extração de dados e processos de múltiplas etapas
- Processamento e monitoramento de dados em larga escala
- Execução segura de código para agentes de IA em configurações empresariais

## Visão Geral dos Tutoriais

1. [Amazon Bedrock AgentCore Code Interpreter](01-Agent-Core-code-interpreter)
2. [Amazon Bedrock AgentCore Browser Tool](02-Agent-Core-browser-tool)
