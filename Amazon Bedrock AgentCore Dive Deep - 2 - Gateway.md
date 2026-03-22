# 🎯 Amazon Bedrock AgentCore Gateway — Guia de Apresentação

> **Apresentador:** Daniel ABIB — Specialist Solutions Architect, Amazon Bedrock  
> **Formato:** Dive Deep técnico com laboratórios práticos  
> **Duração estimada:** ~60 minutos (ajustar conforme audiência)

---

## Slide 1 — Título: Scale Agent Tools with Amazon Bedrock AgentCore Gateway

### 🎯 Mensagem Principal
Hoje vamos aprender como escalar de poucas ferramentas para milhares delas, distribuídas entre centenas de agentes — de forma segura, eficiente e sem dor de cabeça operacional.

### 📝 O que dizer
- Boas-vindas e agradecimento pela presença
- Perguntar para a audiência:
  - "Quantos de vocês estão desenvolvendo agentes de IA hoje?"
  - "Quantos já colocaram agentes em produção?"
  - "Quantos usam MCP como protocolo de ferramentas?"
- Criar engajamento inicial e mostrar que o tema é relevante para todos

---

## Slide 2 — Agentes e suas dependências / ferramentas

### 🎯 Mensagem Principal
Agentes de IA precisam acessar **dados reais** — APIs empresariais, datastores privados, bases de conhecimento. A mágica acontece na camada de integração agente-ferramenta. Escalar isso com segurança é **crítico** para o sucesso.

### 📝 O que dizer
- Explicar a arquitetura básica de um agente: LLMs + Orquestração + Memória + Ferramentas/Knowledge
- Em diversas indústrias (segurança, saúde, finanças), empresas estão transformando experiências com IA agentic
- O ponto-chave: seus agentes precisam acessar **seus dados**. Escalar isso com segurança é o desafio central

---

## Slide 3 — Customer Support Agent (POC)

### 🎯 Mensagem Principal
Começar com um agente simples é fácil. O problema aparece quando a **realidade de produção** bate na porta.

### 📝 O que dizer
- Cenário: agente de suporte ao cliente para varejo — usuários compram, perguntam, pedem ajuda
- POC funciona bem para algumas consultas — stakeholders ficam impressionados
- "Mas aí a realidade chega..."
- Criar expectativa para o problema de escalabilidade

---

## Slide 4 — Customer Support Agent + Ferramentas reais

### 🎯 Mensagem Principal
Quando você precisa integrar com dados reais (APIs empresariais, knowledge bases, outros agentes), a complexidade explode.

### 📝 O que dizer
- Seu agente precisa se conectar a: ferramentas de parceiros 3P, APIs empresariais, ferramentas MCP existentes, knowledge bases, outros agentes
- Cada integração adiciona complexidade

---

## Slide 5 — Ferramentas A, B, C (poucas ferramentas)

### 🎯 Mensagem Principal
Com poucas ferramentas (catálogo, inventário, status de pedido) é gerenciável. Mas até quando?

### 📝 O que dizer
- "Você começa com poucas ferramentas — catálogo de produtos, verificação de inventário, status de pedido. 20, 30 ferramentas. Ainda dá pra gerenciar, certo?"

---

## Slide 6 — Múltiplos agentes, múltiplas ferramentas

### 🎯 Mensagem Principal
O sucesso gera demanda. Múltiplos agentes × múltiplas ferramentas = **complexidade exponencial** (problema M×N).

### 📝 O que dizer
- Você adiciona shopping agent, pricing agent ao lado do customer support
- 50 ferramentas, depois 100. Este é o **problema M×N clássico**: múltiplos agentes, múltiplas ferramentas, complexidade exponencial
- Cada agente precisa saber como conectar a cada ferramenta

---

## Slide 7 — Model Context Protocol (MCP) como padrão

### 🎯 Mensagem Principal
MCP padroniza a comunicação, mas não resolve o problema de **gerenciamento e governança** de centenas de conexões.

### 📝 O que dizer
- MCP fornece uma forma padrão para agentes se comunicarem com ferramentas — ajuda com interoperabilidade
- **Ponto crítico**: mesmo com MCP, a arquitetura ainda é espalhada. Cada agente precisa gerenciar conexões com cada ferramenta
- O que você realmente precisa é um **ponto único de comunicação** entre agentes e ferramentas

---

## Slide 8 — Fine-Grained Access Control (exemplo de promoções)

### 🎯 Mensagem Principal
Com múltiplos usuários, tenants e agentes, você precisa de **controle de acesso granular dinâmico** ao nível da ferramenta.

