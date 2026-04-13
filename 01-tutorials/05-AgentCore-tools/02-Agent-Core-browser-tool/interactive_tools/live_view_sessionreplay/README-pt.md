# Exemplos de Ferramentas do Amazon Bedrock AgentCore SDK

Esta pasta contém exemplos demonstrando o uso das ferramentas do Amazon Bedrock AgentCore SDK:

## Ferramentas de Navegador

* `browser_viewer_replay.py` - Amazon Bedrock AgentCore Browser Live Viewer com suporte adequado de dimensionamento de display.
* `browser_interactive_session.py` - Experiência completa de navegador de ponta a ponta com capacidades de visualização ao vivo, gravação e reprodução.
* `session_replay_viewer.py` - Visualizador para reproduzir sessões de navegador gravadas.
* `view_recordings.py` - Script standalone para visualizar sessões gravadas do S3.

## Pré-requisitos

### Dependências Python
```bash
pip install -r requirements.txt
```

Pacotes necessários: fastapi, uvicorn, rich, boto3, bedrock-agentcore

### Credenciais AWS
Certifique-se de que as credenciais AWS estejam configuradas:
```bash
aws configure
```

## Executando os Exemplos

### Experiência Completa de Navegador com Gravação e Reprodução
Do diretório `02-Agent-Core-browser-tool/interactive_tools`:
```bash
python -m live_view_sessionreplay.browser_interactive_session
```

### Visualizar Gravações
Do diretório `02-Agent-Core-browser-tool/interactive_tools`:
```bash
python -m live_view_sessionreplay.view_recordings --bucket SEU_BUCKET --prefix SEU_PREFIX
```

## Experiência Completa de Navegador com Gravação e Reprodução

Execute um workflow completo de ponta a ponta que inclui visualização de navegador ao vivo, gravação automática para S3 e reprodução de sessão integrada.

### Recursos
- Crie sessões de navegador com gravação automática para S3
- Visualização ao vivo com controle interativo (take/release)
- Ajuste a resolução do display dinamicamente
- Gravação automática de sessão para S3
- Visualizador de reprodução de sessão integrado para assistir gravações

### Como Funciona
1. O script cria um navegador com gravação habilitada
2. Uma sessão de navegador é iniciada e exibida no seu navegador local
3. Você pode assumir controle manual do navegador ou deixar a automação executar
4. Todas as ações são automaticamente gravadas no S3
5. Após encerrar a sessão (Ctrl+C), um visualizador de reprodução abre mostrando sua gravação

### Variáveis de Ambiente
- `AWS_REGION` - Região AWS (padrão: us-west-2)
- `AGENTCORE_ROLE_ARN` - IAM role ARN para execução do navegador (padrão: gerado automaticamente a partir do ID da conta)
- `RECORDING_BUCKET` - Bucket S3 para gravações (padrão: session-record-test-{ACCOUNT_ID})
- `RECORDING_PREFIX` - Prefixo S3 para gravações (padrão: replay-data)

### Permissões IAM Necessárias
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:PutObject",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::session-record-test-*",
                "arn:aws:s3:::session-record-test-*/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "bedrock:*",
            "Resource": "*"
        }
    ]
}
```

## Visualizador Standalone de Reprodução de Sessão

Uma ferramenta separada para visualizar sessões de navegador gravadas diretamente do S3 sem criar um novo navegador.

### Recursos
- Conecte diretamente ao S3 para visualizar gravações
- Visualize qualquer gravação passada especificando seu ID de sessão
- Encontra automaticamente a gravação mais recente se nenhum ID de sessão for fornecido

### Uso

```bash
# Visualizar a gravação mais recente em um bucket
python -m live_view_sessionreplay.view_recordings --bucket session-record-test-123456789012 --prefix replay-data

# Visualizar uma gravação específica
python -m live_view_sessionreplay.view_recordings --bucket session-record-test-123456789012 --prefix replay-data --session 01JZVDG02M8MXZY2N7P3PKDQ74

# Usar um perfil AWS específico
python -m live_view_sessionreplay.view_recordings --bucket session-record-test-123456789012 --prefix replay-data --profile my-profile
```

### Encontrando Gravações

Listar gravações S3:
```bash
aws s3 ls s3://session-record-test-123456789012/replay-data/ --recursive
```

## Solução de Problemas

### DCV SDK Não Encontrado
Certifique-se de que os arquivos do DCV SDK estejam colocados em `interactive_tools/static/dcvjs/`

### Sessão de Navegador Não Visível
- Verifique se o DCV SDK está instalado corretamente
- Verifique o console do navegador (F12) para erros
- Certifique-se de que as credenciais AWS tenham as permissões adequadas

### Gravação Não Está Funcionando
- Verifique se o bucket S3 existe e está acessível
- Verifique as permissões IAM para operações S3
- Certifique-se de que a role de execução tenha as permissões apropriadas

### Problemas com Reprodução de Sessão
- Verifique se as gravações existem no S3 (use AWS CLI ou console)
- Verifique erros nos logs do console
- Certifique-se de que a política do bucket S3 permita leitura de objetos

### Erros de Acesso S3
- Verifique se as credenciais AWS estão configuradas
- Verifique as permissões IAM para operações S3
- Certifique-se de que o nome do bucket seja globalmente único

## Notas de Arquitetura
- O visualizador ao vivo usa FastAPI para servir URLs DCV pré-assinadas
- A gravação é tratada diretamente pelo serviço de navegador no data plane
- A reprodução usa rrweb-player para playback de eventos gravados
- Todos os componentes podem funcionar juntos ou independentemente
