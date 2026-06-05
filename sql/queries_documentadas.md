# SQL Queries — Brazilian E-Commerce (Olist)

Documentación completa de las queries utilizadas en el análisis. Cada consulta incluye su propósito de negocio y la lógica técnica aplicada.

> **Motor:** MySQL 8.0 | **Prefijo de tablas:** `be_`

---

## Módulo 1 — Commercial Performance Analysis

Objetivo: Entender la evolución del negocio, identificar las categorías y regiones más valiosas y detectar patrones de estacionalidad.

---

### 1.1 Evolución mensual de ventas

**Propósito de negocio:** Medir el crecimiento mes a mes del marketplace en términos de órdenes, clientes únicos, revenue y ticket promedio.

```sql
SELECT
  DATE_FORMAT(o.order_purchase_timestamp, '%Y-%m') AS mes,
  COUNT(DISTINCT o.order_id)                        AS total_ordenes,
  COUNT(DISTINCT o.customer_id)                     AS clientes_unicos,
  ROUND(SUM(oi.price), 2)                           AS revenue_total,
  ROUND(SUM(oi.freight_value), 2)                   AS flete_total,
  ROUND(AVG(oi.price), 2)                           AS ticket_promedio
FROM be_orders o
JOIN be_order_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY mes
ORDER BY mes;
```

---

### 1.2 Top 10 categorías por revenue

**Propósito de negocio:** Identificar qué categorías concentran el mayor volumen de ingresos y su participación relativa en el total.

```sql
SELECT
  COALESCE(p.product_category_name, 'Sin categoría') AS categoria,
  COUNT(DISTINCT o.order_id)                          AS total_ordenes,
  ROUND(SUM(oi.price), 2)                             AS revenue_total,
  ROUND(AVG(oi.price), 2)                             AS ticket_promedio,
  ROUND(SUM(oi.price) * 100.0 /
    SUM(SUM(oi.price)) OVER(), 2)                     AS pct_revenue
FROM be_orders o
JOIN be_order_items oi ON o.order_id    = oi.order_id
JOIN be_products p     ON oi.product_id = p.product_id
WHERE o.order_status = 'delivered'
GROUP BY categoria
ORDER BY revenue_total DESC
LIMIT 10;
```

---

### 1.3 Performance por estado de Brasil

**Propósito de negocio:** Evaluar la distribución geográfica de la demanda para identificar mercados prioritarios y oportunidades de expansión regional.

```sql
SELECT
  c.customer_state              AS estado,
  COUNT(DISTINCT o.order_id)    AS total_ordenes,
  COUNT(DISTINCT o.customer_id) AS clientes_unicos,
  ROUND(SUM(oi.price), 2)       AS revenue,
  ROUND(AVG(oi.price), 2)       AS ticket_promedio
FROM be_orders o
JOIN be_order_items oi ON o.order_id    = oi.order_id
JOIN be_customers c    ON o.customer_id = c.customer_id
WHERE o.order_status = 'delivered'
GROUP BY estado
ORDER BY revenue DESC
LIMIT 10;
```

---

### 1.4 Estacionalidad por día de la semana

**Propósito de negocio:** Detectar patrones de comportamiento de compra según el día para optimizar campañas y capacidad operativa.

```sql
SELECT
  DAYNAME(o.order_purchase_timestamp)     AS dia_semana,
  DAYOFWEEK(o.order_purchase_timestamp)   AS orden_dia,
  COUNT(DISTINCT o.order_id)              AS total_ordenes,
  ROUND(SUM(oi.price), 2)                 AS gmv,
  ROUND(AVG(oi.price), 2)                 AS ticket_promedio,
  ROUND(SUM(oi.price) * 100.0 /
    SUM(SUM(oi.price)) OVER(), 2)         AS pct_revenue
FROM be_orders o
JOIN be_order_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY dia_semana, orden_dia
ORDER BY orden_dia;
```

---

## Módulo 2 — Customer Intelligence & RFM Segmentation

Objetivo: Entender el comportamiento de compra, medir la recurrencia y segmentar la base de clientes por valor usando metodología RFM.

---

### 2.1 Tasa de recompra

