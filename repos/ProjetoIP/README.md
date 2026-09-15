# ProjetoIP

> Wiki detalhada do repositório **ProjetoIP** — Projeto de Infraestrutura de Hardware / Game em C com raylib

- **Repositório original:** https://github.com/lucaslimacodes/ProjetoIP
- **Branch principal:** `main`
- **Linguagem principal:** C (padrão C99 / GNU99)
- **Framework gráfico:** raylib (v4.2.0)
- **Última atualização do repo:** commits únicos (histórico mínimo)
- **Criado em:** data não declarada no repositório
- **Estrelas:** — | **Forks:** — | **Issues abertas:** —
- **Wiki deste repositório:** [repos/ProjetoIP/](./ProjetoIP/) (esta pasta)

---

## Visão Geral

Projeto de Infraestrutura de Hardware (IH) em fase de desenvolvimento, implementado como um **jogo 2D de exploração de mapa** usando a biblioteca **raylib** em C. O repositório contém:

1. **Código-fonte principal** — `main.c`: jogo 2D com movimento de personagem (4 direções), sistema de colisão com paredes, câmera 2D (mode 2D com follow), e reprodução de áudio de fundo.
2. **Assets gráficos** — 14 PNGs: mapa principal (`mainMap.png`), fundo (`background.png`) e 12 sprites de animação do personagem (idle + walking para cada direção).
3. **Áudio** — `MainSongSample.mp3`: trilha sonora de loop (MPEG ADTS, 32 kbps, 44.1 kHz).
4. **Build** — `Makefile` portátil do raylib (Desktop: Linux/Windows/macOS/BSD/RPI/Web) + `Makefile.Android` para compilação APK.
5. **Configuração VS Code** — 4 arquivos `.vscode/*.json` (debug, tasks, IntelliSense, settings).
6. **Binário pré-compilado** — `main.exe` (Windows, 2MB).

O projeto parece ser um **protótipo/espaço de teste** de IH: o mapa `mainMap.png` (2500×2208px) carregado a 0.5x de zoom com fundo `background.png` (1781×1164px) a 3x sugere um ambiente de teste de renderização, colisão e câmera — típico de disciplinas de IH que usam raylib como ferramenta de prototipagem gráfica.

> **Observação:** o README original do repositório contém apenas "Projeto em desenvolvimento" (42 chars, 5 linhas). Esta wiki detalhada é uma expansão baseada em análise estática do código-fonte.

---

## Estrutura doProjeto

```
ProjetoIP/
├── main.c                      # Código-fonte principal (jogo 2D raylib) — 376 linhas
├── main.exe                    # Binário Windows pré-compilado (~2MB)
├── main.code-workspace         # Workspace VS Code
├── Makefile                    # Makefile portátil do raylib (Desktop + RPI + Web)
├── Makefile.Android            # Makefile específico para compilação Android/APK
├── README.md                   # README original (mínimo)
├── MainSongSample.mp3          # Áudio de fundo (MPEG ADTS 32kbps 44.1kHz)
├── background.png              # Textura de fundo (1781×1164 RGBA)
├── mainMap.png                 # Mapa principal (2500×2208 RGB)
├── resources/LICENSE           # Licença dos recursos
├── Assets/
│   └── PlayerMovements/        # 12 sprites de animação do personagem
│       ├── PlayerIdleDown.png    # Idle — para baixo (28×32 RGBA)
│       ├── PlayerIdleLeft.png   # Idle — para esquerda (28×32 RGBA)
│       ├── PlayerIdleRight.png  # Idle — para direita (28×33 RGBA)
│       ├── PlayerIdleUp.png     # Idle — para cima (27×32 RGBA)
│       ├── PlayerWalkingDown (1).png   # Walking frame 1 — baixo (28×31)
│       ├── PlayerWalkingDown (2).png   # Walking frame 2 — baixo (28×32)
│       ├── PlayerWalkingLeft (1).png   # Walking frame 1 — esquerda (28×31)
│       ├── PlayerWalkingLeft (2).png   # Walking frame 2 — esquerda (27×32)
│       ├── PlayerWalkingRight (1).png  # Walking frame 1 — direita (28×32)
│       ├── PlayerWalkingRight (2).png  # Walking frame 2 — direita (28×31)
│       ├── PlayerWalkingUp (1).png     # Walking frame 1 — cima (27×31)
│       └── PlayerWalkingUp (2).png     # Walking frame 2 — cima (28×31)
└── .vscode/
    ├── launch.json             # Configurações de debug (cppdbg: gdb/lldb)
    ├── tasks.json              # Tasks: build debug + release
    ├── c_cpp_properties.json  # IntelliSense (Win32/Mac/Linux)
    └── settings.json           # Settings (excluir .git, .o, .exe)
```