### 📝 O que dizer
- Conforme sua base cresce — diferentes usuários, tenants, agentes — você precisa de controle de acesso dinâmico
- Exemplo: pricing agent acessa microserviço de promoções. Alguns usuários podem acessar preços promocionais, outros não
- Precisa enforçar isso **no nível da ferramenta**, não só no nível da aplicação

### 🔬 Lab Relacionado
> **[Lab 09 — Fine-Grained Access Control](01-tutorials/02-AgentCore-gateway/09-fine-grained-access-control/)** — Demonstra controle de acesso granular usando custom scopes e data stores com interceptors do Gateway

---

## Slide 9 — De Microserviços para Agent Tools

### 🎯 Mensagem Principal
Na prática, você "MCPifica" suas APIs existentes (shopping cart, catálogo, promoções) e rapidamente acumula centenas de MCP servers para gerenciar.

### 📝 O que dizer
- Fluxo real: objetivos agentic → tarefas → work backwards para encontrar seus microserviços existentes
- Você identifica as APIs certas para "MCPificar" — shopping cart, catálogo de produtos, gerenciamento de promoções
- Resultado: pode facilmente acabar com **centenas de MCP servers**
- Gerenciar toda essa infraestrutura? "Heavy lifting que ninguém pediu para fazer"

---

## Slide 10 — How do we scale?

### 🎯 Mensagem Principal
Como escalamos para centenas de agentes, milhares de ferramentas, centenas de MCP servers, centenas de milhares de tenants, com políticas de governança?

### 📝 O que dizer
- Esta é a **pergunta central** da apresentação
- Dimensões do desafio: AGENTS × TOOLS × POLICIES × TENANTS × API TYPES × MCP SERVERS
- Criar tensão para a solução que vem a seguir

---

## Slide 11 — Perguntas comuns para escalar ferramentas agentic

### 🎯 Mensagem Principal
A resposta é um **ponto único de comunicação** — um Gateway que elimina complexidade e dá MCPificação com um clique.

### 📝 O que dizer
- 4 pilares da solução: **Unified endpoint**, **Convert APIs into MCP tools**, **MCP server infrastructure**, **Unified governance**
- Arquitetura: múltiplos frameworks (LangGraph, Crew.ai, Strands SDK) conectando através de um **Tools Gateway**
- O Gateway cuida de: compliance MCP, transformação request/response, busca de ferramentas, autorização, métricas, logging, auditoria e controle de acesso granular
- "Este é o padrão Gateway. E é o que construímos."

---

## Slide 12 — Apresentação do AgentCore Gateway (título)

### 🎯 Mensagem Principal
Apresentar formalmente o **Amazon Bedrock AgentCore Gateway**.

### 📝 O que dizer
- "Deixem-me apresentar o Amazon Bedrock AgentCore Gateway"
- Momento de transição — da problemática para a solução

---

## Slide 13 — Bedrock AgentCore Gateway — Overview

### 🎯 Mensagem Principal
AgentCore Gateway é um AI Gateway construído do zero para comunicação segura de agentes em escala empresarial. É **serverless, pay-as-you-go**, sem infra para gerenciar.

### 📝 O que dizer
- Gateway purpose-built, construído do zero para escala massiva
- Se torna o **ponto único de comunicação** — conecta agentes a modelos, ferramentas, recursos, outros agentes
- Capacidades:
  - Criação e busca de ferramentas MCP
  - Autorização com AgentCore Identity
  - Controle de acesso granular e filtragem de ferramentas
  - Conectividade privada para deployments empresariais
  - Integração com CloudWatch para observabilidade
- **Completamente serverless e pay-as-you-go** — sem infra para gerenciar, sem autoscaling para configurar

---

## Slide 14 — AgentCore Gateway — Diagrama detalhado

### 🎯 Mensagem Principal
Visão técnica detalhada: agentes se conectam via MCP client ao Gateway, que roteia para diferentes tipos de targets com segurança completa.

### 📝 O que dizer
- Explicar o fluxo completo: Identity Provider → Inbound Auth → Gateway → Targets (OpenAPI, Smithy, Lambda, MCP Server) → Outbound Auth
- AgentCore Identity cuida do: token vault seguro, caching de tokens, workload identity
- Transporte: **streamable-http connection**
- CloudWatch integrado para observabilidade