**Propósito de negocio:** Medir qué proporción de clientes realizó más de una compra — métrica clave para evaluar la salud de la retención.

```sql
WITH customer_orders AS (
    SELECT
        c.customer_unique_id,
        COUNT(DISTINCT o.order_id) AS total_orders
    FROM be_orders o
    JOIN be_customers c ON o.customer_id = c.customer_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
)
SELECT
    CASE
        WHEN total_orders = 1 THEN 'One-Time Buyer'
        ELSE 'Repeat Buyer'
    END AS customer_type,
    COUNT(*) AS customers,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS pct_customers
FROM customer_orders
GROUP BY customer_type;
```

---

### 2.2 Distribución de órdenes por cliente

**Propósito de negocio:** Visualizar la distribución de frecuencia de compra para entender el perfil de comportamiento de la base de clientes.

```sql
WITH customer_orders AS (
    SELECT
        c.customer_unique_id,
        COUNT(DISTINCT o.order_id) AS total_orders
    FROM be_orders o
    JOIN be_customers c ON o.customer_id = c.customer_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
)
SELECT
    total_orders,
    COUNT(*) AS customers
FROM customer_orders
GROUP BY total_orders
ORDER BY total_orders;
```

---

### 2.3 Top 20 clientes por revenue

**Propósito de negocio:** Identificar los clientes de mayor valor individual para programas de atención prioritaria.

```sql
SELECT
    c.customer_unique_id,
    COUNT(DISTINCT o.order_id)                          AS total_orders,
    ROUND(SUM(oi.price), 2)                             AS revenue,
    ROUND(SUM(oi.price) / COUNT(DISTINCT o.order_id), 2) AS avg_order_value
FROM be_orders o
JOIN be_customers c    ON o.customer_id  = c.customer_id
JOIN be_order_items oi ON o.order_id     = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY c.customer_unique_id
ORDER BY revenue DESC
LIMIT 20;
```

---

### 2.4 Análisis de Pareto de clientes

**Propósito de negocio:** Verificar si el revenue sigue una distribución 80/20 — identifica qué porcentaje de clientes concentra el mayor valor acumulado.

```sql
WITH customer_revenue AS (
    SELECT
        c.customer_unique_id,
        ROUND(SUM(oi.price), 2) AS revenue
    FROM be_orders o
    JOIN be_customers c    ON o.customer_id  = c.customer_id
    JOIN be_order_items oi ON o.order_id     = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
),
ranked AS (
    SELECT
        customer_unique_id,
        revenue,
        ROW_NUMBER() OVER (ORDER BY revenue DESC)        AS rn,
        COUNT(*) OVER ()                                 AS total_customers,
        SUM(revenue) OVER (ORDER BY revenue DESC)        AS cumulative_revenue,
        SUM(revenue) OVER ()                             AS total_revenue
    FROM customer_revenue
)
SELECT
    customer_unique_id,
    revenue,
    ROUND(cumulative_revenue / total_revenue * 100, 2) AS cumulative_pct
FROM ranked
ORDER BY revenue DESC;
```

---

### 2.5 Construcción del modelo RFM

**Propósito de negocio:** Calcular las tres dimensiones de valor del cliente — Recencia, Frecuencia y Valor Monetario — como base para la segmentación.

```sql
WITH customer_rfm AS (
    SELECT
        c.customer_unique_id,
        DATEDIFF(
            (SELECT MAX(order_purchase_timestamp) FROM be_orders),
            MAX(o.order_purchase_timestamp)
        )                          AS recency,
        COUNT(DISTINCT o.order_id) AS frequency,
        ROUND(SUM(oi.price), 2)    AS monetary
    FROM be_orders o
    JOIN be_customers c    ON o.customer_id  = c.customer_id
    JOIN be_order_items oi ON o.order_id     = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
)
SELECT * FROM customer_rfm;
```

---

### 2.6 Scoring RFM con NTILE

**Propósito de negocio:** Asignar puntuaciones del 1 al 5 a cada dimensión RFM usando quintiles para normalizar el scoring entre clientes.

