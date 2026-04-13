# Habilitar Pesquisa de Transações para Observabilidade do Amazon Bedrock AgentCore

Este tutorial demonstra como habilitar o Amazon CloudWatch Transaction Search para observabilidade do AgentCore. O Transaction Search fornece uma experiência analítica interativa para visibilidade completa dos spans e traces de transações da sua aplicação em sistemas distribuídos.

## Primeiros Passos

A pasta do projeto contém o seguinte:

- Um notebook Jupyter demonstrando como habilitar o Transaction Search usando CloudFormation
- Um template CloudFormation (transaction_search.yml) para implantação automatizada
- Imagens de exemplo mostrando antes e depois da habilitação do Transaction Search

## Limpeza

Após concluir o tutorial:

1. Exclua a stack do CloudFormation: `transaction-search`
2. Isso remove a política de recursos e desabilita o Transaction Search
3. Traces e logs existentes são retidos de acordo com as políticas de retenção
