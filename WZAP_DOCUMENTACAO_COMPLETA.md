# Documentação Completa - WZAP.chat
## Ferramenta de Atendimento WhatsApp para Equipes

### Índice
1. [Visão Geral](#visão-geral)
2. [Status de Chats](#status-de-chats)
3. [Sistema de Atribuição](#sistema-de-atribuição)
4. [Problemas Identificados](#problemas-identificados)
5. [Procedimentos Operacionais](#procedimentos-operacionais)
6. [Webhook Events](#webhook-events)
7. [Boas Práticas](#boas-práticas)

---

## Visão Geral

O **WZAP.chat** é uma plataforma completa de gerenciamento de WhatsApp Business que permite:
- Atendimento com múltiplos agentes simultâneos
- Automação via API e webhooks
- Controle de status de conversas em tempo real
- Histórico completo de conversas
- Indicadores de performance da equipe

### Funcionalidades Principais
- ✅ Envio de mensagens de texto, imagens, áudio, vídeos e documentos
- ✅ Confirmações de entrega e leitura
- ✅ Webhooks em tempo real
- ✅ Marcar chats como lidos
- ✅ Transferência entre agentes
- ✅ Dashboard com métricas
- ✅ Histórico exportável

---

## Status de Chats

### Status Disponíveis no Sistema

| Status | Descrição | Quando Usar |
|--------|-----------|-------------|
| **Novo** | Chat recém-iniciado pelo cliente | Automaticamente quando cliente envia primeira mensagem |
| **Em Atendimento** | Chat atribuído a um agente | Quando agente assume o chat |
| **Aguardando** | Esperando resposta do cliente | Após enviar mensagem e aguardar retorno |
| **Pendente** | Necessita ação posterior | Para follow-ups ou questões que precisam ser resolvidas depois |
| **Finalizado** | Atendimento concluído | **SEMPRE** ao finalizar um atendimento |
| **Transferido** | Chat foi passado para outro agente/setor | Durante transferências |

### Eventos de Webhook por Status

```
- aoenviar (ao enviar mensagem)
- aoreceber (ao receber mensagem)
- aoconectar (conectar instância)
- aodesconectar (desconectar instância)
- aopresencadochat (presença no chat)
- aoreceberstatusdamsg (status da mensagem)
```

---

## Sistema de Atribuição

### Tipos de Atribuição

1. **Automática**
   - Distribui chats por disponibilidade
   - Round-robin entre agentes online
   - Baseada na carga de trabalho atual

2. **Manual**
   - Supervisor atribui chat específico para agente
   - Útil para casos especializados
   - Permite reatribuição a qualquer momento

3. **Por Setor**
   - Vendas, Suporte, Financeiro, etc.
   - Roteamento automático por palavras-chave
   - Especialização por tipo de demanda

### Regras de Atribuição

- ⚡ Chat novo: atribuição automática em até 30 segundos
- 🔄 Transferência: notificação imediata para novo agente
- 🚨 Chat sem resposta > 15min: alerta para supervisor
- 📊 Balanceamento: distribui carga entre agentes disponíveis

---

## Problemas Identificados

### ❌ Problema Principal: Chats Não Finalizados

**Situação Atual:**
- Agentes não clicam em "Finalizar Chat"
- Chats ficam em status "Em Atendimento" indefinidamente
- Sistema mostra tempo de resposta muito alto
- Métricas de performance distorcidas

**Consequências:**
- Dashboard com dados incorretos
- Dificuldade para medir produtividade real
- Chats "perdidos" no sistema
- Clientes podem receber atendimento duplicado

### 🔍 Outras Questões Identificadas

1. **Falta de Padronização**
   - Cada agente usa status diferentes
   - Critérios inconsistentes para finalização

2. **Ausência de Monitoramento**
   - Nenhum controle de chats em aberto
   - Supervisão reativa, não proativa

3. **Treinamento Inadequado**
   - Equipe não compreende impacto dos status
   - Falta de procedimentos claros

---

## Procedimentos Operacionais

### 📋 Rotina Obrigatória para Agentes

#### 1. Início do Atendimento
```
✅ Assumir chat em até 2 minutos
✅ Alterar status para "Em Atendimento"
✅ Enviar saudação padronizada
✅ Identificar necessidade do cliente
```

#### 2. Durante o Atendimento
```
✅ Manter status atualizado conforme situação
✅ Usar "Aguardando" quando esperar resposta do cliente
✅ Usar "Pendente" para follow-ups necessários
✅ Transferir com contexto quando necessário
```

#### 3. **🎯 FINALIZAÇÃO (OBRIGATÓRIO)**
```
✅ Confirmar resolução da demanda
✅ Perguntar se cliente tem mais dúvidas
✅ Enviar agradecimento de encerramento
✅ CLICAR EM "FINALIZAR CHAT"
✅ Adicionar observação interna se necessário
```

### ⏰ Tempos Máximos por Status

| Status | Tempo Máximo | Ação |
|--------|--------------|------|
| Novo | 2 minutos | Assumir ou escalar |
| Em Atendimento | 15 minutos sem atividade | Atualizar status ou finalizar |
| Aguardando | 2 horas | Follow-up ou finalizar |
| Pendente | 24 horas | Resolver pendência |

### 🚨 Alertas Automáticos

- **15 minutos**: Chat sem resposta → Notificar agente
- **30 minutos**: Chat sem atividade → Notificar supervisor
- **2 horas**: Chat em "Aguardando" → Follow-up obrigatório
- **4 horas**: Chat não finalizado → Revisão obrigatória

---

## Webhook Events

### Configuração de Notificações

```json
{
  "aoreceber": {
    "action": "notify_agent",
    "priority": "high"
  },
  "aoreceberstatusdamsg": {
    "action": "update_dashboard",
    "priority": "medium"  
  },
  "aoenviar": {
    "action": "log_activity",
    "priority": "low"
  }
}
```

### Automações Recomendadas

1. **Auto-finalização**
   - Finalizar chats sem atividade > 24h
   - Apenas se cliente não respondeu

2. **Reatribuição Automática**
   - Chats sem resposta > 30min
   - Redistribuir para agente disponível

3. **Notificações Internas**
   - Alertas por Slack/Teams
   - Dashboard em tempo real

---

## Boas Práticas

### ✅ Para Agentes

1. **Responsividade**
   - Responder em até 5 minutos
   - Manter cliente informado sobre demoras

2. **Comunicação**
   - Linguagem clara e profissional
   - Confirmar entendimento antes de finalizar

3. **Organização**
   - Manter status sempre atualizado
   - **SEMPRE finalizar chats resolvidos**

4. **Colaboração**
   - Transferir com contexto completo
   - Avisar colegas sobre transferências

### ✅ Para Supervisores

1. **Monitoramento**
   - Revisar dashboard 3x por dia
   - Acompanhar chats sem finalização

2. **Treinamento**
   - Sessões semanais sobre procedimentos
   - Feedback individual regular

3. **Métricas**
   - Acompanhar tempo médio de resposta
   - Taxa de finalização de chats
   - Satisfação do cliente

### 📊 KPIs Essenciais

| Métrica | Meta | Frequência |
|---------|------|------------|
| Tempo Primeira Resposta | < 2 min | Diária |
| Taxa de Finalização | > 95% | Diária |
| Chats Pendentes | < 10 | Tempo Real |
| Satisfação Cliente | > 4.5/5 | Semanal |
| Chats por Agente/Hora | 8-12 | Diária |

---

## Implementação Gradual

### Fase 1 (Semana 1-2): Conscientização
- Treinamento sobre impacto dos status
- Demonstração das métricas distorcidas
- Compromisso da equipe com mudança

### Fase 2 (Semana 3-4): Procedimentos
- Implementar rotina de finalização
- Criar lembretes automáticos
- Monitoramento diário

### Fase 3 (Semana 5-6): Automação
- Configurar webhooks
- Implementar alertas automáticos
- Dashboard de acompanhamento

### Fase 4 (Semana 7+): Otimização
- Ajustar procedimentos baseado em dados
- Treinamento avançado
- Melhoria contínua

---

**📞 Suporte Técnico WZAP:** Entre em contato através da plataforma para configurações avançadas e integrações personalizadas.

**📈 Resultado Esperado:** Redução de 80% nos chats não finalizados e melhoria significativa nas métricas de atendimento em 30 dias.