# Agente de Suporte ao Cliente End-to-End com AgentCore

Mova um agente de suporte ao cliente de protótipo para produção usando os serviços Amazon Bedrock AgentCore. Através de seis laboratórios, você construirá um sistema completo que lida com conversas reais de clientes com memória, ferramentas compartilhadas, observabilidade e uma interface web.

> [!IMPORTANT]
> Este workshop é para fins educacionais. Ele demonstra como os serviços AgentCore são usados ao migrar um caso de uso agêntico de protótipo para produção. Não é destinado para uso direto em ambientes de produção.

## Visão Geral da Arquitetura

Ao final dos 6 laboratórios, você terá construído a seguinte arquitetura:

<div style="text-align:left">
    <img src="images/architecture_lab6_streamlit.png" width="100%"/>
</div>

## Pré-requisitos

- Você precisa de uma conta AWS com acesso ao Amazon Bedrock.
- Python 3.10+ deve estar instalado localmente.
- O AWS CLI deve estar configurado com credenciais apropriadas.
- Amazon Nova 2 Lite deve estar habilitado nas suas configurações de [acesso a modelos do Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html).

## Primeiros Passos

### Se você está executando isto como um laboratório self-paced (não usando uma conta AWS Workshop)

Antes de começar o Lab 1, você precisa provisionar a infraestrutura necessária (funções Lambda, tabelas DynamoDB, funções IAM, Cognito user pool e uma Bedrock Knowledge Base). Siga estes passos:

1. Verifique se sua função IAM tem as [permissões necessárias](https://catalog.us-east-1.prod.workshops.aws/workshops/850fcd5c-fd1f-48d7-932c-ad9babede979/en-US/00-prerequisites/02-self-paced) para o workshop, incluindo as [políticas IAM, políticas gerenciadas AWS e relações de confiança](https://catalog.us-east-1.prod.workshops.aws/workshops/850fcd5c-fd1f-48d7-932c-ad9babede979/en-US/00-prerequisites/02-self-paced#iam-policy-for-bedrock-agentcore-workshop) descritas nos pré-requisitos do workshop.
2. Execute o script de pré-requisitos para implantar as stacks CloudFormation:

```bash
bash scripts/prereq.sh
```

Este script cria um bucket S3, empacota e faz upload do código Lambda, e implanta duas stacks CloudFormation (infraestrutura e Cognito) que provisionam todos os recursos de suporte usados nos laboratórios.

### Instalar dependências e iniciar Lab 1

```bash
pip install -r requirements.txt
```

Então abra [Lab 1](lab-01-create-an-agent-pt.ipynb) e acompanhe. Cada laboratório constrói sobre o anterior.

## Laboratórios

| Lab | Título                                                                | Notebook                                   | Tempo    | O Que Você Vai Aprender                                         |
| --- | -------------------------------------------------------------------- | ------------------------------------------ | ------- | --------------------------------------------------------- |
| 1   | [Criar Protótipo de Agente](#lab-1-criar-protótipo-de-agente)              | [Notebook](lab-01-create-an-agent-pt.ipynb)   | ~20 min | Criação de agente com Strands Agents e integração de ferramentas   |
| 2   | [Adicionar Memória](#lab-2-adicionar-memória)                                      | [Notebook](lab-02-agentcore-memory-pt.ipynb)  | ~20 min | AgentCore Memory para persistência de curto e longo prazo |
| 3   | [Escalar com Gateway & Identity](#lab-3-escalar-com-gateway--identity) | [Notebook](lab-03-agentcore-gateway-pt.ipynb) | ~30 min | AgentCore Gateway e Identity para compartilhamento seguro de ferramentas    |
| 4   | [Implantar em Produção](#lab-4-implantar-em-produção)                  | [Notebook](lab-04-agentcore-runtime-pt.ipynb) | ~30 min | AgentCore Runtime com Observabilidade de nível de produção     |
| 5   | [Avaliar Desempenho do Agente](#lab-5-avaliar-desempenho-do-agente)      | [Notebook](lab-05-agentcore-evals-pt.ipynb)   | ~10 min | AgentCore Evaluations para monitoramento de qualidade              |
| 6   | [Construir Interface do Cliente](#lab-6-construir-interface-do-cliente)          | [Notebook](lab-06-frontend-pt.ipynb)          | ~20 min | Integração de frontend com endpoints seguros do agente          |

### Lab 1: Criar Protótipo de Agente

Construa um protótipo de agente de suporte ao cliente usando Strands Agents e Amazon Nova 2 Lite com quatro ferramentas principais:

- Consultar políticas de devolução para diferentes categorias de produtos.
- Pesquisar informações e especificações de produtos.
- Pesquisar na web por ajuda de troubleshooting.
- Consultar uma Bedrock Knowledge Base para documentação de suporte técnico.

### Lab 2: Adicionar Memória

Transforme seu "agente peixinho-dourado" em um que lembra dos clientes através de conversas usando AgentCore Memory:

- Armazenar histórico de conversas persistentemente com memória de curto prazo.
- Extrair preferências de clientes e padrões comportamentais com memória de longo prazo.
- Manter consciência de contexto através de múltiplas sessões para que o agente possa personalizar respostas.

### Lab 3: Escalar com Gateway & Identity

Migre de ferramentas locais para serviços compartilhados prontos para empresa usando AgentCore Gateway e AgentCore Identity:

- Centralizar gerenciamento de ferramentas expondo funções Lambda como ferramentas compatíveis com MCP através do AgentCore Gateway.
- Proteger seu endpoint de gateway com autenticação baseada em JWT usando Amazon Cognito.
- Integrar funções AWS Lambda existentes (verificação de garantia, busca web) sem reescrever código de ferramentas.
- (Opcional) Definir controle de acesso refinado com políticas Cedar usando AgentCore Policy para restringir invocações específicas de ferramentas.

### Lab 4: Implantar em Produção

Implante seu agente no AgentCore Runtime para lidar com tráfego real com observabilidade completa:

- Implantar seu agente em um runtime totalmente gerenciado e serverless com mudanças mínimas de código (apenas quatro linhas adicionadas).
- Habilitar continuidade de sessão e isolamento de sessão para que cada cliente tenha um contexto de conversa separado.
- Monitorar comportamento do agente através do CloudWatch GenAI Observability com rastreamento e métricas automáticos.

### Lab 5: Avaliar Desempenho do Agente

Configure monitoramento contínuo de qualidade para seu agente de produção usando AgentCore Evaluations:

- Configurar avaliação online com avaliadores integrados para taxa de sucesso de objetivo, correção e precisão de seleção de ferramentas.
- Gerar interações de teste e revisar resultados de avaliação através de dashboards do AgentCore Observability.
- Usar métricas de qualidade para identificar áreas de melhoria e manter alto desempenho do agente.

### Lab 6: Construir Interface do Cliente

Crie um aplicativo web Streamlit que clientes podem usar para interagir com seu agente implantado:

- Fornecer uma interface de chat com streaming de resposta em tempo real alimentado pelo AgentCore Runtime.
- Implementar autenticação segura de usuário através do Amazon Cognito.
- Gerenciar sessões de usuário com histórico de conversa persistente via AgentCore Memory.

## Laboratórios Opcionais

- [Mergulho Profundo em Identity](Optional-lab-identity-pt.ipynb) -- Integrar Google Calendar via AgentCore Identity usando fluxos OAuth2 3LO, habilitando seu agente a criar eventos e recuperar calendários em nome de usuários autenticados.
- [Mergulho Profundo em Observability](Optional-lab-agentcore-observability-pt.ipynb) -- Configurar AgentCore Observability para agentes executando fora do AgentCore Runtime usando instrumentação AWS OpenTelemetry Python e CloudWatch GenAI Observability.

## Limpeza

Quando terminar, execute o [notebook de Limpeza](lab-07-cleanup-pt.ipynb) para derrubar todos os recursos criados durante o workshop.
