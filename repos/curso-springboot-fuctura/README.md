# curso-springboot-fuctura

> Wiki detalhada do repositório **curso-springboot-fuctura** — Materiais do Curso de Spring Boot da Fuctura (Instrutor: Bergson Barros)

- **Repositório original:** https://github.com/lucaslimacodes/curso-springboot-fuctura
- **Branch principal:** `main`
- **Última atualização do repo:** 2022-09-03 (última aula)
- **Criado em:** 2022-07-23 (primeira aula)
- **Total de arquivos:** 36 (13 .zip, 8 .pdf, 7 .md, 4 .png, 2 .json, 2 .pptx — contagem aproximada)
- **Tamanho total:** ~8.5 MB
- **Wiki deste repositório:** [repos/curso-springboot-fuctura/](./curso-springboot-fuctura/) (esta pasta)

---

## Visão Geral

Repositório com os **materiais completos do curso de Spring Boot** ministrado por **Bergson Barros** para a **Fuctura**,compostos por 6 aulas + aula de apresentação, ocorridas entre **23 de julho e 3 de setembro de 2022**.

### Conteúdo programático

| Aula | Data | Tópico principal |
|------|------|------------------|
| Aula 00 | 23/07/2022 | Apresentação do curso (slides) |
| Aula 01 | 30/07/2022 | Introdução ao Spring Boot, projeto escola |
| Aula 02 | 06/08/2022 | Modelagem, DTO, JPA, REST básico |
| Aula 03 | 13/08/2022 | Spring Data JPA, Controller completo (CRUD), Bean Validation |
| Aula 04 | 20/08/2022 | Paginação, Ordenação, Cache |
| Aula 05 | 27/08/2022 | Actuator, Spring Security, Spring Boot Admin, Swagger/OpenAPI, Lombok |
| Aula 06 | 03/09/2022 | Thymeleaf, Relatórios PDF, Query Methods, @Query, Named Queries |

### Arquitetura do projeto-padrão

O curso constrói progressivamente uma **API REST de gestão de escola** (`EscolaApi`), com as seguintes camadas:

```
EscolaApi/
├── br.com.fuctura.escola.model      # Entidades JPA (Aluno, Professor, Curso, Turma, Matricula)
├── br.com.fuctura.escola.dto        # DTOs (AlunoDto, DetalhesDoAlunoDto, AlunoForm, AtualizacaoAlunoForm)
├── br.com.fuctura.escola.repository # Repositórios Spring Data JPA
├── br.com.fuctura.escola.controller # Controladores REST
├── br.com.fuctura.escola.controller.form  # Form objects para validação
├── br.com.fuctura.escola.services   # Services (AlunoServices)
├── br.com.fuctura.escola.report     # Exportação PDF (AlunoPDFExporter)
├── br.com.fuctura.escola.config     # Configurações (Swagger, Security)
└── br.com.fuctura.escola.config.security  # SecurityConfigurations
```

O banco utilizado é **H2 em memória** (configuração padrão), com console habilitado em `/h2-console`.

---

## Estrutura do Repositório

```
curso-springboot-fuctura/
├── README.md                                    # Este arquivo (wiki detalhada)
│
├── Aula00 - 2022-07-23/
│   ├── Aula00 - Spring Boot - Bergson - 2022-07-23.pptx   # Slides da apresentação
│   └── Aula 00 - Spring Boot - Bergson - 2022-07-23.pdf   # PDF dos slides
│
├── Aula01 - 2022-07-30/
│   ├── Aula 01 - 30_07_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── projeto-escola-aula-20220723.zip                   # Projeto inicial (starter)
│   └── README.md                                          # Brevidade: "Material da Aula 01"
│
├── Aula02 - 2022-08-06/
│   ├── Aula 02 - 06_08_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── escola-model.zip                                   # Modelo do projeto
│   └── README.md                                          # Detalhamento da aula 02
│
├── Aula03 - 2022-08-13/
│   ├── Aula 03 - 13_08_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── escola-control-api.postman_collection.json         # Coleção Postman
│   ├── escola-model.zip                                   # Modelo do projeto
│   ├── spring-boot-pratica-aula03.zip                    # Exercício prático
│   └── README.md                                          # Detalhamento completo da aula 03 (CRUD)
│
├── Aula04 - 2022-08-20/
│   ├── Aula 04 - 20_08_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── escola-control-api.postman_collection.json         # Coleção Postman atualizada
│   ├── escola-model.zip                                   # Modelo do projeto
│   ├── spring-boot-pratica-iniciar-aula.zip              # Início do exercício
│   ├── spring-boot-pratica-aula4.zip                     # Exercício prático
│   └── README.md                                          # Detalhamento: paginação, ordenação, cache
│
├── Aula05 - 2022-08-27/
│   ├── Aula 05 - 27_08_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── Aula05-inicio.zip                                 # Projeto inicial da aula
│   ├── pratica-aula5.zip                                 # Exercício prático
│   ├── spring-boot-admin.zip                             # Projeto Spring Boot Admin
│   ├── lombok-sucesso.png                                # Screenshot: instalação Lombok OK
│   ├── lombok-sts-OK.png                                 # Screenshot: Lombok no STS
│   └── README.md                                          # Detalhamento: Actuator, Security, Admin, Swagger, Lombok
│
├── Aula06 - 2022-09-03/
│   ├── Aula 06 - 03_09_2022 - Spring Boot - Bergson.pdf   # PDF da aula
│   ├── Aula06-projeto-API-Escola.pdf                     # Documento do projeto final
│   ├── data.sql                                          # Seed SQL completo (Aluno, Professor, Curso, Turma, Matricula)
│   ├── escola-api-projeto-completo.zip                   # Projeto completo da API Escola
│   ├── pratica-aula6-inicio.zip                         # Início do exercício
│   ├── tela-boas-vindas.png                              # Screenshot: tela de boas-vindas Thymeleaf
│   └── relatorio.png                                     # Screenshot: relatório PDF
│
└── .git/                                                # Metadados do repositório
```

