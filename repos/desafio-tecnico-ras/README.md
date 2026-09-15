# desafio-tecnico-ras

> Wiki detalhada do repositório **desafio-tecnico-ras** — API de Tabela Tarifária de Água (GRUPO RAS — Desafio Técnico para Desenvolvedor de Sistemas Jr.)

- **Repositório original:** https://github.com/lucaslimacodes/desafio-tecnico-ras
- **Branch principal:** `main` (Spring Boot 4.0.2 + Java 21 + PostgreSQL)
- **Deploy em produção:** https://desafio-tecnico-ras.onrender.com/swagger-ui/index.html
- **Documentação Swagger:** http://localhost:8080/swagger-ui/index.html (local) | em produção via Render
- **Linguagem principal:** Java (Spring Boot)
- **Última atualização do repo:** 2026-02-08
- **Criado em:** 2026-02-08
- **Estrelas:** 0 | **Forks:** — | **Issues abertas:** —
- **Wiki deste repositório:** [repos/desafio-tecnico-ras/](./desafio-tecnico-ras/) (esta pasta)

---

## Visão Geral

Esta API foi desenvolvida como solução para o **desafio técnico do GRUPO RAS** no processo seletivo para Desenvolvedor de Sistemas Jr. O objetivo é criar uma API REST com Spring Boot + PostgreSQL para cadastro e gestão de **tabelas tarifárias de água**, permitindo:

1. **Cadastro de tabelas tarifárias** — com múltiplas categorias e faixas de consumo
2. **Cálculo de consumo** — cobrança progressiva por faixa, baseada na tabela vigente
3. **Gestão de categorias** — COMERCIAL, INDUSTRIAL, PARTICULAR, PÚBLICO
4. **Rastreabilidade** — cada tabela registrada com data de criação e status de vigência

A aplicação segue uma arquitetura em camadas clássica (Controllers → Services → Repositories) com Spring Data JPA, validação via Bean Validation, documentação automática com SpringDoc OpenAPI (Swagger UI), e deploy via Docker Compose.

---

## Estrutura do Projeto