**Inventário completo (26 arquivos, ~14MB):**
- 14 arquivos `.png` (mapa + fundo + 12 sprites)
- 4 arquivos `.json` (.vscode)
- 1 arquivo `.mp3` (áudio)
- 1 arquivo `.md` (README)
- 1 arquivo `Makefile` (build Desktop)
- 1 arquivo `Makefile.Android` (build Android)
- 1 arquivo `.c` (código-fonte)
- 1 arquivo `.exe` (binário Windows)
- 1 arquivo `.code-workspace` (workspace)
- 1 arquivo `resources/LICENSE`

---

## O que é este repositório

### Natureza do projeto

Trata-se de um **projeto de Infraestrutura de Hardware (IH)** na forma de um **jogo 2D funcional** em C com raylib. O código implementa um loop principal de jogo com:

- **Movimento do personagem** — WASD/setas (KEY_UP, KEY_DOWN, KEY_LEFT, KEY_RIGHT), velocidade fixa de 3px por frame, estado `isRunning` ligado/desligado.
- **Sistema de colisão** — 53 paredes (`Wall` structs) com retângulos de colisão hardcoded em `generateCollisionsMainMap()`, verificados via `CheckCollisionRecs()` do raylib. Cada parede tem `typeCollision` (HEADING_UP/DOWN/LEFT/RIGHT) que flagga o eixo bloqueado no `Player`.
- **Câmera 2D** — `Camera2D` com target seguindo o personagem (`camera.target = player.posX/posY`), offset centralizado na tela.
- **Animação do personagem** — 4 direções × 4 frames (idle + 2 walking + idle novamente em cada direção). Animação avança a cada 4 frames quando `isRunning==true`, travar em idle quando parado.
- **Renderização em camadas** — fundo (`background.png`) desenhado a escala 3x e offset (-900, -1800), mapa principal (`mainMap.png`) a escala 0.5x e offset (450, 0), personagem por cima.
- **Áudio** — `MainSongSample.mp3` carregado como `Music` e reproduzido em loop via `UpdateMusicStream`/`PlayMusicStream`.

### Tecnologias envolvidas

| Camada | Tecnologia | Observação |
|--------|-----------|------------|
| **Linguagem** | C | Padrão C99 (Desktop) / GNU99 (RPI) |
| **Framework gráfico** | raylib | v4.2.0 (conforme Makefile) |
| **Abstraction gráfica** | OpenGL 3.3 (GRAPHICS_API_OPENGL_33) | Definido no c_cpp_properties.json |
| **Build Desktop** | gcc/clang + GNU Make | Makefile portátil do raylib |
| **Build Android** | Android NDK + Java | Makefile.Android (APK) |
| **Build Web** | Emscripten (emcc) | Suportado pelo Makefile (WASM) |
| **IDE** | VS Code + C/C++ extension | 4 arquivos .vscode |
| **Debug** | GDB (Linux/Windows) / LLDB (macOS) | launch.json |
| **Áudio** | raylib Audio (OpenAL backend) | MusicStream |
| **Formatos** | PNG (rgba/rgb), MP3 (MPEG ADTS) | Assets |

### Público-alvo / contexto

