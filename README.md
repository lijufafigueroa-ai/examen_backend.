# Examen #1: Tienda de Libros - Normalización, Modelo E-R e Implementación SQL

## Parte 1: Normalización hasta Tercera Forma Normal (3FN)

### 1.1 Estado No Normalizado (0FN)
La tabla original contiene datos mezclados de Libros, Autores, Editoriales, Categorías, Clientes, Pedidos y Métodos de Pago en una sola estructura no atómica.

### 1.2 Primera Forma Normal (1FN)
* **Regla:** Se garantiza la atomicidad de los campos (no campos compuestos) y se eliminan listas/grupos repetidos.
* **Ajustes:** Se separan los nombres completos de autores y clientes en campos independientes (`nombre` y `apellido`).

**Atributos atómicos en 1FN:**
`ISBN`, `titulo`, `nombre_autor`, `apellido_autor`, `fecha_nacimiento_autor`, `editorial`, `categoria`, `precio`, `stock`, `nombre_cliente`, `apellido_cliente`, `correo_cliente`, `direccion_cliente`, `telefono_cliente`, `metodo_pago`, `monto_pago`.

### 1.3 Segunda Forma Normal (2FN)
* **Regla:** Eliminación de dependencias parciales respecto a la clave primaria.
* **Ajustes:** Se descomponen los datos en entidades independientes, garantizando que atributos como el título del libro o los datos del cliente dependan únicamente de su identificador principal.

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

## Parte 2: Modelo Entidad-Relación (E-R) y Esquema UML

### 2.1 Cardinalidades y Reglas de Negocio
* **AUTOR - LIBRO:** Muchos a Muchos (N:M). Un autor escribe varios libros; un libro puede tener varios autores (se resuelve con la tabla intermedia `LIBRO_AUTOR`).
* **EDITORIAL - LIBRO:** Uno a Muchos (1:N). Una editorial publica muchos libros.
* **CATEGORIA - LIBRO:** Uno a Muchos (1:N). Una categoría agrupa varios libros.
* **CLIENTE - PEDIDO:** Uno a Muchos (1:N). Un cliente realiza varios pedidos.
* **PEDIDO - LIBRO:** Muchos a Muchos (N:M). Un pedido contiene varios libros; un libro está en varios pedidos (se resuelve con `DETALLE_PEDIDO`).
* **PEDIDO - TRANSACCION:** Uno a Muchos (1:N). Un pedido genera registros de pago.
* **METODO_PAGO - TRANSACCION:** Uno a Muchos (1:N). Un método de pago procesa múltiples transacciones.

---

### 2.2 Diagrama Entidad-Relación (Hecho a Mano)

> **Nota:** A continuación se adjuntan las fotografías/escaneos del Diagrama Entidad-Relación elaborado manualmente.

![Diagrama Entidad Relación a Mano](./diagrama_er_mano.png)

*(Si tienes varias fotos, puedes agregar otra línea como esta:)*
*![Diagrama Entidad Relación Parte 2](./diagrama_er_mano_2.png)*

---

### 2.3 Diagrama de Estructura de Tablas (Esquema UML E-R)

```text
+-----------------------------------+
|               AUTOR               |
+-----------------------------------+
| id_autor: INT (PK)                |
| nombre: VARCHAR(50)               |
| apellido: VARCHAR(50)             |
| fecha_nacimiento: DATE            |
+-----------------------------------+
                  | 1
                  |
                  | N
+-----------------------------------+
|            LIBRO_AUTOR            |
+-----------------------------------+
| isbn: VARCHAR(20) (PK, FK)        |
| id_autor: INT (PK, FK)            |
+-----------------------------------+
                  | N
                  |
                  | 1
+-----------------------------------+         +-----------------------------------+
|               LIBRO               | N     1 |             EDITORIAL             |
+-----------------------------------+---------+-----------------------------------+
| isbn: VARCHAR(20) (PK)            |         | id_editorial: INT (PK)            |
| titulo: VARCHAR(150)              |         | nombre_editorial: VARCHAR(100)    |
| precio: DECIMAL(10,2)             |         +-----------------------------------+
| stock: INT                        |
| id_editorial: INT (FK)            | N     1 +-----------------------------------+
| id_categoria: INT (FK)            |---------|             CATEGORIA             |
+-----------------------------------+         +-----------------------------------+
                  | 1                         | id_categoria: INT (PK)            |
                  |                           | nombre_categoria: VARCHAR(50)     |
                  | N                         +-----------------------------------+
+-----------------------------------+
|          DETALLE_PEDIDO           |
+-----------------------------------+
| id_pedido: INT (PK, FK)           |
| isbn: VARCHAR(20) (PK, FK)        |
| cantidad: INT                     |
| precio_unitario: DECIMAL(10,2)    |
+-----------------------------------+
                  | N
                  |
                  | 1
+-----------------------------------+         +-----------------------------------+
|              PEDIDO               | N     1 |              CLIENTE              |
+-----------------------------------+---------+-----------------------------------+
| id_pedido: INT (PK)               |         | id_cliente: INT (PK)              |
| fecha_pedido: DATETIME            |         | nombre: VARCHAR(50)               |
| monto_total: DECIMAL(10,2)        |         | apellido: VARCHAR(50)             |
| id_cliente: INT (FK)              |         | correo: VARCHAR(100)              |
+-----------------------------------+         | direccion: VARCHAR(150)           |
                  | 1                         | telefono: VARCHAR(20)             |
                  |                           +-----------------------------------+
                  | N
+-----------------------------------+
|            TRANSACCION            |
+-----------------------------------+
| id_transaccion: INT (PK)          |
| monto_pago: DECIMAL(10,2)         |
| fecha_transaccion: DATETIME       |
| id_pedido: INT (FK)               |
| id_metodo_pago: INT (FK)          |
+-----------------------------------+
                  | N
                  |
                  | 1
+-----------------------------------+
|            METODO_PAGO            |
+-----------------------------------+
| id_metodo_pago: INT (PK)          |
| nombre_metodo: VARCHAR(50)        |
+-----------------------------------+
