# carrinho_grupo_1

> Wiki detalhada do repositório **carrinho_grupo_1** — Pacote ROS 2 (ament_python) para controle de um carrinho robótico com motores DC, encoders e GPIO (GRUPO 1)

- **Repositório original:** https://github.com/lucaslimacodes/carrinho_grupo_1
- **Pacote ROS 2:** `carrinho1` (ament_python)
- **Versão do package.xml:** 0.0.0
- **Licença:** Apache-2.0
- **Última compilação registrada nos logs:** 2025-11-20
- **Tamanho total:** 273 arquivos / ~2.3 MB
- **Linguagem principal:** Python 3 (ROS 2 + lgpio)
- **Wiki deste repositório:** [repos/carrinho_grupo_1/](./carrinho_grupo_1/) (esta pasta)

---

## Visão Geral

Este repositório contém um **pacote ROS 2 (ament_python)** para controle de um **carrinho robótico** (plataforma móvel com dupla de motores DC). O nome `carrinho` (português para "cart" / "carro pequeno") refere-se ao veículo robótico, não a um carrinho de compras.

O pacote `carrinho1` fornece:

1. **Nó de driver (`driver_node`)** — controla motores DC via GPIO/PWM (biblioteca `lgpio`), lê encoders para medição de velocidade, implementa controle PID simples (proporcional) para rastreamento de velocidade alvo, recebe comandos de velocidade linear + angular via ROS 2 topic.
2. **Nó publicador simples (`publisher_node`)** — publica comandos de velocidade no topic `/topic` (formato `vel_linear|vel_angular`), lê entrada do stdin.
3. **Script standalone de encoder (`encoder_tester.py`)** — teste independente de encoders sem ROS 2, com lógica de controle PID acumulativo.

O código é voltado para **hardware específico** (Raspberry Pi + placa de drivers de motores L298N ou similar, encoders ópticos/incrementais), com mapeamento explícito de pinos GPIO.

À frente do código-fonte, o repositório acumula **165 arquivos de log** de compilações `colcon` (ROS 2 build tool) e **artefatos de instalação** (egg-info, scripts de setup shell/PowerShell) gerados pelo ciclo de build/install do ROS 2.

---

## Estrutura do Projeto

```
carrinho_grupo_1/
└── grupo_1_carrinho/
    └── src/
        ├── carrinho1/                    # Pacote ROS 2 fonte (ament_python)
        │   ├── package.xml               # Definição do pacote ROS 2 (format 3)
        │   ├── setup.py                  # setuptools + entry_points (nós ROS 2)
        │   └── carrinho1/
        │       ├── __init__.py           # (vazio)
        │       ├── driver.py             # Nó ROS 2: DriverSubscriber (controle de motores)
        │       ├── simple_publisher.py   # Nó ROS 2: publicador de velocidade (stdin)
        │       └── encoder_tester.py     # Script standalone: teste de encoders + PID
        │       └── test/
        │           ├── test_copyright.py # Linter: verificação de cabeçalho copyright
        │           ├── test_flake8.py    # Linter: verificação PEP8 via flake8
        │           └── test_pep257.py    # Linter: verificação docstrings via pep257
        │
        ├── build/                        # Artefatos de build colcon
        │   ├── carrinho1/                # Build do pacote carrinho1
        │   │   ├── carrinho1.egg-info/  # Metadados Python (SOURCES.txt, entry_points.txt, etc.)
        │   │   ├── build/                # Build temporário (lib/carrinho1/...)
        │   │   ├── install.log          # Log da instalação via setup.py
        │   │   └── colcon_command_prefix_setup_py.sh
        │   └── Carrinho1/                # Build com nome "Carrinho1" (maísculo — build duplicado)
        │       ├── Carrinho1.egg-info/
        │       ├── build/lib/Carrinho1/  # Build duplicado com maiúsculas
        │       └── install(1).log
        │
        ├── install/                      # Instalação colcon (prefixo único)
        │   ├── carrinho1/                # Pacote instalado (caminhos, hooks)
        │   │   └── share/carrinho1/
        │   │       ├── package.sh / package.ps1
        │   │       └── hook/             # Hooks ament: pythonpath.sh, ament_prefix_path.*
        │   ├── Carrinho1/                # Instalação duplicada com maiúsculas
        │   │   └── share/Carrinho1/
        │   ├── local_setup.sh            # Script de setup bash (colcon)
        │   ├── local_setup.ps1           # Script de setup PowerShell (colcon)
        │   ├── local_setup.bash          # Alias bash para local_setup.sh
        │   ├── _local_setup_util_sh.py   # Utilitário Python para gerar comandos shell
        │   └── _local_setup_util_ps1.py  # Versão PowerShell do utilitário
        │
        ├── log/                          # 165 arquivos de log de compilações
        │   ├── latest/                   # Build mais recente (latest)
        │   ├── latest_build/             # Build mais recente (alternativo)
        │   └── build_2025-11-20_20-01-33/  # 16 diretórios de build datados (20-01 até 21-05)
        │       ├── logger_all.log        # Log geral do logger
        │       ├── events.log            # Log de eventos do build
        │       └── carrininho1/          # Logs específicos do pacote
        │           ├── command.log       # Comandos executados (setup.py install)
        │           ├── stdout.log / stderr.log
        │           ├── stdout_stderr.log
        │           └── streams.log
        │
        └── resource/                     # (ausente no clone — gerado pelo ament)
```

