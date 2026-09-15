# AFD-AFN_impl

> Wiki detalhada do repositório **AFD-AFN_impl** — Implementação de Autômatos Finitos Determinísticos e Não-Determinísticos em Java (Informática Teórica)

- **Repositório original:** https://github.com/lucaslimacodes/AFD-AFN_impl
- **Linguagem principal:** Java (sem build system formal — estrutura didática)
- **Código-fonte:** `/tmp/repos-inspecao/AFD-AFN_impl/`
- **Criado em:** data não informada
- **Wiki deste repositório:** [repos/AFD-AFN_impl/](./AFD-AFN_impl/) (esta página)

---

## Visão Geral

Este projeto é uma implementação didática de **Autômatos Finitos** em Java, desenvolvida como parte dos estudos de **Informática Teórica**. O objetivo é codificar os conceitos de:

1. **Autômato Finito Determinístico (AFD/DFA)** — para cada estado e símbolo de entrada, existe exatamente uma transição definida.
2. **Autômato Finito Não-Determinístico (AFN/NFA)** — permite múltiplas transições para o mesmo símbolo e **transições epsilon (ε)**, que mudam de estado sem consumir entrada.

A implementação segue uma estrutura de classes com herança: uma classe abstrata `Automata` define o contrato comum, e `AFD` + `AFN` herdam dela com comportamentos específicos de execução.

---

## Estrutura do Projeto

```
AFD-AFN_impl/
├── README.md                          # README original (breve, com tabela de features)
└── src/
    ├── main/
    │   └── java/
    │       ├── Automata/
    │       │   └── Automata.java      # Classe abstrata base (contrato comum)
    │       ├── AFD/
    │       │   └── AFD.java           # Autômato Finito Determinístico
    │       ├── AFN/
    │       │   └── AFN.java           # Autômato Finito Não-Determinístico (+ ε)
    │       ├── State/
    │       │   └── State.java         # Estado do autômato (ID + transições)
    │       ├── Util/
    │       │   └── Pair.java          # Classe utilitária genérica Pair<T1,T2>
    │       └── Main.java              # Exemplo de uso e demonstração
    └── test/
        └── java/
            ├── AFD/
            │   └── AFDTest.java       # Testes JUnit para AFD
            └── State/
                └── StateTest.java     # Testes JUnit para State
```

### Dependências

O projeto **não possui `pom.xml` ou `build.gradle`** — é uma estrutura pura de código-fonte Java. Os testes usam **JUnit 5** (Jupiter), mas não há configuração de build formal documentada. Para compilar e rodar manualmente:

```bash
# Compilar todos os arquivos
javac -d out src/main/java/**/*.java src/test/java/**/*.java

# Executar Main
java -cp out:main.java Main

# Executar testes (requer JUnit na classpath)
java -cp out:junit-platform-console-standalone.jar org.junit.platform.console.ConsoleLauncher
```

---

## Conceitos Teóricos

### Autômato Finito Determinístico (AFD)

Um AFD é uma 5-tuple **(Q, Σ, δ, q₀, F)** onde:

| Componente | Significado | No código |
|------------|-------------|-----------|
| **Q** | Conjunto finito de estados | `allStates[]` (String[]) |
| **Σ** | Alfabeto de entrada (símbolos) | `alphabet[]` (char[]) |
| **δ** | Função de transição Q × Σ → Q | `State.transitions` (Pair<Character, String>) |
| **q₀** | Estado inicial | `initialState` (String) |
| **F** | Conjunto de estados de aceitação | `acceptanceStates[]` (String[]) |

**Propriedade determinística:** Para cada estado `q ∈ Q` e cada símbolo `a ∈ Σ`, existe **exatamente uma** transição δ(q, a). O AFD não permite transições ε.

**Execução:** Dada uma palavra `w = w₁w₂...wₙ`, o AFD parte de q₀, aplica δ seqüencialmente para cada símbolo, e aceita se o estado final estiver em F.

### Autômato Finito Não-Determinístico (AFN)

Um AFN é também uma 5-tuple **(Q, Σ, δ, q₀, F)**, mas com:

