# Examen backend. - Normalización y Modelo E-R

## Parte 1: Normalización hasta Tercera Forma Normal (3FN)

### 1.1 Estado No Normalizado (0FN)
La tabla original contiene datos mezclados de Libros, Autores, Editoriales, Categorías, Clientes, Pedidos y Métodos de Pago en una sola estructura no atómica.

### 1.2 Primera Forma Normal (1FN)
* **Regla:** Se garantiza la atomicidad de los campos (no campos compuestos) y se eliminan listas repetidas.
* **Ajustes:** Se separan nombres y apellidos completos de autores y clientes en campos independientes (`nombre` y `apellido`).

**Atributos en 1FN:**
`ISBN`, `titulo`, `nombre_autor`, `apellido_autor`, `fecha_nacimiento_autor`, `editorial`, `categoria`, `precio`, `stock`, `nombre_cliente`, `apellido_cliente`, `correo_cliente`, `direccion_cliente`, `telefono_cliente`, `metodo_pago`, `monto_pago`.

### 1.3 Segunda Forma Normal (2FN)
* **Regla:** Eliminación de dependencias parciales respecto a la clave primaria.
* **Ajustes:** Se separan las entidades independientes ya que atributos como el título del libro no dependen del cliente o de la transacción.

### 1.4 Tercera Forma Normal (3FN) - Esquema Final de Tablas
* **Regla:** Eliminación de dependencias transitivas (atributos no clave que dependen de otros atributos no clave).

**Tablas resultantes en 3FN:**
1. **AUTOR:** `id_autor` (PK), `nombre`, `apellido`, `fecha_nacimiento`
2. **EDITORIAL:** `id_editorial` (PK), `nombre_editorial`
3. **CATEGORIA:** `id_categoria` (PK), `nombre_categoria`
4. **LIBRO:** `isbn` (PK), `titulo`, `precio`, `stock`, `id_editorial` (FK), `id_categoria` (FK)
5. **LIBRO_AUTOR:** `isbn` (PK, FK), `id_autor` (PK, FK)
6. **CLIENTE:** `id_cliente` (PK), `nombre`, `apellido`, `correo`, `direccion`, `telefono`
7. **METODO_PAGO:** `id_metodo_pago` (PK), `nombre_metodo`
8. **PEDIDO:** `id_pedido` (PK), `fecha_pedido`, `monto_total`, `id_cliente` (FK)
9. **DETALLE_PEDIDO:** `id_pedido` (PK, FK), `isbn` (PK, FK), `cantidad`, `precio_unitario`
10. **TRANSACCION:** `id_transaccion` (PK), `monto_pago`, `fecha_transaccion`, `id_pedido` (FK), `id_metodo_pago` (FK)

---

## Parte 2: Modelo Entidad-Relación (E-R)

### Cardinalidades y Reglas de Negocio
* **AUTOR - LIBRO:** Muchos a Muchos (N:M). Un autor escribe varios libros; un libro puede tener varios autores.
* **EDITORIAL - LIBRO:** Uno a Muchos (1:N). Una editorial publica muchos libros.
* **CATEGORIA - LIBRO:** Uno a Muchos (1:N). Una categoría agrupa varios libros.
* **CLIENTE - PEDIDO:** Uno a Muchos (1:N). Un cliente realiza varios pedidos.
* **PEDIDO - LIBRO:** Muchos a Muchos (N:M). Un pedido contiene varios libros; un libro está en varios pedidos.
* **PEDIDO - TRANSACCION:** Uno a Muchos (1:N). Un pedido genera registros de pago.
* **METODO_PAGO - TRANSACCION:** Uno a Muchos (1:N). Un método de pago procesa múltiples transacciones.

### Diagrama Estructural (UML / E-R)
