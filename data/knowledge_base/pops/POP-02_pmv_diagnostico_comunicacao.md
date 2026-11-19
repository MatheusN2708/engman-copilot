# POP-02 — PMV: diagnóstico de pixels e de comunicação com o CCO

> **DOCUMENTO SIMULADO.** Criado pelo grupo para o Challenge. Não substitui procedimento oficial da Motiva.
> Tipo de ativo: `pmv` · Intervenções cobertas: `inspecao`, `preventiva`, `corretiva` (troca de módulo de LED)

## Pré-requisitos de segurança (obrigatórios antes de começar)

1. APR preenchida e aprovada pelo supervisor.
2. Sinalização de via montada conforme a regra interna de trabalho em via.
3. Trabalho em altura (acesso ao painel): só com trabalhador **treinado e autorizado pela NR-35**.
4. **Painel energizado não pode ser aberto.** A troca de módulo e qualquer acesso ao quadro elétrico exigem desenergização, bloqueio e etiquetagem, feitos **só por profissional habilitado e autorizado pela NR-10**.
5. Não executar com chuva ou raios.

## Parte A — Diagnóstico remoto (feito do CCO, sem ir a campo)

1. Enviar a mensagem de teste padrão pelo software de gestão do PMV.
2. Conferir se o painel responde ao comando. Se não responder, registrar "falha de comunicação".
3. Rodar o teste de pixels e anotar a porcentagem de pixels apagados.
4. Se a falha for só de comunicação, pedir o reinício remoto do controlador e testar de novo.
5. Se continuar com falha, abrir OS para campo.

## Parte B — Atendimento em campo

1. Avisar o CCO do início do serviço e do TAG atendido.
2. Pedir ao CCO que apague o painel (mensagem em branco) durante o serviço.
3. Profissional habilitado NR-10 desenergiza e bloqueia o painel.
4. Subir com o cesto aéreo e trocar os módulos de LED apontados no teste de pixels.
5. Limpar o sensor de luminosidade.
6. Profissional habilitado reenergiza o painel.
7. Pedir ao CCO o teste de pixels e a mensagem de teste.
8. Retirar a sinalização, avisar o CCO e registrar no FSM.

## Critério de aceite

Menos de 1% de pixels apagados e resposta normal aos comandos do CCO.
