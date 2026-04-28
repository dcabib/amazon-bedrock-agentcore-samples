# AgentCore Policy - Demonstração Introdutória

Uma demonstração prática completa de implementação de controles baseados em políticas para agentes de IA usando Amazon Bedrock AgentCore Policy.

## 🚀 Início Rápido

1. **Instalar dependências**: `pip install -r requirements.txt`
2. **Abrir notebook**: `jupyter notebook AgentCore-Policy-Demo-pt.ipynb`
3. **Seguir os passos** no notebook

> **Nota**: Requer boto3 versão 1.42.0 ou superior para suporte nativo à API policy-registry.

## Visão Geral

Esta demonstração fornece um passo a passo completo de implementação de controles baseados em políticas para interações de agentes de IA com ferramentas através do AgentCore Gateway.

## O Que Você Vai Aprender

- ✅ Implantar funções Lambda como ferramentas de agente
- ✅ Configurar AgentCore Gateway com múltiplos alvos Lambda
- ✅ Criar e configurar Policy Engines
- ✅ Escrever políticas Cedar para controle de acesso refinado
- ✅ Testar enforcement de políticas com requisições reais de agentes de IA
- ✅ Entender cenários ALLOW e DENY

## Cenário da Demonstração

Vamos construir um **sistema de processamento de subscrição de seguros** com controles de política:

- **Ferramentas**: 
  - **ApplicationTool** - Cria aplicações de seguro com validação geográfica e de elegibilidade
    - Parâmetros: `applicant_region` (string), `coverage_amount` (integer)
  - **RiskModelTool** - Invoca modelo externo de pontuação de risco com controles de governança
    - Parâmetros: `API_classification` (string), `data_governance_approval` (boolean)
  - **ApprovalTool** - Aprova decisões de subscrição de alto valor ou alto risco
    - Parâmetros: `claim_amount` (integer), `risk_level` (string)

- **Regra de Política**: Permitir apenas aplicações de seguro com cobertura abaixo de $1M
- **Casos de Teste**: 
  - ✅ Aplicação de $750K (PERMITIDA)
  - ❌ Aplicação de $1.5M (NEGADA)

> **Importante**: Políticas só podem referenciar parâmetros definidos no schema do alvo Gateway. Cada ferramenta tem seu próprio schema com parâmetros específicos que podem ser usados em condições de política.

## Pré-requisitos

Antes de começar, certifique-se de ter:

- AWS CLI configurado com credenciais apropriadas
- Python 3.10+ com boto3 1.42.0+ instalado
- Pacote `bedrock_agentcore_starter_toolkit` instalado
- Pacote `strands` instalado (para funcionalidade de agente de IA)
- Acesso ao AWS Lambda (para criar funções alvo)
- Acesso ao Amazon Bedrock (para modelo de agente de IA)
- Trabalhando na região **us-east-1 (N.Virginia)**

> **Nota**: O script de configuração do gateway criará automaticamente as funções IAM necessárias com políticas de confiança adequadas para o serviço AgentCore.

## Instruções de Configuração

### 1. Instalar Dependências

```bash
pip install -r requirements.txt
```

**Importante**: Certifique-se de que boto3 versão 1.42.0 ou superior está instalado:

```bash
pip install --upgrade boto3
```

### 2. Abrir o Notebook de Demonstração

```bash
jupyter notebook AgentCore-Policy-Demo-pt.ipynb
```

### 3. Seguir o Notebook

O notebook guia você através de:

1. **Configuração de Ambiente** - Verificar credenciais e dependências
2. **Implantação Lambda** - Implantar 3 funções Lambda (ApplicationTool, RiskModelTool, ApprovalTool)
3. **Configuração Gateway** - Configurar AgentCore Gateway com OAuth e anexar alvos Lambda
4. **Teste de Agente** - Testar o agente de IA com acesso a todas as ferramentas (sem políticas ainda)
5. **Policy Engine** - Criar policy engine e anexar ao gateway
6. **Políticas Cedar** - Escrever e implantar políticas Cedar para controle de acesso
7. **Teste de Políticas** - Testar cenários ALLOW e DENY com requisições reais de agentes de IA
8. **Limpeza** - Remover todos os recursos criados

> **Nota**: A demonstração usa o cliente policy-registry nativo do boto3 (disponível no boto3 1.42.0+) e o framework Strands para funcionalidade de agente de IA.

## Estrutura do Projeto

```
Getting-Started/
├── AgentCore-Policy-Demo-pt.ipynb # Notebook principal da demonstração
├── README-pt.md                    # Este arquivo
├── requirements.txt                # Dependências Python
├── config.json                     # Arquivo de configuração gerado
└── scripts/                        # Scripts de suporte
    ├── setup_gateway.py            # Configuração Gateway com criação automática de função IAM
    ├── agent_with_tools.py         # Gerenciador de sessão de agente de IA
    ├── get_client_secret.py        # Recuperar client secret do Cognito
    ├── policy_generator.py         # Geração NL para Cedar
    └── lambda-target-setup/        # Scripts de implantação Lambda
        ├── deploy_lambdas.py       # Implantar todas as 3 funções Lambda
        ├── application_tool.js     # Código Lambda ApplicationTool
        ├── risk_model_tool.js      # Código Lambda RiskModelTool
        └── approval_tool.js        # Código Lambda ApprovalTool
```

## Conceitos-Chave

### AgentCore Gateway

Um cliente tipo MCP que permite que agentes acessem ferramentas.

### Policy Engine

Uma coleção de políticas Cedar que avalia requisições contra regras definidas em tempo real.

### Linguagem de Política Cedar

Uma linguagem de política declarativa com esta estrutura:

```cedar
permit(
  principal,              // Quem pode acessar
  action,                 // Qual ação eles podem realizar  
  resource                // Qual recurso eles podem acessar
) when {
  conditions              // Sob quais condições
};
```

### Modos de Política

- **LOG_ONLY**: Avalia políticas mas não bloqueia requisições (para testes)
- **ENFORCE**: Bloqueia ativamente requisições que violam políticas (para produção)

## Exemplo de Política

```cedar
permit(
  principal,
  action == AgentCore::Action::"ApplicationToolTarget___create_application",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  context.input.coverage_amount <= 1000000
};
```

Esta política:
- Permite criação de aplicação de seguro com cobertura abaixo de $1M
- Nega aplicações com cobertura de $1M ou mais
- Aplica-se ao alvo ApplicationTool
- Avalia o parâmetro `coverage_amount` em tempo real

> **Insight-Chave**: Quando um Policy Engine é anexado a um Gateway no modo ENFORCE, a ação padrão é DENY. Você deve criar explicitamente políticas de permissão para cada ferramenta que deseja permitir acesso.

## Arquitetura

```
┌─────────────┐
│   AI Agent  │
└──────┬──────┘
       │ Tool Call Request
       ▼
┌─────────────────────┐
│  AgentCore Gateway  │
│  + OAuth Auth       │
└──────┬──────────────┘
       │ Policy Check
       ▼
┌─────────────────────┐
│   Policy Engine     │
│   (Cedar Policies)  │
└──────┬──────────────┘
       │ ALLOW / DENY
       ▼
┌─────────────────────┐
│   Lambda Target     │
│   (RefundTool)      │
└─────────────────────┘
```

## Testes

A demonstração inclui testes abrangentes com um agente de IA real:

### Antes da Anexação do Policy Engine
- Agente pode listar todas as 3 ferramentas
- Agente pode invocar todas as ferramentas sem restrições
- Sem enforcement de políticas

### Depois da Anexação do Policy Engine (Vazio)
- Agente não pode listar nenhuma ferramenta (DENY padrão)
- Agente não pode invocar nenhuma ferramenta
- Todas as requisições bloqueadas

### Depois de Adicionar Política de Aplicação
- Agente pode listar apenas ApplicationTool
- Agente pode criar aplicações abaixo de $1M ✅
- Agente não pode criar aplicações acima de $1M ❌
- Outras ferramentas permanecem bloqueadas

### Teste 1: Cenário ALLOW ✅
- Requisição: Criar aplicação com cobertura de $750K
- Esperado: PERMITIDO
- Razão: $750K <= $1M
- Resultado: Lambda executa, aplicação criada

### Teste 2: Cenário DENY ❌
- Requisição: Criar aplicação com cobertura de $1.5M
- Esperado: NEGADO
- Razão: $1.5M > $1M
- Resultado: Política bloqueia requisição, Lambda nunca executa

## Recursos Avançados

### Múltiplas Condições

```cedar
permit(...) when {
  context.input.coverage_amount <= 1000000 &&
  has(context.input.applicant_region) &&
  context.input.applicant_region == "US"
};
```

### Condições Baseadas em Região

```cedar
permit(...) when {
  context.input.applicant_region in ["US", "CA", "UK"]
};
```

### Governança de Modelo de Risco

```cedar
permit(
  principal,
  action == AgentCore::Action::"RiskModelToolTarget___invoke_risk_model",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  context.input.API_classification == "public" &&
  context.input.data_governance_approval == true
};
```

### Limites de Aprovação

```cedar
permit(
  principal,
  action == AgentCore::Action::"ApprovalToolTarget___approve_underwriting",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  context.input.claim_amount <= 100000 &&
  context.input.risk_level in ["low", "medium"]
};
```

### Políticas de Negação

```cedar
forbid(...) when {
  context.input.coverage_amount > 10000000
};
```

## Monitoramento e Depuração

### CloudWatch Logs

Decisões de políticas são registradas no CloudWatch:

- **Logs do Gateway**: Detalhes de requisição/resposta
- **Logs do Policy Engine**: Resultados de avaliação de políticas
- **Logs do Lambda**: Detalhes de execução de ferramentas

### Problemas Comuns

1. **Política Não Está Fazendo Enforcement**
   - Verificar modo ENFORCE (não LOG_ONLY)
   - Checar se status da política é ACTIVE
   - Confirmar anexação ao gateway

2. **Todas as Requisições Negadas**
   - Revisar condições da política
   - Verificar se o nome da ação corresponde ao alvo
   - Checar se o ARN do recurso corresponde ao gateway

3. **Falhas de Autenticação**
   - Verificar credenciais OAuth
   - Checar acessibilidade do endpoint de token
   - Garantir que client_id e client_secret estão corretos

4. **Erros de Importação de Módulo**
   - Garantir que boto3 1.42.0+ está instalado: `pip install --upgrade boto3`
   - Garantir que strands está instalado: `pip install strands`
   - Reiniciar kernel Jupyter após atualizar dependências
   - Limpar cache Python: `rm -rf scripts/__pycache__`

5. **Erros de Sessão do Agente**
   - Se você ver `MCPClientInitializationError`, reinicie o kernel do notebook
   - Garantir que config.json tem o campo client_secret populado
   - Executar `scripts/get_client_secret.py` para recuperar o secret se estiver faltando

6. **Token AWS Expirado**
   - Atualizar credenciais AWS: `aws sso login` ou `aws configure`
   - Reiniciar kernel do notebook para pegar novas credenciais
   - Re-executar células desde o início


## Recursos Adicionais

- **Linguagem de Política Cedar**: [Documentação Cedar](https://docs.cedarpolicy.com/)
- **Amazon Bedrock AgentCore Policy**: [Documentação AWS AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html)

---

**Bom Desenvolvimento!** 🚀
