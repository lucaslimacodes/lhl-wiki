# Projeto_IH_RISC-V

> Wiki detalhada do repositório **Projeto_IH_RISC-V** — implementação de processador RISC-V pipeline em SystemVerilog para a disciplina IF674 (Infraestrutura de Hardware) do CIn-UFPE.

- **Repositório original:** https://github.com/lucaslimacodes/Projeto_IH_RISC-V
- **Branch principal:** `main`
- **Disciplina:** IF674 — Infraestrutura de Hardware (CIn-UFPE)
- **Linguagem principal:** SystemVerilog
- **Toolchain de simulação:** ModelSim-Intel FPGAs Standard Edition 20.1.1
- **Criado em:** 2023 (projeto base da disciplina)
- **Última atualização do repo:** 2025-09-15 (verificar)
- **Wiki deste repositório:** [repos/Projeto_IH_RISC-V/](./Projeto_IH_RISC-V/) (esta pasta)

---

## Visão Geral

Este repositório contém o **projeto-base** para o trabalho prático da disciplina IF674, onde os alunos implementam um processador RISC-V de 32 bits com arquitetura **pipeline de 5 estágios** (IF, ID, EX, MEM, WB) usando SystemVerilog.

O processador implementado segue o subconjunto **RV32I** da ISA RISC-V, com os seguintes módulos funcionais:

- **5 estágios pipeline** com registradores de buffer (IF/ID, ID/EX, EX/MEM, MEM/WB)
- **Unidade de controle** (Controller) com suporte a R-type, Load (LW), Store (SW) e Branch (BEQ)
- **ALU** com operações AND, ADD e comparação de igualdade (para BEQ)
- **Unidade de forwarding** (ForwardingUnit) para resolução de hazards de dados EX→EX e MEM→EX
- **Unidade de detecção de hazards** (HazardDetection) com stall para dependências de load
- **Unidade de branch** (BranchUnit) com flush de pipeline em branches tomados
- **Banco de registradores** de 32 registros × 32 bits (RegFile)
- **Gerador de imediatos** (imm_Gen) para formatos I-type, S-type e B-type
- **Memória de instruções** e **memória de dados** de 32 bits, com inicialização via arquivo MIF
- **Controlador ALU** (ALUController) que decodifica ALUOp + Funct3/Funct7 em operações da ALU

As instruções **BEQ, LW, SW, ADD e AND** estão implementadas e testadas como funcionando. As demais 21 instruções do RV32I (e a pseudo-instrução HALT) são tarefas a serem implementadas pelos grupos de alunos.

---

## Pipeline do Processador

O processador é organizado em 5 estágios clássicos com **registradores de buffer (Pipe_Buf_Reg_PKG)** entre eles:

```
         ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
  IF ──► │ Register │ ─►│ Register │ ─►│ Register │ ─►│ Register │ ─►│  (WB)    │
         │   A      │   │   B      │   │   C      │   │   D      │   │          │
         │ (IF/ID)  │   │ (ID/EX)  │   │ (EX/MEM) │   │ (MEM/WB) │   │          │
         └──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘
            ▲ stall         ▲ stall         ▲                ▲
            │               │               │                │
         PcSel (flush)   PcSel (flush)
```

### Estágio IF — Busca de Instrução

- O **PC** (tamanho parametrizável, default 9 bits) é incrementado por 4 a cada ciclo (`adder.sv`)
- Um **multiplexador 2×1** (`mux2.sv`) seleciona entre `PC+4` e o endereço de branch (`BrPC`) para o próximo PC
- A **memória de instruções** (`instructionmemory.sv`) retorna a instrução no endereço do PC
- No posedge do clock, a instrução e o PC corrente são carregados no **Register A (IF/ID)**
- **Stall** (da HazardDetection): quando ativo, o PC e o Register A mantêm seus valores (PC não é incrementado, instrução não avança)
- **PcSel/flush** (da BranchUnit): quando ativo, o PC, Register A e Register B são zerados para descartar instruções pré-carregadas antes de um branch tomado

### Estágio ID — Decodificação de Instrução

- A instrução em `Register A.Curr_Instr` é decomposta em:
  - **Opcode** (bits 6:0) → `Controller.sv`
  - **RS1, RS2** (bits 19:15 e 24:20) → endereços de leitura do RegFile
  - **RD** (bits 11:7) → registrador destino
  - **Funct3** (bits 14:12) → modos de operações (ALU, load/store, branch)
  - **Funct7** (bits 31:25) → distingue ADD vs SUB, SLL vs SRL, etc.
  - **Imm** → gerado por `imm_Gen.sv` (extendido com sinal)
- O **RegFile** (`RegFile.sv`) é lido assincronamente (RS1 e RS2 ficam disponíveis em `Reg1`/`Reg2`)
- Todos os sinais de controle e dados são armazenados no **Register B (ID/EX)** no posedge do clock
- A **HazardDetection** compara RS1/RS2 do Register A com o RD do Register B; se houver dependência de load (`MemRead==1` e `rd` coincide), gera um **stall**
- O **Controller** (`Controller.sv`) gera:
  - `ALUSrc`: 0 se operando vem de RS2; 1 se é imediato (LW, SW)
  - `MemtoReg`: 0 se escrita vem da ALU; 1 se vem da memória (LW)
  - `RegWrite`: habilita escrita no RegFile (R-type e LW)
  - `MemRead`: habilita leitura da memória de dados (LW)
  - `MemWrite`: habilita escrita na memória de dados (SW)
  - `ALUOp`: 00=LW/SW (cálculo de endereço), 01=Branch (comparação), 10=R-type (operação da ALU)
  - `Branch`: ativo para BEQ

