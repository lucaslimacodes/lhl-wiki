# Maraca-PSEL (Armorial-PSEL)

> Wiki detalhada do repositório **Maraca-PSEL** — base implementation for Maracatronics Robotics Programming Selective Proccess (PSEL)

- **Repositório original:** https://github.com/lucaslimacodes/Maraca-PSEL
- **Projeto:** Maracatronics — Robótica (UFPE)
- **Linguagem principal:** C++ (C++17)
- **Framework de build:** CMake + QMake (Qt 5)
- **OS suportado:** Ubuntu 20.04 e 22.04
- **Última atualização do repo:** 2026-09-15
- **Wiki deste repositório:** [repos/Maraca-PSEL/](./Maraca-PSEL/) (esta pasta)

---

## Visão Geral

O **Armorial-PSEL** (Maraca-PSEL) é a implementação base para o **Maracatronics Robotics Programming Selective Proccess** — um framework C++ que fornece uma interface simples para receber dados do ambiente simulado de futebol de robôs (FIRASim / RoboCup SSL-símile) e enviar pacotes de controle aos robôs.

O sistema comunica-se com o simulador via **UDP Multicast** (recepção de detecção) e **UDP Unicast** (envio de comandos), processando pacotes protobuf (`fira_message`) e oferecendo uma camada de abstração orientada a entidades que gerencia robôs, bola, campo e lógica de controle.

**Objetivo central:** permitir que um time de robôs seja controlado programaticamente a partir de uma classe `Coach`, que recebe dados do `Vision` (detecção de campo, bola e robôs), atualiza o `WorldMap` e aciona os `Player`s através do `Actuator`.

---

## Estrutura do Projeto

```text
Maraca-PSEL/
├── Armorial-PSEL.pro              # Projeto QMake (Qt 5, C++17)
├── main.cpp                       # Ponto de entrada: configuração e conexão de módulos
├── README.md                      # README original do repositório
├── LICENSE                        # GNU GPL v3+
├── .gitmodules                    # Submódulo: Armorial-Proto (definições protobuf)
├── .github/
│   └── workflows/
│       └── build.yml              # CI: builds em ubuntu-20.04 e ubuntu-22.04
├── assets/
│   ├── armorial-psel-workflow-diagram.png
│   └── armorial-psel-workflow-diagram-bg.png
├── include/proto/                 # (submódulo) .proto + .pb.cc/.pb.h gerados
│   ├── command.pb.cc/h
│   ├── common.pb.cc/h
│   ├── packet.pb.cc/h
│   └── replacement.pb.cc/h
└── src/
    ├── entities/
    │   ├── actuator/              # Actuator — envio de comandos aos robôs (UDP)
    │   ├── coach/                 # Coach — lógica de controle central (runCoach)
    │   ├── player/                # Player — controle individual de um robô (PID)
    │   ├── vision/                # Vision — recepção de pacotes de detecção (UDP multicast)
    │   └── worldmap/              # WorldMap — informações do campo e posição da bola
    └── utils/
        ├── types/
        │   ├── robotcontrolpacket/   # RobotControlPacket — comando de movimento
        │   └── robotdetectionpacket/ # RobotDetectionPacket — detecção de robô
        └── Utils.cpp/h               # Methods estáticos genéricos (math/geom)
```

### Arquivo de build: `Armorial-PSEL.pro`

| Configuração | Valor |
|---|---|
| Template | `app` |
| Destino | `../bin/Armorial-PSEL` |
| Versão | `1.0.0` |
| C++ Standard | C++17 (`CONFIG += c++17`) |
| Qt módulos | `core network` |
| Lições externas | `-lQt5Core -lprotobuf -lfmt` |
| Geração proto | `protoc` no início do build (linha `system()` no .pro) |

### Fontes compiladas (SOURCES)

