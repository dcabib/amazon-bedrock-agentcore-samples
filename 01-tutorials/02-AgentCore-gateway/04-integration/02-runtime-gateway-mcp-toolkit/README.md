# AgentCore Runtime e Gateway MCP Server Toolkit

Um toolkit configurável para configurar rapidamente um Servidor MCP Personalizado na infraestrutura do AgentCore Runtime e Gateway.

Construído sobre o [bedrock-agentcore-starter-toolkit](https://github.com/aws/bedrock-agentcore-starter-toolkit/) para fornecer uma experiência simplificada para implantação de servidores MCP.

## Visão Geral

- Você quer implantar rapidamente seu servidor MCP personalizado no AgentCore sem escrever nenhum código?
- Você quer consolidar seus múltiplos servidores/ferramentas MCP personalizados em uma única URL que expõe todas as ferramentas de vários Servidores MCP?
- Você quer acesso seguro aos seus Servidores MCP?

Este toolkit ajuda você a fazer tudo isso com simples argumentos de linha de comando. Ele automatiza a criação de:

- Cognito User Pools para autenticação
- Ambiente AgentCore Runtime para cada Servidor MCP
- AgentCore Gateway com suporte ao protocolo MCP
- Alvos de Servidor MCP do Gateway com autenticação OAuth2

O toolkit cria um gateway MCP completo e permite que múltiplos servidores MCP (Exemplo: calculator e helloworld) sejam acessados através de um único endpoint de gateway com autenticação e roteamento adequados.

## Pré-requisitos

1. Credenciais AWS configuradas
2. Python 3.8+ instalado
3. (Opcional) Arquivo `.env` para configuração de usuário Cognito

## Instalação

### A partir do PyPI (quando publicado)
```bash
pip install agentcore-runtime-gw-mcp-toolkit
```

### A partir do Código Fonte
```bash
git clone <repository-url>
cd agentcore-runtime-gw-mcp-tool-kit
pip install -e .
```

## Configuração

### Variáveis de Ambiente (Opcional)

Você pode personalizar as credenciais de usuário Cognito criando um arquivo `.env` no diretório do projeto:

```bash
# .env file
COGNITO_USERNAME=your_username
COGNITO_TEMP_PASSWORD=your_temp_password
COGNITO_PASSWORD=your_permanent_password
```

**Valores padrão** (usados se o arquivo `.env` não for fornecido):
- `COGNITO_USERNAME`: `testuser`
- `COGNITO_TEMP_PASSWORD`: `Temp123!`
- `COGNITO_PASSWORD`: `MyPassword123!`

**Nota**: O toolkit cria automaticamente usuários Cognito com essas credenciais para fins de teste.

## Uso

### Primeiros Passos

1. **Clone o Repositório**
   ```bash
   git clone <repository-url>
   cd agentcore-runtime-gw-mcp-tool-kit
   ```

2. **Instale o Pacote**
   ```bash
   pip install -e .
   ```

3. **Prepare o Código do Seu Servidor MCP**
   - Os arquivos do seu servidor MCP podem estar localizados em qualquer lugar do seu sistema
   - Certifique-se de que cada servidor tenha seu próprio `server.py` e `requirements.txt`
   - Anote os caminhos completos desses arquivos para a configuração do runtime

   
   **Exemplo de estrutura (pode estar em qualquer lugar):**
   ```
   /path/to/my-servers/
   ├── calculator/
   │   ├── server.py
   │   └── requirements.txt
   ├── helloworld/
   │   ├── server.py
   │   └── requirements.txt
   └── my-custom-server/
       ├── server.py
       └── requirements.txt
   ```

4. **Implante com Argumentos de Linha de Comando**
   ```bash
   agentcore-mcp-toolkit \
     --gateway-name "my-gateway" \
     --runtime-configs '[
       {
         "name": "my-custom-runtime",
         "description": "My Custom MCP Server",
         "entrypoint": "/path/to/my-servers/my-custom-server/server.py",
         "requirements_file": "/path/to/my-servers/my-custom-server/requirements.txt"
       }
     ]'
   ```
   **Nota:** O agentcore-mcp-toolkit deve ser invocado a partir da raiz do projeto do Servidor MCP. Exemplo: O utilitário deve ser invocado a partir de /path/to para o exemplo acima.

### Uso Básico

```bash
# Implante com argumentos mínimos
agentcore-mcp-toolkit \
  --gateway-name "my-gateway" \
  --runtime-configs '[{"name":"runtime1","description":"My Runtime","entrypoint":"/path/to/myserver/server.py","requirements_file":"/path/to/myserver/requirements.txt"}]'

# Implante com todas as opções
agentcore-mcp-toolkit \
  --region us-east-1 \
  --gateway-name "my-gateway-mcp-server" \
  --gateway-description "My AgentCore Gateway" \
  --runtime-configs '[
    {
      "name": "my-calculator-runtime",
      "description": "Calculator MCP Server", 
      "entrypoint": "/path/to/calculator/server.py",
      "requirements_file": "/path/to/calculator/requirements.txt"
    }
  ]'
```

### Opções de Linha de Comando

- `--region`: Região AWS (padrão: us-east-1)
- `--gateway-name`: Nome do gateway (obrigatório)
- `--gateway-description`: Descrição do gateway (opcional)
- `--runtime-configs`: Array JSON de configurações de runtime (obrigatório)

### Formato de Configuração do Runtime

Cada configuração de runtime no array JSON `--runtime-configs` deve incluir:

```json
{
  "name": "runtime-name",
  "description": "Runtime description",
  "entrypoint": "path/to/server.py",
  "requirements_file": "path/to/requirements.txt",
  "auto_create_execution_role": true,
  "auto_create_ecr": true
}
```

**Campos obrigatórios:**
- `name`: Nome único do runtime
- `entrypoint`: Caminho completo para o arquivo Python do servidor MCP
- `requirements_file`: Caminho completo para o arquivo requirements.txt

**Campos opcionais:**
- `description`: Descrição do runtime
- `auto_create_execution_role`: Criar automaticamente a role IAM (padrão: true)
- `auto_create_ecr`: Criar automaticamente o repositório ECR (padrão: true)

### Nomes Derivados Automaticamente

O toolkit deriva automaticamente os nomes dos recursos a partir dos campos `gateway-name` e `name` do runtime:

**Recursos do Gateway** (a partir de `--gateway-name`):
- IAM Role: `{gateway-name}-role`
- User Pool: `{gateway-name}-pool`
- Resource Server ID: `{gateway-name}-id`
- Resource Server Name: `{gateway-name}-name`
- Client Name: `{gateway-name}-client`

**Recursos do Runtime** (a partir do `name` do runtime):
- User Pool: `{runtime-name}-pool`
- Resource Server ID: `{runtime-name}-id`
- Resource Server Name: `{runtime-name}-name`
- Client Name: `{runtime-name}-client`
- Agent Name: `{runtime-name}` (com hífens convertidos para underscores)

**Recursos do Target** (gerados automaticamente):
- Target Name: `{runtime-name}-target`
- Identity Provider: `{runtime-name}-identity`

## Testando o Gateway

Uma vez implantado, o toolkit fornece automaticamente todas as informações de conexão necessárias para testar e usar seu gateway MCP.

### Informações de Conexão do Gateway

O toolkit exibe automaticamente os detalhes de conexão e **salva as credenciais de forma segura em um arquivo** após a implantação bem-sucedida:

#### **Armazenamento Seguro de Credenciais**

Por segurança, as credenciais sensíveis são salvas em um arquivo seguro em vez de serem exibidas nos logs do console:

- **Localização do Arquivo**: `.agentcore-credentials-{gateway-name}.json`
- **Permissões do Arquivo**: Acesso somente pelo proprietário (600)
- **Saída do Console**: Mostra `<redacted>` para valores sensíveis
- **Método de Acesso**: Use `cat .agentcore-credentials-{gateway-name}.json`

**Exemplo de Saída:**
```
============================================================
GATEWAY CONNECTION INFORMATION
============================================================
Gateway URL: https://my-gateway-mcp-server-123456789.gateway.bedrock-agentcore.us-east-1.amazonaws.com/mcp
User Pool ID: us-east-1_bt4yEZFOx
Client ID: <redacted>
Client Secret: <redacted>
Access Token: <redacted>
Credentials saved to: .agentcore-credentials-my-gateway.json
File permissions set to owner-only access (600)
Use: cat .agentcore-credentials-my-gateway.json
============================================================

✅ Setup completed successfully!
Gateway ID: my-gateway-mcp-server-123456789
Runtime 1 Agent ARN: arn:aws:bedrock-agentcore:us-east-1:123456789:runtime/my_calculator_runtime-123456789
```

### Configurando o Plugin QDev com Access Token

Para usar o gateway MCP no plugin QDev, configure conforme mostrado abaixo:

![Configuração MCP do QDev](images/qdev_mcp_config.png)

**Passos para configurar o QDev:**
1. **Obtenha as credenciais** do arquivo seguro:
   ```bash
   cat .agentcore-credentials-{gateway-name}.json
   ```
2. Copie o valor do **access_token** do arquivo JSON
3. Nas configurações do plugin QDev, adicione um novo servidor MCP com:
   - **Server URL**: Use o `gateway_url` do arquivo de credenciais
   - **Authentication**: Bearer Token
   - **Token**: Cole o access token do passo 2
4. Salve a configuração e teste a conexão

**Nota de Segurança**: Nunca compartilhe ou faça commit do arquivo de credenciais no controle de versão.

### Exemplos de Demonstração ao Vivo

Uma vez configurado, você pode usar as ferramentas MCP diretamente no QDev:

**Demonstração do Servidor MCP Calculator:**
![Demonstração Calculator Add](images/calculator_add_demo.png)

**Demonstração do Servidor MCP Hello World:**
![Demonstração Greet Hello World](images/greet_hello_world.png)

## Arquitetura

![Arquitetura](images/architecture.png)

### Componentes da Arquitetura

O toolkit cria:
1. **Gateway Único**: Um AgentCore Gateway com múltiplos alvos de Servidor MCP que roteia requisições
2. **Múltiplos Runtimes**: Cada servidor MCP executa em seu próprio AgentCore Runtime
3. **Autenticação**: Recursos Cognito separados para o gateway e cada runtime
4. **Targets**: Alvos de Servidor MCP do Gateway que conectam o gateway a cada runtime

### Fluxo de Autenticação

**Inbound Auth (Cliente → Gateway):**
- O Cliente MCP (QDev) envia requisições com token Bearer
- O JWT Authorizer do Gateway valida o token contra o Cognito User Pool do Gateway
- Requisições autorizadas são roteadas para os alvos apropriados

**Outbound Auth (Gateway → Runtime):**
- Cada alvo possui seu próprio provedor de credenciais OAuth2
- O Gateway obtém tokens OAuth dos respectivos Cognito User Pools do Runtime
- Requisições autenticadas são enviadas para os runtimes individuais dos servidores MCP

## Suporte à Autorização

### Implementação Atual
Este toolkit atualmente suporta **Amazon Cognito OAuth2** para inbound auth e saída:
- **Inbound Auth**: O Gateway usa tokens JWT do Cognito para autenticação de clientes
- **Outbound Auth**: O Gateway se autentica no runtime usando credenciais OAuth2 do Cognito

### Roadmap
- **Autorização baseada em IAM Role**: Suporte para IAM roles e políticas para inbound auth e saída (A FAZER - planejado para a próxima versão)

## Recursos de Segurança

### **Gerenciamento Seguro de Credenciais**
- **Armazenamento em arquivo**: Credenciais salvas em arquivos seguros com permissões restritas
- **Mascaramento no console**: Valores sensíveis exibidos como `<redacted>` nos logs
- **Permissões de arquivo**: Configuração automática de acesso somente pelo proprietário (600)
- **Proteção de fallback**: Tratamento gracioso se operações de arquivo falharem

### **Validação de Entrada**
- **Proteção contra path traversal**: Previne `..` em caminhos de arquivo
- **Validação de extensão de arquivo**: Garante extensões `.py` e `.txt`
- **Validação de estrutura JSON**: Valida o formato de configuração do runtime
- **Verificação de campos obrigatórios**: Garante que todos os campos obrigatórios estejam presentes

### **Tratamento de Erros**
- **Tratamento específico de exceções**: Usa tipos de exceção apropriados
- **Mensagens de erro sanitizadas**: Previne divulgação de informações
- **Degradação graciosa**: Continua a operação quando possível
- **Códigos de saída adequados**: Retorna status apropriado para automação

## Limpeza

### Removendo Recursos

Para limpar todos os recursos criados pelo toolkit, use o script de limpeza:

```bash
# Limpar gateway e runtimes específicos
python -m cleanup \
  --gateway-name "my-gateway" \
  --runtime-names '["runtime1", "runtime2"]' \
  --region us-east-1

# Pular prompt de confirmação
python -m cleanup \
  --gateway-name "my-gateway" \
  --runtime-names '["runtime1", "runtime2"]' \
  --confirm
```

### Opções de Limpeza

- `--gateway-name`: Nome do gateway a ser limpo (obrigatório)
- `--runtime-names`: Array JSON de nomes de runtime a serem limpos (obrigatório)
- `--region`: Região AWS (padrão: us-east-1)
- `--confirm`: Pular prompt de confirmação

### Recursos Removidos

O script de limpeza remove:
- AgentCore Gateway e todos os targets
- Instâncias do AgentCore Runtime
- Cognito User Pools e domínios
- IAM roles e políticas
- Provedores de credenciais OAuth2

**Nota**: O script de limpeza não remove arquivos de credenciais locais. Para removê-los:
```bash
# Remover arquivos de credenciais manualmente
rm .agentcore-credentials-*.json
```

**Aviso**: Esta ação não pode ser desfeita. Sempre confirme os recursos antes de prosseguir.

## Solução de Problemas

1. Certifique-se de que as credenciais AWS estejam configuradas corretamente
2. Verifique se os arquivos necessários do servidor MCP existem em seus respectivos diretórios
3. Verifique as permissões da região AWS
4. Revise os logs do CloudWatch para informações detalhadas de erro
5. Certifique-se de que a URL do gateway esteja formatada corretamente ao testar
6. Verifique se os Cognito User Pools e clientes foram criados com sucesso
7. **Problemas com Access Token**: Se o access token expirar, execute novamente o toolkit para obter um novo token
8. **Problemas de Conexão com QDev**: Certifique-se de que a URL do gateway termine com `/mcp` e que o bearer token esteja copiado corretamente
9. **Descoberta de Ferramentas**: Use termos de consulta diferentes se as ferramentas não forem encontradas (tente "calculator", "greet" ou "tools")
10. **Problemas de Autorização**: Atualmente apenas Cognito OAuth2 é suportado - certifique-se de que toda autenticação use tokens Cognito
11. **Problemas com Usuário Cognito**: Se você encontrar erros na criação de usuários, verifique a configuração do seu arquivo `.env` ou use as credenciais padrão
12. **Problemas de Limpeza**: Se a limpeza falhar, verifique manualmente os recursos no console AWS e tente novamente com nomes de recursos específicos
13. **Problemas com Arquivo de Credenciais**: Se o arquivo de credenciais não puder ser criado, verifique as permissões do diretório e o espaço em disco
14. **Problemas de Permissão de Arquivo**: No Windows, as permissões de arquivo podem não ser definidas corretamente - proteja manualmente o arquivo de credenciais
15. **Erros de Validação de Caminho**: Certifique-se de que os caminhos de arquivo não contenham `..` e tenham as extensões corretas (`.py`, `.txt`)
16. **Erros de Validação JSON**: Verifique se runtime-configs é um array JSON válido com os campos obrigatórios

## Exemplos de Servidores MCP

O toolkit inclui exemplos de servidores MCP:
- **Calculator**: Fornece funções de adição e multiplicação
- **HelloWorld**: Fornece funcionalidade de saudação

Ambos os servidores demonstram a implementação do protocolo MCP e podem ser usados como modelos para criar servidores MCP personalizados.
