# criacomp

> Wiki detalhada do repositório **criacomp** — disciplina Criatividade Computacional (IF866), Centro de Informática da UFPE (CIn-UFPE)

- **Repositório original:** https://github.com/lucaslimacodes/criacomp
- **Projeto original:** https://github.com/filipecalegario/criacomp
- **Branch principal:** `main`
- **Edição atual:** 2026.2 (semestre 2 de 2026)
- **Tipo do repositório:** MIXED — Markdown + Jupyter Notebooks + CSVs
- **Total de arquivos:** 36 (17 `.md`, 17 `.ipynb`, 2 `.csv`)
- **Tamanho total:** ~6.9 MB
- **README original:** 161 linhas, 9903 chars
- **Wiki deste repositório:** [repos/criacomp/](./criacomp/) (esta pasta)

---

## Visão Geral

O repositório **criacomp** é o espaço de trabalho oficial da disciplina **Criatividade Computacional (IF866)**, oferecida pelo Centro de Informática da Universidade Federal de Pernambuco (CIn-UFPE). A disciplina é descrita como **experimental** e promove a integração entre **tecnologias de IA generativa** e o **processo criativo humano**, com enfoque prático em uso criativo, reflexivo e inovador de ferramentas de IA — interfaces, experimentação multimodal e impacto real no processo criativo.

O repositório serve múltiplas funções simultâneas:

1. **Recursos didáticos** — notebooks Jupyter introdutórios que ensinam a usar APIs de IA (OpenAI, Gemini), ferramentas de interface (Gradio), geração de imagem (Stable Diffusion), avaliação com LangChain, e embeddings semânticos.
2. **Notícias curadas semanalmente** — `2026-2-NEWS.md` lista ferramentas, modelos, lançamentos e discussões críticas sobre IA generativa, atualizada a cada semana de aula, com data mais recente no topo.
3. **Coleção de prompts** — `PROMPT_COLLECTION.md` guarda os prompts usados em aula para referência.
4. **Skills de projeto autoral** — quatro skills de ideação e crítica em Markdown puro para grupos usarem durante o Projeto Autoral (2026.2).
5. **Arquivo de edições anteriores** — pasta `ARCHIVED/` com notebooks e notícias de edições de 2023.1 a 2025.1.

Três coisas que o repositório **não** é:

- Não é um pacote instalável — é um repositório de materiais de aula.
- Não exige ferramenta paga ou agente de código específico — qualquer alternativa aberta serve.
- Não avalia se um artefato foi feito por pessoa ou por IA — nenhuma rubrica contém critério de esforço humano, originalidade ou detecção de conteúdo gerado.

---

## Estrutura do Repositório

```
criacomp/
├── README.md                              # README principal (161 linhas, ~9.9KB)
├── CLAUDE.md                             # Instruções contextuais para Claude Code
├── 2026-2-NEWS.md                        # Notícias da edição atual (90 linhas, ~9.5KB)
├── 2026-2-NOTES.md                       # Comentários e contexto dos links (49 linhas, ~4.7KB)
├── PROMPT_COLLECTION.md                  # Coleção de prompts usados em aula (~3.8KB)
├── .git/                                 # Git configurado, origin: github.com/lucaslimacodes/criacomp
│
├── ARCHIVED/                             # Edições anteriores (2023.1 → 2025.1)
│   ├── 2023_1_news.md                    # Tendências e Notícias - 2023.1
│   ├── 2023_2_news.md                    # Tendências e Notícias - 2023.2
│   ├── 2024_1_news.md                    # Tendências e Notícias - 2024.1
│   ├── 2024_2_news.md                    # Tendências e Notícias - 2024.2
│   ├── 2025_1_news.md                    # Tendências e Notícias - 2025.1
│   │
│   ├── ideias_projetos.md                # Ideias de projetos (arquivado)
│   ├── critical_views.md                 # Visões críticas (arquivado)
│   ├── aux_to_image_synth_timeline.md    # Linha do tempo da síntese de imagem (arquivado)
│   │
│   ├── [2023_1] CRIACOMP Word Embeddings.ipynb        # Embeddings (2023.1)
│   ├── [2024_1] CRIACOMP Word Embeddings.ipynb        # Embeddings (2024.1)
│   ├── 2024_2_CRIACOMP_Word_Embeddings.ipynb          # Embeddings (2024.2)
│   │
│   ├── [2024.1] OpenAI Embeddings.ipynb               # Embeddings OpenAI (2024.1)
│   ├── Jina_Embedding.ipynb                          # Embeddings Jina
│   │
│   ├── 2023_1_CRIACOMP_UMAP+Observables+widget.ipynb  # UMAP + visualização (2023.1)
│   ├── OLD_REFERENCE_CRIACOMP_Word_Embeddings.ipynb   # Referência antiga
│   ├── OLD_REFERENCE_UMAP+Observables+widget.ipynb    # Referência UMAP antiga
│   │
│   ├── Copy_of_insanely_fast_whisper_colab.ipynb      # Whisper (colab)
│   ├── Pyannote_plays_and_Whisper_rhymes_v_1_0.ipynb  # Pyannote + Whisper
│
├── embeddings/                           # Word embeddings e visualização (edição 2024.2)
│   ├── word_embeddings.csv               # Vetores de embedding (~2.5 MB, 50 palavras/usos)
│   ├── words.csv                         # Lista de palavras/frases (42 linhas)
│   └── 2024_2_CRIACOMP_Embeddings_and_Visualization.ipynb  # Notebook de visualização UMAP
│
├── primeiros-passos/                     # Notebooks introdutórios (hands-on)
│   ├── Primeiros_passos_com_a_API_da_OpenAI.ipynb       # Primeiros passos OpenAI API
│   ├── [CRIACOMP]_Gemini_Simple_Request.ipynb          # Requisição simples Gemini Pro
│   ├── [CRIACOMP]_Gemini_and_Stable_Diffusion.ipynb    # Gemini + Stable Diffusion
│   ├── [CRIACOMP]_Simple_Evaluator_with_LangChain_and_OpenAI.ipynb  # Avaliador com LangChain
│   ├── 2024_1_CRIACOMP_Experimentando_com_GRADIO.ipynb  # Experimentando Gradio (chat, streaming)
│   └── open_deep_researcher.ipynb                      # Deep Research (abrir)
│
└── skills/                               # Skills de ideação e crítica (Markdown puro, 2026.2)
    ├── README.md                         # Documentação das skills (67 linhas, ~4.1KB)
    ├── abrir-o-leque/                   # Gera possibilidades (passo 1 do PA)
    │   └── SKILL.md                     # 69 linhas, ~3.4KB
    ├── afiar-o-eixo/                   # Entrevista até o eixo ficar específico (passo 3)
    │   └── SKILL.md                     # 85 linhas, ~4.9KB
    ├── derrubar-a-ideia/               # Advogado do diabo (quando acha que está pronto)
    │   └── SKILL.md                     # 80 linhas, ~3.8KB
    └── escutar-a-reuniao/              # Transforma gravação de reunião em material de trabalho
        └── SKILL.md                     # 85 linhas, ~4.6KB
```

