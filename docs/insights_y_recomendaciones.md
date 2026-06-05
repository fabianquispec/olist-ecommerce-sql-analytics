# Insights y Recomendaciones Estratégicas — Olist E-Commerce

Hallazgos por módulo con impacto de negocio y propuestas de acción.

---

## Módulo 1 — Commercial Performance Analysis

### Executive Summary
Entre septiembre de 2016 y agosto de 2018, Olist experimentó una fuerte expansión comercial impulsada por el crecimiento sostenido de clientes y órdenes. El revenue mensual pasó de niveles inferiores a R$100K en los primeros meses a más de R$560K durante 2018. El crecimiento estuvo respaldado por categorías de consumo masivo, una fuerte concentración geográfica en São Paulo y patrones de compra relativamente estables durante los días laborables.

---

### Insight 1 — El marketplace atravesó una fase de crecimiento acelerado
**Query:** `1.1 Evolución mensual de ventas`

| Período | Revenue |
|---------|---------|
| Ene-2017 | R$ 65K |
| Dic-2017 | R$ 420K |
| May-2018 | R$ 566K |

El revenue mensual se multiplicó más de ocho veces durante el período analizado.

**Business Impact**
- La plataforma logró escalar rápidamente tanto en volumen de órdenes como en generación de ingresos
- El crecimiento fue consistente durante casi todo el período analizado
- El negocio muestra señales de haber alcanzado una etapa de consolidación durante 2018

**Strategic Insight**
El crecimiento del revenue estuvo acompañado por un crecimiento prácticamente proporcional de clientes únicos, indicando que la expansión fue impulsada principalmente por adquisición de nuevos compradores.

---

### Insight 2 — La adquisición de clientes fue el principal motor de crecimiento
**Query:** `1.1 Evolución mensual de ventas`

Durante prácticamente todos los meses: `Total Orders ≈ Unique Customers`, lo que indica que la mayoría de compradores realizó una única compra durante el período.

**Business Impact**
- El crecimiento dependió principalmente de la captación de nuevos clientes
- La recurrencia todavía representa una oportunidad de desarrollo
- Existe potencial para incrementar el Customer Lifetime Value mediante estrategias de fidelización

**Recomendación**
Implementar programas de retención y recompra para reducir la dependencia de adquisición constante.

---

### Insight 3 — La demanda presenta una fuerte estacionalidad en Q4
**Query:** `1.1 Evolución mensual de ventas`

| Mes | Revenue |
|-----|---------|
| Nov-2017 | R$ 576K |
| Dic-2017 | R$ 420K |
| Mar-May 2018 | > R$ 549K |

Noviembre de 2017 representa el pico histórico del dataset.

**Business Impact**
- El negocio se beneficia claramente de eventos promocionales y temporadas de alta demanda
- La capacidad logística y comercial debe prepararse para absorber estos picos

**Strategic Insight**
El comportamiento observado es consistente con eventos como Black Friday y campañas navideñas, típicas del comercio electrónico.

---

### Insight 4 — Cinco categorías generan cerca del 40% del revenue total
**Query:** `1.2 Top 10 categorías por revenue`

| Categoría | % Revenue |
|-----------|-----------|
| Beleza & Saúde | 9.46% |
| Relógios & Presentes | 8.95% |
| Cama, Mesa & Banho | 7.73% |
| Esporte & Lazer | 7.31% |
| Informática & Accesorios | 6.86% |

**Business Impact**
- El marketplace presenta una cartera relativamente diversificada
- No existe dependencia extrema de una sola categoría
- La demanda está distribuida entre múltiples verticales de consumo

**Strategic Insight**
Las categorías líderes combinan alta frecuencia de compra con tamaños de mercado amplios.

---

### Insight 5 — Las categorías premium generan tickets significativamente superiores
**Query:** `1.2 Top 10 categorías por revenue`

| Categoría | Ticket Promedio |
|-----------|----------------|
| Relógios & Presentes | R$ 197.70 |
| Cool Stuff | R$ 166.58 |
| Automotivo | R$ 144.75 |
| Beleza & Saúde | R$ 131.40 |

**Recomendación**
Diseñar estrategias diferenciadas para categorías orientadas a volumen y categorías orientadas a margen.

---

### Insight 6 — São Paulo domina la demanda nacional
**Query:** `1.3 Performance por estado de Brasil`