Provável contexto de disciplina de **Infraestrutura de Hardware** (IH) — cursos técnicos ou superiores de TI/hardware que usam:
- **raylib** como ferramenta de prototipagem rápida de interfaces gráficas 2D
- **C** como linguagem de programação principal (próxima ao hardware)
- **Makefile** como sistema de build (ensino de compilação, linkage, flags)

O projeto carrega o estado "em desenvolvimento" no README, o que é consistente com um trabalho em andamento ou protótipo de IH.

---

## Como usar

### Pré-requisitos

- **raylib v4.2.0** instalado (ou disponível em `RAYLIB_PATH`)
- **gcc** (Linux/macOS) ou **clang** (macOS/BSD) ou **MinGW** (Windows)
- **make** (Linux/macOS/BSD) ou **mingw32-make** (Windows)
- Para Android: Android SDK + NDK + JDK configurados
- Para Web: Emscripten SDK (emsdk)

### Build Desktop (Linux)

```bash
# Variáveis de ambiente (opcional — defaults no Makefile)
export RAYLIB_PATH=/caminho/para/raylib   # padrão: ../.. (recursivo)

# Release (padrão)
make PLATFORM=PLATFORM_DESKTOP \
     BUILD_MODE=RELEASE \
     PROJECT_NAME=main \
     OBJS=main.c

# Debug
make PLATFORM=PLATFORM_DESKTOP \
     BUILD_MODE=DEBUG \
     PROJECT_NAME=main \
     OBJS=main.c
```

Saída: binário `main` (Linux) ou `main.exe` (Windows).

### Build com VS Code (tasks pré-configurados)

O repositório traz `tasks.json` com duas tasks:

| Task | Build Mode | Comando |
|------|------------|---------|
| `build debug` | DEBUG | `make PLATFORM=PLATFORM_DESKTOP BUILD_MODE=DEBUG PROJECT_NAME=${fileBasenameNoExtension} OBJS=${fileBasenameNoExtension}.c` |
| `build release` | RELEASE | `make PLATFORM=PLATFORM_DESKTOP PROJECT_NAME=${fileBasenameNoExtension} OBJS=${fileBasenameNoExtension}.c` |

Configurar `RAYLIB_PATH` conforme plataforma:
- **Windows:** `RAYLIB_PATH=C:/raylib/raylib` (no tasks.json Windows)
- **macOS:** `RAYLIB_PATH=<path_to_raylib>/raylib` (no tasks.json osx)
- **Linux:** `RAYLIB_PATH` definido no Makefile como `$(realpath ..)` (padrão)

### Executar

```bash
./main        # Linux
main.exe      # Windows
```

**Requerimentos em runtime:**
- Os arquivos `mainMap.png`, `background.png`, `MainSongSample.mp3` e pasta `Assets/PlayerMovements/` devem estar no mesmo diretório do binário (caminhos relativos no código: `"Assets/PlayerMovements/..."`, `"mainMap.png"`, `"background.png"`, `"MainSongSample.mp3"`).

### Build Android (APK)

```bash
make -f Makefile.Android PLATFORM=PLATFORM_ANDROID
```

Requer: `JAVA_HOME`, `ANDROID_HOME`, `ANDROID_TOOLCHAIN`, `ANDROID_BUILD_TOOLS` configurados.

### Build Web (WASM/HTML5)

```bash
make PLATFORM=PLATFORM_WEB BUILD_MODE=RELEASE PROJECT_NAME=main OBJS=main.c
```

Requer: `EMSDK_PATH` configurado. Saída: `main.html`.

### Especificações de build por plataforma (Makefile)

| Plataforma | Compilador | Flags notáveis |
|------------|-----------|----------------|
| **PLATFORM_DESKTOP (Linux)** | gcc | `-Wall -std=c99 -D_DEFAULT_SOURCE -Wl,-rpath,...` |
| **PLATFORM_DESKTOP (Windows)** | gcc (MinGW) | `-Wl,--subsystem,windows` |
| **PLATFORM_DESKTOP (macOS)** | clang | `-framework OpenGL -framework OpenAL -framework Cocoa` |
| **PLATFORM_DESKTOP (BSD)** | clang | `-lX11 -lXrandr -lXinerama -lXi -lXxf86vm -lXcursor` |
| **PLATFORM_RPI** | arm-linux-gnueabihf-gcc | `-std=gnu99` |
| **PLATFORM_WEB** | emcc | `-Os -s USE_GLFW=3 -s TOTAL_MEMORY=16777216 --preload-file resources` |

