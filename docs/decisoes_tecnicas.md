# Decisões Técnicas — EngMan Copilot (Sprint 01)

Este documento explica as escolhas de stack, a política de contexto e as decisões de arquitetura. A tabela resumida está no README. Nada aqui é código: é o plano que as Sprints 03 e 04 vão executar.

> Informações sobre provedores conferidas em **18/11/2026**. Os modelos gratuitos do OpenRouter mudam com frequência, então os nomes de modelo devem ser conferidos de novo antes da Sprint 03.

---

## 1. Caminho 100% gratuito (resumo)

| Peça | Ferramenta | Custo | Onde roda |
|---|---|---|---|
| LLM | OpenRouter, modelos com sufixo `:free` | R$ 0 (sem cartão) | Nuvem do OpenRouter |
| Embeddings | `intfloat/multilingual-e5-base` (sentence-transformers) | R$ 0 | Google Colab ou máquina do aluno |
| Vector store | ChromaDB | R$ 0 (código aberto) | Google Colab ou máquina do aluno |
| Orquestração | LangChain | R$ 0 (código aberto) | Google Colab |
| Interface (Sprint 04) | Gradio ou Streamlit | R$ 0 | Hugging Face Spaces ou Streamlit Community Cloud |
| Chave de API | Secrets do Colab (`OPENROUTER_API_KEY`) | — | Nunca no repositório |

**Por que mudamos do Ollama para o OpenRouter:** o Ollama Cloud passou a ser pago. O OpenRouter tem uma API compatível com a da OpenAI e vários modelos gratuitos com uma única chave. Na Global Solution da disciplina, o Prof. Jorge aceitou essa troca.

---

## 2. LLM: comparação de modelos

| Critério | **OpenRouter `google/gemma-4-31b-it:free`** (escolhido) | OpenRouter `nvidia/nemotron-3-ultra-550b-a55b:free` (reserva) | Google AI Studio — Gemini Flash (free tier) | Groq (free tier) |
|---|---|---|---|---|
| Custo | Grátis | Grátis | Grátis | Grátis |
| Limite de uso | 20 pedidos/min e 50 pedidos/dia sem créditos | Mesmo limite (é por conta, não por modelo) | Limite diário por modelo | Limite por minuto e por dia |
| Português | Bom (modelo multilíngue) | Bom | Muito bom | Depende do modelo escolhido |
| Tool calling | Suportado pela API do OpenRouter | Suportado | Suportado | Suportado |
| Pesos abertos | **Sim**: o mesmo modelo pode rodar localmente no futuro | Sim | Não | Depende do modelo |
| Privacidade | Endpoints gratuitos podem registrar os prompts | Idem | No free tier, os dados podem ser usados para melhorar o produto | Ver política do provedor |
| Risco de sumir | **Alto**: modelos `:free` saem do catálogo sem aviso | Alto | Baixo | Médio |

**Decisão:** OpenRouter com `google/gemma-4-31b-it:free` como principal. Motivos:

1. É gratuito e usa uma só chave para trocar de modelo, o que facilita comparar modelos no eval set.
2. Tem pesos abertos. Se a Motiva quiser rodar o assistente dentro da empresa (risco de confidencialidade), o mesmo modelo pode ser instalado num servidor próprio, sem refazer o prompt.
3. Usamos a lista de modelos de reserva do OpenRouter (`models: [principal, reserva, "openrouter/free"]`). Se o principal sair do ar ou bater no limite, o pedido vai para o próximo.

**Cuidado com o limite de 50 pedidos por dia:** o eval set tem 14 casos e alguns usam 2 ou 3 chamadas (tool + resposta). Uma rodada completa usa por volta de 35 pedidos. Por isso a Sprint 03 deve rodar o eval set **uma vez por dia** e guardar as respostas em arquivo.

**Reprodutibilidade:** o nome do modelo fica guardado numa única variável. Em cada rodada do eval set, o resultado registra qual modelo respondeu de fato (o OpenRouter informa isso na resposta).

---

## 3. Embeddings e vector store

### Embeddings

| Critério | **`intfloat/multilingual-e5-base`** (escolhido) | `paraphrase-multilingual-MiniLM-L12-v2` |
|---|---|---|
| Português | Sim (multilíngue) | Sim (multilíngue) |
| Tamanho máximo do texto | 512 tokens | 128 tokens |
| Dimensão do vetor | 768 | 384 |
| Custo | Grátis, roda no Colab | Grátis, roda no Colab |

**Decisão:** `multilingual-e5-base`. O outro modelo corta o texto em 128 tokens, e um passo de plano de manutenção ou um boletim passa disso. Detalhe de uso: o e5 pede o prefixo `query: ` na pergunta e `passage: ` nos trechos da base.

### Vector store

