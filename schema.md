# Schema do Wiki

> Convenções e estrutura que regem este wiki. Baseado no padrão [llm-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) do Andrej Karpathy.

## Estrutura

```
lhl-wiki/
├── README.md            ← Visão geral e instruções de uso
├── index.md             ← Catálogo completo de todos os repositórios
├── log.md               ← Histórico cronológico de atualizações
├── schema.md            ← Este arquivo: convenções e estrutura
└── repos/               ← Páginas individuais por repositório
    └── <nome-do-repo>.md
```

## Convenções de paginação

### README.md
- Visão geral do wiki e instruções de uso
- Links para index.md, log.md e todos os sub-diretórios
- Deve ser mantido sempre atualizado

### index.md
- Lista **todos** os repositórios, preferencialmente em ordem alfabética ou por relevância
- Cada entrada deve conter: nome (link para o repo original), linguagem, descrição curta, data de atualização, estrelas e link para a página detalhada neste wiki
- Separar seções para "ativos" e "arquivados"

### repos/<nome>.md
- Página detalhada de **um único repositório**
- Deve conter: link para o repo original, linguagem, descrição, topics, data de atualização, estrelas, status (ativo/arquivado)
- Deve incluir uma tabela com os metadados do repositório
- Deve ter uma seção "Observações" para anotações manuais ou geradas
- Link de volta para index.md e README.md

### log.md
- Registro cronológico de todas as atualizações
- Formato de entrada: `## [YYYY-MM-DD] tipo | descrição`
- Tipos sugeridos: `init`, `update`, `add`, `remove`, `schema`, `manual`
- Deve ser append-only (nunca editar entradas existentes, apenas adicionar novas)

### schema.md
- Documentação das convenções deste wiki
- Deve ser atualizado sempre que uma nova convenção for introduzida

## Operação

1. **Ingest:** Quando um novo repositório for criado ou atualizado, adicionar/atualizar a página em `repos/` e atualizar o `index.md` e o `log.md`.
2. **Query:** Para consultar rapidamente, buscar no `index.md`. Para detalhes, ler a página específica em `repos/`.
3. **Lint:** Periodicamente verificar se há repositórios que foram arquivados ou removidos e atualizar o wiki conforme necessário.

## Metadados de cada repositório

Cada página de repositório deve conter (pelo menos) os seguintes campos:

- **link**: URL do repositório no GitHub
- **language**: Linguagem principal (pode ser "—" se não houver)
- **description**: Descrição do repositório
- **topics**: Lista de topics (se houver)
- **updated**: Data da última atualização
- **stars**: Número de estrelas
- **archived**: Status de arquivamento (Sim/Não)
- **status**: Texto livre (ex: "Ativo", "Em manutenção", "Arquivado")

## Manutenção

- Este wiki é gerado automaticamente via GitHub API
- Para atualizações manuais, editar diretamente os arquivos markdown
- O `log.md` deve ser atualizado sempre que uma mudança for feita
- O `schema.md` deve ser atualizado se novas convenções forem adicionadas

---

*Convenções definidas em 2026-09-15 18:50 UTC.*