### Inventário de arquivos

| Tipo | Quantidade | Observação |
|------|-----------|------------|
| `.log` | 165 | Logs de compilação colcon (builds datadas + latest) |
| `.py` | 22 | Código Python: nós ROS 2, scripts, utilitários, linters, egg-info |
| `.txt` | 18 | Metadados egg-info (SOURCES.txt, requires.txt, entry_points.txt, etc.) |
| `.sh` | 10 | Scripts de setup/colcon (bash, hooks) |
| `.ps1` | 8 | Scripts de setup/colcon (PowerShell) |
| **Total** | **273** | ~2.3 MB |

---

## Stack Tecnológica

| Componente | Tecnologia | Versão/Observação |
|------------|-----------|-------------------|
| **Framework robótico** | ROS 2 (via rclpy) | ament_python (package format 3) |
| **Linguagem** | Python 3 | 3.12 (via site-packages no install/) |
| **Controle GPIO** | lgpio | gpiochip_open, callbacks, PWM, GPIO claims |
| **Mensagens ROS 2** | std_msgs | `std_msgs.msg.String` para topic de velocidade |
| **Build tool** | colcon | Build system padrão do ROS 2 |
| **Package manager** | setuptools | via `setup.py` + entry_points console_scripts |
| **Linters (testes)** | ament_copyright, ament_flake8, ament_pep257 | Verificação de copyright, PEP8, docstrings |
| **GPIO hardware** | BCM GPIO (Raspberry Pi) | Pinos mapeados explicitamente (ver seção "Hardware") |

### Dependências ROS 2 (package.xml)

```
exec_depend: rclpy
exec_depend: std_msgs
exec_depend: python-numpy
exec_depend: arm_interfaces

test_depend: ament_copyright
test_depend: ament_flake8
test_depend: ament_pep257
test_depend: python3-pytest
```

### Entry points (console_scripts)

```python
entry_points={
    'console_scripts': [
        'driver_node = carrinho1.driver:main',
        'publisher_node = carrinho1.simple_publisher:main',
    ],
},
```

---

## Componentes do Sistema

### 1. driver.py — Nó de Driver do Carrinho

**Classe principal:** `DriverSubscriber(Node)`

|Nó| `driver` |
|Criação| `driver = DriverSubscriber()` |

**Funcionalidades:**

