# Diccionario de Datos — Brazilian E-Commerce (Olist)

Descripción del modelo relacional utilizado en el análisis. Todas las tablas tienen el prefijo `be_` en la base de datos MySQL.

> **Fuente:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle  
> **Motor:** MySQL 8.0  
> **Esquema:** Estrella centrada en `be_orders`

---

## Modelo relacional

```
be_customers ──────────────────────────────────────────────┐
                                                            │
be_order_items ──── be_products ──── be_category_name ──   │
        │                                               be_orders ──── be_order_payments
be_order_reviews ───────────────────────────────────────   │
                                                            │
be_sellers ─────────────────────────────────────────────   │
                                                            │
be_geolocation (referencia geográfica independiente)        │
```

---

## Tablas

### be_orders
Tabla central del modelo. Registra cada pedido con su estado y fechas del ciclo de vida.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `order_id` | VARCHAR | Identificador único del pedido (PK) |
| `customer_id` | VARCHAR | Llave foránea a `be_customers` |
| `order_status` | VARCHAR | Estado del pedido: delivered, shipped, canceled, unavailable, processing, invoiced, created, approved |
| `order_purchase_timestamp` | DATETIME | Fecha y hora de la compra |
| `order_approved_at` | DATETIME | Fecha de aprobación del pago |
| `order_delivered_carrier_date` | DATETIME | Fecha de entrega al transportista |
| `order_delivered_customer_date` | DATETIME | Fecha de entrega al cliente |
| `order_estimated_delivery_date` | DATETIME | Fecha estimada de entrega comprometida |

**Volumen:** 57,443 filas | **97% con status `delivered`**

---

### be_order_items
Líneas de cada pedido. Un pedido puede tener múltiples items.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `order_id` | VARCHAR | Llave foránea a `be_orders` |
| `order_item_id` | INT | Número de secuencia del item dentro del pedido |
| `product_id` | VARCHAR | Llave foránea a `be_products` |
| `seller_id` | VARCHAR | Llave foránea a `be_sellers` |
| `shipping_limit_date` | DATETIME | Fecha límite de envío por parte del seller |
| `price` | DECIMAL | Precio del item en reales brasileños (R$) |
| `freight_value` | DECIMAL | Costo de flete del item en R$ |

**Volumen:** 112,131 filas | ~2 items promedio por orden

---

### be_customers
Base de compradores. Un cliente único puede tener múltiples registros si realizó más de una compra.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `customer_id` | VARCHAR | Identificador de la instancia de compra (PK) |
| `customer_unique_id` | VARCHAR | Identificador único del comprador real — usar este para análisis de recurrencia |
| `customer_zip_code_prefix` | VARCHAR | Prefijo del código postal |
| `customer_city` | VARCHAR | Ciudad del cliente |
| `customer_state` | VARCHAR | Estado (UF) del cliente |

**Volumen:** 99,441 filas | **Nota:** usar `customer_unique_id` para análisis de comportamiento, no `customer_id`

---

### be_products
Catálogo de productos disponibles en el marketplace.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `product_id` | VARCHAR | Identificador único del producto (PK) |
| `product_category_name` | VARCHAR | Categoría del producto en portugués |
| `product_name_lenght` | INT | Longitud del nombre del producto |
| `product_description_lenght` | INT | Longitud de la descripción |
| `product_photos_qty` | INT | Cantidad de fotos del producto |
| `product_weight_g` | INT | Peso en gramos |
| `product_length_cm` | INT | Largo en centímetros |
| `product_height_cm` | INT | Alto en centímetros |
| `product_width_cm` | INT | Ancho en centímetros |

**Volumen:** 32,340 filas

---

### be_sellers
Vendedores registrados en el marketplace.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `seller_id` | VARCHAR | Identificador único del seller (PK) |
| `seller_zip_code_prefix` | VARCHAR | Prefijo del código postal del seller |
| `seller_city` | VARCHAR | Ciudad del seller |
| `seller_state` | VARCHAR | Estado (UF) del seller |

**Volumen:** 3,095 filas

---

### be_order_payments
Métodos y montos de pago por pedido. Un pedido puede tener múltiples registros si usó más de un método.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `order_id` | VARCHAR | Llave foránea a `be_orders` |
| `payment_sequential` | INT | Secuencia del pago (para pagos múltiples) |
| `payment_type` | VARCHAR | Método de pago: credit_card, boleto, voucher, debit_card |
| `payment_installments` | INT | Número de cuotas |
| `payment_value` | DECIMAL | Monto del pago en R$ |

**Volumen:** 103,886 filas

---

### be_order_reviews
Reseñas y valoraciones de clientes sobre sus pedidos.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `review_id` | VARCHAR | Identificador único de la reseña (PK) |
| `order_id` | VARCHAR | Llave foránea a `be_orders` |
| `review_score` | INT | Puntuación del 1 al 5 |
| `review_comment_title` | VARCHAR | Título del comentario (opcional) |
| `review_comment_message` | VARCHAR | Texto del comentario (opcional) |
| `review_creation_date` | DATETIME | Fecha de creación de la solicitud de reseña |
| `review_answer_timestamp` | DATETIME | Fecha en que el cliente respondió |

**Volumen:** 279 filas | **Cobertura:** 0.5% del total de órdenes — limitación metodológica documentada

---

### be_product_category_name_translation
Tabla de traducción de categorías del portugués al inglés.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `string_field_0` | VARCHAR | Nombre de la categoría en portugués |
| `string_field_1` | VARCHAR | Nombre de la categoría en inglés |

**Volumen:** ~71 categorías

---

### be_geolocation
Coordenadas geográficas por código postal. Usada como referencia para análisis de proximidad.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `geolocation_zip_code_prefix` | VARCHAR | Prefijo del código postal |
| `geolocation_lat` | DECIMAL | Latitud |
| `geolocation_lng` | DECIMAL | Longitud |
| `geolocation_city` | VARCHAR | Ciudad |
| `geolocation_state` | VARCHAR | Estado (UF) |

---

## Notas metodológicas

**Sobre `customer_id` vs `customer_unique_id`**
La tabla `be_customers` registra una fila por instancia de compra, no por comprador único. Para análisis de recurrencia y segmentación RFM, siempre usar `customer_unique_id`.

**Sobre el bajo volumen de reseñas**
Solo 279 órdenes tienen reseña (0.5% del total). El Módulo 5 debe interpretarse como indicativo, no como representativo del universo completo de experiencias.

**Sobre el filtro de status**
La mayoría de análisis filtra `order_status = 'delivered'` (97% de las órdenes) para garantizar que solo se analizan transacciones completadas con datos de entrega disponibles.

---

*Documentación generada para el repositorio [sql-ecommerce-olist-analysis](https://github.com/fabianquispec/sql-ecommerce-olist-analysis)*
