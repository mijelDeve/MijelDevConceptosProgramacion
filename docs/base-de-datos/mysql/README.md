# MySQL

Curso de MySQL orientado a bases de datos relacionales con MySQL. Cada clase se cubre en un archivo numerado.

> **Usa una base de datos de prueba** (ej. `CREATE DATABASE tienda`) y practica cada consulta en tu cliente MySQL.

---

## 🟢 Nivel Básico: Fundamentos y Manipulación de Datos

Este nivel se centra en cómo extraer y modificar información simple de una sola tabla.

- ✅ [Consultas Básicas (`SELECT`)](01-consultas-select): Selección de columnas, uso de `DISTINCT` para eliminar duplicados y alias de columnas (`AS`).
- [ ] **Filtrado de Datos (`WHERE`)**: Uso de operadores de comparación (`=`, `!=`, `>`, `<`, `>=`, `<=`).
- [ ] **Operadores Lógicos**: Combinación de condiciones con `AND`, `OR` y `NOT`.
- [ ] **Filtros de Rangos y Patrones**: Uso de `BETWEEN`, `IN`, `IS NULL` y el operador de coincidencia parcial `LIKE` (con comodines `%` y `_`).
- [ ] **Ordenamiento y Limitación**: Uso de `ORDER BY` (ASC/DESC) y `LIMIT` / `OFFSET` para paginación.
- [ ] **Manipulación de Datos (DML)**: Inserción (`INSERT INTO`), actualización (`UPDATE`) y eliminación (`DELETE`) de registros.

## 🟡 Nivel Intermedio: Relaciones y Agregación

Aquí empiezas a conectar múltiples tablas y a realizar análisis estadísticos básicos de los datos.

- [ ] **Funciones de Agregación**: Cálculo de métricas con `COUNT`, `SUM`, `AVG`, `MAX` y `MIN`.
- [ ] **Agrupamiento de Datos (`GROUP BY`)**: Agrupar filas que comparten características y el uso de `HAVING` para filtrar grupos (a diferencia de `WHERE` que filtra filas individuales).
- [ ] **Uniones de Tablas (`JOINS`)**:
  - `INNER JOIN`: Coincidencias exactas entre tablas.
  - `LEFT JOIN` / `RIGHT JOIN`: Todo de una tabla y las coincidencias de la otra.
  - `FULL OUTER JOIN`: Todos los registros de ambas tablas.
  - `CROSS JOIN` y `SELF JOIN`: Productos cartesianos y auto-referencias.
- [ ] **Operadores de Conjuntos**: Combinación de resultados de múltiples consultas con `UNION`, `UNION ALL`, `INTERSECT` y `EXCEPT`.
- [ ] **Subconsultas (Subqueries)**: Consultas anidadas dentro de otra consulta (en el `WHERE`, `FROM` o `SELECT`).

## 🔴 Nivel Avanzado: Rendimiento, Estructura y Lógica de Servidor

Este nivel cubre la optimización de consultas, la programación del lado del motor de base de datos y la gestión de transacciones.

- [ ] **Common Table Expressions (CTEs)**: Uso de la cláusula `WITH` para crear tablas temporales legibles dentro de una consulta, incluyendo CTEs recursivas.
- [ ] **Funciones de Ventana (Window Functions)**: Análisis avanzado sin perder el detalle de las filas usando `OVER()` con funciones como `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()` y funciones acumulativas.
- [ ] **Índices y Optimización**: Creación y gestión de índices (`INDEX`, `UNIQUE`), comprensión de la estructura de un árbol B-Tree y uso del comando `EXPLAIN` para analizar y mejorar los planes de ejecución de consultas lentas.
- [ ] **Vistas (Views)**: Creación y mantenimiento de vistas estándar y materializadas para simplificar consultas complejas.
- [ ] **Transacciones y Control de Concurrencia**: Propiedades ACID, uso de `BEGIN TRANSACTION`, `COMMIT`, `ROLLBACK` y manejo de bloqueos (*locks*).
- [ ] **Programación en Base de Datos**: Creación de Procedimientos Almacenados (*Stored Procedures*), Funciones definidas por el usuario (*UDFs*) y Disparadores (*Triggers*).

---

## Índice de clases

- [Conceptos básicos](00-conceptos-basicos) — instalación, conexión y gestión de bases de datos desde la terminal.
- [Consultas con `SELECT`](01-consultas-select) — selección de columnas, `DISTINCT` y alias (`AS`).