### 🔬 Lab Relacionado
> **[Lab 06 — Gateway Observability](01-tutorials/02-AgentCore-gateway/06-gateway-observability/)** — Demonstra métricas, logs e traces via CloudWatch

---

## Slide 15 — MCPify your AWS Lambda

### 🎯 Mensagem Principal
Transforme suas funções Lambda existentes em ferramentas MCP seguras com o AgentCore Gateway — sem precisar reescrever código.

### 📝 O que dizer
- Fluxo: Agent → MCP Client → Gateway (com inbound auth) → Lambda Function
- Outbound auth via AWS IAM
- Cada função Lambda se torna uma ou mais ferramentas MCP acessíveis de qualquer agente

### 🔬 Lab Relacionado
> **[Lab 01 — Transform Lambda into MCP Tools](01-tutorials/02-AgentCore-gateway/01-transform-lambda-into-mcp-tools/)** — Dois notebooks:
> - `01-gateway-target-lambda-oauth.ipynb` — Lambda com OAuth inbound
> - `02-gateway-target-lambda-iam.ipynb` — Lambda com IAM inbound

---

## Slide 16 — MCPify your OpenAPI RESTful APIs

### 🎯 Mensagem Principal
Transforme suas APIs REST existentes (com spec OpenAPI) em ferramentas MCP, incluindo autenticação de saída via OAuth ou API Key.

### 📝 O que dizer
- Traga sua especificação OpenAPI (JSON/YAML) e o Gateway cria as ferramentas MCP automaticamente
- Suporte a **múltiplos métodos de outbound auth**: OAuth token, API Key, IAM
- Cada operação do OpenAPI vira uma ferramenta MCP individual

### 🔬 Lab Relacionado
> **[Lab 02 — Transform APIs into MCP Tools (OpenAPI)](01-tutorials/02-AgentCore-gateway/02-transform-apis-into-mcp-tools/01-transform-openapi-into-mcp-tools/)** — Dois notebooks:
> - `01-openapis-into-mcp-api-key.ipynb` — OpenAPI com API Key
> - `02-openapis-into-mcp-oauth-enterprise-apis.ipynb` — OpenAPI com OAuth (Zendesk)

---

## Slide 17 — MCPify your Smithy APIs

### 🎯 Mensagem Principal
Serviços AWS e APIs que usam Smithy também podem ser MCPificados — exemplo: operações do Amazon S3 como ferramentas MCP.

### 📝 O que dizer
- Modelos Smithy são usados extensivamente pela AWS
- Demonstração: APIs do S3 (ListBuckets, etc.) transformadas em ferramentas MCP
- Outbound auth via IAM

### 🔬 Lab Relacionado
> **[Lab 02 — Transform APIs into MCP Tools (Smithy)](01-tutorials/02-AgentCore-gateway/02-transform-apis-into-mcp-tools/02-transform-smithyapis-into-mcp-tools/)** — Notebook:
> - `01-s3-smithy-into-mcp-iam.ipynb` — APIs Smithy do S3 via IAM

---

## Slide 18 — Integrate your MCP Server with AgentCore Gateway

### 🎯 Mensagem Principal
Já tem um MCP server? Integre-o como target do Gateway para centralizar governança, autenticação e descoberta de ferramentas.

### 📝 O que dizer
- Para quem já tem MCP servers: não precisa reconstruir, basta registrar como target
- Gateway cuida de autenticação inbound e outbound
- Resolve o problema de: descoberta de ferramentas, gerenciamento de múltiplos gateways, complexidade de autenticação

### 🔬 Lab Relacionado
> **[Lab 05 — MCP Server as a Target](01-tutorials/02-AgentCore-gateway/05-mcp-server-as-a-target/)** — Dois notebooks:
> - `01-mcp-server-target.ipynb` — Integrar MCP Server
> - `02-mcp-target-synchronization.ipynb` — Sincronização de ferramentas

---

## Slide 19 — MCP Server Integration: LISTTOOLS

### 🎯 Mensagem Principal
O Gateway faz **cache do schema das ferramentas** e retorna resultados rápidos no ListTools sem precisar chamar o MCP server a cada vez.

### 📝 O que dizer
- Fluxo: MCP Client → Gateway → Cached tools schema
- Múltiplos targets (MCP server, OpenAPI, Lambda) — todos consultados de forma unificada
- Performance otimizada via cache

---

## Slide 20 — MCP Server Integration: INVOKETOOL

### 🎯 Mensagem Principal
Na invocação de ferramenta, o Gateway inicializa sessão com o MCP server, obtém credenciais do AgentCore Identity, e roteia a chamada.

