# Executando Comandos no Amazon Bedrock AgentCore Code Interpreter - Tutorial

## Visão Geral

Este tutorial demonstra como usar o Amazon Bedrock AgentCore Code Interpreter para executar comandos (shell e AWS CLI). Iremos interagir com serviços AWS, focando especificamente em operações S3. Vamos percorrer:

1. Criando um interpretador de código baseado em Python
2. Iniciar sessão do interpretador de código
3. Executar Comandos (shell e AWS CLI)
4. Realizar operações S3 (criar bucket, copiar objetos, listar objetos do bucket)
5. Limpeza (parar sessão e excluir interpretador de código)


### Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Tipo de agente      | Único                                                                            |
| Framework agnético  | Langchain & Strands Agents                                                       |
| Modelo LLM          | Anthropic Claude Sonnet 3.5 & 3.7                                                |
| Componentes do tutorial | Amazon Bedrock AgentCore Code Interpreter                                    |
| Vertical do tutorial | Transversal                                                                     |
| Complexidade do exemplo | Fácil                                                                        |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |


### Arquitetura do Tutorial

O sandbox de execução de código permite que agentes processem consultas de usuários de forma segura ao criar um ambiente isolado com interpretador de código, shell e sistema de arquivos. Após um Large Language Model auxiliar na seleção de ferramentas, o código é executado dentro desta sessão, antes de ser retornado ao usuário ou agente para síntese.

<div style="text-align:left">
    <img src="images/code_interpreter.png" width="100%"/>
</div>