```
desafio-tecnico-ras/
├── pom.xml                              # Configuração Maven (Spring Boot 4.0.2, Java 21)
├── Dockerfile                           # Build do container da API
├── docker-compose.yaml                  # Orquestração: API + PostgreSQL 16
├── README.md                            # README do repositório original
├── src/
│   ├── main/
│   │   ├── java/com/gruporas/tarifas/
│   │   │   ├── TarifasApplication.java         # Ponto de entrada (Spring Boot)
│   │   │   ├── controller/
│   │   │   │   ├── TabelaTarifariaController.java   # CRUD de tabelas tarifárias
│   │   │   │   └── CalculoController.java           # Cálculo de consumo
│   │   │   ├── service/
│   │   │   │   ├── TabelaTarifariaService.java      # Regras de negócio das tabelas
│   │   │   │   └── CalculoService.java              # Algoritmo de cobrança por faixa
│   │   │   ├── repository/
│   │   │   │   ├── TabelaTarifariaRepository.java  # Repositório das tabelas (+ @Query custom)
│   │   │   │   ├── CategoriaRepository.java        # Repositório de categorias
│   │   │   │   └── FaixaConsumoRepository.java     # Repositório de faixas (+ @Query custom)
│   │   │   ├── model/
│   │   │   │   ├── TabelaTarifaria.java            # Entidade central (tabela tarifária)
│   │   │   │   ├── Categoria.java                  # Entidade de categoria
│   │   │   │   ├── FaixaConsumo.java               # Entidade de faixa de consumo
│   │   │   │   ├── TabelaTarifariaCategoria.java   # Entidade associativa N:N
│   │   │   │   └── embeddable/
│   │   │   │       └── TabelaTarifariaCategoriaId.java  # ID composto da associação
│   │   │   ├── dto/
│   │   │   │   ├── TabelaTarifariaDTO.java          # DTO de entrada para criação
│   │   │   │   ├── TabelaTarifariaResponseDTO.java  # DTO de saída (resposta)
│   │   │   │   ├── CategoriaDTO.java               # DTO de categoria
│   │   │   │   ├── FaixaConsumoDTO.java            # DTO de faixa
│   │   │   │   ├── FaixaInicioFimDTO.java          # DTO de início/fim da faixa
│   │   │   │   ├── CobrancaFaixaDTO.java           # DTO de cobrança por faixa
│   │   │   │   ├── CalculoRequestDTO.java          # DTO de entrada do cálculo
│   │   │   │   └── CalculoResponseDTO.java         # DTO de saída do cálculo
│   │   │   ├── exception/
│   │   │   │   ├── CategoriaInvalidaException.java
│   │   │   │   ├── FaixaInvalidaException.java
│   │   │   │   └── TabelaTarifariaNotFoundException.java
│   │   │   ├── infra/
│   │   │   │   ├── ErroDTO.java                    # DTO padronizado de erro
│   │   │   │   └── TarifasExceptionHandler.java    # @ControllerAdvice global
│   │   │   ├── utils/
│   │   │   │   └── TabelaTarifariaValidator.java   # Validação de regras de negócio
│   │   │   └── DBSeeder/
│   │   │       └── DatabaseSeeder.java             # Seeder: popula categorias padrão
│   │   └── resources/
│   │       ├── application.properties              # Configurações gerais
│   │       ├── application-dev.properties          # Profile dev (H2 console, logs)
│   │       ├── application-prod.properties        # Profile prod
│   │       └── seed.sql                            # SQL de seed das categorias
│   └── test/
│       └── java/com/gruporas/tarifas/
│           └── TarifasApplicationTests.java        # Testes de contexto Spring
└── src/main/resources/static/                      # Assets estáticos
    ├── modelagem_banco.png                         # Diagrama MER
    └── arquitetura.png                            # Diagrama de arquitetura
```

---

## Stack Tecnológica

| Componente | Tecnologia | Versão/Observação |
|------------|-----------|-------------------|
| **Linguagem** | Java | 21 |
| **Framework** | Spring Boot | 4.0.2 (parent) |
| **Persistence** | Spring Data JPA + Hibernate | — |
| **Banco de dados** | PostgreSQL | 16 (via Docker) / Supabase em produção |
| **Build** | Maven | mvnw (Maven Wrapper) |
| **Documentação API** | SpringDoc OpenAPI | 2.8.14 (Swagger UI) |
| **Validação** | Bean Validation (jakarta.validation) | — |
| **Testes** | Spring Boot Test + JUnit 5 | Contexto Spring (TEST) |
| **DevTools** | spring-boot-devtools | Runtime (reinicio automático) |
| **Containerização** | Docker + Docker Compose | — |
| **Deploy** | Render (plano free) + Supabase | Cold start no plano gratuito |
| **Lombok** | lombok | @Data, @Builder, @AllArgsConstructor, @NoArgsConstructor |

### Dependências Maven (pom.xml)

```
<!-- Spring Boot Starters -->
spring-boot-starter-data-jpa        # Spring Data JPA + Hibernate
spring-boot-starter-webmvc          # Spring MVC (REST)
spring-boot-starter-validation      # Bean Validation (jakarta)
spring-boot-devtools                # DevTools (runtime)
spring-boot-h2console               # H2 Console (dev)

<!-- Database -->
postgresql                         # Driver PostgreSQL (runtime)
h2                                 # H2 Database (runtime/test)

<!-- Documentation -->
springdoc-openapi-starter-webmvc-ui  # Swagger UI (2.8.14)

<!-- Test -->
spring-boot-starter-data-jpa-test
spring-boot-starter-webmvc-test

<!-- Utils -->
lombok (annotationProcessor)
```

---

## Modelagem de Dados

### Diagrama Entidade-Relacionamento (MER)

