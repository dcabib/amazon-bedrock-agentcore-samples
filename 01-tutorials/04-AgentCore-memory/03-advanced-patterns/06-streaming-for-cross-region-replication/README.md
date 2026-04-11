# Replicação Cross-Region do AgentCore Memory

Replicação cross-region ativo-passivo para o [Amazon Bedrock AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html) usando o recurso de [streaming de registros de memória](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-record-streaming.html).

O AgentCore Memory armazena conhecimento de longo prazo para agentes de IA — preferências do usuário, histórico de conversas, fatos extraídos. Esses dados são críticos para a qualidade do agente. Se a região primária ficar indisponível, os agentes perdem acesso a toda a memória acumulada. Esta solução fornece replicação quase em tempo real para uma região secundária para que você possa fazer failover em segundos.

## Arquitetura

![Arquitetura](images/architecture.png)

### Como Funciona

1. O AgentCore Memory primário tem **streaming habilitado** — toda vez que um registro de memória é criado ou atualizado, um evento é publicado em um Kinesis Data Stream
2. Um **consumidor Lambda** lê do Kinesis via Event Source Mapping (ESM), decodifica o evento e chama `BatchCreateMemoryRecords` na Memória da região secundária
3. Para evitar loops infinitos (secundário replicando de volta para o primário), registros replicados usam um **prefixo de namespace `replicated/`** — o Lambda ignora qualquer evento com este prefixo
4. A região secundária tem toda a mesma infraestrutura pré-implantada (Kinesis, Lambda, IAM), mas **streaming está DESLIGADO** — então o Lambda fica inativo com custo zero
5. **Failover** são duas chamadas de API: habilitar streaming no secundário, desabilitar no primário. Leva segundos.

### Métricas Principais

| Métrica | Valor |
|:--------|:------|
| RPO (Objetivo de Ponto de Recuperação) | 5–15 segundos |
| RTO (Objetivo de Tempo de Recuperação) | 15–30 segundos |
| Mecanismo de failover | Alternar streaming via API `update-memory` |
| Prevenção de loop | Prefixo de namespace `replicated/` |
| Resolução de conflitos | Consolidação nativa do AgentCore Memory |

## Pré-requisitos

- AWS CLI v2 configurado com permissões apropriadas
- Python 3.10+
- Acesso ao Amazon Bedrock AgentCore em `us-east-1` e `us-west-2`
- Permissões para: CloudFormation, Kinesis, Lambda, IAM, SQS, DynamoDB, S3

## Início Rápido

O notebook guia você por tudo passo a passo:

```bash
jupyter notebook 06-memory-cross-region-replication.ipynb
```

Ou implante a infraestrutura diretamente sem o notebook:

```bash
bash scripts/deploy.sh us-east-1 us-west-2
```

## Estrutura do Projeto

```
├── 06-memory-cross-region-replication.ipynb   # Tutorial principal — execute este
├── README.md
├── requirements.txt                           # boto3>=1.42.63
└── scripts/
    ├── deploy.sh                              # Orquestração de implantação
    ├── toggle-streaming.sh                    # Alternância de failover
    ├── handler.py                             # Consumidor Lambda de replicação
    ├── regional-stack.yaml                    # CloudFormation por região
    └── global-stack.yaml                      # DynamoDB Global Table
```

### O que Cada Arquivo Faz

**`06-memory-cross-region-replication.ipynb`** — O notebook tutorial autocontido. Ele implanta infraestrutura, cria registros de memória, verifica replicação, testa failover/failback e faz limpeza. É o que os usuários devem seguir.

**`scripts/deploy.sh`** — Orquestra a implantação completa da primeira vez:
1. Empacota a função Lambda e faz upload para S3 em ambas as regiões
2. Implanta uma DynamoDB Global Table para rastreamento da região ativa
3. Implanta stacks CloudFormation por região (Kinesis, Lambda, SQS DLQ, roles IAM, alarmes CloudWatch)
4. Cria instâncias AgentCore Memory — primária com streaming LIGADO, secundária sem
5. Atualiza stacks com IDs de memória cross-region para que cada Lambda saiba onde replicar
6. Alimenta a tabela de configuração DynamoDB com a região ativa

**`scripts/toggle-streaming.sh`** — Habilita ou desabilita streaming em uma instância de Memória. Este é o mecanismo de failover — habilite na nova região ativa, desabilite na antiga. Por baixo dos panos, ele chama `update-memory --stream-delivery-resources`.

**`scripts/handler.py`** — A função Lambda que consome eventos do stream Kinesis e os replica. Comportamentos principais:
- Ignora eventos `StreamingEnabled` e `MemoryRecordDeleted` (não replicáveis)
- Verifica prefixo de namespace `replicated/` para prevenir loops infinitos
- Gera IDs de requisição determinísticos para que retentativas não criem duplicatas
- Envia erros não retriáveis para SQS DLQ; levanta erros retriáveis para retentativa do ESM
- Falhas de escrita no DLQ são logadas mas nunca travam o Lambda

**`scripts/regional-stack.yaml`** — Template CloudFormation implantado em cada região. Cria:
- Kinesis Data Stream (1 shard, retenção de 24h)
- SQS Dead Letter Queue (retenção de 14 dias)
- Roles IAM para streaming de Memória e execução do Lambda
- Função Lambda com Kinesis ESM (bisect-on-error, máximo 3 retentativas)
- Alarmes CloudWatch para erros do Lambda, profundidade do DLQ e lag de replicação

**`scripts/global-stack.yaml`** — Template CloudFormation para a DynamoDB Global Table que rastreia qual região está atualmente ativa. Implantado uma vez, replicado para ambas as regiões automaticamente.

## Failover

```bash
# Failover: primário → secundário
# Habilite o secundário PRIMEIRO para evitar qualquer gap de replicação
bash scripts/toggle-streaming.sh enable us-west-2
bash scripts/toggle-streaming.sh disable us-east-1

# Failback: secundário → primário
bash scripts/toggle-streaming.sh enable us-east-1
bash scripts/toggle-streaming.sh disable us-west-2
```

A ordem importa — sempre habilite o novo caminho antes de desabilitar o antigo. Se ambas as regiões brevemente tiverem streaming ligado, a prevenção de loop lida com isso com segurança.

## Custo

### Fixo (sempre rodando)

| Recurso | Custo | Notas |
|:---------|:------|:------|
| Kinesis (1 shard × 2 regiões) | ~$22/mês | Precificação por hora de shard |
| DynamoDB Global Table | ~$0,25/mês | Registro único, sob demanda |
| Alarmes CloudWatch (3 × 2 regiões) | ~$0,60/mês | Resolução padrão |

### Variável (proporcional ao uso)

| Recurso | Custo |
|:---------|:------|
| Kinesis PutRecord | $0,014 por 1M registros |
| Invocações Lambda | $0,20 por 1M + duração |
| Escritas AgentCore Memory | Precificação por registro |

O shard Kinesis do secundário custa ~$11/mês mesmo quando inativo — este é o preço da prontidão para failover instantâneo.

## Limitações Conhecidas

- Exclusões não são replicadas (limpeza remota via consolidação do AgentCore Memory)
- Atualizações são replicadas como novos Creates (consolidação lida com deduplicação)
- Apenas conta AWS única (cross-account exigiria roles IAM adicionais)
- Failover manual (poderia ser automatizado com Route 53 health checks + Step Functions)
- `deploy.sh` é apenas para implantação da primeira vez — reimplantação requer deletar instâncias de Memória primeiro