| Estado | Revenue |
|--------|---------|
| SP | R$ 2.92M |
| RJ | R$ 1.03M |
| MG | R$ 871K |

São Paulo genera aproximadamente 2.8x el revenue de Río de Janeiro y 3.3x el de Minas Gerais.

**Strategic Insight**
São Paulo aparece como el principal centro económico del marketplace tanto desde la perspectiva de clientes como de vendedores.

---

### Insight 7 — El comportamiento de compra es más fuerte durante los días laborables
**Query:** `1.4 Estacionalidad por día de la semana`

| Día | % GMV |
|-----|-------|
| Lunes | 16.27% |
| Martes | 15.97% |
| Miércoles | 15.43% |
| Jueves | 15.09% |

Los días laborables concentran más del 60% del GMV total, asociando el consumo más a rutinas diarias que a compras recreativas de fin de semana.

---

## Módulo 2 — Customer Intelligence & RFM Segmentation

### Executive Summary
La segmentación RFM revela una base de clientes relativamente equilibrada, donde los segmentos Champions y Loyal Customers generan una proporción significativa del revenue, mientras que los clientes At Risk representan la principal amenaza para la retención de ingresos futuros.

---

### Insight 1 — Los Champions representan el segmento más valioso
**Query:** `2.8 Revenue por segmento RFM`

| Segmento | Customers | Revenue |
|----------|-----------|---------|
| Champions | 4,746 | R$ 1.29M |

A pesar de representar menos del 9% de los clientes, generan casi el 17% del revenue total.

**Recomendación**
Implementar programas de fidelización premium y beneficios exclusivos para proteger y crecer este segmento.

---

### Insight 2 — Los clientes At Risk representan una amenaza significativa para el revenue
**Query:** `2.8 Revenue por segmento RFM`

| Segmento | Customers | Revenue |
|----------|-----------|---------|
| At Risk | 11,509 | R$ 1.57M |

Este segmento genera más revenue que Champions y Loyal Customers individualmente.

**Strategic Insight**
La recuperación de clientes existentes suele ser más rentable que adquirir nuevos clientes. Acciones de reactivación dirigidas a este segmento tienen potencial de alto retorno.

---

### Insight 3 — Los clientes leales presentan el mayor potencial de crecimiento
**Query:** `2.8 Revenue por segmento RFM`

| Segmento | Customers | Revenue |
|----------|-----------|---------|
| Loyal Customers | 4,322 | R$ 1.11M |

Estos clientes ya muestran comportamientos recurrentes y podrían evolucionar hacia Champions mediante estrategias adecuadas de engagement.

---

### Insight 4 — Los nuevos clientes generan una contribución relevante al negocio
**Query:** `2.1 Tasa de recompra`

| Segmento | Customers | Revenue |
|----------|-----------|---------|
| New Customers | 9,732 | R$ 1.33M |

El verdadero desafío consiste en transformar estos compradores únicos en clientes recurrentes.

---

### Insight 5 — Existe una fuerte concentración de valor en segmentos de alta calidad
**Query:** `2.8 Revenue por segmento RFM`

| Segmento | Avg Customer Value |
|----------|--------------------|
| Champions | R$ 271.99 |
| Loyal Customers | R$ 256.46 |
| New Customers | R$ 136.84 |

La diferencia de valor promedio entre Champions y New Customers es prácticamente el doble.

---

## Módulo 3 — Seller Analysis

### Executive Summary
El análisis de vendedores revela una distribución altamente concentrada del revenue, con un pequeño grupo de sellers top generando la mayor parte del volumen comercial. São Paulo lidera tanto en cantidad de vendedores como en productividad. Existe además un segmento relevante de sellers con señales de inactividad que representa un riesgo para la oferta del marketplace.

---

### Insight 1 — El revenue de sellers sigue una distribución Pareto clara
**Query:** `3.3 Análisis de Pareto de sellers`

El top 10% de sellers concentra más del 80% del revenue total del marketplace.

**Business Impact**
- La dependencia de un pequeño grupo de sellers representa un riesgo de concentración
- La pérdida de un seller top tiene impacto desproporcionado sobre el negocio

**Recomendación**
Implementar programas de retención y account management dedicado para los sellers top.

---

### Insight 2 — São Paulo lidera en concentración de sellers y productividad
**Query:** `3.4 Revenue por estado del seller`

São Paulo concentra la mayor cantidad de sellers activos y el mayor revenue por vendedor, consolidándose como el hub comercial principal del marketplace.

