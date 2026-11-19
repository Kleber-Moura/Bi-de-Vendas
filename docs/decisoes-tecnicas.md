# Decisões técnicas

## Separar a origem transacional do consumo analítico

O cabeçalho do pedido guarda atributos compartilhados entre itens. O esquema analítico repete essas chaves em cada item para favorecer consultas por dimensão. A reconciliação será feita pela chave de pedido e item e por totais mensais e regionais, incluindo a quantidade de itens cancelados. O SQL valida os resultados esperados independentemente das medidas do Power BI.

## Compartilhar dimensões no grão da meta

`dim_meses` e `dim_regioes` filtram os dois fatos diretamente. `dim_calendario` e `dim_localidades` ficam ligadas apenas a vendas. Essa escolha mantém relações simples de um para muitos no Power BI e evita que as metas se repitam quando um mês tem vários dias ou uma região tem várias UFs. As chaves técnicas dos fatos serão ocultadas no painel. Medidas explícitas aplicarão as regras de compatibilidade de filtros e de cobertura de metas. [Orientação de esquema estrela no Power BI](https://learn.microsoft.com/pt-br/power-bi/guidance/star-schema)

Nas consultas SQL, some vendas e metas separadamente no grão de mês e região antes de juntá-las. Comece pela grade de meses e regiões desejada quando precisar exibir combinações sem vendas ou verificar metas faltantes.

## Usar IDs estáveis para o cenário sintético

As dimensões de catálogo reaproveitam os identificadores da origem e adotam atualização do tipo 1 para correções de rótulo. O cenário preserva categoria por produto, região por UF, preço praticado e UF atribuída ao pedido. A categoria atua como dimensão independente no Power BI para que a análise use a classificação registrada na carga do item.

## Preservar cancelamentos e manter o escopo financeiro

Itens cancelados ficam na base para permitir auditoria de volume e consistência da carga. O faturamento reconhecido desconsidera esses valores e representa receita comercial após descontos, antes de custos e tributos. O escopo inicial da página Financeiro cobre faturamento, descontos e cancelamentos. Uma `fato_pagamentos` passa a ter justificativa quando houver perguntas sobre meios de pagamento, parcelamento, liquidação ou conciliação. Devoluções, estornos, custos e margem exigiriam dados adicionais.

## Critérios de aceitação para as próximas fases

| Verificação | Resultado esperado |
| --- | --- |
| Chaves primárias, estrangeiras e valores obrigatórios | Bloquear itens órfãos, chaves repetidas, mês incompatível com a data, região incompatível com a UF e valores fora dos domínios. |
| Reconciliação de vendas | Cada item transacional tem um correspondente analítico com mesma chave, categoria de origem e atributos do cabeçalho. O total reconhecido fecha por mês e região. |
| Cobertura diária e mensal | Calendário com 731 dias consecutivos de 2024 e 2025. Dimensão mensal com 18 meses publicados consecutivos. |
| Cobertura de metas | 90 pares distintos de mês e região para a janela inicial, cada um com meta positiva. |
| Comparação com a meta | Somar a meta uma vez por combinação. Recortes de grão inferior ou com meta faltante retornam em branco. Recorte coberto sem vendas concluídas tem 0% de atingimento. |
| Vendas e ticket | Um pedido com dois itens conta uma vez no total de vendas. Cancelamentos preservam as linhas e somam zero ao faturamento. |

### Exemplo de conferência

Considere uma amostra de janeiro e fevereiro de 2025 com duas regiões. Em janeiro, um cliente do Sudeste conclui dois pedidos, com itens de R$ 90 e R$ 30 no primeiro e um item de R$ 80 no segundo. Há também um pedido cancelado de R$ 400. No Sul, um pedido concluído soma R$ 150. Em fevereiro, o Sudeste fatura R$ 300 e o Sul fica sem pedidos.

| Seleção | Faturamento | Meta | Atingimento |
| --- | ---: | ---: | ---: |
| Janeiro, Sudeste | R$ 200 | R$ 400 | 50,00% |
| Janeiro, Sul | R$ 150 | R$ 200 | 75,00% |
| Janeiro, ambas as regiões | R$ 350 | R$ 600 | 58,33% |
| Fevereiro, Sul | R$ 0 | R$ 100 | 0,00% |
| Janeiro e fevereiro, ambas as regiões | R$ 650 | R$ 1.200 | 54,17% |

Ao selecionar janeiro e Sudeste, o esperado é contar duas vendas, um cliente ativo, um cliente recorrente e ticket médio de R$ 100. A meta de fevereiro do Sudeste nesta amostra é de R$ 500. Se a meta de fevereiro do Sul estiver ausente, a comparação para o total dos dois meses fica em branco até a correção. Os valores são exemplos do contrato para validar as futuras implementações SQL e DAX.
