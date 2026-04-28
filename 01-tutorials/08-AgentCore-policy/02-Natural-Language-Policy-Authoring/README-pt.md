# AgentCore Policy - Criação de Políticas em Linguagem Natural (NL2Cedar)

Uma demonstração prática de geração de políticas Cedar a partir de linguagem natural usando a capacidade NL2Cedar do Amazon Bedrock AgentCore Policy.

## 🚀 Início Rápido

1. **Instalar dependências**: `pip install -r requirements.txt`
2. **Abrir notebook**: `jupyter notebook NL-Authoring-Policy-pt.ipynb`
3. **Seguir os passos** no notebook

> **Nota**: Esta demonstração é baseada no tutorial Getting-Started. Se você não o completou, o notebook configurará automaticamente a infraestrutura necessária.

## Visão Geral

Esta demonstração mostra como escrever requisitos de autorização em linguagem natural e convertê-los automaticamente para sintaxe Cedar. A capacidade NL2Cedar ajuda você a:

- Escrever políticas em português simples ao invés de sintaxe Cedar
- Gerar múltiplas políticas a partir de declarações multi-linha
- Criar políticas baseadas em principal com atributos de identidade
- Verificar que as políticas geradas correspondem aos seus requisitos

## O Que Você Vai Aprender

- ✅ Gerar políticas Cedar a partir de descrições em linguagem natural
- ✅ Criar políticas simples de declaração única
- ✅ Gerar múltiplas políticas a partir de declarações multi-linha
- ✅ Escrever políticas com escopo de principal com atributos de identidade
- ✅ Entender diferentes construções e padrões de políticas

## Pré-requisitos

Antes de começar, certifique-se de ter:

- AWS CLI configurado com credenciais apropriadas
- Python 3.10+ com boto3 1.42.0+ instalado
- Pacote `bedrock_agentcore_starter_toolkit` instalado
- Acesso ao AWS Lambda (para funções alvo)
- Completado o tutorial **01-Getting-Started** (ou deixe o notebook configurá-lo automaticamente)

## Cenário da Demonstração

Esta demonstração usa o **sistema de subscrição de seguros** do tutorial Getting-Started com 3 ferramentas Lambda:

1. **ApplicationTool** - Cria aplicações de seguro
   - Parâmetros: `applicant_region`, `coverage_amount`

2. **RiskModelTool** - Invoca modelo de pontuação de risco
   - Parâmetros: `API_classification`, `data_governance_approval`

3. **ApprovalTool** - Aprova decisões de subscrição
   - Parâmetros: `claim_amount`, `risk_level`

## Exemplos de Políticas em Linguagem Natural

### 1. Política Simples de Declaração Única

**Linguagem Natural:**
```
Allow all users to invoke the application tool when the coverage amount 
is under 1 million and the application region is US or CAN
```

**Política Cedar Gerada:**
```cedar
permit(
  principal,
  action == AgentCore::Action::"ApplicationToolTarget___create_application",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  (context.input.coverage_amount < 1000000) && 
  ((context.input.applicant_region == "US") || 
   (context.input.applicant_region == "CAN"))
};
```

### 2. Declarações Multi-Linha

**Linguagem Natural:**
```
Allow all users to invoke the risk model tool when data governance approval is true.
Block users from calling the application tool unless coverage amount is present.
```

**Resultado:** Gera **2 políticas separadas** - uma política de permissão e uma de proibição.

### 3. Políticas Baseadas em Principal

**Linguagem Natural:**
```
Allow principals with username "test-user" to invoke the risk model tool
```

**Política Cedar Gerada:**
```cedar
permit(
  principal,
  action == AgentCore::Action::"RiskModelToolTarget___invoke_risk_model",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  (principal.hasTag("username")) && 
  (principal.getTag("username") == "test-user")
};
```

**Linguagem Natural:**
```
Forbid principals to access the approval tool unless they have 
the scope group:Controller
```

