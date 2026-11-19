# Inventário da Base de Conhecimento — EngMan Copilot

> Recorte A (Diagnóstico e Confiabilidade) · Ativos: CFTV PTZ, PMV e Callbox · Data de referência dos dados simulados: **31/10/2026**
>
> **Regra de ouro:** tudo que tem origem "SIMULADO" foi criado pelo grupo para o Challenge. Nenhum dado simulado é apresentado como dado real da Motiva. Os arquivos simulados trazem a palavra SIMULADO no nome ou no cabeçalho, e os CSVs têm a coluna `origem = SIMULADO`.

## 1. Como a base chega ao modelo

| Caminho | O que passa por ele | Por quê |
|---|---|---|
| **Tool** (dado estruturado) | Catálogo de ativos, histórico FSM, POPs, OS | Dado exato, que precisa vir completo e sem corte. Fonte única: esses dados **não** são indexados no RAG. |
| **RAG** (texto) | Plano preventivo, matriz de criticidade, fichas técnicas, normas, glossário, briefing, boletins | Texto longo, consultado por semelhança com a pergunta. |

## 2. Documentos

| # | Documento | Arquivo | Origem | Caminho | Campos / conteúdo | Tamanho do chunk | Metadados |
|---|---|---|---|---|---|---|---|
| 1 | Catálogo de ativos | `catalogo_ativos_simulado.csv` | **SIMULADO** (32 ativos: 14 CFTV, 9 PMV, 9 callbox) | Tool `consultar_ativo` | tag, tipo_ativo, fabricante, modelo, rodovia, km, sentido, trecho, data_instalacao, vida_util_anos, notas da matriz, pontuacao, criticidade, observacao, origem | Não se aplica (1 linha = 1 ativo) | tag, tipo_ativo, criticidade |
| 2 | Histórico de falhas FSM | `historico_falhas_fsm_simulado.csv` | **SIMULADO** (59 registros, nov/2024 a out/2026) | Tool `consultar_historico_falhas` | id_registro, tag, data_abertura, sintoma, causa_identificada, tipo_intervencao, tempo_reparo_h, peca_trocada, condicao_ambiental, reincidencia_90d, origem | Não se aplica (1 linha = 1 falha) | tag, data_abertura, tipo_intervencao |
| 3 | POPs de manutenção (3) | `pops/POP-01…03` | **SIMULADO** | Tool `consultar_procedimento` | pré-requisitos de segurança, passo a passo, critério de aceite | Não se aplica (o POP volta inteiro) | pop_id, tipo_ativo, tipos_intervencao |
| 4 | Plano de manutenção preventiva | `plano_manutencao_preventiva.md` (`PLANO-PREV-01`) | **SIMULADO** | RAG | tarefa, ativo, periodicidade, responsável, tempo, recursos, condição; regras de tipo de intervenção | 1 tarefa por trecho (150–300 tokens) | doc_id, tipo_ativo, id_tarefa |
| 5 | Matriz de criticidade e prioridade | `matriz_criticidade.md` (`MATRIZ-CRIT-01`) | **SIMULADO** | RAG | critérios e pesos, faixas A/B/C, tabela de prioridade P1–P4, agravantes, regra contra viés | 1 seção por trecho (200–400 tokens) | doc_id, secao |
| 6 | Boletins de ocorrência | `boletins_ocorrencia_simulados.md` (11 boletins) | **SIMULADO** | RAG | TAG, data, id_registro FSM, condição ambiental, relato do técnico (sem nome) | 1 boletim por trecho (80–200 tokens) | tag, tipo_ativo, data, id_registro |
| 7 | Glossário de siglas | `glossario.md` | **Real** (siglas do briefing e da disciplina; texto do grupo) | RAG | sigla, significado, explicação | 8 siglas por trecho (~200 tokens) | doc_id |
| 8 | Normas de segurança (NR-10 e NR-35) | *a produzir na Sprint 03*: resumo dos itens aplicáveis, com link para o texto oficial | **Real** (texto oficial do Ministério do Trabalho e Emprego) | RAG | item da norma, resumo, link oficial | 300–400 tokens, sobreposição de 50 | norma, item |
| 9 | Fichas técnicas dos equipamentos | *a produzir na Sprint 03*: uma ficha por modelo (PTZ-AX30, PTZ-B20IR, PMV-G48, CB-D2-SOLAR) | **SIMULADO** (fabricantes fictícios) | RAG | especificação, condições de operação, falhas típicas, peças de reposição | 1 seção por trecho (300–400 tokens), sobreposição de 50 | modelo, tipo_ativo, secao |
| 10 | Briefing do Challenge FIAP × Motiva | PDF oficial (não copiado no repositório; fica o link/referência) | **Real** | RAG | contexto da Motiva, dores, glossário do briefing | 400 tokens, sobreposição de 50 | doc_id, secao |

