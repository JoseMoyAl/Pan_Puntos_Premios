# Pan, Puntos y Premios

Sistema de escritorio para un restaurante que gestiona ventas, inventario y un programa de fidelización con puntos canjeables por premios.

Proyecto académico del curso **Programación II** (2026), desarrollado en equipo por dos estudiantes.

![Java](https://img.shields.io/badge/Java-21-orange) ![Swing](https://img.shields.io/badge/Interfaz-Swing-blue) ![Oracle](https://img.shields.io/badge/Oracle-21c%20XE-red) ![JDBC](https://img.shields.io/badge/Acceso%20a%20datos-JDBC-lightgrey)

## Funcionalidades

- **Clientes:** registro, edición y desactivación, con detección de duplicados por teléfono y correo, historial de pedidos y movimientos de puntos.
- **Productos y categorías:** categorías libres, cada una con un tipo (Comida, Bebida, Acompañamientos o Extras) que define sus reglas de puntos y si puede formar parte de un menú.
- **Menús:** combos formados por una comida, una bebida y un acompañamiento, que al venderse descuentan cada componente del inventario.
- **Inventario:** abastecimientos por proveedor con detalle de productos, costo y registro de cada movimiento.
- **Ventas y cobros:** pedidos con estados (pendiente, pagado, entregado, anulado), pago en efectivo con cálculo de cambio y pago con tarjeta mediante un terminal POS simulado. Si falta inventario al cobrar, el pago se rechaza y el pedido sigue pendiente.
- **Fidelización:** 2 puntos por cada producto de tipo Comida y 8 por cada menú, acreditados al pagar; canje de premios con comprobante.
- **Reportes:** 7 reportes en tablas, entre ellos ventas por período y método de pago, productos más vendidos (individualmente y dentro de menús), existencias bajas, puntos por cliente e historial de canjes.

## Tecnologías

| Área | Herramientas |
| --- | --- |
| Lenguaje e interfaz | Java 21, Swing (`JFrame`, `JTable`), Apache NetBeans (proyecto Ant) |
| Base de datos | Oracle Database 21c XE: 16 tablas con llaves foráneas, secuencias y restricciones `CHECK` |
| Acceso a datos | JDBC (`ojdbc17`), `PreparedStatement`, transacciones con `commit` y `rollback` |
| Diseño | Patrón MVC, programación orientada a objetos, UML y modelo entidad-relación |
| Control de versiones | Git y GitHub |

## Aspectos técnicos destacados

- **Transacciones:** el cobro, el canje y el abastecimiento se ejecutan como una sola transacción. Si un paso falla (por ejemplo, la acreditación de puntos), el `rollback` revierte también el descuento de inventario. `SELECT ... FOR UPDATE` evita que un pedido se cobre dos veces al mismo tiempo.
- **Herencia y polimorfismo:** `ItemVenta` es una clase abstracta con dos subclases, `ProductoVenta` y `MenuVenta`. Cada una implementa `calcularPuntos()` según sus propias reglas.
- **Composición:** `Pedido` contiene su lista de artículos (`ArrayList<ItemVenta>`) y calcula su total y sus puntos recorriéndola.
- **Seguridad en consultas:** todas las consultas usan `PreparedStatement`, lo que previene la inyección SQL.
- **Borrado lógico:** clientes, productos y categorías se desactivan en lugar de eliminarse, para conservar su historial.

## Estructura

```
src/
├── model/        Entidades y jerarquía ItemVenta, ProductoVenta, MenuVenta
├── dao/          Acceso a datos: SQL, PreparedStatement y transacciones
├── controller/   Intermediarios entre las pantallas y los DAO
├── view/         Pantallas Swing y la clase Navegacion
└── util/         Conexión JDBC a Oracle
database/         Esquema, migraciones y datos de prueba
docs/             Diagramas UML y entidad-relación (PDF y draw.io)
lib/              Driver JDBC de Oracle
```

## Instalación

**Requisitos:** JDK 21 o superior, Apache NetBeans y Oracle Database 21c XE.

**1. Crear el usuario de la base de datos.** Conectado como administrador (`sqlplus / as sysdba`):

```sql
ALTER SESSION SET CONTAINER = XEPDB1;
CREATE USER PANPUNTOS IDENTIFIED BY Pan12345;
GRANT CONNECT, RESOURCE TO PANPUNTOS;
ALTER USER PANPUNTOS QUOTA UNLIMITED ON USERS;
```

**2. Crear las tablas.** Desde la carpeta `database`, conectado como `PANPUNTOS`, ejecuta en este orden:

```
@01_esquema.sql
@migraciones/001_clientes_y_premios.sql
@migraciones/002_movimientos_inventario.sql
@migraciones/003_tipo_categoria.sql
@migraciones/004_textos_legibles.sql
@02_datos_prueba.sql
```

El último script es opcional: carga productos, un menú, premios y un cliente de demostración.

**3. Ejecutar.** Abre la carpeta en NetBeans (**File → Open Project**), haz **Clean and Build** y ejecuta. La clase principal es `view.FrmInicio`. Si usas otros datos de conexión, cámbialos en `src/util/Conexion.java`.

## Documentación

- [Diagramas UML y entidad-relación (PDF)](docs/Diagramas_UML_y_DER.pdf)
- [Versión editable para draw.io](docs/Diagramas_UML_y_DER.drawio)

## Autores

- **José** ([@JoseMoyAl](https://github.com/JoseMoyAl)) — persistencia, reglas de negocio, categorías por tipo, reportes, mejoras de la interfaz y documentación.
- **Eduardo Noriega** ([@noriega45](https://github.com/noriega45)) — arquitectura inicial y desarrollo de los módulos.

Proyecto desarrollado con fines académicos.
