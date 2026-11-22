# Análise Operacional de Giro, Receita e Frete por Categoria e Produto

## Objetivo e Problema de Negócio
Avaliar o desempenho comercial e logístico de vendas na base pública da Olist para identificar:
- Quais categorias concentram maior receita e volume de itens vendidos (giro).
- Onde o frete pesa mais proporcionalmente sobre o preço do produto (`peso_frete = frete_total / receita_total`), sinalizando possível atrito comercial ou oportunidade de ajuste logístico.
- Quais variáveis mais se destacam na modelagem (`mes_compra`, `prazo_estimado_dias`, `valor_frete`, `proporcao_frete`, `valor_produtos`) e como se distribui o giro no nível de produto (`product_id`) para evitar decisões de sortimento baseadas apenas em médias agregadas.

## Etapas e Decisões Metodológicas
1. **Granularidade e limpeza de vendas válidas:** Cruzamento no nível de item de pedido (`order_item`) filtrando pedidos não cancelados (`order_status != 'canceled'`) e enriquecendo com categoria e peso do produto, evitando inflar faturamento e volume com vendas inválidas.
2. **Métricas de categoria e classificação de giro:** Cálculo de itens vendidos, pedidos únicos, receita total, frete total, ticket médio, peso relativo do frete (`frete / receita`) e classificação relativa de giro pela mediana para não confundir categorias de alto volume com categorias de ticket elevado.
3. **Visualização comparativa em dois painéis:** Gráfico horizontal em dois painéis (`receita_total` e `peso_frete` nas Top 10 categorias) para evitar misturar escalas diferentes e destacar o risco de ignorar a cauda longa fora do Top 10.
4. **Concentração de giro por SKU (Pareto), importância de variáveis e exportação:** Agregação por `product_id` e `categoria` para medir a concentração de unidades vendidas, visualização das Top 5 variáveis mais importantes no modelo (`top5_variaveis_importantes.png`) e exportação das tabelas consolidadas em CSV e da curva de Pareto em PNG.

## Principais Resultados
- **Volume e catálogo:** `112.108` linhas de itens vendidos válidas, distribuídas em `73` categorias e `32.735` SKUs distintos com venda.
- **Contraste entre categorias no Top 10 por receita:** Categorias como `moveis_decoracao` (`peso_frete = 0.24`) e `utilidades_domesticas` (`peso_frete = 0.23`) têm peso de frete alto sobre a receita, enquanto `relogios_presentes` combina ticket médio elevado (`R$ 200,70`) com peso de frete baixo (`0.08`).
- **Concentração do giro (Pareto):** Os **20% produtos mais vendidos (`6.547` SKUs)** concentram **67,2%** de todo o giro de unidades, enquanto os demais `80%` (`26.188` SKUs) dividem os `32,8%` restantes.
- **Variáveis de maior importância:** `mes_compra` (`0.2376`), `prazo_estimado_dias` (`0.2284`), `valor_frete` (`0.1014`), `proporcao_frete` (`0.0697`) e `valor_produtos` (`0.0677`).

## Limitações e Recomendações Práticas
- **Limitação da base:** A base da Olist não contém o custo de aquisição dos produtos (CMV), logo a análise avalia receita, giro e peso de frete — não lucro líquido real.
- **Ação prática:** Evitar cortar uma categoria inteira (como `ferramentas_jardim`) apenas pela média de frete ou posição no Top 10; em vez disso, revisar regras de frete/região, testar kits/cross-sell para diluir frete e separar SKUs de alto giro dos itens de baixa saída.

## Ferramentas e Artefatos Gerados
- **Stack:** Python, `pandas`, `matplotlib`, `seaborn`, `kagglehub` (Google Colab).
- **Arquivos gerados:** `resumo_categorias_giro_frete.csv`, `resumo_produtos_pareto.csv`, `analise_categorias_frete.png`, `curva_pareto_giro_produtos.png` e `top5_variaveis_importantes.png`.

