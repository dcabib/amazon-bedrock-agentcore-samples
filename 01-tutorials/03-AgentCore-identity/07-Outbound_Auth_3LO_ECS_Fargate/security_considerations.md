# Considerações de Segurança Adicionais

## Criptografia em Repouso

Este exemplo usa criptografia KMS gerenciada pela AWS e gerenciada pelo cliente:

**Chave KMS Gerenciada pelo Cliente (Incluída):**

- Amazon CloudWatch Logs (serviços de Agent e OAuth Callback)
- Bucket S3 de sessões

**Chaves de Criptografia Gerenciadas pela AWS:**

- Bucket S3 de logs de acesso do Application Load Balancer (ALB suporta apenas SSE-S3)
- Repositórios Amazon ECR (criados automaticamente pelo CDK com criptografia padrão KMS gerenciada pela AWS)

- **Amazon Bedrock AgentCore Identity** token vault: Se KMS gerenciada pelo cliente for necessária, use [`set-token-vault-cmk`](https://docs.aws.amazon.com/cli/latest/reference/bedrock-agentcore-control/set-token-vault-cmk.html)

- **AWS Secrets Manager** (credenciais SSO): Se KMS gerenciada pelo cliente for necessária, adicione o parâmetro `--kms-key-id` ao criar o secret

### Rede e Monitoramento

- **VPC Flow Logs**: VPC Flow Logs não estão habilitados neste exemplo para reduzir custos. Para implantações de produção, habilite VPC Flow Logs para monitoramento de tráfego de rede e análise de segurança.

- **VPC Endpoints**: Este exemplo não usa VPC endpoints para serviços AWS (Bedrock, S3, Secrets Manager). Para implantações de produção, considere adicionar VPC endpoints para evitar rotear tráfego através de NAT gateways e internet gateways, o que pode reduzir custos.

- **WAF e CloudFront**: O ALB é publicamente acessível (0.0.0.0/0 ingress na porta 443) e protegido por autenticação OIDC. Para implantações de produção, considere adicionar AWS WAF para proteção contra explorações web comuns (injeção SQL, XSS, DDoS) e CloudFront para entrega de conteúdo, cache e proteção adicional contra DDoS na borda.

- **CloudWatch Alarms**: Este exemplo não inclui alarmes CloudWatch para monitoramento. Para implantações de produção, implemente alarmes CloudWatch sobre uso excepcional de recursos e métricas (CPU, memória, taxas de erro, throttling de API) para detectar e responder a problemas operacionais e de segurança.

### Controle de Acesso

- **Políticas de Bucket Amazon S3**: Políticas de bucket S3 podem ser usadas para restringir ainda mais o acesso com chaves de condição IAM para controle de acesso granular baseado em identidade do usuário, endereço IP ou atributos de requisição.

- **Administração de Chave KMS**: A política de chave KMS permite à conta root permissões completas (`kms:*`). Para implantações de produção, considere restringir permissões administrativas de chave a principals ou roles IAM específicos em sua conta para seguir o princípio de privilégio mínimo.

- **Amazon Bedrock Guardrails**: Este exemplo não configura Bedrock Guardrails. Para implantações de produção, considere implementar guardrails para filtrar conteúdo prejudicial, PII e inputs/outputs inadequados do agente com base em seus requisitos.
