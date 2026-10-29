# Tasks

## 1. Setup do Playwright

- [ ] 1.1 Instalar `@playwright/test` (Chromium), criar `playwright.config.ts` com projetos desktop (1440px) e mobile (390px), `webServer` apontando para o Vite com mocks e relatório HTML + traces; adicionar o script `npm run test:e2e` e verificar que `npx playwright test --list` roda sem erro
- [ ] 1.2 Criar fixture em `tests/e2e/fixtures/` que abre a app com `?reset=1` para isolar o estado do banco MSW a cada teste, e verificar com um teste de fumaça que a página inicial carrega

## 2. Domínio, contrato e mocks

- [ ] 2.1 Adicionar `EditionType = 'limited' | 'standard'` e o campo `editionType` em `Nft` e `NftFilters` (`src/modules/catalog/domain/types.ts`); verificar com `npm run typecheck`
- [ ] 2.2 Atribuir `editionType` nas fixtures de forma determinística (`i % 3 === 0` → `standard`, demais → `limited`) em `src/mocks/fixtures/nfts.ts`; verificar que resultam 24 `limited` e 12 `standard`
- [ ] 2.3 Trocar a chave do banco mock para `kurio.mock.db.v2` em `src/mocks/db/db.ts`; verificar no navegador que um banco v1 salvo é substituído por fixtures com `editionType`
- [ ] 2.4 Filtrar por `editionType` em `GET /api/nfts` (`src/mocks/handlers/catalog.ts`), ignorando valores desconhecidos; verificar no DevTools que `?editionType=limited` retorna `total: 24`
- [ ] 2.5 Enviar `editionType` em `catalogApi.list` (`src/modules/catalog/infrastructure/api.ts`); verificar com `npm run typecheck`
- [ ] 2.6 Documentar o novo parâmetro de `GET /api/nfts` no contrato REST (`ARCHITECTURE.md`/`docs/contracts/`) e a troca da chave do banco mock no README; verificar que os exemplos do documento batem com o handler

## 3. URL e interface

- [ ] 3.1 Adicionar `edicao: 'limitada' | 'padrao'` ao schema de `homeSearch.ts` (valor inválido → `undefined`) e mapear para/de `editionType` no estado normalizado; verificar que `/?edicao=xyz` carrega o catálogo sem filtro e `/?edicao=limitada` envia `editionType=limited`
- [ ] 3.2 Adicionar o `NativeSelect` "Edição" (Todas / Limitada / Padrão) na barra lateral da `HomePage`, controlado pela URL e acionando `update`, e incluir `edicao` em `hasFilters`/"Limpar filtros"; verificar manualmente que selecionar "Limitada" grava `edicao=limitada` e reinicia `page`
- [ ] 3.3 Escrever `tests/e2e/flows/catalog-filters.spec.ts` com os 3 cenários da spec (URL contém `edicao=limitada` e só NFTs limitados; F5 mantém resultados e seleção; página 3 → mudar para "Padrão" volta à página 1 com URL atualizada), e verificar que `npm run test:e2e` passa em desktop e mobile

## 4. Integração

- [ ] 4.1 Rodar `npm run typecheck`, `npm run build` e `npm run test:e2e` e verificar que todos passam sem regressões no catálogo (busca, raridade, coleção, preço e ordenação continuam funcionando)