### Inventário de arquivos

| Categoria | Quantidade | Tipos |
|-----------|-----------|-------|
| Markdown | 17 | `.md` — README, notícias, notas, prompts, skills |
| Jupyter Notebooks | 17 | `.ipynb` — notebooks introdutórios + archived |
| Dados | 2 | `.csv` — `word_embeddings.csv` (~2.5MB), `words.csv` (42 linhas) |
| **Total** | **36** | **~6.9 MB** |

---

## Disciplina: Criatividade Computacional (IF866)

### O que é

Disciplina experimental do CIn-UFPE que integra **tecnologias de IA generativa** com o **processo criativo humano**. O foco é prático: uso criativo, reflexivo e inovador de ferramentas de IA, com ênfase em interfaces, experimentação multimodal e impacto real no processo criativo.

### O que você vai aprender

- Conceitos, modelos e teorias da criatividade, e a sua leitura computacional
- As duas grandes frentes da área: ferramentas de suporte à criatividade e geração automática de conteúdo
- Intuição de como a IA generativa cria: espaço latente, difusão e transformers, sem matemática pesada
- Criação nas **cinco modalidades**: imagem, vídeo, texto, código, áudio e música
- Agentes de código na prática: anatomia do harness (prompt, contexto, ferramentas, runtime) e protocolos de integração
- Bases de conhecimento legíveis por modelos de linguagem, e automação sobre a própria base
- Encadeamento de modelos e agentes em workflows criativos, incluindo execução local
- Projeto de interface e de arenas de criação: como se desenha o lugar onde a outra pessoa cria
- Formas de comparar ferramentas, plataformas, modelos e bibliotecas
- Frameworks de avaliação de sistemas de criatividade computacional, e as armadilhas de cada um
- Crítica, ética e implicações sociais da criação com IA: poder e infraestrutura, dívida cognitiva, soberania, futuro do trabalho criativo
- Técnicas de design para **encapsulamento de complexidade** em ferramentas de criatividade computacional

### O que NÃO é coberto

> ❗ Esta disciplina **não cobre detalhes de arquiteturas de redes neurais ou treinamento de modelos**. O foco é no uso, integração e impacto criativo.

---

## Como a disciplina é avaliada

Três componentes, e **nenhuma prova**. Os pesos **não** são fixados no início do semestre: são discutidos com a turma no último dia de aula, a partir do esforço que de fato foi investido. Duas coisas valem como bússola desde a primeira aula: os três componentes pesam de forma comparável, e dentro do Portfólio o Experimento vale mais que a Atividade.

### 1. Portfólio (individual, o semestre inteiro)

Pasta individual onde se acumulam as **Peças**: experimentos pequenos, registrados junto com o relato do processo que os produziu. Toda Peça responde a quatro perguntas: o que você quis tentar, o que usou, o que aconteceu, o que aprendeu.

- Uma Peça nascida de um estímulo do professor é uma **Atividade**.
- Uma Peça nascida de iniciativa própria é um **Experimento**, e vale mais. Não há cota mínima: exigir uma quantidade transformaria iniciativa em tarefa.
- Contribuir com o arquivo de notícias deste repositório conta como Experimento.

