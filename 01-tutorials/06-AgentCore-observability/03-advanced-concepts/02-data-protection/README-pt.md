# Observabilidade do Amazon Bedrock AgentCore: Proteção de Dados

## Visão Geral

Neste tutorial, aprenderemos como implementar proteção de dados abrangente em aplicações de IA agêntica usando Amazon Bedrock Guardrails e políticas de Amazon CloudWatch Logs Data Protection. Este tutorial demonstra como proteger dados sensíveis ao longo de todo o ciclo de vida do agente, desde o processamento de entrada até a geração de saída e logging.

Vamos focar na criação de uma estratégia de defesa em profundidade para proteger suas aplicações de IA, combinando múltiplas camadas de proteção que trabalham em conjunto para salvaguardar informações de identificação pessoal (PII), dados financeiros, registros de saúde e outras informações confidenciais.

### Detalhes do Tutorial

| Informação          | Detalhes                                                                         |
|:--------------------|:---------------------------------------------------------------------------------|
| Tipo de tutorial    | Observabilidade & Segurança                                                      |
| Tipo de agente      | Single                                                                           |
| Framework agêntico  | Strands Agents                                                                   |
| Modelo LLM          | Anthropic Claude Haiku 4.5                                                     |
| Componentes do tutorial | Data Protection, Bedrock Guardrails, CloudWatch Logs Data Protection       |
| Vertical do tutorial| Cross-vertical                                                                   |
| Complexidade do exemplo | Avançado                                                                     |
| SDK utilizado       | Amazon BedrockAgentCore Python SDK e boto3                                      |

### Arquitetura do Tutorial

Neste tutorial, demonstraremos como implementar mecanismos de proteção de dados para agentes implantados no AgentCore runtime. Usaremos um agente de suporte ao cliente que processa informações sensíveis e mostraremos como proteger esses dados usando múltiplas camadas de segurança.

O exemplo inclui:
- Um Strands Agent com capacidades de suporte ao cliente
- Amazon Bedrock Guardrails para filtragem de conteúdo
- CloudWatch Logs Data Protection para mascaramento de logs
- Detecção e tratamento de informações sensíveis

### Funcionalidades Principais do Tutorial

* **Proteção de Dados Multicamada**: Implementação de Bedrock Guardrails e CloudWatch Logs Data Protection
* **Detecção de Informações Sensíveis**: Detecção automática de PII, dados financeiros e outras informações confidenciais
* **Segurança do Agente**: Proteção de dados sensíveis em interações e traces do agente
* **Suporte à Conformidade**: Atendimento aos requisitos de regulamentações de privacidade (GDPR, HIPAA, CCPA)
* **Estratégia de Defesa em Profundidade**: Criação de segurança abrangente para aplicações de IA agêntica

## O Que Você Vai Aprender

Neste tutorial prático, você explorará:

- Como detectar informações sensíveis em interações de Agentes e em CloudWatch Logs e Traces
- Amazon Bedrock Guardrails: Como configurar filtros de informações sensíveis para impedir que agentes de IA processem ou gerem conteúdo sensível
- CloudWatch Logs Data Protection: Como detectar e mascarar automaticamente dados sensíveis em logs de aplicação
- Integração com AgentCore: Como implementar essas medidas de proteção dentro de workflows agênticos

## Por Que Isso Importa

Sem as devidas salvaguardas, sistemas de IA agêntica podem:

- Expor inadvertidamente dados sensíveis de clientes em respostas ou logs
- Processar ou reter informações que violam regulamentações de privacidade
- Gerar saídas contendo PII que não deveriam ser compartilhadas
- Criar vulnerabilidades de conformidade e segurança na infraestrutura da sua aplicação

## Arquivos neste Tutorial

- `data_protection.ipynb` - Notebook principal do tutorial com instruções passo a passo
- `requirements.txt` - Dependências Python necessárias para o tutorial
- `data/` - Arquivos de dados de exemplo incluindo exemplos de conversas de suporte ao cliente
- `images/` - Diagramas de arquitetura e recursos visuais para o tutorial
