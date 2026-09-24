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

## Parte 2: Modelo Entidad-Relación (E-R)

### 2.1 Cardinalidades y Reglas de Negocio
* **AUTOR - LIBRO:** Muchos a Muchos (N:M). Un autor escribe varios libros; un libro puede tener varios autores (se resuelve con la tabla intermedia `LIBRO_AUTOR`).
* **EDITORIAL - LIBRO:** Uno a Muchos (1:N). Una editorial publica muchos libros.
* **CATEGORIA - LIBRO:** Uno a Muchos (1:N). Una categoría agrupa varios libros.
* **CLIENTE - PEDIDO:** Uno a Muchos (1:N). Un cliente realiza varios pedidos.
* **PEDIDO - LIBRO:** Muchos a Muchos (N:M). Un pedido contiene varios libros; un libro está en varios pedidos (se resuelve con `DETALLE_PEDIDO`).
* **PEDIDO - TRANSACCION:** Uno a Muchos (1:N). Un pedido genera registros de pago.
* **METODO_PAGO - TRANSACCION:** Uno a Muchos (1:N). Un método de pago procesa múltiples transacciones.

### 2.2 Diagrama Entidad-Relación Gráfico (Mermaid)

```mermaid
erDiagram
    AUTOR {
        int id_autor PK
        string nombre
        string apellido
        date fecha_nacimiento
    }
    EDITORIAL {
        int id_editorial PK
        string nombre_editorial
    }
    CATEGORIA {
        int id_categoria PK
        string nombre_categoria
    }
    LIBRO {
        string isbn PK
        string titulo
        decimal precio
        int stock
        int id_editorial FK
        int id_categoria FK
    }
    LIBRO_AUTOR {
        string isbn PK, FK
        int id_autor PK, FK
    }
    CLIENTE {
        int id_cliente PK
        string nombre
        string apellido
        string correo
        string direccion
        string telefono
    }
    PEDIDO {
        int id_pedido PK
        datetime fecha_pedido
        decimal monto_total
        int id_cliente FK
    }
    DETALLE_PEDIDO {
        int id_pedido PK, FK
        string isbn PK, FK
        int cantidad
        decimal precio_unitario
    }
    TRANSACCION {
        int id_transaccion PK
        decimal monto_pago
        datetime fecha_transaccion
        int id_pedido FK
        int id_metodo_pago FK
    }
    METODO_PAGO {
        int id_metodo_pago PK
        string nombre_metodo
    }

    AUTOR ||--o{ LIBRO_AUTOR : "escribe"
    LIBRO ||--o{ LIBRO_AUTOR : "pertenece"
    EDITORIAL ||--o{ LIBRO : "publica"
    CATEGORIA ||--o{ LIBRO : "clasifica"
    CLIENTE ||--o{ PEDIDO : "realiza"
    PEDIDO ||--o{ DETALLE_PEDIDO : "contiene"
    LIBRO ||--o{ DETALLE_PEDIDO : "incluye"
    PEDIDO ||--o{ TRANSACCION : "genera"
    METODO_PAGO ||--o{ TRANSACCION : "procesa"
