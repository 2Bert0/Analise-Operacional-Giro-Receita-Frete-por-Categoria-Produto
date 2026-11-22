# Análise Operacional de Giro, Receita e Frete por Categoria e Produto (Olist)

## Objetivo e Problema de Negócio
Avaliar o desempenho comercial e logístico de vendas na base pública da Olist para responder:
- Quais categorias geram maior receita total e volume de itens vendidos (giro)?
- Onde o frete pesa mais proporcionalmente sobre o valor dos produtos (`peso_frete = frete_total / receita_total`), indicando possível atrito comercial ou oportunidade de ajuste logístico?
- Como o giro de unidades se concentra entre os produtos (`product_id`) para evitar decisões de sortimento e estoque baseadas apenas em médias gerais?

## Etapas e Decisões Metodológicas
1. **Filtragem de pedidos válidos e granularidade de item:** Remoção de pedidos com status `canceled` antes do cruzamento com `items` (`how="inner"`), mantendo a granularidade no nível de item de pedido e enriquecendo com categoria e peso do produto (`how="left"`).
2. **Agregação por categoria e classificação de giro:** Cálculo de `itens_vendidos`, `pedidos_unicos`, `receita_total`, `frete_total`, `ticket_medio`, `peso_frete` (com proteção contra divisão por zero) e `perfil_giro` (`Alto Giro` vs. `Baixo Giro` a partir da mediana de `itens_vendidos`).
3. **Visualização comparativa em dois painéis:** Gráfico horizontal com `receita_total` e `peso_frete` nas Top 10 categorias por receita (`analise_categorias_frete.png`), evitando misturar escalas diferentes no mesmo eixo.
4. **Análise de concentração de giro por produto (Pareto) e exportação:** Agrupamento por `product_id` e `categoria`, cálculo da participação acumulada (`pct_acumulado_giro`) e exportação de `resumo_categorias_giro_frete.csv`, `resumo_produtos_pareto.csv` e `curva_pareto_giro_produtos.png`.

## Principais Resultados
- **Base analisada:** `112.108` linhas de itens vendidos em pedidos não cancelados, cobrindo `73` categorias únicas e `32.735` SKUs distintos com venda.
- **Destaques do Top 10 por receita:** `beleza_saude` (`R$ 1.255.695,13`, `peso_frete = 0,14`) lidera em receita, `relogios_presentes` (`R$ 1.198.185,21`) tem o maior ticket médio (`R$ 200,70`) e menor peso relativo de frete (`0,08`), enquanto `moveis_decoracao` (`0,24`) e `utilidades_domesticas` (`0,23`) apresentam os maiores pesos de frete entre as Top 10.
- **Concentração do giro (Pareto):** Os **20% produtos mais vendidos (`6.547` itens)** respondem por **67,2%** de todo o giro de unidades.

## Limitações e Recomendações Práticas
- **Limitação da base:** O dataset público da Olist não traz o custo de aquisição da mercadoria (CMV), portanto os resultados refletem receita, giro e proporção de frete — não margem líquida real.
- **Recomendações:** Não tomar decisões de corte de categoria inteira apenas pelo `peso_frete` ou pela saída do Top 10; revisar políticas de frete/região, testar kits ou combos para diluir frete e priorizar reposição/sortimento nos SKUs que puxam a maior parte do giro.

## Ferramentas e Artefatos Gerados
- **Stack:** Python, `pandas`, `matplotlib`, `seaborn`, `kagglehub` (Google Colab).
- **Arquivos gerados:** `resumo_categorias_giro_frete.csv`, `resumo_produtos_pareto.csv`, `analise_categorias_frete.png` e `curva_pareto_giro_produtos.png`.