O que se avalia é o **registro**, não o artefato, em dois eixos:

- **Especificidade**: o relato permite que outra pessoa refaça o caminho?
- **Risco**: você tentou algo que podia não funcionar e registrou o que aconteceu quando não funcionou?

Decorre daí que uma Peça que falhou com registro específico vale mais que uma Peça que funcionou com registro genérico.

As Peças são publicadas na **Galeria** da disciplina, visível para a turma.

### 2. Projeto Autoral (em grupo)

O grupo escolhe um **eixo**, se apropria das ferramentas que aquele eixo pede, e entrega uma **coleção de artefatos** gerados por ele, junto com o registro de como chegou nela.

O que se avalia não é o artefato mais bonito da coleção: é a **coerência** entre o eixo declarado, o que entrou e o que ficou de fora. Doze arquivos na mesma pasta são um **lote**, e o que transforma lote em coleção é o **eixo**.

### 3. Projeto Ferramenta (em grupo)

Um degrau acima na **Escada de Abstração**: o grupo deixa de ser quem usa ferramentas para criar um artefato e passa a ser quem constrói a ferramenta que permite que outra pessoa crie.

A ferramenta precisa funcionar de verdade, e é testada em **Avaliação Cruzada** por gente de outro grupo, sem ninguém do grupo explicando por cima do ombro. O bloco é escalonado em entregas pequenas, de uma exploração inicial de três ideias até o sistema congelado.

### E ainda

- Um **Relatório Individual** ao fim do semestre, sobre a própria travessia: o que se vivenciou, o que se aprendeu e o que não se aprendeu, qual foi o desafio e qual foi o erro.
- A nota dos projetos é triangulada entre três fontes: o professor, os pares de outros grupos (que respondem a questionários durante as apresentações e a Avaliação Cruzada) e os próprios integrantes do grupo, numa **Avaliação 360** ao fim de cada projeto. É o que permite que a nota varie entre integrantes do mesmo grupo.

### Vocabulário

| Termo | Definição |
|-------|-----------|
| **Peça** | Item individual do Portfólio. Responde às 4 perguntas: o que quis tentar, o que usou, o que aconteceu, o que aprendeu. |
| **Artefato** | Item da coleção do Projeto Autoral. Distinto de Peça em propósito. |
| **Eixo** | Restrição que o grupo se impõe; a pergunta que a série responde; aquilo que se mantém enquanto o resto varia. Sem eixo, doze artefatos na mesma pasta são um lote, não uma coleção. |
| **Atividade** | Peça nascida de um estímulo do professor. |
| **Experimento** | Peça nascida de iniciativa própria. Vale mais que Atividade. |

### Regras de avaliação (não negociáveis)

> ❗ Esta disciplina **não tem avaliação baseada em provas**.

> ❗ A disciplina **não avalia, nem discute para efeito de nota, se um artefato foi feito por uma pessoa ou por uma IA**. Nenhuma rubrica contém critério de esforço humano, originalidade ou detecção de conteúdo gerado.

> ❗ **Nenhuma entrega depende de ferramenta paga ou de agente de código específico.** Onde a aula citar uma marca, qualquer alternativa aberta serve.

---

## Projetos de Períodos Anteriores

O Projeto Ferramenta chamava-se **Projeto Final** nas edições abaixo.

<details>
  <summary><strong>📆 2024.2</strong></summary>

- **EducaHits**: Matérias escolares viram música.
- **PrintItAll!**: Modelos 3D gerados com uma única imagem.
- **Cybersong**: Criar vídeos a partir de músicas.
- **Bizu**: Plataforma de estudos com IA adaptativa.
- **AIdvertise**: Anúncios inteligentes baseados em fotos.
- **IAnk**: Flashcards automáticos para o Anki.
- **MIR.AI**: Filmes interativos gerados por IA.
- **LOFY AI**: Vídeos personalizados com base no humor.
- **MemeficaAI BR**: Geração de vídeos a partir de áudios de memes.
- **Tas Sabendo?**: Podcasts viram resumos e flashcards.
</details>

<details>
  <summary><strong>📆 2024.1</strong></summary>

- **Chatbot Hoteleiro**: Assistente automatizado para hotéis.
- **CInLogos**: Logos e jingles gerados por IA.
- **PodcastGen**: Transformação de notícias em podcasts.
- **ProvAI**: Criação automatizada de provas.
- **CharMix**: Criação de personagens a partir de imagens.
- **Travel With AI**: Roteiros personalizados de viagem.
- **NotiCast**: Podcasts gerados de notícias.
- **Histórias CInistras**: Jogo de mistério com IA.
- **ReceitIA**: Receitas e vídeos gerados a partir de ingredientes.
- **Narrativas Inovadoras**: HQs viram filmes interativos.
</details>

<details>
  <summary><strong>📆 2023.2</strong></summary>

- **FlashFlow**: Flashcards para medicina.
- **capivar.ia**: Newsletters automatizadas.
- **Monkey Typewriter**: Sistema de apoio à escrita criativa.
- **Tale Genius**: Storytelling educativo com IA.
- **Summarize & Visualize**: Apresentações a partir de resumos.
- **ToonCraft**: Histórias ilustradas geradas por IA.
- **Apollo.mp3**: Singles e capas a partir de texto e imagem.
- **comedIAds**: Comerciais gerados a partir do nome do produto.
- **SeasonAI Dress**: Coleções de roupas por paleta e estilo.
</details>

