# Brazilian E-Commerce Marketplace Analysis (Olist) | SQL & Business Intelligence

![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat&logo=mysql&logoColor=white)
![Dataset](https://img.shields.io/badge/Dataset-Olist%20Kaggle-20BEFF?style=flat)
![Tablas](https://img.shields.io/badge/Tablas-9-success?style=flat)
![Órdenes](https://img.shields.io/badge/Órdenes-57%2C443-orange?style=flat)

> **Más que consultas SQL: un análisis de negocio completo sobre un marketplace real.**

---

## 📌 Contexto del proyecto

Olist es el mayor marketplace de e-commerce de Brasil, conectando vendedores independientes con los principales canales de venta del país. Este proyecto analiza datos reales de **57,443 órdenes** realizadas entre septiembre de 2016 y octubre de 2018, con el objetivo de extraer insights accionables sobre performance comercial, comportamiento de clientes, vendedores, logística y satisfacción.

El análisis está estructurado en **5 módulos independientes**, cada uno orientado a responder una pregunta de negocio crítica. Cada query va acompañada de su interpretación de negocio y recomendación estratégica.

---

## 🎯 Preguntas de negocio

| Módulo | Pregunta central |
|--------|-----------------|
| **1. Commercial Performance** | ¿Cómo evolucionó el negocio y qué categorías, regiones y períodos concentran el mayor valor? |
| **2. Customer Intelligence** | ¿Qué tan leales son los clientes y qué segmentos RFM concentran el mayor riesgo de pérdida de revenue? |
| **3. Seller Analysis** | ¿Cuáles son los vendedores más valiosos y cuáles están en riesgo de abandono? |
| **4. Logistics & Fulfillment** | ¿Cómo impacta el tiempo de entrega en la experiencia del cliente y qué regiones concentran los mayores problemas? |
| **5. Customer Experience** | ¿Qué factores determinan la satisfacción del cliente y qué diferencia una experiencia positiva de una negativa? |

---

## 📂 Estructura del repositorio

```
sql-ecommerce-olist-analysis/
│
├── README.md
├── sql/
│   └── queries_documentadas.md
├── docs/
│   ├── insights_y_recomendaciones.md
│   └── diccionario_datos.md
└── outputs/
    │   ├── assets/
    │   ├── evolucion-ventas.jpeg
    │   ├── top-10-categorias.jpeg
    │   ├── revenue-por-segmento.jpeg
    │   ├── tasa-recompra.jpeg
    │   ├── segmentacion-sellers.jpeg
    │   ├── kpis-logisticos.jpeg
    │   ├── impacto-economico-por-retrasos.jpeg
    │   └── retrasos-vs-satisfaccion.jpeg
    └── key_visualizations.md
```

---

## 🗂️ Modelo de datos

El dataset contiene 9 tablas relacionadas con esquema estrella centrado en `be_orders`:

```
be_customers ──────────────────────────────────────────┐
                                                       │
be_order_items ──── be_products ──── be_category ───   │
        │                                           be_orders ──── be_order_payments
be_order_reviews ───────────────────────────────────   │
                                                       │
be_sellers ─────────────────────────────────────────   │
                                                       │
be_geolocation                                         │
```

---

## 📊 Hallazgos principales

### Módulo 1 — Commercial Performance
- El revenue mensual creció **más de 8x** entre enero 2017 (R$65K) y mayo 2018 (R$566K)
- **5 categorías** concentran el 40% del revenue total: Beleza & Saúde, Relógios, Cama/Mesa/Banho, Esporte y Informática
- **São Paulo** genera 2.8x el revenue de Río de Janeiro — fuerte concentración geográfica
- Los **días laborables** concentran más del 60% del GMV semanal

### Módulo 2 — Customer Intelligence
- **97%** de los clientes realizó una sola compra — la recompra es la principal oportunidad de crecimiento
- Los **Champions** representan solo el 9% de clientes pero generan el 17% del revenue
- Los clientes **At Risk** generan R$1.57M en revenue — la mayor amenaza de pérdida de ingresos
- El top 20% de clientes concentra aproximadamente el 80% del revenue (Pareto)

### Módulo 3 — Seller Analysis
- El top 10% de sellers genera el 80%+ del revenue total
- **São Paulo** concentra el mayor número de sellers y el mayor revenue por vendedor
- Existe un segmento relevante de sellers con inactividad reciente — riesgo de abandono del marketplace

### Módulo 4 — Logistics & Fulfillment
- Tiempo promedio de entrega: **12.5 días**
- Tasa de cumplimiento en plazo: **~92%**
- Los estados del norte y noreste de Brasil presentan los mayores retrasos
- El costo logístico representa el **16.57% del revenue total** — palanca clave de rentabilidad

### Módulo 5 — Customer Experience
- Rating promedio: **4.01 / 5**
- Entregas a tiempo: **4.18 estrellas** vs entregas tardías: **2.67 estrellas** — diferencia de 1.5 puntos
- El 13.6% de reseñas otorga 1 estrella — señal de alerta reputacional
- São Paulo lidera en satisfacción, consistente con su mejor desempeño logístico

---

## ⚙️ Stack tecnológico

| Herramienta | Uso |
|-------------|-----|
| **MySQL 8.0** | Motor principal de análisis |
| **CTEs** | Estructuración de queries complejas en capas legibles |
| **Window Functions** | `NTILE`, `ROW_NUMBER`, `SUM OVER`, `RANK` para análisis avanzado |
| **CASE WHEN** | Segmentación dinámica de clientes, sellers y entregas |
| **DATE functions** | `DATEDIFF`, `DATE_FORMAT`, `DAYNAME` para análisis temporal |

---

### Conclusión Ejecutiva Global

El análisis de los 5 módulos revela una narrativa coherente y conectada:

1. **El crecimiento fue real pero dependiente de adquisición** — la retención es la próxima palanca
2. **Los clientes At Risk concentran el mayor riesgo de revenue** — acción inmediata requerida
3. **El ecosistema de sellers está concentrado** — diversificación geográfica es una oportunidad
4. **La logística es el principal driver de experiencia** — invertir en fulfillment tiene retorno directo en satisfacción
5. **São Paulo es el hub que sostiene el marketplace** — replicar su modelo en otras regiones es la estrategia de expansión más evidente

## 💡 Valor para el negocio

Este análisis permite a Olist y marketplaces similares:

- **Priorizar inversión en retención** sobre adquisición — dado que el 97% son compradores únicos
- **Proteger el revenue de clientes At Risk** antes de que se pierda
- **Concentrar esfuerzos logísticos** en estados con mayor retraso y mayor impacto en satisfacción
- **Optimizar el costo de flete** que representa 1 de cada 6 reales de revenue
- **Replicar el modelo de São Paulo** en otras regiones para mejorar eficiencia y satisfacción

---

## 📁 Documentación adicional

- 📄 [Queries](sql/queries_documentadas.md)
- 📄 [Insights y Recomendaciones Estratégicas](docs/insights_y_recomendaciones.md)
- 📄 [Diccionario de Datos](docs/diccionario_datos.md)


---

## 👤 Autor

**Fabian Quispe**
Analista de Business Intelligence | Lima, Perú

[![LinkedIn](https://img.shields.io/badge/LinkedIn-fabianquispec-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/fabianquispec)
[![GitHub](https://img.shields.io/badge/GitHub-fabianquispec-181717?style=flat&logo=github)](https://github.com/fabianquispec)

---

> *Dataset: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle*
