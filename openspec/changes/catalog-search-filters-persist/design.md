# Design

## Context

- A rota `/` já valida a URL com um schema Zod (`homeSearch.ts`) via `validateSearch` do TanStack Router; `HomePage` lê o estado com `route.useSearch()` e o envia a `useNfts`, cuja query key inclui todos os filtros.
- A função `update` da `HomePage` já força `page: 1` em qualquer patch sem `page` explícito; o reinício da paginação vale automaticamente para novos filtros que passem por ela.
- Hoje `Nft.editions` lista tiragens (`1/1`, `1/10`, `1/50`) iguais para todos os NFTs; não há atributo que separe "Limitada" de "Padrão" (decisão da usuária: criar um atributo novo no NFT).
- O banco do MSW é persistido em `localStorage` sob a chave `kurio.mock.db.v1`; NFTs gravados antes da mudança não terão o novo atributo.
- Playwright ainda não está instalado (`tests/e2e/` só tem pastas vazias).

## Goals / Non-Goals

**Goals:**
- Filtro de edição ponta a ponta: URL → schema da rota → query → Axios → handler MSW → fixtures.
- Seleção visível restaurada a partir da URL após refresh.
- Fixtures determinísticas com ao menos 3 páginas de NFTs "limitada".

**Non-Goals:**
- Renomear para pt-BR os parâmetros de URL existentes (`rarity`, `collection`, `page` etc.).
- Relacionar o tipo de edição às tiragens de `Nft.editions` ou ao estoque.
- Filtrar por mais de um tipo de edição ao mesmo tempo.

## Decisions

1. **Atributo de domínio `editionType: 'limited' | 'standard'` no `Nft` e em `NftFilters`.**
   Identificadores em inglês seguem a convenção do projeto. Alternativa descartada: derivar das tiragens/estoque, o que faria um NFT aparecer nos dois filtros.

2. **Parâmetro de URL `edicao` com valores `limitada` | `padrao`, mapeado para `editionType` em `homeSearch.ts`.**
   O nome e os valores da URL vêm dos cenários da spec. O mapeamento fica na camada de apresentação (o schema da rota), que é dona da URL; o domínio permanece em inglês. Valores inválidos caem em `undefined` via `.catch(undefined)`, como os demais filtros. Alternativa descartada: usar `edicao` também no domínio e na API, misturando idiomas nos identificadores.

3. **Contrato REST: `GET /api/nfts?editionType=limited|standard`.**
   Mantém o padrão em inglês dos parâmetros atuais (`rarity`, `minPrice`). O handler MSW ignora valores desconhecidos (sem filtro).

4. **Controle de UI: `NativeSelect` "Edição" (Todas / Limitada / Padrão), igual ao de Raridade.**
   O valor selecionado é derivado da URL (componente controlado por `search`), garantindo a restauração após refresh sem estado local. Mudança chama `update({ editionType })`, que reinicia a página.

5. **Fixtures: tipo atribuído de forma determinística pelo índice (ex.: `i % 3 === 0` → `standard`, demais → `limited`).**
   Com 36 NFTs e `PAGE_SIZE = 9`, resulta em 24 limitados (3 páginas) e 12 padrão (2 páginas), suficiente para o cenário "página 3".

6. **Persistência do mock: incrementar a chave para `kurio.mock.db.v2`.**
   Força a regeneração do banco em quem já tem o v1 salvo, sem exigir `?reset=1` manual.

## Risks / Trade-offs

- [URL em pt-BR convivendo com parâmetros em inglês] → Restrito a `edicao`, exigido pelos cenários; documentar em `ARCHITECTURE.md`.
- [Troca da chave do banco descarta carrinho/pedidos mockados salvos localmente] → Aceitável em ambiente de mocks; documentar no README.
- [Testes E2E dependem de Playwright ainda não configurado] → Tarefa dedicada para configuração mínima, reaproveitável pelos próximos testes.

## Migration Plan

Sem migração de dados reais. Ao publicar, a nova chave do banco MSW regenera as fixtures automaticamente. Rollback: reverter o commit (a chave v1 volta a ser usada).