**Strategic Insight**
La correlación entre concentración de sellers, tiempos de entrega y satisfacción del cliente sugiere que expandir el ecosistema de sellers en otras regiones mejoraría simultáneamente la experiencia del cliente y los indicadores logísticos.

---

### Insight 3 — Existe un segmento relevante de sellers en riesgo de abandono
**Query:** `3.7 Riesgo de abandono del seller`

Un grupo de sellers presenta inactividad prolongada desde su última venta, sugiriendo riesgo de abandono de la plataforma.

**Recomendación**
Implementar alertas de reactivación para sellers con más de 60 días sin ventas, con incentivos comerciales para reactivar su actividad.

---

### Insight 4 — La segmentación de sellers permite estrategias diferenciadas
**Query:** `3.8 Segmentación de sellers`

| Segmento | Criterio | Estrategia |
|----------|----------|------------|
| Top Seller | Revenue ≥ R$100K | Retención y account management |
| Growth Seller | Revenue ≥ R$50K | Aceleración y expansión de catálogo |
| Mid Seller | Revenue ≥ R$10K | Desarrollo y soporte comercial |
| Long Tail | Revenue < R$10K | Activación o evaluación de continuidad |

---

## Módulo 4 — Logistics & Fulfillment Analysis

### Executive Summary
La operación logística de Olist muestra un desempeño sólido a nivel general, con un tiempo promedio de entrega competitivo y una tasa de cumplimiento cercana al 92%. Sin embargo, el análisis revela diferencias regionales significativas y un costo logístico que representa más del 16% del revenue total, posicionándose como una de las principales palancas de optimización del negocio.

---

### Insight 1 — La operación logística presenta un desempeño general sólido
**Query:** `4.2 KPIs logísticos generales`

| KPI | Valor |
|-----|-------|
| Avg Delivery Days | 12.5 días |
| Avg Delay Days | -10.8 días (promedio adelantado) |
| Avg Freight Cost | R$ 21.16 |
| Tasa de cumplimiento | ~92% |

**Business Impact**
El marketplace cumple con los plazos comprometidos en la gran mayoría de los casos, lo que constituye una base sólida para la satisfacción del cliente.

---

### Insight 2 — Los estados del norte y noreste concentran los mayores retrasos
**Query:** `4.4 Estados con más retrasos`

Los estados más alejados de los centros logísticos del sudeste brasileño presentan consistentemente mayores tiempos de entrega y mayores retrasos respecto al plazo estimado.

**Recomendación**
Priorizar la expansión de infraestructura logística o partnerships con operadores regionales en los estados con mayor índice de retraso.

---

### Insight 3 — Los retrasos afectan proporcionalmente más a pedidos de alto valor
**Query:** `4.5 Impacto económico de los retrasos`

**Business Impact**
- Los retrasos afectan proporcionalmente más a pedidos de alto valor
- El impacto potencial sobre satisfacción y percepción de marca es mayor de lo que sugiere el volumen de órdenes afectadas
- Cada retraso implica un mayor riesgo económico

**Recomendación**
Priorizar monitoreo y seguimiento para pedidos de alto valor, especialmente durante etapas críticas del proceso de entrega.

---

### Insight 4 — El costo logístico representa una porción relevante del negocio
**Query:** `4.7 Costo logístico como % del revenue`

| KPI | Valor |
|-----|-------|
| Revenue total | R$ 13.53M |
| Freight Cost | R$ 2.24M |
| Freight % Revenue | 16.57% |

El costo logístico equivale a aproximadamente 1 de cada 6 reales generados en ventas.

**Recomendación**
Monitorear el **Freight as % Revenue** como KPI estratégico para evaluar eficiencia operativa y márgenes comerciales.

---

### Insight 5 — Más de 4,400 clientes experimentaron entregas tardías
**Query:** `4.8 Clientes afectados por retrasos`

Aproximadamente uno de cada doce clientes recibió al menos una orden fuera del plazo comprometido.

**Recomendación**
Implementar alertas tempranas y estrategias de comunicación proactiva para pedidos con riesgo de retraso.

---

### Insight 6 — Los hubs comerciales también son los más eficientes logísticamente
**Query:** `4.4 Estados con más retrasos`

São Paulo, Minas Gerais y Paraná destacan simultáneamente por altos niveles de revenue, alta concentración de sellers y menores tiempos de entrega — confirmando una correlación entre densidad comercial y eficiencia operativa.