### 📝 O que dizer
- Fluxo de 4 passos: (1) MCP client chama tool → (2) Gateway inicializa sessão com MCP server → (3) Obtém OAuth token do AgentCore Identity → (4) Chama a ferramenta com credenciais
- Transparente para o agente — ele só faz `tools/call`

---

## Slide 21 — MCP Server Integration: SEARCH TOOL

### 🎯 Mensagem Principal
A busca semântica retorna apenas as ferramentas relevantes de centenas disponíveis, **reduzindo contexto do LLM**.

### 📝 O que dizer
- Com centenas de ferramentas de múltiplos targets, a busca semântica filtra as mais relevantes
- Exemplo: busca "create a social media post" retorna top 10 ferramentas de 300+
- **Reduz contexto** passado ao LLM, melhorando precisão, velocidade e custo

### 🔬 Lab Relacionado
> **[Lab 03 — Search Tools](01-tutorials/02-AgentCore-gateway/03-search-tools/)** — Notebook:
> - `01-gateway-search.ipynb` — Busca semântica de ferramentas

---

## Slide 22 — Implicit Synchronization of MCP Server Tools

### 🎯 Mensagem Principal
Quando você cria ou atualiza um target MCP, o Gateway **sincroniza automaticamente** as definições de ferramentas.

### 📝 O que dizer
- CreateGatewayTarget e UpdateGatewayTarget disparam sincronização implícita
- Garante que targets em estado READY são imediatamente usáveis
- Processo: estabelece sessão → lista ferramentas em batches de 100 → normaliza com prefixos → atualiza cache

### 🔬 Lab Relacionado
> **[Lab 05 — MCP Target Synchronization](01-tutorials/02-AgentCore-gateway/05-mcp-server-as-a-target/02-mcp-target-synchronization.ipynb)** — Seção de sincronização implícita

---

## Slide 23 — Explicit Synchronization of MCP Server Tools

### 🎯 Mensagem Principal
Use a API `SynchronizeGateway` para sincronizar sob demanda quando seu MCP server adiciona novas ferramentas.

### 📝 O que dizer
- API SynchronizeGateway: operação assíncrona disparada pelo Ops Admin
- Útil após modificar configurações do MCP server
- Limite: 10.000 ferramentas por target

### 🔬 Lab Relacionado
> **[Lab 05 — MCP Target Synchronization](01-tutorials/02-AgentCore-gateway/05-mcp-server-as-a-target/02-mcp-target-synchronization.ipynb)** — Seção de sincronização explícita

---

## Slide 24 — Integrate Amazon API Gateway (Control Plane)

### 🎯 Mensagem Principal
Agora você pode registrar endpoints do Amazon API Gateway como targets, com filtragem de paths e métodos.

### 📝 O que dizer
- Operações de controle: Create/Update Target com REST API Id & Stage
- **Tools Filters**: filtrar por path e métodos HTTP (ex: só GET /order)
- Tipos de auth suportados: No auth, IAM, API Key
- Sincronização automática quando a API muda

### 🔬 Lab Relacionado
> **[Lab 11 — API Gateway as a Target](01-tutorials/02-AgentCore-gateway/11-api-gateway-as-a-target/)** — Notebook:
> - `01-api-gateway-target.ipynb` — PetStore API via API Gateway

---

## Slide 25 — Integrate Amazon API Gateway (Data Plane)

### 🎯 Mensagem Principal
No plano de dados, agentes MCP e clientes REST tradicionais acessam o mesmo endpoint — o Gateway roteia adequadamente.

### 📝 O que dizer
- MCP clients usam `/mcp` para ListTools, InvokeTools
- Microservice clients tradicionais continuam fazendo chamadas RESTful diretas
- Outbound auth flexível: API Key, IAM, ou sem auth

---

## Slide 26 — AgentCore Gateway Semantic Search (detalhado)

### 🎯 Mensagem Principal
A busca semântica é **automática e serverless** — indexa ferramentas automaticamente e retorna top 10 resultados relevantes.

### 📝 O que dizer
- **Sem busca**: 300+ ferramentas vão para o contexto do LLM → lento, caro, impreciso
- **Com busca**: retorna top 10 mais relevantes → contexto reduzido, maior precisão
- O Gateway indexa automaticamente. Ferramenta de busca: `x-amz-bedrock-agentcore-search`
- Habilitado durante CreateGateway

### 🔬 Lab Relacionado
> **[Lab 03 — Search Tools](01-tutorials/02-AgentCore-gateway/03-search-tools/)** — Demonstração completa de busca semântica

