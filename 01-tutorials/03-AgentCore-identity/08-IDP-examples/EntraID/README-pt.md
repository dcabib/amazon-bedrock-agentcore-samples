# Integração do Microsoft Entra ID com Amazon Bedrock AgentCore

Este repositório contém três notebooks abrangentes demonstrando como integrar o Microsoft Entra ID (anteriormente Azure Active Directory) com o Amazon Bedrock AgentCore para vários cenários de autenticação e autorização.

## O que é Microsoft Entra ID?

Microsoft Entra ID é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft que serve como provedor de identidade central para Microsoft 365, Azure e outras aplicações SaaS.

### Recursos Principais:
- **Single Sign-On (SSO)** - Usuários se autenticam uma vez para acessar múltiplas aplicações
- **Multi-Factor Authentication (MFA)** - Segurança aprimorada através de métodos adicionais de verificação
- **Conditional Access** - Controle de acesso baseado em políticas com base em usuário, dispositivo, localização e risco
- **Application Integration** - Suporta protocolos de autenticação modernos como OAuth 2.0, OpenID Connect e SAML

### Integração com AgentCore


Microsoft Entra ID pode ser usado como provedor de identidade com AgentCore Identity para:
- Autenticar usuários antes que possam invocar agentes (autenticação de entrada)
- Autorizar agentes a acessar recursos protegidos em nome dos usuários (autenticação de saída)
- Proteger endpoints do AgentCore Gateway com autorização baseada em JWT

## Visão Geral dos Notebooks de Exemplo

Este caminho de aprendizado inclui três notebooks práticos que demonstram diferentes padrões de integração:

### 1. Step By Step MS EntraID and 3LO Outbound for Tools.ipynb

**Propósito**: Demonstra como usar o Entra ID para **autenticação de saída** onde agentes implantados no AgentCore Runtime acessam recursos externos (Microsoft OneNote) em nome de usuários autenticados.

**O que você aprenderá**:
- Configurar tenant Entra ID e registro de aplicação
- Criar provedores de credenciais OAuth2 do AgentCore
- Implementar fluxo OAuth de 3 etapas (3LO) para delegação de usuário
- Construir agentes e implantar no AgentCore Runtime para criar e gerenciar notebooks OneNote

**Padrão de Integração Principal**:
- Usuário se autentica com Entra ID
- AgentCore Runtime recebe permissões delegadas para acessar a API do OneNote
- Ferramentas do agente AgentCore Runtime executam ações em nome do usuário


**Ferramentas Criadas**:
- `create_notebook` - Cria novos notebooks OneNote
- `create_notebook_section` - Adiciona seções aos notebooks
- `add_content_to_notebook_section` - Cria páginas com conteúdo

### 2. Step by Step Entra ID for Inbound Auth.ipynb

**Propósito**: Mostra como usar o Entra ID para **autenticação de entrada** para proteger endpoints de agentes do AgentCore Runtime, garantindo que apenas usuários autenticados possam invocar agentes.

**O que você aprenderá**:
- Configurar autorizadores JWT personalizados com Entra ID
- Usar MSAL (Microsoft Authentication Library) para fluxo de código de dispositivo
- Proteger endpoints do AgentCore Runtime com tokens bearer
- Gerenciar conversas baseadas em sessão com usuários autenticados

**Padrão de Integração Principal**:
- Usuários devem se autenticar com Entra ID antes de acessar endpoints de agentes do AgentCore Runtime
- Tokens bearer validam a identidade do usuário em cada requisição
- Agentes permanecem protegidos por trás da camada de autenticação


### 3. Step by Step Entra ID with AgentCore Gateway.ipynb

**Propósito**: Demonstra o uso do Entra ID para proteger endpoints do **AgentCore Gateway** com autenticação máquina-para-máquina (M2M) usando fluxo de credenciais de cliente.

**O que você aprenderá**:
- Configurar funções de aplicativo Entra ID para proteção de API
- Configurar AgentCore Gateway com autorização JWT personalizada
- Criar funções Lambda como ferramentas MCP (Model Context Protocol)
- Usar fluxo de credenciais de cliente para autenticação serviço-a-serviço

**Padrão de Integração Principal**:
- Aplicações se autenticam usando credenciais de cliente (sem interação do usuário)
- Gateway valida tokens JWT com Entra ID
- Funções Lambda expostas como ferramentas MCP padronizadas



## Suporte e Documentação

- [Documentação Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/)
- [Documentação Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/)
- [Especificação OAuth 2.0](https://oauth.net/2/)

## Nota

Microsoft Entra ID não é um serviço AWS. Consulte a documentação do Microsoft Entra ID para custos e licenciamento relacionados ao uso do Entra ID.
