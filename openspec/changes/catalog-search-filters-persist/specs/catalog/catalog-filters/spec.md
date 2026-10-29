# Spec Delta

## Purpose

Define como os filtros do catálogo de NFTs são aplicados, representados na URL e restaurados após refresh, garantindo que a paginação acompanhe as mudanças de filtro.

## ADDED Requirements

### Requirement: Filtro de edição refletido na URL
O catálogo SHALL oferecer o filtro "Edição" com as opções "Limitada" e "Padrão". Ao aplicar uma opção, a URL MUST conter o parâmetro `edicao` com o valor correspondente (`limitada` ou `padrao`) e o catálogo MUST listar apenas os NFTs daquele tipo de edição, conforme a resposta da API.

#### Scenario: Filtro aplicado aparece na URL
- **GIVEN** que estou na página de catálogo
- **WHEN** eu aplico o filtro "Edição: Limitada"
- **THEN** a URL deve conter o parâmetro "edicao=limitada"
- **AND** o catálogo deve mostrar só os NFTs dessa edição

### Requirement: Filtro de edição restaurado após refresh
Ao carregar o catálogo com o parâmetro `edicao` na URL, inclusive após atualizar a página, o sistema SHALL listar apenas os NFTs da edição indicada e MUST exibir a opção correspondente como selecionada no controle de filtro.

#### Scenario: Refresh da página mantém o filtro aplicado
- **GIVEN** que apliquei o filtro "Edição: Limitada" e a URL contém "edicao=limitada"
- **WHEN** eu atualizo a página (F5)
- **THEN** o catálogo deve continuar mostrando só os NFTs da edição "Limitada"
- **AND** o filtro "Edição: Limitada" deve aparecer selecionado na interface

### Requirement: Mudança de filtro reinicia a paginação
Quando o colecionador muda o filtro de edição, o catálogo SHALL voltar para a página 1 dos resultados, e a URL MUST refletir o novo filtro e a página 1.

#### Scenario: Mudar o filtro reinicia a paginação
- **GIVEN** que estou na página 3 dos resultados filtrados por "Edição: Limitada"
- **WHEN** eu mudo o filtro para "Edição: Padrão"
- **THEN** devo ser levada de volta à página 1 dos resultados
- **AND** a URL deve refletir tanto o novo filtro quanto a página 1