```
include/proto/command.pb.cc
include/proto/common.pb.cc
include/proto/packet.pb.cc
include/proto/replacement.pb.cc
main.cpp
src/entities/actuator/actuator.cpp
src/entities/coach/coach.cpp
src/entities/player/player.cpp
src/entities/vision/vision.cpp
src/entities/worldmap/worldmap.cpp
src/utils/types/robotcontrolpacket/robotcontrolpacket.cpp
src/utils/types/robotdetectionpacket/robotdetectionpacket.cpp
src/utils/utils.cpp
```

---

## Diagrama de Fluxo de Dados

```
┌─────────────────────────────────────────────────────────────┐
│                      SIMULADOR (FIRA)                        │
│  ┌─────────────┐          ┌─────────────────────────────────┐│
│  │ Vision Server│─────────▶│  UDP Multicast 224.0.0.1:10002  ││
│  └─────────────┘          └─────────────────────────────────┘│
│                            ┌───────────────────────────────┐ │
│                            │ Actuator (UDP Unicast)        │ │
│                            │ ───────────────────────────── │ │
│                            │ Envia comandos ▶ 20011         │ │
│                            └───────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘

┌─────────────────── ARMORIAL-PSEL ───────────────────────────┐
│                                                              │
│  ┌──────────┐   rx packets     ┌───────────┐                │
│  │  Vision  │◀── UDP Multicast │  Simulator │                │
│  │          │                   └───────────┘                │
│  │ Signals: │                                                  │
│  │  ├─ sendRobotDetection ────────────────┐                  │
│  │  ├─ sendBallDetection ─────────────────┼──▶ WorldMap      │
│  │  └─ sendFieldDetection ────────────────┘                  │
│  │                       │                                     │
│  │                       ▼                                     │
│  │  ┌──────────────────────────────────────────────────┐      │
│  │  │              Coach (runCoach loop, 60 Hz)         │      │
│  │  │  getPlayer(BLUE/YELLOW, 0-2) → std::optional<Player*>│  │
│  │  │  getWorldMap() → ballPosition(), RoIs...          │      │
│  │  └──────┬──────────────────────┬──────────────────────┘      │
│  │         │                      │                             │
│  │         ▼                      ▼                             │
│  │  ┌─────────────┐      ┌─────────────────┐                   │
│  │  │   Player    │      │    Actuator     │                   │
│  │  │ (1 robô)    │◀────▶│ (envio UDP)     │                   │
│  │  │  PID controle│      │ QUdpSocket      │                   │
│  │  │  goTo()      │      │ → fira_message  │                   │
│  │  │  rotateTo()  │      │   Packet        │                   │
│  │  └─────────────┘      └─────────────────┘                   │
│  └──────────────────────────────────────────────────────────────┘
```

---

## Modelagem / Arquitetura

### Padrão de comunicação

O sistema usa **QObjects com signals/slots do Qt** como mecanismo de comunicação entre módulos. Não há herança profunda entre as entidades — todas derivam de `QObject` (exceto `RobotControlPacket` e `RobotDetectionPacket` que são POJOs puros).

**Ciclo de execução (60 Hz):**

1. `Vision` recebe pacotes UDP multicast a cada frame do simulador
2. `Vision` emite signals: `sendRobotDetection`, `sendBallDetection`, `sendFieldDetection`
3. `WorldMap` recebe `sendBallDetection` e `sendFieldDetection` → atualiza estado interno
4. `Player`s recebem `sendRobotDetection` via `updateFromDetection()` → atualizam posição/orientação
5. `Coach::runCoach()` é chamado a cada 16 ms por `QTimer` (60 Hz)
6. `Coach` decide controle, chama `Player::goTo()` / `Player::rotateTo()`
7. `Player` emite `sendControlPacket(RobotControlPacket)` → conectado ao `Actuator`
8. `Actuator` agrega pacotes e envia a cada 16 ms via UDP para o simulador

### Diagrama de classes (simplificado)

```
QObject
  ├─ Vision          (recebe detecção do ambiente)
  ├─ WorldMap        (dados do campo + bola)
  ├─ Actuator        (envia comandos ao simulador)
  ├─ Coach           (Manager: itera a cada 16ms, decide estratégia)
  └─ Player          (controla 1 robô individual; friend de Coach)

POJO (não QObject)
  ├─ RobotControlPacket  (isTeamBlue, playerId, wheelLeft, wheelRight)
  └─ RobotDetectionPacket (isTeamBlue, fira_message::Robot)
```