---

## Módulo 5 — Customer Experience & Satisfaction Analysis

### Executive Summary
A pesar de la limitada cobertura de reseñas, los resultados muestran una relación clara entre desempeño logístico y satisfacción del cliente. Los pedidos entregados a tiempo reciben valoraciones significativamente superiores, mientras que las entregas tardías generan una caída sustancial en la experiencia percibida.

---

### Insight 1 — La satisfacción general es alta
**Query:** `5.2 Rating promedio general`

| KPI | Valor |
|-----|-------|
| Average Review Score | 4.01 / 5 |
| Total Reviews | 279 |

> **Nota metodológica:** La cobertura de reseñas es del 0.5% del total de órdenes. Los hallazgos son indicativos y deben interpretarse con cautela.

---

### Insight 2 — Casi el 60% de las reseñas otorgan la máxima calificación
**Query:** `5.1 Distribución de ratings`

| Review Score | % Reviews |
|-------------|-----------|
| 5 estrellas | 58.8% |
| 4 estrellas | 14.0% |
| 3 estrellas | 10.8% |
| 2 estrellas | 2.9% |
| 1 estrella | 13.6% |

El 13.6% de reseñas con una estrella representa una señal de alerta relevante — la experiencia está polarizada.

**Recomendación**
Investigar en profundidad las causas detrás de las reseñas de una estrella para identificar oportunidades de mejora operativa.

---

### Insight 3 — La logística tiene un impacto directo sobre la satisfacción
**Query:** `5.3 Impacto de retrasos en la satisfacción`

| Delivery Status | Avg Review Score |
|----------------|-----------------|
| On Time | 4.18 |
| Delayed | 2.67 |

Las entregas tardías generan una caída de aproximadamente **1.5 puntos** en la valoración promedio — el hallazgo más importante del módulo.

**Strategic Insight**
Este resultado valida directamente los hallazgos del Módulo 4: mejor logística → mayor satisfacción → mejor experiencia de cliente.

---

### Insight 4 — Los clientes que compran productos más caros tienden a valorar mejor la experiencia
**Query:** `5.6 Ticket promedio por rating`

| Review Score | Avg Item Price |
|-------------|---------------|
| 1 estrella | R$ 133.35 |
| 3 estrellas | R$ 104.22 |
| 4 estrellas | R$ 135.70 |
| 5 estrellas | R$ 155.75 |

**Recomendación**
Analizar prácticas exitosas de las categorías con mayor valoración para replicarlas en segmentos con desempeño inferior.

---

### Insight 5 — Existen diferencias importantes entre categorías
**Query:** `5.5 Categorías mejor valoradas`

| Category | Avg Review Score |
|----------|-----------------|
| Ferramentas Jardim | 4.67 |
| Informática Accesorios | 4.38 |
| Relógios Presentes | 4.07 |
| Beleza Saúde | 3.91 |
| Cama Mesa Banho | 3.71 |
| Utilidades Domésticas | 3.41 |

Las categorías relacionadas con herramientas y tecnología muestran los niveles más altos de satisfacción, mientras que las categorías del hogar presentan evaluaciones más bajas.

---

### Insight 6 — São Paulo lidera en satisfacción del cliente
**Query:** `5.7 Estados con mejor experiencia`

| Estado | Avg Review Score |
|--------|-----------------|
| SP | 4.39 |
| MG | 4.20 |
| BA | 3.64 |
| RJ | 3.61 |

Resultado consistente con la mayor concentración de sellers, menores tiempos de entrega y mejor desempeño logístico de São Paulo.

---

### Conclusión Ejecutiva Global

El análisis de los 5 módulos revela una narrativa coherente y conectada:

1. **El crecimiento fue real pero dependiente de adquisición** — la retención es la próxima palanca
2. **Los clientes At Risk concentran el mayor riesgo de revenue** — acción inmediata requerida
3. **El ecosistema de sellers está concentrado** — diversificación geográfica es una oportunidad
4. **La logística es el principal driver de experiencia** — invertir en fulfillment tiene retorno directo en satisfacción
5. **São Paulo es el hub que sostiene el marketplace** — replicar su modelo en otras regiones es la estrategia de expansión más evidente

---

*Análisis desarrollado por **Fabian Quispe** | [github.com/fabianquispec](https://github.com/fabianquispec)*
