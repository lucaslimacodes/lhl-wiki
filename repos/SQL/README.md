# SQL

> Wiki detalhada do repositório **SQL** — Script DDL/DML e consultas ORACLE sobre projeto de banco de dados GDI (Personas, Jogadores, Batalhas)

- **Repositório original:** https://github.com/lucaslimacodes/SQL
- **Branch principal:** `main`
- **Último commit:** a1fac03 ("Add files via upload")
- **Criado em:** 2026-09-15 (data aproximada baseada no commit)
- **Arquivo principal:** `tudo.sql` (16.076 bytes, 866 linhas)
- **Linguagem:** SQL (Dialeto ORACLE/PLSQL)
- **Descrição do repo:** "Sem descrição"
- **Wiki deste repositório:** [repos/SQL/](./SQL/) (esta pasta)

---

## Visão Geral

Este repositório contém um único arquivo SQL (`tudo.sql`) que é um script completo para criação, popularização e consulta de um banco de dados relacional voltado a um **projeto de simulação/Game Design** com temática de **Personas** (cartas/archetipos), jogadores, batalhas e sistema de trocas. O script parece ter sido gerado a partir do **Oracle SQL Developer** ("PROJETO_DE_GDI", conforme o cabeçalho `REM Script: PROJETO_DE_GDI`).

O script cobre:
1. **DDL:** Criação de 12 tabelas com PK, FK e restrições
2. **DML:** Inserção de dados de exemplo (130 INSERTs distribuídos)
3. **Consultas:** 10+ queries de ponta (JOINs, subconsultas, operadores de conjunto — UNION, ANY, NOT EXISTS, IN, semi-junções)
4. **PL/SQL:** 2 funções, 1 procedure e 1 trigger (Oracle)

O domínio modelado parece inspirado em **Personas** (como o jogo da Atlus/Shin Megami Tensei) e simula um ecossistema de jogadores que coletam, treinam, trocam e batalham com "personas".

---

## Estrutura do Projeto

```
SQL/
├── .git/
├── tudo.sql          # Script completo (DDL + DML + Queries + PL/SQL)
└── README.md         # Esta wiki
```

---

## O que o SQL Contém

### 1. Tabelas (DDL — 12 tabelas)

#### PLAYER
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| COIN | NUMBER(4) | NOT NULL | Moedas do jogador |
| HP | NUMBER(3) | NOT NULL | HP do jogador |
| SP | NUMBER(3) | NOT NULL | SP do jogador |
| XP | NUMBER(3) | NOT NULL | XP do jogador |
| COLD_WEAPONS | VARCHAR(20) | — | Arma fria |
| SHIRT | VARCHAR(20) | — | Camisa/vestimenta |
| PANTS | VARCHAR(20) | — | Calça |
| ACESSORY | VARCHAR(20) | — | Acessório |
| GUN | VARCHAR(20) | — | Arma de fogo |
| PLAYER_NAME | VARCHAR(20) | **PK** | Nome do jogador (chave primária) |

#### PERSONA
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| CODE | NUMBER(4) | **PK** | Código da persona |
| TYPE | VARCHAR(20) | NOT NULL | Tipo da persona (ex: FOOL, JOKER, MAGCIAN...) |
| NAME | VARCHAR(20) | NOT NULL, UNIQUE | Nome da persona |

#### PLAYER_PERSONA (Associativa: Jogador ↔ Persona)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| CODE | NUMBER(4) | **PK** (parte da PK composta implícita) | Referência à PERSONA.CODE |
| PLAYER_NAME | VARCHAR(20) | NOT NULL | Referência à PLAYER.PLAYER_NAME |
| FKs | — | PLAYER (ON DELETE CASCADE), PERSONA (ON DELETE CASCADE) | |

#### WILD_PERSONA
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| CODE | NUMBER(4) | **PK** | Referência à PERSONA.CODE (wild = selvagem, encontrada na natureza) |
| FK | — | PERSONA (ON DELETE CASCADE) | |

#### TEAM_PERSONA
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| CODE | NUMBER(4) | **PK** | Referência à PLAYER_PERSONA.CODE (persona do time/jogador em equipe) |
| FK | — | PLAYER_PERSONA (ON DELETE CASCADE) | |

#### SOLD_PERSONA
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| CODE | NUMBER(4) | **PK** | Referência à PLAYER_PERSONA.CODE (persona vendida) |
| COST | NUMBER(4) | NOT NULL | Preço de venda |
| FK | — | PLAYER_PERSONA (ON DELETE CASCADE) | |

