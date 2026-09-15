# listaIH

> Wiki detalhada do repositório **listaIH** — Resoluções de Assembly RISC-V para a disciplina de Infraestrutura de Hardware (IH)

- **Repositório original:** https://github.com/lucaslimacodes/listaIH
- **Branch principal:** `main` (única branch, commit único `afc41ba`)
- **Linguagem principal:** Assembly (RISC-V)
- **Total de arquivos .asm:** 8
- **Tamanho total do código:** ~36 KB (18.643 bytes)
- **Última atualização do repo:** 2023-07-15
- **Criado em:** julho de 2023
- **Estrelas:** 0 | **Forks:** — | **Issues abertas:** —
- **Wiki deste repositório:** [repos/listaIH/](./listaIH/) (esta pasta)

---

## Visão Geral

Este repositório contém **resoluções em Assembly RISC-V** de uma lista de exercícios da disciplina **Infraestrutura de Hardware (IH)**. Os 8 arquivos cobrem diferentes tópicos da arquitetura RISC-V:

1. **Controle de fluxo e comparações** — `RiscV_q1_a.asm`, `RiscV_q1_b.asm`
2. **Identificação de consoantes em string** — `RiscV_q3.asm`
3. **Jogo de dados (bomberman-style)** — `RiscV_q4.asm`
4. **Leitura numérica de teclado + validação A<B, B<62, C>=15** — `q5.asm`
5. **Tabuada/sensor de temperatura com LEDs** — `q6.asm`
6. **Contador de consoantes + saída decodificada** — `RiscV_q7.asm`
7. **Multiplicação de dois inteiros via teclado** — `RiscV_q8.asm`
8. **Display de 7 segmentos com conversor de string para inteiro** — `RiscV_q7.asm` (nota: número diferente)

Cada arquivo é **autônomo** — não há dependências entre eles — e pode ser simulado individualmente em um simulador RISC-V (MARS, RARS, ou qcrt).

---

## Estrutura do Repositório

```
listaIH/
├── .git/
├── ListaIH/                    # Diretório com os 8 arquivos .asm
│   ├── RiscV_q1_a.asm          # (344 bytes)  Controle de fluxo I
│   ├── RiscV_q1_b.asm          # (1.440 bytes) Leitura de teclado + conversão
│   ├── RiscV_q3.asm            # (1.094 bytes) Conta consoantes em string
│   ├── RiscV_q4.asm            # (3.691 bytes) Jogo de dados (player 1 vs player 2)
│   ├── RiscV_q7.asm            # (1.941 bytes) Sensor de temperatura + LEDs
│   ├── RiscV_q8.asm            # (3.419 bytes) Display 7 segmentos
│   ├── q5.asm                  # (3.688 bytes) Input A B C + validação
│   └── q6.asm                  # (3.026 bytes) Fatorial de número via teclado
├── .gitignore                  # (não verificado)
└── README.md                   # Ausente no repositório original
```

> **Nota:** A numeração das questões não segue uma ordem sequencial (há q1, q3, q4, q5, q6, q7, q8 — q2 parece estar ausente). Isso pode indicar que a questão 2 foi entregue em outro repositório ou não foi incluída nesta lista.

---

## Contexto da Disciplina (IH — Infraestrutura de Hardware)

A disciplina de **Infraestrutura de Hardware** geralmente aborda:

- **Arquitetura de computadores:** organização de CPU, memória, barramentos, I/O
- **Conjunto de instruções (ISA):** RISC-V como ISA moderna, open-source, com instruções de 32 bits (RV32I base)
- **Programação em Assembly:** registradores, formatação de instruções, modos de endereçamento
- **Operações de I/O embarcadas:** leitura de teclado (memory-mapped I/O em endereço `1024`), saída para displays de 7 segmentos (`1027`), LEDs (`1033`, `1034`), sensors (`1031`)
- **Tradução de código C/linguagens de alto nível para Assembly:** algoritmos como multiplicação, divisão, fatorial, conversão string↔inteiro

Os arquivos deste repositório são típicos de trabalhos práticos de IH: simulam interação com periféricos via **memory-mapped I/O** em um ambiente simulado (provavelmente **RARS** — RISC-V Assemby and Runtime Simulator).

---

## Arquitetura RISC-V Utilizada

### Registradores

Os programas usam convenções parciais do RISC-V:

| Registrador | Uso neste repositório |
|-------------|----------------------|
| `x0` | Zero register (hardwired 0) |
| `x1` | Return address (`ra`) — usado para `jalr` de retorno |
| `x2` | Stack pointer (`sp`) — manipulado manualmente para push/pop |
| `x5`–`x10` | Uso genérico (temporários, dados do teclado, pointers) |
| `x11`–`x30` | Variáveis de estado (contadores, flags, resultados) |