| Critério | **ChromaDB** (escolhido) | FAISS |
|---|---|---|
| Filtro por metadado (TAG, tipo_ativo) | **Nativo** (`where={"tipo_ativo": "pmv"}`) | Não tem; precisa de código extra |
| Persistência em disco | Nativa | Manual (salvar o índice) |
| Velocidade | Boa para milhares de trechos | Muito alta, para milhões |
| Integração com LangChain | Sim | Sim |

**Decisão:** ChromaDB. Nossa base é pequena (dezenas de trechos), então a velocidade do FAISS não faz diferença. O filtro por metadado faz: a regra 13 do prompt proíbe misturar ativos, e filtrar por `tipo_ativo` e `tag` na busca evita trazer o boletim de outra câmera para o contexto.

---

## 4. Orquestração

| Critério | **LangChain** (escolhido) | Chamada direta pelo SDK da OpenAI |
|---|---|---|
| Memória, RAG e tools | Peças prontas | Tudo feito à mão |
| Exigência do curso | A Sprint 03 pede LangChain | — |
| Curva de aprendizado | Maior | Menor |

**Decisão:** LangChain, porque a Sprint 03 vai pedir memória, RAG e tools mockadas em LangChain, e a Sprint 04 pede um grafo. O OpenRouter funciona no LangChain pelo cliente compatível com a OpenAI, trocando só a `base_url`.

---

## 5. Política de contexto (quantificada)

Estimativa usando cerca de 3,5 caracteres por token em português.

| Parte do contexto | Tamanho estimado | Regra |
|---|---|---|
| System prompt (9 blocos) | ~3.300 tokens | Fixo em toda chamada |
| Trechos do RAG | até 4 trechos × 400 tokens = **1.600** | Top-4, filtrados por `tipo_ativo` e `tag` |
| Resultado de tools | até **1.500** | Histórico limitado à janela pedida (12 meses = ~6 registros num ativo problemático) |
| Histórico da conversa | últimos **6 turnos** ≈ 2.100 | Turnos mais antigos viram um resumo |
| Resumo dos turnos antigos | até **300** | Refeito a cada 6 turnos |
| TAG ativo | ~20 | Repetido em toda chamada |
| Mensagem do usuário | ~150 | — |
| Resposta (`max_tokens`) | **700** | — |
| **Total por chamada** | **~9.700 tokens** | Teto planejado: 12.000 |

**Por que 6 turnos:** uma conversa de diagnóstico normal tem de 3 a 6 trocas (sintoma, histórico, hipótese, OS). Com 6 turnos completos, a conversa inteira cabe sem resumo na maioria dos casos. Se passar disso, o resumo guarda o essencial (TAG, achados e decisões).

**Por que usar RAG, se a base de hoje caberia inteira no contexto?** A base simulada inteira tem cerca de 9 mil tokens, mais uns 10 mil de normas, fichas e briefing. Até caberia, mas:

1. a base real da Motiva tem milhares de ativos e anos de FSM, e não caberia;
2. modelos menores erram mais com contexto muito longo (perdem informação no meio do texto);
3. menos tokens por chamada deixam a resposta mais rápida, o que importa com os limites do plano gratuito;
4. a Sprint 03 cobra RAG.

---

## 6. Chunking da base (resumo; detalhes no inventário)

| Tipo de documento | Como dividir | Tamanho |
|---|---|---|
| Plano preventivo | 1 trecho por tarefa (linha da tabela) + 1 trecho para as regras de tipo de intervenção | 150–300 tokens |
| Matriz de criticidade | 1 trecho por seção | 200–400 tokens |
| Fichas técnicas | 1 trecho por seção da ficha | 300–400 tokens, sobreposição de 50 |
| Normas (NR-10, NR-35) | 1 trecho por item resumido | 300–400 tokens, sobreposição de 50 |
| Boletins | 1 boletim = 1 trecho | 80–200 tokens |
| Glossário | 1 trecho a cada 8 siglas | ~200 tokens |

Catálogo, histórico FSM e POPs **não** são indexados no RAG: chegam só pelas tools (fonte única de verdade).

---

## 7. As duas decisões mais importantes da arquitetura

1. **Guardrail de saída antes da resposta (bloco 7).** O modelo pode errar mesmo com um bom prompt. Por isso, antes de mostrar a resposta, o sistema confere se todo TAG citado veio de tool ou do usuário, se a resposta tem Fontes e Nível de confiança e se a OS só foi aberta com confirmação. É o que segura alucinação e excesso de confiança, os dois riscos de maior impacto no nosso recorte.
2. **Fonte única de verdade para cada dado.** Catálogo e histórico chegam só por tool, e documentos de texto chegam só pelo RAG. Assim não existem duas cópias do mesmo dado que podem discordar. Quando um boletim (RAG) discorda do histórico (tool), a regra 7 do prompt diz que o histórico prevalece.