#### PRISON
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| PRISON_ID | NUMBER(4) | **PK** | ID único da prisão |
| CODE | NUMBER(4) | NOT NULL | Referência à TEAM_PERSONA.CODE (persona presa) |
| FK | — | TEAM_PERSONA (ON DELETE CASCADE) | |

#### REWARD (Tipo de recompensa — tabela de referência)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| TYPE_REWARD | VARCHAR(20) | **PK** | Tipo de recompensa (PERSONA, ITEM, COINS, XP) |

#### EXCHANGE (Tabela de trocas entre jogadores)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| SELLER_NAME | VARCHAR(20) | **PK (parte 1)** | Vendedor (FK → PLAYER) |
| BUYER_NAME | VARCHAR(20) | **PK (parte 2)** | Comprador (FK → PLAYER) |
| DATA | DATE | **PK (parte 3)** | Data da troca |
| PK | — | (SELLER_NAME, BUYER_NAME, DATA) — PK composta |
| FKs | — | PLAYER (ON DELETE CASCADE) para ambos | |

#### ITEM (Item possuído por persona + jogador)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| PERSONA_CODE | NUMBER(4) | **PK (parte 1)** | Código da persona (FK → PERSONA) |
| CODE_ITEM | NUMBER(4) | **PK (parte 2)** | Código do item |
| PLAYER_NAME | VARCHAR(20) | — | Jogador dono (FK → PLAYER, nullable) |
| PK | — | (PERSONA_CODE, CODE_ITEM) — PK composta |
| FKs | — | PERSONA (ON DELETE CASCADE), PLAYER (ON DELETE CASCADE) | |

#### ATTACK (Ataque/conhecimento da persona)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| PERSONA_CODE | NUMBER(4) | **PK (parte 1)** | Código da persona (FK → PERSONA) |
| ATTACK | VARCHAR(20) | **PK (parte 2)** | Nome do ataque (TIRO, MURRO, MAGIA...) |
| PK | — | (PERSONA_CODE, ATTACK) — PK composta |
| FK | — | PERSONA (ON DELETE CASCADE) | |

#### BATTLE (Batalha entre time e wild persona)
| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| TEAM_PERSONA_CODE | NUMBER(4) | **PK (parte 1)** | Código do time (FK → TEAM_PERSONA) |
| WILD_PERSONA_CODE | NUMBER(4) | **PK (parte 2)** | Código da wild persona (FK → WILD_PERSONA) |
| PLAYER_NAME | VARCHAR(20) | NOT NULL | Jogador envolvido (FK → PLAYER) |
| REWARD_TYPE | VARCHAR(20) | — | Tipo de recompensa (FK → REWARD) |
| BATTLE_ID | NUMBER(3) | **PK (parte 3)** | ID da batalha |
| RESULT | VARCHAR(5) | NOT NULL | Resultado (WIN/LOSE) |
| PK | — | (TEAM_PERSONA_CODE, WILD_PERSONA_CODE, BATTLE_ID) — PK composta |
| FKs | — | TEAM_PERSONA, WILD_PERSONA, PLAYER, REWARD (todos ON DELETE CASCADE) | |

### 2. Dados Inseridos (DML)

**Jogadores (10 registros):**
EDNA, RUI, ROBSON, EDUARDO, ACM, GUSTAVO, TSANG, ANJOLINA, PAULO, STEFAN

**Personas (26 registros):**
Tipos presentes: FOOL, JOKER, MAGCIAN, PRIESTESS, DEVIL, EMPEROR, DEATH, EMPRESS, HANGED MAN, MAGICIAN, JUSTICE

**Relacionamentos especiais:**
- PLAYER_PERSONA: 15 associações (alguns jogadores com múltiplas personas)
- WILD_PERSONA: 10 personas selvagens (códigos 0010-0019)
- TEAM_PERSONA: 12 (códigos 0001-0009, 0024-0026)
- SOLD_PERSONA: 3 (códigos 0021-0023 com custos 0010, 0090, 1000)
- PRISON: 3 (códigos 0024-0026 presos nos índices 0001-0003)
- REWARD: 4 tipos (PERSONA, ITEM, COINS, XP)
- EXCHANGE: 11 trocas entre jogadores
- ITEM: 8 itens (alguns sem jogador — NULL)
- ATTACK: 27 ataques distribuídos (TIRO, MURRO, MAGIA)
- BATTLE: 12 batalhas (mix de WIN/LOSE)

### 3. Consultas de Exemplo

