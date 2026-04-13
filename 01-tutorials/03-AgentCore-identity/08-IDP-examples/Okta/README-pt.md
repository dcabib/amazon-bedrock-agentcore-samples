# Integração do Okta com Amazon Bedrock AgentCore

Este repositório contém notebooks abrangentes demonstrando como integrar o Okta com o Amazon Bedrock AgentCore para vários cenários de autenticação e autorização.

## O que é Okta?

Okta é um serviço de gerenciamento de identidade e acesso baseado em nuvem que fornece soluções de identidade seguras para empresas, possibilitando autenticação e autorização contínuas em aplicações e serviços.

### Recursos Principais:
- **Single Sign-On (SSO)** - Usuários se autenticam uma vez para acessar múltiplas aplicações
- **Multi-Factor Authentication (MFA)** - Segurança aprimorada através de métodos adicionais de verificação
- **Adaptive Authentication** - Políticas de autenticação baseadas em risco com base no comportamento e contexto do usuário
- **Universal Directory** - Gerenciamento centralizado de usuários e sincronização de perfis
- **API Access Management** - Suporte OAuth 2.0 e OpenID Connect para segurança de API

### Integração com AgentCore

Okta pode ser usado como provedor de identidade com AgentCore Identity para:
- Autenticar usuários antes que possam invocar agentes (autenticação de entrada)
- Autorizar agentes a acessar recursos protegidos em nome dos usuários (autenticação de saída)
- Proteger endpoints do AgentCore Gateway com autorização baseada em JWT

## Visão Geral dos Notebooks de Exemplo

Este caminho de aprendizado inclui notebooks práticos que demonstram diferentes padrões de integração:

### 1. Step by Step Okta for Inbound Auth.ipynb

**Propósito**: Mostra como usar o Okta para **autenticação de entrada** para proteger endpoints de agentes do AgentCore Runtime, garantindo que apenas usuários autenticados possam invocar agentes.

**O que você aprenderá**:
- Configurar tenant Okta e configuração de aplicação
- Criar provedores de credenciais OAuth2 do AgentCore
- Implementar fluxos OAuth 2.0 para autenticação e delegação de usuário
- Construir e implantar agentes no AgentCore Runtime com integração Okta
- Gerenciar sessões de usuário

**Padrão de Integração Principal**:
- Usuários devem se autenticar com Okta antes de acessar endpoints de agentes do AgentCore Runtime
- Tokens bearer validam a identidade do usuário em cada requisição
- Agentes permanecem protegidos por trás da camada de autenticação

## Suporte e Documentação

- [Documentação Okta Developer](https://developer.okta.com/)
- [Documentação Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/)
- [OAuth 2.0 e OpenID Connect](https://developer.okta.com/docs/concepts/oauth-openid/)

## Nota

Okta não é um serviço AWS. Consulte a documentação do Okta para custos e licenciamento relacionados ao uso do Okta.
