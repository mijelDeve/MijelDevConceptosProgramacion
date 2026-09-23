# SQL — Consultas básicas con `SELECT`

`SELECT` es la instrucción fundamental para **consultar información** de una base de datos.

La idea general es:

```sql
SELECT columnas FROM tabla;
```

Por ejemplo, imaginemos una tabla `users`:

| id | name | email | age | city |
| --- | --- | --- | --- | --- |
| 1 | Miguel | miguel@email.com | 23 | Lima |
| 2 | Ana | ana@email.com | 25 | Cusco |
| 3 | Pedro | pedro@email.com | 23 | Lima |

---

## 1. Selección de columnas

La forma más básica:

```sql
SELECT name, email FROM users;
```

Resultado:

| name | email |
| --- | --- |
| Miguel | miguel@email.com |
| Ana | ana@email.com |
| Pedro | pedro@email.com |

Aquí:

```sql
SELECT name, email
```

significa:

> "Quiero obtener las columnas `name` y `email`."

Y:

```sql
FROM users
```

significa:

> "Esas columnas las quiero obtener de la tabla `users`."

### Obtener todas las columnas

Puedes utilizar `*`:

```sql
SELECT * FROM users;
```

Esto devuelve:

| id | name | email | age | city |
| --- | --- | --- | --- | --- |
| 1 | Miguel | miguel@email.com | 23 | Lima |
| 2 | Ana | ana@email.com | 25 | Cusco |
| 3 | Pedro | pedro@email.com | 23 | Lima |

### ¿Cuándo usar `*`?

Es útil para:

```sql
SELECT * FROM users;
```

cuando estás explorando una tabla o haciendo pruebas.

Pero en código de aplicación suele ser preferible especificar las columnas:

```sql
SELECT id, name, email FROM users;
```

¿Por qué?

Porque estás diciendo exactamente qué datos necesitas y evitas traer columnas innecesarias.

---

## 2. `DISTINCT`

`DISTINCT` sirve para **eliminar filas duplicadas del resultado**.

Supongamos que queremos saber qué ciudades aparecen en nuestra tabla:

```sql
SELECT city FROM users;
```

Resultado:

| city |
| --- |
| Lima |
| Cusco |
| Lima |

Tenemos `Lima` repetido.

Podemos utilizar:

```sql
SELECT DISTINCT city FROM users;
```

Resultado:

| city |
| --- |
| Lima |
| Cusco |

Ahora cada ciudad aparece una sola vez.

---

### `DISTINCT` con varias columnas

Aquí hay un concepto importante.

Supongamos:

```sql
SELECT DISTINCT city, age FROM users;
```

`DISTINCT` no elimina duplicados individualmente de cada columna.

Evalúa la **combinación completa**:

```
city + age
```

Por ejemplo:

| city | age |
| --- | --- |
| Lima | 23 |
| Lima | 25 |
| Lima | 23 |
| Cusco | 23 |

Con:

```sql
SELECT DISTINCT city, age FROM users;
```

obtendríamos:

| city | age |
| --- | --- |
| Lima | 23 |
| Lima | 25 |
| Cusco | 23 |

Porque `(Lima, 23)` estaba repetido.

### Regla mental

Piensa en:

```sql
DISTINCT columna1, columna2
```

como:

> "Dame las combinaciones únicas de estas columnas."

---

## 3. Alias de columnas con `AS`

Un **alias** permite cambiar el nombre que aparece en el resultado de una consulta.

Por ejemplo:

```sql
SELECT name AS nombre FROM users;
```

Resultado:

| nombre |
| --- |
| Miguel |
| Ana |
| Pedro |

La columna en la base de datos sigue llamándose:

```
name
```

No estamos modificando la tabla.

Solamente estamos diciendo:

> "En el resultado de esta consulta, muestra `name` como `nombre`."

---

### Alias más descriptivos

Supongamos:

```sql
SELECT name, email FROM users;
```

Podemos hacer:

```sql
SELECT
    name  AS nombre_usuario,
    email AS correo
FROM users;
```

Resultado:

| nombre_usuario | correo |
| --- | --- |
| Miguel | miguel@email.com |
| Ana | ana@email.com |
| Pedro | pedro@email.com |

Esto es bastante útil cuando estás construyendo consultas que posteriormente consumirán tus aplicaciones.

---

## 4. `AS` no siempre es obligatorio

Puedes escribir:

```sql
SELECT name AS nombre FROM users;
```

o:

```sql
SELECT name nombre FROM users;
```

