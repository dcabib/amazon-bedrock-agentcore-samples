# Execução de Código Baseada em Agente usando Amazon AgentCore Bedrock Code Interpreter - Tutorial

## Visão Geral

Este tutorial demonstra como criar um agente de IA que valida respostas através da execução de código usando Python. Usaremos o Amazon Bedrock AgentCore Code Interpreter para executar código que é gerado pelo LLM

Este tutorial demonstra como usar o Amazon Bedrock AgentCore Code Interpreter para:

1. Configurar um ambiente sandbox
2. Configurar agentes baseados em strands e langchain que geram código com base na consulta do usuário
3. Executar código em um ambiente sandbox usando Code Interpreter
4. Exibir os resultados de volta ao usuário


### Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Tipo de agente      | Único                                                                            |
| Framework agnético  | Langchain & Strands Agents                                                       |
| Modelo LLM          | Anthropic Claude Sonnet 3.5 & 3.7                                                |
| Componentes do tutorial | Amazon Bedrock AgentCore Code Interpreter                                     |
| Vertical do tutorial | Multisetorial                                                                   |
| Complexidade do exemplo | Fácil                                                                         |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |


### Arquitetura do Tutorial

O sandbox de execução de código permite que agentes processem consultas de usuários de forma segura, criando um ambiente isolado com um interpretador de código, shell e sistema de arquivos. Após um Large Language Model ajudar com a seleção de ferramentas, o código é executado dentro desta sessão, antes de ser retornado ao usuário ou agente para síntese.

<div style="text-align:left">
    <img src="images/code_interpreter.png" width="100%"/>
</div>