---

## Notícias da Edição Atual (2026.2)

Arquivo: [`2026-2-NEWS.md`](2026-2-NEWS.md) — 90 linhas, ~9.5 KB.

Lista curada dos links discutidos no início de cada aula: ferramentas, modelos, lançamentos, experimentos e discussões críticas sobre IA generativa. Atualizada semanalmente, com a data mais recente sempre no topo. Alguns links vêm com comentário, contexto e pauta de discussão em [`2026-2-NOTES.md`](2026-2-NOTES.md).

### Temas da edição (agosto–setembro 2026)

O período coberto pelas notícias abrange **27/08/2026 a 10/09/2026**, com os seguintes temas recorrentes:

- **Segurança e alinhamento de IA**: alertas de pesquisadores do Anthropic sobre risco existencial, incidente do Hugging Face com agentes se auto-organizando, relatórios de incidentes de segurança do Claude.
- **Geopolítica da infraestrutura**: Google investindo €1 bilhão em data center na Finlândia, US e China em diálogos de segurança de IA, Nvidia comprando Hugging Face por US$ 12.9 bilhões.
- **Emprego e impacto social**: Stanford sobre impacto da IA concentrado em vagas de entrada, Bank of England alertando sobre cibersegurança e economia global, Lego mantendo aposta em criatividade humana.
- **Frontier models e inovação**: GPT-6-Astra do OpenAI, solução do problema de Navier-Stokes por IA, código gerando aquarelas com RL, Suno lançando modelos de música com Warner Music.
- **Perguntas filosóficas**: quem escreve (watermarking e remoção), quem executa (harness do DeepSeek), quem avalia (MatrAIx com 8.3 bilhões de personas simuladas).
- **Aplicações locais**: startup pernambucana Aicury em destaque no Prêmio Sebrae Startups 2026.

### Notas de aula

Arquivo: [`2026-2-NOTES.md`](2026-2-NOTES.md) — 49 linhas, ~4.7 KB.

Comentários e contexto sobre os links, com pautas de discussão. Três entradas detalhadas:

1. **Google picks Finland for its largest single investment in Europe** (10/09) — investimento de €1B em Hamina, energia 100% renovável, soberania tecnológica e geopolítica da infraestrutura de IA.
2. **Modelo gera aquarelas escrevendo código** (06/09) — Hugging Face treina Qwen3.5 com p5.brush em JavaScript, sem modelos de difusão, usando RL com 178 referências manuais para avaliar estética.
3. **Quanto tempo um agente deve viver? TTL como decisão de arquitetura** (06/09) — Tomasz Tunguz argumenta que agentes de vida longa esquecem instruções, propondo coordenador ~1 dia + especialistas ~30 segundos + estado em arquivos comuns. Discussão sobre memória como superfície de ataque.
4. **MatrAIx: Simulating the World with 8.3 Billion Persona Agents** (20/08) — infraestrutura de avaliação em escala populacional com 8.3B registros de persona, 1.290 dimensões categóricas, coreset de ~1M. Pergunta central: o que sobra de irredutivelmente humano quando autoria, execução e recepção viram todas simuláveis?

---

## Notebooks: primeiros-passos

A pasta [`primeiros-passos/`](primeiros-passos/) contém notebooks Jupyter introdutórios, todos com badge de **Open in Colab** e projetados para executar no Google Colab com API keys armazenadas como secrets.

### [CRIACOMP] Simple Evaluator with LangChain and OpenAI

- **Arquivo:** `primeiros-passos/[CRIACOMP]_Simple_Evaluator_with_LangChain_and_OpenAI.ipynb`
- **Objetivo:** Criar um avaliador simples com LangChain + OpenAI que classifica se uma frase menciona algum animal.
- **Conceitos:** `ChatOpenAI`, `ChatPromptTemplate`, `HumanMessage`, eval com pandas, comparação ground truth vs. LLM output.
- **Modelo usado:** `gpt-4-1106-preview`
- **Código principal:**

```python
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage
from langchain.prompts.chat import ChatPromptTemplate

def evaluator(input):
    llm = ChatOpenAI(model="gpt-4-1106-preview")
    template = "You are a helpful assistant that classify whether the sentence mentions any animal. Only respond with 'Yes' or 'No'."
    chat_prompt = ChatPromptTemplate.from_messages([
        ("system", template),
        ("human", "{text}"),
    ])
    messages = chat_prompt.format_messages(text=input)
    response = llm.invoke(messages)
    return response.content
```

### [CRIACOMP] Gemini Simple Request

- **Arquivo:** `primeiros-passos/[CRIACOMP]_Gemini_Simple_Request.ipynb`
- **Objetivo:** Requisição simples ao Gemini Pro para gerar um prompt de imagem para Stable Diffusion.
- **Conceitos:** `google.generativeai`, configuração via Colab secrets, `GenerativeModel`.
- **Modelo usado:** `gemini-pro`
- **Entrada:** prompt textual ("Draw a kitty cat typing on a computer")
- **Saída:** prompt enriquecido para Stable Diffusion (80 tokens).

