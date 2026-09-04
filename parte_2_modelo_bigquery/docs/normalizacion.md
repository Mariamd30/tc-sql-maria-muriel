# Normalización del modelo — Justificación 3NF

## 1NF — Primera Forma Normal

Todos los atributos del modelo son atómicos: no hay campos que almacenen listas ni
valores múltiples en una sola celda. Por ejemplo, si un pedido tiene varios productos,
no se guarda como una lista dentro de una columna de `orders` — cada producto de un
pedido es una fila independiente en `order_items`. No existen grupos repetidos
(como `product_1`, `product_2`, `product_3` como columnas de `orders`).

## 2NF — Segunda Forma Normal

El modelo cumple 1NF y, además, no hay dependencias parciales. Esto es relevante sobre
todo en tablas con clave compuesta lógica, como `order_items`: aunque su clave primaria
es `order_item_id` (una clave sustituta simple), la combinación real que la identifica es
`(order_id, product_id)`. Todos los atributos no-clave de esa tabla (`quantity`,
`unit_price`, `discount`) dependen de la combinación completa `order_id + product_id`,
no de uno solo de los dos: `quantity` no tiene sentido sin saber ni el pedido ni el
producto concretos.

## 3NF — Tercera Forma Normal

El modelo cumple 2NF y no existen dependencias transitivas: ningún atributo no-clave
depende de otro atributo no-clave en lugar de depender de la clave primaria.

**Ejemplos de decisiones tomadas para respetar 3NF:**

- **`unit_price` vive en `order_items`, no se lee de `products.price`.** El precio de
  venta de un producto puede cambiar con el tiempo. Si `order_items` solo guardara
  `product_id` y calculara el precio leyendo `products.price` en el momento de la
  consulta, el importe de pedidos antiguos cambiaría cada vez que el precio actual del
  producto cambiara — falsificando el histórico. Guardar `unit_price` en el momento de
  la compra es lo que mantiene la integridad histórica de cada pedido.

- **`country` vive directamente en `customers`, no en una tabla `countries` separada.**
  Un país, en este modelo, no tiene ningún atributo adicional que dependa de él (no se
  guarda población, moneda, prefijo telefónico, etc.). Al no haber información extra
  que dependa de `country`, separarlo en su propia tabla no evita ninguna
  inconsistencia real — solo añadiría un JOIN innecesario. La normalización se aplica
  cuando resuelve un problema real de duplicación/inconsistencia, no de forma
  automática sobre cualquier campo de texto repetido.

- **Si `orders` guardara `customer_name` además de `customer_id`, se rompería la 3NF.**
  La clave primaria de `orders` es `order_id`. `customer_id` depende directamente de
  `order_id` (a qué cliente pertenece este pedido). Pero `customer_name` no depende de
  `order_id` — depende de `customer_id`, que a su vez es un atributo no-clave de la
  tabla. Esa cadena `order_id → customer_id → customer_name` es una dependencia
  transitiva. Si un cliente cambiara de nombre, habría que actualizarlo en todas sus
  filas de `orders`, con riesgo de dejar alguna desactualizada (anomalía de
  actualización). Por eso `customer_name` vive una única vez en `customers`, y `orders`
  solo referencia el `customer_id`.

## La relación N:M entre pedidos y productos

Un pedido puede incluir varios productos, y un mismo producto aparece en muchos
pedidos distintos: es una relación muchos-a-muchos. Como una clave foránea solo puede
apuntar a una fila del otro lado, esta relación no se puede modelar con un FK directo
entre `orders` y `products`. Se resuelve con la tabla intermedia `order_items`, que
convierte la relación N:M en dos relaciones 1:N: `orders (1) — (N) order_items (N) —
(1) products`. Cada fila de `order_items` representa un producto concreto dentro de un
pedido concreto, con su cantidad, precio y descuento propios.
