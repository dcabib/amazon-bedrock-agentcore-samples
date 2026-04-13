# Amazon Bedrock AgentCore Browser Tool

## Visão Geral

Amazon Bedrock AgentCore Browser Tool fornece aos agentes de IA uma maneira segura e totalmente gerenciada de interagir com sites como humanos fazem. Permite que os agentes naveguem em páginas web, preencham formulários e completem tarefas complexas sem exigir que os desenvolvedores escrevam e mantenham scripts de automação personalizados.

## Como Funciona

Um sandbox de ferramenta de navegador é um ambiente de execução seguro que permite aos agentes de IA interagir com segurança com navegadores web. Quando um usuário faz uma solicitação, o Large Language Model (LLM) seleciona as ferramentas apropriadas e traduz os comandos. Esses comandos são executados dentro de um ambiente sandbox controlado contendo um navegador headless e servidor de biblioteca hospedado (usando ferramentas como Playwright). O sandbox fornece isolamento e segurança ao conter as interações web dentro de um espaço restrito, prevenindo acesso não autorizado ao sistema. O agente recebe feedback através de capturas de tela e pode realizar tarefas automatizadas mantendo a segurança do sistema. Esta configuração permite automação web segura para agentes de IA.

![architecture local](images/browser-tool.png)

## Principais Recursos

### Interação Web Segura e Gerenciada

Fornece aos agentes de IA uma maneira segura e totalmente gerenciada de interagir com sites como humanos fazem, permitindo navegação, preenchimento de formulários e conclusão de tarefas complexas sem exigir scripts de automação personalizados.

### Recursos de Segurança Empresarial

Fornece isolamento em nível de VM com mapeamento 1:1 entre sessão de usuário e sessão de navegador, oferecendo segurança de nível empresarial. Cada sessão de navegador é executada em um ambiente sandbox isolado para atender às necessidades de segurança empresarial.

### Integração Agnóstica de Modelo

Suporta vários modelos de IA e frameworks, fornecendo abstrações de linguagem natural para ações do navegador através de ferramentas como interact(), parse() e discover(), tornando-o particularmente adequado para ambientes empresariais. A ferramenta pode executar comandos de navegador de qualquer biblioteca e suporta vários frameworks de automação como Playwright e Puppeteer.

### Integração

Amazon Bedrock AgentCore Browser Tool se integra com outras capacidades do Amazon Bedrock AgentCore através de um SDK unificado, incluindo:

- Amazon Bedrock AgentCore Runtime
- Amazon Bedrock AgentCore Identity
- Amazon Bedrock AgentCore Memory
- Amazon Bedrock AgentCore Observability

Esta integração visa simplificar o processo de desenvolvimento e fornecer uma plataforma abrangente para construir, implantar e gerenciar agentes de IA, com capacidades poderosas para realizar tarefas baseadas em navegador.

### Casos de Uso

O Amazon Bedrock AgentCore Browser Tool é adequado para uma ampla gama de aplicações, incluindo:

- Navegação e Interação Web
- Automação de Workflow incluindo preenchimento de formulários

## Visão Geral dos Tutoriais

Estes tutoriais demonstram as capacidades do AgentCore Browser Tool em diferentes frameworks e configurações:

### Primeiros Passos

**Exemplos Browser Use**
- [Primeiros Passos com Bedrock AgentCore Browser Tool e Browser Use](02-browser-with-browserUse/getting_started-agentcore-browser-tool-with-browser-use.ipynb)
- [Amazon Bedrock AgentCore Browser Tool Live View e Browser Use](02-browser-with-browserUse/agentcore-browser-tool-live-view-with-browser-use.ipynb)

**Exemplos Nova Act**
- [Primeiros Passos com Bedrock AgentCore Browser Tool e Nova Act](01-browser-with-NovaAct/01_getting_started-agentcore-browser-tool-with-nova-act.ipynb)
- [Amazon Bedrock AgentCore Browser Tool Live View e Nova Act](01-browser-with-NovaAct/02_agentcore-browser-tool-live-view-with-nova-act.ipynb)

**Exemplos Strands**
- [Primeiros Passos com Bedrock AgentCore Browser Tool e Strands](04-browser-with-Strands/01_getting_started-agentcore-browser-tool-with-strands.ipynb)

### Recursos Avançados

**Observabilidade**
- [Amazon Bedrock AgentCore Browser Tool Observability](03-browser-observability/01_browser_observability.ipynb)

**Live View**
- [Amazon Bedrock AgentCore Browser Tool DCV Live View](05-browser-live-view/01-embed-dcv-live-view-tutorial.ipynb)

**Autenticação de Web Bot**
- [Amazon Bedrock AgentCore Browser Tool Web Bot Auth](06-Web-Bot-Auth-Signing/01_agentcore-browser-tool-with-web-bot-auth.ipynb)

### Integração VPC

**Configuração VPC**
- [Conectando Navegador Público de VPC Privada](07-connecting-public-browser-from-private-vpc/01-connecting-public-browser-from-private-vpc-runtime.ipynb)
- [Interagindo com Navegador Baseado em VPC de VPC](08-Interacting-with-vpc-based-browser-from-vpc/01-Interacting-with-vpc-based-browser-from-vpc.ipynb)

### Segurança e Configuração

**Filtragem de Domínio**
- [Navegador com Filtragem de Domínio usando AWS Network Firewall](09-browser-with-domain-filtering/) - Implanta AgentCore Browser em uma VPC com Network Firewall para filtragem de domínio com allowlist/denylist

**Roteamento de Proxy**
- [Navegador com Squid Proxy](11-browser-with-proxy/) - Roteia o tráfego do navegador através de um proxy Squid autenticado com logs de acesso enviados para S3

**Perfis de Navegador**
- [Navegador com Perfis Persistentes](10-browser-with-profile/browser-profile.ipynb) - Persiste e reutiliza dados de sessão do navegador (cookies, armazenamento local) em múltiplas sessões
