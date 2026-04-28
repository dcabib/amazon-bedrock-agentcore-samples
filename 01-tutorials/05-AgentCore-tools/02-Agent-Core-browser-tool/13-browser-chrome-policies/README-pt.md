# AgentCore Browser com Políticas Chrome Enterprise e Root CAs Personalizados

Este exemplo demonstra como usar [políticas enterprise do Chrome](https://chromeenterprise.google/policies/) e certificados root CA personalizados com Amazon Bedrock AgentCore Browser e Code Interpreter.

## Visão Geral

Políticas enterprise do Chrome permitem que você:
- **Restrinja navegação de agentes**: Defina listas de URLs permitidas e bloqueadas que limitam onde os agentes podem navegar
- **Desabilite recursos arriscados**: Desative o gerenciador de senhas, bloqueie downloads, desabilite DevTools
- **Force conformidade**: Aplique políticas gerenciadas no nível do navegador que não podem ser substituídas por sessões

Certificados root CA personalizados permitem que você:
- **Conecte a serviços internos**: Confie em certificados assinados pela CA privada da sua organização (Jira, Artifactory, portais de RH)
- **Trabalhe com proxies corporativos**: Confie em root CAs de proxies que interceptam SSL (Zscaler, Palo Alto Networks)

## Casos de Uso

- Bloquear um agente de entrada de dados para acessar apenas um portal corporativo específico
- Evitar que agentes armazenem credenciais ou baixem arquivos
- Habilitar agentes a se conectarem a infraestrutura interna que usa PKI privada
- Rotear tráfego de agentes através de proxies corporativos que interceptam SSL

## Primeiros Passos

### Pré-requisitos

- Python 3.10 ou posterior
- Uma conta AWS com acesso ao Amazon Bedrock AgentCore habilitado
- Credenciais AWS configuradas (`aws sts get-caller-identity`)
- Uma região AWS onde Amazon Bedrock AgentCore está disponível

> **Nota:** O notebook cria todos os recursos necessários (bucket S3, função IAM, AgentCore Browser, Code Interpreter) automaticamente. Você não precisa pré-criar nenhum recurso.

### Instalação

```bash
pip install -r requirements.txt
```

### Executar

```bash
jupyter notebook browser-chrome-policies.ipynb
```

Execute as células sequencialmente. A Parte 1 cobre políticas enterprise do Chrome, a Parte 2 cobre certificados root CA personalizados.

## Passo a Passo do Notebook

O notebook [browser-chrome-policies.ipynb](browser-chrome-policies.ipynb) demonstra:

### Configuração

- Cria um bucket S3 para arquivos de política e gravações de sessão
- Cria uma função IAM de execução com uma política de confiança para `bedrock-agentcore.amazonaws.com` e permissões S3

### Parte 1: Políticas Chrome Enterprise

1. **Criar política Chrome** — Defina um JSON de política que bloqueia todas as URLs exceto documentação AWS e desabilita recursos arriscados, depois faça upload para S3
2. **Criar browser com políticas gerenciadas** — Crie um AgentCore Browser personalizado com a política aplicada via `enterprise_policies` com `type: "MANAGED"` e gravação de sessão habilitada
3. **Demonstrar com Playwright** — Navegue para uma URL permitida (página carrega) e uma URL bloqueada (Chrome exibe uma página de erro), mostrando enforcement no nível do navegador independente de qualquer lógica de agente
4. **Revisar gravação de sessão** — Reproduza a sessão no console AgentCore para observar o enforcement da política
5. **(Opcional) Executar um agente Strands** — Use o navegador restrito com um framework de agente de IA para mostrar comportamento de agente end-to-end sob restrições de política

### Parte 2: Certificados Root CA Personalizados

6. **Armazenar root CA no Secrets Manager** — Armazene o certificado root CA não confiável do [BadSSL](https://badssl.com) (um certificado de teste público) no AWS Secrets Manager
7. **Code Interpreter SEM root CA** — Mostre o erro `SSLCertVerificationError` ao conectar a um site com certificado não confiável
8. **Code Interpreter COM root CA** — Crie um Code Interpreter personalizado com `Certificate.from_secret_arn()` e mostre uma conexão HTTP 200 bem-sucedida

### Limpeza

Deleta todos os recursos: browser personalizado, Code Interpreter, função IAM, secret do Secrets Manager e arquivo de política S3.

## Padrões-Chave do SDK

### Políticas Chrome gerenciadas (nível do navegador)

```python
from bedrock_agentcore.tools import BrowserClient

client = BrowserClient(REGION)

response = client.create_browser(
    name="my_browser",
    execution_role_arn=EXECUTION_ROLE_ARN,
    network_configuration={"networkMode": "PUBLIC"},
    enterprise_policies=[
        {
            "location": {
                "s3": {
                    "bucket": POLICY_BUCKET,
                    "prefix": POLICY_KEY,
                }
            },
            "type": "MANAGED",
        }
    ],
)
```

### Certificados root CA personalizados

```python
from bedrock_agentcore.tools import CodeInterpreter, Certificate

ci_client = CodeInterpreter(REGION)

response = ci_client.create_code_interpreter(
    name="my_interpreter",
    execution_role_arn=EXECUTION_ROLE_ARN,
    network_configuration={"networkMode": "PUBLIC"},
    certificates=[
        Certificate.from_secret_arn(SECRET_ARN)
    ],
)
```

### Níveis de enforcement de políticas

| Nível | Parâmetro | Quando definido | Diretório Chrome | Pode substituir? |
|-------|-----------|-----------------|------------------|------------------|
| Gerenciado | `type: "MANAGED"` | `create_browser()` | `/etc/chromium/policies/managed/` | Não |
| Recomendado | `type: "RECOMMENDED"` | `start()` / `browser_session()` | `/etc/chromium/policies/recommended/` | Sim (por gerenciado) |

## O Que Observar

- **No seu terminal**: Saída do Playwright mostrando o título da página permitida e o erro da URL bloqueada
- **No console AgentCore**: Navegue para **Built-in tools** → seu navegador → sessão ativa → **View live session** para assistir em tempo real
- **Replay de sessão**: Depois que a sessão terminar, escolha **View Recording** na sessão terminada para ver a linha do tempo com a tentativa de URL bloqueada
- **Demo de Root CA**: Saída do terminal mostra o erro SSL (sem cert) e resposta 200 bem-sucedida (com cert)

## Arquivos

| Arquivo | Descrição |
|---------|-----------|
| `browser-chrome-policies.ipynb` | Notebook tutorial completo com configuração, políticas Chrome, demo de root CA e limpeza |
| `requirements.txt` | Dependências Python |
| `README-pt.md` | Este arquivo |

## Considerações de Segurança

- Políticas Chrome aplicam restrições no nível do navegador, independente de prompts de agente
- Políticas gerenciadas não podem ser substituídas por políticas recomendadas no nível de sessão
- Certificados root CA devem ser rotacionados antes da expiração
- Use políticas IAM de privilégio mínimo para acesso S3 e Secrets Manager
- Gravações de sessão podem conter conteúdo sensível de páginas — aplique controles de acesso S3 apropriados

## Recursos Adicionais

- [Documentação Amazon Bedrock AgentCore Browser](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-tool.html)
- [Lista de políticas Chrome Enterprise](https://chromeenterprise.google/policies/)
- [Documentação AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)
- [Strands Agents — Model Providers](https://strandsagents.com/latest/user-guide/concepts/model-providers/)
- [Amazon Bedrock AgentCore Python SDK](https://github.com/aws/bedrock-agentcore-sdk-python)