### Conexões de signals/slots (main.cpp)

| Signal (emissor) | Slot (receptor) | Função |
|---|---|---|
| `Vision::sendHostAddress` | `Actuator::connectToNetwork` | IP do simulador para Actuator |
| `Vision::sendFieldDetection` | `WorldMap::updateFieldDetection` | Atualiza dimensões do campo |
| `Vision::sendBallDetection` | `WorldMap::updateBallDetection` | Atualiza posição da bola |
| `Vision::sendRobotDetection` | `Player::updateFromDetection` | Atualiza posição do robô |
| `Player::sendControlPacket` | `Actuator::receiveControlPacket` | Envia comando de movimento |
| `QTimer::timeout` (Actuator) | `Actuator::sendControlPacketsToNetwork` | Loop de envio a cada 16ms |
| `QTimer::timeout` (Coach) | `Coach::runCoach` | Loop de decisão a cada 16ms |

---

## Entidades

### 1. Vision (`src/entities/vision/`)

> Interface de recepção de pacotes de detecção do ambiente simulado via UDP multicast.

**Responsabilidade:** Escutar o simulador FIRA na endereço multicast `224.0.0.1:10002`, receber datagramas protobuf (`fira_message::sim_to_ref::Environment`), parsear `Frame` (robôs + bola) e `Field` (dimensões), e emitir signals para os módulos interessados.

**Key features:**
- Descobre interfaces de rede via `QNetworkInterface::allInterfaces()` e tenta join multicast em cada interface até sucesso (`connectToNetwork()` rodando a cada 200ms)
- Ao receber o primeiro pacote, descobre o broadcast address da interface e emite `sendHostAddress` para o Actuator conectar
- Parser robusto: `ParseFromArray` com fallback (`continue` em caso de falha)
- Distingue robôs BLUE (`robots_blue`) e YELLOW (`robots_yellow`) nos frames

**Signals expostos:**
- `sendRobotDetection(const RobotDetectionPacket&)` — um por robô detectado
- `sendBallDetection(const fira_message::Ball&)` — bola do frame
- `sendFieldDetection(const fira_message::Field&)` — campo do frame
- `sendHostAddress(const QHostAddress&)` — broadcast address para actuator

**Constante de configuração:**
- `visionAddress` default: `"224.0.0.1"`
- `visionPort` default: `10002`

---

### 2. WorldMap (`src/entities/worldmap/`)

> Interface para informações do campo: dimensões, gols, penalidades, marcações e posição da bola.

**Responsabilidade:** Agreguar os dados do `Field` protobuf e `Ball`, fornecendo métodos calculados de posição (RoIs) baseados na orientação (left side ou right side).

**Construtor:** `WorldMap(bool isPlayingLeft)` — define se o time joga do lado esquerdo do campo.

**Métodos de dimensão do campo (delegam ao `_field` protobuf):**
| Método | Retorno | Origem |
|---|---|---|
| `length()` | `float` | `_field.length()` |
| `width()` | `float` | `_field.width()` |
| `goalDepth()` | `float` | `_field.goal_depth()` |
| `goalWidth()` | `float` | `_field.goal_width()` |
| `penaltyDepth()` | `float` | hardcoded: `0.15f` |
| `penaltyWidth()` | `float` | hardcoded: `0.7f` |
| `penaltyMarkDistanceFromGoal()` | `float` | hardcoded: `0.1125f` |
| `centerRadius()` | `float` | hardcoded: `0.25f` |

