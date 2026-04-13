# Ferramenta Browser do AgentCore com Perfis de Navegador

Este exemplo demonstra como usar [perfis de navegador](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-profiles.html) com a Ferramenta Browser do Amazon Bedrock AgentCore. Os perfis de navegador permitem persistir e reutilizar dados de sessão do navegador (cookies, armazenamento local) em múltiplas sessões.

## Visão Geral

Os perfis de navegador permitem:
- **Persistir dados de sessão**: Salvar cookies e armazenamento local
- **Simular comportamento do usuário**: Testar fluxos de trabalho que requerem estado persistente do navegador
- **Compartilhar contexto**: Usar o mesmo perfil em múltiplas sessões de navegador

## Casos de Uso

- Testes de e-commerce com carrinhos de compras persistentes
- Testar fluxos de trabalho autenticados sem re-login
- Jornadas de usuário em múltiplas etapas que abrangem várias sessões

## Começando

### Pré-requisitos

Antes de começar, você deve navegar pelo [sample-ecommerce](sample-ecommerce/README-pt.md) e seguir as instruções para implantar o e-commerce simulado que será usado neste exemplo.

### Instalação

```bash
pip install -r requirements.txt
```

## Passo a Passo do Notebook

O notebook [browser-profile.ipynb](browser-profile-pt.ipynb) demonstra:

### 1. Configuração
- Criar bucket S3 para gravações do navegador
- Criar role IAM com permissões necessárias
- Criar Browser personalizado do AgentCore
- Criar perfil de navegador

### 2. Primeira Sessão
- Iniciar sessão do navegador
- Navegar para o DNS do CloudFront (que aponta para o bucket S3 com nosso e-commerce simulado)
- Adicionar produtos ao carrinho
- **Salvar sessão no perfil**
- Encerrar sessão

### 3. Segunda Sessão
- Iniciar nova sessão **com perfil salvo**
- Navegar para o carrinho
- Verificar que o produto persiste da sessão anterior

### 4. Opcional: Baixar Gravações
- Baixar gravações de sessão do S3
- Converter para formato rrweb
- Reproduzir sessão no notebook

### 5. Solução de Problemas
- Perfil não carregando: Certifique-se de que o perfil foi salvo antes de encerrar a sessão
- Erros de permissão: Verifique se a role IAM tem a permissão SaveBrowserSessionProfile
- Timeout da sessão: Sessões de navegador têm duração máxima; salve perfis antes do timeout
- **Cookies Expirados:** Cookies têm seus próprios tempos de expiração definidos pelos sites. Os perfis de navegador preservam cookies, mas cookies expirados são automaticamente removidos pelo navegador de acordo com suas datas de expiração

## Arquivos

- **browser-profile.ipynb**: Notebook tutorial completo com exemplos passo a passo
- **browser_helper.py**: Funções auxiliares para assinatura SigV4 e geração de URL WebSocket
- **requirements.txt**: Dependências Python

## Conceitos Principais

### Perfil de Navegador
Um perfil de navegador armazena informações da sessão incluindo:
- Cookies
- Armazenamento local

### Ciclo de Vida do Perfil
1. **Criar perfil**: `create_browser_profile()`
2. **Salvar sessão**: `save_browser_session_profile()` - captura o estado atual da sessão
3. **Carregar perfil**: `start_browser_session(profileConfiguration={...})` - restaura o estado salvo
4. **Excluir perfil**: `delete_browser_profile()` - limpeza

## Permissões IAM

A role de execução requer:
```json
{
  "Effect": "Allow",
  "Action": [
    "bedrock-agentcore:StartBrowserSession",
    "bedrock-agentcore:SaveBrowserSessionProfile"
  ],
  "Resource": [
    "arn:aws:bedrock-agentcore:REGION:ACCOUNT:browser-profile/PROFILE_NAME",
    "arn:aws:bedrock-agentcore:REGION:ACCOUNT:browser-custom/BROWSER_NAME"
  ]
}
```

## Limpeza

Para remover todos os recursos:
```python
# Excluir navegador
browser_boto3.delete_browser(browserId=browser_id)

# Excluir perfil
browser_boto3.delete_browser_profile(profileId=profile_id)

# Excluir role IAM (via console ou CLI)
# Excluir bucket S3 (via console ou CLI)
```

## Considerações de Segurança

- Perfis de navegador podem conter dados de sessão sensíveis
- Use políticas IAM apropriadas para restringir acesso ao perfil
- Considere políticas de retenção de perfis para conformidade
- Gravações armazenadas no S3 devem ter criptografia adequada e controles de acesso

## Solução de Problemas

**Perfil não carregando**: Certifique-se de que o perfil foi salvo antes de encerrar a sessão

**Erros de permissão**: Verifique se a role IAM tem a permissão `SaveBrowserSessionProfile`

**Timeout da sessão**: Sessões de navegador têm duração máxima; salve perfis antes do timeout

## Recursos Adicionais

- [Documentação do AgentCore Browser](https://docs.aws.amazon.com/bedrock/latest/userguide/agentcore-browser.html)
- [Documentação do Playwright](https://playwright.dev/docs/intro)
- [rrweb Player](https://github.com/rrweb-io/rrweb)
