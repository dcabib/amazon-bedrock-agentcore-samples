# Compreendendo o Contexto de Runtime e Gerenciamento de Sessão no AgentCore Runtime

## Visão Geral

Neste tutorial, aprenderemos como compreender e trabalhar com contexto de runtime e gerenciamento de sessão no Amazon Bedrock AgentCore Runtime. Este exemplo demonstra como o AgentCore Runtime gerencia sessões, mantém contexto através de múltiplas invocações, e como agentes podem acessar informações de runtime através do objeto de contexto.

O Amazon Bedrock AgentCore Runtime fornece sessões isoladas para cada interação de usuário, permitindo que agentes mantenham contexto e estado através de múltiplas invocações enquanto garantem isolamento completo de segurança entre diferentes usuários.

### Detalhes do Tutorial

|Informação| Detalhes|
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Contexto e Gerenciamento de Sessão|
| Tipo de agente      | Único         |
| Framework Agêntico  | Strands Agents |
| Modelo LLM          | Anthropic Claude Haiku 4.5 |
| Componentes do tutorial | Contexto de Runtime, Gerenciamento de Sessão, AgentCore Runtime, Strands Agent e modelo Amazon Bedrock |
| Vertical do tutorial | Cross-vertical                                                                   |
| Complexidade do exemplo | Intermediário                                                                 |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3|

### Arquitetura do Tutorial

Neste tutorial, exploraremos como o Amazon Bedrock AgentCore Runtime gerencia sessões e fornece contexto aos agentes. Demonstraremos:

1. **Continuidade de Sessão**: Como o mesmo ID de sessão mantém contexto através de múltiplas invocações
2. **Objeto de Contexto**: Como agentes podem acessar informações de runtime através do parâmetro de contexto
3. **Isolamento de Sessão**: Como diferentes IDs de sessão criam ambientes completamente isolados
4. **Flexibilidade de Payload**: Como passar dados personalizados para agentes através do payload

Para fins de demonstração, usaremos um Strands Agent que demonstra essas capacidades de gerenciamento de sessão.

    
<div style="text-align:left">
    <img src="images/architecture_runtime.png" width="60%"/>
</div>

### Principais Funcionalidades do Tutorial

* **Gerenciamento de Contexto Baseado em Sessão**: Compreendendo como o AgentCore Runtime mantém contexto dentro de sessões
* **Ciclo de Vida de Sessão do Runtime**: Aprendendo sobre criação, manutenção e término de sessão
* **Acesso ao Objeto de Contexto**: Acessando informações de runtime como ID de sessão através do parâmetro de contexto
* **Isolamento de Sessão**: Demonstrando como diferentes sessões fornecem isolamento completo
* **Manipulação de Payload**: Passagem flexível de dados através de estruturas de payload personalizadas
* **Estado entre Invocações**: Mantendo estado do agente através de múltiplas chamadas dentro da mesma sessão