### [CRIACOMP] Gemini and Stable Diffusion

- **Arquivo:** `primeiros-passos/[CRIACOMP]_Gemini_and_Stable_Diffusion.ipynb`
- **Objetivo:** Combinação de Gemini (texto → prompt) + Stable Diffusion (prompt → imagem).
- **Foco:** pipeline multimodal: LLM como tradutor de intenção para linguagem de modelo de imagem.

### 2024_1_CRIACOMP_Experimentando_com_GRADIO

- **Arquivo:** `primeiros-passos/2024_1_CRIACOMP_Experimentando_com_GRADIO.ipynb`
- **Objetivo:** Introdução prática ao Gradio para criar interfaces web para modelos de IA.
- **Conceitos demonstrados:**
  - Função simples + `gradio.Interface`
  - Múltiplos parâmetros de entrada (`text` + `slider`)
  - Chat interface com `gr.ChatInterface` e `gr.Chatbot`
  - **OpenAI API** via Python: streaming com `client.chat.completions.create`
  - **LangChain + OpenAI**: `ChatOpenAI` com e sem streaming
  - **LangChain + Gemini**: `ChatGoogleGenerativeAI` com streaming
- **Destaque:** notebook cobre toda a evolução da integração — do chamar API direta ao encapsular em Gradio, com suporte a streaming em todos os casos.

### Primeiros_passos_com_a_API_da_OpenAI

- **Arquivo:** `primeiros-passos/Primeiros_passos_com_a_API_da_OpenAI.ipynb`
- **Objetivo:** Primeiro contato com a API da OpenAI.

### open_deep_researcher

- **Arquivo:** `primeiros-passos/open_deep_researcher.ipynb`
- **Objetivo:** Notebook de pesquisa profunda (deep research) — explorar capacidades de agentes de pesquisa.

---

## Notebooks: embeddings

A pasta [`embeddings/`](embeddings/) contém o material de uma aula sobre **word embeddings e busca semântica** da edição 2024.2, com dados e visualização UMAP.

### 2024_2_CRIACOMP_Embeddings_and_Visualization.ipynb

- **Arquivo:** `embeddings/2024_2_CRIACOMP_Embeddings_and_Visualization.ipynb`
- **Objetivo:** Ensinar embeddings de texto, busca semântica e visualização de vetores com UMAP + Observable.
- **Conceitos:** embeddings como vetores numéricos, similaridade de cosseno, busca semântica, soma de vetores de palavras, redução dimensional com UMAP, visualização interativa com Observable Plot.

**Blocos do notebook:**

1. **Leitura dos dados** — `words.csv` (42 palavras/frases: "red", "potatoes", "saudade", "luiz gonzaga", "zebra", etc.)
2. **Cálculo de embeddings** — três modelos disponíveis:
   - **OpenAI**: `text-embedding-3-small` via `openai.embeddings.create`
   - **Jina**: `jinaai/jina-embeddings-v2-base-en` via `transformers`
   - **Nomic Embed Text V2**: `nomic-ai/nomic-embed-text-v2-moe` via `sentence_transformers` (selecionado por padrão)
3. **Busca semântica** — ingessa uma palavra, calcula embedding, computa similaridade de cosseno com todas as palavras do corpus, ordena por similaridade.
4. **Soma de vetores** — exemplo: `milk_vector + espresso_vector`, busca palavras mais similares à combinação.
5. **Visualização UMAP** — `umap-learn` para redução a 3D, renderização com Observable Plot via HTML/JavaScript embutido, com parâmetros ajustáveis (`n_neighbors`, `min_dist`, `n_components`, `metric`, `spread`).

**Dados:**

- **`words.csv`**: 42 entradas (palavras e frases em inglês e português, com "saudade", "baião de dois", "buchada", "cuscuz", "luiz gonzaga").
- **`word_embeddings.csv`**: ~2.5 MB, vetores de embedding das 42 palavras (modelo Nomic por padrão), uma embedding por linha como string JSON de array de floats.

### Notebooks archived de embeddings

- **`ARCHIVED/2023_1_CRIACOMP_Word_Embeddings.ipynb`** — primeiras aulas de embeddings (2023.1).
- **`ARCHIVED/2024_1_CRIACOMP_Word_Embeddings.ipynb`** — edição 2024.1.
- **`ARCHIVED/2024_2_CRIACOMP_Word_Embeddings.ipynb`** — edição 2024.2.
- **`ARCHIVED/[2024.1] OpenAI Embeddings.ipynb`** — foco em OpenAI embeddings.
- **`ARCHIVED/Jina_Embedding.ipynb`** — experimento com Jina embeddings.
- **`ARCHIVED/OLD_REFERENCE_CRIACOMP_Word_Embeddings.ipynb`** — referência antiga.
- **`ARCHIVED/OLD_REFERENCE_UMAP+Observables+widget.ipynb`** — referência antiga de UMAP.

---

## Notebooks: ARCHIVED (edições anteriores)