---

## Stack Tecnológica

| Componente | Tecnologia | Observação |
|------------|-----------|------------|
| **Linguagem** | Java | Versão não explicitada no repo (provável Java 8-11, dados as dependências) |
| **Framework** | Spring Boot | Versão não explicitada (dependências: spring-boot-starter-web, data-jpa, etc.) |
| **Build** | Maven | `pom.xml` (dependências·박om padrão) |
| **Banco de dados** | H2 | Em memória (`jdbc:h2:mem:escola-controle-api`) |
| **ORM** | Spring Data JPA + Hibernate | `ddl-auto=update` |
| **Documentação API** | SpringDoc OpenAPI (Swagger UI) | Versão 1.6.11 (`springdoc-openapi-ui`) |
| **Validação** | Bean Validation | `spring-boot-starter-validation` |
| **Monitoramento** | Spring Actuator + Spring Boot Admin | Actuator 2.x, SBA 2.6.6 (server) / 2.5.1 (client) |
| **Segurança** | Spring Security | Configuração básica (permitAll, CSRF desabilitado) |
| **Templating** | Thymeleaf | `spring-boot-starter-thymeleaf` |
| **Relatórios** | OpenPDF | `com.github.librepdf:openpdf:1.3.8` |
| **Cache** | Spring Cache abstraction | `spring-boot-starter-cache` |
| **Produtividade** | Lombok | Versão 1.18.24 (`provided`) |
| **IDE recomendada** | Spring Tool Suite (STS) / Eclipse | Com Lombok instalado via jar |
| **Cliente HTTP** | Postman | Coleções `.json` inclusas no repo |

### Dependências Maven (inferidas dos READMEs)

```xml
<!-- Spring Boot Starters -->
spring-boot-starter-web         # REST MVC
spring-boot-starter-data-jpa    # JPA + Hibernate
spring-boot-starter-validation  # Bean Validation
spring-boot-starter-cache       # Cache abstraction
spring-boot-starter-actuator    # Actuator (Aula 05)
spring-boot-starter-security    # Spring Security (Aula 05)
spring-boot-starter-thymeleaf   # Thymeleaf (Aula 06)

<!-- Documentação -->
springdoc-openapi-ui            # 1.6.11 — Swagger UI

<!-- Monitoramento -->
spring-boot-admin-starter-server  # 2.6.6 — SBA Server (projeto separado)
spring-boot-admin-starter-client  # 2.5.1 — SBA Client (na API Escola)

<!-- Relatórios -->
openpdf                         # 1.3.8 — geração PDF

<!-- Lombok -->
lombok                          # 1.18.24, scope provided

<!-- DevTools (habitual no curso) -->
spring-boot-devtools            # reinício automático
```

---

## Conteúdo Programático por Aula

### Aula 00 — 23/07/2022 — Apresentação do Curso

**Material:** Slides em PPTX + PDF (`Aula00 - Spring Boot - Bergson - 2022-07-23`).

Conteúdo introdutório: apresentação do curso, do instrutor (Bergson Barros), objetivos, metodologia e visão geral do que seria Spring Boot.

---

### Aula 01 — 30/07/2022 — Introdução e Projeto Escola

**Material:** PDF da aula + `projeto-escola-aula-20220723.zip` (projeto inicial).

Princípios abordados:
- Criação do projeto Spring Boot (via Spring Initializr ou STS)
- Configuração básica do projeto `EscolaApi`
- Primeiro contato com os pacotes e estrutura do projeto

---

### Aula 02 — 06/08/2022 — Modelagem, DTO e JPA Básico

**Material:** PDF da aula + `escola-model.zip` + README detalhado.

#### Tópicos ensinados

1. **Estrutura de pacotes** (padrão do curso):
   ```
   br.com.fuctura.escola.model
   br.com.fuctura.escola.dto
   br.com.fuctura.escola.repository
   br.com.fuctura.escola.controller
   ```

2. **Classe `Aluno`** (modelo inicial, sem JPA):
   ```java
   public class Aluno {
       private Long id;
       private String cpf;
       private String nome;
       private String email;
       private String fone;
       private String tipo = TipoAluno.CONVENCIONAL.toString();
   }
   ```

3. **Enum `TipoAluno`**:
   ```java
   public enum TipoAluno {
       CONVENCIONAL,
       MONITOR;
   }
   ```

