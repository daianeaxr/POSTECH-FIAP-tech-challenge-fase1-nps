# 📊 Tech Challenge Fase 1 — NPS Preditivo

## Objetivo do Projeto

Este projeto tem como objetivo analisar dados operacionais de um e-commerce para entender os fatores que influenciam a satisfação dos clientes (medida pelo NPS — Net Promoter Score) e construir um modelo preditivo capaz de antecipar o nível de satisfação antes da aplicação da pesquisa.

---

## 📁 Estrutura de Pastas

```
nps_project/
│
├── data/
│   └── desafio_nps_fase_1.csv       # Base de dados original
│
├── notebooks/
│   ├── nps_analise.ipynb            # Notebook principal (EDA + Modelo)
│   └── nps_analise_executado.ipynb  # Notebook executado com outputs
│
├── reports/
│   ├── 01_distribuicao_nps.png
│   ├── 02_logistica_nps.png
│   ├── 03_atendimento_nps.png
│   ├── 04_regiao_perfil_nps.png
│   ├── 05_correlacao_nps.png
│   ├── 06_heatmap_promotor_detrator.png
│   ├── 07_curva_roc.png
│   ├── 08_feature_importance.png
│   └── 09_matriz_confusao.png
│
├── analise_negocio.md               # Respostas discursivas (etapas 1, 2 e 4)
└── README.md
```

---

## 🗂️ Descrição da Base de Dados

| Variável | Descrição |
|---|---|
| `customer_id` | ID único do cliente |
| `order_id` | ID único do pedido |
| `customer_age` | Idade do cliente |
| `customer_region` | Região geográfica |
| `customer_tenure_months` | Tempo de relacionamento (meses) |
| `order_value` | Valor total do pedido |
| `items_quantity` | Quantidade de itens |
| `discount_value` | Desconto aplicado |
| `payment_installments` | Número de parcelas |
| `delivery_time_days` | Tempo total de entrega (dias) |
| `delivery_delay_days` | Dias de atraso na entrega |
| `freight_value` | Valor do frete |
| `delivery_attempts` | Tentativas de entrega |
| `customer_service_contacts` | Contatos com atendimento |
| `resolution_time_days` | Tempo de resolução de problemas |
| `complaints_count` | Número de reclamações |
| `repeat_purchase_30d` | Recompra em 30 dias (0/1) |
| `csat_internal_score` | Score interno de satisfação |
| `nps_score` | **Target** — Nota NPS (0–10) |

---

## 🔬 Metodologia

1. **Análise exploratória (EDA):** estatísticas descritivas, distribuição do NPS, análise por segmento (promotores, neutros, detratores), correlações e comparativo entre perfis.
2. **Definição da target:** variável binária — Satisfeito (NPS ≥ 7) vs Insatisfeito (NPS < 7).
3. **Modelos testados:** Regressão Logística, Random Forest e Gradient Boosting.
4. **Avaliação:** AUC-ROC, Precision, Recall, F1-Score e Matriz de Confusão.

---

## ▶️ Como Reproduzir

```bash
# 1. Instalar dependências
pip install pandas numpy matplotlib seaborn scikit-learn jupyter

# 2. Executar o notebook
jupyter notebook notebooks/nps_analise.ipynb
```

Ou executar diretamente via linha de comando:

```bash
jupyter nbconvert --to notebook --execute notebooks/nps_analise.ipynb --output nps_analise_executado.ipynb --output-dir notebooks/
```

---

## 👩‍💻 Tecnologias Utilizadas

- Python 3.x
- Pandas, NumPy
- Matplotlib, Seaborn
- Scikit-learn
- Jupyter Notebook