---

## Slide 27 — Secure Private Inbound Connectivity (PrivateLink)

### 🎯 Mensagem Principal
Para workloads sensíveis, o Gateway suporta **AWS PrivateLink** — tráfego nunca sai da rede AWS.

### 📝 O que dizer
- Interface VPC Endpoint usando PrivateLink: ENIs com IPs privados
- Tráfego do MCP client resolve para IP privado — sem internet gateway, NAT, VPN
- PrivateLink restringe todo tráfego à rede Amazon
- Suporte a Direct Connect para on-premises

---

## Slide 28 — AgentCore Gateway Integration (visão geral)

### 🎯 Mensagem Principal
O Gateway é o ponto unificado que integra agentes com todo o ecossistema — MCP servers públicos/privados, Lambda, OpenAPIs, API Gateway, outros agentes, e AgentCore Runtime.

### 📝 O que dizer
- Múltiplos frameworks de agentes (LangGraph, Strands) se conectam ao Gateway
- Targets diversos: Lambda, OpenAPIs, MCP servers públicos e privados, API Gateway
- Integração bidirecional com AgentCore Runtime

### 🔬 Lab Relacionado
> **[Lab 04 — Integration (Runtime + Gateway)](01-tutorials/02-AgentCore-gateway/04-integration/)** — Vários notebooks de integração:
> - `04-runtime-gateway.ipynb` — Runtime com Gateway
> - `01_vscode_agentcore_confluence_serverless.ipynb` — VS Code + Gateway

---

## Slide 29 — Ingress Authorization: OAuth

### 🎯 Mensagem Principal
Autorização de entrada via OAuth 2.1 — o Gateway valida tokens JWT contra o servidor de autorização configurado.

### 📝 O que dizer
- Fluxo: (1) MCP client envia token → (2) Gateway faz discovery do OAuth server → (3) Verifica token → (4) Permite/nega acesso
- Configurado via CreateGateway: audience, subject claim, issuer
- OAuth 2.1 com suporte a authcode e client credentials grant type
- Logs de auditoria via CloudTrail

---

## Slide 30 — Ingress Authorization: IAM

### 🎯 Mensagem Principal
Para cenários AWS-native, autorização de entrada via **IAM + SigV4** — sem necessidade de IdP externo.

### 📝 O que dizer
- MCP client envia requisição assinada com SigV4
- Gateway autoriza contra `bedrock-agentcore:InvokeGateway` e o ARN do Gateway
- Simples e direto para workloads AWS-native

---

## Slide 31 — Ingress Authorization: No Auth

### 🎯 Mensagem Principal
Para cenários de desenvolvimento/teste, suporte a **sem autenticação** (No Auth).

### 📝 O que dizer
- Útil para POCs e testes rápidos
- MCP client chama diretamente sem token
- **Não recomendado para produção**

---

## Slide 32 — Egress Authorization Flow

### 🎯 Mensagem Principal
Autorização de saída: o Gateway obtém credenciais do **credentials provider** para acessar APIs de backend em nome do usuário.

### 📝 O que dizer
- Fluxo: (1) Credentials provider configurado → (2) Token/API Key obtido → (3) MCP client chama Gateway → (4) Gateway faz outbound request com credenciais
- Tipos: OAuth token, API Key, IAM role

### 🔬 Lab Relacionado
> **[Lab 13 — Outbound Auth Code Grant](01-tutorials/02-AgentCore-gateway/13-outbound-auth-code-grant/)** — Notebook:
> - `01-outbound-auth-code-grant-linkedin.ipynb` — Auth code grant com LinkedIn

---

## Slide 33 — Authorization completa (Inbound + Outbound + Identity)

### 🎯 Mensagem Principal
O fluxo completo de autorização: inbound OAuth → workload identity → token exchange → outbound OAuth. Tudo gerenciado pelo AgentCore Identity.

### 📝 O que dizer
- 4 passos: (1) Recuperar workload identity → (2) Autorizar token OAuth inbound → (3) Obter workload access token → (4) Trocar por outbound token
- AgentCore Identity gerencia: token vault, caching, workload identity, OAuth2 authorizer, resource credentials provider
- Token exchange transparente para o agente

### 🔬 Lab Relacionado
> **[Lab 14 — Token Exchange at Request Interceptor](01-tutorials/02-AgentCore-gateway/14-token-exchange-at-request-interceptor/)** — Demonstra token exchange via interceptor

---

