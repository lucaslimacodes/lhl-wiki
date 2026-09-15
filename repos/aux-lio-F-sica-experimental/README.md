# aux-lio-F-sica-experimental

> Wiki detalhada do repositório **aux-lio-F-sica-experimental** — biblioteca Python de funções para a cadeira de Física Experimental

- **Repositório original:** https://github.com/lucaslimacodes/aux-lio-F-sica-experimental
- **Linguagem principal:** Python
- **Última atualização do repo:** 2023-12-01
- **Criado em:** 2023-12-01 (estimado)
- **Estrelas:** 0 | **Forks:** — | **Issues abertas:** —
- **Arquivos no repo:** 1 (fisExp.py, ~1.7 KB)
- **Wiki deste repositório:** [repos/aux-lio-F-sica-experimental/](./aux-lio-F-sica-experimental/) (esta pasta)

---

## Visão Geral

Este repositório contém uma **biblioteca de funções Python** voltada para a disciplina de **Física Experimental**, desenvolvida como auxiliares de cálculo para laboratório. O código inclui funções para:

1. **Cálculo de erro propagado** — usando derivadas parciais e a fórmula de propagação de incertezas
2. **Estatística descritiva** — média, média dos quadrados e desvio padrão de conjuntos de dados
3. **Uso de SymPy** para cálculo simbólico (derivadas e substituição numérica)

O projeto foi likely criado durante o curso de graduação do autor, como ferramenta de apoio às aulas e relatórios de laboratório. Não há estrutura de projeto (diretórios, testes, dependências além do SymPy), sendo um arquivo monolítico de ~1.7 KB com funções + código de exemplo comentado.

---

## Contexto

| Campo | Valor |
|-------|-------|
| Nome | `aux-lio-F-sica-experimental` |
| Proprietário | `lucaslimacodes` |
| URL | https://github.com/lucaslimacodes/aux-lio-F-sica-experimental |
| Linguagem | Python |
| Descrição | código em python com funções utilizadas na cadeira de física experimental |
| Topics | N/A |
| Estrelas | 0 |
| Data de atualização | 2023-12-01T19:34:59Z |
| Arquivado | Não |
| Página deste wiki | [repos/aux-lio-F-sica-experimental.md](./aux-lio-F-sica-experimental.md) |

---

## Estrutura do Projeto

```
aux-lio-F-sica-experimental/
├── fisExp.py               # Único arquivo: funções + código de exemplo (~1.7 KB)
└── .git/                   # Historico do Git (sem commits extras relevantes)
```

**Nota:** O repositório não possui `README.md`, nem `setup.py`/`requirements.txt`, nem estrutura de pacotes. É um arquivo-fonte único e autoconciente.

### Conteúdo de fisExp.py (linha a linha)

O arquivo começa importando `from sympy import *` e definindo **24 símbolos** (a até z, com ressalva: `s` recebe `Symbol('a')` — duplicidade com `a`):

```python
a = Symbol('a')   # ...
b = Symbol('b')
# ... até z = Symbol('z')
s = Symbol('a')   # NOTE: aqui s = Symbol('a'), não Symbol('s') — provável erro de digitação
t = Symbol('t')
# ... resto das letras
z = Symbol('z')
```

Em seguida surgem **4 funções**:

1. `erroPropagado(f, variaveis, valores, erros)` — propagação de erro
2. `media(dados)` — média aritmética
3. `media_dos_quadrados(dados)` — média dos quadrados
4. `desvio_padrao(dados)` — desvio padrão (pela fórmula de raiz da diferença)

E no final, um **bloco de exemplo** com código comentado que mostra o uso das funções para cálculo de energia cinética (`E = m*v²/2`) e estatística de uma lista de dados.

---

## Stack Tecnológica

| Componente | Tecnologia | Observação |
|------------|-----------|-------------|
| **Linguagem** | Python | Versão não especificada (provavelmente 3.x) |
| **Biblioteca** | SymPy (`python sympy`) | para derivadas simbólicas e `N()` (avaliação numérica) |
| **Formato** | Script monolítico | 1 arquivo `.py`, sem estrutura de pacote |
| **Docstring** | Não utiliza | Funções sem documentação inline |

### Dependências

O único `import` é `from sympy import *`. Para rodar, basta ter o SymPy instalado:

```bash
python -c "from sympy import *"   # verifica se está disponível
pip install sympy                  # se não estiver
```

---

## O que as Funções Fazem

### 1. `erroPropagado(f, variaveis, valores, erros)` — Propagação de incertezas

**Fórmula implementada:** propagação de erro pelo método das derivadas parciais (primeira ordem):

$$\sigma_f = \sqrt{ \sum_{i=1}^{n} \left( \frac{\partial f}{\partial x_i} \cdot \sigma_{x_i} \right)^2 }$$

**Assinatura:**

```python
def erroPropagado(f, variaveis, valores, erros):
    # f             : expressão simbólica de SymPy (ex: m*(v**2)/2)
    # variaveis     : lista de símbolos [m, v, ...]
    # valores       : lista de valores numéricos [10, 10, ...]
    # erros         : lista de incertezas [\delta m, \delta v, ...]
    # retorno       : float — erro propagado \sigma_f
```