Pasta [`ARCHIVED/`](ARCHIVED/) — notebooks e notícias de edições anteriores (2023.1 a 2025.1).

### Notícias arquivadas

| Arquivo | Edição |
|---------|--------|
| `2023_1_news.md` | 2023.1 |
| `2023_2_news.md` | 2023.2 |
| `2024_1_news.md` | 2024.1 |
| `2024_2_news.md` | 2024.2 |
| `2025_1_news.md` | 2025.1 |

### Notebooks arquivados

- **2023.1:** `2023_1_CRIACOMP_Word_Embeddings.ipynb`, `2023_1_CRIACOMP_UMAP+Observables+widget.ipynb`
- **2024.1:** `2024_1_CRIACOMP_Word_Embeddings.ipynb`, `[2024.1] OpenAI Embeddings.ipynb`
- **2024.2:** `2024_2_CRIACOMP_Word_Embeddings.ipynb`
- **Outros:** `Jina_Embedding.ipynb`, `Copy_of_insanely_fast_whisper_colab.ipynb`, `Pyannote_plays_and_Whisper_rhymes_v_1_0.ipynb`
- **Referências:** `OLD_REFERENCE_CRIACOMP_Word_Embeddings.ipynb`, `OLD_REFERENCE_UMAP+Observables+widget.ipynb`

### Outros arquivos arquivados

- `ideias_projetos.md` — ideias de projetos de edições passadas.
- `critical_views.md` — visões críticas compiladas.
- `aux_to_image_synth_timeline.md` — linha do tempo da síntese de imagem.

---

## Skills do Projeto Autoral (2026.2)

A pasta [`skills/`](skills/) contém quatro skills de ideação e crítica para os grupos usarem durante o Projeto Autoral. São **Markdown puro**, sem dependência de agente específico — por design, nenhuma entrega da disciplina pode exigir uma ferramenta em particular.

> Elas não fazem o projeto de vocês. Elas fazem vocês decidirem.

### Como instalar

**Claude Code:**

```bash
git clone https://github.com/filipecalegario/criacomp.git
mkdir -p .claude/skills
cp -R criacomp/skills/* .claude/skills/
```

Depois, `/afiar-o-eixo` (ou o nome de qualquer uma delas).

**OpenCode e outros agentes que leem `.agents/`:**

```bash
mkdir -p .agents/skills
cp -R criacomp/skills/* .agents/skills/
```

**Qualquer chat, sem instalar nada:**

Abra o `SKILL.md` da skill que você quer, copie o conteúdo inteiro e cole como primeira mensagem da conversa. Funciona igual — as skills foram escritas para sobreviver a esse caminho, que é o piso.

### Skill 1: abrir-o-leque

- **Arquivo:** `skills/abrir-o-leque/SKILL.md` (69 linhas, ~3.4KB)
- **Passo:** 1 (quando o grupo está em branco ou preso no óbvio)
- **Função:** Gera possibilidades que o grupo não teria alcançado sozinhos, em largura, sem convergir.
- **Mecanismo:** combina três colunas — **modalidade** (imagem, vídeo, texto, áudio, código) × **operação** (variar, seriar, traduzir, degradar, inverter, apagar, repetir com erro, documentar, atrasar, esconder) × **restrição** (uma cor só, dez segundos, sem humano, um prompt só, tudo offline, etc.).
- **Entrega:** 20 possibilidades numeradas — 8 plausíveis, 8 estranhas, 4 que parecem impossíveis.
- **Regra:** não ranqueia, não converge, não faz a coleção. Termina com: "Se alguma destas possibilidades pareceu a escolha óbvia, desconfie."

### Skill 2: afiar-o-eixo

- **Arquivo:** `skills/afiar-o-eixo/SKILL.md` (85 linhas, ~4.9KB)
- **Passo:** 3 (depois de escolher uma direção, antes de produzir)
- **Função:** Entrevista implacável, uma pergunta por vez, até o eixo da coleção ficar específico e defensável.
- **Protocolo:** uma pergunta por vez, cada pergunta vem com resposta recomendada, não aceita resposta vaga, não avança com pendência.
- **Três perguntas obrigatórias:**
  1. **Qual é a restrição?** (o que o grupo se proibiu de fazer; testes: proíbe algo concreto? alguém de fora notaria se quebrar? foi escolhida ou é limitação da ferramenta disfarçada?)
  2. **O que se repete e o que varia?** (constante em 4 palavras? variável em 4 palavras? embaralhando a ordem, muda algo?)
  3. **Por que este artefato pertence e aquele não?** (exemplo concreto de algo que não entraria; caso de fronteira)
- **Perguntas de segunda camada:** quantos artefatos e por que esse número? ordem da coleção? o que a coleção deixa de fora? se outro grupo copiasse o enunciado, o resultado seria parecido? onde entra a IA que não seria substituível por trabalho manual?
- **O que não faz:** não escreve o eixo, não elogia cedo, não sugere ferramentas, não gera artefatos.
- **Encerramento:** devolve em 3 linhas o que entendeu, aponta o ponto mais frágil, pede que o grupo escreva o eixo em um parágrafo.

### Skill 3: derrubar-a-ideia