## Slide 34 — Exemplo de Inbound Authorization Flow (detalhe MCP spec)

### 🎯 Mensagem Principal
O Gateway implementa a **especificação de autorização MCP** completa — discovery automático do authorization server.

### 📝 O que dizer
- Fluxo completo conforme spec MCP: (1) Request sem token → 401 → (2) Discovery do resource server → (3) Discovery do authorization server → (4) OAuth flow → (5) Token → (6) Request com token → Resposta
- Em conformidade com a especificação oficial MCP Authorization

---

## Slide 35 — Credentials Provider para Outbound Auth

### 🎯 Mensagem Principal
Tabela de referência dos tipos de credentials provider disponíveis para cada tipo de target.

### 📝 O que dizer
- **OAuth**: para OpenAPI e Smithy targets — scopes configuráveis
- **API Keys**: para OpenAPI e Smithy — via header ou query parameter
- **IAM Role**: para Lambda targets — usa a role do Gateway
- Cada target pode ter seu próprio credentials provider

---

## Slide 36 — Gateway Security (visão completa)

### 🎯 Mensagem Principal
Visão completa da segurança: inbound auth (3LO/IAM) → Gateway → múltiplos targets com outbound auth individual.

### 📝 O que dizer
- Exemplo real: targets Zendesk, Genesys, Google APIs, Enterprise APIs, Smithy APIs, Lambda
- Cada target com seu tipo de outbound auth (OAuth, API Key, IAM)
- Segurança end-to-end desde o usuário até o backend

---

## Slide 37 — Gateway Interceptors (conceito)

### 🎯 Mensagem Principal
**Interceptors** são o mecanismo de extensibilidade do Gateway — permitem customizar o comportamento em request e response.

### 📝 O que dizer
- Casos de uso: Fine-grained access control, Schema translation, Custom header propagation, Per-tool authorization, Tenant isolation, Data sanitization
- Interceptors são **funções Lambda** que processam request/response

### 🔬 Labs Relacionados
> - **[Lab 07 — Bearer Token Injection](01-tutorials/02-AgentCore-gateway/07-bearer-token-injection/)** — Injeção de token no request
> - **[Lab 08 — Custom Header Propagation](01-tutorials/02-AgentCore-gateway/08-custom-header-propagation/)** — Propagação de headers
> - **[Lab 10 — Sensitive Data Masking](01-tutorials/02-AgentCore-gateway/10-sensitive-data-masking/)** — Mascaramento de dados
> - **[Lab 15 — Prevent SQL Injection](01-tutorials/02-AgentCore-gateway/15-prevent-sql-injection/)** — Prevenção de SQL injection

---

## Slide 38 — Gateway Interceptors: Execution Workflow

### 🎯 Mensagem Principal
Fluxo completo: Request → Request Interceptor → Target → Response → Response Interceptor → Cliente. Interceptors podem **transformar** tanto request quanto response.

### 📝 O que dizer
- 8 passos: (1) JWT inbound → (2) Request interceptor Lambda → (3) Transformed request → (4) Call target → (5) Original response → (6) Response interceptor → (7) Transformed response → (8) Retorno ao cliente
- Interceptors têm acesso ao JWT, body, headers

---

## Slide 39 — Fine-Grained Access Control: CALL TOOL

### 🎯 Mensagem Principal
Request interceptor extrai JWT, consulta mapeamento de permissões e **permite ou nega** invocação de ferramenta dinamicamente.

### 📝 O que dizer
- Interceptor: extrai JWT → obtém mapeamento Agent/User/Tenant → Tools → permite/nega
- Pode também **modificar o request** antes de chegar ao target
- Decisão é por ferramenta individual, não por target inteiro

### 🔬 Lab Relacionado
> **[Lab 09 — Fine-Grained Access Control](01-tutorials/02-AgentCore-gateway/09-fine-grained-access-control/)** — Dois notebooks:
> - `01-fine-grained-access-control-using-custom-scopes.ipynb` — Usando custom scopes
> - `02-fine-grained-access-control-using-data-store.ipynb` — Usando DynamoDB como data store

---

## Slide 40 — Fine-Grained Access Control: Fluxo No Auth + OAuth

### 🎯 Mensagem Principal
Arquitetura avançada: ListTools sem auth (para discovery) + CallTool com OAuth (para execução segura), ambos com interceptor de validação.

### 📝 O que dizer
- ListTools pode funcionar sem auth (discovery público)
- CallTool requer JWT — interceptor valida e permite/nega
- Combinar os dois fluxos dá flexibilidade máxima

