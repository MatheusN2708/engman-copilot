# Matriz de Criticidade e Regras de Prioridade

> **DOCUMENTO SIMULADO.** Criado pelo grupo para o Challenge FIAP × Motiva.
> ID do documento: `MATRIZ-CRIT-01` · Versão 1.0 · Data de referência: 31/10/2026

## 1. Criticidade do ativo (fixa, vem do catálogo)

Cada ativo recebe uma nota de 1 a 3 em três critérios. A soma define a criticidade.

| Critério | Nota 1 | Nota 2 | Nota 3 |
|---|---|---|---|
| Segurança viária | Falha não afeta o usuário da via | Falha atrasa a resposta a incidentes | Falha pode deixar o usuário sem socorro ou sem aviso de risco (callbox, trecho de serra/neblina) |
| Disponibilidade para o CCO | Outro ativo cobre a mesma função | Função coberta em parte | Único ativo daquela função no trecho |
| Arrecadação | Não afeta a praça de pedágio | Afeta indiretamente | Afeta a cobrança |

**Pontuação = segurança + disponibilidade + arrecadação**

- **A** (alta): 7 a 9 pontos
- **B** (média): 6 pontos
- **C** (baixa): 3 a 5 pontos

> No nosso recorte (CFTV, PMV e callbox), a nota de arrecadação é sempre 1, porque esses ativos ficam na faixa de domínio e não participam da cobrança.

## 2. Prioridade do atendimento (muda a cada ocorrência)

A prioridade cruza a **criticidade do ativo** com o **efeito da falha**.

| Efeito da falha \ Criticidade | A | B | C |
|---|---|---|---|
| **Perda total** da função | P1 | P2 | P3 |
| **Degradação** (funciona mal ou só às vezes) | P2 | P3 | P4 |
| **Sem impacto na função** (estético, preventivo) | P4 | P4 | P4 |

| Prioridade | Prazo de atendimento |
|---|---|
| P1 — emergencial | até 4 horas |
| P2 — alta | até 24 horas |
| P3 — média | até 7 dias |
| P4 — baixa | na próxima preventiva programada |

## 3. Agravantes (sobem a prioridade)

1. **Callbox sem resposta ao botão** é sempre **P1**, qualquer que seja a criticidade, porque o usuário pode estar sem socorro.
2. **PMV em trecho com neblina ou interdição ativa** sobe **1 nível** (ex.: P3 vira P2).
3. **Reincidência**: se a mesma causa já aconteceu nos últimos 90 dias no mesmo TAG, a prioridade **não muda**, mas a OS deve pedir análise de causa raiz.

## 4. Regra contra viés

A prioridade só pode usar os critérios desta matriz. Região, praça, rodovia, horário do pedido ou quem fez o pedido **não** entram no cálculo.