### Estágio EX — Execução

- Os sinais de controle e dados do **Register B** alimentam:
  - **ForwardingUnit**: compara RS1/RS2 do Register B com RD do Register C (EX/MEM) e RD do Register D (MEM/WB); gera seleção para os multiplexadores FA e FB:
    - `2'b00`: sem forwarding (ler de Register B)
    - `2'b01`: forwarding de MEM/WB (Register D)
    - `2'b10`: forwarding de EX/MEM (Register C)
  - **FAmux (mux4)**: seleciona SrcA para a ALU
    - 0: `B.RD_One` (valor lido do RegFile)
    - 1: `WrmuxSrc` (dados de WB — forwarding MEM/WB)
    - 2: `C.Alu_Result` (ALU result do estágio EX anterior — forwarding EX/MEM)
    - 3: `B.RD_One` (fallback)
  - **FBmux (mux4)**: seleciona SrcB para a ALU (estrutura idêntica)
  - **srcbmux (mux2)**: seleciona entre `FBmux_Result` (registrador) ou `B.ImmG` (imediato extendido) para SrcB, controlado por `B.ALUSrc`
  - **ALU** (`alu.sv`): operações AND (op 0000), ADD (op 0010) e comparação de igualdade (op 1000, usado pelo BEQ)
  - **ALUController** (`ALUController.sv`): decodifica `ALUOp` + `Funct3` + `Funct7` em código de operação de 4 bits para a ALU. Suporta (via lógica combinatória):
    - LW/SW (ALUOp=00): ADD para cálculo de endereço
    - BEQ (ALUOp=01): comparação de igualdade
    - R-type/I-type (ALUOp=10): ADD, OR, SRL, SRA, SLL, SLT, SUB, XOR, AND
    - Reserva ALUOp=11 para JAL/LUI (não implementados)
  - **BranchUnit** (`BranchUnit.sv`): calcula `BrPC = PC + Imm` e `PcSel = Branch && (ALUResult[0] == 1)`. O ALUResult[0] é o flag de igualdade da ALU; quando `Branch_Sel` é verdadeiro, o PC pula para `PC+Imm` e as instruções nos estágios IF e ID são descartadas (flush)
- O Register B também carrega os seguintes campos para os estágios seguintes:
  - `Curr_Pc`, `ImmG`, `func3`, `func7`, `rd`, `RS_One`, `RS_Two`
  - Todos os sinais de controle: `ALUSrc`, `MemtoReg`, `RegWrite`, `MemRead`, `MemWrite`, `ALUOp`, `Branch`

### Estágio MEM — Acesso à Memória

- Os sinais vêm do **Register C (EX/MEM)**:
  - `C.MemRead`, `C.MemWrite`, `C.Alu_Result` (endereço), `C.RD_Two` (dados para store), `C.func3` (tipo de acesso)
- A **datamemory** (`datamemory.sv`) usa um `Memoria32Data` (instância de `Memoria32.sv`) para ler/escrever:
  - Endereço de leitura: `a[8:0]` (9 bits LSB do ALU result)
  - Endereço de escrita: `a[8:2]` (alinhado a palavras para SW) ou `a[8:0]` para os stores
  - Para **LW** (Funct3=010): lê 32 bits da memória → `ReadData`
  - Para **SW** (Funct3=010): escreve 32 bits (`Wr=4'b1111`)
  - Para outros Funct3: comportamento default (escrita de 32 bits)
  - `Wr`, `Datain`, `Dataout` são controlados internamente pelo `datamemory`
- O estágio MEM é responsável também por:
  - Gerar os sinais de saída para o testbench: `wr`, `rd`, `addr`, `wr_data`, `rd_data`
  - Passar `C.Alu_Result`, `C.rd`, `C.RegWrite`, `C.MemtoReg`, `C.MemRead`, `C.MemWrite` para o estágio WB

### Estágio WB — Escrita de Volta

- Os sinais vêm do **Register D (MEM/WB)**:
  - `D.Alu_Result` (resultado da ALU), `D.MemReadData` (dados lidos da memória), `D.rd` (registrador destino), `D.RegWrite`, `D.MemtoReg`
- Um **multiplexador 2×1** (`resmux`, mux2) seleciona:
  - `D.Alu_Result` se `MemtoReg == 0` (R-type, ADD, AND, etc.)
  - `D.MemReadData` se `MemtoReg == 1` (LW, load)
- O resultado final (`WrmuxSrc`) é escrito no **RegFile** via `D.rd` e `D.RegWrite`
- O testbench monitora `reg_write_sig`, `reg_num` e `reg_data` para verificar o conteúdo dos registradores após cada instrução

---

## Arquitetura do Datapath

O diagrama de blocos está em [`doc/PipeLine.png`](doc/PipeLine.png).

### Componentes Principais

#### Module Hierarchy (chamada de `RISC_V.sv` → `Datapath.sv`)

