# AWS re:Invent 2025 AIML301: Construa um Caso de Uso SRE Completo com Bedrock AgentCore

## Visão Geral

Este workshop demonstra como Engenheiros de Confiabilidade de Sites (SREs) podem aproveitar o Amazon Bedrock AgentCore para automatizar a resposta a incidentes, desde diagnósticos até remediação e prevenção.

**Cenário do Workshop:** Uma aplicação CRM implantada na AWS (EC2 + NGINX + DynamoDB) apresenta falhas. Você construirá um sistema multi-agente para diagnosticar problemas, remediá-los com segurança usando fluxos de aprovação e prevenir recorrências através de pesquisa e melhores práticas.

## Objetivos de Aprendizado

Ao completar este workshop, você irá:

1. **Lab 1** - Verificar pré-requisitos e configurar uma stack de aplicação CRM realista com capacidades de injeção de falhas
2. **Lab 2** - Construir um agente de diagnóstico que analisa logs e métricas do CloudWatch
3. **Lab 3a** - Criar um agente de remediação com fluxos de aprovação e interpretador de código
4. **Lab 3b** - Implementar controle de acesso granular com interceptor Lambda personalizado
5. **Lab 4** - Implementar um agente de prevenção usando o Browser do AgentCore para pesquisa
6. **Lab 5** - Orquestrar todos os agentes usando um padrão supervisor com AgentCore Gateway e interface interativa Streamlit

## Início Rápido

### Fluxo Recomendado dos Labs

```
Lab-01 (Pré-requisitos e Infraestrutura)
   ↓
Lab-02 (Agente de Diagnóstico)
   ↓
Lab-03a (Agente de Remediação)
   ↓
Lab-03b (Controle de Acesso Granular)
   ↓
Lab-04 (Agente de Prevenção)
   ↓
Lab-05 (Orquestração Multi-Agente + Interface Streamlit)
```

### Começando

1. **Baixe o workshop** para sua máquina local
2. **Abra o Jupyter Notebook/Lab** no diretório do workshop
3. **Comece com `Lab-01-prerequisites-infra.ipynb`** e execute todas as seções
4. **Siga os labs sequencialmente** até o Lab-05
5. **Limpe os recursos** quando terminar usando as células de limpeza no Lab-05

**⏱️ Tempo Estimado:**
- Workshop completo (Labs 1-5): **2 horas**

**✨ Tudo acontece dentro do notebook - nenhum comando de terminal necessário!**

## Como Funciona

### Tudo Roda nos Notebooks

- Abra um notebook, execute de cima para baixo
- Toda configuração, setup e provisionamento acontece automaticamente
- Nenhum comando de terminal necessário
- Cada notebook é autocontido
- Notebooks importam helpers e utilitários conforme necessário

**Exemplo do que acontece dentro de um notebook:**
1. Instala pacotes Python necessários via `pip install`
2. Configura credenciais AWS e ambiente
3. Verifica pré-requisitos
4. Provisiona recursos AWS (EC2, DynamoDB, Lambda, etc.)
5. Implementa e testa agentes
6. Injeta falhas para teste
7. Executa fluxos de diagnóstico, remediação ou prevenção
8. Monitora resultados via CloudWatch
9. Limpa recursos quando terminar

## Arquitetura

O workshop implementa um sistema multi-agente para resposta automatizada a incidentes:

![Diagrama de Arquitetura](architecture/architecture.png)

**Componentes Principais:**

1. **Stack da Aplicação CRM**
   - Instâncias EC2 executando servidores web NGINX
   - DynamoDB para persistência de dados
   - CloudWatch para logs e métricas

2. **Sistema de Agentes**
   - **Agente de Diagnóstico**: Analisa logs e métricas do CloudWatch para identificar problemas
   - **Agente de Remediação**: Executa correções usando o Interpretador de Código com fluxos de aprovação
   - **Agente de Prevenção**: Pesquisa melhores práticas usando a ferramenta Browser
   - **Agente Supervisor**: Orquestra todos os agentes e gerencia o fluxo de trabalho

