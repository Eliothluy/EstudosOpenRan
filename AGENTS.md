# AGENTS.md

Vault Obsidian de estudo pessoal sobre O-RAN, em português (pt-BR). Não é um projeto de código: não há build, lint ou testes. O trabalho principal é traduzir e estudar o survey *Understanding O-RAN* (Polese et al., 2023), cujo PDF está na raiz como `Understading.pdf` (erro de digitação no nome do arquivo — não renomear sem pedir).

## Estrutura e papéis (ver `Início.md` para o mapa oficial)

- `Fontes/` — traduções de papers, material de referência (fase 1). Uma nota por paper, nome `AAAA Autor - Título (tradução).md`. Notas de estudo (ideias-chave, siglas, perguntas) ficam em nota irmã `AAAA Autor - Título (notas de estudo).md` — **nunca dentro da tradução**, que deve conter apenas o texto do artigo.
- `Conceitos/` — uma nota por conceito, **nome do arquivo em inglês técnico** (ex.: `Vendor lock-in.md`, `Closed-loop control.md`) (fase 2).
- `Ideias/` — pensamento próprio do usuário: dúvidas, críticas, conexões (fase 2). Nome datado `AAAA-MM-DD — Título.md`.
- `Modelos/` — templates (ex.: `Sessão de estudo.md`).

## Regras de edição

- **`Ideias/` é a voz do usuário**: nunca reescrever ou apagar conteúdo lá; no máximo acrescentar quando pedido.
- Em traduções (`Fontes/`), a nota contém apenas o texto traduzido do artigo. Qualquer acréscimo de estudo (ideias-chave, descrições de figuras, tabelas de siglas, perguntas de revisão) vai para a nota irmã `(notas de estudo).md` correspondente.
- Links internos usam wiki-links Obsidian `[[Nota|alias]]`. Antes de criar link para conceito, verificar se a nota já existe em `Conceitos/`; prefira linkar apenas notas existentes.
- **Não renomear headings usados como âncora** de links (ex.: `## Resumo` em Fontes é alvo de `[[...#Resumo]]` a partir de Conceitos).
- Notas de conceito seguem o esqueleto de `Conceitos/O-RAN.md`: definição curta com links → `**Primeira aparição:** [[fonte#seção]]` → `## Minhas notas` → `## Relacionados`.
- Notas de tradução têm frontmatter YAML com chaves em pt (`título`, `autores`, `publicação`, `doi`, `arxiv`, `tipo`, `status`, `revisado`); atualizar `status` ao concluir trechos e o checklist de progresso em `Início.md`.
- Português do Brasil; termos técnicos em inglês mantidos no original, em itálico na primeira ocorrência (ex.: *white papers*, *testbeds*, *cloud-native*).

## Gotchas

- Textos colados podem conter caracteres invisíveis (zero-width/espaços duplos) — verificar ao revisar.
- Nomes de arquivo têm espaços, acentos e travessões: sempre citar caminhos entre aspas no shell.
- Git: branch `main` com remote `origin`; `.obsidian/` é versionado, exceto `workspace.json`/`hotkeys.json`. Commitar apenas quando o usuário pedir.
