# p5_project

> Wiki detalhada do repositório **p5_project** — Visualização interativa de algoritmos de busca em grade com p5.js (creative coding)

- **Repositório original:** https://github.com/lucaslimacodes/p5_project
- **Branch principal:** `main` (p5.js 1.4.1 via CDN, JavaScript vanilla, sem build step)
- ** Linguagem principal:** JavaScript (p5.js)
- ** Última atualização do repo:** 2025-06 (commit `769c21f`)
- ** Criado em:** 2025-06
- ** Estrelas:** 0 | ** Forks:** — | ** Issues abertas:** —
- ** Wiki deste repositório:** [repos/p5_project/](./p5_project/) (esta pasta)

---

## Visão Geral

Este projeto é uma **simulação visual interativa** de algoritmos de busca em grafos/grade, construída com **p5.js** — a biblioteca de creative coding para JavaScript. O objetivo é demonstrar, de forma animada e visual, como diferentes algoritmos de pathfinding exploram um ambiente gerado proceduralmente para encontrar um alvo (alimento).

O cenário é uma **grade 2D** onde cada tile tem um tipo de terreno com custo de movimento diferente:

| Terreno | Constante | Custo | Cor/Visual |
|---------|-----------|-------|------------|
| Areia (SAND) | `SAND = 1` | Baixo | Textura de areia |
| Lama (MUD) | `MUD = 3` | Médio | Textura de terra |
| Água (WATER) | `WATER = 5` | Alto | Textura de água |
| Obstáculo (OBSTACLE) | `Infinity` | Impossível | Pedra/rock |

Um **agent** (vaca `cow.png`) deve alcançar um **alimento** (trigo `trigo.png`), atravessando o terreno. A cada frame, o algoritmo de busca avança um passo na exploração, permitindo observar a expandão da **fronteira** (verde) e dos nós **explorados** (vermelho) em tempo real.

Quando o caminho é encontrado, o agente percorre a trajetória com animação, velocidade variando conforme o terreno (areia: 10 frames/tile, lama: 20, água: 30).

**Os 5 algoritmos implementados:**

| Algoritmo | Tipo | Estrutura de fronteira | Heurística |
|-----------|------|------------------------|------------|
| **BFS** (Breadth-First Search) | Não informado | Queue (array, FIFO) | — |
| **DFS** (Depth-First Search) | Não informado | Stack (array, LIFO) | — |
| **Dijkstra** | Custo uniforme | Priority Queue (min-heap) | — (custo real) |
| **Greedy Best-First** | Informado | Priority Queue (min-heap) | Distância de Manhattan ao alvo |
| **A\*** (A-Star) | Informado (otimo) | Priority Queue (min-heap) | Distância de Manhattan (g + h) |

---

## O que é p5.js

**p5.js** é uma biblioteca JavaScript criada para tornar a programação visual e criativa mais acessível. Inspirada no Processing (Java), ela roda no navegador e fornece:

- `setup()` / `draw()` — loop principal da aplicação, equivalente ao `main` de creative coding
- `createCanvas()` — criação de canvas HTML5
- `createVector()` — vetores 2D/3D com operações matemáticas
- `loadImage()`, `image()` — carregamento e renderização de imagens
- `random()`, `floor()`, `line()`, `square()`, `stroke()`, `fill()` — primitivas gráficas e utilitários
- Integração direta com o DOM via `select()` e manipulação de elementos HTML

Neste projeto, p5.js abstrai toda a renderização gráfica e o loop de animação, permitindo que a lógica dos algoritmos seja escrita em código puro e visualizada frame a frame.

---

## Estrutura do Projeto

```
p5_project/
├── index.html              # Página principal — carrega p5.js (CDN), define controles e scripts
├── sketch.js               # Entrada principal (setup/draw) — orquestra mundo + algoritmo + UI
├── world.js                # Classe World — grade, geração procedural de terrenos, renderização, animação do agente
├── style.css               # Estilo dos controles HTML e canvas
├── package.json            # Dependências de desenvolvimento: @types/p5 (TypeScript types para autocomplete)
├── jsconfig.json           # Configuração do compilador JS (target ES6, inclusão de types)
├── .gitignore              # Padronão (node_modules, etc.)
├── src/
│   ├── algorithms/
│   │   ├── algorithm.js    # Classe base Algorithm — interface comum, getNeighbors, getPath, cameFrom, reset
│   │   ├── bfs.js          # BFS — Queue (FIFO)
│   │   ├── dfs.js          # DFS — Stack (LIFO, via pop())
│   │   ├── dijkstra.js     # Dijkstra — PriorityQueue com custo acumulado
│   │   ├── astar.js        # A* — PriorityQueue com f = g + h (Manhattan)
│   │   └── greedy.js       # Greedy — PriorityQueue com h apenas (distância Manhattan)
│   ├── utils/
│   │   └── priorityqueue.js  # PriorityQueue — min-heap binário (enqueue/dequeue/_bubbleUp/_sinkDown)
│   └── assets/
│       ├── cow.png         # Sprite do agente (vaca)
│       ├── trigo.png       # Sprite do alimento (trigo)
│       ├── sandImage.jpg   # Textura de terreno sand
│       ├── waterImage.jpg  # Textura de terreno water
│       ├── stoneImage.png  # Textura de obstáculo
│       ├── dirtImage.webp  # Textura de terreno mud
└── .git/                   # Histórico do repositório (shallow clone)
```

