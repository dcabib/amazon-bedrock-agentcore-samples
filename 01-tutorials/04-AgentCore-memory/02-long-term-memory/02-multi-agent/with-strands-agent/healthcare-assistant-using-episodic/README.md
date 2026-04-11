# Sistema de Saúde Multi-Agente com Memória Episódica

A memória episódica captura fatias significativas de interação, identificando momentos importantes e resumindo-os em registros compactos e organizados para recuperação focada sem ruído.

Reflexões analisam episódios para revelar insights, padrões e conclusões—ajudando o sistema a entender por que eventos importam e como devem influenciar comportamento futuro, transformando experiência em orientação acionável.

Um exemplo abrangente demonstrando **coordenação multi-agente com memória episódica** usando Amazon Bedrock AgentCore Memory. Este tutorial mostra como agentes de IA podem aprender com interações passadas e melhorar a tomada de decisão ao longo do tempo.

## Visão Geral

Este tutorial apresenta um sistema de assistente de saúde com:
- **Agente Supervisor**: Encaminha perguntas de pacientes para agentes especializados
- **Agente de Sinistros**: Lida com sinistros de seguro e consultas de faturamento
- **Agente de Dados Demográficos**: Gerencia informações demográficas dos pacientes
- **Agente de Medicamentos**: Lida com consultas sobre medicamentos e prescrições

Cada agente mantém memória de curto prazo isolada através de **ramificação de memória**, enquanto compartilha insights de longo prazo através de **estratégias de memória episódica**.

## Arquitetura
<div style="text-align:left">
    <img src="architecture.png" width="75%" />
</div>

## Estratégia de Memória

### Episódica

O sistema usa uma estratégia de memória episódica com:

**Extração**: Converte eventos de conversa em episódios estruturados
- Prompt: "Extrair interações de pacientes com agentes de saúde"
- Namespace: `healthcare/{actorId}/{sessionId}/`

**Consolidação**: Mescla episódios relacionados
- Prompt: "Consolidar conversas de saúde"

**Reflexão**: Gera insights entre sessões
- Prompt: "Gerar insights a partir de padrões de cuidado ao paciente"
- Namespace: `healthcare/{actorId}/` (prefixo exato do namespace)

### Ramificação de Memória

Cada agente opera em sua própria ramificação de memória:
- `main`: Decisões de encaminhamento do agente supervisor
- `claims_agent`: Conversas sobre seguro e faturamento
- `demographics_agent`: Atualizações de informações do paciente
- `medication_agent`: Discussões sobre prescrições

**Benefícios:**
- Agentes não veem as conversas uns dos outros
- Separação limpa de responsabilidades
- Todos os agentes contribuem para a memória de longo prazo compartilhada
- Insights no nível do paciente abrangem todas as interações

## Pré-requisitos

### Serviços AWS
- **Amazon Bedrock**: Acesso ao modelo Claude Sonnet 4
- **Amazon Bedrock AgentCore Memory**: Para estratégia de memória episódica
- **Amazon HealthLake** (opcional): Datastore FHIR com dados de pacientes
  - Pode criar novo datastore com dados Synthea durante a configuração
  - Ou usar datastore existente

### Permissões IAM
```json
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
    },
    {
      "Effect": "Allow",
      "Action": [
        "healthlake:DescribeFHIRDatastore",
        "healthlake:CreateFHIRDatastore",
        "healthlake:ReadResource",
        "healthlake:SearchWithGet"
      ],
      "Resource": "*"
    }
  ]
}
```

### Ambiente Python
- Python 3.10+
- Jupyter Notebook ou JupyterLab

## Instalação

1. Instale as dependências:
```bash
pip install -r requirements.txt
```

2. Configure as credenciais AWS:
```bash
aws configure
```

## Uso

### Início Rápido

1. Abra o notebook:
```bash
jupyter notebook healthcare-data-assistant.ipynb
```

2. Execute as células sequencialmente:
   - **Passo 1**: Configuração do Ambiente
   - **Passo 2**: Configurar o Datastore HealthLake
   - **Passo 3**: Criar Memória como ferramenta para memória de Longo prazo com Estratégia Episódica
   - **Passo 4**: Criar Provedor de Hook de Memória com Suporte a Ramificação para memória de Curto prazo
   - **Passo 5**: Criar Arquitetura de Saúde Multi-Agente com Ramificação de Memória
   - **Passo 6**: Testar com chat interativo
   - **Passo 7**: Inspecionar ramificações de memória de saúde
   - **Passo 8**: Validar memória de longo prazo (episódios e reflexões)

### Entradas Interativas

O notebook solicita:
- **ID do datastore HealthLake**: Datastore existente ou criar novo com dados Synthea (nenhuma informação real de paciente é usada)
- **Região do HealthLake**: Região AWS para o HealthLake

### Testando o Sistema

O chat interativo (Passo 7) permite que você:
- Pergunte sobre sinistros de seguro
- Solicite informações demográficas
- Consulte medicamentos e prescrições
- Veja o encaminhamento do supervisor em ação
- Observe a ramificação de memória

Exemplos de perguntas:
```
Você: Qual é o status do meu sinistro de seguro?
Você: Pode me dizer sobre meus medicamentos?
Você: Qual é meu endereço atual cadastrado?
```

