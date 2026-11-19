# Dicionário de dados

**PK** indica chave primária, **FK** chave estrangeira e **UQ** restrição de unicidade. Todos os campos são obrigatórios. IDs numéricos e números de item são positivos e estáveis entre reprocessamentos do mesmo cenário. Os valores monetários estão em reais (BRL), com duas casas decimais.

## Esquema `transacional`

| Tabela | Coluna | Tipo PostgreSQL | Chave e regra |
| --- | --- | --- | --- |
| `regioes` | `id_regiao` | `smallint` | PK. Código do catálogo das cinco macrorregiões brasileiras. |
| `regioes` | `nome_regiao` | `text` | UQ. Norte, Nordeste, Centro-Oeste, Sudeste ou Sul. |
| `localidades` | `id_localidade` | `smallint` | PK. Uma linha por UF, incluindo o Distrito Federal. |
| `localidades` | `sigla_uf` | `text` | UQ. Uma das 27 siglas oficiais de UF. |
| `localidades` | `nome_uf` | `text` | Nome da UF. |
| `localidades` | `id_regiao` | `smallint` | FK para `regioes.id_regiao`. Pertencimento conforme catálogo de UFs. |
| `categorias` | `id_categoria` | `integer` | PK. |
| `categorias` | `nome_categoria` | `text` | UQ. Rótulo da categoria. |
| `produtos` | `id_produto` | `integer` | PK. |
| `produtos` | `nome_produto` | `text` | Nome comercial fictício. |
| `produtos` | `id_categoria` | `integer` | FK para `categorias.id_categoria`. Categoria fixa por produto no cenário inicial. |
| `clientes` | `id_cliente` | `integer` | PK. Identificador sintético. |
| `clientes` | `data_cadastro` | `date` | Data de início da relação comercial. Antecede ou coincide com a data do primeiro pedido. |
| `clientes` | `id_localidade` | `smallint` | FK para `localidades.id_localidade`. UF do cadastro. |
| `vendedores` | `id_vendedor` | `integer` | PK. Identificador sintético. |
| `vendedores` | `nome_vendedor` | `text` | Rótulo fictício de vendedor ou da operação de um canal de autosserviço. |
| `canais` | `id_canal` | `smallint` | PK. |
| `canais` | `nome_canal` | `text` | UQ. Exemplos de rótulo incluem Loja física, E-commerce e Marketplace. |
| `pedidos` | `id_pedido` | `bigint` | PK. |
| `pedidos` | `data_venda` | `date` | Data comercial do pedido, inclusive quando cancelado. |
| `pedidos` | `situacao_pedido` | `text` | `concluido` ou `cancelado`. |
| `pedidos` | `id_cliente` | `integer` | FK para `clientes.id_cliente`. |
| `pedidos` | `id_vendedor` | `integer` | FK para `vendedores.id_vendedor`. |
| `pedidos` | `id_canal` | `smallint` | FK para `canais.id_canal`. |
| `pedidos` | `id_localidade` | `smallint` | FK para `localidades.id_localidade`. UF do cliente capturada no pedido. |
| `itens_pedido` | `id_pedido` | `bigint` | Parte da PK composta, FK para `pedidos.id_pedido`. |
| `itens_pedido` | `numero_item` | `smallint` | Parte da PK composta. Ordem do item no pedido. |
| `itens_pedido` | `id_produto` | `integer` | FK para `produtos.id_produto`. |
| `itens_pedido` | `quantidade` | `smallint` | Maior que zero, inclusive para itens cancelados. |
| `itens_pedido` | `preco_unitario` | `numeric(12,2)` | Maior que zero. Preço unitário praticado antes do desconto. |
| `itens_pedido` | `valor_desconto` | `numeric(18,2)` | De zero até `quantidade * preco_unitario`. Desconto total da linha. |
| `metas_faturamento` | `mes_referencia` | `date` | Parte da PK composta. Primeiro dia do mês coberto. |
| `metas_faturamento` | `id_regiao` | `smallint` | Parte da PK composta, FK para `regioes.id_regiao`. |
| `metas_faturamento` | `valor_meta` | `numeric(18,2)` | Maior que zero. Faturamento previsto para o mês na região. |

## Esquema `analitico`

### Dimensões

As dimensões oriundas do esquema transacional reutilizam os IDs de origem e os tipos de suas colunas homônimas. Os tipos e chaves de todas as colunas são definidos abaixo.