**Inventário de arquivos:** 22 arquivos totais — 9 `.js` (lógica), 3 `.png` (sprites/texturas), 3 `.json` (config), 2 `.jpg` (texturas), 1 `.webp` (textura), 568KB.

---

## Como Usar

### Pré-requisitos

- Navegador moderno com suporte a ES6 e Canvas
- Extensão **Live Server** do VS Code (recomendado para rodar localmente sem problemas de CORS de imagens locais)

### Rodando localmente

1. Clone o repositório:
   ```bash
   git clone https://github.com/lucaslimacodes/p5_project.git
   cd p5_project
   ```

2. Instale os tipos do p5.js para autocomplete no VS Code (opcional):
   ```bash
   npm install
   ```

3. Abra `index.html` com **Live Server** (clique direito → "Open with Live Server") ou abra o arquivo diretamente no navegador (imagens locais podem ter restrição de CORS dependendo do navegador).

4. A interface é apresentada imediatamente — nenhum servidor backend é necessário.

### Controles da Interface

| Controle | Elemento HTML | Descrição |
|----------|---------------|-----------|
| **Seleção de algoritmo** | `<select id="combo">` | BFS, DFS, Dijkstra, Greedy, A* |
| **Tamanho X** | `<input type="number" id="x">` | Largura da grade em tiles (2–50, default: 30) |
| **Tamanho Y** | `<input type="number" id="y">` | Altura da grade em tiles (2–50, default: 15) |
| **Start** | `<button id="startbutton">` | Inicia a execução do algoritmo |
| **Stop** | `<button id="stopbutton">` | Pausa/para a animação (zera o caminho) |
| **Reset** | `<button id="resetbutton">` | Recria o mundo e o algoritmo do zero |

### Como funciona a execução

1. **setup()** (sketch.js:37–100):
   - Lê os valores de `x` e `y` dos inputs, calcula `WIDTH = x * GRID_SIZE` e `HEIGHT = y * GRID_SIZE` (GRID_SIZE = 40px)
   - Cria o canvas via `createCanvas(WIDTH, HEIGHT)`
   - Instancia um novo `World(WIDTH, HEIGHT, GRID_SIZE)` — grade aleatória com agente e alimento em posições válidas
   - Instancia o algoritmo selecionado no `<select>` (BFS, DFS, Dijkstra, Greedy, A*), passando `world.tiles`, `world.agentPosition` e `world.foodPosition`
   - Registra event listeners nos controles HTML

2. **draw()** (sketch.js:105–110):
   - Limpa o canvas com `background(255)` (branco)
   - Chama `world.draw()` — renderiza grid, tiles, agente, alimento, fronteira/explorados, caminho
   - Chama `runAlgorithm()` — avança o algoritmo um passo por frame

3. **runAlgorithm()** (sketch.js:12–35):
   - Se `start && !stopped`:
     - Se `currAlgorithm.status == INPROGRESS`: executa `currAlgorithm.runStep()`, atualiza `world.frontier` e `world.explored`
     - Se `currAlgorithm.status == FAILURE`: recria o mundo e reseta o algoritmo (novo cenário)
     - Se `status == SUCCESS`: desenha o caminho (`world.path`), anima o agente movendo-se tile a tile até chegar ao alimento, então gera novo alimento e reseta
   - Se `stopped`: zera `world.path`

### Geração procedural do mundo (world.js)

`World.getRandomTilesConfiguration()` gera uma matriz `nX × nY` onde cada tile recebe um tipo baseado em probabilidades acumuladas:

| Terreno | Probabilidade | Intervalo |
|---------|---------------|-----------|
| OBSTACLE | 15% | [0, 0.15) |
| SAND | 30% | [0.15, 0.45) |
| MUD | 30% | [0.45, 0.75) |
| WATER | 25% | [0.75, 1.0] |