**Métodos de posição (RoIs) — calculados:**
| Método | Descrição |
|---|---|
| `minX() / maxX()` | Fronteiras X do campo: `±length()/2` |
| `minY() / maxY()` | Fronteiras Y: `±width()/2` |
| `ourGoalCenter()` | Centro do gol do próprio time (reflete X baseado em `isPlayingLeft`) |
| `ourGoalLeftPost()` | Pólo esquerdo do gol prprio |
| `ourGoalRightPost()` | Pólo direito do gol próprio |
| `ourPenaltyMark()` | Marca de penalidade do próprio time |
| `theirGoalCenter()` | Centro do gol adversário (X invertido) |
| `theirGoalLeftPost()` | Pólo esquerdo do gol adversário |
| `theirGoalRightPost()` | Pólo direito do gol adversário |
| `theirPenaltyMark()` | Marca de penalidade do adversário |
| `ballPosition()` | `QVector2D(_ball.x(), _ball.y())` — posição atual da bola |

**Slots de atualização:**
- `updateBallDetection(const fira_message::Ball&)` — armazena `_ball`
- `updateFieldDetection(const fira_message::Field&)` — armazena `_field`

---

### 3. Player (`src/entities/player/`)

> Interface de um robô individual no campo: posição, orientação, status de detecção e controle via PID.

**Responsabilidade:** Manter o estado local de um robô (posição, orientação, team, id), detectar quando o robô sumiu do ambiente, e gerar comandos de movimento (wheels speeds) baseados em estratégias de **goTo** (ir para posição) e **rotateTo** (rotacionar para posição/ângulo).

**Constantes do controlador (hardcoded no header):**
| Constante | Valor | Significado |
|---|---|---|
| `OUT_OF_FIELD` | `(MAX_FLOAT, MAX_FLOAT)` | Posição sentinel quando robô não detectado |
| `PACKETS_TILL_MISSING` | `60` | Número de pacotes sem detecção antes de marcar como missing (~1 segundo a 60Hz) |
| `KP` | `25.0f` | Ganho proporcional do PID |
| `KI` | `0.0f` | Ganho integral (zerado — sem accululação) |
| `KD` | `2.5f` | Ganho derivativo do PID |
| `BASE_SPEED` | `30.0f` | Velocidade base dos motores (rad/s) |

**Métodos públicos:**
| Método | Descrição |
|---|---|
| `isMissing()` | `true` se `_position == OUT_OF_FIELD` |
| `getPosition()` | `QVector2D` posição atual |
| `getOrientation()` | `float` orientação em radianos (range [-π, π)) |
| `isTeamBlue()` | `bool` — time da equipe |
| `getPlayerId()` | `quint8` — id do robô (0-2) |

**Métodos protegidos (acessados pelo Coach via `friend class Coach`):**
| Método | Descrição |
|---|---|
| `goTo(targetPosition)` | Move o robô para uma posição alvo, calculando ângulo, PID e velocidades dos motores |
| `rotateTo(targetPosition)` | Chama `rotateTo(ângulo)` calculado via `Utils::getAngle()` |
| `rotateTo(orientation)` | Rotaciona o robô para um ângulo alvo (PID puro, motores em rotação simétrica) |

**Algoritmo de `goTo` (detalhado):**

1. Normaliza orientação do robô: `Utils::normalizeAngle(getOrientation())`
2. Calcula ângulo até o alvo: `Utils::getAngle(position, target)`
3. Verifica se é melhor andar para trás: `Utils::checkIfCanBeReversed(robotAngle, angleToTarget)` — true se `|angDiff| > π/2 + π/20`
4. Se reverso: roda o ângulo do robô + π
5. Calcula erro angular: `Utils::smallestAngleDiff(normalizedRobotAngle, angleToTarget)`
6. PID: `motorSpeed = KP*angError + KI*cumulativeError + KD*(angError - lastError)`
7. Clamps `motorSpeed` em `[-BASE_SPEED, BASE_SPEED]`
8. Define velocidades das rodas baseado em direção (normal/reverso):
   - **Normal:** robô vira usando diferença de velocidade entre rodas (uma roda em BASE_SPEED, outra em `BASE_SPEED ± motorSpeed`)
   - **Reverso:** ambas rodas negativas, com diferença para virar

**Algoritmo de `rotateTo(orientation)`:**
1. Similar ao `goTo` mas sem translação — os motores giram em sentidos opostos: `left = motorSpeed, right = -motorSpeed`
2. Se reverso: inverte os sinais