- **δ: Q × (Σ ∪ {ε}) → P(Q)** — a função de transição mapeia para um **conjunto** de estados possíveis.
- **Transições ε:** Permitem mudar de estado sem consumir símbolo de entrada (representadas por `'$'` no código).

**Acceptance:** Uma palavra é aceita se **existe pelo menos um caminho** de q₀ a um estado de aceitação que consome a palavra inteira.

### Equivalência AFD ↔ AFN

Teorema fundamental: para todo AFN existe um AFD equivalente (construção dos subconjuntos / subset construction). A implementação atual não inclui essa conversão — é uma funcionalidade futura marcada como `:x:` no README original.

---

## Modelagem de Classes

### Hierarquia

```
Automata (abstract)
├── AFD    — currentState (String simples), feed linear, accepts por único caminho
└── AFN    — currentState (ArrayList<String>), epsilon closure recursivo, accepts por ∃cammino
```

### Automata (classe abstrata base)

**Pacote:** `main.java.Automata`

**Responsabilidade:** Define o contrato comum de todo autômato — alfabeto, estados, estados de aceitação, estado inicial, e métodos utilitários.

**Atributos:**
| Atributo | Tipo | Visibilidade | Descrição |
|----------|------|--------------|-----------|
| `alphabet` | `char[]` | private | Símbolos de entrada aceitos |
| `allStates` | `String[]` | private | Identificadores de todos os estados |
| `acceptanceStates` | `String[]` | private | Subconjunto de estados que aceitam |
| `initialState` | `String` | private | Estado inicial q₀ |
| `states` | `ArrayList<State>` | private | Coleção de objetos State instanciados |

**Construtor:**
```java
public Automata(char[] alphabet, String[] allStates,
                String[] acceptanceStates, String initialState)
```
Instancia um `State` para cada identificador em `allStates` e os adiciona à lista `states`.

**Métodos concretos (herdados por AFD/AFN):**
| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `getStateByID` | `protected State getStateByID(String stateID)` | Busca um State pela ID na lista interna; retorna `null` se não encontrado |
| `isAcceptanceState` | `protected boolean isAcceptanceState(String stateID)` | Verifica se o ID pertence ao array `acceptanceStates` |
| `addTransition` | `public void addTransition(String begin, String end, char symbol)` | Delega a `beginState.addTransition(symbol, end)` |
| `getAlphabet` / `setAlphabet` | getter/setter | Acesso ao alfabeto |
| `getAcceptanceStates` | getter | Retorna o array de estados de aceitação |
| `getInitialState` / `setInitialState` | getter/setter | Acesso ao estado inicial |

**Métodos abstratos (implementados por subclasses):**
| Método | Assinatura | Responsabilidade da subclasse |
|--------|------------|-------------------------------|
| `accepts` | `public abstract boolean accepts(String word)` | Algoritmo de aceitação específico |
| `reset` | `protected abstract void reset()` | Restaurar estado inicial após execução |
| `feed` | `protected abstract void feed(char symbol)` | Processar um único símbolo de entrada |

---

### State

**Pacote:** `main.java.State`

**Responsabilidade:** Representa um estado individual do autômato, com seu identificador e lista de transições outgoing.

**Atributos:**
| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `stateID` | `String` | Identificador único do estado |
| `transitions` | `ArrayList<Pair<Character, String>>` | Lista de transições: cada Pair<char, String> mapeia símbolo → estado destino |

**Construtores:**
```java
public State(String stateID)
public State(String stateID, ArrayList<Pair<Character, String>> transitions)
```

**Métodos:**
| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `addTransition` | `public void addTransition(char symbol, String endState)` | Adiciona uma transição (symbol → endState) à lista |
| `getStateID` / `setStateID` | getter/setter | Acesso ao ID |
| `getTransitions` / `setTransitions` | getter/setter | Acesso à lista de transições |

**Observação técnica:** As transições são armazenadas como `ArrayList` — não há índice ou hash por símbolo. A busca é linear (`O(n)` por estado), o que é aceitável para autômatos pequenos mas ineficiente para Alfabetos grandes.

