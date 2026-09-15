# ProjetoBank

> Wiki detalhada do repositório **ProjetoBank** — Sistema Bancário Console em Java (projeto fundamental)

- **Repositório original:** https://github.com/lucaslimacodes/ProjetoBank
- **Branch principal:** `main`
- **Linguagem principal:** Java (JDK genérico, sem framework)
- **Tipo:** Aplicação console / CLI
- **Última atualização do repo:** 2026-09-15 (1 commit no histórico)
- **Criado em:** 2026-09-15
- **Estrelas:** 0 | **Forks:** — | **Issues abertas:** —
- **Wiki deste repositório:** [repos/ProjetoBank/](./ProjetoBank/) (esta pasta)

---

## Visão Geral

**ProjetoBank** é um projeto Java fundamental de sistema bancário, executado inteiramente via console (CLI). O programa simula um banco com três tipos de conta — **Conta Corrente**, **Conta Poupança** e **Conta Especial** — e permite ao usuário realizar operações básicas de depósito, saque, transferência entre contas e aplicação de dinheiro.

O código é voltado para fins didáticos: demonstra herança, encapsulamento, interação com o usuário via `Scanner`, e operações matemáticas simples sobre saldos. Não há banco de dados, framework, Web, API REST ou testes automatizados — é um projeto single-file style com múltiplas classes em pacotes.

**Fluxo do programa:**

1. Solicita dados do titular (nome, número da conta) e saldos iniciais das três contas
2. Exibe um menu com 9 opções de operação + saída
3. Cada operação aciona métodos nas classes de conta correspondentes
4. A cada ciclo, a conta poupança rende 10% automáticos (`render()`)
5. O loop continua até o usuário escolher sair (opção 0)

---

## Estrutura do Projeto

```text
ProjetoBank/
├── Banco/
│   └── Banco.java              # Classe principal — main(), menu, fluxo do caixa
├── fucturaBanco/
│   ├── Contas.java             # Classe base (superclasse) — dados comuns das contas
│   ├── ContaCorrente.java      # Conta Corrente — depósito, saque, aplicações
│   ├── ContaPoupança.java      # Conta Poupança — resgate, renderização 10%
│   └── ContaEspecial.java      # Conta Especial — saque com taxa, resgate
└── .git/                       # Repositório Git (1 commit)
```

**Observação sobre os pacotes:** O código usa dois pacotes distintos (`Banco` e `fucturaBanco`). A classe `Banco` (main) importa as classes de conta de `fucturaBanco`. Não há hierarquia de pacotes alinhada à estrutura de domínio — é uma escolha simples para um projeto introdutório.

---

## Stack Tecnológica

| Componente | Tecnologia | Observação |
|------------|-----------|------------|
| **Linguagem** | Java | JDK genérico (sem versão explícita no código) |
| **Tipo** | Aplicação console (CLI) | Sem framework, sem bibliotecas externas |
| **Entrada/Saída** | `java.util.Scanner` | Captura de dados do usuário via stdin |
| **Modelagem** | OO pura (herança, encapsulamento) | `Contas` como superclasse, 3 subclasses |
| **Build** | `javac` direto (não há build tool) | Sem Maven/Gradle |
| **Teste** | Nenhum | Sem testes unitários ou de integração |
| **Persistência** | Nenhuma | Dados são voláteis (memória apenas) |
| **Git** | 1 commit (`4e4ed99 first commit`) | Repositório com histórico mínimo |

### Dependências

Nenhuma. O projeto usa apenas a **JDK standard library** (`java.util.Scanner` e classes base de Java). Não há `pom.xml`, `build.gradle`, ou qualquer arquivo de configuração de build.

---

## Modelagem de Dados / Classes

### Diagrama de Classes (simplificado)