**Detecção de missing (`updateFromDetection`):**
- Ignora pacotes de time errado (`robotDetectionPacket.isTeamBlue() != this->isTeamBlue()`)
- Se `robot_id` não bate com `getPlayerId()`: incrementa `_missingPackets`; após `PACKETS_TILL_MISSING` (60), marca como `OUT_OF_FIELD`
- Se `robot_id` bate: reseta `_missingPackets`, atualiza `_position` e `_orientation`

**Signal de saída:** `sendControlPacket(RobotControlPacket(isTeamBlue, playerId, leftSpeed, rightSpeed))`

---

### 4. Actuator (`src/entities/actuator/`)

> Interface de envio de pacotes de controle aos robôs no ambiente simulado via UDP.

**Responsabilidade:** Agregar `RobotControlPacket`s vindos dos `Player`s e enviá-los em intervalos regulares (16ms → ~60Hz) como um único `fira_message::sim_to_ref::Packet` protobuf via UDP unicast para o simulador.

**Construtor:** `Actuator(quint16 simPort = 20011)` — porta padrão do servidor de referência FIRA.

**Membros internos:**
- `_actuatorSocket` (`QUdpSocket*`) — socket de envio (criado sob demanda via `connectToNetwork`)
- `_actuatorTimer` (`QTimer`) — dispara `sendControlPacketsToNetwork()` a cada 16ms
- `_controlPackets` (`QList<RobotControlPacket>`) — fila de comandos a enviar
- `_actuatorMutex` (`QMutex`) — protege a lista de pacotes (thread-safety entre slots de recepção e timer)

**Métodos de comunicação:**

| Método | Gatilho | Função |
|---|---|---|
| `connectToNetwork(hostAddress)` | Signal `Vision::sendHostAddress` | Cria socket, conecta a `hostAddress:simPort`, loga sucesso/falha via spdlog |
| `receiveControlPacket(pkt)` | Signal `Player::sendControlPacket` | Locka mutex e empurra pacote para `_controlPackets` |
| `sendControlPacketsToNetwork()` | QTimer timeout (16ms) | Serializa e envia pacotes acumulados |

**Algoritmo de envio (`sendControlPacketsToNetwork`):**
1. Se socket nulo: limpa fila e retorna
2. Cria `fira_message::sim_to_ref::Packet packet`
3. Locka mutex, itera `_controlPackets`:
   - Adiciona `Command` ao packet: `id = pkt.getPlayerId()`, `yellowteam = !pkt.isTeamBlue()`
   - `wheel_left = pkt.getWheelLeft()`, `wheel_right = pkt.getWheelRight()`
4. Limpa `_controlPackets`, libera mutex
5. Serializa: `packet.ByteSizeLong()` → `SerializeToArray(buffer)`
6. Cria `QNetworkDatagram` e envia via `writeDatagram()`
7. Loga warning se falhar

---

### 5. Coach (`src/entities/coach/`)

> Interface central de gerenciamento e controle de todos os robôs no campo.

**Responsabilidade:** O Coordenador (Coach) é o "cérebro" da estratégia. Ele itera a cada 16ms (60Hz), consulta o `WorldMap` para informações de campo/bola e chama os `Player`s para executarem ações de controle.

**Construtor:** `Coach(QMap<bool, QList<Player*>> players, WorldMap* worldMap)`
- `players`: mapa `isTeamBlue → lista de Player*`. Espera-se 2 times × 3 robôs (ids 0-2).
- `worldMap`: ponteiro para o WorldMap compartilhado.

**Métodos públicos/protetidos:**
| Método | Descrição |
|---|---|
| `getPlayer(bool isTeamBlue, quint8 playerId)` | Retorna `std::optional<Player*>` — busca na lista do time pelo id. **Retorna `std::nullopt` se não encontrado** (cuidado: deve usar `.value()` apenas após conferir, ou `if (auto p = getPlayer(...))`). |
| `getWorldMap()` | Retorna `WorldMap*` para acesso aos dados do campo |
| `runCoach()` | Slot do timer (16ms). Lógica de estratégia — implementação default disponível para edição |