---

### Pair<T1, T2>

**Pacote:** `main.java.Util`

**Responsabilidade:** Classe utilitária genérica que armazena dois valores de tipos possivelmente diferentes.

**Atributos:**
| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `first` | `T1` | Primeiro valor (no contexto do autômato: o símbolo de transição `char`) |
| `second` | `T2` | Segundo valor (no contexto: o ID do estado destino `String`) |

**Construtor:**
```java
public Pair(T1 first, T2 second)
```

**Métodos:** `getFirst()`, `setFirst()`, `getSecond()`, `setSecond()` — acesso simples com getters/setters.

**Observação técnica:** Esta é uma implementação manual de um conceito equivalente a `AbstractMap.SimpleEntry<K,V>` ou Java's built-in record (Java 14+). A escolha por classe customizada pode refletir uma versão Java anterior a records, ou preferência didática.

---

### AFD (Autômato Finito Determinístico)

**Pacote:** `main.java.AFD`

**Herda:** `Automata`

**Responsabilidade:** Implementação determinística. Para cada símbolo, segue exatamente uma transição.

**Atributos adicionais:**
| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `currState` | `String` | Estado atual durante a execução (single state) |

**Construtor:**
```java
public AFD(char[] alphabet, String[] allStates,
           String[] acceptanceStates, String initialState)
```
Chama `super(...)` e inicializa `currState = initialState`.

**Métodos:**
| Método | Implementação | Detalhes |
|--------|---------------|----------|
| `feed(char symbol)` | Itera sobre `currState.getTransitions()`, encontra o Pair cujo `first == symbol`, e atualiza `this.currState = transition.getSecond()`. **Break** após primeira correspondência (comportamento determinístico). | Complexidade: O(n_transitions) por símbolo |
| `reset()` | `this.currState = getInitialState()` | Restaura ao estado inicial |
| `accepts(String word)` | Para cada char de word: `feed(symbol)`. Salva `finalState = currState`. Chama `reset()`. Retorna `isAcceptanceState(finalState)`. | Reseta após cada aceitação para manter o autômato reutilizável |

**Fluxo de accepts (AFD):**
```
word = "010"
currState = q0
  feed('0') → currState = q1
  feed('1') → currState = q2
  feed('0') → currState = q1
finalState = q1
reset() → currState = q0
return isAcceptanceState("q1")  // true se q1 ∈ F
```

---

### AFN (Autômato Finito Não-Determinístico)

**Pacote:** `main.java.AFN`

**Herda:** `Automata`

**Responsabilidade:** Implementação não-determinística com suporte a transições epsilon (ε).

**Constantes:**
```java
public static Character EPSILON = '$'
```
O caractere `'$'` é usado como símbolo especial para transições epsilon — não consome entrada.

**Atributos adicionais:**
| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `currState` | `ArrayList<String>` | Conjunto de estados atuais (múltiplos estados simultâneos) |

**Construtor:**
```java
public AFN(char[] alphabet, String[] allStates,
           String[] acceptanceState, String initialState)
```
Chama `super(...)`, inicializa `currState` com `ArrayList` contendo apenas o estado inicial.

**Métodos:**

#### `allStatesFromEpsilon(String beginState)` — Fecho Epsilon (ε-closure)
```java
private ArrayList<String> allStatesFromEpsilon(String beginState)
```
**Algoritmo recursivo:** Dado um estado, encontra todas as transições ε saindo dele, adiciona os destinos, e recursa para cada destino. Retorna o conjunto de todos os estados alcançáveis via ε a partir de `beginState`.

**Pseudocódigo:**
```
allStatesFromEpsilon(q):
  ret = []
  for each transition (ε, q') de q:
    ret.add(q')
    ret.addAll(allStatesFromEpsilon(q'))   // recursão
  return ret
```

**Observação importante:** Não há detecção de ciclos ε. Se o autômato tiver um ciclo de transições ε (ex: q1 →ε q2 →ε q1), este método entra em recursão infinita (StackOverflow). Esta é uma limitação conhecida da implementação.