**Flags de build mode:**
- **DEBUG:** `-g -O0` (debug symbols, sem otimização)
- **RELEASE:** `-s -O1` (strip, otimização nível 1)

### Configuração do VS Code

#### Debug (`launch.json`)
- **Debug:** build debug antes (`preLaunchTask: build debug`), gdb (`/usr/bin/gdb` no Linux)
- **Run:** build release antes (`preLaunchTask: build release`), gdb
- `cwd`: `${workspaceFolder}`
- `stopAtEntry`: false

#### IntelliSense (`c_cpp_properties.json`)
3 configurações por plataforma:
- **Win32:** `C:/raylib/raylib/src/**`, `gcc.exe`, C99
- **Mac:** `<path_to_raylib>/src/**`, `/usr/bin/clang`, C11
- **Linux:** `<path_to_raylib>/src/**`, `/usr/bin/clang`, C11

#### Tasks (`tasks.json`)
Como descrito na seção Build com VS Code acima.

#### Settings (`settings.json`)
Exclui da visualização: `.git`, `.svn`, `.hg`, `CVS`, `.DS_Store`, `*.o`, `*.exe`.

---

## Análise do Código-Fonte (`main.c`)

### Estruturas de dados

#### `Player`
```c
typedef struct {
    int posX;           // posição X do personagem
    int posY;           // posição Y do personagem
    int frameCounter;   // contador de frame para animação
    int walkMode;       // direção atual: HEADING_UP/DOWN/LEFT/RIGHT
    bool isRunning;     // true quando pressionada alguma tecla
    bool collidedUp;    // flag de colisão no eixo Y (negativo)
    bool collidedDown;  // flag de colisão no eixo Y (positivo)
    bool collidedLeft;  // flag de colisão no eixo X (negativo)
    bool collidedRight; // flag de colisão no eixo X (positivo)
} Player;
```

#### `Wall`
```c
typedef struct {
    Rectangle collision;   // retângulo de colisão (x, y, width, height)
    int typeCollision;     // direção associada: HEADING_UP/DOWN/LEFT/RIGHT
} Wall;
```

#### Constantes de direção
```c
#define HEADING_UP    1
#define HEADING_RIGHT 2
#define HEADING_DOWN  3
#define HEADING_LEFT  4
```

### Funções principais

| Função | Assinatura | Responsabilidade |
|--------|-----------|------------------|
| `generateCollisionsMainMap` | `void generateCollisionsMainMap(Wall *walls)` | Popula 53 paredes com retângulos e tipos de colisão hardcodeados |
| `verifyCollision` | `void verifyCollision(Wall *walls, int numberWalls, Rectangle playerCollision, Player *player)` | Verifica colisão de cada parede com `CheckCollisionRecs()` e seta flags `collided*` no player |
| `StartPlayerAnim` | `void StartPlayerAnim(int frames, int *frameCounter, int walkMode, bool isRunning, Texture2D walkingUp[], Texture2D walkingDown[], Texture2D walkingLeft[], Texture2D walkingRight[], Player player)` | Desenha o sprite do personagem com animação (idle ou walking) |
| `UnloadPlayerTextures` | `void UnloadPlayerTextures(Texture2D walkingUp[], Texture2D walkingDown[], Texture2D walkingLeft[], Texture2D walkingRight[])` | Libera as 16 texturas dos sprites (4 direções × 4 frames) |

### Contagem de paredes
`numberWallsMain = 53` (define na linha 225). As paredes são hardcoded em `generateCollisionsMainMap()` com coordenadas no formato `Rectangle {x, y, width, height}`.