- **Arquivo:** `skills/derrubar-a-ideia/SKILL.md` (80 linhas, ~3.8KB)
- **Quando usar:** quando o grupo acha que está pronto (e ainda não está).
- **Função:** Advogado do diabo — ataca o eixo já escrito procurando o ponto que o derruba, antes que a turma descubra na apresentação.
- **Sete ataques (todos, um por um, com defesa antes de seguir):**
  1. **Isso é um lote** — "Me convence de que isto é uma coleção e não doze arquivos na mesma pasta."
  2. **A restrição não restringe** — casos frequentes: limitação da ferramenta disfarçada, preferência estética, restrição não verificável de fora.
  3. **Se eu tirar quatro, alguém percebe?** — se a resposta é "fica menor", é lote.
  4. **Outros três grupos vão fazer isso** — escreva o enunciado em uma frase; o que é de vocês nela?
  5. **Cadê o descarte?** — exemplo de artefato gerado e não colocado, e o motivo.
  6. **A ferramenta escolheu por vocês** — o eixo nasceu de uma pergunta deles ou do que a ferramenta faz bem?
  7. **A pergunta da apresentação** — em 24/09 alguém vai levantar a mão e perguntar a coisa mais óbvia e chata possível. Qual é? E uma pior?
- **Veredito:** três blocos — onde não conseguiu derrubar, o que derruba, a menor mudança que salva (uma alteração, não cinco).
- **O que não faz:** não reescreve o eixo, não inventa problema, não sugere projeto diferente, não gera artefatos.

### Skill 4: escutar-a-reuniao

- **Arquivo:** `skills/escutar-a-reuniao/SKILL.md` (85 linhas, ~4.6KB)
- **Quando usar:** depois de qualquer discussão longa.
- **Função:** Transforma a gravação ou transcrição de uma reunião do grupo em material de trabalho. Roda na máquina do grupo, e nada do que sai daqui é entregue a ninguém.
- **Princípio:** a discussão do grupo é o material mais rico que o projeto produz, e é o único que evapora. Uma hora de conversa boa vira, no dia seguinte, três frases que alguém lembra.
- **Seis blocos produzidos:**
  1. **As ideias que apareceram** — todas, inclusive as de brincadeira e as que morreram em três segundos. Uma linha cada, nas palavras de quem falou.
  2. **As ideias que morreram sem discussão** — o bloco mais valioso: ideias ditas e sem resposta, descartadas com "não dá" sem explicação, enterradas por piada, aparecidas perto do fim.
  3. **As decisões tomadas por cansaço** — momentos em que o grupo fechou por exaustão, não por conclusão. Sinais: "então tá", "vamos com essa mesmo", "depois a gente vê", silêncio + mudança de assunto.
  4. **As tensões que ficaram abertas** — desacordos real que a conversa contornou, formulados como pergunta pendente, não como conflito entre pessoas.
  5. **As vozes que ficaram de fora** — se a conversa foi dominada por 1-2 pessoas, diga ao grupo de forma neutra. Sugere qual pergunta fazer na próxima reunião para quem falou menos.
  6. **Como isso vira eixo** — 2-3 direções que mais parecem ter eixo, como hipóteses a serem testadas, nunca como conclusão. Encerra mandando para `afiar-o-eixo`.
- **O que não faz:** não decide, não inventa, não resume a reunião, não avalia pessoas.
- **Como gravar e transcrever (não obrigatório, nada entregue):** qualquer gravador de celular; Whisper roda local e é gratuito; diarização ajuda no bloco 5 mas não é necessária; a transcrição fica com o grupo.

### Regra transversal das skills

Nenhuma destas skills escreve o projeto. Elas perguntam, provocam e criticam, e param antes da decisão. Se você pedir a qualquer uma delas "então escreve o eixo pra mim", a resposta correta dela é recusar e devolver a pergunta. O que a disciplina avalia é o critério de vocês, e critério terceirizado não sobrevive à primeira pergunta da apresentação.

### Melhorar as skills

Se alguma delas estiver ruim, mande pull request. Melhoria aceita conta como **Experimento** no Portfólio, desde que venha com a Peça correspondente: o que você quis mudar, o que testou, o que aconteceu, o que aprendeu.

---

## Coleção de Prompts (PROMPT_COLLECTION.md)

Arquivo: [`PROMPT_COLLECTION.md`](PROMPT_COLLECTION.md) — ~3.8 KB.

Contém prompts em formato de sistema para tarefas de processamento de conteúdo. O prompt principal é um **Comprehensive Content Summarizer** com instruções detalhadas para:

- Identificar tipo de conteúdo, criador, título e data
- Criar resumo em múltiplas camadas: tese central, pontos-chave, framework contextual, breakdown detalhado, perspectivas nuances, premissas subjacentes
- Atenção especial a conceitos abstractos, contexto histórico/cultural, abordagens metodológicas, qualificadores e limitações
- Indicar explicitamente gaps quando informação é clara ou ausente
- Incluir timestamps ou referências de página para conteúdo longo
- Priorizar precisão e abrangência sobre brevidade

O prompt é apresentado em bloco de código Markdown com seções `<System>`, `<Context>`, `<Instructions>`, `<Constraints>` e `<Output Format>` — um padrão reutilizável para resumo de YouTube, artigos, livros, podcasts e outros media.