```
                 +-------------------+
                 |     Contas        |  (superclasse / base)
                 +-------------------+
                 | - nome: String    |
                 | - numConta: int   |
                 | - saldo: double   |
                 +-------------------+
                 | + getNome()       |
                 | + setNome()       |
                 | + getNumConta()   |
                 | + setNumConta()   |
                 | + getSaldo()      |
                 | + setSaldo()      |
                 +-------------------+
                        ↑ herança (extends)
        +----------------+----------------+----------------+
        |                |                |                |
   +----------+     +------------+   +--------------+   (nenhuma 4ª subclasse)
   |ContaCorrente|  |ContaPoupança|  |ContaEspecial |
   +----------+     +------------+   +--------------+
   |+ depositar()|  |+ resgatar() |  |+ resgatar()  |
   |+ sacar()    |  |+ dadosDaConta()| |+ sacar()     |
   |+ aplicarPoupança()| |+ mostrarSaldo()| |+ mostrarSaldo()|
   |+ aplicarEspecial()| |+ render()    |  |+ dadosDaConta()|
   |+ dadosDaConta()   | +--------------+  +--------------+
   |+ mostrarSaldo()   |
   +-------------------+
```

> **Nota do diagrama:** A classe `Contas` (note o plural) é a superclasse com os atributos comuns. As três subclasses herdam e adicionam comportamentos específicos. Não há interface, nem classe abstrata, nem polimorfismo explícito no menu — o menu trata cada conta como um tipo fixo.

### Contas (superclasse)

Arquivo: `fucturaBanco/Contas.java` (464 bytes)

> Classe base que agrega os atributos comuns a todas as contas: titular, número da conta e saldo. Segue o princípio de encapsulamento com campos `private` e getters/setters públicos.

**Atributos:**

| Campo | Tipo | Visibilidade | Descrição |
|-------|------|-------------|-----------|
| `nome` | `String` | `private` | Nome do titular da conta |
| `numConta` | `int` | `private` | Número da conta |
| `saldo` | `double` | `private` | Saldo atual da conta |

**Métodos (todos getters/setters padrão):**

| Método | Descrição |
|--------|-----------|
| `getNome()` / `setNome(String)` | Acessa/modifica o nome do titular |
| `getNumConta()` / `setNumConta(int)` | Acessa/modifica o número da conta |
| `getSaldo()` / `setSaldo(double)` | Acessa/modifica o saldo |

**Observação:** A classe `Contas` não tem construtor explícito — o Java fornece o construtor padrão. Não há validação de saldo negativo, nem regras de negócio nesta classe. É puramente um aggregate de dados.

### ContaCorrente

Arquivo: `fucturaBanco/ContaCorrente.java` (1.441 bytes)

> Subclasse de `Contas` que representa a conta corrente. Implementa operações de depósito, saque, e duas operações de "aplicar" (transferir dinheiro para poupança ou conta especial).

**Métodos específicos:**

| Método | Assinatura | Descrição |
|--------|-----------|-----------|
| `depositar` | `void depositar(double valor)` | Soma `valor` ao saldo e imprime confirmação |
| `sacar` | `void sacar(double valor)` | Verifica saldo; se insuficiente, imprime "saldo insuficiente"; caso contrário, subtrai e confirma |
| `aplicarPoupança` | `void aplicarPoupança(double valor, ContaPoupança cp)` | Verifica saldo da corrente; se OK, subtrai da corrente e soma na poupança (transferência interna) |
| `aplicarEspecial` | `void aplicarEspecial(double valor, ContaEspecial ce)` | Mesma lógica, mas destina à conta especial |
| `dadosDaConta` | `void dadosDaConta()` | Imprime um bloco com nome, número da conta e saldo formatado |
| `mostrarSaldo` | `void mostrarSaldo()` | Imprime o saldo atual da conta corrente |

### ContaPoupança

Arquivo: `fucturaBanco/ContaPoupança.java` (851 bytes)

> Subclasse de `Contas` que representa a conta poupança. A característica distintiva é o método `render()` que aplica rendimento de 10% sobre o saldo atual — e é chamado a cada ciclo do menu no `main()`.

**Métodos específicos:**

| Método | Assinatura | Descrição |
|--------|-----------|-----------|
| `resgatar` | `void resgatar(double valor, ContaCorrente cc)` | Verifica saldo; se OK, subtrai da poupança e soma na conta corrente (transferência inversa) |
| `dadosDaConta` | `void dadosDaConta()` | Imprime bloco com titular, número da conta (suffix "-1") e saldo |
| `mostrarSaldo` | `void mostrarSaldo()` | Imprime o saldo da poupança |
| `render` | `void render()` | Aplica 10% de rendimento: `setSaldo(getSaldo() * 1.1)` — chamado a cada iteração do menu |

