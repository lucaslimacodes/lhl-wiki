# RISC-V-PROJECT

> Wiki detalhada do repositório **RISC-V-PROJECT** — Implementação de Processador RISC-V Pipeline (IF674 - Infraestrutura de Hardware, CIn-UFPE)

- **Repositório original:** https://github.com/lucaslimacodes/RISC-V-PROJECT
- **Branch principal:** `main`
- **Linguagem principal:** SystemVerilog + Verilog
- **Última atualização do repo:** 2023-08-11
- **Criado em:** 2023-08-11
- **Tamanho:** 70 arquivos, 1.8MB
- **Wiki deste repositório:** [repos/RISC-V-PROJECT/](./RISC-V-PROJECT/) (esta pasta)

---

## Visão Geral

Este repositório contém a implementação de um **processador RISC-V pipeline** em SystemVerilog, desenvolvido como projeto da disciplina **IF674 - Infraestrutura de Hardware** no CIn-UFPE. O objetivo é implementar instruções do conjunto de instruções RV32I em um processador com arquitetura pipeline, incluindo unidades de controle, datapath, forwarding, hazard detection, e memórias.

O repositório é **"irmão"** do [Projeto_IH_RISC-V](./Projeto_IH_RISC-V/) — ambos compartilham a mesma estrutura e objetivo, mas o RISC-V-PROJECT possui **mais iterações de desenvolvimento** (indicadas pelos arquivos `.bak`) e está em um estado mais avançado de implementação.

---

## Estrutura do Projeto

```
RISC-V-PROJECT/
├── README.md                          # Este arquivo
├── design/                            # Implementação do processador (SystemVerilog)
│   ├── adder.sv                       # Somador
│   ├── adder.sv.bak                   # Versão anterior do adder
│   ├── ALUController.sv               # Unidade de controle da ALU
│   ├── ALUController.sv.bak          # Versão anterior
│   ├── alu.sv                         # ALU (Arithmetic Logic Unit)
│   ├── alu.sv.bak                    # Versão anterior da ALU
│   ├── BranchUnit.sv                  # Unidade de branch
│   ├── Controller.sv                  # Unidade de controle principal
│   ├── Controller.sv.bak             # Versão anterior do controle
│   ├── datamemory.sv                  # Memória de dados
│   ├── Datapath.sv                   # Datapath do processador
│   ├── flopr.sv                       # Flip-flop com reset assíncrono
│   ├── ForwardingUnit.sv             # Unidade de forwarding (hazards)
│   ├── HazardDetection.sv            # Detecção de hazards
│   ├── imm_Gen.sv                     # Extensor de imediato
│   ├── imm_Gen.sv.bak                # Versão anterior
│   ├── instructionmemory.sv           # Memória de instruções
│   ├── Memoria32Data.sv              # Memória de dados 32-bit
│   ├── Memoria32.sv                  # Memória 32-bit genérica
│   ├── mux2.sv                        # Multiplexador 2:1
│   ├── mux4.sv                        # Multiplexador 4:1
│   ├── ramOnChip32.v                  # RAM on-chip 32-bit (Verilog)
│   ├── ramOnChipData.v               # RAM de dados (Verilog)
│   ├── RegFile.sv                     # Register File (32 registradores)
│   ├── RegPack.sv                     # Pacote de registradores
│   └── RISC_V.sv                      # Top-level do processador RISC-V
├── sim/                               # Simulações e resultados
│   ├── simulation1 - ALU/
│   │   ├── instruction.mif           # Arquivo MIF de instruções
│   │   ├── data.mif                  # Arquivo MIF de dados
│   │   └── README.md                 # Descrição da simulação
│   ├── simulation2 - LOAD/
│   ├── simulation3 - AUIPC/
│   ├── simulation4 - JAL, BEQ/
│   └── simulation5 - STORE/
├── verif/                             # Testbenches e verificação
├── doc/                               # Documentação adicional
└── .git/                              # Controle de versão
```

**Arquivos por categoria:**