**Política Cedar Gerada:**
```cedar
forbid(
  principal,
  action == AgentCore::Action::"ApprovalToolTarget",
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  !((principal.hasTag("scope")) && 
    (principal.getTag("scope") like "*group:Controller*"))
};
```

**Linguagem Natural:**
```
Block principals from using risk model tool and approval tool 
unless the principal has role "senior-adjuster"
```

**Política Cedar Gerada:**
```cedar
forbid(
  principal,
  action in [AgentCore::Action::"RiskModelToolTarget",
             AgentCore::Action::"ApprovalToolTarget"],
  resource == AgentCore::Gateway::"<gateway-arn>"
) when {
  !((principal.hasTag("role")) && 
    (principal.getTag("role") == "senior-adjuster"))
};
```

## Como o NL2Cedar Funciona

1. **Consciência de Schema**: Os schemas de alvos do Gateway são fornecidos ao NL2Cedar para ajudar o modelo de fundação a entender nomes de ferramentas e parâmetros

2. **Entrada em Linguagem Natural**: Você fornece requisitos de autorização em inglês simples

3. **Geração Cedar**: O sistema gera políticas Cedar sintaticamente corretas

4. **Criação de Política**: Políticas geradas podem ser criadas diretamente no seu Policy Engine

## Fluxo de Trabalho

O notebook guia você através de:

1. **Configuração de Ambiente** - Verificar credenciais e dependências
2. **Verificação de Infraestrutura** - Configurar automaticamente Gateway se necessário (do Getting-Started)
3. **Criação de Policy Engine** - Criar um Policy Engine para políticas NL2Cedar
4. **Geração de Política Simples** - Gerar uma única política a partir de linguagem natural
5. **Criação de Política** - Criar a política gerada no Policy Engine
6. **Geração Multi-Linha** - Gerar múltiplas políticas a partir de declarações multi-linha
7. **Políticas Baseadas em Principal** - Criar políticas conscientes de identidade
8. **Limpeza** - Remover todos os recursos criados

## Recursos-Chave

### Configuração Automática de Infraestrutura

Se você não completou o tutorial Getting-Started, o notebook irá:
- Implantar 3 funções Lambda (ApplicationTool, RiskModelTool, ApprovalTool)
- Criar AgentCore Gateway com autenticação OAuth
- Configurar alvos Lambda com schemas apropriados
- Salvar configuração em `config.json`

### Geração Multi-Política

Quando você fornece declarações multi-linha com delimitadores consistentes (vírgulas, pontos, ponto-e-vírgula), o NL2Cedar automaticamente:
- Detecta declarações de política individuais
- Gera políticas Cedar separadas para cada declaração
- Retorna todas as políticas no array `generatedPolicies`

### Suporte a Escopo de Principal

Para políticas baseadas em identidade, você pode referenciar:
- **Username**: `principal.getTag("username")`
- **Role**: `principal.getTag("role")`
- **Scope**: `principal.getTag("scope")`
- **Claims Personalizados**: Qualquer atributo do seu token OAuth

> **💡 Dica**: Fornecer o nome exato da tag na sua declaração de linguagem natural ajuda o NL2Cedar a criar a política Cedar correta.


## Melhores Práticas

1. **Seja Específico**: Declare claramente o nome da ferramenta, parâmetros e condições
2. **Use Nomes Exatos de Parâmetros**: Referencie parâmetros como eles aparecem no schema do Gateway
3. **Especifique Atributos de Principal**: Para políticas baseadas em identidade, mencione o nome exato da tag
4. **Um Conceito Por Linha**: Para geração multi-linha, separe políticas distintas com delimitadores consistentes
5. **Teste Políticas Geradas**: Sempre revise a sintaxe Cedar gerada antes de implantar



## Recursos Adicionais

- **Políticas de Exemplo**: [Políticas Cedar Suportadas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/example-policies.html)
- **Tutorial Getting Started**: `../01-Getting-Started/README-pt.md`

---

**Bom Desenvolvimento!** 🚀
