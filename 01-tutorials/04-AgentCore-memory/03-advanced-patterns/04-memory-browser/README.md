# Painel AgentCore Memory

Um painel leve em React + FastAPI para navegar pelos dados do AWS Bedrock AgentCore Memory.

**📦 Tamanho do Repositório**: ~2MB (dependências excluídas - veja instruções de configuração abaixo)

## ✨ Funcionalidades Principais

- **Configuração Dinâmica**: ID de Memória, ID de Ator e ID de Sessão inseridos pela interface
- **Memória de Curto Prazo**: Consultar eventos e turnos de conversa
- **Memória de Longo Prazo**: Navegar por fatos, preferências e resumos
- **Busca em Tempo Real**: Filtragem de conteúdo com resultados ao vivo



## 📋 Pré-requisitos

- **Node.js** 16+
- **Python** 3.8+
- **AWS CLI** configurado com credenciais
- Acesso ao **AWS Bedrock AgentCore Memory**

## 🔑 Configuração de Credenciais AWS

### Passo 1: Instalar AWS CLI
```bash
# macOS
brew install awscli

# Linux
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Windows
# Baixe e execute o instalador MSI do AWS CLI no site da AWS
```

### Passo 2: Configurar Credenciais AWS
Escolha um destes métodos:

#### Opção A: AWS Configure (Recomendado)
```bash
aws configure
```
Insira:
- AWS Access Key ID
- AWS Secret Access Key
- Região padrão (ex.: `us-east-1`)
- Formato de saída padrão (ex.: `json`)

#### Opção B: Variáveis de Ambiente
```bash
export AWS_ACCESS_KEY_ID=seu-access-key-id
export AWS_SECRET_ACCESS_KEY=sua-secret-access-key
export AWS_DEFAULT_REGION=us-east-1
```

#### Opção C: Arquivo de Credenciais AWS
Crie `~/.aws/credentials`:
```ini
[default]
aws_access_key_id = seu-access-key-id
aws_secret_access_key = sua-secret-access-key
```

Crie `~/.aws/config`:
```ini
[default]
region = us-east-1
output = json
```

### Passo 3: Verificar Acesso AWS
```bash
# Testar conexão AWS
aws sts get-caller-identity

# Testar acesso ao Bedrock
aws bedrock list-foundation-models --region us-east-1
```

### Passo 4: Permissões IAM Necessárias
Seu usuário/role AWS precisa destas permissões:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "bedrock-agentcore:ListMemoryRecords",
                "bedrock-agentcore:ListEvents", 
                "bedrock-agentcore:GetLastKTurns",
                "bedrock-agentcore:RetrieveMemories",
                "bedrock-agentcore:GetMemoryStrategies"
            ],
            "Resource": "*"
        }
    ]
}
```

## 🚀 Guia de Início Rápido

### Passo 1: Clonar e Configurar
```bash
# Clonar o repositório
git clone <repository-url>
cd 01-tutorials/04-AgentCore-memory/03-advanced-patterns/04-memory-browser

# Instalar dependências do frontend (isso baixará ~200MB de pacotes)
npm install
```

**Nota**: 
- 📦 **Dependências não incluídas**: `node_modules` e `backend/venv` estão excluídos do repositório
- 🔧 **Primeira configuração**: Execute `npm install` para baixar todas as dependências do frontend
- ✅ **`.env` do Frontend**: Já configurado com valores padrão
- ❌ **`.env` do Backend**: Precisa ser criado (veja Passo 2)

### Passo 2: Configurar Variáveis de Ambiente

#### Configuração do Backend
Copie o arquivo de exemplo e customize:
```bash
# Copiar o arquivo de exemplo
cp backend/.env.example backend/.env

# Editar backend/.env e definir seu perfil AWS (se necessário)
# AWS_PROFILE=seu-nome-de-perfil
```

O arquivo `backend/.env` deve conter:
```env
# Configuração AWS (região será detectada automaticamente do AWS CLI/perfil se não definida)
# AWS_REGION=us-east-1