```
riscv (top-level)
├── Controller        ← decodifica opcode → sinais de controle
├── ALUController     ← decodifica ALUOp + Funct3/Funct7 → operação ALU
└── Datapath
    ├── pcadd (adder)          ← PC + 4
    ├── pcmux (mux2)           ← seleção do próximo PC (PC+4 vs BrPC)
    ├── pcreg (flopr)          ← registrador do PC com stall/reset
    ├── instructionmemory      ← Memória de Instruções (Memoria32)
    ├── Register A (IF/ID)    ← buffer manual (always_ff com reset/stall/flush)
    ├── HazardDetection       ← detecta dependência de load (stall)
    ├── RegFile               ← banco de 32 registradores
    ├── imm_Gen               ← extensão de sinal (I-type, S-type, B-type)
    ├── Register B (ID/EX)   ← buffer manual (controle + dados)
    ├── ForwardingUnit        ← forwarding EX/MEM e MEM/WB
    ├── FAmux (mux4)          ← seleção SrcA com forwarding
    ├── FBmux (mux4)          ← seleção SrcB com forwarding
    ├── srcbmux (mux2)        ← seleção entre registrador e imediato
    ├── alu_module (alu)      ← operações AND, ADD, Equal
    ├── BranchUnit            ← cálculo de branch + flush
    ├── Register C (EX/MEM)  ← buffer manual
    ├── datamemory            ← Memória de Dados (Memoria32Data)
    ├── Register D (MEM/WB)  ← buffer manual
    └── resmux (mux2)         ← seleção do dado de escrita (ALU vs Memória)
```

#### Package `Pipe_Buf_Reg_PKG`

Os registradores de pipeline (A, B, C, D) são definidos como structs no package `Pipe_Buf_Reg_PKG` (importado em `Datapath.sv`):

```systemverilog
import Pipe_Buf_Reg_PKG::*;
```

Os tipos `if_id_reg`, `id_ex_reg`, `ex_mem_reg`, `mem_wb_reg` são definidos em algum arquivo do design (provavelmente 하나의 arquivo de package separado). Verificar.

---

## Instruções Implementadas vs. Pendentes

### ✅ Implementadas e Testadas (5 instruções)

| # | Instrução | Tipo | Opcode | Funct3 | Funct7 | Descrição |
|---|-----------|------|--------|--------|--------|-----------|
| 1 | `BEQ` | B-type | `1100011` | `000` | — | Branch se igual (RS1 == RS2) |
| 2 | `LW` | I-type | `0000011` | `010` | — | Load word da memória para registrador |
| 3 | `SW` | S-type | `0100011` | `010` | — | Store word do registrador na memória |
| 4 | `ADD` | R-type | `0110011` | `000` | `0000000` | Adição de dois registradores |
| 5 | `AND` | R-type | `0110011` | `111` | `0000000` | AND bitwise de dois registradores |

**Status:** Implementadas no Controller, ALU e/ou Datapath; testadas via testbench e simulações com resultados corretos.

### ❌ Pendentes de Implementação (21 instruções)

#### Branches adicionais (4)

| # | Instrução | Opcode | Funct3 | Descrição |
|---|-----------|--------|--------|-----------|
| 3 | `BNE` | `1100011` | `001` | Branch se não igual (RS1 != RS2) |
| 4 | `BLT` | `1100011` | `100` | Branch se menor que (RS1 < RS2, signed) |
| 5 | `BGE` | `1100011` | `101` | Branch se maior ou igual (RS1 >= RS2, signed) |
| — | *(BLTU, BGEU — testadas em simulação mas não listadas como pendentes no README)* | `1100011` | `110` / `111` | Branch unsigned |

**O que precisa ser feito:**
- Ampliar o **Controller** para reconhecer os novos funct3 de branch (`001`, `100`, `101`, `110`, `111`)
- Modificar a **ALU** para implementar as comparações de magnitude (SLT, LT, GE) além da igualdade
- Modificar a **BranchUnit** ou a lógica de branch para usar os novos flags da ALU
- Os imediatos B-type já são gerados corretamente por `imm_Gen.sv` para todos os tipos de branch (BEQ, BNE, BLT, BGE, BLTU, BGEU)

#### Load/Store adicionais (6)

| # | Instrução | Tipo | Opcode | Funct3 | Descrição |
|---|-----------|------|--------|--------|-----------|
| 6 | `LB` | I-type | `0000011` | `000` | Load byte (com sinal) |
| 7 | `LH` | I-type | `0000011` | `001` | Load halfword (com sinal) |
| 8 | `LBU` | I-type | `0000011` | `100` | Load byte (sem sinal) |
| 9 | `LHU` | I-type | `0000011` | `101` | Load halfword (sem sinal) |
| 10 | `SB` | S-type | `0100011` | `000` | Store byte |
| 11 | `SH` | S-type | `0100011` | `001` | Store halfword |

**O que precisa ser feito:**
- Modificar o **datamemory** (`datamemory.sv`) para suportar acessos parciais (byte/halfword) com sinal/não-sinal
- O `Funct3` já diferencia LW (010), LB (000), LH (001), LBU (100), LHU (101)
- Para stores, o endereço de escrita no `Memoria32Data` precisa considerar a alinhamento de byte/halfword
- As simulações já existem em `sim/simulation2 - LOAD` (LB, LH, LW, LBU, LHU) e `sim/simulation5 - STORE` (SB, SH, SW) com resultados esperados — servem de referência

