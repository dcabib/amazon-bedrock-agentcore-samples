# AgentCore Browser Tool — Ações em Nível de SO (API InvokeBrowser)

Este tutorial demonstra como usar **ações em nível de SO** com Amazon Bedrock AgentCore Browser Tool via a API `InvokeBrowser`, usando chamadas REST assinadas com SigV4.

## Visão Geral

Ações em nível de SO permitem que você execute operações brutas de mouse, teclado, screenshot e scroll diretamente no sandbox do navegador — ignorando completamente a camada de automação CDP/Playwright. Isso é útil para interagir com:

- **Diálogos nativos do SO** — prompts de upload/download de arquivo, diálogos de impressão, pop-ups de autenticação
- **Elementos chrome do navegador** — barra de endereços, popups de extensão, banners de permissão
- **Atalhos de teclado** — Ctrl+S, Ctrl+A, Alt+Tab que automação baseada em CDP não pode enviar para o SO
- **Conteúdo Canvas / WebGL** — onde seletores DOM não existem
- **Qualquer elemento** que resiste à automação baseada em CDP

## Casos de Uso

- Automatizar diálogos de upload de arquivo que Playwright não consegue alcançar
- Enviar atalhos de teclado em nível de SO (Ctrl+S, Ctrl+P) para o navegador
- Interagir com aplicações Canvas/WebGL usando coordenadas do mouse
- Tirar screenshots da VM completa do navegador (incluindo elementos em nível de SO)
- Operações de arrastar e soltar no nível do SO

## Arquitetura

```
┌──────────┐    SigV4-signed     ┌──────────────────────┐    OS-level     ┌─────────────────┐
│  Client   │ ──────────────────▶│  AgentCore Browser   │ ──────────────▶│  Browser Sandbox │
│ (Notebook │    REST calls      │  InvokeBrowser API   │    actions      │  (Headless VM)   │
│  / Script)│ ◀──────────────────│                      │ ◀──────────────│                  │
└──────────┘   JSON + screenshot └──────────────────────┘   results       └─────────────────┘
```

A API `InvokeBrowser` aceita requisições assinadas com SigV4 e as traduz em eventos de entrada no nível do SO executados dentro de uma VM sandbox isolada do navegador.

## Primeiros Passos

### Pré-requisitos

- Python 3.10 ou posterior
- Uma conta AWS com acesso ao Amazon Bedrock AgentCore habilitado
- Credenciais AWS configuradas (`aws sts get-caller-identity`)
- Uma região AWS onde Amazon Bedrock AgentCore está disponível

> **Nota:** O notebook cria todos os recursos necessários (função IAM, navegador personalizado) automaticamente. Você não precisa pré-criar nenhum recurso.

### Instalação

```bash
pip install -r requirements.txt
```

### Executar

```bash
jupyter notebook browser-os-actions.ipynb
```

Execute as células sequencialmente. O notebook passa por configuração, ações em nível de SO e limpeza.

## Passo a Passo do Notebook

O notebook [browser-os-actions.ipynb](browser-os-actions.ipynb) demonstra:

### Configuração

- Cria uma função IAM de execução com uma política de confiança para `bedrock-agentcore.amazonaws.com` e permissões `InvokeBrowser`, `StartBrowserSession`, `StopBrowserSession`
- Cria um AgentCore Browser personalizado com configuração de rede pública
- Inicia uma sessão de navegador com ações em nível de SO habilitadas

### Ações em Nível de SO

1. **Ações de mouse** — Clique (esquerdo, direito, meio, duplo clique), mover e operações de arrastar em coordenadas específicas da tela
2. **Ações de scroll** — Scroll vertical e horizontal com deltas configuráveis
3. **Ações de teclado** — Digitação de texto, pressionamento de teclas (Enter, Tab, Escape, Backspace, setas) e atalhos de teclado (Ctrl+S, Ctrl+P, Ctrl+Shift+I)
4. **Screenshots** — Capturar a tela completa da VM do navegador em formato PNG e exibir inline

### Limpeza

Para a sessão do navegador, deleta o navegador personalizado e remove a função e política IAM.

## Ações de Exemplo

```python
# Clique do mouse
invoke(endpoint, sid, {"mouseClick": {"x": 600, "y": 370, "button": "LEFT"}}, ...)

# Digitação no teclado
invoke(endpoint, sid, {"keyType": {"text": "Hello World"}}, ...)

# Atalho de teclado
invoke(endpoint, sid, {"keyShortcut": {"keys": ["ctrl", "s"]}}, ...)

# Screenshot
invoke(endpoint, sid, {"screenshot": {"format": "PNG"}}, ...)

# Scroll do mouse
invoke(endpoint, sid, {"mouseScroll": {"x": 500, "y": 300, "deltaX": 0, "deltaY": -500}}, ...)
```

## Arquivos

| Arquivo | Descrição |
|---------|-----------|
| `browser-os-actions.ipynb` | Notebook tutorial interativo com configuração, ações em nível de SO e limpeza |
| `helpers/browser.py` | Funções auxiliares para requisições assinadas com SigV4 e gerenciamento de sessão |
| `helpers/utils.py` | Utilitários de criação e limpeza de função IAM |
| `requirements.txt` | Dependências Python |
| `.env_sample` | Template para variáveis de ambiente de credenciais AWS |
| `README-pt.md` | Este arquivo |

## Considerações de Segurança

- Todas as chamadas de API usam autenticação SigV4 — requisições não autenticadas são rejeitadas com HTTP 403
- Cada sessão de navegador executa em uma VM sandbox isolada com mapeamento 1:1 sessão-para-VM
- Função IAM segue privilégio mínimo com apenas permissões `InvokeBrowser`, `StartBrowserSession` e `StopBrowserSession`
- Nunca faça commit de credenciais AWS — use `.env` (excluído via `.gitignore`) ou `isengardcli creds`

## Recursos Adicionais

- [Documentação Amazon Bedrock AgentCore Browser](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-tool.html)
- [Amazon Bedrock AgentCore Python SDK](https://github.com/aws/bedrock-agentcore-sdk-python)
