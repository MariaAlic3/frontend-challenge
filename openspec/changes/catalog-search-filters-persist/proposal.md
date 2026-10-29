# Proposal

## Why

O enunciado exige que busca, filtros, ordenação e paginação do catálogo componham o estado da URL e sobrevivam a refresh e ao histórico, e que mudar um filtro reinicie a paginação. Hoje o catálogo já guarda busca, coleção, raridade, preço, ordenação e página na URL, mas não existe filtro por **Edição**: o colecionador não consegue restringir o catálogo a NFTs de edição limitada ou padrão, nem compartilhar/recarregar essa seleção.

## What Changes

- Novo atributo de domínio no NFT: tipo de edição, com os valores `limitada` e `padrao`, mutuamente exclusivos (cada NFT pertence a exatamente um tipo).
- Novo filtro "Edição" no catálogo, com as opções "Limitada" e "Padrão" (além de "Todas"), serializado na URL como o parâmetro `edicao` (`edicao=limitada` / `edicao=padrao`).
- O filtro é lido da URL ao carregar a página, de modo que um refresh (F5) restaura tanto os resultados filtrados quanto a seleção visível na interface.
- Mudar o filtro de edição reinicia a paginação para a página 1, refletida na URL junto com o novo filtro.
- O contrato REST `GET /api/nfts` passa a aceitar um parâmetro de tipo de edição, e o handler MSW filtra por ele.
- As fixtures do MSW passam a atribuir um tipo de edição a cada NFT, com volume suficiente de NFTs "limitada" para haver ao menos 3 páginas de resultados filtrados.

## Capabilities

### New Capabilities
- `catalog/catalog-filters`: filtros do catálogo representados na URL (aplicação, restauração após refresh e reinício da paginação), começando pelo filtro de Edição.

### Modified Capabilities
<!-- Nenhuma: ainda não há specs em openspec/specs/. -->

## Impact

- **Domínio** (`src/modules/catalog/domain/types.ts`): novo tipo de edição no `Nft` e em `NftFilters`.
- **Infraestrutura** (`src/modules/catalog/infrastructure/api.ts`): envio do tipo de edição em `GET /nfts`.
- **Apresentação** (`src/modules/catalog/presentation/pages/homeSearch.ts`, `HomePage.tsx`): validação do parâmetro `edicao` no schema de search da rota `/` e controle de filtro na barra lateral.
- **Mocks** (`src/mocks/fixtures/nfts.ts`, `src/mocks/handlers/catalog.ts`): atributo nas fixtures e filtragem no handler. Requer `?reset=1` para regenerar o banco persistido.
- **Testes** (`tests/e2e/`): cenários Playwright para os 3 comportamentos.
- **Docs**: contrato de `GET /api/nfts` em `ARCHITECTURE.md`/`docs/contracts/`.