4. **Primeiro `RestController`** — `PrimeiroController`:
   ```java
   @RestController
   @RequestMapping("/primeiro")
   public class PrimeiroController {
       @GetMapping("/listar1")
       public List<Aluno> listar1() {
           // retorna lista em memória de Aluno
       }
   }
   ```
   Endpoint: `GET localhost:8080/primeiro/listar1`

5. **DTO `AlunoDto`** — conversão de `Aluno` para representação enxuta:
   ```java
   public class AlunoDto {
       private String cpf;
       private String nome;
       private String email;
       // construtor a partir de Aluno, getters
   }
   ```
   DTO usado para `listar2()`: `GET localhost:8080/primeiro/listar2`

6. **`application.properties`** — configuração do H2 e JPA:
   ```properties
   spring.datasource.driverClassName=org.h2.Driver
   spring.datasource.url=jdbc:h2:mem:escola-controle-api
   spring.datasource.username=sa
   spring.datasource.password=
   spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
   spring.jpa.hibernate.ddl-auto=update
   spring.jpa.defer-datasource-initialization=true
   spring.h2.console.enabled=true
   spring.h2.console.path=/h2-console
   ```

7. **Classe `Aluno` com anotações JPA**:
   ```java
   @Entity
   @Table
   public class Aluno {
       @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;

       @Column(nullable = false, name = "CPF")
       private String cpf;

       @Column(nullable = false, name = "NOME")
       private String nome;

       @Column(nullable = true, name = "EMAIL")
       private String email;

       @Column(nullable = false, name = "FONE")
       private String fone;

       @Column(nullable = false, name = "TIPO")
       private String tipo = TipoAluno.CONVENCIONAL.toString();
   }
   ```

8. **Seed SQL (`data.sql`)** — 3 registros iniciais:
   ```sql
   INSERT INTO ALUNO (cpf, nome, email, fone, tipo) VALUES
     ('11111111111', 'Huguinho', 'aluno111@escola.com', '81 1234-5555', 'CONVENCIONAL'),
     ('22222222222', 'Zezinho', 'aluno222@escola.com', '81 1234-5555', 'CONVENCIONAL'),
     ('33333333333', 'Luizinho', 'aluno333@escola.com', '81 1234-5555', 'MONITOR');
   ```

---

### Aula 03 — 13/08/2022 — CRUD Completo com Spring Data JPA + Bean Validation

**Material:** PDF da aula +coleção Postman (`escola-control-api.postman_collection.json`) + modelo (`escola-model.zip`) + prática (`spring-boot-pratica-aula03.zip`) + README completo.

#### Tópicos ensinados

**Refinamento dos endpoints:**

- `GET /primeiro/listar1` — lista de `Aluno` (objetos completos)
- `GET /primeiro/listar2` — lista de `AlunoDto` (usando Stream + `.map(AlunoDto::new)`)

**Spring Data JPA na prática:**

- `AlunoRepository extends JpaRepository<Aluno, Long>` — repositórioDAO sem implementação
- `data.sql` poblado (3 alunos iniciais)
- Acesso ao console H2: `http://localhost:8080/h2-console`

**Controlador `AlunosController` — CRUD completo:**

| Operação | Método | Endpoint | Observação |
|----------|--------|----------|------------|
| Listar todos | `GET` | `/alunos` | Retorna `List<AlunoDto>` |
| Buscar por ID | `GET` | `/alunos/{id}` | Retorna `ResponseEntity<DetalhesDoAlunoDto>` (200 ou 404) |
| Cadastrar | `POST` | `/alunos` | `@RequestBody @Valid AlunoForm`, retorna 201 Created |
| Atualizar | `PUT` | `/alunos/{id}` | `@RequestBody @Valid AtualizacaoAlunoForm`, retorna 200 ou 404 |
| Remover | `DELETE` | `/alunos/{id}` | Retorna 200 OK ou 404 |

**DTOs e Form Objects:**

- `AlunoDto` — DTO de saída para listagem
- `DetalhesDoAlunoDto` — DTO de saída com ID incluído
- `AlunoForm` — DTO de entrada para POST, com validação:
  ```java
  @NotNull @NotEmpty @Length(min = 11, max = 11)
  private String cpf;

  @NotNull @NotEmpty @Length(min = 5)
  private String nome;

  @Nullable private String email;
  @Nullable private String fone;
  @Nullable private String tipo;
  ```
- `AtualizacaoAlunoForm` — DTO de entrada para PUT, com campos opcionais

**Bean Validation:**

- Dependência: `spring-boot-starter-validation`
- Anotações usadas: `@NotNull`, `@NotEmpty`, `@Length`, `@Size`, `@Nullable`

**Testando com Postman:**

- Header obrigatório: `Content-Type: application/json`
- Coleção Postman inclusa no repo (`escola-control-api.postman_collection.json`) com exemplos de request para POST e PUT

**Exemplo de POST (cadastro):**
```json
{
  "cpf": "44444444444",
  "nome": "Pato Donald",
  "email": "superduck@escola.com",
  "fone": "81 4444-4444",
  "tipo": "CONVENCIONAL"
}
```

**Exemplo de PUT (atualização):**
```json
{
  "nome": "Huguinho Novo aluno 2022",
  "email": "aluno111-huguinho@escola.com",
  "fone": "81 1234-5555",
  "tipo": "CONVENCIONAL"
}
```