> **Observação:** A convenção RISC-V formal usa `x1=ra`, `x2=sp`, `x5–x7=t0–t2`, `x8=a0–a5`, `x9=s0`, etc. Muitos destes programas não seguem rigidamente a calling convention — são programas seqüenciais com controle manual de `x1` como endereço de retorno.

### Memory-Mapped I/O

Os endereços de I/O usados nos programas:

| Endereço | Função | Arquivos que usam |
|----------|--------|-------------------|
| `1024` | Saída de caracteres (display/tela) | Todos |
| `1025` | Entrada do teclado (buffer de leitura) | `RiscV_q1_b`, `RiscV_q3`, `RiscV_q4`, `RiscV_q7`, `RiscV_q8`, `q5`, `q6` |
| `1027` | Display de 7 segmentos (word) | `RiscV_q8` |
| `1028`–`1029` | Controles de display | `RiscV_q8` |
| `1030` | Configuração de periféricos | `RiscV_q7` |
| `1031` | Leitura de sensor (ex: temperatura) | `RiscV_q7` |
| `1033` | LED esquerdo | `RiscV_q7` |
| `1034` | LED direito | `RiscV_q7` |

---

## Análise dos Arquivos

### RiscV_q1_a.asm (344 bytes)

**Descrição:** Lógica condicional com comparações entre variáveis.

**Funcionalidade:**
- Carrega valores de `a`, `b`, `c` (memória) para registradores
- Define valores constantes: `x10=62`, `x11=15`, `x12=1`
- Verifica `a >= 0`, se verdadeiro compara `62 >= b`, se verdadeiro compara `15 < c`
- Se todas as condições passam, altera o valor de `x` para `1`

**Code snippets relevantes:**
```asm
sw x0, x       # x = 0 (inicializa)
lw x5, a       # x5 = a
lw x6, b       # x6 = b
lw x7, c       # x7 = c
addi x10, x0, 62
addi x11, x0, 15
addi x12, x0, 1
bge x5, x0, comp1   # if a >= 0 goto comp1
halt                # else halt
```

### RiscV_q1_b.asm (1.440 bytes)

**Descrição:** Leitura de 3 números de teclado (A, B, C) com conversão de caractere para dígito, validação de faixa e saída de resultado.

**Funcionalidade:**
- Lê caracteres do teclado via `lb x5, 1025(x0)`
- Converte ASCII → dígito subtraindo 48 (`addi x7, x0, 48`)
- Acumula dígito em registrador (`x15`, `x16`, `x17`)
- Após 3 números, valida: `A < 0` → end; `62 < B` → end; `C < 15` → end
- Se válido, adiciona 48 ao acumulador `x14` e printa como caractere em `1024`

**Nota:** Contém funções não chamadas diretamente (`getnum`, `savenum`, `savedez`, `return`, `end`) — parte do código parece ser esqueleto ou tentativa inicial de função de conversão mais completa.

### RiscV_q3.asm (1.094 bytes)

**Descrição:** Conta o número de consoantes em uma string digitada pelo usuário e exibe o resultado em dois dígitos.

**Funcionalidade:**
- Loop `getstring`: lê caracteres do teclado até encontrar um valor < 16 (encerrador)
- Ignora vogais (maiúsculas e minúsculas: A, E, I, O, U) comparando ASCII
- Incrementa contador `x10` para cada consoante
- Quando termina, converte contador para dezena (`x11`) e unidade (`x12`)
- Exibe os dois dígitos em `1024`

**ASCII das vogais verificadas:**
```
Lowercase: 97(a) 101(e) 105(i) 111(o) 117(u)
Uppercase: 65(A) 69(E) 73(I) 79(O) 85(U)
```

### RiscV_q4.asm (3.691 bytes)

**Descrição:** Jogo de dados entre dois jogadores — lê sequências de letras representando faces de dados e soma pontos, depois compara e imprime o vencedor.

**Funcionalidade:**
- Cada letra representa uma face de dado com valor pontual:
  ```
  A, E, I, O, U → 1 ponto (compare1)
  D, G, T → 3 pontos (compare3)
  B, C, M, N, P → 5 pontos (compare5)
  F, H, V, W, Y → 4 pontos (compare4)
  K, R, S → 2 pontos (compare2)
  J, L, X → 6 pontos (compare6)
  Q, Z → 9 pontos (compare9)
  ```
- Lê caracteres do teclado, classifica, acumula pontos
- Imprime placar de Player 1 e Player 2
- Compara e imprime "P1 WINS", "P2 WINS" ou "DRAW"