**Observação sobre `render()`:** O rendimento de 10% é aplicado incondicionalmente a cada ciclo do menu, independentemente de tempo ou depósito. Isso é uma simplificação didática, não uma simulação real de rendeamento de poupança.

### ContaEspecial

Arquivo: `fucturaBanco/ContaEspecial.java` (1.054 bytes)

> Subclasse de `Contas` que representa uma conta especial com taxa de saque fixa de R$ 4,50. Suporta resgate (transferência para conta corrente) e saque com taxa.

**Métodos específicos:**

| Método | Assinatura | Descrição |
|--------|-----------|-----------|
| `resgatar` | `void resgatar(double valor, ContaCorrente cc)` | Verifica saldo; se OK, subtrai da conta especial e soma na corrente |
| `sacar` | `void sacar(double valor)` | Verifica se `saldo >= valor + 4.50`; se OK, subtrai `valor + 4.50` (taxa fixa) e confirma. Mensagem explícita sobre a taxa. |
| `mostrarSaldo` | `void mostrarSaldo()` | Imprime o saldo da conta especial |
| `dadosDaConta` | `void dadosDaConta()` | Imprime bloco com titular, número da conta (suffix "-2") e saldo |

**Observação sobre a taxa:** A taxa de R$ 4,50 é fixa, hardcoded no método `sacar()`. Não há configuração, nem parametrização. É uma característica definida na lógica da classe.

### Banco (classe principal)

Arquivo: `Banco/Banco.java` (4.323 bytes)

> Classe com o método `main()` que dá início ao programa. Responsável por: captura de dados iniciais, criação das 3 instâncias de conta, e o loop principal do menu de operações.

**Fluxo detalhado do `main()`:**

1. **Captura de dados iniciais:**
   - Nome do titular (`input.nextLine()`)
   - Número da conta (`input.nextInt()`)
   - Saldo inicial da conta corrente (`input.nextDouble()`)
   - Saldo inicial da poupança (`input.nextDouble()`)
   - Saldo inicial da conta especial (`input.nextDouble()`)

2. **Instanciação e população:**
   - Cria `ContaCorrente cc`, `ContaPoupança cp`, `ContaEspecial ce`
   - Define nome, número da conta e saldo em cada uma

3. **Loop principal (`while(n != 0)`):**
   - A cada iteração: chama `cp.render()` (rende 10% na poupança)
   - Exibe menu de 9 opções + saída
   - Captura opção (`input.nextInt()`)
   - Valida: se `n > 9 || n < 0`, pede nova opção
   - Executa a operação escolhida (veja tabela abaixo)
   - Após a operação: pergunta se deseja repetir (qualquer valor = SIM, 0 = NÃO)
   - Se 0, encerra

**Operações do menu:**

| Opção | Método acionado | Descrição |
|-------|----------------|-----------|
| 1 | `cc.sacar(valor)` | Sacar da conta corrente |
| 2 | `ce.sacar(valor)` | Sacar da conta especial (com taxa R$ 4,50) |
| 3 | `cc.depositar(valor)` | Depositar na conta corrente |
| 4 | `cp.resgatar(valor, cc)` | Resgatar da poupança (transferir para corrente) |
| 5 | `ce.resgatar(valor, cc)` | Resgatar da conta especial (transferir para corrente) |
| 6 | `cc.aplicarPoupança(valor, cp)` | Aplicar na poupança (transferir da corrente para poupança) |
| 7 | `cc.aplicarEspecial(valor, ce)` | Aplicar na conta especial (transferir da corrente para especial) |
| 8 | `cc.mostrarSaldo()` + `cp.mostrarSaldo()` + `ce.mostrarSaldo()` | Mostrar saldos das 3 contas |
| 9 | `cc.dadosDaConta()` + `cp.dadosDaConta()` + `ce.dadosDaConta()` | Mostrar dados completos das 3 contas |
| 0 | — | Sair |

**Observações sobre o main:**

- Não há tratamento de exceção para entrada inválida (ex: texto onde se espera número). Um `InputMismatchException` do Scanner encerraria o programa abruptamente.
- O menu valida apenas o intervalo 0-9; não valida tipos de entrada.
- As contas compartilham o mesmo nome e número da conta — não há separação de titulares.

