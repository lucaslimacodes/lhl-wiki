# Currency-neurotech-challenge

> Wiki detalhada do repositório **Currency-neurotech-challenge** — API de Câmbio BRL/USD (Neurotech Challenge)

- **Repositório original:** https://github.com/lucaslimacodes/Currency-neurotech-challenge
- **Branch principal:** `main` (Spring Boot 3.4.2 + Java 21 + H2)
- **Deploy local:** `docker-compose up` — API na porta 8080, Frontend na porta 8081
- **Documentação Swagger:** http://localhost:8080/swagger-ui/index.html
- **Linguagem principal:** Java (Spring Boot)
- **Frontend:** Node.js + Chart.js
- **Criado em:** 2026-09-15
- **Wiki deste repositório:** [repos/Currency-neurotech-challenge/](./Currency-neurotech-challenge/) (esta pasta)

---

## Visão Geral

Esta API foi desenvolvida como solução para o **desafio técnico da Neurotech** no processo seletivo para Desenvolvedor de Sistemas Jr. O objetivo é criar uma API REST com Spring Boot para consulta de **cotações do dólar americano (USD) em reais (BRL)**, permitindo:

1. **Consulta da cotação mais recente** — retorna o último rateio disponível no banco
2. **Consulta por intervalo de datas** — retorna todas as cotações entre duas datas, preenchendo automaticamente gaps com valores zero
3. **População automática do banco** — ao iniciar, fetacha dados do Banco Central do Brasil (BCB) de 01/01/2010 até o dia atual
4. **Frontend visual** — gráfico de linhas com Chart.js para visualização das cotações

A aplicação segue uma arquitetura em camadas clássica (Controllers → Services → Repositories) com Spring Data JPA, H2 como banco em memória, documentação automática com SpringDoc OpenAPI (Swagger UI), e deploy via Docker Compose.

---

## Estrutura do Projeto

```
Currency-neurotech-challenge/
├── docker-compose.yaml                  # Orquestração: API + Frontend
├── README.md                            # README original
├── Resources/
│   └── Images/
│       ├── entityRelationshipDiagram.png    # Diagrama MER
│       ├── ApiArchitecture.png              # Diagrama de arquitetura
│       ├── frontEndSample.png               # Screenshot do frontend
│       └── sampleSwagger.png                # Screenshot do Swagger UI
├── currencyAPI/                         # Backend — API Spring Boot
│   ├── Dockerfile                       # Multi-stage build (Maven → JRE)
│   ├── pom.xml                          # Maven: Spring Boot 3.4.2, Java 21
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/neurotech/currencyAPI/
│   │   │   │   ├── CurrencyApiApplication.java     # Ponto de entrada
│   │   │   │   ├── Controller/
│   │   │   │   │   └── CambioController.java       # REST endpoints
│   │   │   │   ├── Service/
│   │   │   │   │   └── CambioService.java          # Lógica de negócio
│   │   │   │   ├── Repository/
│   │   │   │   │   └── CambioRepository.java       # Spring Data JPA
│   │   │   │   ├── model/
│   │   │   │   │   └── Cambio.java                 # Entidade JPA (PK = dataCambio)
│   │   │   │   ├── DBPopulator/
│   │   │   │   │   ├── DBPopulator.java            # Populator (ApplicationReadyEvent)
│   │   │   │   │   ├── Cotacao.java               # DTO de cotação do BCB
│   │   │   │   │   └── CotacaoResponse.java        # DTO de resposta do BCB
│   │   │   │   ├── Exception/
│   │   │   │   │   ├── CambioNotFoundException.java
│   │   │   │   │   └── DateNotValidException.java
│   │   │   │   ├── Infra/
│   │   │   │   │   ├── CambioExceptionHandler.java  # @ControllerAdvice
│   │   │   │   │   └── ErrorResponse.java           # DTO de erro padronizado
│   │   │   │   ├── Configurations/
│   │   │   │   │   └── ApiConfigurer.java          # CORS (localhost:8081)
│   │   │   │   └── Utils/
│   │   │   │       ├── DateUtils.java              # Utilitários de data
│   │   │   │       └── Pair.java                   # Tupla genérica
│   │   │   └── resources/
│   │   │       └── application.properties          # Config: H2, console, JPA
│   │   └── test/
│   │       └── java/com/neurotech/currencyAPI/
│   │           ├── CurrencyApiApplicationTests.java   # Contexto Spring
│   │           ├── Controller/
│   │           │   └── CambioControllerTest.java      # @WebMvcTest
│   │           ├── Service/
│   │           │   └── CambioServiceTest.java         # MockitoExtension
│   │           └── Repository/
│   │               └── CambioRepositoryTest.java      # @DataJpaTest
│   └── target/                                          # Artefato jar
└── front/                              # Frontend — Node.js + Chart.js
    ├── Dockerfile                       # Node alpine + http-server
    ├── package.json                    # Dependências: chart.js, http-server
    ├── index.html                      # Página única com gráfico e conversor
    ├── script.js                       # Lógica: fetch API, plot gráfico, conversão
    ├── styles.css
    └── package-lock.json
```