Exemplos de paredes (primeiras entradas):
```
walls[0]: {520, 300, 5, 300}    — tipo LEFT
walls[1]: {485, 730, 5, 335}    — tipo LEFT
walls[2]: {520, 300, 538, 5}    — tipo UP
walls[3]: {1058, 40, 5, 265}    — tipo LEFT
...
walls[52]: {675, 735, 5, 60}    — tipo RIGHT
```

### Loop principal (`main()`)

1. **Inicialização:**
   - `InitWindow(screenWidth, screenHeight, "raylib test")`
   - `InitAudioDevice()`
   - Carrega 16 texturas de player (4 direções × 4 frames)
   - Carrega `mainMap.png` e `background.png`
   - Aloca 53 paredes via `malloc` e popula com `generateCollisionsMainMap()`
   - Carrega música `MainSongSample.mp3`
   - Configura `Camera2D` (offset centralizado, zoom=1, rotação=0)

2. **Game loop (`while (!WindowShouldClose())`):**
   - `UpdateMusicStream(m)` + `PlayMusicStream(m)` (áudio looped)
   - `camera.target = (Vector2){player.posX, player.posY}` (câmera segue jogador)
   - Reset das flags de colisão (`collided* = false`)
   - `verifyCollision(walls, 53, PlayerCollision, &player)` — PlayerCollision = `{posX+3, posY+32, 35, 17}`
   - Controle de movimento (KEY_LEFT/RIGHT/UP/DOWN) com velocidade 3px/frame, respeitando flags de colisão
   - `isRunning` desligado quando nenhuma tecla pressionada
   - **Renderização (BeginDrawing → BeginMode2D):**
     - `ClearBackground(RAYWHITE)`
     - `DrawTextureEx(background, {-900, -1800}, 0, 3, RAYWHITE)` — fundo 3x
     - `DrawTextureEx(mainMap, {450, 0}, 0, 0.5, RAYWHITE)` — mapa 0.5x
     - `StartPlayerAnim(...)` — personagem animado
   - `EndDrawing()` + `EndMode2D()`

3. **Limpeza (após loop):**
   - `UnloadPlayerTextures(...)`
   - `UnloadTexture(mainMap)`, `UnloadTexture(background)`
   - `UnloadMusicStream(m)`
   - `free(walls)`
   - `CloseAudioDevice()`, `CloseWindow()`

### Pontos de atenção no código

- **PlayerCollision offset:** `Rectangle playerCollision = (Rectangle){player.posX+3, player.posY+32, 35, 17}` — o offset +3 no X e +32 no Y sugere que a colisão não é feita no centro do sprite, mas ajustada para o "foot" ou base do personagem (32 parece ser altura do sprite, +32 coloca o retângulo na base).
- **Frame counter reset:** quando `frames > 24`, frames resetado para 0 — um contador global de frames de 24 Hz (60 FPS / 2.5?), usado para sync de animação.
- **Frame animation advance:** `*frameCounter` incrementa a cada 4 frames (`frames % 4 == 0`) quando `isRunning==true`, com 4 frames totais por direção (idle 0, walk 1, idle 2, walk 3). Isso significa a animação "idle" usa frames 0 e 2, e "walking" usa frames 1 e 3.
- **Música sem verificação de retorno:** `LoadMusicStream()` e `InitAudioDevice()` sem verificação de erro (null checks ausentes) — pode crashar se arquivos ausentes.
- **Memória:** `malloc` para walls sem verificação de malloc failure; `free(walls)` no final (correto).
- **Câmera fixa:** zoom=1 constante, sem scroll limits ou bound — o jogador pode sair do mapa visualmente.

---

## Configurações e Arquivos de Suporte

### Makefile (Desktop + RPI + Web)

Makefile portátil do raylib (copyright 2013-2019 Ramon Santamaria / raysan5). Principais variáveis:

| Variável | Default | Descrição |
|----------|---------|-----------|
| `PROJECT_NAME` | `game` | Nome do binário de saída |
| `RAYLIB_VERSION` | `4.2.0` | Versão do raylib |
| `RAYLIB_PATH` | `../..` | Caminho para raiz do raylib |
| `PLATFORM` | `PLATFORM_DESKTOP` | Plataforma alvo |
| `BUILD_MODE` | `RELEASE` | Modo de build |
| `RAYLIB_LIBTYPE` | `STATIC` | STATIC (.a) ou SHARED (.so/.dll) |
| `USE_EXTERNAL_GLFW` | `FALSE` | Usar GLFW externo vs rglfw interno |
| `USE_WAYLAND_DISPLAY` | `FALSE` | Wayland vs X11 no Linux |

### Makefile.Android (Android/APK)

Makefile específico para build Android. Principais variáveis:

| Variável | Default | Descrição |
|----------|---------|-----------|
| `ANDROID_ARCH` | `ARM` | `ARM` (armeabi-v7a) ou `ARM64` (arm64-v8a) |
| `ANDROID_API_VERSION` | `21` | API level mínima |
| `JAVA_HOME` | `C:/JavaJDK` | JDK path |
| `ANDROID_HOME` | `C:/android-sdk` | Android SDK |
| `ANDROID_TOOLCHAIN` | `C:/android_toolchain_ARM_API21` | NDK toolchain |
| `ANDROID_BUILD_TOOLS` | `$(ANDROID_HOME)/build-tools/28.0.1` | Build tools |
| `PROJECT_NAME` | `raylib_game` | Nome do projeto |
| `PROJECT_LIBRARY_NAME` | `main` | Nome da library |
| `PROJECT_RESOURCES_PATH` | `resources` | Assets do Android |

### .vscode/launch.json

Configurações de debug para `cppdbg` (Microsoft C/C++ extension):
- 2 configurações: **Debug** (build debug, gdb) e **Run** (build release, gdb)
- `preLaunchTask` vinculado às tasks de build
- GDB path: `/usr/bin/gdb` (Linux), `C:/raylib/w64devkit/bin/gdb.exe` (Windows), LLDB (macOS)

### .vscode/c_cpp_properties.json

3 configurações de IntelliSense (Win32 / Mac / Linux), todas com:
- `GRAPHICS_API_OPENGL_33` e `PLATFORM_DESKTOP` no `defines`
- C99 (Win32) ou C11 (Mac/Linux)

---

## Build e Compilação (resumo)

### Rota Desktop (Linux — recomendada)

```bash
# 1. Garantir raylib instalado (se usar versão system-wide)
sudo apt install libraylib-dev    # Debian/Ubuntu (se disponível)
# ou construir a partir da fonte: https://github.com/raysan5/raylib

# 2. Build release
cd /tmp/repos-inspecao/ProjetoIP
make PLATFORM=PLATFORM_DESKTOP BUILD_MODE=RELEASE PROJECT_NAME=main OBJS=main.c

# 3. Executar
./main
```

### Rota com VS Code

1. Abrir pasta no VS Code (`main.code-workspace` presente)
2. Selecionar task `build debug` ou `build release` (Ctrl+Shift+B)
3. Debug com F5 (configuração "Debug" ou "Run" no launch.json)
4. Garantir que `RAYLIB_PATH` aponta para raylib instalado

### Rota Windows

```bash
# Requer: MinGW-w64 + raylib em C:/raylib/raylib
mingw32-make.exe PLATFORM=PLATFORM_DESKTOP BUILD_MODE=RELEASE \
    RAYLIB_PATH=C:/raylib/raylib PROJECT_NAME=main OBJS=main.c
main.exe
```

### Rota Android

```bash
make -f Makefile.Android PLATFORM=PLATFORM_ANDROID
# Requer: JAVA_HOME, ANDROID_HOME, ANDROID_TOOLCHAIN configurados
```

### Rota Web

```bash
make PLATFORM=PLATFORM_WEB BUILD_MODE=RELEASE PROJECT_NAME=main OBJS=main.c
# Requer: emsdk ativo (source <emsdk>/emsdk_env.sh)
# Saída: main.html (abra no navegador)
```

