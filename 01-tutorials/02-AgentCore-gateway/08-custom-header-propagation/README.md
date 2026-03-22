# Propagação de Cabeçalhos Personalizados e Parâmetros de Consulta com o AgentCore Gateway

## Visão Geral
Sistemas modernos de agentes empresariais requerem a passagem de informações contextuais através de chamadas de API para rastreamento distribuído, isolamento multi-tenant, versionamento de API e limitação de taxa. O AgentCore Gateway fornece suporte nativo para propagar cabeçalhos HTTP personalizados e parâmetros de consulta dos clientes para os alvos através do recurso `metadataConfiguration`, habilitando esses padrões empresariais sem a necessidade de código de interceptor personalizado.

Este tutorial demonstra como configurar a propagação de cabeçalhos e parâmetros de consulta no nível do alvo, permitindo que o gateway encaminhe automaticamente cabeçalhos específicos (como IDs de correlação e identificadores de tenant) e parâmetros de consulta (como versões de API e flags de ambiente) para funções Lambda downstream ou servidores MCP.

![Como funciona](images/08-custom-header-propagation.png)

### Propagação de Cabeçalhos vs Abordagem com Interceptor

O AgentCore Gateway fornece duas abordagens para manipulação de cabeçalhos:

**Propagação de Cabeçalhos** (este tutorial): Configure `metadataConfiguration` para encaminhar automaticamente cabeçalhos e parâmetros de consulta específicos. Melhor para cabeçalhos personalizados como IDs de correlação, IDs de tenant e versões de API onde nenhuma transformação é necessária.

**Interceptor Lambda** (tutorial 14-token-exchange-at-request-interceptor): Use um Lambda interceptor para cenários sensíveis à segurança como troca de token do cabeçalho Authorization, lógica de autenticação personalizada ou transformação dinâmica de cabeçalhos.

### Como a Propagação de Cabeçalhos Funciona

Ao criar um alvo do gateway, você especifica quais cabeçalhos e parâmetros de consulta propagar através da `metadataConfiguration`:

```python
"metadataConfiguration": {
    "allowedRequestHeaders": ["x-correlation-id", "x-tenant-id"],
    "allowedResponseHeaders": ["x-rate-limit-remaining"],
    "allowedQueryParameters": ["version", "environment"]
}
```

O gateway automaticamente:
1. Extrai os cabeçalhos e parâmetros de consulta especificados das requisições do cliente
2. Encaminha-os para o Lambda alvo na estrutura de evento adequada
3. Retorna os cabeçalhos de resposta especificados ao cliente

### Casos de Uso Principais

* **Rastreamento Distribuído**: IDs de correlação rastreiam requisições entre microsserviços
* **Multi-Tenancy**: Identificadores de tenant garantem o isolamento adequado dos dados
* **Versionamento de API**: Parâmetros de versão roteiam para implementações apropriadas
* **Roteamento de Ambiente**: Flags de ambiente controlam o comportamento de staging vs produção
* **Limitação de Taxa**: Cabeçalhos de resposta comunicam informações de cota

### Detalhes do Tutorial


| Informação           | Detalhes                                                  |
|:---------------------|:----------------------------------------------------------|
| Tipo de tutorial     | Interativo                                                |
| Componentes AgentCore| AgentCore Gateway                                         |
| Framework de Agentes | Strands Agents                                            |
| Tipo de Alvo do Gateway | AWS Lambda                                             |
| IdP de Auth de Entrada | Amazon Cognito, mas pode usar outros                    |
| Auth de Saída        | AWS IAM                                                   |
| Modelo LLM           | Anthropic Claude Haiku 4.5, Amazon Nova Pro              |
| Componentes do tutorial | Criação do AgentCore Gateway e Invocação do AgentCore Gateway |
| Vertical do tutorial | Cross-vertical                                            |
| Complexidade do exemplo | Fácil                                                  |
| SDK utilizado        | boto3                                                     |

## Arquitetura do Tutorial

### Principais Funcionalidades do Tutorial

* Configurar propagação de cabeçalhos personalizados usando metadataConfiguration
* Encaminhar IDs de correlação e identificadores de tenant para alvos Lambda
* Propagar parâmetros de consulta para versionamento de API e roteamento de ambiente
* Retornar cabeçalhos de resposta personalizados para informações de limitação de taxa

## Visão Geral dos Tutoriais

Neste tutorial, cobriremos a seguinte funcionalidade:

- [Propagação de Cabeçalhos Personalizados e Parâmetros de Consulta com o AgentCore Gateway](gateway-interceptor-header-propagation.ipynb)

## Recursos

* [Propagação de cabeçalhos com Gateway - Documentação AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-headers.html)
* [Referência da API MetadataConfiguration](https://docs.aws.amazon.com/bedrock-agentcore-control/latest/APIReference/API_MetadataConfiguration.html)