O agente e o alimento são posicionados aleatoriamente, com rejeição se caírem em obstáculo ou se o alimento ficar na mesma posição do agente.

### Animação do agente (world.js:188–210)

Quando o caminho é encontrado, o agente percorre `world.path` tile a tile. A velocidade depende do terreno atual:

- SAND: 10 frames por tile (`SAND_TIME_FRAMES`)
- MUD: 20 frames por tile (`MUD_TIME_FRAMES`)
- WATER: 30 frames por tile (`WATER_TIME_FRAMES`)

Isso simula o custo de movimento de forma visual — o agente "demora mais" em terrenos difíceis.

---

## Interface e Controles (index.html)

```html
<!-- Controles -->
<select id="combo">
  <option value="BFS">BFS</option>
  <option value="DFS">DFS</option>
  <option value="Dijkstra">Dijkstra</option>
  <option value="Greedy">Greedy</option>
  <option value="A*">A*</option>
</select>

<input type="number" id="x" min="2" max="50" value="30" />
<b>por</b>
<input type="number" id="y" min="2" max="50" value="15" />

<button id="startbutton">Start</button>
<button id="stopbutton">Stop</button>
<button id="resetbutton">Reset</button>
```

Scripts carregados em ordem (todos em `src/`, exceto os que estão na raiz):

```
p5.min.js (CDN → 1.4.1)
sketch.js
world.js
algorithms/algorithm.js
utils/priorityqueue.js
algorithms/bfs.js
algorithms/dfs.js
algorithms/dijkstra.js
algorithms/greedy.js
algorithms/astar.js
```

> **Nota:** Novos arquivos de algoritmo ou utilidade devem ser incluídos como `<script>` no corpo do `index.html`.

---

## Algoritmos — Detalhes de Implementação

### Classe base: `Algorithm` (algorithm.js)

Todas as subclasses herdam de `Algorithm`, que define:

| Método/Propriedade | Descrição |
|---------------------|-----------|
| `grid` | Matriz de tiles (valores: SAND=1, MUD=3, WATER=5, OBSTACLE=Infinity) |
| `startPosition`, `endPosition` | `p5.Vector` com a posição do agente e do alimento |
| `cameFrom` | `Map<p5.Vector, p5.Vector>` — rastreamento do caminho (quem veio de quem) |
| `explored` | Array de `p5.Vector` — nós já explorados |
| `status` | `INPROGRESS (0)`, `SUCCESS (1)`, `FAILURE (-1)` |
| `runStep()` | Método a ser sobrescrito — executa um passo da busca |
| `getNeighbors(position)` | Retorna vizinhos válidos (não obstáculos, dentro dos limites) em 4 direções (N, S, L, O) |
| `getPath()` | Reconstrói o caminho a partir de `cameFrom`, do fim ao início |
| `wasPositionExplored(position)` | Verifica se posição já está em `explored` (busca linear) |
| `isPositionInFrontier(position)` | Método a ser sobrescrito — verifica se está na fronteira |
| `reset(...)` | Reseta estado para nova busca |
| `getFrontierArray()` | Retorna a fronteira como array para renderização (sobrescrito por cada algoritmo) |

### BFS (bfs.js)

- **Fronteira:** Array atuando como Queue (FIFO): `shift()` para dequeue, `push()` para enqueue
- **Strategy:** Expande em camadas — todos os nós a distância `d` são explorados antes de nós a distância `d+1`
- **Caminho:** Garante o caminho mais curto em número de arestas (não considera custo de terreno)
- **Complexidade:** O(V + E) — cada tile visitado uma vez

### DFS (dfs.js)

- **Fronteira:** Array atuando como Stack (LIFO): `pop()` para dequeue (pega o último adicionado)
- **Strategy:** Explora profundamente em uma direção antes de voltar — pode encontrar caminhos subótimos
- **Caminho:** Pode ser longo e não necessariamente o mais curto
- **Observação:** Em grades com obstáculos, DFS pode "perder" caminhos mais curtos queBFS encontraria

### Dijkstra (dijkstra.js)

- **Fronteira:** `PriorityQueue` (min-heap) ordenada pelo custo acumulado (`g`)
- **Strategy:** Expande sempre o nó com menor custo acumulado — garante caminho mínimo considerando custos de terreno
- **Custo:** `newPriority = state.priority + grid[neighbor.x][neighbor.y]`
- **Caminho:** Ótimo considerando os pesos de terreno (SAND=1, MUD=3, WATER=5)
- **Implementação do heap:** `PriorityQueue.enqueue` insere e faz `_bubbleUp`; `dequeue` remove o mínimo e faz `_sinkDown`; suporta atualização de prioridade de nó existente