Ambas formas pueden funcionar dependiendo del motor SQL.

Sin embargo, para código mantenible recomiendo utilizar explícitamente:

```sql
AS
```

porque deja clara la intención:

```sql
SELECT
    name  AS nombre,
    email AS correo
FROM users;
```

---

## 5. Alias para tablas

Aunque este tema habla específicamente de alias de **columnas**, es importante que conozcas que `AS` también puede utilizarse para tablas.

Por ejemplo:

```sql
SELECT u.name, u.email FROM users AS u;
```

Aquí:

```sql
users AS u
```

significa:

> "Dentro de esta consulta, voy a referirme a `users` como `u`."

Entonces:

```sql
u.name
```

significa:

```sql
users.name
```

Esto se vuelve **muy importante cuando estudies `JOIN`**.

Por ejemplo:

```sql
SELECT
    u.name  AS usuario,
    u.email AS correo
FROM users AS u;
```

---

## 6. Combinando `SELECT`, `DISTINCT` y `AS`

Podemos utilizar los tres juntos.

```sql
SELECT DISTINCT
    city AS ciudad
FROM users;
```

Resultado:

| ciudad |
| --- |
| Lima |
| Cusco |

Aquí tenemos:

```sql
SELECT
```

→ queremos consultar datos.

```sql
DISTINCT
```

→ queremos eliminar combinaciones duplicadas.

```sql
city
```

→ queremos la columna `city`.

```sql
AS ciudad
```

→ queremos que el resultado se llame `ciudad`.

```sql
FROM users
```

→ los datos vienen de `users`.

---

## 7. Orden básico de una consulta

Por ahora, puedes memorizar esta estructura:

```sql
SELECT [DISTINCT] columnas FROM tabla;
```

Ejemplos:

### Una columna

```sql
SELECT name FROM users;
```

### Varias columnas

```sql
SELECT name, email FROM users;
```

### Todas

```sql
SELECT * FROM users;
```

### Sin duplicados

```sql
SELECT DISTINCT city FROM users;
```

### Con alias

```sql
SELECT name AS username FROM users;
```

### Todo junto

```sql
SELECT DISTINCT
    city AS ciudad
FROM users;
```

---

## 8. Algo importante: `SELECT` no modifica datos

Esta distinción es fundamental en SQL.

```sql
SELECT
```

sirve para **consultar**.

No modifica la información.

Por ejemplo:

```sql
SELECT name FROM users;
```

solo lee información.

Más adelante encontrarás:

```sql
INSERT
```

para insertar.

```sql
UPDATE
```

para actualizar.

```sql
DELETE
```

para eliminar.

Por ahora puedes pensar:

```sql
SELECT → leer
INSERT → crear
UPDATE → modificar
DELETE → eliminar
```

---

## 9. Ejercicios para practicar

Supongamos esta tabla `products`:

| id | name | category | price |
| --- | --- | --- | --- |
| 1 | Laptop | Technology | 3500 |
| 2 | Mouse | Technology | 80 |
| 3 | Keyboard | Technology | 150 |
| 4 | Chair | Furniture | 500 |
| 5 | Desk | Furniture | 800 |
| 6 | Mouse | Technology | 80 |

### Ejercicio 1

Obtén todos los productos.

```sql
SELECT * FROM products;
```

### Ejercicio 2

Obtén únicamente el nombre y precio.

```sql
SELECT name, price FROM products;
```

### Ejercicio 3

Obtén las categorías sin repetir.

```sql
SELECT DISTINCT category FROM products;
```

### Ejercicio 4

Obtén el nombre del producto, pero que la columna del resultado se llame `producto`.

```sql
SELECT name AS producto FROM products;
```

### Ejercicio 5

Obtén categorías únicas y cambia el nombre de la columna a `tipo_producto`.

```sql
SELECT DISTINCT
    category AS tipo_producto
FROM products;
```

---

## 🧠 Lo que deberías llevarte de este tema

| Concepto | Para qué sirve | Ejemplo |
| --- | --- | --- |
| `SELECT` | Elegir qué datos consultar | `SELECT name` |
| `*` | Seleccionar todas las columnas | `SELECT *` |
| `DISTINCT` | Eliminar resultados duplicados | `SELECT DISTINCT city` |
| `AS` | Crear un alias | `name AS nombre` |
| `FROM` | Indicar la tabla origen | `FROM users` |

La estructura que deberías tener grabada es:

```sql
SELECT [DISTINCT] columna1, columna2 FROM tabla;
```