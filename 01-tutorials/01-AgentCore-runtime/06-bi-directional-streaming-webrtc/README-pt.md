# Agente de Voz WebRTC Mínimo com KVS

Exemplo mínimo demonstrando streaming de áudio WebRTC com AWS Nova Sonic.

## Estrutura do Projeto

```
agent/
  bot.py              - Servidor FastAPI, oferta/resposta WebRTC, tratamento ICE
  kvs.py              - Canal de sinalização KVS e auxiliares de servidor TURN
  audio.py            - Reamostragem de áudio (av) e trilha de saída WebRTC (av.AudioFifo)
  nova_sonic.py       - Sessão de streaming bidirecional Nova Sonic
  requirements.txt
  Dockerfile
  .env.example
server/
  index.html          - Cliente de navegador (WebRTC + AgentCore Runtime opcional)
  server.py           - Servidor de arquivos estáticos
  requirements.txt
kvs-iam-policy.json     - Política IAM mínima para KVS
bedrock-iam-policy.json - Política IAM mínima para Nova Sonic
```

## Requisitos

- **Python 3.12+** (necessário para aws-sdk-bedrock-runtime)
- Credenciais AWS configuradas
- **VPC com saída para internet** para implantação no AgentCore Runtime (veja a configuração abaixo)

## Configuração de VPC para AgentCore Runtime

O agente precisa de saída para internet para alcançar os servidores TURN do KVS para conectividade WebRTC. Se você já tem uma VPC com uma sub-rede privada que possui acesso ao NAT gateway, pule para [Implantando no AgentCore Runtime](#implantando-no-agentcore-runtime).

### 1. Crie uma VPC com sub-redes públicas e privadas

1. Abra o [console VPC](https://console.aws.amazon.com/vpc/)
2. Clique em **Create VPC**
3. Selecione **VPC and more**
4. Defina um nome (por exemplo, `webrtc-bot-example`)
5. Mantenha o CIDR padrão (`10.0.0.0/16`)
6. Defina **Number of Availability Zones** como **1**
7. Defina **Number of public subnets** como **1**
8. Defina **Number of private subnets** como **1**
9. Defina **NAT gateways** como **In 1 AZ**
10. Clique em **Create VPC**

### 2. Anote os IDs

No console VPC, copie:
- **ID da sub-rede privada** (por exemplo, `subnet-0123456789abcdef0`) — é onde o agente é executado
- **ID do grupo de segurança** — o grupo de segurança padrão criado com a VPC (por exemplo, `sg-0123456789abcdef0`)

Você usará estes na etapa `agentcore configure` abaixo.

## Configuração Local

### 1. Agente

```bash
cd agent
python3.12 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env  # Edite com suas credenciais AWS
python bot.py          # http://localhost:8080
```

### 2. Servidor

```bash
cd server
pip install -r requirements.txt
python server.py       # http://localhost:7860
```

### 3. Teste

Abra `http://localhost:7860` e clique em "Connect".

## Implantando no AgentCore Runtime

### 1. Instale o starter toolkit

```bash
pip install bedrock-agentcore-starter-toolkit
```

### 2. Configure

Do diretório `agent/`:

```bash
cd agent

export SUBNET_IDS=subnet-0123456789abcdef0  # sub-rede privada (com NAT gateway para saída para internet)
export SECURITY_GROUP_ID=sg-0123456789abcdef0

agentcore configure \
  -e bot.py \
  --deployment-type container \
  --disable-memory \
  --vpc \
  --subnets $SUBNET_IDS \
  --security-groups $SECURITY_GROUP_ID \
  --non-interactive
```

O modo de rede VPC é necessário porque o modo de rede PÚBLICO não suporta conectividade UDP de saída. 

### 3. Implante

```bash
agentcore deploy --env KVS_CHANNEL_NAME=voice-agent-minimal --env AWS_REGION=us-west-2
```

Isso constrói um contêiner ARM64 via CodeBuild (Docker não é necessário localmente) e o implanta no AgentCore Runtime. Observe o ARN na saída.

### 4. Anexe permissões IAM

A role de execução criada pelo toolkit precisa de permissões KVS e Bedrock. Primeiro, atualize `ACCOUNT_ID` em `kvs-iam-policy.json` e `bedrock-iam-policy.json` com o ID da sua conta AWS. Depois, substitua `ROLE_NAME` pelo nome da role da saída do deploy (por exemplo, `AmazonBedrockAgentCoreSDKRuntime-us-west-2-9d74932bdb`):

```bash
ROLE_NAME=ROLE_HERE

aws iam put-role-policy \
  --role-name $ROLE_NAME \
  --policy-name kvs-access \
  --policy-document file://kvs-iam-policy.json

aws iam put-role-policy \
  --role-name $ROLE_NAME \
  --policy-name bedrock-nova-sonic \
  --policy-document file://bedrock-iam-policy.json
```

### 5. Teste

Insira o ARN do agente da saída de `agentcore deploy` no cliente do navegador em `http://localhost:7860` junto com as credenciais AWS, depois clique em Connect. Uma vez conectado, fale no seu microfone — o agente responderá com áudio falado em tempo real.

### Limpeza

```bash
agentcore destroy
```

## Como Funciona

### Fluxo de Áudio

**Navegador → Nova Sonic:**
1. WebRTC captura áudio do microfone
2. `aiortc` recebe frames de áudio no agente
3. `av.AudioResampler` converte para PCM 16kHz/16-bit/mono
4. Codificado em Base64 e transmitido para Nova Sonic

**Nova Sonic → Navegador:**
1. Agente recebe chunks de áudio do Nova Sonic
2. Bytes PCM brutos em buffer em `av.AudioFifo`
3. `OutputTrack` serve frames de 20ms de tamanho fixo para WebRTC
4. Navegador reproduz áudio via elemento `<audio>`

### Configuração de Áudio

| Parâmetro | Valor |
|-----------|-------|
| Taxa de Amostragem de Entrada | 16kHz |
| Taxa de Amostragem de Saída | 24kHz |
| Formato | PCM 16-bit mono |
| Modelo | amazon.nova-2-sonic-v1:0 |
| Voz | matthew |

## Dependências Principais

| Pacote | Propósito |
|---------|---------|
| `aws-sdk-bedrock-runtime` | Streaming Nova Sonic (requer Python 3.12+) |
| `aiortc` | Conexões peer WebRTC |
| `av` | Reamostragem de áudio e buffering de frames (FFmpeg) |
| `boto3` | Canal de sinalização KVS e servidores TURN |
| `fastapi` / `uvicorn` | Servidor HTTP |

## Permissões IAM

O agente precisa de permissões KVS para acesso ao servidor TURN. Veja `kvs-iam-policy.json` para a política mínima — substitua `ACCOUNT_ID` pelo ID da sua conta AWS.

Além disso, o agente precisa de permissão `bedrock:InvokeModelWithBidirectionalStream` para o modelo Nova Sonic.

## Solução de Problemas

**Erro de versão do Python** (`Could not find aws-sdk-bedrock-runtime`):
Use Python 3.12+.

**Áudio não funciona:**
- Verifique as permissões do microfone no navegador
- Verifique se as credenciais AWS têm acesso ao Bedrock
- Execute o agente com `-v` para log detalhado

**Falha na conexão:**
- Certifique-se de que tanto o agente quanto o servidor estão em execução
- Verifique as permissões IAM do KVS
- Verifique a conectividade do servidor TURN

## Referência

Baseado em: https://github.com/aws-samples/sample-nova-sonic-speech2speech-webrtc