- **Subscrição ROS 2:** Ouve mensagens `std_msgs/String` no topic `topic` (formato: `vel_linear|vel_angular`).
- **Controle de motores (lgpio):**
  - 2 motores DC (esquerdo = Motor A, direito = Motor B)
  - Driver L298N (ou similar): pinos PWM (PWMA=13, PWMB=12) + direção (AIN1/2, BIN1/2) + STBY (27)
  - PWM a 1000 Hz, controle via `lgpio.tx_pwm()`
- **Leitura de encoders:**
  - Encoder Esquerdo: ENC_VRD_ESQ=6 (borda de subida), ENC_AMR_ESQ=5
  - Encoder Direito: ENC_VRD_DIR=23 (borda de subida), ENC_AMR_DIR=26
  - Callbacks incrementam contadores globais a cada pulso
- **Cálculo de velocidade:**
  - `DISTANCIA_POR_PULSO = 2π × RAIO_RODA / PULSOS_POR_VOLTA`
  - `RAIO_RODA = 0.034m`, `PULSOS_POR_VOLTA = 56 × 11 = 616`
  - Velocidade instantânea = `pulsos × DISTANCIA_POR_PULSO / t` (t = 0.1s)
- **Controle PID simplificado (proporcional):**
  - `diff = vel_alvo - vel_atual`
  - `pwm += K × diff` (K = 10)
  - Limite inferior: pwm = 0 (sem controle integral/derivativo)
- **Thread separada:** Loop de controle em `Thread(target=run)` a cada 0.1s

**Conversão kinemática ( diferencial):**
```
vel_alvo_dir = vel_linear + (DISTANCIA_ENTRE_RODA/2) × vel_angular
vel_alvo_esq = vel_linear - (DISTANCIA_ENTRE_RODA/2) × vel_angular
```
`DISTANCIA_ENTRE_RODA = 0.2185m`

### 2. simple_publisher.py — Publicador Simples

**Classe principal:** `MinimalPublisher(Node)`

- Publica `std_msgs/String` no topic `topic` a cada 1 segundo
- Lê comando do stdin: `"vel_linear|vel_angular"` (ex: `"0.2|0.5"`)
- Usado para enviar comandos de velocidade ao nó driver

### 3. encoder_tester.py — Teste Standalone de Encoders

- Versão "bare-metal" do driver sem ROS 2
- Mesma lógica de GPIO/encoder/PWM, mas com PID acumulativo (`acumulado_esq`, `acumulado_dir`)
- Útil para testar hardware fora do contexto ROS 2

---

## Modelagem de Hardware (GPIO)

### Mapeamento de Pinos (BCM numbering)

| Função | Pino GPIO | Descrição |
|--------|-----------|-----------|
| **Motor A (Esquerdo)** | | |
| PWMA_PIN | 13 | PWM controle de velocidade Motor A |
| AIN1_PIN | 24 | Direção Motor A (bit 1) |
| AIN2_PIN | 22 | Direção Motor A (bit 2) |
| **Motor B (Direito)** | | |
| PWMB_PIN | 12 | PWM controle de velocidade Motor B |
| BIN1_PIN | 16 | Direção Motor B (bit 1) |
| BIN2_PIN | 17 | Direção Motor B (bit 2) |
| **Controle** | | |
| STBY_PIN | 27 | Standby (ativo em HIGH = 1) |
| **Encoder Esquerdo** | | |
| ENC_VRD_ESQ | 6 | LRU encoder esquerdo (interrupção) |
| ENC_AMR_ESQ | 5 | (reservado/no usado no driver.py atual) |
| **Encoder Direito** | | |
| ENC_VRD_DIR | 23 | LRU encoder direito (interrupção) |
| ENC_AMR_DIR | 26 | (reservado/no usado no driver.py atual) |

