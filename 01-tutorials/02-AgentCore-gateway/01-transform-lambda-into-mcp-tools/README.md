# Implementar ferramentas Lambda para o Gateway

## Visão Geral
O Bedrock AgentCore Gateway oferece aos clientes uma maneira de transformar suas funções Lambda existentes em servidores MCP totalmente gerenciados, sem a necessidade de gerenciar infraestrutura ou hospedagem. Os clientes podem trazer suas funções AWS Lambda existentes, ou adicionar novas funções Lambda como front-end para suas ferramentas. O Gateway fornece uma interface uniforme do Model Context Protocol (MCP) para todas essas ferramentas. O Gateway emprega um modelo de autenticação dupla para garantir o controle de acesso seguro tanto para inbound requests quanto para outbound connections aos recursos de destino. O framework consiste em dois componentes principais: Inbound Auth (Inbound Auth), que valida e autoriza usuários que tentam acessar os alvos do gateway, e Outbound Auth (Outbound Auth), que permite ao gateway conectar-se de forma segura aos recursos de backend em nome dos usuários autenticados. Juntos, esses mecanismos de autenticação criam uma ponte segura entre os usuários e seus recursos de destino, suportando tanto credenciais IAM quanto fluxos de autenticação baseados em OAuth.

![Como funciona](images/lambda-iam-gateway.png)

![Como funciona](images/lambda-gw-iam-inbound.png)


### Entendendo o objeto de contexto do Lambda
Quando o Gateway invoca uma função Lambda, ele passa informações de contexto especiais através do objeto context.client_context. Este contexto inclui metadados importantes sobre a invocação, que sua função pode usar para determinar como processar a requisição.
As seguintes propriedades estão disponíveis no objeto context.client_context.custom:
* bedrockagentcoreEndpointId: O ID do endpoint do Gateway que recebeu a requisição.
* bedrockagentcoreTargetId: O ID do alvo do Gateway que roteou a requisição para sua função.
* bedrockagentcoreMessageVersion: A versão do formato de mensagem usado para a requisição.
* bedrockagentcoreToolName: O nome da ferramenta sendo invocada. Isso é particularmente importante quando sua função Lambda implementa múltiplas ferramentas.
* bedrockagentcoreSessionId: O ID da sessão para a invocação atual, que pode ser usado para correlacionar múltiplas chamadas de ferramentas dentro da mesma sessão.

Você pode acessar essas propriedades no código da sua função Lambda para determinar qual ferramenta está sendo invocada e personalizar o comportamento da sua função de acordo.

![Como funciona](images/lambda-context-object.png)

### Formato de resposta e tratamento de erros

Sua função Lambda deve retornar uma resposta que o Gateway possa interpretar e repassar ao cliente. A resposta deve ser um objeto JSON com a seguinte estrutura: O campo statusCode deve ser um código de status HTTP indicando o resultado da operação:
* 200: Sucesso
* 400: Requisição inválida (erro do cliente)
* 500: Erro interno do servidor

O campo body pode ser uma string ou uma string JSON representando uma resposta mais complexa. Se você quiser retornar uma resposta estruturada, deve serializá-la como uma string JSON.

### Tratamento de erros
O tratamento adequado de erros é importante para fornecer feedback significativo aos clientes. Sua função Lambda deve capturar exceções e retornar respostas de erro apropriadas.

### Testes

Note que o campo ```__context__``` não faz parte do evento real que será passado para sua função quando invocada pelo Gateway. Ele é usado apenas para fins de teste para simular o objeto de contexto.
Ao testar no console do Lambda, você precisará modificar sua função para lidar com o contexto simulado. Esta abordagem permite que você teste sua função Lambda com diferentes nomes de ferramentas e parâmetros de entrada antes de implantá-la como um alvo do Gateway.

### Acesso Lambda entre contas

Se sua função Lambda está em uma conta AWS diferente do seu Gateway, você precisa configurar uma política baseada em recurso na função Lambda para permitir que o Gateway a invoque. Aqui está um exemplo de política:

```
{
  "Version": "2012-10-17",
  "Id": "default",
  "Statement": [
    {
      "Sid": "cross-account-access",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/GatewayExecutionRole"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:us-west-2:987654321098:function:MyLambdaFunction"
    }
  ]
}
```
Nesta política:
- 123456789012 é o ID da conta onde o Gateway está implantado
- GatewayExecutionRole é a role IAM usada pelo Gateway.
- 987654321098 é o ID da conta onde a função Lambda está implantada.
- MyLambdaFunction é o nome da função Lambda.

Após adicionar esta política, você pode especificar o ARN da função Lambda na configuração do alvo do Gateway, mesmo que esteja em uma conta diferente.

### Detalhes do Tutorial


| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway, AgentCore Identity, AWS IAM            |
| Framework de Agentes | Strands Agents                                            |
| Modelo LLM           | Anthropic Claude Haiku 4.5, Amazon Nova Pro              |
| Componentes do tutorial | Criação do AgentCore Gateway e Invocação do AgentCore Gateway |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Fácil                                                  |
| SDK utilizado        | boto3                                                     |

## Arquitetura do Tutorial

### Principais Funcionalidades do Tutorial

* Expor funções Lambda como ferramentas MCP
* Proteger as chamadas de ferramentas usando OAuth e IAM

## Visão Geral dos Tutoriais

Nestes tutoriais, cobriremos as seguintes funcionalidades:

- [Transformar sua função AWS Lambda em ferramentas MCP com inbound auth OAuth](01-gateway-target-lambda-oauth.ipynb)

- [Transformar sua função AWS Lambda em ferramentas MCP com inbound auth AWS IAM](02-gateway-target-lambda-iam.ipynb)
