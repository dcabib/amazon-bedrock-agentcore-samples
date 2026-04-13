# Conceitos Avançados de Observabilidade

Esta seção aborda padrões e técnicas avançadas de observabilidade para o Amazon Bedrock AgentCore, ajudando você a implementar capacidades sofisticadas e personalizadas de monitoramento e depuração.

## Tutoriais Disponíveis

### 01-custom-span-creation/

- **Notebook**: `Custom_Span_Creation.ipynb`
- **Descrição**: Aprenda a criar spans personalizados para rastreamento detalhado de operações
- **Funcionalidades**: Criação manual de spans, atributos personalizados
- **Casos de Uso**: Monitoramento granular, depuração

### 02-data-protection/

- **Notebook**: `data_protection.ipynb`
- **Descrição**: Implemente proteção de dados abrangente para informações sensíveis em workflows de agentes
- **Funcionalidades**: Integração com Bedrock Guardrails, CloudWatch Logs Data Protection, detecção e mascaramento de PII
- **Casos de Uso**: Conformidade (GDPR, HIPAA, CCPA), tratamento de dados sensíveis, proteção de privacidade

## O Que Você Vai Aprender

- **Criação de Spans Personalizados**: Adicionar rastreamento detalhado a operações específicas
- **Atributos de Span**: Enriquecer traces com metadados personalizados
- **Spans Aninhados**: Criar estruturas hierárquicas de trace
- **Monitoramento de Performance**: Identificar gargalos em workflows de agentes
- **Rastreamento de Erros**: Capturar e rastrear exceções e falhas
- **Proteção de Dados**: Implementar detecção e mascaramento de dados sensíveis em logs e traces
- **Integração de Conformidade**: Configurar Bedrock Guardrails e CloudWatch Data Protection

## Primeiros Passos

1. Navegue até o diretório do tutorial
2. Copie `.env.example` para `.env` e configure:
   - Credenciais AWS
   - Configurações do log group do CloudWatch
   - Configuração do OpenTelemetry
3. Habilite o CloudWatch Transaction Search na sua região AWS
4. Instale as dependências: `pip install -r requirements.txt`
5. Abra e execute o Jupyter notebook

## Pré-requisitos

- Compreensão dos conceitos básicos do OpenTelemetry
- Familiaridade com o Amazon CloudWatch
- Experiência com frameworks de agentes (recomendado)
- Conta AWS com permissões apropriadas

## Padrões Avançados Abordados

- **Instrumentação Manual**: Quando e como adicionar spans personalizados
- **Métricas Personalizadas**: Criação de medições específicas do domínio
- **Políticas de Proteção de Dados**: Configuração de filtros de informações sensíveis
- **Segurança Multicamada**: Combinação de Guardrails com CloudWatch Data Protection

## Limpeza

Após concluir os tutoriais:

1. Exclua os log groups do CloudWatch criados durante os exemplos
2. Remova quaisquer recursos de teste
3. Limpe os arquivos de configuração de ambiente
