# Começando com Amazon Bedrock AgentCore Identity

## Introdução

Amazon Bedrock AgentCore Identity é um serviço abrangente de gerenciamento de identidade e credenciais projetado especificamente para agentes de IA e cargas de trabalho automatizadas. Ele fornece recursos seguros de autenticação, autorização e gerenciamento de credenciais que permitem aos usuários invocar agentes, e aos agentes acessar recursos e serviços externos em nome dos usuários, mantendo controles de segurança rigorosos e trilhas de auditoria. Identidades de agentes são implementadas como identidades de carga de trabalho com atributos especializados que habilitam capacidades específicas de agente, mantendo compatibilidade com padrões de identidade de carga de trabalho da indústria. O serviço se integra nativamente com Amazon Bedrock AgentCore para fornecer gerenciamento abrangente de identidade e credenciais para aplicações de agente.

## Tipos de Autenticação suportados pelo AgentCore Identity

AgentCore Identity permite validar acesso de entrada (Inbound Auth) para usuários e aplicações chamando agentes ou ferramentas em um AgentCore Runtime ou validar acesso a alvos AgentCore Gateway. Ele também fornece acesso seguro de saída (Outbound Auth) de um agente ou alvo Gateway para serviços externos. Ele se integra com seus provedores de identidade existentes (como Amazon Cognito) enquanto impõe limites de permissão para agentes atuando independentemente ou em nome de usuários (via OAuth). 

<div style="text-align:center">
    <img src="images/auth_basics1.png" width="75%" />
    <img src="images/auth_basics2.png" width="75%" />
</div>

* Inbound (Entrada): Inbound Auth é usado para conceder acesso aos usuários para invocar agentes ou ferramentas. Considere um cenário onde um usuário quer invocar um agente dentro de uma aplicação. Esse usuário deve ter permissões para invocar o agente e o agente deve reter quem é o usuário ao realizar tarefas. Inbound auth suporta dois mecanismos de autenticação, AWS IAM ou OAuth. OAuth permite aos construtores de agentes uma maneira de invocar o agente sem ter que conceder aos usuários permissões IAM.

* Outbound (Saída): Outbound auth é usado para conceder aos agentes ou AgentCore Gateways acesso a serviços AWS ou recursos externos em nome dos usuários. AgentCore Identity usará a role de execução IAM fornecida para acessar recursos AWS. Fluxos de acesso OAuth de 2 ou 3 etapas serão usados para recursos externos.