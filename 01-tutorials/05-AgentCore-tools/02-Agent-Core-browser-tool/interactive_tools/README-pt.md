# Exemplos de Ferramentas do Amazon Bedrock Agentcore SDK

Esta pasta contém exemplos demonstrando o uso das ferramentas do AgentCore SDK:

## Ferramentas de Navegador

* `browser_viewer.py` - Amazon Bedrock Agentcore Browser Live Viewer com suporte adequado de dimensionamento de display.
* `run_live_viewer.py` - Script standalone para executar o Bedrock Agentcore Browser Live Viewer.

## Ferramentas de Code Interpreter

* `dynamic_research_agent_langgraph.py` - Agente de pesquisa alimentado por LangGraph com geração dinâmica de código

## Pré-requisitos

### Dependências Python
```bash
pip install -r requirements.txt
```

Pacotes necessários: fastapi, uvicorn, rich, boto3, bedrock-agentcore

### Credenciais AWS (Para Armazenamento S3)
Para armazenamento de gravação S3, certifique-se de que as credenciais AWS estejam configuradas:
```bash
aws configure
```

## Executando os Exemplos

### Browser Live Viewer
Do diretório `02-Agent-Core-browser-tool`:
```bash
python -m interactive_tools.run_live_viewer
```

### Dynamic Research Agent
Do diretório `02-Agent-Core-browser-tool`:
```bash
python -m interactive_tools.dynamic_research_agent_langgraph
```

### Acesso ao Modelo Bedrock
O exemplo do agente de pesquisa dinâmica usa modelos Claude no Amazon Bedrock:
- Você precisa de acesso aos modelos Anthropic Claude em sua conta AWS
- O modelo padrão é `global.anthropic.claude-haiku-4-5-20251001-v1:0`
- Você pode alterar o modelo modificando esta linha em `dynamic_research_agent_langgraph.py`:
  ```python
  # Linha 38 em DynamicResearchAgent.__init__()
  self.llm = ChatBedrockConverse(
      model="global.anthropic.claude-haiku-4-5-20251001-v1:0", # <- Altere isto para o modelo de sua preferência
      region_name=region
  )
  ```
- Solicite acesso ao modelo no [console do Amazon Bedrock](https://console.aws.amazon.com/bedrock/home#/modelaccess)

### Session Replay
Do diretório `02-Agent-Core-browser-tool/interactive_tools`:
```bash
python -m live_view_sessionreplay.browser_interactive_session
```

## Browser Live Viewer

Capacidade de visualização de navegador em tempo real usando a tecnologia Amazon DCV.

### Recursos

**Controle de Tamanho de Display**
- 1280×720 (HD)
- 1600×900 (HD+) - Padrão
- 1920×1080 (Full HD)
- 2560×1440 (2K)

**Controle de Sessão**
- Take Control: Desabilita automação e permite interação manual
- Release Control: Retorna controle para automação

### Configuração
- Portas personalizadas: `BrowserViewerServer(browser_client, port=8080)`

## Gravação e Reprodução de Sessão de Navegador

Grave e reproduza sessões de navegador para depuração, teste e fins de demonstração.

### Limitações Importantes
Esta ferramenta grava eventos DOM usando rrweb, não fluxos de vídeo:
- O conteúdo real do navegador (canvas DCV) pode aparecer como uma caixa preta
- Para gravação de vídeo pixel-perfect, use software de gravação de tela

## Solução de Problemas

### DCV SDK Não Encontrado
Certifique-se de que os arquivos do DCV SDK estejam colocados em `interactive_tools/static/dcvjs/`

### Sessão de Navegador Não Visível
- Verifique o console do navegador (F12) para erros
- Certifique-se de que as credenciais AWS tenham as permissões adequadas

### Gravação Não Encontrada Durante Reprodução
- Verifique o caminho exato mostrado quando a gravação foi salva
- Para gravações S3, use a URL S3 completa
- Certifique-se de que o arquivo existe usando comandos `aws s3 ls` ou `ls`

### Erros de Acesso S3
- Verifique se as credenciais AWS estão configuradas
- Verifique as permissões IAM para operações S3
- Certifique-se de que o nome do bucket seja globalmente único

## Considerações de Desempenho
- A gravação adiciona sobrecarga ao desempenho do navegador
- Tamanhos de arquivo tipicamente 1-10MB por minuto
- Uploads S3 acontecem após a gravação parar
- A reprodução requer baixar o arquivo inteiro primeiro

## Notas de Arquitetura
- O visualizador ao vivo usa FastAPI para servir URLs DCV pré-assinadas
- A gravação captura eventos DOM via biblioteca rrweb
- A reprodução usa rrweb-player para playback
- Todos os componentes compartilham a mesma instância BrowserClient
- Design modular permite uso independente de cada componente