> **Nota:** No `driver.py`, apenas ENC_VRD_ESQ (6) e ENC_VRD_DIR (23) são usados com `gpio_claim_alert` + callback. ENC_AMR_ESQ e ENC_AMR_DIR são definidos mas não ativados.

---

## Como Usar / Build

### Pré-requisitos

- ROS 2 (Humble/Iron/Jazzy) com `ament_python` instalado
- Python 3.12 com `lgpio` (biblioteca GPIO para Raspberry Pi/Linux)
- `colcon` (build tool ROS 2)
- Hardware: Raspberry Pi ou similar com GPIOs acessíveis

### Compilação (colcon)

```bash
# Na raiz do workspace (grupo_1_carrinho/ ou acima)
colcon build --packages-select carrinho1

# Ou build de todos os pacotes
colcon build
```

O `colcon build` executa:
1. `setup.py egg_info` → gera metadados
2. `setup.py install` → instala o pacote no prefixo de install
3. Gera scripts de setup (`local_setup.sh`, `local_setup.ps1`, hooks)

### Instalação / Ambiente

```bash
# Bash
source install/local_setup.sh
# ou
source install/setup.bash

# PowerShell
.\install\local_setup.ps1
```

Isso configura `PYTHONPATH`, `COLCON_PREFIX_PATH` e paths de hooks do ROS 2.

### Execução dos nós ROS 2

```bash
# Nó de driver (controle de motores)
ros2 run carrinho1 driver_node

# Nó publicador (envia comandos de velocidade)
ros2 run carrinho1 publisher_node
# Digite: vel_linear|vel_angular  (ex: 0.2|0.5)
# Ctrl+C para parar
```

### Teste standalone de encoders

```bash
python3 src/carrinho1/carrinho1/encoder_tester.py
# Ctrl+C para parar
```

### Topic ROS 2

| Topic | Tipo | Descrição |
|-------|------|-----------|
| `/topic` | `std_msgs/String` | Comando de velocidade: `"vel_linear|vel_angular"` |

---

## Logs e Build Artifacts

O repositório contém **165 arquivos `.log`** de múltiplas compilações `colcon` datadas de **2025-11-20** (de 20:01 até 21:05), mais diretórios `latest/` e `latest_build/`.

### Tipos de log

| Arquivo | Conteúdo |
|---------|----------|
| `logger_all.log` | Log consolidado do logger de build |
| `events.log` | Eventos do build colcon (etapas, pacotes) |
| `carrinho1/command.log` | Comandos `setup.py` executados (install) |
| `carrinho1/stdout.log` / `stderr.log` | Saída e erros do build do pacote |
| `carrinho1/streams.log` | Streams combinados |
| `install.log` | Log da instalação via setuptools |

### Observações sobre os logs

- Os logs mostram comandos `python3 setup.py install` executados em `/home/robot/Documents/grupo_1_carrinho/src/carrinho1`
- Prefixo de instalação: `/home/robot/Documents/grupo_1_carrinho/src/install/carrinho1/lib/python3.12/site-packages`
- Há **builds duplicadas** com nomes `carrinho1` (minúsculas) e `Carrinho1` (maiúsculas) — artefato de execuções 컨secutivas ou builds isoladas
- Os logs são **produto do processo de desenvolvimento/compilação**, não de execução do robô em campo

---

## Qualidade e Observações

### Pontos positivos

- Código funcional com controle PID proporcional implementado
- Separação clara entre nó ROS 2 (`driver.py`) e script standalone (`encoder_tester.py`)
- Mapeamento de hardware bem documentado nos comentários do código
- Uso correto de callbacks GPIO para leitura de encoders (borda de subida)
- Linters ROS 2 configurados (ament_copyright, ament_flake8, ament_pep257) — embora os testes de linter atualmente estejam **skipados**
- build/instalar via colcon + setuptools segue o padrão ament_python

### Observações e possíveis melhorias