# Configuração do Servidor
# Segurança: Use 127.0.0.1 para desenvolvimento local (recomendado)
# Use 0.0.0.0 apenas se precisar acessar de outras máquinas na sua rede
BACKEND_HOST=127.0.0.1
BACKEND_PORT=8000
DEBUG=true

# Configuração CORS
ALLOWED_ORIGINS=http://localhost:3000,http://127.0.0.1:3000

# Opcional: Perfil AWS (se usando múltiplos perfis)
# AWS_PROFILE=seu-nome-de-perfil
```

**Nota de Segurança**: O backend agora se vincula a `127.0.0.1` (apenas localhost) por padrão para segurança. Isso evita exposição a todas as interfaces de rede. Se você precisar acessar o backend de outras máquinas na sua rede, defina `BACKEND_HOST=0.0.0.0` no seu arquivo `.env`, mas esteja ciente de que isso expõe o serviço a toda sua rede.

**Nota**: A região AWS é automaticamente detectada da sua configuração AWS CLI. Defina `AWS_REGION` apenas se precisar sobrescrever o padrão.

#### Configuração do Frontend
O arquivo `.env` do frontend já está configurado com valores padrão. Você pode modificá-lo se necessário:
```env
# URL da API do Backend
REACT_APP_BACKEND_URL=http://localhost:8000

# Configurações do Painel
REACT_APP_MAX_MEMORY_ENTRIES=50
REACT_APP_REFRESH_INTERVAL=5000
```

### Passo 3: Instalar Dependências do Backend
```bash
# Navegar até o diretório do backend
cd backend

# Criar ambiente virtual Python (pacotes Python isolados)
python3 -m venv venv

# Ativar ambiente virtual
# No macOS/Linux:
source venv/bin/activate
# No Windows:
# venv\Scripts\activate

# Instalar dependências Python (~50MB de pacotes)
pip install -r requirements.txt

# Retornar à raiz do projeto
cd ..
```

**Nota**: O ambiente virtual (`backend/venv/`) está excluído do repositório para mantê-lo leve.

### Passo 4: Iniciar a Aplicação

#### Opção A: Iniciar Ambos os Serviços Juntos (Recomendado)
```bash
# Da raiz do projeto
npm run dev
```
Isso iniciará tanto o backend (FastAPI) quanto o frontend (React) simultaneamente.

#### Opção B: Iniciar Serviços Separadamente
```bash
# Terminal 1: Iniciar backend
npm run start-backend

# Terminal 2: Iniciar frontend  
npm start
```



### Passo 5: Acessar o Painel
- **Frontend**: http://localhost:3000
- **API do Backend**: http://localhost:8000
- **Documentação da API**: http://localhost:8000/docs

### Passo 6: Configurar Acesso à Memória
1. Abra o painel em http://localhost:3000
2. Insira seu **ID de Memória** e **ID de Ator** no cabeçalho
3. Clique em **Configurar** para validar o acesso
4. Comece a consultar seus dados do AgentCore Memory!

## 📊 Funcionalidades do Painel

### Memória de Curto Prazo
- Consultar eventos e turnos de conversa
- Filtrar por conteúdo, tipo de evento e papel

### Memória de Longo Prazo
- **Entrada do Usuário Necessária**: ID de Memória e Namespace (inseridos pela interface)
- Consulta baseada em namespace com filtragem de conteúdo
- Navegar por fatos, preferências e resumos

## 🔧 Resolução de Problemas

### Problemas Comuns
- **Backend não inicia**: Verifique se o ambiente virtual Python está ativado
- **Frontend não conecta**: Verifique se o backend está rodando na porta 8000
- **Erros de permissão AWS**: Execute `aws sts get-caller-identity` para verificar credenciais
- **ID de Memória não encontrado**: Verifique se o ID de Memória existe e você tem as permissões adequadas

---
