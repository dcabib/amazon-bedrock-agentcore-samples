# Amazon Bedrock AgentCore Identity

## Visão Geral

Amazon Bedrock AgentCore Identity é um serviço abrangente de gerenciamento de identidade e credenciais projetado especificamente para agentes de IA e cargas de trabalho automatizadas. Ele fornece recursos seguros de autenticação, autorização e gerenciamento de credenciais que permitem aos usuários invocar agentes, e aos agentes acessar recursos e serviços externos em nome dos usuários, mantendo controles de segurança rigorosos e trilhas de auditoria.

AgentCore Identity aborda um desafio fundamental na implantação de agentes de IA: permitir que os agentes acessem com segurança dados específicos do usuário em múltiplos serviços sem comprometer a segurança ou a experiência do usuário. O serviço opera no princípio de **delegação em vez de representação**, onde os agentes se autenticam como eles mesmos enquanto carregam contexto verificável do usuário.

## Principais Funcionalidades

- **Autenticação de Entrada (Inbound)**: Valida acesso para usuários e aplicações que chamam agentes ou ferramentas
- **Autenticação de Saída (Outbound)**: Acesso seguro de agentes a serviços externos em nome dos usuários
- **Integração OAuth**: Suporte para fluxos OAuth de 2 e 3 etapas
- **Integração com AWS IAM**: Integração nativa com gerenciamento de identidade e acesso da AWS
- **Segurança Zero Trust**: Toda requisição é validada independentemente da origem ou relacionamentos de confiança anteriores
- **Suporte Multiplataforma**: Funciona na AWS, outros provedores de nuvem e ambientes on-premise

## Tipos de Autenticação

AgentCore Identity suporta dois padrões principais de autenticação:

### Autenticação de Entrada (Inbound Auth)
Valida acesso para usuários e aplicações que chamam agentes ou ferramentas em alvos AgentCore Runtime ou Gateway. Suporta:
- **AWS IAM**: Controle de acesso baseado em IAM direto
- **OAuth**: Autenticação baseada em token sem exigir permissões IAM para usuários finais

### Autenticação de Saída (Outbound Auth)
Permite que agentes acessem serviços AWS e recursos externos em nome dos usuários:
- **Recursos AWS**: Usa roles de execução IAM para acesso a serviços AWS
- **Serviços Externos**: Fluxos OAuth de 2 etapas (credenciais do cliente) e 3 etapas (código de autorização)


![Authentication Basics](images/auth_basics3.png)

## Como Funciona

AgentCore Identity implementa um fluxo de trabalho abrangente que orquestra autenticação e autorização em múltiplos domínios de confiança:

1. **Autenticação do Usuário**: Usuários se autenticam através de provedores de identidade existentes (Cognito, Auth0, etc.)
2. **Autorização do Agente**: Aplicações solicitam acesso ao agente com tokens de usuário
3. **Troca de Tokens**: AgentCore Identity valida tokens de usuário e emite tokens de acesso de carga de trabalho
4. **Acesso a Recursos**: Agentes usam tokens de carga de trabalho para acessar recursos AWS e de terceiros
5. **Delegação e Auditoria**: Todas as ações mantêm contexto do usuário e trilhas de auditoria

![How It Works](images/how_it_works.png)

Para informações técnicas detalhadas, veja [Como AgentCore Identity Funciona](02-how_it_works-pt.md).

## Exemplos de Tutorial

Este tutorial inclui quatro exemplos práticos demonstrando diferentes cenários de autenticação:

| Exemplo | Tipo | Descrição |
|---------|------|-----------|
| **[Exemplo de Inbound Auth](03-Inbound%20Auth%20example)** | Inbound | Autenticação de usuário com agentes Strands e modelos Bedrock |
| **[Exemplo de Outbound Auth](04-Outbound%20Auth%20example)** | Outbound | Acesso de agente a serviços externos com Strands e OpenAI |
| **[OAuth 3 Etapas](05-Outbound_Auth_3lo)** | Outbound | Acesso delegado pelo usuário com Cognito e fluxo OAuth 3 etapas com Google |
| **[Integração GitHub](06-Outbound_Auth_Github)** | Outbound | Acesso à API do GitHub usando autenticação OAuth 3 etapas |

Cada exemplo inclui:
- Passo a passo completo em notebook Jupyter
- Instruções de configuração detalhadas
- Exemplos de código e explicações
- Melhores práticas e considerações de segurança

## Começando

1. **Leia a Introdução**: Comece com [Começando](01-getting_started-pt.md) para entender os conceitos do AgentCore Identity
2. **Entenda o Fluxo de Trabalho**: Revise [Como Funciona](02-how_it_works-pt.md) para detalhes técnicos
3. **Escolha um Exemplo**: Selecione um exemplo de tutorial baseado em suas necessidades de autenticação:
   - Para autenticação de usuário em agentes: Comece com **Exemplo de Inbound Auth**
   - Para acesso de agente a serviços externos: Experimente **Exemplo de Outbound Auth**
   - Para padrões de acesso delegado pelo usuário: Explore **OAuth 3 Etapas** ou **Integração GitHub**

## Principais Benefícios

- **Segurança Aprimorada**: Autenticação zero trust com controles de acesso granulares
- **Experiência do Usuário**: Acesso contínuo sem solicitações repetidas de autenticação
- **Auditoria e Conformidade**: Trilhas de auditoria completas para todas as ações do agente
- **Agnóstico de Framework**: Funciona com qualquer framework de agente (Strands, LangGraph, CrewAI, etc.)
- **Escalável**: Pronto para empresas com suporte a múltiplos provedores de identidade
- **Baseado em Padrões**: Construído sobre OAuth 2.0, OIDC e padrões de segurança da indústria

## Integração de Arquitetura

AgentCore Identity se integra perfeitamente com outros componentes AgentCore:

- **AgentCore Runtime**: Fornece autenticação para agentes hospedados
- **AgentCore Gateway**: Protege acesso a ferramentas e APIs externas
- **AgentCore Memory**: Mantém acesso seguro a armazenamentos de memória específicos do usuário
- **Serviços de Terceiros**: Permite integração segura com APIs e serviços externos

## Próximos Passos

Após completar os tutoriais, você pode:
- Integrar AgentCore Identity com sua infraestrutura de identidade existente
- Configurar provedores OAuth personalizados e escopos
- Implementar políticas de segurança avançadas e controles de acesso
- Implantar fluxos de autenticação de agente prontos para produção
- Escalar sua infraestrutura de agentes segura em múltiplos serviços e plataformas