**Observação:** A função `getpoints` é recursiva (chama a si mesma via `jal x0, getpoints`) — isso é imoral em Assembly sem pilha formal mas funciona na prática pois o retorno é feito com `jalr x0, 0(x1)`.

### RiscV_q7.asm (1.941 bytes)

**Descrição:** Controlador de LEDs baseado em leitura de sensor de temperatura — varia a luz dos LEDs conforme faixas de temperatura.

**Funcionalidade:**
- Lê sensor em `lh x10, 1031(x0)`
- Compara com 5 faixas de temperatura:
  ```
  < 307   → Case 1 (LED esquerdo vermelho)
  < 410   → Case 2 (LED esquerdo amarelo)
  < 512   → Case 3 (LED direito amarelo)
  < 614   → Case 4 (LED esquerdo verde)
  < 717   → Case 5 (LED direito verde)
  >= 717  → Case 6 (LED direito vermelho)
  ```
- Cada case acende o LED correspondente por um ciclo (gambiarra com flags `x16–x21`)
- Loop infinito `loop` verifica temperatura continuamente

**Observação:** Os LEDs são controlados via `sb` nos endereços `1033` (esquerdo) e `1034` (direito). O valor `0` apaga e valores como `3`, `5`, `6`, `9`, `10`, `11` acendem.

### RiscV_q8.asm (3.419 bytes)

**Descrição:** Conversor de string numérica digitada no teclado para inteiro, e exibe o resultado em um display de 7 segmentos.

**Funcionalidade:**
- Lê caracteres do teclado até space (ASCII 32) ou fim
- Converte caracteres para dígitos (subtrai 48)
- Empilha dígitos na pilha (endereço `x2` como stack pointer)
- Função `stoi`: converte string empilhada para inteiro via:
  - `mult`: multiplicação usando shift-and-add
  - Loop acumulando `dígito × 10^posição`
- Após converter, compara com números 0–9 e exibe no display de 7 segmentos via `show0`..`show9`
- Os padrões de 7 segmentos estão em `.byte` com bitmask:
  ```
  a=8, b=9, c=10, d=11, e=12, f=13, g=2
  ex: num0 = b11111111 (all segments on)
  ```

**Detalhes do display 7 segmentos:**
- `num0`–`num9`: padrões binários para cada dígito
- `turnGon`: byte 1 (liga g?)
- `reset_num`: byte 0 (reseta display)

### q5.asm (3.688 bytes)

**Descrição:** Input de dois inteiros separados por espaço, multiplica via shift-and-add, converte resultado para string e imprime.

**Funcionalidade:**
- Lê primeiro número (input1): empilha dígitos até space
- Converte para inteiro (stoi)
- Salva em `x14`
- Lê segundo número (input2) e converte para `x15`
- Chama `mult` para multiplicar `x14 × x15`
- Converte resultado para string (int_to_string) usando divisão
- Imprime string (print_num)

**Algoritmo de multiplicação `mult`:**
- Multiplicação russa (shift-and-add):
  ```
  enquanto multiplier > 0:
    se LSB do multiplier == 1: product += multiplicand
    multiplier >>= 1
    multiplicand <<= 1
  ```

**Algoritmo de divisão `div`:**
- Divisão por subtração sucessiva com shifts (sem instrução `div` no RV32I base)

### q6.asm (3.026 bytes)

**Descrição:** Calcula fatorial de um número digitado no teclado e exibe o resultado.

**Funcionalidade:**
- Lê número como string, converte para inteiro via `stoi`
- Para cada dígito... na verdade, lê o número inteiro e salva em `x24`
- Chama `fact`: multiplicação cumulativa de `n × (n-1) × ... × 1`
- Usa a função `mult` reutilizada do q5
- Converte resultado para string e imprime

**Função `fact`:**
```
fact(n):
  result = 1
  enquanto n > 0:
    result = result * n
    n = n - 1
  return result
```

---

## Como Usar

### Pré-requisitos