![Modelagem](https://raw.githubusercontent.com/lucaslimacodes/desafio-tecnico-ras/main/src/main/resources/static/modelagem_banco.png)

(fonte: `src/main/resources/static/modelagem_banco.png` no repositório original)

### Entidades

#### TabelaTarifaria

> Entidade central da aplicação. Representa uma tabela tarifária completa.

| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| `id` | Long | PK, IDENTITY | Identificador único |
| `nome` | String | — | Nome da tabela tarifária |
| `dataCriacao` | LocalDateTime | **NOT NULL** | Data/hora de criação (America/Sao_Paulo) |
| `vigente` | Boolean | — | Se esta é a tabela vigente (ativa) |
| `tarifas` | List<TabelaTarifariaCategoria> | 1:N (cascade ALL) | Categorias vinculadas a esta tabela |

**Lógica de negócio:**
- Ao criar uma nova tabela, todas as anteriores são **revogadas** (vigência = false) via `tabelaTarifariaRepository.revogarTabelasTarifarias()`.
- A tabela mais recente sempre se torna **vigente** automaticamente.
- Ao **deletar** a tabela vigente, a tabela mais recente (por dataCriacao DESC) herda a vigência.

#### Categoria

> Entidade que representa os tipos de cliente/consumidor.

| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| `id` | Long | PK, IDENTITY | Identificador único |
| `nome` | String | **NOT NULL, UNIQUE** | Nome da categoria (ex: COMERCIAL) |
| `descricao` | String | — | Descrição livre |
| `tarifas` | List<TabelaTarifariaCategoria> | 1:N (cascade ALL) | Associações com tabelas |

**Categorias padrão (seed):**
- COMERCIAL — Estabelecimentos comerciais
- INDUSTRIAL — Indústrias e fábricas
- PARTICULAR — Residências
- PUBLICO — Órgãos públicos

#### TabelaTarifariaCategoria

> **Entidade associativa** N:N entre TabelaTarifaria e Categoria. Contém o ID composto e a lista de faixas de consumo para aquela categoria naquela tabela.

| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| `id_tabela_tarifaria` | Long | PK (parte 1, FK → TabelaTarifaria) | ID da tabela |
| `id_categoria` | Long | PK (parte 2, FK → Categoria) | ID da categoria |
| `tabelaTarifaria` | TabelaTarifaria | M:1 (via @JoinColumns) | Referência à tabela |
| `categoria` | Categoria | M:1 (via @JoinColumns) | Referência à categoria |
| `faixasConsumo` | List<FaixaConsumo> | 1:N | Faixas de consumo desta categoria nesta tabela |

**ID Composto:** Usa `TabelaTarifariaCategoriaId` (embeddable) com `@GeneratedValue` não aplicável (IDs são atribuídos manualmente via `new TabelaTarifariaCategoriaId()`).

#### FaixaConsumo

> Entidade que representa uma faixa de consumo com valor unitário. Pertence a uma TabelaTarifariaCategoria.

| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| `id` | Long | PK, IDENTITY | Identificador único |
| `tabelaTarifariaCategoria` | TabelaTarifariaCategoria | **NOT NULL, M:1** (JoinColumns) | Relação com a associação |
| `inicio` | Integer | **NOT NULL** | Início da faixa (m³) |
| `fim` | Integer | **NOT NULL** | Fim da faixa (m³) |
| `valor` | Float | **NOT NULL** | Valor unitário por m³ nesta faixa |

**Observação importante:** A faixa de consumo usa um relacionamento @ManyToOne com **dois JoinColumns** apontando para a chave composta. Isso é uma escolha de modelagem que evita criar uma entidade associativa extra.

---

## API REST (Endpoints)

### TabelaTarifariaController (`/api/tabelas-tarifarias`)

| Método | Caminho | Descrição | Status |
|--------|---------|-----------|--------|
| **POST** | `/` | Cria uma nova tabela tarifária | 201 Created |
| **GET** | `/` | Lista todas as tabelas tarifárias | 200 OK |
| **GET** | `/{id}` | Busca uma tabela pelo ID | 200 OK / 404 |
| **PUT** | `/{id}` | Atualiza uma tabela existente | 200 OK / 404 |
| **DELETE** | `/{id}` | Deleta uma tabela (e reverte vigência se necessário) | 204 No Content |

### CalculoController (`/api/calculos`)

| Método | Caminho | Descrição | Status |
|--------|---------|-----------|--------|
| **POST** | `/` | Calcula o valor de consumo para uma categoria na tabela vigente | 200 OK / 400 / 404 |

#### POST `/api/calculos` — Detalhes do Cálculo

**Request:**
```json
{
  "categoria": "PARTICULAR",
  "consumo": 45
}
```

**Response (`CalculoResponseDTO`):**

```json
{
  "categoria": "PARTICULAR",
  "consumoTotal": 45,
  "detalhamento": [
    {
      "faixa": { "inicio": 0, "fim": 10 },
      "valorUnitario": 5.0,
      "m3Cobrados": 10,
      "subtotal": 50.0
    },
    {
      "faixa": { "inicio": 11, "fim": 20 },
      "valorUnitario": 7.5,
      "m3Cobrados": 10,
      "subtotal": 75.0
    }
  ],
  "valorTotal": 187.5
}
```

**Algoritmo de cobrança (em `CalculoService`):**
1. Obtém as faixas da categoria na tabela vigente (`findAllByNomeCategoriaAndTabelaVigente`)
2. Percorre as faixas em ordem, calculando quanto do consumo cabe em cada faixa
3. Para cada faixa: `subtotal = valorUnitario × m3Cobrados`
4. Acumula `valorTotal` e decrementa `consumoRestante`
5. Se `consumoRestante == 0`, interrompe o loop
6. **Correção de bug:** para faixas com `inicio > 0`, o `deltaFaixa` é ajustado com `+1` para corrigir um offset na segunda faixa em diante

---

## Deployment

### Docker Compose (local)

```yaml
services:
  db:
    image: postgres:16
    container_name: postgres_db
    environment:
      POSTGRES_DB: app_db
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports: ["5432:5432"]
    volumes:
      - postgres_data:/var/lib/postgresql/data

  api:
    container_name: tarifas-api
    build: { context: ., dockerfile: Dockerfile }
    image: tarifas-api:latest
    ports: ["8080:8080"]
    depends_on: [db]

volumes:
  postgres_data:
```

**Comandos:**
```bash
# Subir tudo
docker compose up -d --build

# Acessar Swagger UI
open http://localhost:8080/swagger-ui/index.html

# Acessar banco (via DBeaver / pgAdmin)
Host: localhost | Database: app_db | User: user | Password: password

# Parar
docker compose down

# Parar + remover volumes
docker compose down -v
```

### Produção (Render + Supabase)

- **Host:** https://desafio-tecnico-ras.onrender.com
- **Swagger:** https://desafio-tecnico-ras.onrender.com/swagger-ui/index.html
- **Banco:** Supabase (PostgreSQL managed)
- **Observação:** Como o Render usa plano gratuito, há **cold start** — a primeira requisição (ou após inatividade) pode levar alguns segundos para inicializar.

---

## Configurações (application.properties)

```
spring.application.name=tarifas
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.profiles.active=${PROFILE:dev}
```

- `ddl-auto=update`: cria/atualiza o schema automaticamente (não para produção ideal, mas prático para o desafio)
- `show-sql=true`: log das queries no console
- Profile selecionado via variável de ambiente `PROFILE` (default: `dev`)

### Profiles

| Profile | Arquivo | Observação |
|---------|---------|------------|
| `dev` | application-dev.properties | Desenvolvimento local, H2 console habilitado |
| `prod` | application-prod.properties | Produção (Render, Supabase) |

---

## Database Seeder

A classe `DatabaseSeeder` (implementa `CommandLineRunner`) executa no início da aplicação e popula as 4 categorias padrão via `seed.sql`:

```sql
INSERT INTO categoria(id, nome, descricao)
values
    (1,'COMERCIAL','Estabelecimentos comerciais'),
    (2,'INDUSTRIAL','Indústrias e fábricas'),
    (3,'PARTICULAR','Residências'),
    (4,'PUBLICO','Órgãos públicos')
ON CONFLICT (id) DO NOTHING;
```

Isso garante que, mesmo em um banco novo, as categorias esperadas já existem antes do primeiro uso.

---

## Testes

```
src/test/java/com/gruporas/tarifas/TarifasApplicationTests.java
```

- Teste de contexto Spring (verifica se o contexto carrega sem erros)
- Usa `spring-boot-starter-data-jpa-test` e `spring-boot-starter-webmvc-test`
- H2 como banco em memória para testes

> **Observação:** Não há testes de negócio (unitários/integração) implementados além do teste de contexto. Isso é uma lacuna comum em desafios técnicos de curto prazo.

---

## Qualidade e Observações

### Pontos positivos
- Modelagem N:N bem estruturada com entidade associativa explícita
- Swagger UI completo com @Operation, @ApiResponse, @Tag
- Handler de exceções global (@ControllerAdvice) com ErroDTO padronizado
- Seeder que popula categorias básicas automaticamente
- Dockerfile e docker-compose para reproducibilidade
- Deploy functional via Render + Supabase
- Lógica de revogação de vigência bem implementada

### Possíveis melhorias (técnico debt)
1. **Testes de negócio:** faltam testes unitários para `CalculoService` e `TabelaTarifariaService`
2. **ddl-auto=update em produção:** em um projeto real, usar migrations (Flyway/Liquibase)
3. **Tratamento de exceção mais granular:** alguns casos podem não estar cobrindo todos os cenários
4. **Validações de negócio no DTO:** alguns validadores específicos poderiam ser adicionados via annotations
5. **Segurança:** não há autenticação/autorização (Spring Security) — razoável para desafio, mas ausente
6. **Paginação:** lista de tabelas não é paginada (Spring Data Pageable fácil de adicionar)
7. **Query customizada do repositório:** `findAllByNomeCategoriaAndTabelaVigente` pode ser mais eficiente com fetch joins (problema N+1 potencial em cenários de muitas faixas)

### Pontos de atenção no código
- O correction de bug no `CalculoService` (deltaFaixa +1 para faixas que não começam em 0) sugere uma edge case que pode ser melhor modelada
- `TabelaTarifariaCategoriaId` é criado manualmente (`new TabelaTarifariaCategoriaId()`) sem estratégia de geração explícita — pode causar conflitos se múltiplas threads criarem tabelas simultaneamente

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Commits iniciais:** criação do projeto Spring Boot, modelagem, controllers, services, repositories, Docker
- **Atividade recente:** última atualização em 2026-02-08

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original: https://github.com/lucaslimacodes/desafio-tecnico-ras/branches e https://github.com/lucaslimacodes/desafio-tecnico-ras/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Diagramas atualizados:** se a modelagem mudar, atualizar `modelagem_banco.png` e esta página
- **Novos endpoints:** documentar com tabela de métodos, request/response DTOs
- **Mudanças de configuração:** registrar novas propriedades ou mudanças de profile
- **Decisões de modelo:** registrar escolhas como ID composto, cascade, etc.
- **Histórico:** manter log.md atualizado com data e descrição da mudança

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/desafio-tecnico-ras
- **Branch main:** https://github.com/lucaslimacodes/desafio-tecnico-ras/tree/main
- **Swagger UI (produção):** https://desafio-tecnico-ras.onrender.com/swagger-ui/index.html
- **Documentação Spring Boot:** https://docs.spring.io/spring-boot/docs/current/reference/html/
- **Spring Data JPA:** https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
- **SpringDoc OpenAPI:** https://springdoc.org/
- **PostgreSQL Docker:** https://hub.docker.com/_/postgres
- **Render:** https://render.com/
- **Supabase:** https://supabase.com/

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15 22:46 UTC*
