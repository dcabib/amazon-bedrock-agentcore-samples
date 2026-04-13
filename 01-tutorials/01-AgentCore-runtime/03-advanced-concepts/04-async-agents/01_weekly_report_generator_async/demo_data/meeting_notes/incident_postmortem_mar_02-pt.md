# Postmortem de Incidente - Queda de Produção 24 de janeiro de 2026

**ID do Incidente:** INC-2026-0124-001  
**Duração:** 47 minutos (14h15 - 15h02 PST)  
**Severidade:** Alta  
**Impacto:** 100% dos usuários sem conseguir autenticar

## Linha do Tempo
- **14h15** - Alertas de monitoramento disparados para falhas no serviço de autenticação
- **14h18** - Engenheiro de plantão (Samantha Brooks) reconheceu o alerta
- **14h22** - Identificado vazamento de memória no serviço de autenticação causando erros de OOM
- **14h35** - Decisão tomada para reiniciar os pods afetados
- **14h41** - Serviços reiniciados, autenticação parcialmente restaurada
- **14h58** - Todos os serviços saudáveis, monitoramento normal
- **15h02** - Incidente declarado resolvido

## Causa Raiz
Vazamento de memória no serviço de autenticação v2.4.1 causado por limpeza inadequada de objetos de sessão. Sob alta carga, o uso de memória cresceu até que o OOM killer encerrou os processos.

## Resolução
- Imediato: Reiniciou serviços afetados
- Curto prazo: Rollback para v2.4.0 (versão estável)
- Longo prazo: Corrigiu vazamento de memória no código, adicionou profiling de memória ao CI/CD

## Itens de Ação
- [x] Samantha: Implementar alertas de uso de memória (concluído em 25 de jan)
- [ ] Alex: Adicionar detecção de vazamento de memória aos testes automatizados (até 31 de jan)
- [ ] DevOps: Melhorar automação de reinicialização de pods (até 5 de fev)
- [ ] Maya: Enviar comunicação ao cliente sobre o incidente (concluído em 24 de jan)

## Lições Aprendidas
- Necessário melhor profiling de memória no ambiente de staging
- Alertas de monitoramento funcionaram bem - detectados em 3 minutos
- Tempo de resposta ao incidente foi excelente (8 minutos para diagnóstico)
- Deveria ter detectado isso nos testes de carga