3. **Plataforma AgentCore**
   - **Runtime**: Implantação serverless para agentes
   - **Gateway**: Protocolo MCP para orquestração de ferramentas com autenticação JWT
   - **Interpretador de Código**: Ambiente de execução seguro para scripts de remediação
   - **Browser**: Capacidades de pesquisa web para prevenção
   - **Memória**: Persistência de contexto entre interações

4. **Segurança e Controle de Acesso**
   - Cognito para autenticação de usuários
   - OAuth2 M2M para comunicação agente-a-agente
   - Interceptor Lambda para RBAC granular
   - Autorização baseada em JWT

5. **Interface do Usuário**
   - Aplicação web Streamlit para interação interativa com agentes
   - Respostas em streaming em tempo real
   - Integração com fluxo de aprovação

## Vídeo de Demonstração

Assista ao passo a passo completo do workshop:

![Demo do Workshop](demo/aim301-multi-agent-mcp-agentcore-gateway.gif)

A demonstração mostra:
- Configuração da infraestrutura da aplicação CRM
- Injeção de falhas para simular incidentes reais
- Execução de diagnósticos para identificar problemas
- Execução de remediação com fluxos de aprovação
- Pesquisa de estratégias de prevenção
- Orquestração de todos os agentes através da interface Streamlit

## Estrutura do Workshop

```
├── Lab-01-prerequisites-infra.ipynb             # Lab 1: Pré-requisitos e Configuração de Infraestrutura
├── Lab-02-diagnostics-agent.ipynb               # Lab 2: Agente de Diagnóstico
├── Lab-03a-remediation-agent.ipynb              # Lab 3a: Agente de Remediação + Aprovação
├── Lab-03b-remediation-agent-fgac.ipynb         # Lab 3b: Controle de Acesso Granular
├── Lab-04-prevention-agent.ipynb                # Lab 4: Agente de Prevenção
├── Lab-05-multi-agent-orchestration.ipynb       # Lab 5: Orquestração Multi-Agente + Streamlit
│
├── lab_helpers/                        # Módulos auxiliares importados pelos notebooks
│   ├── lab_01/                        # Helpers específicos do Lab 1
│   ├── lab_02/                        # Helpers específicos do Lab 2
│   ├── lab_03/                        # Helpers específicos do Lab 3
│   ├── lab_04/                        # Helpers específicos do Lab 4
│   ├── lab_05/                        # Helpers específicos do Lab 5 (inclui streamlit_app.py)
│   ├── constants.py                   # Constantes de configuração
│   ├── parameter_store.py             # Utilitários do AWS Parameter Store
│   └── ...                            # Outros utilitários compartilhados
├── requirements.txt                    # Dependências Python
└── README.md                           # Este arquivo
```

## Pré-requisitos

Antes de começar, certifique-se de ter:

- Python 3.10 ou superior
- Jupyter Notebook ou JupyterLab instalado
- Conta AWS com permissões para EC2, DynamoDB, Lambda, CloudWatch, Bedrock
- Credenciais AWS configuradas localmente (ou serão configuradas no Lab 1)

O notebook `Lab-01-prerequisites-infra.ipynb` verificará todos esses itens e instalará quaisquer dependências faltantes.

## Visão Geral dos Labs

**Lab 1: Pré-requisitos e Configuração de Infraestrutura**
- Verificar versão do Python, credenciais AWS e dependências
- Instalar requisitos do workshop e verificar acesso ao Bedrock
- Implantar aplicação CRM (EC2 + NGINX + DynamoDB)
- Configurar Cognito para autenticação
- Configurar monitoramento CloudWatch
- Criar utilitários de injeção de falhas

**Lab 2: Agente de Diagnóstico**
- Construir agente Strands para analisar logs do CloudWatch
- Implantar função Lambda com ferramentas de diagnóstico
- Criar AgentCore Gateway com protocolo MCP
- Testar agente contra logs reais da aplicação

**Lab 3a: Agente de Remediação com Interpretador de Código**
- Implantar agente no AgentCore Runtime
- Integrar Interpretador de Código do AgentCore para execução segura
- Implementar autenticação OAuth2 M2M
- Testar fluxos de remediação

**Lab 3b: Controle de Acesso Granular**
- Criar interceptor Lambda para autorização de requisições
- Implementar controle de acesso baseado em papéis (RBAC)
- Configurar grupos Cognito (Aprovadores vs SRE)
- Testar controle de acesso com diferentes papéis de usuário

