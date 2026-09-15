# ess-base-project

> [Ativo] Repositório público de Lucas Lima no GitHub.

- **Link:** [https://github.com/lucaslimacodes/ess-base-project](https://github.com/lucaslimacodes/ess-base-project)
- **Linguagem principal:** TypeScript
- **Descrição:** The ESS Base Project is a repository designed to kickstart new projects for the Software Engineering and Systems discipline at CIn-UFPE. It aims to simplify the project initiation process providing a solid base by offering an initial structure, architecture, configurations, and implementation examples.
- **Tamanho:** 38MB | 930 arquivos
- **Topics:** —

---

## Contexto

|| Campo | Valor |
|-------|-------|-------|
| Nome | `ess-base-project` |
| Proprietário | `lucaslimacodes` |
| URL | https://github.com/lucaslimacodes/ess-base-project |
| Linguagem | TypeScript |
| Arquitetura | Full-stack: Backend (Node.js/TypeORM) + Frontend (React) separados em pastas |
| Descrição | Projeto base (kickstart) para a disciplina de Engenharia de Software e Sistemas (ESS) do CIn-UFPE |
| Tamanho | 38MB, 930 arquivos (371 .ts, 204 .js, 141 .map, 59 .json, 37 .md) |
| Data de atualização | 2025-03-16T14:47:16Z |
| Estrelas | 0 |
| Arquivado | Não |
| Página deste wiki | [repos/ess-base-project.md](./ess-base-project.md) |

---

## Visão Geral

O **ESS Base Project** é um repositório "kit de início" para a disciplina de **Engenharia de Software e Sistemas (ESS)** do **Centro de Informática (CIn) da UFPE**. Ele foi criado para eliminar a fricção inicial que os grupos de estudantes enfrentam ao começar um projeto novo: copiar estruturas, configurar ambientes, escolher stacks e montar documentação do zero.

O repositório oferece:

- **Estrutura pronta** com separação clara entre backend e frontend, cada um com sua organização padrão de pastas e arquivos;
- **Arquitetura preenchida com exemplos funcionais** de uma aplicação de streaming de música (entidades, DTOs, controladores, rotas, serviços, repositórios, modelos, banco de dados PostgreSQL, frontend React com router, contextos, components, test setup);
- **APIs e casos de uso documentados em Gherkin** (arquivos `.feature` no diretório `features/`), alinhados com a metodologia da disciplina;
- **Configuração CLI (Python)** para gerar projetos derivados via Git subtree a partir de repos especializados por stack (React, Vue, Angular, Next.js para frontend; NodeJS, FastAPI para backend);
- **TypeScript como linguagem principal** com source maps para depuração em produção e configuração de tooling básico.

---

## Disciplina: Engenharia de Software e Sistemas (ESS) — CIn-UFPE

A disciplina de Engenharia de Software e Sistemas (ESS) do CIn-UFPE é um curso que une princípios de engenharia de software e de sistemas, geralmente trabalhada em grupos com projetos práticos de desenvolvimento full-stack. O foco inclui:

- Ciclo de desenvolvimento de software (requisitos, projeto, implementação, teste);
- Documentação com linguagem de casos de uso (Gherkin/spec);
- Desenvolvimento de backends com persistência (ex: PostgreSQL);
- Desenvolvimento de frontends com interfaces reutilizáveis;
- Integração entre camadas (API REST, serviços, repositórios);
- Qualidade de código, testes e revisão.

O **ESS Base Project** serve como ponto de partida padronizado para grupos que precisam iniciar um projeto sem gastar tempo no "hello world" da estrutura.

---

## Propósito do Projeto Base

O documento oficial do repositório afirma:

> "The ESS Base Project is a repository designed to kickstart new projects for the Software Engineering and Systems discipline at CIn-UFPE. It aims to simplify the project initiation process providing a solid base by offering an initial structure, architecture, configurations, and implementation examples."

Traduzindo: **não é um produto final**, mas uma base reutilizável. Grupos copiam essa base, adaptam os nomes de entidades/serviços/rotas, substituem os exemplos (music streaming) pelo domínio do próprio projeto, e começam a entregar funcionalidades rapidamente.

---

## Como Usar

### Como ponto de partida direto

1. Clonar o repositório:
```bash
git clone https://github.com/lucaslimacodes/ess-base-project.git
```

2. Instalar dependências:
```bash
cd ess-base-project
npm install
```

3. Configurar ambiente (variáveis, banco PostgreSQL) conforme os arquivos de exemplo em `backend/src/env.ts` e `backend/src/database/postgresConnection.ts`.

4. Adaptar:
   - Renomear/atualizar entidades em `backend/src/entities/`
   - Ajustar DTOs em `backend/src/dto/`
   - Reimplementar controladores/s�름/rotas conforme o domínio
   - Substituir o conteúdo do frontend (React) pelo de nova interface

### Como gerador via CLI (Git subtree)

O repositório inclui uma **CLI em Python** (`config/cli.py`) que foi projetada para criar projetos derivados usando **Git subtree**, juntando um frontend e um backend de repositórios especializados por tecnologia.

Fluxo da CLI:

1. Exibe boas-vindas com efeito de digitação.
2. Pergunta framework de frontend: **React ⚛️**, **Vue.js 🔥**, **Angular 🅰️**, **Next.js 🇳**.
3. Pergunta framework de backend: **NodeJS 🚀**, **FastAPI ⚡️**.
4. Pergunta protocolo de clone: **HTTPS 🔒** ou **SSH 🔑**.
5. Confirma a escolha.
6. Executa `git subtree add` para trazer os repositórios remoto dentro das pastas `frontend/` e `backend/`.

Mapeamento de frameworks (em `config/constants.py`):

| Framework | Prefixo do repositório remoto |
|-----------|-------------------------------|
| React ⚛️ | `frontend-react` |
| Vue.js 🔥 | `frontend-vue` |
| Angular 🅰️ | `frontend-angular` |
| Next.js 🇳 | `frontend-nextjs` |
| NodeJS 🚀 | `backend-nodejs` |
| FastAPI ⚡️ | `backend-fastAPI` |
| HTTPS 🔒 | `https://github.com/` |
| SSH 🔑 | `git@github.com:` |

A URL completa do subtree segue o padrão:
```
{nome-do-framework}-ess.git
```
dentro do namespace `Software-Engineering-Assistantship` no GitHub.

---

## Estrutura de Pastas

### Visão geral

```
ess-base-project/
├── backend/                 # Backend TypeScript/Node.js
│   ├── src/
│   │   ├── app.ts          # Aplicação principal (Express-like)
│   │   ├── index.ts        # Entry point
│   │   ├── env.ts          # Variáveis de ambiente
│   │   ├── logger.ts       # Configuração de logging
│   │   ├── database/
│   │   │   ├── index.ts
│   │   │   └── postgresConnection.ts
│   │   ├── di/             # Injeção de dependências
│   │   │   ├── index.ts
│   │   │   └── injector.ts
│   │   ├── entities/       # Entidades/ORM (TypeORM-style)
│   │   ├── dto/            # Data Transfer Objects
│   │   ├── models/         # Modelos de domínio
│   │   ├── controllers/    # Controladores de requisição
│   │   ├── services/       # Lógica de negócio
│   │   ├── repositories/   # Acesso a dados
│   │   ├── routes/         # Definição de rotas
│   │   └── utils/          # Utilitários (errors, result)
│   ├── tests/
│   │   ├── controllers/
│   │   ├── features/
│   │   └── utils/
│   └── docs/
├── frontend/                # Frontend React
│   ├── src/
│   │   ├── App.tsx         # Componente raiz com router
│   │   ├── main.tsx        # Ponto de entrada React DOM
│   │   ├── global.css      # Estilos globais
│   │   ├── Provider.tsx    # Context provider raiz
│   │   ├── app/
│   │   │   └── home/
│   │   │       ├── components/
│   │   │       ├── context/HomeContext/
│   │   │       ├── forms/
│   │   │       ├── models/
│   │   │       └── pages/
│   │   ├── shared/         # Componentes, hooks, serviços, tipos
│   │   └── tests/
│   ├── public/
│   ├── cypress/            # Testes E2E
│   │   ├── e2e/
│   │   ├── fixtures/
│   │   └── support/
│   ├── docs/
│   └── vite-env.d.ts
├── features/                # Casos de uso em Gherkin
│   ├── *.feature            # Ex: userService.feature, playlistService.feature
├── config/
│   ├── cli.py              # CLI Python para geração via subtree
│   └── constants.py        # Mapeamentos de frameworks/transporte
├── node_modules/
├── .gitignore
├── LICENSE
├── package.json
├── package-lock.json
└── README.md
```

### Backend: camadas

- **entities/**: mapeamento de tabelas/comunicação (ex: `user.entity.ts`, `artist.entity.ts`, `albums.entity.ts`, `playlist.entity.ts`, `songs.entity.ts`, `category.entity.ts`, `musichistory.entity.ts`, `test.entity.ts`, `base.entity.ts`).
- **dto/**: contratos de entrada/saída (`criarUser.dto.ts`, `editarUser.dto.ts`, `criarPlaylist.dto.ts`, `editarPlaylist.dto.ts`).
- **models/**: modelos de representação (`base.model.ts`, `test.model.ts`).
- **controllers/**: handlers de requisição HTTP (`user.controller.ts`, `artist.controller.ts`, `songs.controller.ts`, `albuns.controller.ts`, `playlist.controller.ts`, `search.controller.ts`, `musichistory.controllers.ts`, `test.controller.ts`).
- **services/**: regras de negócio (`user.service.ts`, `artist.service.ts`, `songs.service.ts`, `album.service.ts`, `playlist.service.ts`, `search.service.ts`, `musichistory.service.ts`, `test.service.ts`).
- **repositories/**: abstração de persistência (`base.repository.ts`, `artist.repository.ts`, `other.repository.ts`, `test.repository.ts`).
- **routes/**: mapeamento de endpoints (`user.routes.ts`, `artist.routes.ts`, `songs.routes.ts`, `albuns.routes.ts`, `playlist.routes.ts`, `search.routes.ts`, `musichistory.routes.ts`, `index.ts`).
- **database**: conexão PostgreSQL (`postgresConnection.ts`, `index.ts`).
- **di**: container de injeção (`injector.ts`, `index.ts`).

### Frontend: React

- **App.tsx**: roteamento com `createBrowserRouter` (React Router v6), rotas `/`, `/create-test`, `/tests`.
- **main.tsx**: renderização com `ReactDOM.createRoot`, wrapper `Provider`.
- **context/HomeContext/**: gerenciamento de estado com `index.tsx`, `reducer.ts`, `service.ts`, `types.ts`.
- **pages/**: telas `CreateTest` e `ListTests`, cada uma com estilo CSS module.
- **shared/**: componentes reutilizáveis (`Button`), modelos de resposta HTTP (`BaseApiResponseModel.ts`), serviços (`ApiService.ts`), enums (`request-status.enum.ts`, `result.enum.ts`), erros (`app-error.ts`, `http-error.ts`), hooks (`usePrevious.tsx`), tipos.

### Features (Gherkin)

Os arquivos `.feature` descrevem serviços e telas do sistema de exemplo:

- **userService.feature**, **userGUI.feature**
- **playlistService.feature**, **playlistGUI.feature**
- **albunsService.feature**, **albunsGUI.feature**
- **ArtistasService.feature**, **ArtistasGUI.feature**
- **categoriaService.feature**, **categoriaGUI.feature**
- **compartilharServico.feature**, **compartilharGUI.feature**
- **passwordRecoveryService.feature**, **passwordRecoveryGUI.feature**
- **musichistoryService.feature**, **musichistoryGUI.feature**
- **moreListenedService.feature**
- **buscaPorFiltro.feature**
- **Seguidores_e_DonoServico.feature**, **Seguidores_e_DonoGUI.feature**

---

## Tecnologias

### Linguagens e runtimes

- **TypeScript** — linguagem principal do backend (371 arquivos `.ts`).
- **JavaScript** — código compilado/gerado e scripts (204 arquivos `.js`).
- **Source maps** — 141 arquivos `.map` para depuração.
- **Python** — CLI de geração (`cli.py`, `constants.py`).

### Backend

- **Node.js** — runtime (implícito pelo package.json e `ts-node`).
- **ts-node** — execução TypeScript sem compilar à mão.
- **TypeORM-style entities** — mapeamento de entidades com herança (`base.entity.ts`).
- **PostgreSQL** — banco relacional (módulo `database/postgresConnection.ts`).
- **Express-like** — estrutura de rotas, controladores e serviços sugere framework HTTP estilo Express (não verificado explicitamente em `package.json`, mas padrão adotado).

### Frontend

- **React** — biblioteca UI principal.
- **React Router v6** (`createBrowserRouter`, `RouterProvider`).
- **Vite** — evidente pelo `vite-env.d.ts` e estrutura.
- **CSS Modules** — `index.module.css` por página/component.
- **Cypress** — testes E2E (`frontend/cypress/`).
- **Jest + jest-cucumber + supertest** — testes backend com padrão BDD (arquivos de teste em `backend/tests/`).

### Ferramentas e padrões

- **Git subtree** — mecanismo de composição do projeto base via CLI.
- **Gherkin / BDD** — especificação de casos de uso nos `.feature`.
- **Source maps** — transpilação TypeScript→JavaScript com mapeamento.
- **Injeção de dependências simples** — módulo `di/` com `injector.ts`.

---

## Arquitetura

O projeto adota um **modelo layered** no backend e um **modelo componentizado** no frontend.

### Backend (layered)

```
Requisição HTTP
  → Routes (mapeamento de endpoint)
    → Controllers (validação, delegação)
      → Services (regra de negócio)
        → Repositories (persistência)
          → PostgreSQL (TypeORM/entities)
```

- **Entities** representam o domínio e modelos de banco.
- **DTOs** isolam contratos de entrada/saída das entidades internas.
- **Services** encapsulam regras, independentes de HTTP.
- **Repositories** abstraem acesso ao banco.
- **Controllers** lidam com HTTP, parsing e resposta.
- **DI container** simples permite injetar dependências quando necessário.

### Frontend (component + context)

```
Provider (context raiz)
  → App (Router)
    → Pages (CreateTest, ListTests)
      → Components (Button, outros)
      → Context (HomeContext com reducer + service)
      → Shared (API Service, modelos, hooks, tipos, erro)
```

- **State management** via context + reducer para o fluxo de Home.
- **API Service** centraliza chamadas HTTP com tratamento de erro.
- **CSS Modules** para isolamento de estilos.
- **Cypress** para validar fluxos E2E.

### Banco de dados

- PostgreSQL, acessado por um módulo de conexão (`postgresConnection.ts`) que expõe um `getRepository(...)` para cada entidade.
- A disciplina provavelmente usa um banco de desenvolvimento compartilhado ou local por grupo.

---

## Caso de Uso de Exemplo: Streaming de Música

O repositório foi populado com um exemplo funcional de sistema de streaming de música, que serve como **modelo preenchido** das camadas:

- **Artistas** — cadastro, perfil, bio.
- **Albuns** — criação com gênero, subgênero, músicas, caminhos de arquivos, artista associado.
- **Músicas (Songs)** — entidades de faixas.
- **Playlists** — criação, edição, listas de músicas.
- **Histórico de música (Music History)** — registro de reproduções.
- **Busca por filtro** — funcionalidade de consulta.
- **Categorias** — entidades de categorização.
- **Seguidores / Dono de serviço** — relacionamentos de usuário.
- **Recuperação de senha** — fluxo GUI + serviço.
- **Compartilhamento** — funcionalidade de compartilhar serviço/playlist.
- **Mais ouvida (More Listened)** — ranking de música mais reproduzida.

Esses casos alimentam os arquivos `.feature` e os testes com `jest-cucumber` + `supertest`, que simulam um usuário/artista autenticado e verificam respostas da API.

---

## Qualidade

### Testes

- **Backend**: suite com Jest, `jest-cucumber` e `supertest`. Os testes em `backend/tests/controllers/` carregam features Gherkin e executam passos `given/when/and/then` contra a aplicação real (ex: criação de artista, inserção de single/álbum).
- **Frontend**: setup de teste (`frontend/src/tests/setup.ts`) e teste de component (`Button.test.tsx`).
- **E2E**: Cypress com specs em `frontend/cypress/e2e/features` e suporte em `frontend/cypress/support/`.

### Documentação

- 37 arquivos `.md` no repositório indicam documentação distribuída (provavelmente por componente, disciplina, ou instruções de uso).
- Arquivos `.feature` funcionam como documentação executável dos requisitos.

### Tipagem e mapeamento

- TypeScript com 371 arquivos fonte e 204 `.js` compilados/gerados.
- 141 source maps para depuração em production.

### Padrões adotados

- Separação backend/frontend em raiz.
- Camadas claras no backend.
- Componentização no frontend com context + router.
- Especificação de requisitos em Gherkin.
- CLI para reproduzir a base em novas stacks.

---

## Informações de Repositório

- **Repositório:** https://github.com/lucaslimacodes/ess-base-project
- **Linguagem principal listada:** TypeScript
- **Descrição oficial:** "The ESS Base Project is a repository designed to kickstart new projects for the Software Engineering and Systems discipline at CIn-UFPE. It aims to simplify the project initiation process providing a solid base by offering an initial structure, architecture, configurations, and implementation examples."
- **Tamanho:** 38MB, 930 arquivos
- **Atualizado em:** 2025-03-16T14:47:16Z
- **Arquivado:** Não

---

## Links Externos

- **Repositório no GitHub:** https://github.com/lucaslimacodes/ess-base-project
- **Disciplina (contexto):** Engenharia de Software e Sistemas (ESS) — CIn-UFPE
- **Git subtree (documentação Atlassian — link citado pela CLI):** https://www.atlassian.com/br/git/tutorials/git-subtree

---

## Observações

<!-- Preencher com observações relevantes sobre o repositório -->

- O repositório é um **projeto base (kickstart)**, não um produto final; grupos devem adaptá-lo ao domínio de seu projeto de ESS.
- O exemplo implementado é um **sistema de streaming de música** usado para demostrar as camadas, entidades, rotas, serviços e fluxos de teste.
- A CLI `config/cli.py` foi desenhada para gerar projetos derivados via **Git subtree**, integrando frontend + backend de repositórios específicos; isso indica que o autor mantém (ou mantia) variantes por stack em um namespace `Software-Engineering-Assistantship`.
- O `package.json` atual contém apenas `class-transformer` e `ts-node` como dependências; ferramentas como Express, React e Cypress podem ser gerenciadas em subtrees dos repositórios especializados ou em dependências de desenvolvimento não listadas — verificar em uso real.
- Source maps (141 `.map`) indicam tooling de build TypeScript; considerar em configurações de deploy/lint.

---

*Documento gerado automaticamente por Hermes Agent. Atualizado em 2026-09-15 19:05 UTC.*