#### Instruções da ALU adicionais (11)

| # | Instrução | Tipo | Opcode | Funct3 | Funct7 | Descrição |
|---|-----------|------|--------|--------|--------|-----------|
| 12 | `ADDI` | I-type | `0010011` | `000` | — | Adição com imediato |
| 13 | `SLTI` | I-type | `0010011` | `010` | — | Set less than (signed) com imediato |
| 14 | `SLLI` | I-type | `0010011` | `001` | `0000000` | Shift left logical imediato |
| 15 | `SRLI` | I-type | `0010011` | `101` | `0000000` | Shift right logical imediato |
| 16 | `SRAI` | I-type | `0010011` | `101` | `0100000` | Shift right aritmético imediato |
| 17 | `SUB` | R-type | `0110011` | `000` | `0100000` | Subtração de dois registradores |
| 18 | `SLT` | R-type | `0110011` | `010` | `0000000` | Set less than (signed) |
| 19 | `XOR` | R-type | `0110011` | `100` | `0000000` | XOR bitwise |
| 20 | `OR` | R-type | `0110011` | `110` | `0000000` | OR bitwise |
| 21 | `LUI` | U-type | `0110111` | — | — | Load upper immediate |
| — | `HALT` | pseudo | — | — | — | Parar execução (não é RV32I oficial) |

**O que precisa ser feito:**
- Modificar a **ALU** (`alu.sv`) para adicionar as operações: SUB (SrcA - SrcB), SLT (comparação signed), XOR, OR, SLL, SRL, SRA, e o comportamento de igualdade já existe
- Modificar o **ALUController** (`ALUController.sv`) para mapear os novos funct3/funct7 nas operações da ALU
- O **Controller** (`Controller.sv`) já aceita ALUOp=10 (R-type/I-type), então a dificuldade está na ALUController e na ALU em si
- Para **LUI**: é uma instrução U-type que coloca o imediato nos 20 bits superiores do registrador destino; não usa a ALU tradicionalmente. Precisa modificar a lógica de escrita do WB (ou adicionar uma rota especial) ou fazer a ALU simplesmente passar o imediato
- Para **HALT**: pseudo-instrução; pode ser implementada como uma instrução especial que injeta NOP no pipeline indefinidamente ou aciona um sinal de halt

As simulações das instruções de ALU estão em `sim/simulation1 - ALU` (ADDI, OR, ADD, SLL, SRL, SRA, SLT, SLTU, SLTI, SLTIU, SLLI, SRLI, SRAI, XORI, ORI, ANDI, XOR, SUB, AND, LUI) — os resultados esperados estão documentados e servem de referência para implementação.

---

## Simulações

O diretório `sim/` contém 5 cenários de simulação com arquivos MIF de entrada e README com os resultados esperados:

### simulation1 — ALU (`sim/simulation1 - ALU/`)

**Instruções testadas (ARQUIVO: `instruction.mif`):**
```assembly
addi x0,x0,0
addi x1,x0,8
addi x2,x0,4
or x3,x1,x2
or x4,x2,x0
add x6,x4,x2
addi x4,x0,2
addi x5,x0,-2
sll x18,x1,x4
srl x19,x5,x4
sra x20,x5,x4
slt x21,x1,x2
slt x22,x2,x1
sltu x23,x5,x1
sltu x24,x1,x5
slti x25,x1,8
slti x26,x1,16
addi x5,x0,-4
sltiu x27,x1,-2
sltiu x28,x5,-2
slli x29,x5,1
srli x30,x5,1
srai x31,x5,1
xori x6,x1,10
ori x7,x1,2
andi x8,x1,10
xor x9,x1,x2
```

**Resultados esperados (extrutos do log de simulação):**
```
45: Register [ 0] written with value: [00000000] | [0]
45: Register [ 1] written with value: [00000008] | [8]
55: Register [ 2] written with value: [00000004] | [4]
65: Register [ 3] written with value: [0000000c] | [12]    ← or x3,x1,x2 = 8|4=12
75: Register [ 4] written with value: [00000004] | [4]    ← or x4,x2,x0 = 4
85: Register [ 6] written with value: [00000008] | [8]    ← add x6,x4,x2 = 4+4=8
95: Register [ 4] written with value: [00000002] | [2]
105: Register [ 5] written with value: [fffffffe] | [4294967294]  ← addi x5,x0,-2 (unsigned)
115: Register [18] written with value: [00000020] | [32]   ← sll x18,x1,x4 = 8<<2=32
125: Register [19] written with value: [3fffffff] | [1073741823]  ← srl x19,x5,x4
135: Register [20] written with value: [ffffffff] | [4294967295]  ← sra x20,x5,x4 (arithmetic)
145: Register [21] written with value: [00000000] | [0]    ← slt x21,x1,x2 = (8<4)?0:1 = 0
155: Register [22] written with value: [00000001] | [1]
165: Register [23] written with value: [00000000] | [0]
175: Register [24] written with value: [00000001] | [1]
185: Register [25] written with value: [00000000] | [0]    ← slti x25,x1,8 = (8<8)?0:1 = 0
195: Register [26] written with value: [00000001] | [1]
205: Register [ 5] written with value: [fffffffc] | [4294967292]
215: Register [27] written with value: [00000001] | [1]
225: Register [28] written with value: [00000001] | [1]
235: Register [29] written with value: [fffffff8] | [4294967288]
245: Register [30] written with value: [7ffffffe] | [2147483646]
255: Register [31] written with value: [fffffffe] | [4294967294]
265: Register [ 6] written with value: [00000002] | [2]    ← xori x6,x1,10 = 8^10=2
275: Register [ 7] written with value: [0000000a] | [10]   ← ori x7,x1,2 = 8|2=10
285: Register [ 8] written with value: [00000008] | [8]    ← andi x8,x1,10 = 8&10=8
295: Register [ 9] written with value: [0000000c] | [12]   ← xor x9,x1,x2 = 8^4=12
```