#### `feed(char symbol)` — Transição de símbolo
```java
protected void feed(char symbol)
```
Para cada estado `s` no conjunto `currState`:
1. Busca as transições de `s`
2. Se found uma transição com `symbol`, adiciona o destino `newStates`
3. Adiciona também o **fecho epsilon** do destino (`allStatesFromEpsilon`)
4. Substitui `currState` pelo novo conjunto

**Pseudocódigo:**
```
feed(symbol):
  newStates = []
  for s in currState:
    for (symbol, dest) in transitions[s]:
      if symbol == symbol:
        newStates.add(dest)
        newStates.addAll(allStatesFromEpsilon(dest))
  currState = newStates
```

#### `reset()`
```java
protected void reset()
```
Limpa `currState` e re-adiciona apenas o estado inicial.

#### `accepts(String word)` — Aceitação
```java
public boolean accepts(String word)
```
1. Calcula o fecho epsilon do primeiro estado em `currState` e adiciona ao conjunto
2. Para cada char de word: `feed(c)`
3. Verifica se **qualquer** estado em `currState` é de aceitação
4. Retorna `true` se existir pelo menos um — comportamento não-determinístico

**Fluxo de accepts (AFN):**
```
word = "00001110"
currState = [q0]
  ε-closure(currState[0]) → currState = [q0, q1, ...]  // adiciona alcançáveis por ε
  feed('0') → newStates = [...]
  feed('0') → ...
  ...
  feed('0') → currState = [q3]  // ou conjunto com q3
  → isAcceptanceState("q3") = true → ACCEPT
```

---

### Main.java — Exemplo de Uso

**Pacote:** `main.java`

Cria um AFN com:
- **Alfabeto:** `{0, 1}`
- **Estados:** `{"1", "2", "3"}`
- **Estado inicial:** `"1"`
- **Estados de aceitação:** `{"3"}`
- **Transições:**
  - `1 --0--> 1` (loop sobre 0)
  - `1 --ε--> 2` (transição epsilon)
  - `2 --1--> 2` (loop sobre 1)
  - `2 --0--> 3` (transição para estado de aceitação)
  - `3 --0--> 3` (loop no estado final)

**Teste:** `afn.accepts("00001110")` — imprime o resultado no console.

**AFD comentado:** O código contém um bloco comentado demonstrando uso do AFD com estados `q1`/`q2`, alfabeto `{0,1}`, e aceitação de palavras com pelo menos um `0` (afd.accepts("0101100111111111011")). Este é um exemplo de como o AFD seria usado.

---

## Testes

### AFDTest.java

**Pacote:** `test.java.AFD`

**Framework:** JUnit 5 (Jupiter)

**Cenário testado:** AFD com 2 estados (`q1`, `q2`), alfabeto `{0,1}`, estado inicial `q1`, aceitação em `q2`. Transições:
- `q1 --1--> q1` (loop)
- `q1 --0--> q2` (transição para aceitação)
- `q2 --1--> q2` (loop no aceitável)
- `q2 --0--> q1` (volta ao inicial)

**Casos de teste:**
| Entrada | Resultado esperado | Razão |
|---------|-------------------|-------|
| `"0"` | `assertTrue` (aceita) | q1→q2 via 0, q2∈F |
| `"111110"` | `assertTrue` (aceita) | q1 stays q1 via 1s, then →q2 via 0 |
| `"0001100111111"` | `assertTrue` (aceita) | Complexa mas termina em q2 |
| `""` (vazio) | `assertFalse` (rejeita) | q1∉F, sem símbolos Processados |
| `"1"` | `assertFalse` (rejeita) | q1→q1 via 1, q1∉F |
| `"1001"` | `assertFalse` (rejeita) | q1→q1→q2→q1, termina em q1∉F |

### StateTest.java

**Pacote:** `test.java.State`

**Cenário testado:** Cria um State com ID `"oi"`, adiciona transição `(char 's', String "end")`, verifica os valores do Pair.