```sql
WITH customer_rfm AS (
    SELECT
        c.customer_unique_id,
        DATEDIFF(
            (SELECT MAX(order_purchase_timestamp) FROM be_orders),
            MAX(o.order_purchase_timestamp)
        )                          AS recency,
        COUNT(DISTINCT o.order_id) AS frequency,
        ROUND(SUM(oi.price), 2)    AS monetary
    FROM be_orders o
    JOIN be_customers c    ON o.customer_id  = c.customer_id
    JOIN be_order_items oi ON o.order_id     = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
)
SELECT
    customer_unique_id,
    recency,
    frequency,
    monetary,
    NTILE(5) OVER (ORDER BY recency DESC)   AS r_score,
    NTILE(5) OVER (ORDER BY frequency)      AS f_score,
    NTILE(5) OVER (ORDER BY monetary)       AS m_score
FROM customer_rfm;
```

---

### 2.7 Segmentación RFM

**Propósito de negocio:** Clasificar a cada cliente en un segmento estratégico (Champions, Loyal, New, At Risk) basado en su score RFM combinado.

```sql
WITH customer_rfm AS (
    SELECT
        c.customer_unique_id,
        DATEDIFF(
            (SELECT MAX(order_purchase_timestamp) FROM be_orders),
            MAX(o.order_purchase_timestamp)
        )                          AS recency,
        COUNT(DISTINCT o.order_id) AS frequency,
        ROUND(SUM(oi.price), 2)    AS monetary
    FROM be_orders o
    JOIN be_customers c    ON o.customer_id  = c.customer_id
    JOIN be_order_items oi ON o.order_id     = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
),
score_rfm AS (
    SELECT
        customer_unique_id, recency, frequency, monetary,
        NTILE(5) OVER (ORDER BY recency DESC)  AS r_score,
        NTILE(5) OVER (ORDER BY frequency)     AS f_score,
        NTILE(5) OVER (ORDER BY monetary)      AS m_score
    FROM customer_rfm
)
SELECT
    *,
    CASE
        WHEN r_score >= 4 AND f_score >= 4 AND m_score >= 4 THEN 'Champions'
        WHEN f_score >= 4 AND m_score >= 4                  THEN 'Loyal Customers'
        WHEN r_score >= 4 AND f_score <= 2                  THEN 'New Customers'
        WHEN r_score <= 2 AND f_score <= 2                  THEN 'At Risk'
        ELSE 'Others'
    END AS segment
FROM score_rfm;
```

---

### 2.8 Revenue por segmento RFM

**Propósito de negocio:** Cuantificar el valor económico de cada segmento para priorizar estrategias de retención e inversión comercial.

```sql
WITH customer_rfm AS (
    SELECT
        c.customer_unique_id,
        DATEDIFF(
            (SELECT MAX(order_purchase_timestamp) FROM be_orders),
            MAX(o.order_purchase_timestamp)
        )                          AS recency,
        COUNT(DISTINCT o.order_id) AS frequency,
        ROUND(SUM(oi.price), 2)    AS monetary
    FROM be_orders o
    JOIN be_customers c    ON o.customer_id  = c.customer_id
    JOIN be_order_items oi ON o.order_id     = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
),
score_rfm AS (
    SELECT
        customer_unique_id, recency, frequency, monetary,
        NTILE(5) OVER (ORDER BY recency DESC)  AS r_score,
        NTILE(5) OVER (ORDER BY frequency)     AS f_score,
        NTILE(5) OVER (ORDER BY monetary)      AS m_score
    FROM customer_rfm
),
rfm_segments AS (
    SELECT *,
        CASE
            WHEN r_score >= 4 AND f_score >= 4 AND m_score >= 4 THEN 'Champions'
            WHEN f_score >= 4 AND m_score >= 4                  THEN 'Loyal Customers'
            WHEN r_score >= 4 AND f_score <= 2                  THEN 'New Customers'
            WHEN r_score <= 2 AND f_score <= 2                  THEN 'At Risk'
            ELSE 'Others'
        END AS segment
    FROM score_rfm
)
SELECT
    segment,
    COUNT(*)                                               AS customers,
    ROUND(SUM(monetary), 2)                               AS revenue,
    ROUND(AVG(monetary), 2)                               AS avg_customer_value,
    ROUND(SUM(monetary) * 100 / SUM(SUM(monetary)) OVER(), 2) AS pct_revenue
FROM rfm_segments
GROUP BY segment
ORDER BY revenue DESC;
```