**Sub-grupo (arquivo `luiandsub.mif`): SUB, AND, LUI**
```assembly
addi x1,x0,8
sub x6,x6,x1       ← 0 - 8 = -8 = 0xfffffff8 (4294967288)
and x7,x6,x1       ← -8 & 8 = 8
lui x6,3           ← x6 = 3 << 12 = 0x3000 (12288)
```

### simulation2 — LOAD (`sim/simulation2 - LOAD/`)

**Instruções testadas (ARQUIVO: `instruction.mif`):**
```assembly
addi x7,x0,1        ← x7 = 1 (endereço base)
addi x2,x0,4        ← x2 = 4
or x4,x2,x0         ← x4 = 4
lb x6,0(x7)         ← load byte do endereço 1 (signed) → 0xffffff8f = -113
add x6,x4,x0        ← x6 = 4 + 0 = 8 (sobrescreve)
lb x7,0(x6)         ← load byte do endereço 8 → 0xfffffffb = -45
lh x8,0(x6)         ← load halfword do endereço 8 → 0xffffaafb = -21145
lw x9,0(x6)         ← load word do endereço 8 → 0x0001aafb = 109307
```

**Instruções LBU, LHU (ARQUIVO: `lbulhu.mif`):**
```assembly
addi x7,x0,1
addi x2,x0,4
or x4,x2,x0
lb x6,0(x7)
add x6,x4,x0
lbu x7,0(x6)        ← load byte unsigned: 0x000000fb = 251
lhu x8,0(x6)        ← load halfword unsigned: 0x0000aafb = 43771
```

### simulation3 — AUIPC (`sim/simulation3 - AUIPC/`)

**Instruções testadas (ARQUIVO: `instruction.mif`):**
```assembly
addi x1,x0,8        ← x1 = 8
sub x6,x6,x1        ← x6 = 0 - 8 = -8 = 0xfffffff8
and x7,x6,x1        ← x7 = -8 & 8 = 8
auipc x6,3          ← x6 = PC + (3 << 12); PC=0 → 0 + 12288 = 12288 = 0x3000
                     mas com depuração: PC em 0 → instrução em 16 → PC=16?
                     resultado: 0x0000300c = 12300 (PC Effectivo = 12 = 0xC)
```

> **Nota:** O resultado `0x0000300c` sugere que o PC usado pelo AUIPC está em 12 (0xC), não em 0. Isso pode indicar que o PC já passou por algumas instruções antes da AUIPC, ou que há um offset de 12 no cálculo.

### simulation4 — JAL, BEQ (`sim/simulation4 - JAL, BEQ/`)

**Cenários testados:**

#### BEQ tomado (`beqtaken.mif`)
```assembly
addi x7,x0,1        ← x7 = 1
addi x2,x0,4        ← x2 = 4
jal x10,8           ← x10 = PC+4 = 12 (saltando 2 instruções adiante)
or x4,x2,x0         ← x4 = 4 (executado após jal)
add x6,x4,x2        ← x6 = 4+4 = 8
addi x7,x0,1        ← x7 = 1
addi x8,x0,2        ← x8 = 2
beq x7,x7,-8        ← BEQ tomado: PC = PC+(-8) = retornando ao 'addi x7,x0,1'
```
Resultado: loop infinito exibindo os mesmos valores (x7=1, x8=2) a cada iteração — comprovando que o BEQ tomado está funcionando e o flush/branch está correto.

#### BEQ não tomado (`beqntaken.mif`)
```assembly
beq x8,x7,-8        ← x8=2, x7=1 → não são iguais → branch NÃO tomado
```
Resultado: execução continua normalmente sem salto.

#### BNE tomado (`bnetaken.mif`)
```assembly
bne x8,x7,-8        ← x8=2, x7=1 → são diferentes → branch tomado
```
Resultado: loop infinito com x7=1, x8=2.

#### BLT tomado (`blttaken.mif`)
```assembly
addi x7,x0,2        ← x7 = 2
addi x8,x0,1        ← x8 = 1
blt x8,x7,-8        ← 1 < 2 → branch tomado (loop)
```

#### BGE tomado (`bgetaken.mif`)
```assembly
addi x7,x0,2        ← x7 = 2
addi x8,x0,1        ← x8 = 1
bge x7,x8,-8        ← 2 >= 1 → branch tomado (loop)
```

#### BLTU tomado (`bltutaken.mif`)
```assembly
sub x7,x0,x2        ← x7 = 0 - 4 = -4 = 0xfffffffc (unsigned: 4294967292)
addi x8,x0,1        ← x8 = 1
bltu x8,x7,-8       ← 1 < 4294967292 → branch tomado (loop)
```