1. **Simulador RISC-V** — recomenda-se:
   - [RARS](https://github.com/TheThirdOne/RARS) (RISC-V Assembler and Runtime Simulator) — mais comum para cursos de IH
   - [MARS](http://courses.missouristate.edu/KenVollmar/mars/) — originalmente para MIPS mas alguns usam para RISC-V com adaptação
   - [qemu-riscv](https://www.qemu.org/docs/master/system/riscv/virt.html) — para ambiente completo com periféricos virtuais

### Executando um arquivo

No **RARS**:
1. Abra o arquivo `.asm` no RARS
2. Assemble (`F3` ou botão Assemble)
3. Runtime com syscalls de I/O configurados:
   - Para os programas que usam memory-mapped I/O (`1024`, `1025`, etc.), o RARS precisa das extensões de I/O mapeado — algumas versões simulam isso automaticamente
4. Execute (`F5`) e interaja com o teclado

> **Nota:** Os programas foram desenvolvidos presumivelmente para uma versão específica do simulador usada em aula (possivelmente **RARS** com memory-mapped I/O habilitado, ou uma versão customizada da USP/eschola). A compatibilidade exata pode variar.

### Exemplos de execução

```bash
# RARS — executar RiscV_q1_a.asm
java -jar rars.jar RiscV_q1_a.asm

# RARS — executar com interface gráfica
java -jar rars.jar
# → File → Open → selecione arquivo .asm → Assemble → Run
```

---

## Qualidade e Observações

### Pontos positivos

1. **Cobertura de tópicos variados:** os 8 arquivos cobrem controle de fluxo, manipulação de strings, I/O, algoritmos numéricos (mult, div, fatorial), e interação com periféricos — bom espectro para uma disciplina de IH
2. **Funções reutilizadas:** `mult` (shift-and-add) e `stoi`/`int_to_string` aparecem em múltiplos arquivos com consistente implementação
3. **Comentários em português:** ajudam na compreensão do código, especialmente para o público-alvo (estudantes brasileiros)
4. **Código compacto:** os arquivos são concisos, sem boilerplate excessivo — adequado para exercícios de IH

### Observações técnicas

1. **Falta de calling convention rigorosa:** Vários programas usam `x1` como endereço de retorno manualmente sem seguir o padrão RISC-V de `jal`/`jalr` com `ra`. Isso é aceitável para programas curtos, mas limita reutilização como sub-rotinas em projetos maiores.
2. **Espaço de endereçamento:** Usa endereços absolutos de I/O (`1024`, `1025`, etc.) — codifica suposições do simulador. Código não é portável para HW real sem ajustes.
3. **Ausência de q2:** Não há arquivo para a questão 2 — pode ter sido entregue separadamente ou não incluída.
4. **Recursão `jal x0`:** Em `RiscV_q4.asm`, a função `getpoints` chama a si mesma com `jal x0, getpoints` — isso não preserva o endereço de retorno na pilha, o que significa que o último caractere lido é o único cuja classificação "retorna". Na prática funciona para a entrada esperada, mas é uma técnica frágil.
5. **Sem Makefile ou scripts de build:** Não há forma automatizada de rodar todos os arquivos — execução manual arquivo a arquivo.

### Possíveis melhorias

1. **Documentação por arquivo:** um README.md ou comentários no topo de cada `.asm` com:
   - Número da questão e enunciado resumido
   - Como executar (endereços de I/O usados)
   - Entradas e saídas esperadas
2. **Convenção de registradores:** adotar a calling convention RISC-V formal para facilitar reutilização de funções
3. **Teste automatizado:** um script ou Makefile que roda todos os `.asm` e compara saída esperada
4. **Numeração consistente:** renomear arquivos para `q01`, `q02`, etc. ou documentar a razão da numeração atual

---

## Commits, Branches e Histórico

- **Branch principal:** `main` (única branch)
- **Total de commits:** 1 (`afc41ba — Update q6.asm`)
- **Última atualização:** 2023-07-15

```bash
git log --oneline
# afc41ba Update q6.asm
```

> **Nota:** O repositório parece ter sido criado e populado em uma única operação — não há histórico de desenvolvimento iterativo visível.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**, adaptado para repositórios de exercícios de Assembly. As próximas seções (se adicionadas) devem incluir:

- **Atualizações de código:** se novos arquivos `.asm` forem adicionados ou existentes modificados, documentar as mudanças aqui
- **Novas questões:** registrar o número da questão, tópico e arquivos criados
- **Correções:** se bugs forem encontrados e corrigidos (ex: recursão frágil em q4, validação em q5), documentar
- **Informação do simulador:** registrar versão do RARS/MARS usada e configurações de I/O

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/listaIH
- **Branch main:** https://github.com/lucaslimacodes/listaIH/tree/main
- **RARS (RISC-V simulator):** https://github.com/TheThirdOne/RARS
- **RISC-V Specification:** https://riscv.org/technical/specifications/
- **RISC-V Assembly Programmer's Manual:** https://github.com/riscv/riscv-asm-manual
- **Memory-mapped I/O no RARS:** documentação do simulador para endereços de periféricos
- **Disciplina IH (exemplo):** se houver syllabus público da disciplina, linkar aqui

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15*
