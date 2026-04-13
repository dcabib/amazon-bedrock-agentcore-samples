# E-Commerce de Exemplo

Site estático simples de e-commerce para camisetas com carrinho de compras persistente usando localStorage.

## Executar Localmente

```bash
cd sample-ecommerce
python3 -m http.server 8000
```

Depois abra: http://localhost:8000

## Implantar na AWS com CloudFormation

### Implantação Rápida

```bash
chmod +x deploy.sh
./deploy.sh
```

O script irá automaticamente:
- Criar bucket S3 (ou usar existente se houver conflito de nome)
- Implantar stack CloudFormation com CloudFront
- Fazer upload dos arquivos do site
- Invalidar cache do CloudFront
- Exibir URL do CloudFront

### Atualizar Implantação Existente

```bash
./update.sh
```

Atualiza arquivos no S3 e invalida o cache do CloudFront sem tocar na infraestrutura.

### Implantação Manual

```bash
# Criar bucket
BUCKET_NAME="sample-ecommerce-static-site-$(date +%s)"
aws s3 mb s3://$BUCKET_NAME --region us-east-1

# Implantar CloudFormation
aws cloudformation deploy \
  --template-file cloudformation.yaml \
  --stack-name sample-ecommerce-stack \
  --parameter-overrides BucketName=$BUCKET_NAME \
  --region us-east-1

# Fazer upload dos arquivos
aws s3 sync . s3://$BUCKET_NAME/ \
  --exclude "*.yaml" --exclude "*.sh" --exclude "*.md"

# Obter URL do CloudFront
aws cloudformation describe-stacks \
  --stack-name sample-ecommerce-stack \
  --query 'Stacks[0].Outputs[?OutputKey==`CloudFrontURL`].OutputValue' \
  --output text
```

### Excluir Stack (Mantém o Bucket)

```bash
./delete.sh
```

Ou manualmente:
```bash
aws cloudformation delete-stack --stack-name sample-ecommerce-stack --region us-east-1
```

O bucket S3 NÃO é gerenciado pelo CloudFormation e permanecerá após a exclusão da stack.

## Implantar no S3 (Simples - Sem CloudFront)

```bash
# Criar bucket
aws s3 mb s3://your-bucket-name

# Fazer upload dos arquivos
aws s3 sync . s3://your-bucket-name --acl public-read

# Habilitar hospedagem de site estático
aws s3 website s3://your-bucket-name --index-document index.html
```

Seu site estará disponível em: http://your-bucket-name.s3-website-[region].amazonaws.com

## Recursos

- 6 produtos de camisetas com imagens
- Adicionar ao carrinho
- Visualizar carrinho com miniaturas e total
- Remover itens
- Carrinho persiste no navegador (localStorage)
- Caminhos de URL claros para automação (#home, #cart)

## Navegação com Playwright

```python
# Navegar para páginas
page.goto("http://localhost:8000/#home")
page.goto("http://localhost:8000/#cart")

# Ou usar seletores
page.locator('[data-page="home"]')
page.locator('[data-page="cart"]')
```