#### BGEU não tomado (`bgeuntaken.mif`)
```assembly
sub x7,x0,x2        ← x7 = -4 = 4294967292 (unsigned)
addi x8,x0,7        ← x8 = 7
bgeu x8,x7,-8       ← 7 >= 4294967292? NÃO → branch não tomado
```

#### JALR (`jalr.mif`)
```assembly
addi x7,x0,-1       ← x7 = -1 = 0xffffffff
sw x7,0(x0)         ← mem[0] = 0xffffffff
lw x9,0(x0)         ← x9 = 0xffffffff
or x4,x2,x0         ← x4 = 0
add x6,x4,x2        ← x6 = 0
jalr x12,x0,12      ← x12 = PC+4 = 24; PC = x0 + 12 = 12 (saltando para a instrução em PC=12)
```
Resultado: x12=24 e o PC salta para o endereço 12 (instrução `or x4,x2,x0` no endereço 12).

> **Nota:** No dado de simulação, há repetição de escritas nos registradores. Isso ocorre porque após o JALR pular para o endereço do `or x4,x2,x0`, o pipeline continua executando instruções que já estão em cache no pipeline, gerando escritas repetidas nos registradores.

### simulation5 — STORE (`sim/simulation5 - STORE/`)

**SW (`sw.mif`):**
```assembly
addi x7,x0,-1       ← x7 = -1 = 0xffffffff
sw x7,0(x0)         ← mem[0] = 0xffffffff (write de 32 bits)
lw x9,0(x0)         ← x9 = 0xffffffff
```

**SB, SH (`sb-sh.mif`):**
```assembly
addi x7,x0,0        ← x7 = 0
sb x7,2(x0)         ← mem[2] = 0x00000000 (byte store no offset 2)
lw x9,0(x0)         ← lê word inteiro: upper bytes corrompidos inicialmente
sh x7,2(x0)         ← mem[2:3] = 0x0000 (halfword store)
lw x8,0(x0)         ← lê word: 0x0000aa80 (byte/halfword envolvendo os stores)
```

---

## Como Testar

### Pré-requisitos

- ModelSim-Intel FPGAs Standard Edition 20.1.1 (ou versão compatível)
- Python 3.x (para o assembler)
- Arquivos do design em `/design/`

### Fluxo de Teste

#### 1. Preparar os arquivos MIF

O testbench espera dois arquivos no diretório do projeto:

- **`instruction.mif`** — memória de instruções (inicializada com o programa a ser testado)
- **`data.mif`** — memória de dados (inicializada com os dados iniciais)

#### 2. Gerar `instruction.mif` com o assembler

O script `verif/assembler.py` (criado por Nathalia Barbosa @nathaliafab, 2023-06-16) traduz assembly RISC-V para o formato MIF:

```shell
# 1. Crie um arquivo instructions.txt com as instruções em assembly RISC-V
# 2. Coloque instructions.txt no diretório verif/
# 3. Execute:
python3 assembler.py
```

O script gera `instruction.mif` no mesmo diretório.

**Formatos suportados pelo assembler:**
```
<instrução> <reg>,<reg>,<reg>
<instrução> <reg>,<reg>,<imediato>
<instrução> <reg>,<offset>(<reg>)
<instrução> <reg>,<imediato>
```

**Exemplo de `instructions.txt`:**
```assembly
sub x6,x6,x1
addi x1,x0,8
lw x9,0(x0)
auipc x6,3
```

O script suporta todas as instruções listadas no dicionário `opcode` do `assembler.py` (29 instruções/formatos).

#### 3. Configurar o projeto no ModelSim

1. Crie um novo projeto no ModelSim
2. Adicione **todos os arquivos da pasta `design/`** ao projeto
3. Adicione o testbench `verif/tb_top.sv` ao projeto
4. Garanta que os seguintes arquivos existam no diretório do projeto:
   - `compile_verilog` — script de compilação do ModelSim
   - `runtb_top` — script de execução do testbench
   - `instruction.mif` — gerado pelo assembler
   - `data.mif` — memória de dados de teste

5. Ajuste os caminhos nos scripts conforme necessário

#### 4. Executar a simulação

No terminal do ModelSim:
```shell
do runtb_top
```

O script `runtb_top` compila todos os arquivos do design e do testbench, e então executa a simulação.

#### 5. Interpretar os resultados

O testbench (`tb_top.sv`) monitorea eventos de leitura/escrita de registradores e memória, e imprime no formato:

```
<tempo>: Register [<número_do_reg>] written with value: [<hex>] | [<decimal>]
<tempo>: Memory [<endereço>] read with value: [<hex>] | [<decimal>]
<tempo>: Memory [<endereço>] written with value: [<hex>] | [<decimal>]
```

Comparar os resultados com os logs esperados em cada `sim/simulationN - XXX/README.md`.

---

## Testbench

O testbench `verif/tb_top.sv` é um ambiente de teste auto-contido:

```systemverilog
module tb_top;
  logic tb_clk, reset;
  logic [31:0] tb_WB_Data;
  logic [4:0] reg_num;
  logic [31:0] reg_data;
  logic reg_write_sig;
  logic wr, rd;
  logic [8:0] addr;
  logic [31:0] wr_data, rd_data;

  // Instância do processador
  riscv riscV (
      .clk(tb_clk), .reset(reset),
      .WB_Data(tb_WB_Data), .reg_num(reg_num),
      .reg_data(reg_data), .reg_write_sig(reg_write_sig),
      .wr(wr), .rd(rd), .addr(addr),
      .wr_data(wr_data), .rd_data(rd_data)
  );

  // Clock: período de 10ns (100 MHz)
  localparam CLKPERIOD = 10;
  localparam CLKDELAY = CLKPERIOD / 2;

  initial begin
    tb_clk = 0;
    reset  = 1;
    #(CLKPERIOD);   // reset por 1 ciclo
    reset = 0;
    #(CLKPERIOD * 50);  // simula por 50 ciclos
    $stop;          // pausa a simulação
  end

  // Monitor de registradores
  always_comb begin : REGISTER
    if (reg_write_sig)
      $display($time, ": Register [%d] written with value: [%X] | [%d]\n",
               reg_num, reg_data, reg_data);
  end

  // Monitor de memória
  always_comb begin : MEMORY
    if (wr && ~rd)
      $display($time, ": Memory [%d] written with value: [%X] | [%d]\n",
               addr, wr_data, wr_data);
    else if (rd && ~wr)
      $display($time, ": Memory [%d] read with value: [%X] | [%d]\n",
               addr, rd_data, rd_data);
  end

  // Geração de clock
  always #(CLKDELAY) tb_clk = ~tb_clk;
endmodule
```

**Observações:**
- O reset é sincronizado: ativo por 1 ciclo de clock e depois desativado
- A simulação roda por 50 ciclos de clock (500ns) e então pausa com `$stop`
- O testbench não possui verificação automática (assertions); a análise é manual comparando os logs com os esperados

---

## Memória de Dados (`data.mif`)

O arquivo `verif/data.mif` inicializa a memória de dados com os seguintes valores (somente os primeiros bytes são não-zero):

```
Endereço  | Valor (hex)
----------+---------
0x00      | 00000001  (byte 0 = 1)
0x01      | 00000010  (byte 1 = 2)
0x02      | 00000000
...       | (restante = 0)
```

Isso permite testar loads/store no início da memória com valores conhecidos.

---

## Qualidade e Pontos de Atenção

### Pontos Fortes do Código-Base

- **Pipeline completo com 4 registradores de buffer** — implementação clássica e bem documentada
- **Forwarding de 2 níveis** (EX/MEM e MEM/WB) com unidade dedicada (`ForwardingUnit`)
- **Detecção de hazard de load com stall** — previne dependênciasRAW de load
- **Flush de pipeline em branches tomados** — descarta instruções após branch tomado
- **Código parametrizável** — larguras de PC, dados, endereços são parâmetros, facilitando ajustes
- **Testbench com monitoramento** — registradores e memória são logados automaticamente
- **Assembler Python** — permite testar programas completos sem editar manualmente os arquivos MIF
- **Simulações documentadas** — cada simulação tem README com instruções e resultados esperados
- **Memória de 32 bits reutilizável** — `Memoria32.sv` usado tanto para instruções quanto para dados

### Técnicas e Decisões de Projeto Identificadas

1. **Registradores de pipeline manuais (always_ff)** — ao contrário de usar um module dedicado, os buffers IF/ID, ID/EX, EX/MEM e MEM/WB são implementados como blocos `always @(posedge clk)` diretamente no `Datapath.sv`. Isso facilita a inserção de lógica de stall/flush no mesmo bloco.

2. **Forwarding com mux4** — os multiplexadores FA e FB são do tipo 4×1 (`mux4.sv`) para suportar 4 fontes de dados: RegFile original, WB data, EX/MEM alu result e fallback.

3. **BranchUnit gera PcSel e BrPC separadamente** — enquanto PcSel é o sinal de flush (chega ao PC, Register A e B), BrPC é o endereço alvo que vai para o mux2 do PC. Ambos são gerados pela mesma unidade.

4. **HazardDetection com lógica combinacional simples** — a detecção é: `stall = (ID_EX_MemRead) ? ((ID_EX_rd == IF_ID_RS1) || (ID_EX_rd == IF_ID_RS2)) : 0`. Não há detecção de hazards de forward (que são resolvidos pelo ForwardingUnit) nem de hazards de controle (que são resolvidos pelo flush da BranchUnit).

5. **ALU com case simples** — a ALU implementa apenas 3 operações (AND, ADD, Equal) em um case combinacional de 4 bits. As demais operações são adicionadas expandindo o case e os códigos de operação no ALUController.

6. **Controller com decode de opcode direto** — os opcodes são comparados diretamente no assign: `R_TYPE = 7'b0110011`, `LW = 7'b0000011`, etc.

7. **Memória de dados com byte/halfword não implementados ainda** — o `datamemory.sv` não distingue entre acessos de byte/halfword/word; todos os writes são de 32 bits (`Wr=4'b1111`). Para implementar LB, LH, SB, SH, é necessário modificar este módulo para fazer acesso paracial com extensão de sinal apropriada.

8. **Memoria32 usa 4 bancos de RAM de 8 bits cada** (`ramOnChip32`) com 65536 palavras cada → memória total de 256KB (65536 × 4 bytes = 262.144 bytes). Endereços de 32 bits, mas apenas os 16 bits inferiores são usados para acesso.