---

## Stack Tecnológica

| Componente | Tecnologia | Versão/Observação |
|------------|-----------|-------------------|
| **Linguagem** | Java | 21 |
| **Framework** | Spring Boot | 3.4.2 (parent pom) |
| **Persistence** | Spring Data JPA + Hibernate | H2 Dialect |
| **Banco de dados** | H2 | In-memory (`jdbc:h2:mem:db`) |
| **Build** | Maven | `mvn clean package` |
| **Documentação API** | SpringDoc OpenAPI | 2.8.4 (Swagger UI) |
| **Testes** | Spring Boot Test + JUnit 5 + Mockito | Contexto + controller + service + repository |
| **DevTools** | spring-boot-devtools | Runtime (reinicio automático) |
| **Containerização** | Docker + Docker Compose | Multi-stage builds |
| **Frontend** | Node.js + http-server + Chart.js | Chart.js 4.4.7 |
| **Lombok** | lombok | @Data, @Getter, @Setter, @AllArgsConstructor |

### Dependências Maven (pom.xml)

```xml
<!-- Spring Boot Starters -->
spring-boot-starter-web        # Spring MVC (REST)
spring-boot-starter-data-jpa   # Spring Data JPA + Hibernate
spring-boot-devtools           # DevTools (runtime, optional)
spring-boot-starter-test       # Testes (JUnit 5, Mockito)

<!-- Database -->
h2                            # H2 Database (runtime)

<!-- Documentation -->
springdoc-openapi-starter-webmvc-ui  # Swagger UI (2.8.4)

<!-- Utils -->
lombok (annotationProcessor)
```

> **Observação:** Não há dependência explícita de `spring-boot-starter-validation` — validação de datas é feita manualmente no serviço com `SimpleDateFormat`.

---

## Modelagem de Dados

### Diagrama Entidade-Relacionamento (MER)