---

## Slide 41 — Tools Filtering: LIST TOOLS

### 🎯 Mensagem Principal
Response interceptor filtra a **lista de ferramentas** baseado em permissões — cada usuário/tenant vê apenas as ferramentas autorizadas.

### 📝 O que dizer
- Interceptor de resposta: extrai JWT → consulta mapeamento de permissões → filtra ListTools response
- Resultado: cada agente/usuário/tenant recebe apenas ferramentas que tem permissão de usar

---

## Slide 42 — Tools Filtering: SEARCH TOOL

### 🎯 Mensagem Principal
A mesma filtragem funciona para busca semântica — resultados são filtrados por permissões.

### 📝 O que dizer
- Response interceptor filtra resultados de search tools baseado em permissões
- Combina busca semântica com controle de acesso

---

## Slide 43 — Header Propagation & Schema Translation

### 🎯 Mensagem Principal
Interceptors permitem propagar headers customizados, gerar novos tokens (PAT) e transformar schemas de request.

### 📝 O que dizer
- Cenário: extrair JWT → consultar registry de políticas → criar novo Personal Access Token → modificar body do request
- Integração com AgentCore Policy Engine para governança
- Headers customizados propagados para o target

### 🔬 Labs Relacionados
> - **[Lab 08 — Custom Header Propagation](01-tutorials/02-AgentCore-gateway/08-custom-header-propagation/)** — Propagação de headers e query parameters
> - **[Lab 14 — Token Exchange](01-tutorials/02-AgentCore-gateway/14-token-exchange-at-request-interceptor/)** — Token exchange via interceptor

---

## Slide 44 — Schema Support Agent

### 🎯 Mensagem Principal
Um agente inteligente que analisa, valida e **repara automaticamente** specs OpenAPI para compatibilidade com o Gateway.

### 📝 O que dizer
- Agente com ferramentas especializadas: Static Validator (Spectral linter), Schema Editor, Code Interpreter
- Abordagem iterativa: modificações precisas preservando a integridade semântica da API
- Suporte a Swagger 2.0 e OpenAPI 3.0
- Open source: Schema repair interativo

---

## Slide 45 — Private Connectivity to AgentCore Gateway (título)

### 🎯 Mensagem Principal
Seção dedicada a conectividade privada — essencial para clientes em indústrias regulamentadas.

### 📝 O que dizer
- Transição para tópico de segurança de rede
- "Para workloads sensíveis em finanças, saúde — compliance com HIPAA, PCI DSS, EU-US Data Privacy Framework"
- Opções: subnets isoladas, sem acesso à internet, ou conectividade privada on-premises

---

## Slide 46 — Endpoints: Control Plane vs Data Plane

### 🎯 Mensagem Principal
O Gateway tem dois endpoints: **Control Plane** (criação/gestão) e **Data Plane** (invocação de ferramentas).

### 📝 O que dizer
- Control Plane: `bedrock-agentcore-control.{region}.amazonaws.com` — CreateGateway, CreateGatewayTarget, etc.
- Data Plane: `{gateway-Id}.gateway.bedrock-agentcore.{region}.amazonaws.com` — ListTools, InvokeTool
- **Nota importante**: VPC endpoints ainda não suportam o control plane endpoint

---

## Slides 47-51 — Conectividade Privada (detalhes)

### 🎯 Mensagem Principal
Detalhamento técnico de como configurar PrivateLink: ENIs, Direct Connect, VPC endpoint policies.

### 📝 O que dizer
- **Slide 47 (Client Connectivity)**: MCP client na VPC + client on-premises
- **Slide 48 (Public routing)**: Por padrão resolve IP público, tráfego via backbone AWS — bom mas não privado
- **Slide 49 (Pergunta)**: Como tornamos isso privado?
- **Slide 50 (PrivateLink)**: Resposta! PrivateLink estabelece conexão privada — tráfego isolado da internet pública
- **Slide 51 (Detalhe)**: ENI local na VPC, Gateway resolve para IP privado, sem NAT gateway necessário
- **Múltiplas VPCs**: mesmo VPC endpoint para múltiplos Gateways; VPC endpoint separado por VPC
- **On-premises**: Direct Connect VIF ou Site-to-Site VPN
- **Data perimeter**: VPC endpoint policies como guardrails para identidades e recursos confiáveis

---

## Slide 52 — Resources

### 🎯 Mensagem Principal
Recursos disponíveis: code samples e schema repair agent.

