# AGENTS.md

Vault Obsidian de estudo pessoal sobre O-RAN, em português (pt-BR). **É uma pasta de estudos, não um projeto de software**: não há build, lint, testes nem CI. O fluxo é em duas fases: **(1)** traduzir papers para `Fontes/`; **(2)** pensar e conectar em `Ideias/` e `Conceitos/`. `Início.md` é o mapa oficial do vault e do progresso de tradução.

## Fontes na raiz

- `Understanding_O-RAN_Architecture_Interfaces_Algorithms_Security_and_Research_Challenges.pdf` — survey *Understanding O-RAN* (Polese et al., 2023), em tradução.
- `ML-Enabled_Open_RAN_...pdf` — segundo survey (adicionado em 2026-10-22), ainda sem tradução nem entrada em `Início.md`. Não presumir que é o foco atual; perguntar antes de começar.

## Papéis das pastas

- `Fontes/` — traduções. Nome `AAAA Autor - Título curto (tradução).md`; a nota irmã `(notas de estudo).md` guarda ideias-chave, figuras, siglas e perguntas de revisão.
- `Conceitos/` — uma nota por conceito, **nome do arquivo em inglês técnico** (`Vendor lock-in.md`, `Closed-loop control.md`).
- `Ideias/` — voz do usuário: dúvidas, críticas, conexões. Nome datado `AAAA-MM-DD — Título.md`.
- `Modelos/` — templates (`Sessão de estudo.md`).

## Regras de conteúdo

- A tradução contém **apenas** o texto do artigo mais a linha-pointer para a nota irmã logo após o H1. Todo acréscimo de estudo vai para `(notas de estudo).md`.
- `Ideias/`: nunca reescrever nem apagar; no máximo acrescentar quando pedido.
- Conceito segue o esqueleto de `Conceitos/O-RAN.md`: definição curta com links → `**Primeira aparição:** [[fonte#seção]]` → `## Minhas notas` → `## Relacionados`. Deixar `## Minhas notas` vazio (é espaço do usuário) salvo pedido explícito.
- pt-BR; termos técnicos em inglês no original, em itálico na primeira ocorrência, de preferência via alias de wiki-link: `[[RIC|RICs]]`, `[[vRAN|RANs virtualizadas]]`.
- Linkar somente notas existentes; conferir `Conceitos/` antes de criar link. Não há imagens no vault: figuras são descritas em texto na seção `## Figuras` da nota de estudo.

## Links e âncoras

- Headings de `Fontes/` são alvo de link e **não podem ser renomeados**. Copiar o texto exato, incluindo numeral romano: `#Resumo`, `#I. Introdução`, `#II. Princípios fundamentais da arquitetura O-RAN`.
- Arquivos também são linkados pelo nome: renomear quebra links em conceitos e no `Início.md`.

## Frontmatter e progresso

- Chaves em pt. Tradução: `título`, `autores`, `publicação`, `doi`, `arxiv`, `tipo`, `status`, `revisado`. Notas de estudo: `título`, `fonte`, `tipo`, `status`, `revisado`.
- Ao concluir/revisar um trecho, atualizar `status` e `revisado` nas **duas** notas e o checklist em `Início.md`.
- Estado atual: Resumo e Seções I–V concluídos; Seção VI (fluxos de IA/ML) traduzida só até VI-B e ainda desmarcada.

## Gotchas

- Caminhos têm espaços, acentos e travessão (—): sempre entre aspas no shell.
- Textos colados podem trazer caracteres invisíveis (zero-width, espaços duplos) — verificar ao revisar.
- Artefatos vazios versionados na raiz (`2026-10-07.md`, `Sem título*.canvas`, `Sem título*.base`) são ruído do Obsidian: não tratar como conteúdo nem criar mais.
- A nota em `Ideias/` tem uma seção de estudo anexada em revisão antiga; é histórico, não é o padrão a repetir.
- Git: branch `main` com remote `origin`. **Nada é ignorado**: não existe `.gitignore` e o `.obsidian/` inteiro (incluindo `workspace.json`) é versionado. Commitar apenas quando pedido, com mensagem descritiva em pt-BR.
