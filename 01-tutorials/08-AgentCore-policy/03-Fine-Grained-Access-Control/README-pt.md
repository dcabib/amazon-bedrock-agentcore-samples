# Policy para Amazon Bedrock AgentCore

## Visão Geral

Policy para Amazon Bedrock AgentCore habilita controle de acesso refinado para agentes de IA usando políticas Cedar. Ele avalia claims de tokens JWT para determinar se invocações de ferramentas devem ser permitidas ou negadas.

### Arquitetura

```
                                ┌───────────────────────┐
                                │  Policy for AgentCore │
                                │  (Cedar Policies)     │
                                │                       │
                                │  Avalia:              │
                                │  - principal tags     │
                                │  - context.input      │
                                │  - resource           │
                                └───────────┬───────────┘
                                            │ anexado
                                            ▼
┌─────────────────┐             ┌───────────────────────┐             ┌─────────────┐
│   Amazon        │  JWT Token  │  Amazon Bedrock       │             │   Lambda    │
│   Cognito       │────────────▶│  AgentCore Gateway    │────────────▶│   Target    │
│   + AWS Lambda  │  com        │                       │  se ALLOWED │   (Tool)    │
└─────────────────┘  claims     └───────────────────────┘             └─────────────┘
```

### Detalhes do Tutorial

| Informação           | Detalhes                                                |
|:---------------------|:--------------------------------------------------------|
| Componentes AgentCore| Gateway, Identity, Policy                               |
| Complexidade exemplo | Intermediário                                           |
| SDK usado            | boto3, requests                                         |

## Pré-requisitos

- Conta AWS com permissões IAM apropriadas
- Amazon Bedrock AgentCore Gateway com autorizador OAuth
- Amazon Cognito User Pool (cliente M2M, tier **Essentials** ou **Plus**)
- Python 3.8+

## Primeiros Passos

### Opção 1: Script de Configuração (Novos Recursos)

```bash
pip install bedrock-agentcore-starter-toolkit
python setup-gateway.py
```

### Opção 2: Recursos Existentes

Crie `gateway_config.json` com seus detalhes de Gateway e Cognito (veja o notebook para template).

### Executar o Tutorial

Abra [policy_for_agentcore_tutorial-pt.ipynb](policy_for_agentcore_tutorial-pt.ipynb)

## Sintaxe de Política Cedar

| Padrão | Sintaxe Cedar |
|---------|-------------|
| Verificar se claim existe | `principal.hasTag("claim_name")` |
| Correspondência exata | `principal.getTag("claim_name") == "value"` |
| Correspondência de padrão | `principal.getTag("claim_name") like "*value*"` |
| Validação de entrada | `context.input.field <= value` |

## Cenários de Teste

1. **Baseado em Departamento** - Permitir apenas usuários de departamentos específicos
2. **Baseado em Grupos** - Permitir usuários em grupos específicos (correspondência de padrão)
3. **Baseado em ID de Principal** - Permitir aplicações cliente específicas
4. **Condições Combinadas** - Múltiplas condições com validação de entrada

## Melhores Práticas

- Use `hasTag()` antes de `getTag()` para evitar erros
- Use correspondência de padrão com cuidado - `like "*value*"` pode corresponder strings não intencionais
- Teste ambos os cenários ALLOW e DENY
- Use trigger Lambda V3_0 para fluxo de credenciais de cliente M2M