---

## Observações Técnicas e Qualidade

### Pontos fortes
- Código funcional com movimento, colisão, câmera e áudio operando
- Build multiplataforma (Desktop Linux/Windows/macOS/BSD/RPI/Web/Android)
- Configuração VS Code completa para debug e IntelliSense
- Animação do personagem com 4 direções e 4 frames por direção
- Sistema de colisão com 53 paredes hardcoded (protótipo consolidado)

### Pontos de melhoria / dívida técnica
1. **Hardcoded collision data** — 53 paredes hardcoded no `generateCollisionsMainMap()`; ao mudar o mapa, é necessário editar o C manually. Sugestão: carregar de arquivo externo (JSON/CSV).
2. **Sem verificação de erros** — `LoadTexture()`, `LoadMusicStream()`, `InitAudioDevice()`, `malloc()` sem null checks.
3. **Sem bounds checking** — o jogador pode sair da área do mapa; não há limites de tela ou world bounds.
4. **Câmera simples** — zoom fixo em 1, sem resize handling ou limite de viewport.
5. **Sensor de colisão hardcoded** — `PlayerCollision = (Rectangle){player.posX+3, player.posY+32, 35, 17}` com offsets mágicos (3 e 32).
6. **Frames de animação misturados** — cada direção tem [idle0, walk1, idle2, walk3]; o idle é representado por 2 frames diferentes (0 e 2), o que pode ser consolidado.
7. **README mínimo** — apenas "Projeto em desenvolvimento"; falta documentação de como usar, requisitos, controles.
8. **Binário pré-compilado** — `main.exe` presente no repo (Windows); em projetos de IH geralmente se evita commitar binários.

### Sugestões de evolução (se este for projeto de IH em andamento)
- Mover wall definitions para JSON/CSV e carregar em runtime
- Adicionar camera bounds + viewport limits
- Adicionar particle effects / HUD (FPS counter, coordenadas do player)
- Separar jogo em módulos (player.c, collision.c, render.c, audio.c)
- Adicionar Makefile para integração com CI (GitHub Actions)
- Documentar controles, requisitos e instruções de build no README

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Remote:** `origin` → `git@github.com:lucaslimacodes/ProjetoIP.git` (SSH)
- **Commits:** 1 commit (`caf6142 commit`) — repositório com histórico mínimo, provável primeiro envio
- **Atividade recente:** única revisão conhecida (commit único)

> **Nota:** detalhes exatos de commits podem ser verificados em https://github.com/lucaslimacodes/ProjetoIP/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Diagramas:** se o projeto evoluir para incluir diagramas de arquitetura ou fluxo, adicionar no repositório e referenciar aqui
- **Novas funcionalidades:** documentar novas entidades, controles, telas
- **Mudanças de build:** registrar novas flags, plataformas suportadas, dependências
- **Decisões de modelagem:** justificar escolhas de estrutura de dados (ex: 왜 usar structs vs objetos, coordinate system)
- **Histórico:** manter log das mudanças nesta página ou em arquivo de log dedicado

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/ProjetoIP
- **Branch main:** https://github.com/lucaslimacodes/ProjetoIP/tree/main
- **raylib (framework):** https://www.raylib.com/ | https://github.com/raysan5/raylib
- **raylib wiki (Makefile portátil):** https://github.com/raysan5/raylib/wiki
- **raylib examples:** https://github.com/raysan5/raylib/tree/master/examples
- **Makefile do raylib (fonte):** https://github.com/raysan5/raylib/blob/master/makefile
- **Emscripten (Web build):** https://emscripten.org/
- **Android NDK:** https://developer.android.com/ndk
- **C99 standard:** https://en.wikipedia.org/wiki/C99
- **VS Code C/C++ extension:** https://code.visualstudio.com/docs/languages/cpp

---

*\*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte do repositório ProjetoIP.*
*\*Última atualização: 2026-09-15 22:50 UTC*