---

## Módulo 3 — Seller Analysis

Objetivo: Evaluar el performance de los vendedores del marketplace, identificar los más valiosos y detectar riesgo de abandono.

---

### 3.1 Performance general por seller

**Propósito de negocio:** Obtener una vista completa del desempeño individual de cada vendedor en términos de órdenes, productos y revenue.

```sql
SELECT
    s.seller_id,
    COUNT(DISTINCT o.order_id)    AS total_orders,
    COUNT(DISTINCT oi.product_id) AS unique_products,
    ROUND(SUM(oi.price), 2)       AS revenue,
    ROUND(AVG(oi.price), 2)       AS avg_ticket
FROM be_orders o
JOIN be_order_items oi ON o.order_id  = oi.order_id
JOIN be_sellers s      ON oi.seller_id = s.seller_id
WHERE o.order_status = 'delivered'
GROUP BY s.seller_id
ORDER BY revenue DESC;
```

---

### 3.2 Top 10 sellers por revenue

**Propósito de negocio:** Identificar los vendedores de mayor valor para programas de retención y account management prioritario.

```sql
WITH seller_revenue AS (
    SELECT
        seller_id,
        ROUND(SUM(price), 2) AS revenue
    FROM be_order_items
    GROUP BY seller_id
)
SELECT *
FROM seller_revenue
ORDER BY revenue DESC
LIMIT 10;
```

---

### 3.3 Análisis de Pareto de sellers

**Propósito de negocio:** Medir la concentración del revenue entre vendedores — cuántos sellers generan el 80% del negocio.

```sql
WITH seller_revenue AS (
    SELECT
        seller_id,
        SUM(price) AS revenue
    FROM be_order_items
    GROUP BY seller_id
),
pareto AS (
    SELECT
        seller_id,
        revenue,
        ROW_NUMBER() OVER (ORDER BY revenue DESC)   AS seller_rank,
        COUNT(*) OVER ()                            AS total_sellers,
        SUM(revenue) OVER (ORDER BY revenue DESC)   AS cumulative_revenue,
        SUM(revenue) OVER ()                        AS total_revenue
    FROM seller_revenue
)
SELECT
    seller_rank,
    seller_id,
    revenue,
    ROUND(cumulative_revenue / total_revenue * 100, 2) AS cumulative_pct
FROM pareto
ORDER BY seller_rank;
```

---

### 3.4 Revenue por estado del seller

**Propósito de negocio:** Analizar la distribución geográfica de los vendedores y su productividad promedio por región.

```sql
SELECT
    s.seller_state,
    COUNT(DISTINCT s.seller_id)                         AS sellers,
    ROUND(SUM(oi.price), 2)                             AS revenue,
    ROUND(SUM(oi.price) / COUNT(DISTINCT s.seller_id), 2) AS revenue_per_seller
FROM be_order_items oi
JOIN be_sellers s ON oi.seller_id = s.seller_id
GROUP BY s.seller_state
ORDER BY revenue DESC;
```

---

### 3.5 Concentración geográfica de sellers

**Propósito de negocio:** Medir qué estados concentran la mayor proporción de vendedores activos del marketplace.

```sql
SELECT
    seller_state,
    COUNT(DISTINCT seller_id) AS sellers,
    ROUND(
        COUNT(DISTINCT seller_id) * 100 /
        SUM(COUNT(DISTINCT seller_id)) OVER(), 2
    ) AS pct_sellers
FROM be_sellers
GROUP BY seller_state
ORDER BY sellers DESC;
```

---

### 3.6 Antigüedad comercial del seller

**Propósito de negocio:** Medir cuánto tiempo lleva activo cada vendedor en el marketplace como indicador de estabilidad y compromiso.

```sql
SELECT
    oi.seller_id,
    MIN(o.order_purchase_timestamp) AS first_sale,
    MAX(o.order_purchase_timestamp) AS last_sale,
    DATEDIFF(
        MAX(o.order_purchase_timestamp),
        MIN(o.order_purchase_timestamp)
    ) AS active_days
FROM be_orders o
JOIN be_order_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY oi.seller_id;
```

---

### 3.7 Riesgo de abandono del seller