### Greedy (greedy.js)

- **Fronteira:** `PriorityQueue` ordenada pela heurística `h` (distância de Manhattan ao alvo)
- **Heurística:** `h = (|x - food.x| + |y - food.y|) * 1`
- **Strategy:** Sempre expande o nó que está "mais próximo" do alimento na grid — rápido, mas não garante caminho ótimo (pode levar a "becos sem saída" queforce backtrack indireto via falha e reset)
- **Caminho:** Geralmente curto, mas não ótimo — pode ignorar terrenos de baixo custo que pareçam "mais distantes"

### A\* (astar.js)

- **Fronteira:** `PriorityQueue` ordenada por `f = g + h`
- **g:** custo acumulado real do início até o nó
- **h:** heurística de Manhattan ao alimento
- **Exclusão de nós:** nó já na fronteira com menor `f` é substituído se novo `f` for menor
- **Caminho:** Ótimo (se a heurística é admissível — Manhattan em grid 4-direções com custos positivos é admissível)
- **Heurística:** `heuristic(state) = (Math.abs(state.x - endPosition.x) + Math.abs(state.y - endPosition.y)) * 1`

---

## Classe World (world.js)

| Propriedade | Tipo | Descrição |
|-------------|------|-----------|
| `width`, `height` | Number | Dimensões do canvas em pixels |
| `gridSize` | Number | Tamanho de cada tile em pixels (40) |
| `nX`, `nY` | Number | Quantidade de tiles nos eixos X e Y |
| `agentPosition` | p5.Vector | Posição atual do agente |
| `foodPosition` | p5.Vector | Posição do alimento |
| `tiles` | Number[][] | Matriz nX×nY de tipos de terreno |
| `frontier` | p5.Vector[] | Tiles na fronteira do algoritmo (renderizados em verde) |
| `explored` | p5.Vector[] | Tiles já explorados (renderizados em vermelho) |
| `path` | p5.Vector[] | Caminho encontrado (renderizado como linha verde) |
| `frameCounter` | Number | Contador de frames para animação do agente |
| `pathIndex` | Number | Índice do tile atual na animação do caminho |
| `foodImage`, `agentImage`, `sandImage`, `waterImage`, `stoneImage`, `dirtImage` | p5.Image | Sprites/texturas carregados |

| Método | Descrição |
|--------|-----------|
| `getRandomTilesConfiguration()` | Gera matriz de tiles aleatórios baseada em probabilidades |
| `draw()` | Renderização completa: grid + tiles + agente/alimento + fronteira/explorados + caminho + animação |
| `drawGrid()` | Linhas verticais e horizontais da grade |
| `drawTiles()` | Desenha cada tile com sua textura/imagem correspondente |
| `drawFoodAndAgent()` | Renderiza as imagens do agente e do alimento |
| `drawFrontierAndExplored()` | Tiles da fronteira (verde) e explorados (vermelho) com opacidade 125 |
| `drawPath()` | Linha conectando os tiles do caminho |
| `runPathAnimation()` | Move o agente ao longo do caminho, respeitando delay por tipo de terreno |
| `resetWorldStateToNewFoodPosition()` | Gera novo alimento e reseta variáveis de animação quando o agente chega |
| `resetAlgorithmArrays()` | Zera `frontier` e `explored` |

---

## Estados do Algoritmo

```
INPROGRESS (0) → algoritmo explorando
SUCCESS (1)    → caminho encontrado, agente animando sobre o path
FAILURE (-1)   → fronteira esvaziou, sem caminho possível → mundo recriado automaticamente
```

A transição `INPROGRESS → SUCCESS` acontece quando o nó extraído da fronteira é igual ao `endPosition`. A transição `INPROGRESS → FAILURE` acontece quando a fronteira fica vazia (não há caminho possível, geralmente quando o alimento ficou isolado por obstáculos).

---

## Qualidade e Observações

### Pontos positivos

- **Múltiplos algoritmos** implementados com mesma interface (`Algorithm`), facilitando comparação visual direta
- **Visualização bidimensional da busca** — fronteira e explored coloridos permitem acompanhar a expansão do algoritmo em tempo real
- **Terrenos com custos diferentes** — diferencia BFS/DFS (ignoram custo) de Dijkstra/A\* (consideram)
- **Animação do agente** após encontrar o caminho, com velocidade variável por terreno
- **Recriação automática** de mundo e alimento ao encontrar caminho ou falhar — demonstração contínua sem intervenção
- **PriorQueue como min-heap binário** — implementação do zero, sem dependências externas, usada por Dijkstra, A\* e Greedy
- **Código modular** — cada algoritmo é um arquivo separado, herança clara via `class extends Algorithm`