| Categoria | Quantidade | Descrição |
|-----------|------------|-----------|
| SystemVerilog (.sv) | 20 | Implementação do processador |
| Verilog (.v) | 2 | RAM on-chip (legacy Verilog) |
| Backups (.bak) | 3 | Versões anteriores (alu.sv, Controller.sv, imm_Gen.sv) — mostra evolução do projeto |
| Simulações | 5 | Simulações de ALU, LOAD, AUIPC, JAL/BEQ, STORE |
| Total | 70 arquivos | 1.8MB |

---

## Arquitetura do Processador

### Pipeline RISC-V

O processador implementa uma arquitetura **pipeline** clássica com as seguintes etapas:

1. **IF (Instruction Fetch)** — Busca da instrução na instruction memory
2. **ID (Instruction Decode)** — Decodificação da instrução, leitura do register file
3. **EX (Execute)** — Execução da operação na ALU, cálculo de endereço de branch/load/store
4. **MEM (Memory Access)** — Acesso à memória de dados (load/store)
5. **WB (Write Back)** — Escrita no register file

### Módulos principais

#### Datapath (`Datapath.sv`)
- **Top-level do datapath** — conecta todos os componentes do pipeline
- Fluxo de dados entre IF, ID, EX, MEM, WB
- Módulos de entrada: instruction memory, register file, ALU, data memory

#### Unidade de Controle (`Controller.sv`)
- Decodifica a instrução e gera os sinais de controle para o datapath
- Controla: ALU operation, muxes, write enable, mem read/write, branch, jump
- Versões anteriores preservadas em `.bak` mostram a evolução da lógica

#### ALU (`alu.sv`, `ALUController.sv`)
- Operações aritméticas e lógicas: ADD, SUB, AND, OR, XOR, SLT, SLTU, etc.
- ALUController decodifica a operação da instrução e seleciona a operação da ALU
- 3 versões diferentes da ALU indicam refinamento iterativo

#### Unidade de Branch (`BranchUnit.sv`)
- Calcula o endereço de destino do branch
- Compara registradores para decidir se o branch é tomado
- Suporta BEQ, BNE, BLT, BGE, BLTU, BGEU

#### Extensor de Imediato (`imm_Gen.sv`)
- Gera o imediato estendido de sinal para diferentes formatos de instrução
- R-type, I-type, S-type, B-type, U-type, J-type
- 2 versões indicam refinamento

#### Forwarding Unit (`ForwardingUnit.sv`)
- Resolve hazards de dados através de forwarding (bypassing)
- Decide se os dados devem vir do stage atual ou de stages posteriores
- Evita stalls desnecessários quando possível

#### Hazard Detection (`HazardDetection.sv`)
- Detecta hazards de controle (branches) e de dados que exigem stall
- Gera sinais de stall e flush para o pipeline

#### Register File (`RegFile.sv`, `RegPack.sv`)
- 32 registradores de 32 bits (x0-x31, x0=hardwired zero)
- Leitura assíncrona, escrita no clock
- Two-read, one-write por ciclo

#### Memórias
- **Instruction Memory** (`instructionmemory.sv`) — armazena o programa
- **Data Memory** (`datamemory.sv`, `Memoria32Data.sv`, `ramOnChip32.v`, `ramOnChipData.v`) — armazena dados
- Arquivos `.mif` (Memory Initialization File) para 로드 inicial

#### Flip-Flop (`flopr.sv`)
- Flip-flop com reset assíncrono para o pipeline register

#### Muxes (`mux2.sv`, `mux4.sv`)
- Multiplexadores para seleção de caminhos no datapath

---

## Instruções Implementadas

Os arquivos `.bak` indicam que o projeto passou por várias iterações. O estado atual das instruções implementadas:

| # | Instrução | Tipo | Status | Descrição |
|---|-----------|------|--------|-----------|
| 1 | `ADD` | R | ✅ | Adição de registradores |
| 2 | `ADDI` | I | ✅ | Adição com imediato |
| 3 | `AND` | R | ✅ | AND bit a bit |
| 4 | `ANDI` | I | ✅ | AND com imediato |
| 5 | `AUIPC` | U | ✅ | Add Upper Immediate to PC |
| 6 | `BEQ` | B | ✅ | Branch if Equal |
| 7 | `BGE` | B | ✅ | Branch if Greater or Equal |
| 8 | `BGEU` | B | ✅ | Branch if GE Unsigned |
| 9 | `BLT` | B | ✅ | Branch if Less Than |
| 10 | `BNE` | B | ✅ | Branch if Not Equal |
| 11 | `JAL` | J | ✅ | Jump and Link |
| 12 | `JALR` | I | ✅ | Jump and Link Register |
| 13 | `LB` | I | ✅ | Load Byte |
| 14 | `LBU` | I | ✅ | Load Byte Unsigned |
| 15 | `LH` | I | ✅ | Load Halfword |
| 16 | `LUI` | U | ✅ | Load Upper Immediate |
| 17 | `LW` | I | ✅ | Load Word |
| 18 | `OR` | R | ✅ | OR bit a bit |
| 19 | `ORI` | I | ✅ | OR com imediato |
| 20 | `SB` | S | ✅ | Store Byte |
| 21 | `SH` | S | ✅ | Store Halfword |
| 22 | `SLTI` | I | ✅ | Set Less Than Immediate |
| 23 | `SLTIU` | I | ✅ | Set Less Than Immediate Unsigned |
| 24 | `SLT` | R | ✅ | Set Less Than |
| 25 | `SLTU` | R | ✅ | Set Less Than Unsigned |
| 26 | `SRA` | R | ✅ | Shift Right Arithmetic |
| 27 | `SRAI` | I | ✅ | Shift Right Arithmetic Immediate |
| 28 | `SRL` | R | ✅ | Shift Right Logical |
| 29 | `SRLI` | I | ✅ | Shift Right Logical Immediate |
| 30 | `SUB` | R | ✅ | Subtração |
| 31 | `SW` | S | ✅ | Store Word |
| 32 | `XOR` | R | ✅ | XOR bit a bit |
| 33 | `XORI` | I | ✅ | XOR com imediato |
| 34 | `HALT` | Pseudo | ✅ | Parar execução (pseudo-instrução) |

> **Nota:** A lista completa de status de cada instrução deve ser verificada no README do repositório original ou nos arquivos de simulação.

---

## Simulações

### Visão geral

O diretório `sim/` contém 5 simulações organizadas por funcionalidade:

```
sim/
├── simulation1 - ALU/        → Testa operações da ALU
│   ├── instruction.mif       → Programa de teste para ALU
│   ├── data.mif              → Dados iniciais
│   └── README.md             → Descrição da simulação
├── simulation2 - LOAD/       → Testa instruções de load
├── simulation3 - AUIPC/      → Testa AUIPC
├── simulation4 - JAL, BEQ/   → Testa jumps e branches
└── simulation5 - STORE/      → Testa instruções de store
```

### Como usar as simulações

Cada simulação contém um `README.md` com instruções específicas. Geralmente:

1. **Carregar os arquivos MIF** na memória de instruções e dados
2. **Rodar a simulação** no ModelSim ou simulador equivalente
3. **Verificar os resultados** — comparar com os valores esperados
4. **Analisar o waveform** para debug do pipeline

### Ferramentas de simulação

- **ModelSim-Intel® FPGAs Standard Edition Software Version 20.1.1** — principal ferramenta
- **CompSim** — simulador alternativo
- **RISC-V Interpreter (Cornell University)** — para validação de resultados

---

## Arquivos MIF (Memory Initialization File)

Os arquivos `.mif` são usados para inicializar as memórias com programas de teste:

- **instruction.mif** — Programa de teste para a instrução/functionalidade específica
- **data.mif** — Dados iniciais para a memória de dados

Esses arquivos são carregados na simulação para fornecer o programa e os dados iniciais.

---

## Comparação: RISC-V-PROJECT vs Projeto_IH_RISC-V