**Classe `AlunosController` completa (resumo):**
```java
@RestController
@RequestMapping("/alunos")
public class AlunosController {

    @Autowired
    private AlunoRepository alunoRepository;

    @GetMapping
    public List<AlunoDto> listaAlunos() {
        return AlunoDto.converter(alunoRepository.findAll());
    }

    @GetMapping("/{id}")
    public ResponseEntity<DetalhesDoAlunoDto> detalhar(@PathVariable Long id) {
        Optional<Aluno> aluno = alunoRepository.findById(id);
        return aluno.map(a -> ResponseEntity.ok(new DetalhesDoAlunoDto(a)))
                    .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    @Transactional
    public ResponseEntity<AlunoDto> cadastrar(@RequestBody @Valid AlunoForm form) {
        Aluno aluno = form.converterDTO();
        alunoRepository.save(aluno);
        return new ResponseEntity<>(new AlunoDto(aluno), HttpStatus.CREATED);
    }

    @PutMapping("/{id}")
    @Transactional
    public ResponseEntity<AlunoDto> atualizar(@PathVariable Long id,
                                                @RequestBody @Valid AtualizacaoAlunoForm form) {
        Optional<Aluno> optional = alunoRepository.findById(id);
        if (optional.isPresent()) {
            Aluno aluno = form.atualizar(id, alunoRepository);
            return ResponseEntity.ok(new AlunoDto(aluno));
        }
        return ResponseEntity.notFound().build();
    }

    @DeleteMapping("/{id}")
    @Transactional
    public ResponseEntity<?> remover(@PathVariable Long id) {
        Optional<Aluno> optional = alunoRepository.findById(id);
        if (optional.isPresent()) {
            alunoRepository.deleteById(id);
            return ResponseEntity.ok().build();
        }
        return ResponseEntity.notFound().build();
    }
}
```

---

### Aula 04 — 20/08/2022 — Paginação, Ordenação e Cache

**Material:** PDF da aula + coleção Postman atualizada + modelo + 2 arquivos de prática + README detalhado.

#### Tópicos ensinados

**1. Melhoria do `listaAlunos` com parâmetro `nomeAluno`:**

```java
@GetMapping
public List<AlunoDto> listaAlunos(@RequestParam(required = false) String nomeAluno) {
    if (nomeAluno == null) {
        return AlunoDto.converter(alunoRepository.findAll());
    } else {
        return AlunoDto.converter(alunoRepository.findByNome(nomeAluno));
    }
}
```

**2. Método derivado no repositório:**
```java
public interface AlunoRepository extends JpaRepository<Aluno, Long> {
    List<Aluno> findByNome(String nomeAluno);
    // outros: findByCpf(String cpf), etc.
}
```

**3. Paginação com Spring Data:**

- Anotação `@EnableSpringDataWebSupport` na classe principal
- Uso de `Pageable` e `Page<T>`:
  ```java
  @GetMapping
  public Page<AlunoDto> listaAlunos(
          @RequestParam(required = false) String nomeAluno,
          @PageableDefault(sort = "id", direction = Direction.ASC, page = 0, size = 10)
          Pageable paginacao) {

      if (nomeAluno == null) {
          return AlunoDto.converter(alunoRepository.findAll(paginacao));
      } else {
          return AlunoDto.converter(alunoRepository.findByNome(nomeAluno, paginacao));
      }
  }
  ```
- Método `converter` em `AlunoDto` adaptado para `Page<AlunoDto>`:
  ```java
  public static Page<AlunoDto> converter(Page<Aluno> alunos) {
      return alunos.map(AlunoDto::new);
  }
  ```
- `AlunoRepository.findByNome` agora retorna `Page<Aluno>`:
  ```java
  Page<Aluno> findByNome(String nomeAluno, Pageable paginacao);
  ```

**Testes de paginação (via Postman/URL):**
```
localhost:8080/alunos
localhost:8080/alunos?page=0&size=5
localhost:8080/alunos?page=1&size=5
localhost:8080/alunos?page=2&size=5
```

**4. Ordenação:**

Passada diretamente na requisição via `sort`:
```
localhost:8080/alunos?size=15&sort=nome,asc
localhost:8080/alunos?size=15&sort=nome,desc
localhost:8080/alunos?size=15&sort=cpf,asc
localhost:8080/alunos?size=15&sort=nome,asc&sort=cpf,desc
```

Para testes, recomenda-se triplicar o `data.sql` (total de 10 registros).

**5. Cache de Consultas:**

- Dependência: `spring-boot-starter-cache`
- Anotação `@EnableCaching` na classe principal
- Anotação `@Cacheable` no método de listagem:
  ```java
  @GetMapping
  @Cacheable(value = "listaDeAlunos")
  public Page<AlunoDto> listaAlunos(...) { ... }
  ```
- Configuração para ver SQL no console:
  ```properties
  spring.jpa.properties.hibernate.show_sql=true
  spring.jpa.properties.hibernate.format_sql=true
  ```
- **Invalidação do cache no POST:**
  ```java
  @PostMapping
  @Transactional
  @CacheEvict(value = "listaDeAlunos", allEntries = true)
  public ResponseEntity<AlunoDto> cadastrar(...) { ... }
  ```