### Possíveis melhorias (technical debt)

1. **Busca linear em `wasPositionExplored` e `isPositionInFrontier`** — O(n) em arrays. Para grades maiores, usar `Set` (com hash de vetor) ou `Map` para O(1)
2. **`findIndexInFrontier` no Dijkstra/A\*/Greedy** — busca linear no heap. Poderia usar um mapa de posição→índice para atualização O(1) do heap
3. **Sem teste automatizado** — não há suite de testes (unitários ou de renderização); a validação é visual/manual
4. **Imagens hardcoded** — caminhos relativos às assets (`./assets/...`) podem quebrar se o arquivo for aberto diretamente via `file://` (por CORS). Live Server ou servidor HTTP resolve
5. **Interface em inglês (títulos) com textos mistos** — "por" em português no HTML; UI inconsistente
6. **Sem campanha de navegação com teclado** — apenas controles de clique; seria possível adicionar setas para mover agente manualmente ou focar com tab
7. **Sem zoom/pan** — grade limitada a 50×50 tiles (2000×2000px max) — para grades maiores, seria útil scroll/zoom
8. **Greedy pode falhar e resetar** sem mostra a causa visualmente de forma destacada — apenas recria o mundo

### Pontos de atenção no código

- **`World.getRandomTilesConfiguration` usa probabilidades acumuladas com intervalos sobrepostos** — a lógica usa `if` encadeados (não `else if`), o que pode atribuir múltiplos valores se os intervalos não se decompõem corretamente; no código atual os intervalos são disjuntos, então funciona, mas a estrutura é frágil (se alterar uma probabilidade sem ajustar as outras, pode haver gaps ou sobreposições)
- **`sketch.js` lê os valores de `x` e `y` no `setup()` e novamente nos event listeners** — se o usuário mudar o tamanho durante execução, o canvas é recriado e o mundo regenerado; start deve ser reiniciado manualmente
- **`stopped` flag** — quando `stopped` é true, `world.path` é zerado, mas `currAlgorithm.status` não é resetado; se o usuário apertar Start novamente após Stop sem Reset, o algoritmo pode continuar do estado anterior (dependendo da lógica)
- **Sem `else if` em `sketch.js:14–31`** — as condições `if(currAlgorithm.status == INPROGRESS)`, `else if(currAlgorithm.status == FAILURE)`, `else` cobrem todos os estados, mas a falta de `else if` explícito no segundo pode gerar confusão (é funcional, mas menos claro)

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Commits:**
  - `769c21f` — `fix(sketch): fixes greedy call` (único commit, 2025-06)
- **Remoto:** `git@github.com:lucaslimacodes/p5_project.git`

> **Nota:** O clone é shallow ( 깊이에 limit), então o histórico completo pode não estar disponível localmente. Verificar no GitHub: https://github.com/lucaslimacodes/p5_project/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Novos algoritmos:** documentar novas subclasses de `Algorithm` com seu critério de prioridade/fronteira e heurística (se houver)
- **Novos tipos de terreno:** adicionar constante, custo, probabilidade e textura na tabela de terrenos e aqui
- **Novas assets:** registrar imagens adicionadas e seu uso (sprite, textura, ícone)
- **Mudanças na UI:** registrar novos controles HTML ou mudanças no `index.html`
- **Decisões de implementação:** registrar escolhas como estrutura de fronteira, heurística, modo de renderização da fronteira/explored
- **Testes:** se adicionados, documentar framework e comandos de execução

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/p5_project
- **Branch main:** https://github.com/lucaslimacodes/p5_project/tree/main
- **p5.js:**
  - Site oficial: https://p5js.org/
  - Reference: https://p5js.org/reference/
  - CDN usado: https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.1/p5.min.js
- **Live Server (VS Code):** https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer
- **TypeScript types para p5.js:** `@types/p5` (https://www.npmjs.com/package/@types/p5)
- **Pathfinding visualizer padrão (referência de UX):** https://qiao.github.io/PathFinding.js/visualizer/
- **Algoritmos de busca (teoria):**
  - BFS/DFS: https://en.wikipedia.org/wiki/Breadth-first_search e https://en.wikipedia.org/wiki/Depth-first_search
  - Dijkstra: https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm
  - A\*: https://en.wikipedia.org/wiki/A*_search_algorithm
  - Greedy Best-First: https://en.wikipedia.org/wiki/Best-first_search

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*

*Última atualização: 2026-09-16*