| # | Tema | Técnica SQL |
|---|------|-------------|
| 1 | Jogadores com mais de 1 persona | GROUP BY + HAVING COUNT(*) > 1 |
| 2 | Personas que ganharam batalha | INNER JOIN + WHERE RESULT LIKE 'WIN' |
| 3 | Personas que não podem ser trocadas por itens | LEFT OUTER JOIN + IS NULL |
| 4 | Jogadores que não batalharam | NOT EXISTS (anti-junção) |
| 5 | Wild personas que brigaram | Semi-junção (IN + EXISTS) |
| 6 | Jogador com mais moedas | Subconsulta escalar (MAX) |
| 7 | Vendedores que venderam para EDUARDO em 12/12/2023 | Tupla/row value comparison |
| 8 | Jogadores com moedas maiores que o menor | Operador ANY |
| 9 | Jogadores com personas vendidas OU na prisão | IN + UNION (operação de conjunto) |
| 10 | Compradores distintos que começam com 'E' | Cursor em PL/SQL (procedure) |
| 11 | Compradores de ANJOLINA em 14/12/2023 | Tupla + subconsulta |

### 4. PL/SQL (Oracle)

#### Função: `qtd_batalhas(NAME VARCHAR) RETURN NUMBER`
Retorna a quantidade de batalhas feitas por um jogador. Trata `NO_DATA_FOUND` retornando NULL.

#### Função: `QTD_TYPE(NOME VARCHAR) RETURN NUMBER`
Retorna a quantidade de personas de um determinado tipo. Trata `NO_DATA_FOUND` retornando NULL.

#### Procedure: `troca`
Curador de compradores distintos que começam com a letra 'E', usando cursor implícito e `DBMS_OUTPUT.PUT_LINE`.

#### Trigger: `BUYER_TROCA`
Trigger `BEFORE INSERT OR UPDATE OF BUYER_NAME ON EXCHANGE` que fires apenas quando o `BUYER_NAME` antigo é 'GUSTAVO'. Em UPDATE: loga que "TUDO SE MANTEM, MENOS O BUYER_NAME". Em INSERT: loga "TUDO É SOBRE O VALOR RECENTE".

---

## Como Usar

### Pré-requisitos
- **Oracle Database** (Desktop, Express Edition, ou acesso a um schema Oracle)
- **SQL*Plus**, **SQL Developer**, ou qualquer cliente Oracle que execute scripts SQL/PLSQL

### Executando o Script

```bash
# Via SQL*Plus
sqlplus usuario/senha@conexao @tudo.sql

# Via SQL Developer
Arquivo → Abrir → tudo.sql → Executar (F5)
```

> **Atenção:** O script usa sintaxe Oracle-specific (NUMBER, VARCHAR2-compatible com VARCHAR, TO_DATE, `CREATE OR REPLACE FUNCTION`, `CREATE OR REPLACE TRIGGER`, `DBMS_OUTPUT`). Não é compatível com MySQL/MariaDB/PostgreSQL sem adaptações significativas.

### Fluxo de execução esperado

1. **DDL:** 12 CREATE TABLE (cria tabelas com PK/FK)
2. **DML:** INSERTs nas tabelas
3. **Queries:** SELECTs exibindo resultados variados
4. **PL/SQL:** 2 funções + 1 procedure + 1 trigger

---

## Modelagem — Diagrama Conceitual (texto)

```
                    ┌─────────────┐
                    │   PLAYER    │
                    │ (PK:NAME)   │
                    └──────┬──────┘
                           │ 1:N
           ┌───────────────┼───────────────┐
           │               │               │
    ┌──────▼──────┐  ┌─────▼─────┐  ┌─────▼────────┐
    │PLAYER_PERSONA│  │  EXCHANGE │  │     ITEM     │
    │  (PK:CODE)   │  │ (PK trïple)│  │(PK:PERCODE, │
    └──────┬──────┘  └───────────┘  │ CODE_ITEM)   │
           │ 1:N                     └──────┬───────┘
    ┌──────▼──────┐                       │
    │    PERSONA  │◄──────────────────────┘
    │ (PK:CODE)   │
    └──────┬──────┘
           │
    ┌──────┼──────┐
    │      │      │
┌───▼──┐ ┌─▼───┐ ┌▼────┐
│WILD_ │ │TEAM_│ │SOLD_│
|PERS  │ │PERS │ │PERS │
└───┬──┘ └──┬──┘ └──┬──┘
    │       │       │
    │    ┌──▼──┐    │
    │    │PRISON│   │
    │    └─────┘   │
    │              │
    │         ┌────▼──────┐
    │         │   BATTLE  │
    │         │(PK trïple)│
    │         └─────┬─────┘
    │               │
    │         ┌─────▼─────┐
    │         │   REWARD  │
    │         │(PK:TYPE)  │
    │         └───────────┘
    │
┌───▼───┐
│ATTACK │
│(PK tr│
│ïple) │
└───────┘
```