1. **`__init__.py` vazio** — pacote sem `__all__` ou docstrings de pacote
2. **Linters skipados:** `test_copyright.py`, `test_flake8.py`, `test_pep257.py` usam `@pytest.mark.skip(reason='No copyright header has been placed in the generated source file.')` — os arquivos gerados não têm cabeçalho copyright
3. **PID incompleto:** controle apenas proporcional (P), sem integral (I) ou derivativo (D) — pode haver erro em regime permanente
4. **Global state:** uso extensivo de variáveis globais (`GLOBAL_qtd_pulsos_dir`, `GLOBAL_pwmA`, etc.) — não thread-safe além do controle básico
5. **Encoder parcial:** apenas 2 dos 4 pinos de encoder são usados nos callbacks (ENC_VRD_ESQ e ENC_VRD_DIR); os pinos AMR parecem reservados
6. **`simple_publisher.py` usa `input()` bloqueante** dentro do callback do timer ROS 2 — pode travar o nó se o stdin não estiver pronto
7. **Build duplicado:** presença de `Carrinho1` (maiúsculas) e `carrinho1` (minúsculas) como builds/installs separados — sugere execuções não limpas ou builds manuais misturados
8. **Sem mensagens customizadas:** usa `std_msgs/String` para comando de velocidade — em um projeto real, uma mensagem customizada (ex: `geometry_msgs/Twist` ou mensagem própria) seria mais idiomática
9. **`arm_interfaces` como dependência:** sugere que o pacote foi projetado para integrar com um pacote de interfaces de braço robótico (`arm_interfaces`), mas não há uso visível no código atual

### Pontos de atenção no código

- `encoder_tester.py` e `driver.py` têm lógica duplicada de mapeamento de pinos e cálculo de velocidade — idealmente extrair uma biblioteca compartilhada
- `lgpio.gpiochip_open(0)` assume gpiochip 0 — pode falhar em sistemas com múltiplos gpiochips
- Sem tratamento de erros em caso de falha de GPIO claim ou GPIO write
- `print()` usado para logging em vez de `self.get_logger()` no `encoder_tester.py`

---

## Commits, Branches e Histórico

- **Branch principal:** `main` (suposto — não verificado no clone)
- **Última compilação nos logs:** 2025-11-20 (várias tentativas entre 20:01 e 21:05)
- **Criado em:** data não determinada a partir do código (os logs indicam 2025-11-20 como atividade recente)
- **Repositório original:** https://github.com/lucaslimacodes/carrinho_grupo_1

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original: https://github.com/lucaslimacodes/carrinho_grupo_1/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Diagrama de hardware:** foto/esquemático da conexão GPIO e da placa de drivers
- **Novos nós/componentes:** documentar novos ROS nodes, topics, serviços
- **Mudanças de configuração:** registrar novos parâmetros ou mudaças de pino
- **Decisões de modelo:** registrar escolhas como PID simplificado, uso de String vs Twist, etc.
- **Histórico:** manter log.md atualizado com data e descrição da mudança

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/carrinho_grupo_1
- **ROS 2 Documentation:** https://docs.ros.org/
- **rclpy (ROS 2 Python client):** https://docs.ros.org/en/humble/Contributing/Developer-Guide.html
- **ament_python (package format 3):** https://docs.ros.org/en/humble/Concepts/About-Ament.html
- **colcon build tool:** https://colcon.readthedocs.io/
- **lgpio (Python GPIO library):** https://github.com/io Sturm/lgpio
- **Raspberry Pi GPIO:** https://www.raspberrypi.com/documentation/computers/os.html#gpio
- **std_msgs.msg.String:** http://docs.ros.org/en/api/std_msgs/html/msg/String.html
- **Wiki do projeto lhl-wiki:** https://github.com/lucaslimacodes/lhl-wiki

---

*Wiki detalhada gerada por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte e logs de compilação.*
*Última atualização: 2025-11-20 (data da última compilação registrada nos logs)*