### 📝 O que dizer
- Apontar para os repositórios de exemplos
- Schema repair agent open source

### 🔬 Lab Relacionado — Todos os Labs!
> **[Repositório completo de laboratórios](01-tutorials/02-AgentCore-gateway/)** — 15 pastas de tutoriais cobrindo todos os cenários do Gateway

---

## Slide 53 — Melhores Práticas (se existir)

### 🎯 Mensagem Principal
Regras de ouro para escalar ferramentas agentic em produção.

### 📝 O que dizer — Ferramentas
- Comece pelo caso de uso de negócio. Work backwards para identificar as APIs certas
- **Não converta todas as APIs** — consolide em menos ferramentas direcionadas
- Princípio: **Crawl → Walk → Run** — comece com poucas ferramentas e escale gradualmente
- Verifique quão bem os agentes usam suas ferramentas — feedback loops importam
- Reduza contexto do LLM — use busca semântica, namespace suas ferramentas
- Retorne contexto legível por humanos — não IDs de baixo nível. Use paginação e truncamento

### 📝 O que dizer — Segurança e Governança
- Automatize scanning de segurança antes do onboarding
- Agentes são autônomos e não-determinísticos → use identity propagation act-on-behalf
- Pen testing contínuo
- Fine-grained access control via **Gateway interceptors**
- Use interceptors para **isolamento de tenant**
- Construa um **registry empresarial** de agentes e ferramentas como fonte única de verdade
- Analytics de uso e scanning de segurança no workflow de deployment

### 🔬 Lab Relacionado
> **[Lab 12 — Agents as Tools using MCP](01-tutorials/02-AgentCore-gateway/12-agents-as-tools-using-mcp/)** — 6 Labs completos de multi-agent orchestration com todas as boas práticas

---

# 📋 Mapeamento Rápido: Slides → Labs

| Slide(s) | Tópico | Lab |
|-----------|--------|-----|
| 15 | MCPify Lambda | [01-transform-lambda-into-mcp-tools](01-tutorials/02-AgentCore-gateway/01-transform-lambda-into-mcp-tools/) |
| 16 | MCPify OpenAPI | [02-transform-apis (OpenAPI)](01-tutorials/02-AgentCore-gateway/02-transform-apis-into-mcp-tools/01-transform-openapi-into-mcp-tools/) |
| 17 | MCPify Smithy | [02-transform-apis (Smithy)](01-tutorials/02-AgentCore-gateway/02-transform-apis-into-mcp-tools/02-transform-smithyapis-into-mcp-tools/) |
| 18-23 | MCP Server Target | [05-mcp-server-as-a-target](01-tutorials/02-AgentCore-gateway/05-mcp-server-as-a-target/) |
| 21, 26 | Busca Semântica | [03-search-tools](01-tutorials/02-AgentCore-gateway/03-search-tools/) |
| 24-25 | API Gateway Target | [11-api-gateway-as-a-target](01-tutorials/02-AgentCore-gateway/11-api-gateway-as-a-target/) |
| 28 | Integração Runtime | [04-integration](01-tutorials/02-AgentCore-gateway/04-integration/) |
| 14 | Observabilidade | [06-gateway-observability](01-tutorials/02-AgentCore-gateway/06-gateway-observability/) |
| 37-38 | Bearer Token | [07-bearer-token-injection](01-tutorials/02-AgentCore-gateway/07-bearer-token-injection/) |
| 43 | Header Propagation | [08-custom-header-propagation](01-tutorials/02-AgentCore-gateway/08-custom-header-propagation/) |
| 8, 39-42 | FGAC | [09-fine-grained-access-control](01-tutorials/02-AgentCore-gateway/09-fine-grained-access-control/) |
| 37 | Data Masking | [10-sensitive-data-masking](01-tutorials/02-AgentCore-gateway/10-sensitive-data-masking/) |
| 53 | Multi-Agent | [12-agents-as-tools-using-mcp](01-tutorials/02-AgentCore-gateway/12-agents-as-tools-using-mcp/) |
| 32 | Outbound Auth 3LO | [13-outbound-auth-code-grant](01-tutorials/02-AgentCore-gateway/13-outbound-auth-code-grant/) |
| 33 | Token Exchange | [14-token-exchange-at-request-interceptor](01-tutorials/02-AgentCore-gateway/14-token-exchange-at-request-interceptor/) |
| 37 | SQL Injection | [15-prevent-sql-injection](01-tutorials/02-AgentCore-gateway/15-prevent-sql-injection/) |
