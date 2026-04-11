# Assistente Culinário com Estratégia de Memória Autogerenciada (Com Citações)

Este exemplo demonstra a estratégia de memória autogerenciada do Amazon Bedrock AgentCore com rastreamento aprimorado de citações. Esta versão estende o exemplo base do assistente culinário adicionando informações abrangentes de citação às memórias de longo prazo extraídas.

## O que há de Diferente

Este exemplo adiciona funcionalidade de citação para rastrear a origem das memórias extraídas:

### Funcionalidades de Citação

1. **Rastreamento de Origem**: Cada memória extraída inclui metadados sobre sua origem:
   - ID da Sessão e ID do Ator
   - Timestamps de início e fim
   - URI S3 onde o payload original da memória de curto prazo está armazenado
   - ID do job de extração

2. **Metadados de Citação**: Informações de citação estruturadas são armazenadas nos metadados da memória:
   ```python
   citation_info = {
       'source_type': 'short_term_memory',
       'session_id': session_id,
       'actor_id': actor_id,
       'starting_timestamp': starting_timestamp,
       'ending_timestamp': timestamp,
       's3_uri': s3_location,
       's3_payload_location': s3_location,
       'extraction_job_id': job_id
   }
   ```

3. **Citações Legíveis por Humanos**: Cada conteúdo de memória inclui um texto de citação anexado:
   ```
   [Citation: Extracted from session {session_id}, actor {actor_id}, source: {s3_location}, job: {job_id}, timestamp: {timestamp}]
   ```

### Arquivos Modificados

#### `lambda_function.py`

As principais mudanças estão na classe `MemoryExtractor`:

- O método `extract_memories()` agora aceita os parâmetros `s3_location` e `job_id`
- O método `_format_extracted_memories()` constrói informações de citação e as anexa ao conteúdo da memória
- Logging aprimorado para rastrear informações de citação

**Método Principal**: `_format_extracted_memories` (linha 97)
Este método formata memórias extraídas com metadados e informações de citação, criando um link rastreável das memórias de longo prazo de volta à sua origem na memória de curto prazo.

#### `agentcore_self_managed_memory_demo.ipynb`

Atualizado para demonstrar a funcionalidade de citação em ação, mostrando como as memórias extraídas agora incluem atribuição de origem.

## Casos de Uso

Esta versão aprimorada com citações é particularmente útil para:

1. **Trilhas de Auditoria**: Manter um registro completo de onde as memórias se originaram
2. **Depuração**: Rastrear de volta ao contexto original da conversa
3. **Conformidade**: Atender requisitos de linhagem de dados e atribuição de origem
4. **Verificação de Memória**: Capacidade de verificar o conteúdo da memória contra a origem original no S3

## Pré-requisitos

Mesmos do exemplo base do assistente culinário:
- Python 3.11+
- Credenciais AWS configuradas
- Acesso ao Amazon Bedrock com modelos Claude
- Serviços AWS necessários: Lambda, S3, SNS, SQS

## Configuração

Siga o mesmo processo de configuração do exemplo base do assistente culinário. O notebook guiará você através de:

1. Criar a função Lambda com suporte a citações
2. Configurar a estratégia de memória com condições de gatilho
3. Testar a funcionalidade aprimorada de citações

## Comparação com o Exemplo Base

| Funcionalidade | Exemplo Base | Com Citações |
|----------------|-------------|--------------|
| Extração de memória | ✅ | ✅ |
| Rastreamento de payload S3 | ❌ | ✅ |
| Atribuição de origem | ❌ | ✅ |
| Rastreamento de ID do job | ❌ | ✅ |
| Contexto de timestamp | ❌ | ✅ |
| Metadados de citação | ❌ | ✅ |

## Documentação

Para mais informações sobre estratégias de memória autogerenciada, consulte a [documentação do Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-self-managed-strategies.html).
