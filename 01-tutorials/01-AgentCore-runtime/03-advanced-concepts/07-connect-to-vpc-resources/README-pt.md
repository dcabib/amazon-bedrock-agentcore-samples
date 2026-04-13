# VPC Fargate Agent Runtime CDK Stack

Esta stack CDK implanta um container Fargate em uma VPC com AWS Bedrock AgentCore Runtime. O container expõe dois endpoints HTTP:

- `POST /invocations` - Endpoint principal para processar requisições
- `GET /ping` - Endpoint de verificação de saúde

## Arquitetura

A stack cria:

- **VPC** com subnets públicas e privadas em 2 zonas de disponibilidade
- **NAT Gateway** para acesso à internet da subnet privada
- **Security Group** permitindo tráfego de entrada na porta 8080 dentro da VPC
- **Imagem Docker** construída automaticamente pelo CDK e enviada para o ECR (ARM64/Graviton)
- **AgentCore Runtime** executando o container em subnets privadas
- **IAM Role** com as permissões necessárias para operações do AgentCore

## Pré-requisitos

1. **AWS CLI** configurado com credenciais apropriadas
2. **Node.js** e **npm** instalados
3. **Docker** instalado e em execução (necessário para construir a imagem do container)
4. **AWS CDK** com bootstrap na sua conta/região:
   ```bash
   npx cdk bootstrap
   ```

## Estrutura do Projeto

```
07-connect-to-vpc-resources/
├── bin/
│   └── app.ts              # Ponto de entrada da aplicação CDK
├── lib/
│   └── vpc-fargate-stack.ts # Definição principal da stack
├── agent-code/
│   ├── app.py              # Aplicação Flask com /ping e /invocations
│   ├── Dockerfile          # Definição do container
│   └── requirements.txt    # Dependências Python
├── package.json            # Dependências NPM
├── tsconfig.json           # Configuração TypeScript
└── cdk.json               # Configuração CDK
```

## Instalação

1. Instale as dependências NPM:

   ```bash
   npm install
   ```

2. Compile o código TypeScript:
   ```bash
   npm run build
   ```

## Implantação

### Implantar a Stack

```bash
npm run deploy
```

Este comando irá:

1. Compilar o código TypeScript do CDK
2. Construir a imagem Docker para ARM64 (Graviton)
3. Enviar a imagem para o ECR
4. Criar a VPC, security groups e rede
5. Implantar o AgentCore Runtime com o container

### Ver o Template CloudFormation Sintetizado

```bash
npm run synth
```

### Mostrar Diferenças

Para ver quais mudanças serão feitas antes da implantação:

```bash
npx cdk diff
```

## Outputs da Stack

Após a implantação, a stack fornece estes outputs:

- **VpcId** - ID da VPC criada
- **SecurityGroupId** - ID do security group
- **AgentRuntimeId** - ID do runtime AgentCore
- **AgentRuntimeArn** - ARN do runtime AgentCore
- **AgentRoleArn** - ARN da role de execução
- **DockerImageUri** - URI da imagem Docker no ECR
- **ECRRepositoryName** - Nome do repositório ECR

## Testando a Aplicação

### Testar Localmente

Você pode testar a aplicação Flask localmente antes de implantar:

```bash
cd agent-code
python app.py
```

Então em outro terminal:

```bash
# Testar o endpoint ping
curl http://localhost:8080/ping

# Testar o endpoint invocations
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}'
```

### Testar na AWS

Após a implantação, o container executa em subnets privadas e não é diretamente acessível pela internet. Você precisaria:

1. Configurar uma VPN ou bastion host para acessar as subnets privadas
2. Usar AWS PrivateLink ou API Gateway para expor o serviço
3. Configurar o AgentCore para invocar o runtime

## Detalhes do Container

O container:

- Executa na arquitetura **ARM64** (Graviton) para melhor desempenho e custo
- Expõe a porta **8080**
- Executa como usuário não-root (`bedrock_agentcore`)
- Inclui uma verificação de saúde no endpoint `/ping`
- Usa Python 3.11 com Flask

## Limpeza

Para destruir todos os recursos criados por esta stack:

```bash
npm run destroy
```

**Nota:** Isso irá deletar a VPC, repositório ECR (incluindo todas as imagens) e todos os recursos associados.

## Parâmetros

A stack aceita estes parâmetros na implantação:

- **AgentName** (padrão: `VpcFargateAgent`) - Nome para o runtime do agente

Para definir parâmetros durante a implantação:

```bash
npx cdk deploy --parameters AgentName=MyCustomAgent
```

## Customização

### Modificando a Aplicação

Edite `agent-code/app.py` para customizar a lógica da aplicação. A implementação atual é uma aplicação Flask simples que:

- Retorna status de saúde em `GET /ping`
- Ecoa dados recebidos em `POST /invocations`

### Modificando a Infraestrutura

Edite `lib/vpc-fargate-stack.ts` para:

- Alterar intervalos CIDR da VPC
- Ajustar número de zonas de disponibilidade
- Modificar regras do security group
- Adicionar recursos AWS adicionais

### Alterando Dependências Python

Edite `agent-code/requirements.txt` para adicionar ou atualizar pacotes Python.

## Solução de Problemas

### Falha na Construção do Docker

Certifique-se de que o Docker está em execução:

```bash
docker ps
```

### Bootstrap do CDK Necessário

Se você ver um erro sobre bootstrap:

```bash
npx cdk bootstrap
```

### Erros de Permissão

Certifique-se de que suas credenciais AWS têm permissões suficientes para:

- Criar VPCs e recursos de rede
- Criar repositórios ECR e enviar imagens
- Criar roles e políticas IAM
- Criar recursos BedrockAgentCore

## Considerações de Custo

Esta stack cria recursos que incorrem custos:

- **NAT Gateway** - Cobranças horárias + processamento de dados
- **ECR** - Custos de armazenamento para imagens Docker
- **Fargate** - Cobranças de vCPU e memória quando o runtime está ativo
- **Recursos VPC** - Cobranças de transferência de dados

## Segurança

- Container executa em **subnets privadas** sem acesso direto à internet
- Acesso de saída à internet via NAT Gateway para obter dependências
- Security group restringe tráfego de entrada à porta 8080 apenas de dentro da VPC
- Container executa como usuário não-root
- IAM role segue o princípio do privilégio mínimo

## Recursos Adicionais

- [Documentação AWS CDK](https://docs.aws.amazon.com/cdk/)
- [Documentação Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock/)
- [Documentação AWS Fargate](https://docs.aws.amazon.com/fargate/)
