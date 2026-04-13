# AgentCore Browser Tool com AWS Network Firewall

Este exemplo demonstra como implantar a Ferramenta de Navegador do Amazon Bedrock AgentCore com lista de permissões/bloqueios baseada em domínios usando AWS Network Firewall. Isso fornece um ambiente de navegação seguro e controlado com filtragem em nível de rede.

## Visão Geral da Arquitetura

A solução implanta:
- **VPC com 3 sub-redes**: Privada (Browser), Pública (NAT Gateway) e Firewall
- **AWS Network Firewall**: Filtragem de domínios com listas de permissões/bloqueios
- **AgentCore Browser**: Implantado em sub-rede privada com configuração VPC
- **NAT Gateway**: Acesso de saída à internet através de inspeção do firewall
- **CloudWatch Logs**: Logs de alerta e fluxo do firewall para monitoramento

## Implantação Rápida

Para criar a stack usando CloudFormation, use o seguinte script:

[![Launch Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](agentcore-browser-firewall.yaml)

### Parâmetros

- **VpcCidr**: Bloco CIDR para a VPC (padrão: `10.0.0.0/16`)
- **AvailabilityZone**: Selecione uma AZ para todas as sub-redes
- **AllowedDomains**: Lista de domínios permitidos separados por vírgula (ex.: `.example.com,.wikipedia.org`)
- **DeniedDomains**: Lista de domínios bloqueados separados por vírgula (ex.: `.facebook.com,.twitter.com`)
- **BrowserName**: Nome para o navegador (padrão: `secure_browser`)
- **BucketConfigForOutput**: Nome do bucket S3 para gravações do navegador

## Testes

### Opção 1 - Jupyter Notebook

Se você deseja um passo a passo direto, pode abrir [verify_domain_filtering.ipynb](verify_domain_filtering.ipynb) e executar passo a passo.


### Opção 2 - Código Python

Se você deseja ir direto ao código, pode usar arquivos Python. 

Após a implantação, obtenha o identificador do AgentCore Browser. Você pode obtê-lo pela linha de comando:

```bash
# Obter o ID do Browser das saídas do CloudFormation
export BROWSER_ID=$(aws cloudformation describe-stacks \
  --stack-name agentcore-browser-firewall \
  --query 'Stacks[0].Outputs[?OutputKey==`BrowserToolCustomOutput`].OutputValue' \
  --output text)
```

ou do Console AWS, das informações de saída na stack do CloudFormation:

![browser-output](img/cfn-output.png)

**Importante: O script `verify_domain_filtering.py` espera o identificador do navegador na variável de ambiente BROWSER_ID.**

Teste a filtragem de domínios usando o script Python fornecido:

```bash
# Instalar dependências
pip install -r requirements.txt
playwright install chromium

# Executar testes
python verify_domain_filtering.py
```

### Script de Teste

O script `verify_domain_filtering.py` valida:
- ✅ Domínios permitidos (example.com, wikipedia.org) - devem ter sucesso
- ❌ Domínios bloqueados (facebook.com, twitter.com) - devem ser bloqueados
- ❌ Domínios não listados - devem ser bloqueados (bloqueio padrão)

## Arquivos

- **agentcore-browser-firewall.yaml**: Template CloudFormation com infraestrutura completa
- **verify_domain_filtering.py**: Script de teste automatizado usando Playwright para verificar regras do firewall
- **verify_domain_filtering.ipynb**: Jupyter notebook com passo a passo

## Monitoramento

Visualize logs do firewall no CloudWatch:
- Logs de alerta: `/aws/network-firewall/{StackName}/alerts`
- Logs de fluxo: `/aws/network-firewall/{StackName}/flow`

## Limpeza

```bash
aws cloudformation delete-stack --stack-name agentcore-browser-firewall
```

## Considerações de Segurança

- O navegador executa em sub-rede privada sem acesso direto à internet
- Todo o tráfego é inspecionado pelo Network Firewall antes de alcançar a internet
- Política de bloqueio padrão - apenas domínios explicitamente permitidos são acessíveis
- Gravações armazenadas no S3 com permissões IAM apropriadas
