# MySQL: Conceptos básicos

Comandos esenciales para gestionar bases de datos desde la terminal de MySQL.

---

## 1. Conectarse a MySQL

```bash
mysql -u root -p
```

Ingresa la contraseña cuando se solicite. Para conectarte a un servidor remoto:

```bash
mysql -h host -u usuario -p
```

---

## 2. Ver bases de datos existentes

```sql
SHOW DATABASES;
```

---

## 3. Crear una base de datos

```sql
CREATE DATABASE nombre_db;
```

Especificando juego de caracteres y cotejamiento:

```sql
CREATE DATABASE nombre_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

```bash
# También desde la terminal sin entrar al cliente:
mysql -u root -p -e "CREATE DATABASE nombre_db"
```

---

## 4. Seleccionar una base de datos

```sql
USE nombre_db;
```

Para ver en qué base de datos estás trabajando:

```sql
SELECT DATABASE();
```

---

## 5. Renombrar una base de datos

MySQL **no** tiene un comando `RENAME DATABASE` (fue eliminado en MySQL 5.1.23 por riesgos de seguridad). Hay dos alternativas:

### Opción A: Usar mysqldump (recomendada)

```bash
# 1. Exportar la base de datos actual
mysqldump -u root -p nombre_viejo > backup.sql

# 2. Crear la nueva base de datos
mysql -u root -p -e "CREATE DATABASE nombre_nuevo"

# 3. Importar los datos
mysql -u root -p nombre_nuevo < backup.sql

# 4. Eliminar la base de datos vieja
mysql -u root -p -e "DROP DATABASE nombre_viejo"
```

### Opción B: Renombrar tabla por tabla (más rápida, sin dump)

```sql
CREATE DATABASE nombre_nuevo;
RENAME TABLE nombre_viejo.tabla1 TO nombre_nuevo.tabla1,
             nombre_viejo.tabla2 TO nombre_nuevo.tabla2,
             nombre_viejo.tabla3 TO nombre_nuevo.tabla3;
DROP DATABASE nombre_viejo;
```

Para generar automáticamente los comandos `RENAME TABLE` de todas las tablas:

```sql
SELECT CONCAT('RENAME TABLE nombre_viejo.', table_name,
              ' TO nombre_nuevo.', table_name, ';')
FROM information_schema.tables
WHERE table_schema = 'nombre_viejo';
```

---

## 6. Eliminar una base de datos

```sql
DROP DATABASE nombre_db;
```

```bash
# Desde la terminal:
mysql -u root -p -e "DROP DATABASE nombre_db"
```

> **⚠️ Cuidado:** `DROP DATABASE` elimina la base de datos y todas sus tablas de forma irreversible. No se puede deshacer.

---

## 7. Ver la base de datos activa

```sql
SELECT DATABASE();
```

---

## Buenas prácticas

- Usa `utf8mb4` como `CHARACTER SET` para soportar emojis y caracteres especiales.
- Nombra las bases de datos en **snake_case** (ej. `mi_proyecto`).
- No uses mayúsculas ni espacios en los nombres.
- Siempre verifica que estás en la base correcta antes de ejecutar `DROP`.
- Para entornos de producción, haz un backup (`mysqldump`) antes de renombrar o eliminar.
- Usa `SHOW CREATE DATABASE nombre_db;` para ver la configuración actual de una base existente.
