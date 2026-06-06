# Análise de Negócio — Tech Challenge Fase 1

---

## Etapa 1 — Entendimento do Negócio

### Qual problema de negócio está sendo resolvido?

O problema central é a **incapacidade da empresa de antecipar a insatisfação dos clientes**. Atualmente, o NPS é coletado apenas após o encerramento da jornada de compra, ou seja, quando já é tarde demais para agir. A empresa opera de forma reativa: descobre que o cliente está insatisfeito depois que o dano à relação já está feito.

O desafio é transformar dados operacionais disponíveis durante ou logo após a compra (logística, atendimento, pedido) em sinais de alerta precoces, permitindo intervenções proativas antes que o cliente se torne um detrator.

---

### Por que o NPS é importante para um e-commerce?

O NPS é o indicador mais direto de **lealdade** do cliente. Em um e-commerce, onde a barreira de troca para um concorrente é mínima, a diferença entre um promotor e um detrator tem impacto financeiro direto e mensurável:

- **Recompra:** Promotores têm probabilidade significativamente maior de realizar novas compras e detratores tendem a abandonar a plataforma após uma experiência ruim e dificilmente retornam.

- **Boca a boca:** O NPS mede exatamente a propensão de indicação, sendo que em comercios online, avaliações públicas, comentários em redes sociais e indicações orgânicas são canais de aquisição de baixo custo e alto valor. Um detrator não apenas deixa de indicar, ele ativamente desencoraja outros, amplificado pelo alcance das redes sociais.

- **Market share:** Em um mercado com múltiplos players e margens comprimidas, a experiência do cliente é o principal diferenciador. NPS acima da média do setor está diretamente correlacionado com crescimento de market share. 

---

### Quais áreas poderiam se beneficiar dessas informações?

| Área | Como se beneficia |
|---|---|
| **Logística** | Identificar rotas, parceiros ou regiões com maior índice de atraso e correlação com queda de NPS; renegociar SLAs |
| **Atendimento (CS)** | Priorizar clientes com alta probabilidade de detração; melhorar resolução no primeiro contato |
| **Produto** | Entender se itens específicos (quantidade, valor, frete) geram mais insatisfação |
| **Pricing / Comercial** | Avaliar se o valor do desconto e forma de pagamento influenciam a percepção de valor |
| **CRM / Retenção** | Disparar comunicações proativas e ofertas de recuperação para clientes em risco |
| **Estratégia** | Benchmarking de NPS por região e perfil para priorização de investimentos |

---

### Indicadores de mercado que complementariam essa análise

- **Benchmark de NPS por setor:** comparar o NPS da empresa com benchmark da média do mercado contextualiza se os resultados são aceitáveis ou críticos.
- **SLA logístico de mercado:** tempo médio de entrega de concorrentes (ex.: Mercado Livre promete 48h em categorias principais). Desvios acima disso aumentam a percepção de atraso.
- **Taxa de first contact resolution (FCR) do setor:** benchmark de CS indica que empresas líderes resolvem >70% dos casos no primeiro contato.
- **Churn rate por segmento NPS:** correlacionar o NPS com a taxa de cancelamento/inatividade para calcular o impacto financeiro do detrator.
- **CSAT benchmarks:** comparar o `csat_internal_score` com médias setoriais para calibrar se o score interno está bem calibrado.

---

## Etapa 2 — Definição da Target

### Qual variável representa a satisfação do cliente?

A variável **`nps_score`** (escala 0–10), coletada após o encerramento da jornada de compra.

### Por que ela foi escolhida?

O `nps_score` é a única variável que captura diretamente a **percepção do cliente sobre toda a experiência**, considerando não apenas um ponto isolado. Diferente do `csat_internal_score`, que é gerado internamente, o NPS é declarado pelo próprio cliente, tornando-o mais representativo da lealdade real.

A partir dele, é possível segmentar clientes em três grupos com comportamentos distintos e acionáveis:

- **Promotores (9–10):** embaixadores da marca, alta probabilidade de recompra e indicação.
- **Neutros (7–8):** satisfeitos, mas vulneráveis à concorrência.
- **Detratores (0–6):** clientes em risco de churn e potenciais geradores de reviews negativos.

### Em que momento da jornada essa informação é coletada?

Após o encerramento da jornada de compra, geralmente por pesquisa enviada por e-mail ou notificação push após a confirmação de entrega. Isso significa que **no momento da coleta, o pedido já foi entregue e qualquer problema de logística ou atendimento já aconteceu**.

### Existe algum risco de usar essa variável de forma inadequada?

Sim. Os principais riscos são:

1. **Viés de resposta:** nem todos os clientes respondem. Clientes muito insatisfeitos ou muito satisfeitos têm maior propensão a responder, o que pode distorcer a distribuição observada.
2. **Momento de captura:** se a pesquisa for enviada muito depois da entrega, outros fatores externos (humor, contexto) podem influenciar a nota.
3. **Leakage no modelo:** a variável `csat_internal_score` pode estar altamente correlacionada com o NPS por ser coletada no mesmo momento ou calculada com base nele. Seu uso como feature precisa ser avaliado com cuidado.
4. **Conflação de experiências:** para clientes com múltiplos pedidos, o NPS de um pedido pode refletir experiências anteriores, não apenas a jornada analisada.

---

## Etapa 4 — Reflexão sobre Modelo Preditivo

### Estratégia adotada

**Classificação binária:** Satisfeito (NPS ≥ 7) vs Insatisfeito (NPS < 7).

**Justificativa técnica:** A regressão poderia estimar a nota exata, mas o erro de previsão em uma escala contínua de 0–10 tornaria o resultado menos acionável. Do ponto de vista de negócio, o que importa não é saber se o cliente dará 5,3 ou 5,7 — mas sim identificar quem está no grupo de risco.

**Justificativa de negócio:** O modelo alimentaria uma régua de retenção automatizada: clientes com alta probabilidade de insatisfação receberiam contato proativo do CS, cupom de desconto ou comunicação personalizada antes da pesquisa ser disparada.

### Variável alvo

`target = 1 se nps_score >= 7, senão 0`

### Features utilizadas

Variáveis operacionais disponíveis antes ou no momento da entrega:
- Dados do pedido: `order_value`, `items_quantity`, `discount_value`, `payment_installments`
- Dados logísticos: `delivery_time_days`, `delivery_delay_days`, `freight_value`, `delivery_attempts`
- Dados de atendimento: `customer_service_contacts`, `resolution_time_days`, `complaints_count`
- Perfil do cliente: `customer_age`, `customer_tenure_months`, `customer_region`, `repeat_purchase_30d`
- Score interno: `csat_internal_score` (com atenção ao risco de leakage)

### Modelos avaliados

| Modelo | 
|---|
| Regressão Logística  |
| Random Forest |
| Gradient Boosting |

### Avaliação

Métrica principal: **AUC-ROC** — equilibra a capacidade do modelo de distinguir satisfeitos de insatisfeitos, complementada por Precision, Recall e F1 para ajuste operacional (ex.: se o custo de contatar um falso positivo for baixo, prioriza-se Recall).

### Uso prático na empresa

1. O modelo seria retreinado mensalmente com novos dados de pedidos.
2. Diariamente, o score de probabilidade de insatisfação seria calculado para pedidos recém-entregues.
3. Clientes com probabilidade > 0,6 entrariam automaticamente em uma fila prioritária do CS ou receberiam comunicação de recuperação.
4. O impacto seria medido comparando o NPS real dos clientes acionados vs não acionados (A/B test).
