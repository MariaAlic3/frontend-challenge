# Glossário — Marketplace de NFTs

## Contexto: Catálogo
Pasta: src/modules/catalog/

**NFT**: o item colecionável exibido, buscado, filtrado e detalhado no catálogo.
**Detalhe**: a página com galeria, informações e opções de compra de um NFT específico.
**Edição**: a tiragem/versão específica do NFT (ex.: edição limitada), exibida no catálogo e no carrinho.
## Contexto: Carrinho
Pasta: src/modules/cart/

**Item**: um NFT (com sua edição e quantidade) adicionado ao carrinho.
**Cupom**: código aplicado ao carrinho que gera desconto, podendo ser inválido ou expirado.
**Subtotal**: soma dos itens do carrinho antes de descontos e taxas.
## Palavra que muda de sentido: Quantidade

No **Catálogo**, quantidade é quanto daquela edição do NFT ainda está disponível (atributo do produto).
No **Carrinho**, quantidade é quanto o colecionador decidiu comprar (valor editável pelo usuário, que afeta o subtotal).
