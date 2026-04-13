# Amazon Bedrock AgentCore Code Interpreter

## Visão Geral
O Amazon Bedrock AgentCore Code Interpreter é um ambiente seguro e serverless onde agentes de IA podem escrever e executar código diretamente para completar tarefas de ponta a ponta, permitindo que realizem análises de dados complexas, executem simulações, gerem visualizações e automatizem tarefas de programação.

## Como funciona

O sandbox de execução de código permite que agentes processem consultas de usuários de forma segura ao criar um ambiente isolado com interpretador de código, shell e sistema de arquivos. Após um Large Language Model auxiliar na seleção de ferramentas, o código é executado dentro desta sessão, antes de ser retornado ao usuário ou agente para síntese.

![architecture local](../01-Agent-Core-code-interpreter/images/code-interpreter.png)

## Principais Recursos

### Sessões no Ambiente

Capacidade de persistir sessões através de execuções

### Suporte VPC e Acesso à Internet

Fornece recursos de nível empresarial incluindo conectividade VPC e acesso à internet externa

### Múltiplos Runtimes de Ambiente Pré-construídos

Múltiplos runtimes pré-construídos incluindo Python, NodeJS & TypeScript (em breve: suporte para motor de execução de código runtime personalizado com bibliotecas personalizadas)

### Integração

O Amazon Bedrock AgentCore Code Interpreter se integra com outras capacidades do Amazon Bedrock AgentCore através de um SDK unificado, incluindo:

- Amazon Bedrock AgentCore Runtime
- Amazon Bedrock AgentCore Identity
- Amazon Bedrock AgentCore Memory
- Amazon Bedrock AgentCore Observability

Esta integração visa simplificar o processo de desenvolvimento e fornecer uma plataforma abrangente para construir, implantar e gerenciar agentes de IA, com poderosas capacidades de execução de código.

### Casos de Uso

O Amazon Bedrock AgentCore Code Interpreter é adequado para uma ampla gama de aplicações, incluindo:

- Execução e Revisão de Código
- Análise e Visualização de Dados

## Visão geral dos Tutoriais

Nestes tutoriais vamos cobrir as seguintes funcionalidades:

- [Operações de Arquivo usando Amazon Bedrock AgentCore Code Interpreter](01-file-operations-using-code-interpreter)
- [Execução de Código com agente usando Amazon Bedrock AgentCore Code Interpreter](02-code-execution-with-agent-using-code-interpreter)
- [Análise Avançada de Dados com Agente de IA usando Amazon Bedrock AgentCore Code Interpreter](03-advanced-data-analysis-with-agent-using-code-interpreter)
- [Executar Comandos usando Amazon Bedrock AgentCore Code Interpreter](04-run-commands-using-code-interpreter)