**Lab 4: Agente de Prevenção com Browser**
- Implantar agente Runtime com ferramenta Browser do AgentCore
- Pesquisar documentação AWS e melhores práticas
- Gerar playbooks de prevenção
- Autenticação OAuth2 M2M

**Lab 5: Orquestração Multi-Agente com Streamlit**
- Criar agente supervisor para coordenar todos os três agentes
- Configurar AgentCore Gateway central com autenticação JWT
- Reutilizar interceptor do Lab 3b para RBAC
- Implantar sistema multi-agente
- Lançar interface de chat interativa Streamlit com streaming em tempo real
- Testar fluxo de resposta a incidentes de ponta a ponta

## Tecnologias Principais

- **Amazon Bedrock** - Modelos de fundação (Claude 3.7 Sonnet)
- **AgentCore** - Plataforma serverless de agentes
  - Runtime (implantação)
  - Memory (persistência de contexto)
  - Gateway (orquestração de ferramentas com autenticação JWT)
  - Code Interpreter (execução de remediação)
  - Browser (pesquisa e documentação)
  - Observability (monitoramento e rastreamento)
- **Strands Framework** - Framework de agentes para padrões de uso de ferramentas com suporte a streaming
- **Streamlit** - Interface web interativa para interação com agentes em tempo real
- **Serviços AWS** - EC2, DynamoDB, CloudWatch, Lambda, IAM, Cognito, Bedrock
- **Jupyter Notebooks** - Ambiente de aprendizado interativo

## Arquivos do Projeto

### Helpers dos Labs (`lab_helpers/`)
Módulos Python que os notebooks importam para código mais limpo:
- `lab_01/` - Implantação de infraestrutura e injeção de falhas
- `lab_02/` - Implantação Lambda, cliente MCP, configuração do gateway
- `lab_03/` - Implantação Runtime, configuração OAuth2, interceptor
- `lab_04/` - Implantação Runtime, configuração do gateway, logging
- `lab_05/` - Código do agente supervisor, aplicação Streamlit, configuração IAM
- `constants.py` - Constantes de configuração e caminhos de parâmetros
- `parameter_store.py` - Utilitários do AWS Parameter Store
- `config.py` - Configuração do workshop
- `cognito_setup.py` - Configuração do user pool e cliente Cognito
- `short_term_memory.py` - Integração com Memory do AgentCore

## Solução de Problemas

**Se algo der errado:**
1. Verifique a saída do notebook para mensagens de erro
2. Verifique as credenciais AWS na saída de erro
3. Certifique-se de estar na região AWS correta
4. Revise os logs do CloudWatch diretamente do notebook
5. Execute a verificação de pré-requisitos novamente

**Problemas comuns:**
- Credenciais AWS ausentes → Execute `Lab-01-prerequisites-infra.ipynb` novamente
- Modelo Bedrock não acessível → Certifique-se de que o Bedrock está habilitado na sua região
- Timeout do Lambda → Verifique os logs do CloudWatch no notebook
- Recurso já existe → Execute o notebook de limpeza e tente novamente

## Após o Workshop

Para aplicar o que você aprendeu:

1. **No seu próprio ambiente:**
   - Adapte os agentes aos seus sistemas de monitoramento
   - Integre com seu pipeline de implantação
   - Conecte à sua plataforma de gerenciamento de incidentes

2. **Para uso em produção:**
   - Implante agentes no AgentCore Runtime
   - Configure memória persistente para histórico de incidentes
   - Habilite observabilidade e alertas
   - Estabeleça fluxos de aprovação da equipe

3. **Capacidades avançadas:**
   - Orquestração multi-equipe
   - Resposta a incidentes entre contas
   - Desenvolvimento de ferramentas personalizadas
   - Integrações com terceiros

## Recursos

- [Documentação do Amazon Bedrock](https://docs.aws.amazon.com/bedrock/)
- [Documentação do AgentCore](https://docs.aws.amazon.com/agentcore/)
- [GitHub do Strands Framework](https://github.com/aws-samples/strands-agents)
- [AWS re:Invent 2025](https://reinvent.awsevents.com/)

## Licença

Este workshop é fornecido como está sob a Licença MIT.