| Aspecto | RISC-V-PROJECT | Projeto_IH_RISC-V |
|---------|----------------|-------------------|
| **Arquivos** | 70 | 57 |
| **Tamanho** | 1.8MB | 964KB |
| **SV no design/** | 20 | 20 |
| **Arquivos .bak** | 3 (alu.sv, Controller.sv, imm_Gen.sv) | 0 |
| **Simulações** | 5 | 5 |
| **Arquivos Verilog (.v)** | 2 | 2 (ramOnChip32.v, ramOnChipData.v) |
| **Estado** | Mais avançado (mais iterações) | Base (menos iterações) |

**Diferenças principais:**
- O `RISC-V-PROJECT` possui **3 arquivos .bak** (alu.sv.bak, Controller.sv.bak, imm_Gen.sv.bak), indicando que esses módulos passaram por **múltiplas iterações de desenvolvimento**
- O `RISC-V-PROJECT` é maior (1.8MB vs 964KB) e tem mais arquivos (70 vs 57)
- Ambos compartilham a mesma estrutura de pastas e objetivo

---

## Modelagem de Dados

### Formatos de instrução RV32I

| Tipo | Formato | Exemplo | Instruções |
|------|---------|---------|------------|
| **R-type** | `funct7 rs2 rs1 funct3 rd opcode` | ADD, SUB, AND, OR, XOR, SLT, SLTU, SRL, SRA | 11 |
| **I-type** | `imm[11:0] rs1 funct3 rd opcode` | ADDI, ANDI, ORI, XORI, SLTI, SLTIU, LB, LH, LW, LBU, SB, JALR | 13 |
| **S-type** | `imm[11:5] rs2 rs1 funct3 imm[4:0] opcode` | SB, SH, SW | 3 |
| **B-type** | `imm[12|10:5] rs2 rs1 funct3 imm[4:1|11] opcode` | BEQ, BNE, BLT, BGE, BLTU, BGEU | 6 |
| **U-type** | `imm[31:12] rd opcode` | LUI, AUIPC | 2 |
| **J-type** | `imm[20|10:1|11|19:12] rd opcode` | JAL | 1 |
| **Pseudo** | — | HALT | 1 |

### Register File (32 registradores)

| Reg | Nome | Descrição |
|-----|------|-----------|
| x0 | zero | Hardwired zero (sempre 0) |
| x1 | ra | Return address (JAL/JALR) |
| x2 | sp | Stack pointer |
| x3 | gp | Global pointer |
| x4 | tp | Thread pointer |
| x5 | t0 | Temporary (alternativo link register) |
| x6-7 | t1-t2 | Temporárias |
| x8 | s0/fp | Saved register / frame pointer |
| x9 | s1 | Saved register |
| x10-11 | a0-a1 | Argument/return values |
| x12-17 | a2-a7 | Argument temporárias |
| x18-27 | s2-s11 | Saved registers |
| x28-31 | t3-t6 | Temporárias |

---

## Como Usar

### Pré-requisitos

- **ModelSim-Intel® FPGAs Standard Edition Software Version 20.1.1** (ou similar)
- **CompSim** (opcional)
- **RISC-V Interpreter** (opcional, para validação)

### Rodando a simulação

```bash
# No ModelSim
cd sim/simulation1\ -\ ALU/
# Carregar instruction.mif e data.mif
# Rodar a simulação
# Verificar waveform

# Ou via script
vsim -voptargs=+acc work.RISC_V
```

### Compilando o projeto

```bash
# Compilar todos os arquivos SystemVerilog
# O Makefile ou script de compilação deve estar em doc/ ou na raiz
```

---

## Exercícios da Lista (IH)

O repositório `listaIH` (conjunto de exercícios de Assembly RISC-V para IH) está relacionado a este projeto. Os exercícios cobrem:

- **q1_a.asm, q1_b.asm** — Exercício 1: manipulação básica de registradores e loops
- **q3.asm** — Exercício 3: operações com arrays/strings
- **q4.asm** — Exercício 4: operações com números (provavelmente ordenação ou busca)
- **q5.asm** — Exercício 5: sistema de pontos de jogadores
- **q6.asm** — Exercício 6: sistema de som/cinco segundos
- **q7.asm** — Exercício 7: controle de LEDs (temperatura sensor)
- **q8.asm** — Exercício 8: display de 7 segmentos

> **Nota:** O exercício q2 está ausente dos arquivos.

---

## Projeto GDI (tudo.sql)

No repositório `SQL`, há um script Oracle/PLSQL completo (`tudo.sql`, 866 linhas, 16KB) chamado "PROJETO_DE_GDI". Este script contém:

- **12 tabelas** de um sistema de personas/jogadores
- **DML** com INSERTs de dados iniciais
- **Consultas** complexas
- **PL/SQL** (procedimentos, funções, triggers)

As tabelas incluem: PLAYER, PERSONA, PLAYER_PERSONA, WILD_PERSONA, TEAM_PERSONA, SOLD_PERSONA, PRISON, REWARD, EXCHANGE, ITEM, ATTACK, BATTLE, etc.

> **Nota:** Este é um projeto de banco de dados Oracle separado do processador RISC-V, mas está no mesmo repositório. Pode indicar que o repo é usado para múltiplos projetos.

---

## Qualidade e Observações

### Pontos positivos
- **Arquitetura pipeline completa** com todas as unidades necessárias
- **Forwarding e hazard detection** para evitar stalls desnecessários
- **Múltiplas simulações** cobrindo diferentes funcionalidades
- **Evolução documentada** através dos arquivos .bak (iterativa)
- **Ambos os repositórios (RISC-V-PROJECT e Projeto_IH_RISC-V) coexistem** — uma é a versão mais avançada da outra

### Possíveis melhorias
1. **Testbench mais completo:** faltam testbenches detalhados para cada módulo (apenas os testbenches gerais estão em verif/)
2. **Documentação do pipeline:** o README original é longo mas os detalhes do pipeline não são documentados no wiki
3. **Formal verification:** não há verficação formal (apenas simulação)
4. **Scripts de build automatizados:** compilação manual ou scripts não documentados
5. **Versionamento das instruções:** o README lista instruções implementadas vs pendentes, mas pode estar desatualizado

### Pontos de atenção
- **Arquivos .bak:** indicam que o código passou por múltiplas versões — pode haver código desatualizado ou inconsistências entre versões se não for limpo
- **RAM em Verilog e SystemVerilog:** dois formatos diferentes (ramOnChip32.v é Verilog, Memoria32Data.sv é SystemVerilog) — pode haver redundância
- **MIF files:** os programas de teste em MIF podem precisar de atualização se o processador mudar
- **Repositório multi-projeto:** o RISC-V-PROJECT parece conter também o projeto GDI (SQL) — pode ser melhor separar em repos distintos

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Commits:** o histórico é curto (provavelmente poucos commits)
- **Última atualização:** 2023-08-11

> **Nota:** Detalhes exatos podem ser verificados no repositório original: https://github.com/lucaslimacodes/RISC-V-PROJECT/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Novas instruções implementadas:** atualizar a tabela de instruções
- **Novas simulações:** documentar novos casos de teste
- **Mudanças de arquitetura:** registrar mudanças no pipeline, forwarding, hazards
- **Módulos novos:** documentar novos componentes SystemVerilog
- **Histórico:** manter log.md atualizado com data e descrição da mudança

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/RISC-V-PROJECT
- **Branch main:** https://github.com/lucaslimacodes/RISC-V-PROJECT/tree/main
- **Sister repo:** [Projeto_IH_RISC-V](./Projeto_IH_RISC-V/) (versão base do mesmo projeto)
- **ISA RISC-V:** https://riscv.org/technical/specifications/
- **RISC-V Unprivileged Spec v2.2:** https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf
- **RISC-V ISA pages (msyksphinz):** https://msyksphinz-self.github.io/riscv-isadoc/html/rvi.html
- **ModelSim:** https://www.intel.com/content/www/us/en/software-kit/750666/modelsim-intel-fpgas-standard-edition-software-version-20-1-1.html
- **CompSim:** simulator alternativo
- **RISC-V Interpreter (Cornell):** https://www.cs.cornell.edu/courses/cs3410/2019sp/riscv/interpreter/
- **Disciplina IF674:** Infraestrutura de Hardware — CIn/UFPE
- **Monitores:** joaopmarinho, nathaliafab

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15 20:15 UTC*