| Tabela | Coluna | Tipo PostgreSQL | Chave e regra |
| --- | --- | --- | --- |
| `dim_regioes` | `id_regiao` | `smallint` | PK. Origem em `regioes`. |
| `dim_regioes` | `nome_regiao` | `text` | UQ. Nome da macrorregião. |
| `dim_localidades` | `id_localidade` | `smallint` | PK. Origem em `localidades`. |
| `dim_localidades` | `sigla_uf` | `text` | UQ. Sigla oficial da UF. |
| `dim_localidades` | `nome_uf` | `text` | Nome da UF. |
| `dim_localidades` | `id_regiao` | `smallint` | Copiado da origem. Com `id_localidade`, forma uma UQ para a FK composta de vendas. |
| `dim_categorias` | `id_categoria` | `integer` | PK. Origem em `categorias`. |
| `dim_categorias` | `nome_categoria` | `text` | UQ. Rótulo da categoria. |
| `dim_produtos` | `id_produto` | `integer` | PK. Origem em `produtos`. |
| `dim_produtos` | `nome_produto` | `text` | Nome comercial fictício. |
| `dim_clientes` | `id_cliente` | `integer` | PK. Origem em `clientes`. |
| `dim_clientes` | `data_cadastro` | `date` | Data de cadastro. |
| `dim_vendedores` | `id_vendedor` | `integer` | PK. Origem em `vendedores`. |
| `dim_vendedores` | `nome_vendedor` | `text` | Rótulo fictício. |
| `dim_canais` | `id_canal` | `smallint` | PK. Origem em `canais`. |
| `dim_canais` | `nome_canal` | `text` | UQ. Rótulo do canal. |
| `dim_calendario` | `data` | `date` | PK. Uma linha por dia em anos completos, sem lacunas. |
| `dim_calendario` | `mes_referencia` | `date` | Primeiro dia do mês de `data`. Com `data`, forma uma UQ para a FK composta de vendas. |
| `dim_calendario` | `dia_mes` | `smallint` | Dia do mês de `data`, de 1 a 31. |
| `dim_calendario` | `dia_semana_iso` | `smallint` | Dia da semana de `data`, de 1 (segunda-feira) a 7 (domingo). |
| `dim_meses` | `mes_referencia` | `date` | PK. Primeiro dia de cada mês da janela publicada de dados. Chave de ordenação cronológica. |
| `dim_meses` | `ano` | `smallint` | Ano de `mes_referencia`. |
| `dim_meses` | `numero_mes` | `smallint` | Mês de `mes_referencia`, de 1 a 12. |
| `dim_meses` | `trimestre` | `smallint` | Trimestre civil de `mes_referencia`, de 1 a 4. |

### Fatos

| Tabela | Coluna | Tipo PostgreSQL | Chave e regra |
| --- | --- | --- | --- |
| `fato_vendas` | `id_pedido` | `bigint` | Parte da PK composta. Identificador do pedido para contagem distinta. |
| `fato_vendas` | `numero_item` | `smallint` | Parte da PK composta. Copiado de `itens_pedido`. |
| `fato_vendas` | `data_venda` | `date` | FK para `dim_calendario.data`. Copiada de `pedidos`. |
| `fato_vendas` | `mes_referencia` | `date` | FK para `dim_meses.mes_referencia`. Derivado de `data_venda`. |
| `fato_vendas` | `id_regiao` | `smallint` | FK para `dim_regioes.id_regiao`. Derivado da UF do pedido. |
| `fato_vendas` | `id_localidade` | `smallint` | FK para `dim_localidades.id_localidade`. UF do pedido. |
| `fato_vendas` | `id_cliente` | `integer` | FK para `dim_clientes.id_cliente`. Cliente do pedido. |
| `fato_vendas` | `id_vendedor` | `integer` | FK para `dim_vendedores.id_vendedor`. Vendedor do pedido. |
| `fato_vendas` | `id_canal` | `smallint` | FK para `dim_canais.id_canal`. Canal do pedido. |
| `fato_vendas` | `id_produto` | `integer` | FK para `dim_produtos.id_produto`. Produto do item. |
| `fato_vendas` | `id_categoria` | `integer` | FK para `dim_categorias.id_categoria`. Categoria do produto na origem. |
| `fato_vendas` | `situacao_pedido` | `text` | Cópia da situação do cabeçalho, igual em todos os itens do pedido. |
| `fato_vendas` | `quantidade` | `smallint` | Cópia da quantidade de origem. |
| `fato_vendas` | `preco_unitario` | `numeric(12,2)` | Cópia do preço de origem. |
| `fato_vendas` | `valor_bruto` | `numeric(18,2)` | `quantidade * preco_unitario`. |
| `fato_vendas` | `valor_desconto` | `numeric(18,2)` | Cópia do desconto total do item. |
| `fato_vendas` | `valor_faturamento` | `numeric(18,2)` | `valor_bruto - valor_desconto` para pedido concluído. Zero para pedido cancelado. |
| `fato_metas` | `mes_referencia` | `date` | Parte da PK composta, FK para `dim_meses.mes_referencia`. |
| `fato_metas` | `id_regiao` | `smallint` | Parte da PK composta, FK para `dim_regioes.id_regiao`. |
| `fato_metas` | `valor_meta` | `numeric(18,2)` | Cópia da meta positiva em `metas_faturamento`. |

Restrições compostas adicionais de `fato_vendas`:

- `(data_venda, mes_referencia)` → `dim_calendario(data, mes_referencia)`.
- `(id_localidade, id_regiao)` → `dim_localidades(id_localidade, id_regiao)`.

A categoria do item e os atributos copiados do cabeçalho são reconciliados com a origem durante a carga. Os identificadores de rastreio permitem essa conferência sem criar dependência por FK entre os dois esquemas.