**Algoritmo interno (passo a passo):**

1. Inicializa `resultado = 0`
2. Para cada variável `i`:
   - Calcula `diff(f, variaveis[i])` → derivada parcial ∂f/∂xᵢ
   - Substitui todas as variáveis pelos valores numéricos: `func = func.subs(variaveis[j], valores[j])` para cada `j`
   - Acumula `(N(func) * erros[i])**2` no resultado
3. Retorna `sqrt(resultado)`

**Observações críticas:**

- O `subs` ocorre em **toda a lista de variáveis para cada variável `i`**, o que é redundantes mas funciona.
- `N(func)` converte a expressão simbólica para um float numérico antes de multiplicar pelo erro.
- A fórmula assume variáveis **independentes** (sem correlação).

### 2. `media(dados)` — Média aritmética

**Fórmula:**

$$\bar{x} = \frac{1}{n} \sum_{i=1}^{n} x_i$$

Implementação direta: soma os elementos e divide pelo comprimento da lista.

### 3. `media_dos_quadrados(dados)` — Média dos quadrados

**Fórmula:**

$$\overline{x^2} = \frac{1}{n} \sum_{i=1}^{n} x_i^2$$

Função auxiliar usada pelo desvio padrão.

### 4. `desvio_padrao(dados)` — Desvio padrão amostral

Usa a **identidade algébrica**:

$$\sigma = \sqrt{\overline{x^2} - (\bar{x})^2}$$

Ou seja: raiz da diferença entre a média dos quadrados e o quadrado da média.

> **Nota importante:** Esta fórmula computa o desvio padrão **da população** (divide por `n`), não o desvio padrão **amostral** (que divide por `n-1`). Em contextos de laboratório com poucos dados, isso pode importar. A função não recebe parâmetro para escolher entre os dois.

---

## Contexto da Disciplina de Física Experimental

A disciplina de **Física Experimental** é comum no currículo de licenciatura e bacharelado em Física, e também em cursos de engenharia e ciências afins. O objetivo é treinar o aluno no **método científico aplicado ao laboratório**:

- Medir grandezas físicas com instrumentos (régua, balança, cronômetro, osciloscópio, etc.)
- Identificar e quantificar **fontes de erro** (sistemático, aleatório, instrumental)
- Calcular **incertezas** e **propagá-las** para grandezas derivadas
- Usar **estatística descritiva** para analisar conjuntos de medições (média, desvio padrão, redução de dados)
- Elaborar **relatórios de laboratório** com tratamento de dados

As funções deste repositório mapeiam diretamente essas necessidades:

| Necessidade em Física Experimental | Função em fisExp.py |
|------------------------------------|---------------------|
| Calcular resultado de fórmula com medidas (ex: energia cinética) | `erroPropagado` |
| Quantificar incerteza total propagada | `erroPropagado` (usa derivadas parciais) |
| Reduzir conjunto de medições repetidas (ex: período de oscilação) | `media` |
| Calcular dispersão dos dados | `desvio_padrao` |
| Verificar consistência estatística | `media_dos_quadrados` + `desvio_padrao` |

**Exemplo típico de uso em laboratório:** medir massa `m` e velocidade `v` de um objeto, calcular energia cinética `E = m·v²/2`, e obter o erro propagado sabendo as incertezas de `m` e `v`.

---

## Como Usar

### Pré-requisito

Instalar o SymPy se não estiver disponível:

```bash
pip install sympy
```

### Importar e usar

```python
from sympy import *

# === Símbolos (definidos no arquivo; repita ou exporte) ===
m = Symbol('m')
v = Symbol('v')

# === 1. Propagação de erro ===
f = m * (v**2) / 2         # energia cinética
variaveis = [m, v]
valores = [1.5, 3.0]       # m=1.5 kg, v=3.0 m/s
erros = [0.05, 0.1]        # delta m=0.05 kg, delta v=0.1 m/s

erro_total = erroPropagado(f, variaveis, valores, erros)
print(f"Erro propagado: {erro_total:.4f}")

# === 2. Estatística de dados ===
dados = [9.81, 9.78, 9.83, 9.80, 9.79]  # medições de g

print(f"Média: {media(dados):.4f}")
print(f"Desvio padrão (pop.): {desvio_padrao(dados):.4f}")
```

### Como rodar o arquivo como script

```bash
python fisExp.py
```

O arquivo já contém um bloco de execução comentado no final:

```python
f = m*(v**2)/2
variaveis = [m, v]
valores = [10, 10]
erros = [0.5, 0.2]
print(erroPropagado(f, variaveis, valores, erros))

dados = [1, 2, 3, 4, 5, 6]
print(media(dados))
print(desvio_padrao(dados))
```

Se executado diretamente, o script calculará a energia cinética para `m=10`, `v=10` com erros `[0.5, 0.2]` e imprimirá a média e desvio de `[1..6]`.

### Adaptando para outros cálculos

Para usar com outra fórmula, basta:

1. Definir os símbolos correspondentes (ex: `g = Symbol('g')`, `h = Symbol('h')`)
2. Escrever a expressão: `f = g*h` (ex: altura em queda livre)
3. Chamar `erroPropagado(f, [g, h], [9.8, 5.0], [0.1, 0.05])`

---

## Qualidade e Observações

### Pontos positivos

- **Compactidade:** o arquivo é enxuto (~1.7 KB) e atende ao propósito de um auxiliar de laboratório sem sobrecarga.
- **Corretude conceitual:** a fórmula de propagação de erro (derivadas parciais, soma quadrática) está implementada corretamente para variáveis independentes.
- **Uso adequado de SymPy:** `diff` para derivada parcial, `subs` para substituição, `N()` para conversão numérica — padrão simbólico correto.
- **Funções de estatística simples e legíveis:** média, média dos quadrados e desvio padrão estão claros e não dependem de bibliotecas externas além do SymPy (a função `desvio_padrao` usa `sqrt` do SymPy).
- **Código de exemplo no próprio arquivo:** facilita o entendimento imediato do uso.

### Possíveis melhorias (técnico debt)

1. **Desvio padrão amostral vs. populacional:** a função atual usa `n` no denominador (populacional). Para laboratório, o desvio padrão amostral (`n-1`) é mais adequado quando se quer estimar a incerteza da média. Poderia adicionar um parâmetro `amostral=True/False`.
2. **`s = Symbol('a')`:** provável erro de digitação nas definições de símbolos — `s` foi definido com `'a'` em vez de `'s'`, resultando em dois símbolos com o mesmo nome. Isso pode causar comportamento inesperado se `s` for usado.
3. **Ausência de docstrings:** nenhuma função tem docstring; o uso requer leitura do código.
4. **Ausência de `requirements.txt` ou `setup.py`:** não há forma declarada de instalação ou dependência; o usuário precisa saber que depende de `sympy`.
5. **Ausência de estrutura de pacote:** tudo está no namespace global; não há `if __name__ == '__main__':` (o bloco de teste é executado em importação).
6. **Nomenclatura em português:** `variaveis`, `valores`, `erros` são compreensíveis mas não seguem PEP 8 (lower_case_with_underscores) — é um padrão proposital (português) mas pode dificultar integração com código em inglês.
7. **Redundância no `subs`:** dentro do loop de `erroPropagado`, o `subs` é aplicado para cada variável `j` em **todas** as variáveis a cada iteração `i`. Pode ser simplificado substituindo uma vez antes do loop.
8. **Sem testes:** não há suite de testes, o que é compreensível para um repositório de auxiliar pessoal, mas limita confiança em modificações futuras.
9. **Hardcoded no exemplo:** os valores `m=10, v=10, erro=[0.5, 0.2]` e `dados=[1..6]` são fixos no final — não há interface interativa ou CLI.

### Pontos de atenção no código

- `s = Symbol('a')` não é `Symbol('s')` — cuidado ao usar a variável `s` em expressões.
- `erroPropagado` não valida que `len(variaveis) == len(valores) == len(erros)` — pode gerar erro de índice ou comportamento inesperado se listas de tamanhos diferentes forem passadas.
- `desvio_padrao` usa `sqrt` do SymPy (devolve um objeto simbólico); para listas puramente numéricas, o resultado pode ser um `Float` do SymPy em vez de um `float` nativo do Python.

---

## Commits, Branches e Histórico

- **Branch principal:** `main` (padrão do GitHub)
- **Última atualização:** 2023-12-01
- **Atividade:** repositório com pouca atividade, provavelmente criado para entrega de tarefa ou uso pessoal durante o curso.

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original: https://github.com/lucaslimacodes/aux-lio-F-sica-experimental/branches e https://github.com/lucaslimacodes/aux-lio-F-sica-experimental/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**, adaptado para um repositório simples de funções Python:

- **Visão geral** — propósito e escopo
- **Estrutura** — arquivos e conteúdo
- **Contexto da disciplina** — motivação pedagógica
- **Análise das funções** — o que cada função faz, fórmula, assinatura, exemplo
- **Como usar** — instruções práticas e código
- **Qualidade e observações** — pontos fortes, melhorias
- **Links** — repositório original

Se o repositório for atualizado (novas funções, documentação, estrutura de pacote), esta página deve ser atualizada para refletir as mudanças.

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/aux-lio-F-sica-experimental
- **Branch main:** https://github.com/lucaslimacodes/aux-lio-F-sica-experimental/tree/main
- **Fonte do código:** `fisExp.py` no repositório acima
- **Documentação SymPy:** https://docs.sympy.org/latest/index.html
- **Propagação de incertezas (Referência):** https://en.wikipedia.org/wiki/Propagation_of_uncertainty
- **Desvio padrão (Referência):** https://en.wikipedia.org/wiki/Standard_deviation
- **Física Experimental (conceitos gerais):** varia conforme a instituição de ensino

---

*Wiki detalhada gerada automaticamente por Hermes Agent com análise estática do código-fonte (fisExp.py) e contexto do repositório.*
*Última atualização: 2026-09-15*