**Propósito de negocio:** Detectar vendedores con inactividad prolongada que podrían estar abandonando la plataforma.

```sql
WITH seller_activity AS (
    SELECT
        oi.seller_id,
        MAX(o.order_purchase_timestamp) AS last_order
    FROM be_orders o
    JOIN be_order_items oi ON o.order_id = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY oi.seller_id
)
SELECT
    seller_id,
    last_order,
    DATEDIFF(
        (SELECT MAX(order_purchase_timestamp) FROM be_orders),
        last_order
    ) AS days_since_last_sale
FROM seller_activity
ORDER BY days_since_last_sale DESC;
```

---

### 3.8 Segmentación de sellers

**Propósito de negocio:** Clasificar vendedores en segmentos estratégicos para diseñar estrategias diferenciadas de retención y desarrollo.

```sql
WITH seller_metrics AS (
    SELECT
        oi.seller_id,
        COUNT(DISTINCT o.order_id)  AS orders_qty,
        ROUND(SUM(oi.price), 0)     AS revenue,
        DATEDIFF(
            (SELECT MAX(order_purchase_timestamp) FROM be_orders),
            MAX(o.order_purchase_timestamp)
        ) AS recency
    FROM be_orders o
    JOIN be_order_items oi ON o.order_id = oi.order_id
    WHERE o.order_status = 'delivered'
    GROUP BY oi.seller_id
)
SELECT
    seller_id,
    orders_qty,
    revenue,
    recency,
    CASE
        WHEN revenue >= 100000 THEN 'Top Seller'
        WHEN revenue >= 50000  THEN 'Growth Seller'
        WHEN revenue >= 10000  THEN 'Mid Seller'
        ELSE 'Long Tail'
    END AS seller_segment
FROM seller_metrics;
```

---

## Módulo 4 — Logistics & Fulfillment Analysis

Objetivo: Medir el desempeño logístico del marketplace, identificar regiones con mayor riesgo de retraso y cuantificar el impacto económico del fulfillment.

---

### 4.1 Logistics Mart — tabla base del módulo

**Propósito de negocio:** Construir una vista consolidada con todas las métricas logísticas por orden, usada como base para el resto de queries del módulo.

```sql
SELECT
    o.order_id,
    c.customer_unique_id,
    c.customer_state,
    o.order_purchase_timestamp,
    o.order_delivered_customer_date,
    o.order_estimated_delivery_date,
    DATEDIFF(
        o.order_delivered_customer_date,
        o.order_purchase_timestamp
    ) AS delivery_days,
    DATEDIFF(
        o.order_delivered_customer_date,
        o.order_estimated_delivery_date
    ) AS delay_days,
    CASE
        WHEN o.order_delivered_customer_date <= o.order_estimated_delivery_date
        THEN 'On Time'
        ELSE 'Delayed'
    END AS delivery_status,
    ROUND(SUM(oi.price), 2)         AS revenue,
    ROUND(SUM(oi.freight_value), 2) AS freight_cost
FROM be_orders o
JOIN be_customers c    ON o.customer_id = c.customer_id
JOIN be_order_items oi ON o.order_id    = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY
    o.order_id, c.customer_unique_id, c.customer_state,
    o.order_purchase_timestamp, o.order_delivered_customer_date,
    o.order_estimated_delivery_date;
```

---

### 4.2 KPIs logísticos generales

**Propósito de negocio:** Obtener los indicadores clave de la operación logística a nivel global para evaluar el desempeño general del fulfillment.

```sql
WITH logistics_mart AS ( -- [ver query 4.1] )
SELECT
    COUNT(*)                        AS total_orders,
    ROUND(AVG(delivery_days), 2)    AS avg_delivery_days,
    ROUND(AVG(delay_days), 2)       AS avg_delay_days,
    ROUND(AVG(freight_cost), 2)     AS avg_freight_cost
FROM logistics_mart;
```

---

### 4.3 Cumplimiento de entregas

**Propósito de negocio:** Medir la tasa de órdenes entregadas dentro del plazo comprometido — KPI central de la operación logística.

