# Tutorial de Análise Avançada de Dados usando Amazon AgentCore Bedrock Code Interpreter

## Visão Geral

Este tutorial demonstra como criar um agente de IA que realiza análise avançada de dados através da execução de código usando Python. Utilizamos o Amazon Bedrock AgentCore Code Interpreter para executar código gerado pelo LLM.

Este tutorial demonstra como usar o AgentCore Bedrock Code Interpreter para:

1. Configurar um ambiente sandbox
2. Configurar agentes baseados em Strands e Langchain que realizam análise avançada de dados gerando código baseado na consulta do usuário
3. Executar código em um ambiente sandbox usando o Code Interpreter
4. Exibir os resultados de volta ao usuário



### Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Conversacional                                                                   |
| Tipo de agente      | Único                                                                            |
| Framework Agêntico  | Langchain & Strands Agents                                                       |
| Modelo LLM          | Anthropic Claude Sonnet 3.5 & 3.7                                                |
| Componentes do tutorial | AmazonBedrock AgentCore Code Interpreter                                     |
| Vertical do tutorial | Cross-vertical                                                                  |
| Complexidade do exemplo | Fácil                                                                        |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                       |


### Arquitetura do Tutorial

O sandbox de execução de código permite que os agentes processem consultas de usuários com segurança, criando um ambiente isolado com um interpretador de código, shell e sistema de arquivos. Após um Large Language Model auxiliar na seleção de ferramentas, o código é executado dentro desta sessão, antes de ser retornado ao usuário ou agente para síntese.

<div style="text-align:left">
    <img src="images/code_interpreter.png" width="100%"/>
</div>
