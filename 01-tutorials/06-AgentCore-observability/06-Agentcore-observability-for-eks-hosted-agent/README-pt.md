# Implantando um Agente Strands no Amazon EKS

Este exemplo demonstra como implantar uma aplicação Python construída com o [Strands Agents SDK](https://github.com/strands-agents/sdk-python) no Amazon EKS. O exemplo implanta uma aplicação de agente de pesquisa de viagens que é executada como um serviço containerizado no Amazon EKS com um Application Load Balancer.

A aplicação é construída com FastAPI e fornece um endpoint `/travel` que retorna informações de viagem com base no prompt fornecido.

## Pré-requisitos

- [AWS CLI](https://aws.amazon.com/cli/) instalado e configurado
- [eksctl](https://eksctl.io/installation/) (v0.208.x ou posterior) instalado
- [Helm](https://helm.sh/) (v3 ou posterior) instalado
- [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html) instalado
- Uma das opções:
    - [Podman](https://podman.io/) instalado e em execução
    - (ou) [Docker](https://www.docker.com/) instalado e em execução
- Modelo Anthropic Claude do Amazon Bedrock habilitado no seu ambiente AWS

## Início Rápido (Implantação Automatizada)

Para uma experiência de implantação automatizada, use o notebook Jupyter incluído:

```bash
# Navegue até este diretório
cd strands-travel-agent-eks

# Inicie o Jupyter
jupyter notebook deploy.ipynb
```

O notebook automatiza todo o processo de implantação incluindo:
- Criação do grupo de logs do CloudWatch
- Criação do cluster EKS
- Build da imagem Docker e push para o ECR
- Configuração de política IAM e Pod Identity
- Implantação do chart Helm
- Port-forwarding e teste do agente

> **Nota:** O addon de Observabilidade do CloudWatch (Seção 8 no notebook) é **opcional**. Ele NÃO é necessário para a Observabilidade do Bedrock AgentCore. O AgentCore envia telemetria diretamente para o CloudWatch usando a configuração OTEL no Dockerfile.

**Variáveis de Ambiente (Opcional):**

Personalize a implantação definindo estas variáveis de ambiente antes de executar o notebook:

| Variável | Padrão | Descrição |
|----------|--------|-----------|
| `AWS_REGION` | `us-east-1` | Região AWS para implantação |
| `CLUSTER_NAME` | `eks-strands-agents-demo` | Nome do cluster EKS |
| `SERVICE_NAME` | `strands-agents-travel` | Nome do serviço para o release Helm |
| `LOG_GROUP_NAME` | `/strands-agents/travel` | Grupo de logs do CloudWatch |
| `LOG_STREAM_NAME` | `agent-logs` | Stream de logs do CloudWatch |
| `METRIC_NAMESPACE` | `StrandsAgents/Travel` | Namespace de métricas do CloudWatch |
| `LOCAL_PORT` | `8080` | Porta local para port-forwarding |

## Estrutura do Projeto

```
.
├── README.md
├── deploy.ipynb              # Notebook de implantação automatizada
├── chart/                    # Chart Helm para implantação no Kubernetes
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
└── docker/                   # Arquivos de container Docker
    ├── Dockerfile
    ├── app/
    │   └── app.py           # Aplicação FastAPI de agente de viagem
    └── requirements.txt
```

## Implantação Manual

As seções a seguir descrevem os passos de implantação manual. Use-os se preferir comandos CLI ao notebook automatizado.

### Configuração

Antes de construir a imagem Docker, atualize os seguintes valores em `docker/Dockerfile`:

| Variável | Descrição | Ação Necessária |
|----------|-----------|-----------------|
| `OTEL_RESOURCE_ATTRIBUTES` | Nome do serviço para Observabilidade do AgentCore | Substitua `<YOUR_SERVICE_NAME>` pelo nome do seu serviço |
| `OTEL_EXPORTER_OTLP_LOGS_HEADERS` | Configuração de observabilidade OpenTelemetry | Substitua `<YOUR_LOG_GROUP>`, `<YOUR_LOG_STREAM>` e `<YOUR_METRIC_NAMESPACE>` pelos seus valores |

A aplicação também suporta estas variáveis de ambiente em tempo de execução (os padrões são definidos em `docker/app/app.py`):

| Variável | Descrição | Padrão |
|----------|-----------|--------|
| `MODEL_ID` | ID do modelo Amazon Bedrock | `us.anthropic.claude-haiku-4-5-20251001-v1:0` |
| `MODEL_TEMPERATURE` | Temperatura do modelo para respostas | `0` |
| `MODEL_MAX_TOKENS` | Máximo de tokens na resposta | `1028` |

### Criar cluster EKS Auto Mode

Defina as variáveis de ambiente:
```bash
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query 'Account' --output text)
export AWS_REGION=us-east-1
export CLUSTER_NAME=eks-strands-agents-demo
```

Crie o cluster EKS Auto Mode:
```bash
eksctl create cluster --name $CLUSTER_NAME --enable-auto-mode
```

Configure o contexto do kubeconfig:
```bash
aws eks update-kubeconfig --name $CLUSTER_NAME
```

### Construindo e Enviando Imagem Docker para o ECR

Siga estes passos para construir a imagem Docker e enviá-la para o Amazon ECR:

1. Autentique-se no Amazon ECR:
```bash
aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
```

2. Crie o repositório ECR se ele não existir:
```bash
aws ecr create-repository --repository-name strands-agents-travel --region ${AWS_REGION}
```

3. Construa a imagem Docker:
```bash
docker build --platform linux/amd64 -t strands-agents-travel:latest docker/
```

4. Aplique a tag da imagem para o ECR:
```bash
docker tag strands-agents-travel:latest ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/strands-agents-travel:latest
```

5. Envie a imagem para o ECR:
```bash
docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/strands-agents-travel:latest
```

### Configurar EKS Pod Identity para acessar o Amazon Bedrock

Crie uma política IAM para permitir InvokeModel e InvokeModelWithResponseStream para todos os modelos do Amazon Bedrock:
```bash
cat > bedrock-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel",
        "bedrock:InvokeModelWithResponseStream"
      ],
      "Resource": "*"
    }
  ]
}
EOF

aws iam create-policy \
  --policy-name strands-agents-travel-bedrock-policy \
  --policy-document file://bedrock-policy.json
rm -f bedrock-policy.json
```

Crie uma associação de EKS Pod Identity:
```bash
eksctl create podidentityassociation --cluster $CLUSTER_NAME \
  --namespace default \
  --service-account-name strands-agents-travel \
  --permission-policy-arns arn:aws:iam::$AWS_ACCOUNT_ID:policy/strands-agents-travel-bedrock-policy \
  --role-name eks-strands-agents-travel
```

### Implantar a aplicação strands-agents-travel

Implante o chart Helm com a imagem do ECR:
```bash
helm install strands-agents-travel ./chart \
  --set image.repository=${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/strands-agents-travel \
  --set image.tag=latest
```

Aguarde o Deployment estar disponível (Pods em execução):
```bash
kubectl wait --for=condition=available deployments strands-agents-travel --all
```

### Testar o Agente

Usando port-forward do Kubernetes:
```bash
kubectl --namespace default port-forward service/strands-agents-travel 8080:80 &
```

Chame o serviço de viagem:
```bash
curl -X POST \
  http://localhost:8080/travel \
  -H 'Content-Type: application/json' \
  -d '{"prompt": "What are the best places to visit in Tokyo in March?"}'
```

### Expor Agente através do Application Load Balancer

[Crie um IngressClass para configurar um Application Load Balancer](https://docs.aws.amazon.com/eks/latest/userguide/auto-configure-alb.html):
```bash
cat <<EOF | kubectl apply -f -
apiVersion: eks.amazonaws.com/v1
kind: IngressClassParams
metadata:
  name: alb
spec:
  scheme: internet-facing
EOF
```

```bash
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: alb
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"
spec:
  controller: eks.amazonaws.com/alb
  parameters:
    apiGroup: eks.amazonaws.com
    kind: IngressClassParams
    name: alb
EOF
```

Atualize a implantação Helm para criar Ingress usando o IngressClass criado:
```bash
helm upgrade strands-agents-travel ./chart \
  --set image.repository=${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/strands-agents-travel \
  --set image.tag=latest \
  --set ingress.enabled=true \
  --set ingress.className=alb
```

Obtenha a URL do ALB:
```bash
export ALB_URL=$(kubectl get ingress strands-agents-travel -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
echo "O ALB compartilhado está disponível em: http://$ALB_URL"
```

Aguarde o ALB estar ativo:
```bash
aws elbv2 wait load-balancer-available --load-balancer-arns $(aws elbv2 describe-load-balancers --query 'LoadBalancers[?DNSName==`'"$ALB_URL"'`].LoadBalancerArn' --output text)
```

Chame o serviço de viagem via Application Load Balancer:
```bash
curl -X POST \
  http://$ALB_URL/travel \
  -H 'Content-Type: application/json' \
  -d '{"prompt": "What are the top attractions in Barcelona?"}'
```

### Configurar Alta Disponibilidade e Resiliência

Para configurar alta disponibilidade:
- Aumente as réplicas para 3
- [Topology Spread Constraints](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/): Distribuir a carga de trabalho em múltiplas zonas de disponibilidade
- [Pod Disruption Budgets](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#pod-disruption-budgets): Tolerar minAvailable de 1

```bash
helm upgrade strands-agents-travel ./chart -f - <<EOF
image:
  repository: ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/strands-agents-travel
  tag: latest

ingress:
  enabled: true
  className: alb

replicaCount: 3

topologySpreadConstraints:
  - maxSkew: 1
    minDomains: 3
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: DoNotSchedule
    labelSelector:
      matchLabels:
        app.kubernetes.io/name: strands-agents-travel
  - maxSkew: 1
    topologyKey: kubernetes.io/hostname
    whenUnsatisfiable: ScheduleAnyway
    labelSelector:
      matchLabels:
        app.kubernetes.io/instance: strands-agents-travel

podDisruptionBudget:
  enabled: true
  minAvailable: 1
EOF
```

## Limpeza

Desinstale o chart Helm:
```bash
helm uninstall strands-agents-travel
```

Exclua o cluster EKS Auto Mode:
```bash
eksctl delete cluster --name $CLUSTER_NAME --wait
```

Exclua a política IAM:
```bash
aws iam delete-policy --policy-arn arn:aws:iam::$AWS_ACCOUNT_ID:policy/strands-agents-travel-bedrock-policy
```

## Licença

Este projeto é licenciado sob a Licença Apache 2.0 - consulte o arquivo LICENSE para detalhes.
