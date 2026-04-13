# Suporte a Middleware no AgentCore Runtime

## Visão Geral

Este tutorial demonstra como implementar middleware no Amazon Bedrock AgentCore Runtime. O middleware permite processar requisições antes que elas cheguem ao seu agente e respostas antes que sejam enviadas de volta aos clientes.

O AgentCore Runtime usa o sistema de middleware ASGI do Starlette, permitindo adicionar funcionalidades transversais como logging, autenticação e manipulação de cabeçalhos sem modificar o código do seu agente.

## Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Implementação de Middleware                                                      |
| Tipo de agente      | Único                                                                            |
| Framework Agêntico  | Strands Agents                                                                   |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                                       |
| Componentes         | Middleware, Processamento de Request/Response, AgentCore Runtime, Strands Agent e Amazon Bedrock Model |
| Vertical            | Cross-vertical                                                                   |
| Complexidade        | Intermediário                                                                    |
| SDK usado           | Amazon BedrockAgentCore Python SDK e boto3                                       |

## O que é Middleware?

Middleware é um componente ASGI que envolve sua aplicação, interceptando requisições e respostas. Cada middleware pode:

- Inspecionar ou modificar requisições recebidas
- Executar lógica antes da execução do seu agente
- Inspecionar ou modificar respostas enviadas
- Adicionar cabeçalhos, logging ou métricas
- Lidar com autenticação ou limitação de taxa

O middleware é avaliado de cima para baixo na ordem especificada, com cada camada envolvendo a próxima.

## Como Funciona

BedrockAgentCoreApp aceita um parâmetro `middleware` durante a inicialização:

```python
from bedrock_agentcore import BedrockAgentCoreApp
from starlette.middleware import Middleware
from starlette.middleware.base import BaseHTTPMiddleware

app = BedrockAgentCoreApp(
    middleware=[
        Middleware(CustomMiddleware),
    ]
)
```

Cada middleware implementa um método async `dispatch` que recebe a requisição e uma função `call_next` para invocar a próxima camada.

## Principais Funcionalidades do Tutorial

* **BaseHTTPMiddleware**: Escreva middleware usando interface de request/response
* **Cabeçalhos Customizados**: Adicione cabeçalhos de rastreamento e depuração
* **Timing de Requisição**: Meça a duração do processamento
* **Logging**: Logging centralizado de request/response
* **Encadeamento**: Empilhe múltiplos componentes de middleware
* **Testes**: Testes locais com TestClient

## Casos de Uso

- **Logging**: Rastreie timing e metadados de request/response
- **Autenticação**: Valide chaves de API ou tokens
- **Cabeçalhos**: Adicione cabeçalhos customizados para rastreamento
- **Métricas**: Colete dados de desempenho
- **CORS**: Lide com requisições cross-origin
- **Limitação de Taxa**: Controle a frequência de requisições