---

### Aula 05 — 27/08/2022 — Actuator, Spring Security, Spring Boot Admin, Swagger/OpenAPI e Lombok

**Material:** PDF da aula + 3 zips (`Aula05-inicio.zip`, `pratica-aula5.zip`, `spring-boot-admin.zip`) + 2 screenshots (instalação Lombok) + README detalhado.

#### Tópicos ensinados

**1. Monitoramento com Spring Actuator:**

- Dependência: `spring-boot-starter-actuator`
- Endpoint disponível: `http://localhost:8080/actuator`
- `actuator/health` — status da aplicação e components (db, diskSpace, ping)
- Configurações no `application.properties`:
  ```properties
  management.endpoint.health.show-details=always
  management.endpoints.web.exposure.include=*
  info.app.name=@project.name@
  info.app.version=@project.version@
  ```

**2. Spring Security básico:**

- Dependência: `spring-boot-starter-security`
- Classe `SecurityConfigurations` (extende `WebSecurityConfigurerAdapter`):
  ```java
  @EnableWebSecurity
  @Configuration
  public class SecurityConfigurations extends WebSecurityConfigurerAdapter {

      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception { }

      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http.authorizeRequests().anyRequest().permitAll()
              .and().csrf().disable();
      }

      @Override
      public void configure(WebSecurity web) throws Exception { }
  }
  ```
- Configuração inicial permite acesso a todos os endpoints sem autenticação ( curso em fase de aprendizado).

**3. Spring Boot Admin:**

- **Servidor SBA:** projeto separado criado no Spring Initializr (`group=br.com.fuctura`, `artifact=spring-boot-admin`)
  - Dependências: `spring-boot-admin-starter-server:2.6.6`, `spring-boot-starter-web`
  - Classe principal anotada com `@Configuration`, `@EnableAutoConfiguration`, `@EnableAdminServer`
  - Porta: `server.port=8081` (para não conflitar com a API Escola na 8080)

- **Cliente SBA (API Escola):**
  - Dependências: `spring-boot-admin-starter-client:2.5.1`, `spring-boot-starter-security`
  - Configuração: `spring.boot.admin.client.url=http://localhost:8081`
  - Após iniciar, a API Escola aparece no dashboard do SBA

- Link do projeto: https://github.com/codecentric/spring-boot-admin

**4. Documentação com Spring Doc OpenAPI (Swagger UI):**

- Dependência: `springdoc-openapi-ui:1.6.11`
- Configuração da URL customizada:
  ```properties
  springdoc.swagger-ui.path=/swagger-ui.html
  ```
- Classe `SwaggerConfigurations`:
  ```java
  @Configuration
  public class SwaggerConfigurations {
      @Bean
      public GroupedOpenApi publicApi() {
          return GroupedOpenApi.builder()
              .group("br.com.fuctura")
              .pathsToMatch("/**")
              .build();
      }
  }
  ```
- No `SecurityConfigurations`, ignorar recursos estáticos do Swagger:
  ```java
  @Override
  public void configure(WebSecurity web) throws Exception {
      web.ignoring().antMatchers(
          "/**.html", "/v2/api-docs", "/webjars/**",
          "/configuration/**", "/swagger-resources/**");
  }
  ```
- Adicionar `@Operation` nos métodos do controlador:
  ```java
  @Operation(summary = "listarAlunos", description = "listar os alunos da escola")
  @Operation(summary = "detalhar", description = "detalha um aluno de acordo com o Id")
  ```

**5. Lombok:**

- Site: https://projectlombok.org/
- Instalação na IDE (STS/Eclipse):
  1. Baixar `lombok.jar`
  2. Executar `java -jar lombok.jar`
  3. "Specify location..." → diretório da IDE
  4. Selecionar `SpringToolSuite4.ini` → "Install/Update"
  5. Reiniciar IDE
  6. Verificar em Help → About STS 4: `Lombok v1.18.24 "Envious Ferret" is installed.`

- Dependência no `pom.xml`:
  ```xml
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.24</version>
      <scope>provided</scope>
  </dependency>
  ```

- Aplicação nas entidades e DTOs:
  ```java
  @Entity
  @Table
  @NoArgsConstructor @AllArgsConstructor
  @Data
  @EqualsAndHashCode @ToString
  public class Aluno implements Serializable {
      // campos...
  }
  ```
- `@Data` substitui getters, setters, `toString`, `equals`, `hashCode`
- `@NoArgsConstructor` + `@AllArgsConstructor` gera construtores

---

### Aula 06 — 03/09/2022 — Thymeleaf, Relatórios PDF, Query Methods

**Material:** PDF da aula + documento do projeto (`Aula06-projeto-API-Escola.pdf`) + `data.sql` expandido + projeto completo (`escola-api-projeto-completo.zip`) + prática + 2 screenshots + README detalhado.

#### Tópicos ensinados

**1. Thymeleaf — páginas web com Spring Boot:**