9. **Imediato B-type com bit extra** — o `imm_Gen.sv` gera o imediato B-type com 1 bit extra (32 bits no total: `{sign_ext 19bits, inst[31], inst[7], inst[30:25], inst[11:8], 0}`), o que é o padrão RISC-V para B-type.

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Criador do código-base:** Lucas Fernando da Silva Cambuim (lsc@cin.ufpe.br) — autor de `Memoria32.sv` (2018)
- **Assemble r Python:** Nathalia Barbosa (@nathaliafab), 2023-06-16
- **Projeto base:** código inicial da disciplina IF674 fornecido para os alunos implementarem as instruções restantes

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original.

---

## Entrega e Avaliação

### Entrega

- Data definida no Classroom
- Forma: **fork do repositório no GitHub** com o código-fonte modificado + **relatório** (PDF ou Google Docs)
- O relatório deve conter:
  - Nome dos integrantes
  - Link do fork
  - Descrição das escolhas de projeto
  - Descrição dos testes realizados
  - Resultados obtidos
  - Dificuldades encontradas
  - Conclusão

Grupo deve ter **todos os integrantes contribuindo** e podem modificar a implementação como desejarem (incluir fios, alterar tamanhos, modificar sinais, remover/adcionar módulos), desde que o resultado final seja um pipeline funcional com resultados corretos. As decisões de projeto devem ser documentadas.

### Avaliação

- Implementação correta das instruções
- Testes realizados
- Funcionamento adequado do processador
- **Qualidade do código** e do relatório
- O projeto será submetido a casos de teste para verificar o funcionamento

---

## Stack Tecnológica

| Componente | Tecnologia | Observação |
|------------|-----------|------------|
| **Linguagem** | SystemVerilog | IEEE 1800, usada para descrição do hardware |
| **Simulação** | ModelSim-Intel FPGAs Standard Edition | Versão 20.1.1 |
| **Compilação/Execução** | Scripts ModelSim (`compile_verilog`, `runtb_top`) | Compilação + `do runtb_top` |
| **Assembler** | Python 3 | `verif/assembler.py` converter assembly → MIF |
| **Memória** | Arquivos MIF (Memory Initialization File) | Quartus/ModelSim compatible |
| **Disciplina** | IF674 — Infraestrutura de Hardware | CIn-UFPE |
| **ISA** | RISC-V RV32I | Subconjunto de 32 bits, base integer |

---

## Testes Disponíveis

| Simulação | Instruções | Arquivo principal | Status |
|-----------|-----------|-------------------|--------|
| `sim/simulation1 - ALU` | ADDI, OR, ADD, SLL, SRL, SRA, SLT, SLTU, SLTI, SLTIU, SLLI, SRLI, SRAI, XORI, ORI, ANDI, XOR, SUB, AND, LUI | `instruction.mif` + `luiandsub.mif` | ✅ Resultados documentados |
| `sim/simulation2 - LOAD` | LB, LH, LW, LBU, LHU | `instruction.mif` + `lbulhu.mif` | ✅ Resultados documentados |
| `sim/simulation3 - AUIPC` | AUIPC | `instruction.mif` | ✅ Resultados documentados |
| `sim/simulation4 - JAL, BEQ` | JAL, BEQ (taken/not), BNE (taken), BLT (taken), BGE (taken), BLTU (taken), BGEU (not), JALR | múltiplos `.mif` files | ✅ Resultados documentados |
| `sim/simulation5 - STORE` | SW, SB, SH | `sw.mif` + `sb-sh.mif` | ✅ Resultados documentados |

As simulações que **testam instruções não implementadas** (LB, LH, LBU, LHU, SB, SH, BLT, BGE, BLTU, BGEU, BNE, JALR, SLTI, SUB, XOR, OR, LUI, JAL) servem como **referência de resultado esperado** — os alunos devem implementar as instruções e comparar seus resultados com os logs documentados.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Diagramas atualizados:** se o datapath mudar, atualizar `doc/PipeLine.png` e esta página
- **Novas implementações de instruções:** documentar módulos modificados e novos módulos
- **Novas simulações:** adicionar cenários de teste com resultados
- **Mudanças na arquitetura:** registrar alterações no pipeline, forwarding, hazard detection
- **Histórico:** manter log de mudanças com data e descrição

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/Projeto_IH_RISC-V
- **Branch main:** https://github.com/lucaslimacodes/Projeto_IH_RISC-V/tree/main
- **Manual RISC-V ISA Volume I (v2.2):** https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf
- **RISC-V ISA Pages (msyksphinz):** https://msyksphinz-self.github.io/riscv-isadoc/html/rvi.html
- **RISC-V Interpreter (Cornell):** https://www.cs.cornell.edu/courses/cs3410/2019sp/riscv/interpreter/
- **ModelSim Intel FPGAs SE 20.1.1:** https://www.intel.com/content/www/us/en/software-kit/750666/modelsim-intel-fpgas-standard-edition-software-version-20-1-1.html
- **CompSim:** simulateur RISC-V (verificar site oficial do projeto)
- **Contato dos monitores:** [joaopmarinho](https://github.com/joaopmarinho) | [nathaliafab](https://github.com/nathaliafab)

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15 22:46 UTC*
