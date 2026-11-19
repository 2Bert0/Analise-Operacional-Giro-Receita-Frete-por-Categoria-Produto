# Previsão de Atraso de Pedidos no E-Commerce (Machine Learning Supervisionado)

## Visão Geral do Negócio
Em operações de e-commerce, atrasos na entrega impactam diretamente a retenção de clientes, aumentam custos com suporte/reclamações e desgastam a reputação da marca. Este projeto aplica **Machine Learning supervisionado** sobre dados públicos do e-commerce brasileiro (**Olist**) para estimar o risco de atraso na entrega a partir de informações disponíveis no momento ou logo após a compra — sem vazamento de dados (*data leakage*).

---

## Dados e Preparação
* **Base analisada:** 96.476 pedidos efetivamente entregues com data estimada válida.
* **Variável alvo (`atrasou`):** `1` quando a entrega ao cliente ocorreu após a data estimada (`order_delivered_customer_date > order_estimated_delivery_date`) e `0` caso contrário.
* **Desbalanceamento de classes:** **8,11%** dos pedidos atrasaram (`1`) e **91,89%** foram entregues no prazo ou antecipados (`0`).
* **Engenharia de features (sem vazamento):** prazo estimado em dias, dia da semana e mês da compra, quantidade de itens, valor dos produtos, valor do frete, proporção do frete sobre o valor dos produtos, indicador de mesma UF (`mesma_uf`) e dimensões/peso do produto.
* **Validação:** divisão estratificada (`80%` treino / `20%` teste) e imputação de valores ausentes pela mediana dentro de `Pipeline` do `scikit-learn`.

---

## Modelagem e Resultados
Como a base é desbalanceada (~8,11% de atrasos), o foco da avaliação foi além da acurácia global, acompanhando **ROC-AUC**, **Precision**, **Recall** e **F1-Score** para a classe de atraso (`1`):

| Modelo | ROC-AUC | Precision (Classe 1) | Recall (Classe 1) | F1-Score (Classe 1) | Acurácia |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Regressão Logística (Baseline)** | `0.6283` | `0.111` | `0.599` | `0.188` | `0.579` |
| **Random Forest** | `0.7351` | `0.214` | `0.493` | `0.298` | `0.812` |

O **Random Forest** superou o baseline linear em capacidade discriminativa (`ROC-AUC` de `0.6283` para `0.7351`), equilíbrio entre precisão e recall (`F1-Score` de `0.188` para `0.298`) e acurácia geral (`81.2%`).

---

## Principais Fatores de Atraso
As **5 variáveis mais importantes** no Random Forest concentram cerca de **70,5%** da importância total do modelo:
1. **`mes_compra` (`0.2376`):** efeito de sazonalidade e períodos de maior pressão operacional/climática.
2. **`prazo_estimado_dias` (`0.2284`):** relação direta entre a promessa de prazo e o risco de estouro.
3. **`valor_frete` (`0.1014`):** reflexo de distância, peso ou complexidade logística.
4. **`proporcao_frete` (`0.0697`):** peso relativo do frete frente ao valor do pedido.
5. **`valor_produtos` (`0.0677`):** características do ticket/composição do pedido.

![Importância das Variáveis na Previsão de Atraso](feature_importance_atraso.png)

---

## Recomendações Práticas para o Negócio
* **Ajuste sazonal de prazos e capacidade:** revisar margens de prazo estimado e reforçar capacidade logística nos meses de maior incidência de atrasos (`mes_compra`).
* **Monitoramento preventivo de alto risco:** priorizar acompanhamento de pedidos com maior probabilidade prevista pelo modelo — especialmente rotas/itens com frete elevado e prazos apertados — para agir antes da falha ou enviar comunicação proativa ao cliente.

---

## Stack Utilizada
* **Linguagem & Manipulação:** Python, Pandas
* **Machine Learning:** Scikit-Learn (`Pipeline`, `SimpleImputer`, `train_test_split`, `LogisticRegression`, `RandomForestClassifier`, métricas de classificação e ROC-AUC)
* **Visualização:** Matplotlib, Seaborn
* **Ambiente:** Google Colab e GitHub