- Dependência: `spring-boot-starter-thymeleaf`
- `WelcomeController`:
  ```java
  @Controller
  public class WelcomeController {

      private List<String> assuntos = Arrays.asList(
          "Rest", "MVC", "API", "JSON", "Java", "Controller", "JPA");

      @RequestMapping(value = "/", method = RequestMethod.GET)
      public String main(Model model) {
          model.addAttribute("message",
              "Olá Aluno, seja bem vindo ao curso de Spring Boot da Fuctura");
          model.addAttribute("assuntos", assuntos);
          return "welcome";
      }

      @GetMapping("/hello")
      public String mainWithParam(
              @RequestParam(name = "name", required = false, defaultValue = "")
              String name, Model model) {
          model.addAttribute("message",
              "Olá " + name + ", seja bem vindo ao curso de Spring Boot da Fuctura");
          model.addAttribute("assuntos", assuntos);
          return "welcome";
      }
  }
  ```
- Template `src/main/resources/templates/welcome.html`:
  ```html
  <!DOCTYPE HTML>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
      <title>Spring Boot Thymeleaf Fuctura</title>
      <link rel="stylesheet" th:href="@{webjars/bootstrap/4.2.1/css/bootstrap.min.css}"/>
      <link rel="stylesheet" th:href="@{/css/main.css}"/>
  </head>
  <body>
  <main role="main" class="container">
      <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTCpE4j0_9z28bBm16L_pnFlq4ip65HWKlx9-Vg_lzQ&s">
      <div class="starter-template">
          <h1>Curso de Spring Boot da Fuctura</h1>
          <h2><span th:text="'Hello, ' + ${message}"></span></h2>
      </div>
      <ol>
          <li th:each="assunto : ${assuntos}" th:text="${assunto}"></li>
      </ol>
  </main>
  <script type="text/javascript" th:src="@{webjars/bootstrap/4.2.1/js/bootstrap.min.js}"></script>
  </body>
  </html>
  ```
- Configuração: `spring.thymeleaf.cache=false` (para desenvolvimento)

**2. Relatórios em PDF:**

- Dependência: `com.github.librepdf:openpdf:1.3.8`
- `AlunoServices` (`@Service`):
  ```java
  @Service
  @Transactional
  public class AlunoServices {
      @Autowired
      private AlunoRepository repositorioAlunos;

      public List<Aluno> listarTodosAlunos() {
          return repositorioAlunos.findAll(Sort.by("id").ascending());
      }
  }
  ```
- `AlunoPDFExporter` — geração do relatório:
  ```java
  public class AlunoPDFExporter {
      private List<Aluno> listaAlunos;

      public AlunoPDFExporter(List<Aluno> listaAlunos) {
          this.listaAlunos = listaAlunos;
      }

      private void writeTableHeader(PdfPTable table) {
          // Cabeçalho com colunas: ID, Nome, Cpf, E-mail, Fone, Tipo
          // Fonte azul com texto branco
      }

      private void writeTableData(PdfPTable table) {
          // Itera listaAlunos, adiciona células
      }

      public void export(HttpServletResponse response) throws DocumentException, IOException {
          Document document = new Document(PageSize.A4);
          PdfWriter.getInstance(document, response.getOutputStream());
          document.open();

          Font font = FontFactory.getFont(FontFactory.HELVETICA_BOLD);
          font.setSize(18);
          font.setColor(Color.BLUE);
          Paragraph p = new Paragraph("Lista de Alunos", font);
          p.setAlignment(Paragraph.ALIGN_CENTER);
          document.add(p);

          PdfPTable table = new PdfPTable(6);
          table.setWidthPercentage(100f);
          table.setWidths(new float[]{1.5f, 3.5f, 3.0f, 3.0f, 1.5f, 1.5f});
          table.setSpacingBefore(10);

          writeTableHeader(table);
          writeTableData(table);
          document.add(table);
          document.close();
      }
  }
  ```
- Endpoint de exportação no controlador:
  ```java
  @GetMapping("/relatorio-pdf")
  public void exportarRelatorioPDF(HttpServletResponse response)
          throws DocumentException, IOException {
      response.setContentType("application/pdf");
      DateFormat dateFormatter = new SimpleDateFormat("yyyy-MM-dd_HH:mm:ss");
      String currentDateTime = dateFormatter.format(new Date());
      String headerKey = "Content-Disposition";
      String headerValue = "attachment; filename=users_" + currentDateTime + ".pdf";
      response.setHeader(headerKey, headerValue);

      List<Aluno> listaAlunos = alunoService.listarTodosAlunos();
      AlunoPDFExporter exporter = new AlunoPDFExporter(listaAlunos);
      exporter.export(response);
  }
  ```
- Acesso: `http://localhost:8080/alunos/relatorio-pdf`

**3. Repository Query Methods (Spring Data JPA):**

Palavras-chave suportadas para criação automática de queries:
`Distinct`, `And`, `Or`, `Between`, `Is`, `Equal`, etc.

Exemplos:
```java
List<Aluno> findByCpf(String cpf);
List<Aluno> findByEmailAndFone(String email, String fone);
List<Aluno> findByEmailOrFone(String email, String fone);
```

**4. Consultas com `@Query` (JPQL):**
```java
@Query("select a from Aluno a where a.email = ?1")
List<Aluno> findByEmail(String email);

@Query("select a from Aluno a where a.fone = ?1")
List<Aluno> findByFone(String fone);
```