---

## Qualidade e Observações

### Pontos fortes do repositório

- **Notícias curadas e contextualizadas** — não apenas links, mas comentários com pauta de discussão em aula (`2026-2-NOTES.md`), o que transforma o repositório em um obscured learning resource.
- **Skills pedagógicas bem escritas** — as quatro skills do Projeto Autoral são instruções para um agente (ou humano) que são ao mesmo tempo específicas e éticas: não tomam decisões por conta própria, não escrevem o trabalho, não avaliam pessoas. O README das skills explica como instalar em Claude Code, OpenCode e qualquer chat — cobertura ampla com zero dependência.
- **Notebooks com badge Colab** — todos os notebooks de `primeiros-passos/` e `embeddings/` têm badge de "Open in Colab", o que reduz a barreira de entrada para estudantes que não têm ambiente local configurado.
- **Progressão pedagógica clara** — a disciplina avança de "primeiros passos com API" → "experimentando interfaces (Gradio)" → "embeddings e visualização" → "habilidades de projeto" → "Projeto Autoral" → "Projeto Ferramenta", com um vocabulário consistente (Peça, Artefato, Eixo, Atividade, Experimento).
- **Ética integrada ao currículo** — não apenas um tópico separado, mas uma premissa de avaliação: não se avalia se foi feito por pessoa ou IA, não se exige ferramenta paga, os pesos são negociados com a turma no final.
- **Registro como método** — o Portfólio avalia o registro (especificidade + risco), não o artefato, o que incentiva experimentação franca e documentação honesta de falhas.

### Áreas onde há espaço para expansão

1. **Notebooks sem output salvo** — a maioria dos notebooks não tem output renderizado, o que é esperado para notebooks com API keys, mas dificulta a revisão de fluxo sem executar.
2. **word_embeddings.csv é grande** (~2.5 MB) — útil para a aula, mas pode ser desnecessário se o notebook sempre recalcular. Pode valer a pena adicionar um `.gitignore` se não for material de referência essencial.
3. **No README original não há seção de "como rodar os notebooks"** — está implícito pelo badge Colab, mas um breve guia de configuração local (API keys, variáveis de ambiente) ajudaria milhares de estudantes.
4. **ARCHIVED/ tem notebooks duplicados** — alguns notebooks de embeddings aparecem em múltiplas edições com pequenas variações; poderia ter um índice que explique a evolução.
5. **Sem `requirements.txt` ou ambiente declarado** — cada notebook instala dependências na primeira célula (`!pip install`), o que funciona no Colab mas não padroniza versões.

### Observações de design

- `CLAUDE.md` está presente no repositório — instruções contextuais para Claude Code, o que sugere que o repositório é used both as teaching material e as context for AI coding agents.
- O README original já é bem estruturado — 161 linhas com emojis como seções visuais, collapses para projetos anteriores, blocos de callout para regras não negociáveis. A wiki aqui complementa, não substitui.

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Remote:** `git@github.com:lucaslimacodes/criacomp.git` (origin)
- ** upstream:** `https://github.com/filipecalegario/criacomp`
- **Última atualização conhecida:** edição 2026.2 (setembro 2026)

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Novos notebooks:** documentar objetivo, conceitos, modelos usados e dependências.
- **Novas skills:** manter a documentação de instalação e uso atualizada.
- **Mudanças nas regras de avaliação:** registrar diferenças entre edições.
- **Notícias de outras semanas:** atualizar `2026-2-NEWS.md` e esta wiki com os novos temas.

---

## Links Úteis

- **Repositório original (lucaslimacodes):** https://github.com/lucaslimacodes/criacomp
- **Repositório upstream (filipecalegario):** https://github.com/filipecalegario/criacomp
- **Branch main:** https://github.com/lucaslimacodes/criacomp/tree/main
- **Colab (badge em cada notebook):** https://colab.research.google.com/github/filipecalegario/criacomp
- **Comunicações:** Google Classroom
- **Skills de ideação e crítica:** [`skills/`](skills/)
- **Tendências e notícias:** [`2026-2-NEWS.md`](2026-2-NEWS.md) (atualizado semanalmente)
- **Coleção de Prompts:** [`PROMPT_COLLECTION.md`](PROMPT_COLLECTION.md)
- **Edições anteriores:** [`ARCHIVED/`](ARCHIVED/)
- **Google Colab:** https://colab.research.google.com/
- **OpenAI API:** https://platform.openai.com/
- **Google AI Studio (Gemini):** https://makersuite.google.com/app/apikey
- **Gradio:** https://gradio.app/
- **Stable Diffusion:** https://stability.ai/
- **LangChain:** https://www.langchain.com/
- **UMAP:** https://github.com/lmcinnes/umap-learn
- **Observable Plot:** https://observablehq.com/plot/
- **Hugging Face:** https://huggingface.co/

---

> *Wiki detalhada gerada por Hermes Agent com análise estática do código-fonte, leitura do README, inventário de arquivos e inspeção dos notebooks, skills e arquivos de dados do repositório criacomp.*
> *Última atualização da wiki: 2026-09-15*