**Implementação default de `runCoach()` (exemplo didático):**

```cpp
void Coach::runCoach() {
    // Example 1: robôs 0 (BLUE e YELLOW) seguem a bola
    QVector2D ballPosition = getWorldMap()->ballPosition();
    getPlayer(BLUE, 0).value()->goTo(ballPosition);
    getPlayer(YELLOW, 0).value()->goTo(ballPosition);

    // Example 2: robôs 1 e 2 (ambas as equipes) rotacionam em direção à bola
    getPlayer(BLUE, 1).value()->rotateTo(ballPosition);
    getPlayer(BLUE, 2).value()->rotateTo(ballPosition);
    getPlayer(YELLOW, 1).value()->rotateTo(ballPosition);
    getPlayer(YELLOW, 2).value()->rotateTo(ballPosition);
}
```

**Constantes:**
- `COACH_ITERATION_INTERVAL_MS = 16` — frequência do loop de estratégia (60Hz)
- `YELLOW = false`, `BLUE = true` — defines para teams

---

## Tipos de Pacote (Utils/types)

### RobotControlPacket (`src/utils/types/robotcontrolpacket/`)

POJO que encapsula um comando de movimento para um robô específico.

**Construtor:** `RobotControlPacket(bool isTeamBlue, quint8 playerId, float wheelLeft, float wheelRight)`

| Método | Retorno | Significado |
|---|---|---|
| `isTeamBlue()` | `bool` | Time alvo (BLUE = true, YELLOW = false) |
| `getPlayerId()` | `quint8` | ID do robô (0-2) |
| `getWheelLeft()` | `float` | Velocidade da roda esquerda (rad/s) |
| `getWheelRight()` | `float` | Velocidade da roda direita (rad/s) |

### RobotDetectionPacket (`src/utils/types/robotdetectionpacket/`)

POJO que encapsula um robô detectado pelo Vision, adicionando a cor da equipe ao `fira_message::Robot` protobuf.

**Construtor:** `RobotDetectionPacket(bool isTeamBlue, const fira_message::Robot& robotDetectionPacket)`

| Método | Retorno | Significado |
|---|---|---|
| `isTeamBlue()` | `bool` | Time do robô detectado |
| `getRobotDetectionPacket()` | `fira_message::Robot` | Pacote protobuf original do Vision |

---

## Utils (`src/utils/`)

Classe estática com métodos geométricos e de matemética para uso por Player e outras entidades.

**Implementação:** todos os métodos são `static` — não há instânciação.

| Método | Assinatura | Descrição |
|---|---|---|
| `smallestAngleDiff` | `static float smallestAngleDiff(float targetAngle, float sourceAngle)` | Diferença angular mínima entre dois ângulos, retornando valor em `[-π, π]` (wrap correto) |
| `normalizeAngle` | `static float normalizeAngle(float angle)` | Normaliza ângulo para o range `[-π, π)` usando `fmod` |
| `checkIfCanBeReversed` | `static bool checkIfCanBeReversed(float robotAngle, float targetAngle)` | Verdadeiro se o robô pode atingir o alvo mais rápido andando para trás (`|angDiff| > π/2 + π/20`) |
| `getAngle` | `static float getAngle(QVector2D current, QVector2D target)` | Ângulo (radians) do vetor current→target via `atan2(dy, dx)` |

**Implementação de `smallestAngleDiff`:**
```cpp
float a = fmod(targetAngle + 2*M_PI, 2*M_PI) - fmod(sourceAngle + 2*M_PI, 2*M_PI);
if (a > M_PI)      a -= 2*M_PI;
else if (a < -M_PI) a += 2*M_PI;
return a;
```

---

## main.cpp — Orquestração dos Módulos

O arquivo `main.cpp` é o ponto de entrada e configura toda a árvore de objetos e conexões:

```cpp
// 1. Cria Vision (escuta multicast)
Vision *vision = new Vision();

// 2. Cria Actuator e conecta ao host address do Vision
Actuator* actuator = new Actuator();
QObject::connect(vision, &Vision::sendHostAddress, actuator, &Actuator::connectToNetwork);

// 3. Cria WorldMap e conecta aos signals de detecção
WorldMap *worldMap = new WorldMap(false);  // jogando do lado direito
QObject::connect(vision, &Vision::sendFieldDetection, worldMap, &WorldMap::updateFieldDetection);
QObject::connect(vision, &Vision::sendBallDetection,  worldMap, &WorldMap::updateBallDetection);

// 4. Cria 6 Players (BLUE ids 0-2, YELLOW ids 0-2)
QMap<bool, QList<Player*>> _players;
for(int i = 0; i <= 1; i++) {
    _players.insert(i, QList<Player*>());
    for(int j = 0; j < 3; j++) {
        _players[i].push_back(new Player(i, j));
        QObject::connect(vision, &Vision::sendRobotDetection, _players[i][j], &Player::updateFromDetection);
        QObject::connect(_players[i][j], &Player::sendControlPacket, actuator, &Actuator::receiveControlPacket);
    }
}

// 5. Cria Coach com players e worldmap
Coach* coach = new Coach(_players, worldMap);

// 6. Executa event loop Qt
bool exec = a.exec();

// 7. Limpeza (destrutores)
delete coach;
delete worldMap;
for (auto& team : _players)
    for (auto& player : team)
        delete player;
delete actuator;
delete vision;
```

---

## Build, Dependências e Deploy

### Pré-requisitos (Ubuntu 20.04 / 22.04)

```bash
sudo apt-get update
sudo apt-get install build-essential cmake qtbase5-dev qt5-qmake \
  libprotobuf-dev protobuf-compiler libprotoc-dev protobuf-compiler-grpc \
  libgrpc++-dev libgrpc-dev libqt5serialport5-dev \
  google-mock libgmock-dev libgtest-dev \
  libspdlog-dev libfmt-dev
sudo apt-get upgrade
```

### Submódulo protobuf

O repositório usa um submódulo Git para as definições protobuf do FIRA:

```bash
git submodule update --init --recursive
```

**Submódulo:** `include/proto` → `https://github.com/MaracatronicsRobotics/Armorial-Proto.git`

O build gera automaticamente os headers C++ dos `.proto` via `protoc` na linha `system()` do `.pro`:

```pro
system(echo "Generating simulation proto headers" && \
  cd include/proto/simulation && protoc --cpp_out=../ *.proto && cd ../../..)
```

### Build local (QMake)

```bash
cd /caminho/para/Maraca-PSEL
mkdir build && cd build
qmake ..
make -j$(nproc)
```

**Saída:** `../bin/Armorial-PSEL` (relativo ao diretório do projeto, configurado pelo `DESTDIR` no `.pro`).

### CI/CD (GitHub Actions)

Arquivo: `.github/workflows/build.yml`

| Etapa | Ação |
|---|---|
| Checkout | `actions/checkout@v3` com `submodules: recursive` |
| Instalar deps | `apt-get install` da lista completa |
| Instalar Google Test | Build manual do `googletest`/`googlemock` do `/usr/src/googletest` e cópia dos `.a` para `/usr/lib` |
| ldconfig | Atualiza linker cache |
| Build | `mkdir build && cd build && qmake .. && make -j$(nproc)` |

**Matriz:** `ubuntu-20.04` e `ubuntu-22.04` em paralelo.

** Gatilhos:** push (ignorando caminhos `docs**`) e pull_request (somente se o PR vier de repo externo — evita builds de forks).

---

## Dependências de Software

| Biblioteca | Propósito |
|---|---|
| **Qt 5** (qtbase5-dev) | QObject, signals/slots, QUdpSocket, QNetworkDatagram, QTimer, QMap, QVector2D |
| **protobuf** (libprotobuf-dev + protobuf-compiler) | Serialização dos pacotes `fira_message` (Environment, Frame, Robot, Ball, Field, Packet, Command) |
| **fmt** (libfmt-dev) | Formatação de strings (potencial uso interno; linkado via `-lfmt`) |
| **spdlog** (libspdlog-dev) | Logging (info/warn/critical) em Actuator, Vision, Player |
| **Google Test / Google Mock** | Framework de testes (instalado mas sem testes implementados no repositório) |