**5. Named Queries:**
```java
@NamedQuery(name = "Aluno.findByEmail", query = "select a from Aluno a where a.email = ?1")
@NamedQuery(name = "Aluno.findByFone",  query = "select a from Aluno a where a.fone = ?1")
@NamedQuery(name = "Aluno.findByEmailAndFone",
        query = "select a from Aluno a where a.email = ?1 and a.fone = ?1")
@NamedQuery(name = "Aluno.findByEmailOrFone",
        query = "select a from Aluno a where a.email = ?1 or a.fone = ?1")
@NamedQuery(name = "Aluno.findByTipo",  query = "select a from Aluno a where a.tipo = ?1")
public class Aluno implements Serializable { ... }
```

**6. Projeto final da API de Escola:**

O documento `Aula06-projeto-API-Escola.pdf` traz o projeto consolidado que consolida tudo aprendido nas 6 aulas (7 encontros). O projeto final inclui múltiplas entidades: `Aluno`, `Professor`, `Curso`, `Turma`, `Matricula`.

---

## Modelagem de Dados (Evolução)

### Fase inicial (Aulas 02-03) — Aluno apenas

| Entidade | Tabela | Campos |
|----------|--------|--------|
| `Aluno` | `ALUNO` | `id` (PK), `cpf`, `nome`, `email`, `fone`, `tipo` |

Enum `TipoAluno`: `CONVENCIONAL`, `MONITOR`.

### Fase expandida (Aula 06) — Modelo completo da Escola

O `data.sql` da Aula 06 revela o modelo completo com 5 entidades:

| Entidade | Tabela | Campos |
|----------|--------|--------|
| `Aluno` | `ALUNO` | `id`, `cpf`, `nome`, `email`, `fone`, `tipo` |
| `Professor` | `PROFESSOR` | `id`, `cpf`, `nome`, `email`, `valor_hora`, `certificados`, `tipo` |
| `Curso` | `CURSO` | `id`, `nome`, `requisitos`, `carga_horaria`, `preco` |
| `Turma` | `TURMA` | `id`, `nome`, `carga_horaria`, `professor_id` (FK), `curso_id` (FK) |
| `Matricula` | `MATRICULA` | `id`, `turma_id` (FK), `aluno_id` (FK), `data_matricula` |

**Seed SQL completo (Aula 06, `data.sql`):**
```sql
-- 10 alunos
INSERT INTO ALUNO (cpf, nome, email, fone, tipo) VALUES
  ('11111111111', 'Alberto', ...),
  ('22222222222', 'Bruno', ...),
  ... (10 registros);

-- 3 professores
INSERT INTO PROFESSOR (cpf, nome, email, valor_hora, certificados, tipo) VALUES
  ('11156356399', 'Professor Pardal', 'professor111@escola.com', 50.00, 'DP-900, LGPDF, PCEP-02', 'TITULAR'),
  ('22256356399', 'Doutor Brown', ...),
  ('33356356399', 'Professor Rodrigo', ...);

-- 3 cursos
INSERT INTO CURSO (nome, requisitos, carga_horaria, preco) VALUES
  ('Spring Boot', 'Java, Orientação a Objetos, REST, JPA', 24, 1000.0),
  ('Java Básico', 'Java, Orientação a Objetos, REST, JPA', 40, 1000.0),
  ('Banco de Dados', 'JDBC, Java, JPA', 30, 1000.0);

-- 2 turmas
INSERT INTO TURMA(nome, carga_horaria, professor_id, curso_id) VALUES
  ('TURMA-Spring Boot-2022-1Sem', 40, 1, 1),
  ('TURMA-Spring Boot-2022-2Sem', 40, 2, 1);

-- 1 matrícula
INSERT INTO MATRICULA(turma_id, aluno_id, data_matricula) VALUES
  (1, 1, '2022-06-01');
```

---

## Coleções Postman

Duas coleções Postman estão inclusas no repositório:

| Arquivo | Aula | Endpoints cobertos |
|---------|------|-------------------|
| `Aula03/.../escola-control-api.postman_collection.json` | 03 | `GET /alunos`, `POST /alunos`, `PUT /alunos/1`, `DELETE /alunos/5`, `GET /alunos/1`, `GET /professores`, `POST /professores`, `PUT /professores`, `DELETE /professores` |
| `Aula04/.../escola-control-api.postman_collection.json` | 04 | Mesmos endpoints (versão atualizada) |

Ambas as coleções têm o nome `escola-control-api` e contêm as requests para os recursos **alunos** e **professores**.

---

## Como Usar

### Pré-requisitos

- JDK 8+ (recomendável 11+)
- Maven 3.6+
- IDE Spring Tool Suite (STS) ou Eclipse com Lombok instalado (v1.18.24+)
- Postman (opcional, mas útil — coleções inclusas)

### Passo a passo geral

1. **Importar o projeto** no STS/Eclipse:
   - File → Import → Existing Maven Projects
   - Selecionar a pasta do projeto (ex: `escola-api-projeto-completo` da Aula 06)

2. **Instalar Lombok** (se não instalado):
   - Baixar `lombok.jar` do site oficial
   - Executar `java -jar lombok.jar`
   - Indicar diretório da IDE, selecionar `SpringToolSuite4.ini`, clicar "Install/Update"
   - Reinicar IDE