Digite `quit`, `exit` ou `q` para encerrar a sessão de chat.

## Integração com o Navegador de Memória

Após executar o notebook, você pode visualizar a memória usando o navegador de memória:

1. Anote o ID da Memória a partir do resumo de configuração
2. Abra - [Navegador de Memória](https://github.com/awslabs/amazon-bedrock-agentcore-samples/tree/main/01-tutorials/04-AgentCore-memory/03-advanced-patterns/04-memory-browser) - Visualize e explore eventos, episódios e reflexões de memória em `http://localhost:8000`
3. Insira o ID da Memória para explorar:
   - **Memória de curto prazo**: Eventos por ramificação
   - **Episódios**: Memórias consolidadas no nível da sessão
   - **Reflexões**: Insights no nível do paciente

**⏱️ Nota**: A geração de episódios e reflexões leva 10-15 minutos após as conversas. Verifique novamente mais tarde se nenhum episódio/reflexão aparecer imediatamente.

## Conceitos Principais Demonstrados

### 1. Coordenação Multi-Agente
- Padrão supervisor para encaminhamento
- Agentes especializados com expertise de domínio
- Uso dinâmico de ferramentas para dados em tempo real

### 2. Ramificação de Memória
- Conversas isoladas por agente
- Armazenamento de eventos específico por ramificação
- Contexto de sessão compartilhado

### 3. Memória Episódica
- Prompts de extração, consolidação e reflexão
- Episódios no nível da sessão
- Reflexões no nível do paciente

### 4. Integração com HealthLake
- Consultas FHIR dinâmicas
- Acesso a dados de pacientes em tempo real
- Todos os dados são sintéticos (gerados pelo Synthea) - nenhuma informação real de paciente é usada

## Customização

### Adicionando Novos Agentes

```python
@tool
def get_patient_allergies(patient_id: str = PATIENT_ID) -> dict:
    """Obter alergias do paciente do HealthLake"""
    return query_healthlake('AllergyIntolerance', {'patient': patient_id})

allergy_agent = Agent(
    model="global.anthropic.claude-sonnet-4-20250514-v1:0",
    system_prompt="Você lida com alergias de pacientes. Use a ferramenta get_patient_allergies.",
    tools=[get_patient_allergies]
)
```

### Usando Modelos Diferentes

Altere o parâmetro `model` na criação do agente:
```python
Agent(
    model="anthropic.claude-3-5-sonnet-20241022-v2:0",  # Modelo diferente
    system_prompt="...",
    tools=[...]
)
```

## Recursos Adicionais

- [Melhores Práticas de Memória Episódica](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/episodic-memory-strategy.html#memory-episodic-retrieve-episodes) - Aprenda como recuperar episódios para melhorar o desempenho agêntico


## Resolução de Problemas

### Erros de Criação de Ramificação
Se você ver "Branch rootEventId is required when creating a branch":
- **Reinicie o kernel do Jupyter** (Kernel → Reiniciar)
- **Re-execute todas as células** desde o início para recarregar a classe `HealthcareMemoryHooks` corrigida
- A correção garante que a ramificação principal tenha um evento inicial antes de criar ramificações de agentes especializados

### Erros de Hook de Memória
Se você ver "MemorySession.add_turns() got an unexpected keyword argument 'branch_name'":
- Isso indica que o notebook está usando código antigo/em cache
- **Reinicie o kernel** e re-execute todas as células para carregar as correções da API
- O código corrigido usa o formato `branch={"name": branch_name}`

### Modelo Não Disponível
Se você ver "serviceUnavailableException", certifique-se de:
- Usar perfil de inferência global: `global.anthropic.claude-sonnet-4-20250514-v1:0`
- Ou perfil específico da região para sua região

### Acesso Negado ao HealthLake
Verifique se as permissões IAM incluem:
- `healthlake:DescribeFHIRDatastore`
- `healthlake:ReadResource`
- `healthlake:SearchWithGet`

### Falha na Criação de Memória
Verifique se:
- A role IAM possui permissões de invocação do Bedrock


## Limpeza

Após completar o tutorial, você pode limpar os recursos para evitar cobranças contínuas:

1. Execute a célula de **Limpeza** no final do notebook
2. Você será solicitado a deletar:
   - **Memória**: Instância do AgentCore Memory
   - **Datastore HealthLake**: Datastore FHIR (opcional)

Cada recurso pode ser deletado independentemente conforme suas necessidades.

### Limpeza Manual

Se necessário, você também pode deletar recursos manualmente:

```bash
# Deletar memória
aws bedrock-agentcore-cp delete-memory --memory-id <MEMORY_ID> --region us-east-1

# Deletar datastore HealthLake
aws healthlake delete-fhir-datastore --datastore-id <DATASTORE_ID> --region <REGION>
```

## Saiba Mais

- [Documentação do AgentCore Memory](https://docs.aws.amazon.com/bedrock/latest/userguide/agentcore-memory.html)
- [Guia do Strands Agents](https://strandsagents.com)
- [API FHIR do HealthLake](https://docs.aws.amazon.com/healthlake/latest/devguide/working-with-FHIR-healthlake.html)
- [Padrões de Ramificação de Memória](https://docs.aws.amazon.com/bedrock/latest/userguide/agentcore-memory-branching.html)
