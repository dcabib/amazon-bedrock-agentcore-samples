# AgentCore Browser com Proxy Squid

Este exemplo implanta um Amazon Bedrock AgentCore Browser que roteia todo o tráfego web através de um proxy Squid de encaminhamento rodando no EC2. O proxy autentica requisições via Secrets Manager e envia logs de acesso para o S3, fornecendo uma trilha de auditoria completa de cada URL que o navegador visita.

## Arquitetura

```
┌─────────────────────────────────────────────────────┐
│  VPC (10.0.0.0/16)                                  │
│                                                     │
│  ┌─────────────────────┐  ┌──────────────────────┐  │
│  │  Subnet Privada     │  │  Subnet Pública      │  │
│  │                     │  │                      │  │
│  │  AgentCore Browser  │──│  Squid EC2 (:3128)   │──── Internet
│  │  (modo VPC)         │  │  ├─ autenticação básica │
│  │                     │  │  ├─ logs de acesso → S3 │
│  └─────────────────────┘  │  └─ credenciais ← Secrets Mgr │
│                           └──────────────────────┘  │
│                                                     │
│  Bucket S3 (squid-logs)    Secrets Manager (creds)  │
└─────────────────────────────────────────────────────┘
```

O grupo de segurança do navegador só permite saída para o Squid na porta 3128 — não há NAT Gateway, então o proxy é o único caminho para a internet.

## Implantação Rápida

Para criar a stack usando CloudFormation, use o seguinte script:

[![Launch Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](agentcore-browser-proxy.yaml)

### Parâmetros

| Parâmetro | Padrão | Descrição |
|-----------|---------|-------------|
| VpcCidr | `10.0.0.0/16` | Bloco CIDR para a VPC |
| AvailabilityZone | — | AZ para todas as subnets |
| BrowserName | `proxy_browser` | Nome do AgentCore Browser |
| SquidInstanceType | `t3.micro` | Tipo de instância EC2 para o Squid |

As credenciais do proxy (nome de usuário + senha aleatória) são geradas automaticamente no Secrets Manager.

## O Que é Implantado

| Recurso | Finalidade |
|----------|---------|
| VPC + 2 subnets | Isolamento de rede |
| EC2 (Squid) | Proxy de encaminhamento com autenticação básica |
| Secret do Secrets Manager | Credenciais do proxy (geradas automaticamente) |
| Bucket S3 | Logs de acesso do Squid (ciclo de vida de 90 dias) |
| AgentCore Browser | Modo VPC, saída bloqueada para o proxy |
| Roles IAM | Privilégios mínimos para navegador + EC2 |

## Verificar o Proxy

Após a implantação, obtenha as saídas da stack:

```bash
aws cloudformation describe-stacks \
  --stack-name agentcore-browser-proxy \
  --query 'Stacks[0].Outputs' --output table
```

### Opção A: Notebook

```bash
pip install -r requirements.txt
```

Carregue `verify_proxy.ipynb` no Kiro IDE ou seu IDE favorito.

### Opção B: Script

```bash
pip install -r requirements.txt
python verify_proxy.py
```

Ambos irão:
1. Ler o ID do Browser, IPs do Squid e ARN do secret do CloudFormation
2. Iniciar uma sessão de navegador com `proxyConfiguration` apontando para o Squid
3. Navegar para `icanhazip.com` e comparar o IP observado com o IP público do Squid
4. Imprimir PASS se coincidirem

### Estrutura da Configuração do Proxy

A `proxyConfiguration` passada para `start_browser_session()`:

```json
{
  "proxies": [{
    "externalProxy": {
      "server": "<squid-private-ip>",
      "port": 3128,
      "credentials": {
        "basicAuth": {
          "secretArn": "arn:aws:secretsmanager:..."
        }
      }
    }
  }]
}
```

Você também pode adicionar `domainPatterns` para rotear apenas domínios específicos através do proxy, e usar `bypass.domainPatterns` para pular o proxy para certos domínios.

## Logs de Acesso

Os logs de acesso do Squid são sincronizados para o S3 a cada 5 minutos:

```
s3://<stack>-squid-logs-<account>/squid-logs/YYYY/MM/DD/HH/<instance>-access.log.<n>
```

Listar logs recentes:

```bash
BUCKET=$(aws cloudformation describe-stacks --stack-name agentcore-browser-proxy \
  --query 'Stacks[0].Outputs[?OutputKey==`LogBucketName`].OutputValue' --output text)
aws s3 ls "s3://$BUCKET/squid-logs/" --recursive
```

## Arquivos

| Arquivo | Descrição |
|------|-------------|
| `agentcore-browser-proxy.yaml` | Template CloudFormation |
| `verify_proxy.py` | Script de verificação CLI |
| `verify_proxy.ipynb` | Versão notebook com verificação de logs S3 |
| `requirements.txt` | Dependências Python |

## Limpeza

```bash
# Esvaziar o bucket de logs primeiro (obrigatório antes da exclusão da stack)
BUCKET=$(aws cloudformation describe-stacks --stack-name agentcore-browser-proxy \
  --query 'Stacks[0].Outputs[?OutputKey==`LogBucketName`].OutputValue' --output text)
aws s3 rm "s3://$BUCKET" --recursive

aws cloudformation delete-stack --stack-name agentcore-browser-proxy
```

## Considerações de Segurança

- Navegador roda em subnet privada sem acesso direto à internet
- Todo tráfego web é forçado através do proxy Squid autenticado
- Credenciais do proxy são armazenadas no Secrets Manager (nunca em texto simples)
- Bucket de logs S3 tem acesso público bloqueado e criptografia do lado do servidor
- Grupo de segurança do Squid só aceita conexões do grupo de segurança do navegador