![Modelagem](https://raw.githubusercontent.com/lucaslimacodes/Currency-neurotech-challenge/main/Resources/Images/entityRelationshipDiagram.png)

*(fonte: `Resources/Images/entityRelationshipDiagram.png` no repositório original)*

### Entidades

#### Cambio

> Entidade única da aplicação. Representa uma cotação diária do dólar.

| Campo | Tipo | Restrição | Descrição |
|-------|------|-----------|-----------|
| `dataCambio` | `java.util.Date` | **PK**, `@Column(name="dataCambio")` | Data da cotação (yyyy-MM-dd). Chave primária. |
| `cotacaoCompra` | `float` | **NOT NULL** | Cotação de compra (BRL por USD, invertida do BCB) |
| `cotacaoVenda` | `float` | **NOT NULL** | Cotação de venda (BRL por USD, invertida do BCB) |

**Observações de modelagem:**
- `dataCambio` é a **chave primária** — uma vez que cada cotação é única por dia, isso elimina necessidade de ID auto-incremento
- O valor é **invertido** no DBPopulator: o BCB retorna USD-based (1 USD = X BRL), e a API armazena BRL-based (1 BRL = Y USD), invertendo com `1.0f / cotacao`
- Não há relacionamentos — é uma entidade isolada (single-table)
- Usa `java.util.Date` (não `LocalDate`) — escolha que afeta a estratégia de persistência e consultas

**`./currencyAPI/src/main/java/com/neurotech/currencyAPI/model/Cambio.java`:**
```java
@Entity
@Table(name = "Cambio")
public class Cambio {
    @Id
    @Column(name = "dataCambio")
    private Date dataCambio;

    @Column(name = "cotacaoCompra", nullable = false)
    private float cotacaoCompra;

    @Column(name = "cotacaoVenda", nullable = false)
    private float cotacaoVenda;
}
```

---

## API REST (Endpoints)

### CambioController (`/` — raiz do contexto)

| Método | Caminho | Descrição | Status |
|--------|---------|-----------|--------|
| **GET** | `/latest` | Retorna a cotação mais recente no banco | 200 OK / 404 |
| **GET** | `/interval` | Retorna lista de cotações entre duas datas | 200 OK / 400 / 404 |

#### GET `/latest` — Cotação Mais Recente

Retorna o último `Cambio` cadastrado, ordenado por `dataCambio DESC`.

**Response (200):**
```json
{
  "dataCambio": "2024-01-10T00:00:00.000Z",
  "cotacaoCompra": 0.175,
  "cotacaoVenda": 0.180
}
```

**Response (404 — banco vazio):**
```json
{
  "message": "latest cambio was not found",
  "status": "NOT_FOUND"
}
```

**Implementação no controller:**
```java
@GetMapping("/latest")
public ResponseEntity<Cambio> getLatest() {
    Cambio latest = cambioService.getLatestCambio();
    return new ResponseEntity<>(latest, HttpStatus.OK);
}
```

#### GET `/interval` — Intervalo de Cotações

Retorna todas as cotações entre `startDate` e `endDate` (formato `yyyy-MM-dd`). Se houver **gaps** (dias sem cotação), o serviço preenche automaticamente com instâncias `Cambio` com `cotacaoCompra = 0` e `cotacaoVenda = 0`.

**Query Parameters:**
- `startDate` (obrigatório): data inicial no formato `yyyy-MM-dd`
- `endDate` (obrigatório): data final no formato `yyyy-MM-dd`

**Response (200):**
```json
[
  {
    "dataCambio": "2024-01-01T00:00:00.000Z",
    "cotacaoCompra": 0.175,
    "cotacaoVenda": 0.180
  },
  {
    "dataCambio": "2024-01-02T00:00:00.000Z",
    "cotacaoCompra": 0.0,
    "cotacaoVenda": 0.0
  },
  ...
]
```

**Regras de validação de datas (`CambioService.validateDateString`):**
1. Formato deve ser `yyyy-MM-dd` (usando `SimpleDateFormat` com `setLenient(false)`)
2. Nenhuma data pode ser no futuro
3. `startDate` deve ser menor ou igual a `endDate`

**Response (400 — data inválida):**
```json
{
  "message": "Date format is not valid",
  "status": "BAD_REQUEST"
}
```

**Response (404 — intervalo vazio):**
```json
{
  "message": "cambio list is empty",
  "status": "NOT_FOUND"
}
```

### Lógica de preenchimento de gaps (`fillGapsOfInterval`)

O serviço preenche gaps em três passos:
1. **Início da lista:** enquanto houver diferença ≥ 1 dia entre `startDate` e o primeiro registro, insere registo com dia anterior e valores 0
2. **Fim da lista:** enquanto houver diferença ≥ 1 dia entre o último registro e `endDate`, insere registo com dia seguinte e valores 0
3. **Meio da lista:** para cada par consecutivo onde a diferença > 1 dia, insere um registo entre eles com valores 0

Isso garante que o frontend receba um array contínuo de datas para plotar o gráfico.

---

## DBPopulator

### Visão Geral

O `DBPopulator` é um componente Spring que implements `ApplicationListener<ApplicationReadyEvent>`. Isso significa que **executa automaticamente após o contexto Spring estar pronto**, antes de qualquer requisição externa.

### Fonte de dados

- **API do Banco Central do Brasil:** `https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo`
- **Período:** 01/01/2010 até o dia atual (configurável via `startDate`)
- **Formato:** OData JSON (`$format=json`)

### Fluxo de execução

```
ApplicationReadyEvent
    ↓
DBPopulator.onApplicationEvent()
    ↓
Constrói URL OData com startDate='01-01-2010' e endDate='MM-dd-yyyy' (hoje)
    ↓
RestTemplate.getForObject(url, CotacaoResponse.class)
    ↓
CotacaoResponse contém List<Cotacao>
    ↓
Para cada Cotacao:
    - Inverte valor: cotacaoCompra = 1.0f / cotacao.getCotacaoCompra()
    - Parsing da data: substring(0,11) + SimpleDateFormat("yyyy-MM-dd").parse()
    - new Cambio(date, cotacaoCompraInvertido, cotacaoVendaInvertida)
    - cambioRepository.save(cambio)
```

### DTOs do Populator

#### Cotacao.java
```java
@Getter @Setter
public class Cotacao {
    @JsonProperty("cotacaoCompra")
    private float cotacaoCompra;    // 1 USD = X BRL (valor original do BCB)

    @JsonProperty("cotacaoVenda")
    private float cotacaoVenda;     // 1 USD = Y BRL

    @JsonProperty("dataHoraCotacao")
    private String dataCotacao;     // String no formato "yyyy-MM-dd HH:mm:ss"
}
```

#### CotacaoResponse.java
```java
@Getter @Setter
public class CotacaoResponse {
    @JsonProperty("value")
    private List<Cotacao> cotacoes;  // Lista de cotações retornada pelo BCB
}
```

### Configurabilidade

A variável `startDate` é hardcoded como `"'01-01-2010'"` dentro da classe. Para alterar o período, modifica-se o valor dessa variável em `DBPopulator.java` e reinicia-se a aplicação.

> **Observação:** O DBPopulator roda a cada inicialização — como o banco é in-memory (H2), os dados são perdidos ao parar o container. Em ambiente real, seria necessário persistência em disco ou um banco externo.

### Endpoints externos consumidos

| API | URL |
|-----|-----|
| Banco Central do Brasil — PTAX Dólar | `https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo` |

---

## Frontend

### Visão Geral

Frontend simples, single-page, implementado com HTML + CSS + JavaScript puro, usando:
- **http-server** (Node.js) para servir arquivos estáticos na porta 8081
- **Chart.js** (v4.4.7 via CDN) para plotar gráfico de linhas

### Funcionalidades

1. **Gráfico de cotações:** linha do tempo com duas séries (Compra em azul, Venda em vermelho)
2. **Conversor BRL → USD:** converte valor em reais usando a cotação mais recente (compra e venda)

### Arquitetura do frontend

```
index.html          → estrutura HTML + inputs de data + canvas Chart.js
    ↓
script.js           → lógica:
    - buscarDados(): fetch GET /interval?startDate=...&endDate=...
      → plotarGrafico(dados): Chart.js line chart
    - obterCotacaoAtual(): fetch GET /latest
      → conversor Valor(): BRL × cotacao = USD
```

### Fluxo de conversão

O conversor usa a cotação de **compra** e **venda** independentemente:
- `valorCompra = valorReais × cotacaoCompra`
- `valorVenda = valorReais × cotacaoVenda`

Isso reflete a realidade de que, ao comprar USD, você usa a cotação de compra (mais cara), e ao vender USD, você usa a cotação de venda (mais barata).

### CORS

A API permite requisições do frontend via `ApiConfigurer`:
```java
@Configuration
public class ApiConfigurer {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowedOrigins("http://localhost:8081");
            }
        };
    }
}
```

> **Observação:** O CORS está restrito a `http://localhost:8081` — não há suporte para outras origens sem alteração na config.

---

## Deployment

### Docker Compose (local)

```yaml
version: '3.1'
services:
  currency-api:
    build: currencyAPI/
    ports:
      - 8080:8080

  front:
    build: front/
    ports:
      - 8081:8081
    depends_on:
      - currency-api
```

**Comandos:**
```bash
# Subir tudo (build + run)
docker-compose up --build

# Acessar API Swagger UI
open http://localhost:8080/swagger-ui/index.html

# Acessar Frontend
open http://localhost:8081

# Parar
docker-compose down

# Parar + remover containers (mantém volumes)
docker-compose down
```

### Dockerfile da API (multi-stage)

```dockerfile
# Stage 1: Build
FROM maven:3.9.6-eclipse-temurin-21 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:21-jdk AS runtime
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

### Dockerfile do Frontend

```dockerfile
FROM node:alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8081
CMD ["npm", "start"]
```

> **Observação:** O `npm start` executa `http-server -p 8081` conforme definido no `package.json`.

---

## Configurações (application.properties)

```properties
spring.application.name=currencyAPI
spring.datasource.url=jdbc:h2:mem:db
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
```

| Propriedade | Valor | Observação |
|-------------|-------|------------|
| `spring.application.name` | `currencyAPI` | Nome do contexto Spring |
| `spring.datasource.url` | `jdbc:h2:mem:db` | Banco H2 em memória |
| `spring.datasource.username` | `sa` | Usuário padrão H2 |
| `spring.datasource.password` | `password` | Senha padrão H2 |
| `spring.jpa.database-platform` | `H2Dialect` | Dialeto Hibernate para H2 |
| `spring.h2.console.enabled` | `true` | Habilita console H2 (acesso via `/h2-console`) |

> **Atenção:** Não há profiles definidos (não há `application-dev.properties` ou `application-prod.properties`). Todos os ambientes usam as mesmas configurações em memória.

---

## Testes

### Overview

O projeto possui **4 classes de teste** cobrindo controller, service, repository e contexto de aplicação.

| Classe de Teste | Tipo | Framework | Cobertura |
|-----------------|------|-----------|-----------|
| `CurrencyApiApplicationTests` | Contexto Spring | `@SpringBootTest` | Carregamento do contexto |
| `CambioControllerTest` | Controller (mock) | `@WebMvcTest` + MockMvc + Mockito | GET /latest e GET /interval (sucesso + erro) |
| `CambioServiceTest` | Service (unitário) | `MockitoExtension` + AssertJ | getLatest, getInterval (com e sem gaps), validação de datas |
| `CambioRepositoryTest` | Repository (integração H2) | `@DataJpaTest` + EntityManager | Queries, intervalos, casos limite |

### Detalhes das classes

#### CurrencyApiApplicationTests
```java
@SpringBootTest
class CurrencyApiApplicationTests {
    @Test
    void contextLoads() {}
}
```
- Teste mínimo que só verifica se o contexto Spring carrega sem erros.

#### CambioControllerTest (`@WebMvcTest`)
- **Mocks:** `CambioService` via `@MockitoBean`
- **Testes:**
  1. `getLatestCambioSuccess`: retorno 200 com body Cambio válido
  2. `getLatestCambioFail`: retorno 404 com ErrorResponse
  3. `getCambioIntervalSuccess`: retorno 200 com lista de 10 Cambios
  4. `getCambioIntervalFail`: retorno 404 para lista vazia

- Usa `MockMvc` para simular requisições HTTP e validar status + JSON response.

#### CambioServiceTest (`MockitoExtension`)
- **Mocks:** `CambioRepository`
- **Testes:**
  1. `getLatestCambioSuccess`: retorna Cambio com dia 10/jan/2024
  2. `getLatestCambioFail`: lança `CambioNotFoundException`
  3. `getCambioIntervalSuccessNoGaps`: retorna 10 registros contíguos
  4. `getCambioIntervalSuccessWithGapsFilled`: insere 15 gaps, resulta em 30 registros, alternando zero/non-zero
  5. `getCambioIntervalFail`: lança `CambioNotFoundException` para lista vazia
  6. `getCambioIntervalInvalidDate`: lança `DateNotValidException` para formato inválido e para start > end

- Usa AssertJ (`assertThat`) para assertions mais expressivas.

#### CambioRepositoryTest (`@DataJpaTest`)
- Usa `EntityManager` para persistir entidades e `CambioRepository` para consultar
- **Testes:**
  1. `getLatestCambioSuccess`: persiste 2 cambios, verifica que o mais recente é retornado
  2. `getLatestCambioFail`: banco vazio → optional vazio → `NoSuchElementException` ao chamar `.get()`
  3. `getCambiosIntervalSuccess`: persiste 10 registros jan/2024, consulta subintervalos, verifica contagem e dias
  4. `getCambiosIntervalFail`: pesquisa em intervalo sem dados → lista vazia

---

## Qualidade e Observações

### Pontos positivos
- Arquitetura em camadas clara e coesa (Controller → Service → Repository)
- Swagger UI completo com `@Operation`, `@ApiResponses` e `@Parameter`
- Tratamento global de exceções com `@ControllerAdvice` e DTO padronizado (`ErrorResponse`)
- Populator automático que garante dados reais de mercado sem intervenção manual
- Preenchimento automático de gaps — decisão de design que simplifica o frontend
- Testes cobrindo controller, service (incluindo gaps), repository e contexto
- Dockerfile multi-stage para API (Maven → JRE) e frontend (Node alpine)
- CORS configurado explicitamente para o frontend

### Possíveis melhorias (technical debt)
1. **Chave primária como `Date`:** usar `Date` como `@Id` pode causar problemas com fusos horários e comparações — o ideal seria `LocalDate` ou um ID auto-incremento com unique constraint na data
2. **`float` para cotações financeiras:** imprecisão de ponto flutuante — usar `BigDecimal` para valores monetários
3. **`SimpleDateFormat` não thread-safe:** usado no service e no populator — pode causar problemas em concorrência; usar `DateTimeFormatter` (Java Time) ou criar instância local por chamada
4. **DBPopulator bloqueia startup:** executa no `ApplicationReadyEvent` fazendo HTTP externo — se o BCB estiver indisponível, a aplicação fica com banco vazio e endpoints retornam 404. Idealmente, tratar falha e permitir inicialização com banco vazio
5. **Dados perdidos ao parar:** H2 em memória (`mem:db`) perde dados ao reiniciar — para ambiente real, usar `jdbc:h2:file:...` ou banco externo
6. **CORS restrictivo:** apenas `localhost:8081` — não há configuração para outros ambientes (prod, staging)
7. **Sem paginação:** endpoint `/interval` pode retornar milhares de registros sem limitação
8. **Sem validação Bean Validation:** validação de datas feita manualmente com `SimpleDateFormat` — poderia usar `@Pattern` ou `@DateTimeFormat` nos parâmetros
9. **Sem testes de integração com DBPopulator:** não há teste que verifique o populator populando o banco corretamente
10. **Hardcoded startDate:** data de início hardcoded no código — idealmente via `application.properties` ou variável de ambiente

### Pontos de atenção no código
- `fillGapsOfInterval` modifica a lista original (`cambios.addFirst/adLast/add`) — pode ser confundido com comportamento in-place não documentado
- `DBPopulator` faz `System.exit(1)` em caso de erro de parsing de data — aborta전체 processo, não apenas registra o erro
- `CambioRepository.getLatestCambio()` usa subquery `MAX(d.dataCambio)` — funciona, mas pode ser substituído por `findTopByOrderByDataCambioDesc` (não está implementado)

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Comits iniciais:** criação do projeto Spring Boot, modelagem, controllers, services, repositories, DBPopulator, docker-compose, frontend
- **Recursos:** imagens de documentação (MER, arquitetura, screenshot frontend, Swagger) em `Resources/Images/`

> **Nota:** Detalhes exatos de commits e branches podem ser verificados diretamente no repositório original: https://github.com/lucaslimacodes/Currency-neurotech-challenge/branches e https://github.com/lucaslimacodes/Currency-neurotech-challenge/commits/main

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada estabelecido para o projeto **lhl-wiki**. As próximas seções (se adicionadas) devem incluir:

- **Atualizações de modelagem:** se a entidade `Cambio` mudar, atualizar esta seção e o diagrama MER
- **Novos endpoints:** documentar com tabela de métodos, request/response
- **Mudanças de configuração:** registrar novos profiles, propriedades ou variáveis de ambiente
- **Decisões técnicas:** registrar escolhas como `Date` como PK, `float` para cotações, etc.
- **Histórico:** manter log das mudanças com data e descrição

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/Currency-neurotech-challenge
- **Branch main:** https://github.com/lucaslimacodes/Currency-neurotech-challenge/tree/main
- **Swagger UI (local):** http://localhost:8080/swagger-ui/index.html
- **Frontend (local):** http://localhost:8081
- **API do Banco Central (PTAX):** https://dadosabertos.bcb.gov.br/dataset/dolar-americano-usd-todos-os-boletins-diarios/resource/ada8e867-7e1f-498b-aa54-97c6a523e8d5
- **Documentação Spring Boot:** https://docs.spring.io/spring-boot/docs/current/reference/html/
- **Spring Data JPA:** https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
- **SpringDoc OpenAPI:** https://springdoc.org/
- **H2 Database:** https://h2database.com/
- **Chart.js:** https://www.chartjs.org/
- **Docker:** https://www.docker.com/

---

*Wiki detalhada gerada automaticamente por Hermes Agent (Upstage Solar Pro) com análise estática do código-fonte.*
*Última atualização: 2026-09-15*
