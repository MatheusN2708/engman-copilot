# Plano de Manutenção Preventiva — CFTV PTZ, PMV e Callbox

> **DOCUMENTO SIMULADO.** Criado pelo grupo para o Challenge FIAP × Motiva. Não é o plano real da Motiva.
> ID do documento: `PLANO-PREV-01` · Versão 1.0 · Data de referência: 31/10/2026

## 1. Tarefas preventivas

| ID da tarefa | Ativo | Tarefa | Periodicidade | Responsável | Tempo estimado | Recursos | Condição de execução |
|---|---|---|---|---|---|---|---|
| PP-CFTV-01 | CFTV PTZ | Limpeza da lente e da cúpula, com remoção de teias e ninhos | Trimestral (a cada 90 dias) | Técnico de campo | 40 min | Caminhão com cesto aéreo, kit de limpeza óptica | Sem chuva, com sinalização de via (POP-01) |
| PP-CFTV-02 | CFTV PTZ | Teste noturno do iluminador infravermelho (IR), feito pelo CCO | Mensal | Operador do CCO | 10 min | Acesso remoto à câmera | À noite |
| PP-CFTV-03 | CFTV PTZ | Inspeção da fixação do suporte e da base do poste | Semestral | Técnico de campo | 30 min | Cesto aéreo, torquímetro | Sem vento forte (POP-01) |
| PP-PMV-01 | PMV | Teste remoto de pixels e de comunicação | Mensal | Operador do CCO | 15 min | Software de gestão do PMV | Qualquer horário |
| PP-PMV-02 | PMV | Inspeção física, limpeza do painel e do sensor de luminosidade | Semestral | Técnico de campo | 1 h 30 min | Cesto aéreo, kit de limpeza | Sem chuva, com sinalização de via (POP-02) |
| PP-CBX-01 | Callbox | Teste funcional de botão, áudio nos dois sentidos e geolocalização | Mensal | Técnico de campo + CCO | 15 min | Rádio ou telefone com o CCO | Com sinalização de acostamento (POP-03) |
| PP-CBX-02 | Callbox | Medição da tensão da bateria e limpeza do painel solar | Trimestral (a cada 90 dias) | Técnico de campo | 30 min | Multímetro, kit de limpeza | Sem chuva (POP-03) |
| PP-CBX-03 | Callbox | Troca programada da bateria de backup | A cada 24 meses | Técnico de campo habilitado | 1 h | Bateria nova | Equipamento desenergizado (POP-03 e NR-10) |

## 2. Regras para escolher o tipo de intervenção

| Tipo | Quando usar |
|---|---|
| **corretiva** | A falha já aconteceu e o ativo perdeu a função, total ou parcialmente. |
| **inspecao** | O sintoma é intermitente ou a causa ainda não foi confirmada. Antes de trocar peça, é preciso ver o ativo em campo. |
| **preventiva** | Uma tarefa deste plano está vencida ou vence nos próximos 15 dias. |
| **cbm** (manutenção baseada em condição) | Existe um indicador medido que mostra degradação (ex.: tensão da bateria abaixo de 12,0 V, mais de 2% de pixels apagados) **ou** a mesma causa se repetiu 3 ou mais vezes em 12 meses no mesmo TAG. Nesse caso, a recomendação é rever a periodicidade da tarefa ou monitorar o indicador. |

## 3. Regra de revisão do plano

Se um TAG tem 3 ou mais falhas com a **mesma causa** em 12 meses, a periodicidade da tarefa preventiva ligada a essa causa deve ser revista pelo engenheiro de manutenção. O assistente pode **sugerir** a revisão, mas não pode alterar o plano.