---

## Fluxo de Execução (exemplo)

```
Bem-vindo ao banco, vamos começar com o cadastramento dos dados da conta corrente, poupança e conta especial
digite seu nome:
> Lucas
digite o número da sua conta:
> 1234
digite seu saldo inicial da conta corrente:
> 1000
digite seu saldo inicial da poupança:
> 5000
digite seu saldo inicial da conta especial
> 2000

cadastramento realizado com sucesso, agora digite a sua ação:
 a cada ação, a conta poupança rende 10%
+---------------- Banco ações -------------+
|   (1) sacar da conta corrente      |
|   (2) sacar da conta especial      |
|   (3) depositar na conta corrente  |
|   (4) resgatar pela poupança       |
|   (5) resgatar pela conta especial |
|   (6) aplicar na poupança          |
|   (7) aplicar na conta especial    |
|   (8) mostrar saldos               |
|   (9) mostrar dados das 3 contas   |
|   (0) sair                         |
+------------------------------------+
> 3
você escolheu depositar na conta corrente. Escolha o valor do depósito:
> 500
valor depositado com sucesso

gostaria de repetir o processo?
(qualquer valor) SIM       (0) NÃO
> 0
operação finalizada com sucesso
```

---

## Comentários de Qualidade e Observações

### Pontos positivos

- **Didática clara:** ótimo exemplo introdutório de OO em Java — herança, encapsulamento, metode específicos por subclasse
- **Código autoconectado:** todas as classes estão no mesmo pacote ou intercomunicando-se diretamente, sem dependências externas
- **Lógica de taxa:** a conta especial demonstra como adicionar uma regra de negócio (taxa fixa) na subclasse
- **Render automático:** a poupança rende a cada ciclo — ilustra o conceito de efeitos colaterais periódicos em um loop

### Possíveis melhorias / debt

1. **Classe base com lógica:** `Contas` é apenas um container de dados. Uma classe abstrata ou interface poderia definir contratos comuns (ex: `sacar()`, `depositar()` polimórficos)
2. **Tratamento de entrada:** nenhum `try/catch` para `InputMismatchException` — entrada inválida crasha o programa
3. **Validação de saldo:** não há verificação de saldo negativo no construtor ou nos setters
4. **Número da conta:** as 3 contas compartilham o mesmo `numConta` — em um sistema real, cada conta teria identificador único
5. **Hardcoded:** taxa de R$ 4,50 fixa no código; rendimento de 10% fixo no `render()`
6. **Sem separação de responsabilidades:** a classe `Banco` faz tudo — UI, fluxo, criação de objetos. Em um projeto maior, o menu e a lógica de entrada seriam separados do domínio
7. **Sem persistência:** dados são voláteis — ao encerrar, tudo se perde
8. **Sem testes:** zero cobertura de teste
9. **Package naming:** `fucturaBanco` parece ser um erro de digitação (talvez "estructuraBanco" ou "banco"), e `Banco` usado tanto para o pacote quanto para a classe principal pode causar confusão

### Observações técnicas

- O programa não é internationalizado — todos os textos são em português
- Não há logging ou feedback além de `System.out.println`
- O `Scanner` não é fechado no final de cada operação — apenas no final do `main()` com `input.close()`

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Commits:** 1 commit apenas (`4e4ed99 first commit`)
- **Origem remoto:** `git@github.com:lucaslimacodes/ProjetoBank.git`
- **Histórico:** projeto enviado em um único commit inicial — sem evolução registrável no Git

> **Nota:** O histórico do projeto é mínimo. Não há branches, pull requests, ou evolução de código registrável no repositório.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Evolução do código:** se o projeto ganhar novos recursos, registrar mudanças nas classes e no fluxo
- **Novas classes:** documentar no diagrama de classes e nas respectivas seções
- **Melhorias de qualidade:** registrar refactorings, adição de testes, ou mudanças de modelo
- **Histórico:** manter log.md atualizado com data e descrição da mudança

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/ProjetoBank
- **Branch main:** https://github.com/lucaslimacodes/ProjetoBank/tree/main
- **Java Documentation (Oracle):** https://docs.oracle.com/javase/
- **Scanner Java:** https://docs.oracle.com/javase/8/docs/api/java/util/Scanner.html

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15*