```sql
WITH logistics_mart AS ( -- [ver query 4.1] )
SELECT
    delivery_status,
    COUNT(*) AS orders_qty,
    ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER(), 2) AS pct_orders
FROM logistics_mart
GROUP BY delivery_status;
```

---

### 4.4 Estados con más retrasos

**Propósito de negocio:** Identificar las regiones del país con peor desempeño en tiempos de entrega para priorizar acciones de mejora logística.

```sql
WITH logistics_mart AS ( -- [ver query 4.1] )
SELECT
    customer_state,
    COUNT(*)                        AS orders_qty,
    ROUND(AVG(delivery_days), 2)    AS avg_delivery_days,
    ROUND(AVG(delay_days), 2)       AS avg_delay_days
FROM logistics_mart
GROUP BY customer_state
HAVING COUNT(*) >= 100
ORDER BY avg_delay_days DESC;
```

---

### 4.5 Impacto económico de los retrasos

**Propósito de negocio:** Cuantificar el revenue asociado a órdenes retrasadas para dimensionar el riesgo económico y reputacional de los incumplimientos.

```sql
WITH logistics_mart AS ( -- [ver query 4.1] )
SELECT
    delivery_status,
    COUNT(*)                     AS orders_qty,
    ROUND(SUM(revenue), 2)       AS revenue,
    ROUND(AVG(revenue), 2)       AS avg_order_value
FROM logistics_mart
GROUP BY delivery_status;
```

---

### 4.6 Categorías más afectadas por retrasos

**Propósito de negocio:** Detectar qué categorías de productos presentan mayor retraso promedio para identificar problemas específicos de fulfillment por tipo de producto.

```sql
SELECT
    p.product_category_name AS category,
    COUNT(DISTINCT o.order_id) AS orders_qty,
    ROUND(AVG(DATEDIFF(
        o.order_delivered_customer_date,
        o.order_estimated_delivery_date
    )), 2) AS avg_delay_days
FROM be_orders o
JOIN be_order_items oi ON o.order_id    = oi.order_id
JOIN be_products p     ON oi.product_id = p.product_id
WHERE o.order_status = 'delivered'
GROUP BY category
HAVING COUNT(DISTINCT o.order_id) >= 100
ORDER BY avg_delay_days DESC;
```

---

### 4.7 Costo logístico como % del revenue

**Propósito de negocio:** Medir cuánto del revenue total se destina a costos de flete — palanca clave para evaluar la rentabilidad del negocio.

```sql
SELECT
    ROUND(SUM(price), 2)         AS revenue,
    ROUND(SUM(freight_value), 2) AS freight_cost,
    ROUND(
        SUM(freight_value) * 100 / SUM(price), 2
    ) AS freight_pct_revenue
FROM be_order_items;
```

---

### 4.8 Clientes afectados por retrasos

**Propósito de negocio:** Dimensionar cuántos clientes únicos experimentaron al menos una entrega fuera de plazo.

```sql
SELECT
    COUNT(DISTINCT customer_unique_id) AS affected_customers
FROM (
    SELECT c.customer_unique_id
    FROM be_orders o
    JOIN be_customers c ON o.customer_id = c.customer_id
    WHERE o.order_status = 'delivered'
      AND o.order_delivered_customer_date > o.order_estimated_delivery_date
) t;
```

---

## Módulo 5 — Customer Experience & Satisfaction Analysis

Objetivo: Analizar los patrones de satisfacción del cliente, identificar los factores que determinan una experiencia positiva o negativa y detectar oportunidades de mejora.

> **Nota metodológica:** Este módulo trabaja con 279 reseñas — 0.5% del total de órdenes. Los resultados son indicativos y deben interpretarse con cautela estadística.

---

### 5.1 Distribución de ratings

**Propósito de negocio:** Entender la distribución de puntuaciones para evaluar la percepción general del marketplace.

```sql
SELECT
    review_score,
    COUNT(*) AS reviews,
    ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER(), 2) AS pct_reviews
FROM be_order_reviews
GROUP BY review_score
ORDER BY review_score;
```

---

### 5.2 Rating promedio general

**Propósito de negocio:** Obtener el NPS simplificado del marketplace como KPI ejecutivo de satisfacción.

```sql
SELECT
    ROUND(AVG(review_score), 2) AS avg_review_score,
    COUNT(*)                    AS total_reviews
FROM be_order_reviews;
```