**Total: 10 documentos** (8 já produzidos no repositório, 2 planejados para a Sprint 03).

## 3. Ativos-persona (histórico sintético rico)

Quatro ativos têm histórico completo e coerente, para os testes do eval set ficarem realistas:

| TAG | Criticidade | Falhas em 12 meses | História |
|---|---|---|---|
| `CFTV-R100-K412-N` | A | 6 | Perda de imagem noturna. Causa mais comum: teia de aranha refletindo o infravermelho (4 vezes). Também teve lente suja e iluminador IR queimado. |
| `CFTV-R100-K415-N` | A | 4 | Imagem tremida e desfocada por vibração do poste perto da ponte. Terminou com fissura na base do poste. |
| `PMV-R100-K410-S` | A | 5 | Módulos de LED queimados, um surto depois de temporal e desgaste geral dos LEDs (painel com 9 anos). |
| `CBX-R100-K413-S` | A | 4 | Callbox na serra, sem sinal de celular. Falhas de bateria, painel solar sujo, microfone com umidade e controlador de carga. |

Exemplo de métrica para o `CFTV-R100-K412-N` (12 meses = 8.760 h): λ = 6 / 8.760 ≈ 0,000685 falha/h; MTBF ≈ 1.460 h (cerca de 61 dias); R(720 h) = e^(−0,000685 × 720) ≈ 0,61. Ou seja, a chance de passar 30 dias sem falhar é de cerca de 61%.

## 4. O que NÃO entra na base (e por quê)

| Não entra | Por quê |
|---|---|
| Imagens das câmeras (vídeo e fotos do CFTV) | Podem mostrar pessoas e veículos: dado pessoal (LGPD). Também não são necessárias para diagnóstico de manutenção. |
| Placas de veículos e leituras de OCR | Dado pessoal, e fora do recorte (laços, pórticos e OCR ficaram de fora). |
| Nome, CPF, telefone e matrícula de técnicos e usuários | Dado pessoal. Os boletins trocam o nome pela função ("Técnico de campo"). |
| Gravação de áudio das chamadas do callbox | Dado pessoal do usuário da rodovia. Só entra o fato técnico ("áudio unidirecional"). |
| Contratos, valores de compra e dados de fornecedores | Confidencial e fora do objetivo do assistente. |
| Coordenadas exatas e esquemas da rede de comunicação | Informação sensível de segurança da infraestrutura. Usamos só rodovia, km e sentido. |
| Dados reais da Motiva (FSM, catálogo, OS) | Não temos acesso e não podemos inventar dados e dizer que são reais. Por isso a base é simulada. |

## 5. Atualização da base

- Catálogo e histórico: lidos direto pela tool a cada pergunta (sem cópia no RAG), então estão sempre atualizados.
- Documentos do RAG: reindexados quando um documento mudar de versão. Cada trecho guarda o `doc_id` e a versão, para a resposta citar a fonte certa.
