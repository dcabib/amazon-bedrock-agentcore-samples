# Workshop End-to-End AgentCore

Este workshop guia você na construção de um agente completo de suporte ao cliente desde o protótipo até a produção usando os serviços Amazon Bedrock AgentCore. O mesmo workshop é implementado em três frameworks agênticos diferentes para que você possa acompanhar com o framework de sua preferência.

> [!IMPORTANT]
> Estes workshops são para fins educacionais. Ele demonstra como os serviços AgentCore são usados ao migrar um caso de uso agêntico de protótipo para produção. Não é destinado para uso direto em ambientes de produção.

## Frameworks

| Framework                                              | Pasta                             | Status      |
| ------------------------------------------------------ | ---------------------------------- | ----------- |
| [Strands Agents](https://strandsagents.com/)           | [strands-agents/](strands-agents/) | Disponível   |
| [Google ADK](https://google.github.io/adk-docs/)       | [google-adk/](google-adk/)         | Em breve |
| [LangGraph](https://langchain-ai.github.io/langgraph/) | [langgraph/](langgraph/)           | Em breve |

## O Que Você Vai Construir

Através de seis laboratórios, você construirá incrementalmente um agente de suporte ao cliente pronto para produção que inclui AgentCore Runtime para implantação serverless, AgentCore Memory para conversas personalizadas, AgentCore Gateway e Identity para ferramentas compartilhadas seguras, AgentCore Policy para controle de acesso refinado com políticas Cedar, AgentCore Observability para rastreamento e monitoramento de comportamento do agente, AgentCore Evaluations para monitoramento contínuo de qualidade e um frontend Streamlit para interação com clientes.

Cada pasta de framework é auto-contida com seu próprio README, notebooks e dependências. Escolha um framework e siga as instruções em seu README para começar.