---

## Estratégia de Controle (exemplo)

A estratégia default em `Coach::runCoach()` é didática:

- **Robô 0 (Both teams):** `goTo(ballPosition)` — movimento direto em direção à bola com correção de rotação via PID
- **Robôs 1 e 2 (Both teams):** `rotateTo(ballPosition)` — rotação em lugar para apontar a bola (sem translação)

Isso ilustra os dois modos de controle do `Player`:
1. **goTo:** movimento com direção e rotação simultâneas (wheels differenced)
2. **rotateTo:** rotação pura (wheels em sentidos opostos, mesma magnitude)

Em um projeto real, a estratégia do Coach seria expandida com:
- Atribuição de posições (defesa, ataque, zaga)
- Formações (ex: quadruponto, retrato)
- Comportamento de chute (quando próximo da bola)
- Evitação de colisões e robôs adversários
- Comunicação interpara robôs

---

## Pontos de Atenção no Código

### `std::optional` e `.value()` no Coach

O método `getPlayer()` retorna `std::optional<Player*>`. O exemplo default usa `.value()` diretamente, o que **lança `std::bad_optional_access`** se o player não existir. Em código de produção, prefira:

```cpp
if (auto player = getPlayer(BLUE, 0)) {
    player.value()->goTo(ballPosition);
}
```

ou use `std::optional::has_value()` antes.

### Hardcoded constants na estratégia

As constantes de controle (`KP=25, KD=2.5, BASE_SPEED=30`) e de campo (`penaltyDepth=0.15`, `penaltyWidth=0.7`, `penaltyMarkDistanceFromGoal=0.1125`, `centerRadius=0.25`) são hardcoded no código. Em uma implementação mais flexível, poderiam vir de config (arquivo, variáveis de ambiente, ou protobuf de configuração).

### Sem tratamento de `std::nullopt` no exemplo

O `runCoach()` exemplo não verifica `std::nullopt` — se um robô não for detectado ou se a simulação não tiver 6 robôs, o código crashará. Isso é aceitável para um template base, mas deve ser corrigido antes de uso real.

### Faltam testes

O repositório inclui as bibliotecas Google Test/Mock nas dependências e no CI, mas **não há testes implementados** no diretório `src/` ou em subdiretório de testes. Isso é comum em projetos base/protótipo.

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Commit inicial:** criação do projeto com estrutura de entidades, build QMake e main.cpp
- **Submódulo:** `Armorial-Proto` (comunicação protobuf FIRA)
- **Licença:** GNU GPL v3+

> **Nota:** detalhes exatos de commits podem ser verificados no repositório original.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. Seções subsequentes devem incluir:

- **Testes:** registrar cobertura e casos testados quando implementados
- **Configuração:** registrar parâmetros ajustáveis (porta, endereço multicast, constantes PID)
- **Evolução da estratégia:** documentar mudanças no `Coach::runCoach()` quando substituída
- **Diagramas:** atualizar diagramas de fluxo se a arquitetura mudar
- **Histórico:** manter log com data e descrição das mudanças significativas

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/Maraca-PSEL
- **Branch main:** https://github.com/lucaslimacodes/Maraca-PSEL/tree/main
- **Maracatronics (UFPE):** http://www.maracatronics.com/
- **Submódulo protobuf:** https://github.com/MaracatronicsRobotics/Armorial-Proto
- **Qt 5 Documentation:** https://doc.qt.io/qt-5/
- **Protocol Buffers:** https://protobuf.dev/
- **spdlog:** https://github.com/gabime/spdlog
- **fmt library:** https://fmt.dev/
- **FIRASim (reference):** https://github.com/MaracatronicsRobotics/FIRASim
- **RoboCup Small Size League:** https://www.robocup.org/research/ssl/

---

*Wiki detalhada gerada por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*

*Última atualização: 2026-09-15*