3. **Executar a aplicação:**
   - Executar a classe principal `EscolaApiApplication` (ou similar)
   - A API fica disponível em `http://localhost:8080`

4. **Testar os endpoints:**

   - Swagger UI: `http://localhost:8080/swagger-ui.html`
   - Console H2: `http://localhost:8080/h2-console`
     - JDBC URL: `jdbc:h2:mem:escola-controle-api`
     - Username: `sa`, Password: (em branco)
   - Actuator: `http://localhost:8080/actuator`
   - Health: `http://localhost:8080/actuator/health`

5. **Usar as coleções Postman:**
   - Importar o arquivo `.json` correspondente à aula
   - Garantir o header `Content-Type: application/json` nas requests POST/PUT

### Arquivos de prática

Cada aula (03-06) inclui arquivos `.zip` de exercício:
- `*-pratica-*.zip` — projeto com ponto de partida para a prática
- `*-inicio.zip` — versão inicial do projeto para acompanhar a aula passo a passo
- `*-projeto-completo.zip` — projeto final consolidado

---

## Qualidade e Observações

### Pontos positivos do curso
- Progressão lógica e incremental: do "Hello World" REST até CRUD completo com JPA, paginação, cache, segurança, documentação e relatórios
- Uso de **Bean Validation** desde cedo na Aula 03
- Introdução ao **Spring Data JPA** com repositórios derivados e `@Query`
- **DTOs e Form Objects** bem diferenciados (entrada vs saída)
- **Paginação e ordenação** usando Spring Data nativamente
- **Cache** com `@Cacheable` + `@CacheEvict` — conceito importante
- **Monitoramento** com Actuator + Spring Boot Admin — visão operacional
- **Documentação Swagger** com `@Operation`
- **Thymeleaf** para páginas web simples
- **Relatórios PDF** com OpenPDF
- **Lombok** para reduzir boilerplate
- Coleções Postman inclusas para facilitar testes

### Observações técnicas

- Não há evidência de **testes unitários** no repositório (apenas os materiais de aula)
- O curso foca no **backend REST**; não há Frontend JavaScript explícito (apenas Thymeleaf para a página de boas-vindas)
- A segurança é **bem básica** (permitAll + CSRF desabilitado) — serve para aprendizado, mas não para produção
- O Spring Boot Admin usado é da versão 2.x (do curso de 2022) — a versão atual do SBA mudou para uma arquitetura diferente
- O `data.sql` da Aula 06 mostra que o projeto evoluiu para um modelo mais complexo (Professor, Curso, Turma, Matricula), mas os READMEs detalhados focam principalmente na entidade `Aluno`
- Dependências como `springdoc-openapi-ui:1.6.11` são versões da época (2022) — hoje há versões mais recentes (`springdoc-openapi-starter-webmvc-ui`)

---

## Commits, Branches e Histórico

- **Branch principal:** `main`
- **Remoto:** `git@github.com:lucaslimacodes/curso-springboot-fuctura.git`
- **Datas de conteúdo:** 23/07/2022 a 03/09/2022 (7 encontros semanais)
- **Commits:** ver no repositório original:
  - Branches: https://github.com/lucaslimacodes/curso-springboot-fuctura/branches
  - Commits: https://github.com/lucaslimacodes/curso-springboot-fuctura/commits/main

> **Nota:** O repositório foi arquivado/clonado em 2022 e não parece ter atividade recente.

---

## Padrão Wiki deste Repositório

Esta página segue o padrão de wiki detalhada do projeto **lhl-wiki**, adaptado para repositórios de cursos. As seções incluídas são:

- **Visão geral** do curso e seus objetivos
- **Estrutura** do repositório com inventário de arquivos
- **Stack tecnológica** com versões e dependências
- **Conteúdo programático** detalhado por aula
- **Modelagem de dados** e evolução das entidades
- **Materiais inclusos** (zip, pdf, imagens, coleções Postman)
- **Como usar** — pré-requisitos e passo a passo
- **Qualidade e observações** — pontos positivos e limitações
- **Links úteis** para Referências

Para atualizações futuras, deve-se:
- Registrar novos materiais ou aulas adicionadas
- Atualizar versões de dependências quando relevantes
- Adicionar diagramas de sequência ou arquitetura se disponíveis
- documentar correções ou evoluções do projeto

---

## Links Úteis

- **Repositório original:** https://github.com/lucaslimacodes/curso-springboot-fuctura
- **Branch main:** https://github.com/lucaslimacodes/curso-springboot-fuctura/tree/main
- **Spring Boot Documentation:** https://docs.spring.io/spring-boot/docs/current/reference/html/
- **Spring Data JPA:** https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
- **Spring Doc OpenAPI:** https://springdoc.org/
- **Lombok:** https://projectlombok.org/
- **Spring Boot Admin:** https://github.com/codecentric/spring-boot-admin
- **Spring Actuator:** https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html
- **Thymeleaf:** https://www.thymeleaf.org/
- **OpenPDF:** https://github.com/LibrePDF/OpenPDF
- **Postman:** https://www.postman.com/
- **H2 Database:** http://www.h2database.com/

---

*Wiki detalhada gerada por Hermes Agent com análise estática dos materiais do curso.*
*Última atualização: 2026-09-15*