---

### 5.3 Impacto de retrasos en la satisfacción

**Propósito de negocio:** Medir cuánto impacta el cumplimiento del plazo de entrega en la valoración del cliente — validar la hipótesis logística.

```sql
SELECT
    CASE
        WHEN o.order_delivered_customer_date <= o.order_estimated_delivery_date
        THEN 'On Time'
        ELSE 'Delayed'
    END AS delivery_status,
    COUNT(*)                        AS reviews,
    ROUND(AVG(r.review_score), 2)   AS avg_review_score
FROM be_orders o
JOIN be_order_reviews r ON o.order_id = r.order_id
WHERE o.order_status = 'delivered'
GROUP BY delivery_status;
```

---

### 5.4 Días de retraso vs rating promedio

**Propósito de negocio:** Cuantificar la relación entre el nivel de retraso y la calificación promedio recibida.

```sql
SELECT
    ROUND(AVG(DATEDIFF(
        o.order_delivered_customer_date,
        o.order_estimated_delivery_date
    )), 2) AS avg_delay_days,
    ROUND(AVG(r.review_score), 2) AS avg_review_score,
    COUNT(*) AS reviews
FROM be_orders o
JOIN be_order_reviews r ON o.order_id = r.order_id
WHERE o.order_status = 'delivered';
```

---

### 5.5 Categorías mejor valoradas

**Propósito de negocio:** Identificar qué categorías generan mayor satisfacción para replicar sus buenas prácticas en categorías con peor desempeño.

```sql
SELECT
    p.product_category_name AS category,
    COUNT(*)                        AS reviews,
    ROUND(AVG(r.review_score), 2)   AS avg_review_score
FROM be_order_reviews r
JOIN be_orders o     ON r.order_id   = o.order_id
JOIN be_order_items oi ON o.order_id = oi.order_id
JOIN be_products p   ON oi.product_id = p.product_id
GROUP BY category
HAVING COUNT(*) >= 10
ORDER BY avg_review_score DESC;
```

---

### 5.6 Ticket promedio por rating

**Propósito de negocio:** Analizar si existe relación entre el precio del producto y la satisfacción del cliente.

```sql
SELECT
    r.review_score,
    COUNT(*)                    AS reviews,
    ROUND(AVG(oi.price), 2)     AS avg_item_price
FROM be_order_reviews r
JOIN be_order_items oi ON r.order_id = oi.order_id
GROUP BY r.review_score
ORDER BY r.review_score;
```

---

### 5.7 Estados con mejor experiencia

**Propósito de negocio:** Detectar diferencias regionales en satisfacción del cliente para correlacionar con desempeño logístico por estado.

```sql
SELECT
    c.customer_state,
    COUNT(*)                        AS reviews,
    ROUND(AVG(r.review_score), 2)   AS avg_review_score
FROM be_order_reviews r
JOIN be_orders o   ON r.order_id   = o.order_id
JOIN be_customers c ON o.customer_id = c.customer_id
GROUP BY c.customer_state
HAVING COUNT(*) >= 10
ORDER BY avg_review_score DESC;
```

---

### 5.8 Perfil de clientes que dejan reseñas

**Propósito de negocio:** Entender si los clientes que reseñan lo hacen una sola vez o de forma recurrente, para evaluar la representatividad del sistema de reviews.

```sql
SELECT
    CASE
        WHEN review_count = 1 THEN 'One Review'
        ELSE 'Multiple Reviews'
    END AS reviewer_type,
    COUNT(*) AS customers
FROM (
    SELECT
        c.customer_unique_id,
        COUNT(*) AS review_count
    FROM be_order_reviews r
    JOIN be_orders o    ON r.order_id   = o.order_id
    JOIN be_customers c ON o.customer_id = c.customer_id
    GROUP BY c.customer_unique_id
) customer_reviews
GROUP BY reviewer_type;
```

---

*Documentación generada para el repositorio [sql-ecommerce-olist-analysis](https://github.com/fabianquispec/sql-ecommerce-olist-analysis)*  
*Autor: **Fabian Quispe Castillo** | [github.com/fabianquispec](https://github.com/fabianquispec)*