**Observações de modelagem:**
- A chave primária de `PLAYER` é o nome (`PLAYER_NAME VARCHAR(20)`) — uma escolha atípica, mas funcional para o exercício
- `PLAYER_PERSONA` é uma associação N:N entre PLAYER e PERSONA (com PK simples em CODE, mas com FK para ambos)
- `WILD_PERSONA`, `TEAM_PERSONA`, `SOLD_PERSONA` são especializações/relacionamentos 1:1 ou 1:N com `PLAYER_PERSONA` e `PERSONA`
- `BATTLE` é uma entidade forte com PK composta em 3 colunas
- `REWARD` é uma tabela de domínio/referência com PK em VARCHAR

---

## Qualidade e Observações

### Pontos positivos
- Modelagem completa com 12 tabelas (entidades, associativas e de referência)
- Uso consistente de chaves estrangeiras com `ON DELETE CASCADE`
- PK composta em diversas entidades (BATTLE, ITEM, ATTACK, EXCHANGE)
- Diversidade de técnicas de consulta: JOINs (INNER, LEFT OUTER), subconsultas escalares, tuplas, anti-junções (NOT EXISTS), semi-junções (EXISTS/IN), operadores de conjunto (ANY, UNION), GROUP BY/HAVING
- PL/SQL completo: funções com tratamento de exceção, procedure com cursor, trigger condicional
- Regras de negócio claras: personas selvagens, equipes, vendas, prisão, trocas, batalhas

### Pontos de atenção / Melhorias possíveis
1. **PLAYER_NAME como PK da tabela PLAYER** — nomes não são bons identificadores (podem não ser únicos na vida real). Idealmente usar um ID surrogate (`NUMBER` com sequence).
2. **VARCHAR(20) pequeno demais** — nomes de jogadores e personas ocupam VARCHAR(20), o que é curto para algum dos nomes usados.
3. **`PLAYER_PERSONA` com PK em `CODE`** — parece haver redundância na modelagem: CODE é PK, mas também é FK para PERSONA. Seria mais claro ter uma PK composta (`CODE, PLAYER_NAME`) ou um ID surrogate.
4. **Tipo PERSONA com typo** — "MAGCIAN" vs "MAGICIAN" aparece em dois registros (código 0004 vs 0012) — provável erro de digitação.
5. **Categorias REWARD com potencial de expansão** — se novos tipos de recompensa surgirem, é só INSERT na tabela (bom design).
6. **Trigger condicional estreita** — a trigger `BUYER_TROCA` só dispara quando `BUYER_NAME = 'GUSTAVO'`, o que é um exemplo didático, mas limitado.
7. **Ausência de INDEX** — não há índices explícitos além das PKs; em um volume maior, JOINs em VARCHAR(pK) poderiam ser lentos.
8. **Sem restrição de CHECK** — não há CHECK constraints (ex: RESULT IN ('WIN','LOSE'), TYPE em lista Fechada, valores positivos para COIN/COST).
9. **Script monolítico** — tudo em um único arquivo de 866 linhas; para projetos reais, separar DDL, DML, consultas e PL/SQL em scripts diferentes seria mais manutenível.
10. **Independência de ordem de execução** — o script não usa `DROP TABLE IF EXISTS` ou `CREATE OR REPLACE` para tabelas, então rodar duas vezes falharia com erro de tabela já existente (salvo se o script fosse reexecutável).

### Observações sobre a origem
O cabeçalho `REM Script: PROJETO_DE_GDI` sugere que este script foi gerado/exportado pelo **Oracle SQL Developer** ou ferramenta similar como parte de um projeto educacional ("PROJETO DE GDI" — talvez "Game Design" ou curso específico). Não há README, licença, ou metadados no repositório.

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/SQL
- **Branch main:** https://github.com/lucaslimacodes/SQL/tree/main
- **Oracle Documentation (SQL Language Reference):** https://docs.oracle.com/en/database/oracle/oracle-database/
- **Oracle PL/SQL Documentation:** https://docs.oracle.com/en/database/oracle/oracle-database/latest/plsql/
- **Oracle SQL Developer:** https://www.oracle.com/database/sqldeveloper/
- **W3Schools SQL Tutorial:** https://www.w3schools.com/sql/
- **PostgreSQL (alternativa para portar o script):** https://www.postgresql.org/

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada do projeto **lhl-wiki** (veja também [desafio-tecnico-ras](../desafio-tecnico-ras/README.md) para o formato de referência). As próximas seções, se adicionadas, devem incluir:

- Atualizações na modelagem (se tabelas vierem a mudar)
- Novas consultas ou objetos PL/SQL
- Mudanças de dialecto ou compatibilidade
- Histórico de versões

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15*