**Asserções:**
- `state.getTransitions().getFirst().getFirst()` == `'s'`
- `state.getTransitions().getFirst().getSecond()` == `"end"`

---

## Qualidade e Observações

### Pontos fortes
- **Separção clara de responsabilidades:** `Automata` (contrato), `State` (estado), `Pair` (utilidade), `AFD`/`AFN` (algoritmos)
- **Herança bem utilizada:** AFD e AFN compartilham infraestrutura via `Automata`
- **AFN com ε-closure:** Implementação de fecho epsilon com recursão — conceito teórico correto
- **Reset pós-accepts:** Ambos os autômatos resetam o estado após `accepts()`, permitindo reutilização
- **Testes unitários existentes:** AFDTest e StateTest cobrem funcionalidades básicas
- **Código didático e legível:** Nomes de métodos e variáveis claros, pouco acoplamento

### Limitações e dívida técnica

1. **Sem build system formal** — Não há `pom.xml`, `build.gradle` ou similar. Compilação e execução dependem de `javac` manual ou configuração externa.

2. **Sem tratamento de exceções** — Marcado como `:x:` no README original. Métodos como `getStateByID` retornam `null` sem lançar exceção; `feed` em AFD pode falhar silenciosamente se o símbolo não tiver transição definida (o `currState` não muda, o que pode ser comportamento inesperado).

3. **Ciclos ε não detectados** — `allStatesFromEpsilon` é recursivo puro sem memoization ou tracking de estados visitados. Autômatos com ciclos ε causam StackOverflow.

4. **Busca linear de transições** — `State.transitions` é `ArrayList`; busca por símbolo é O(n). Para alfabetos grandes ou autômatos com muitas transições, um `Map<Character, String>` seria mais eficiente.

5. **AFD sem transição indefinida** — Se `feed(char)` não encontra transição para o símbolo atual, o `currState` não é atualizado (comportamento indefinido). Uma lançada de exceção ou transição para um "estado sink" (dead state) seria mais correto.

6. **AFDTest não cobre caso de transição faltando** — Os testes assumem que todas as transições necessárias estão definidas.

7. **Nenhum teste para AFN** — Apenas AFD e State têm testes. O AFN, que é a parte mais complexa (ε-closure recursivo), não possui suite de testes.

8. **AFD stub no Main.java** — O exemplo de AFD está comentado, não executável.

9. **Sem documentação Javadoc** — Os métodos não têm documentação formal Javadoc.

10. **AFN.accepts não valida entrada vazia** — Para palavra vazia, o loop `for(char c : word.toCharArray())` não executa; o resultado depende apenas do fecho epsilon inicial e dos estados de aceitação. Isso está correto teoricamente, mas merece teste explícito.

---

## Commits, Branches e Histórico

- **Branch principal:** `main` (assumido)
- **Status do repositório:** Projeto didático em fase inicial — funcionalidade AFD implementada, AFN implementado, testes parciais
- **Atividade recente:** Não informada

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido pelo projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Diagramas de estado:** para cada autômato exemplo, diagrama gráfico das transições
- **Novos métodos:** documentar assinaturas e comportamentos esperados
- **Correções:** registrar bugs encontrados e corrigidos (ex: StackOverflow em ε-closure)
- **Conversão AFD↔AFN:** se a subset construction for implementada, documentar o algoritmo
- **Build system:** se Maven/Gradle for adicionado, documentar dependências e comandos

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/AFD-AFN_impl
- **Branch main:** https://github.com/lucaslimacodes/AFD-AFN_impl/tree/main
- **Teoria dos autômatos (séries):** Livros clássicos como "Introduction to the Theory of Computation" (Sipser) e "Automata and Computability" (Kozen)
- **Especificação Java:** https://docs.oracle.com/javase/specs/
- **JUnit 5:** https://junit.org/junit5/
- **Wiki do projeto lhl-wiki:** https://github.com/lucaslimacodes/lhl-wiki

---

*Wiki detalhada gerada por Hermes Agent com análise estática do código-fonte.*
*Última atualização: 2026-09-15*
