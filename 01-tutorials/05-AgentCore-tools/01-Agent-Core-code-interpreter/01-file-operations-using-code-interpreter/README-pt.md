# Amazon AgentCore Bedrock Code Interpreter - Tutorial de Introdução

## Visão Geral

Neste tutorial você aprenderá como usar o AgentCore Bedrock Code Interpreter para:

1. Configurar um ambiente sandbox
2. Carregar e analisar dados
3. Executar código em um ambiente sandbox
4. Processar e recuperar resultados


### Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Componentes do tutorial | Bedrock AgentCore Code Interpreter                                           |
| Vertical do tutorial | Cross-vertical                                                                  |
| Complexidade do exemplo | Fácil                                                                        |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |


### Arquitetura do Tutorial

O sandbox de execução de código permite que agentes processem consultas de usuários com segurança, criando um ambiente isolado com um interpretador de código, shell e sistema de arquivos. Após um Large Language Model auxiliar na seleção de ferramentas, o código é executado dentro desta sessão, antes de ser retornado ao usuário ou agente para síntese.

<div style="text-align:left">
    <img src="images/code_interpreter.png" width="100%"/>
</div>
