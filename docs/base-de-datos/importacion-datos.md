# Importación de Datos

## 1. Importar desde línea de comandos

Es mucho más rápido que phpMyAdmin o herramientas gráficas.

```bash
mysql -u usuario -p nombre_base < backup.sql
```

Si estás en Windows PowerShell:

```powershell
Get-Content backup.sql | mysql -u usuario -p nombre_base
```

o mejor aún desde CMD:

```cmd
mysql -u usuario -p nombre_base < backup.sql
```

## 2. Desactivar validaciones temporalmente

Antes de importar:

```sql
SET foreign_key_checks = 0;
SET unique_checks = 0;
SET autocommit = 0;
```

Después de importar:

```sql
COMMIT;
SET foreign_key_checks = 1;
SET unique_checks = 1;
```

Muchos dumps generados por `mysqldump` ya incluyen estas optimizaciones.

## 3. Aumentar buffers de MySQL

Si tienes suficiente RAM, aumenta temporalmente en `my.ini`:

```ini
innodb_buffer_pool_size=2G
innodb_log_file_size=512M
innodb_log_buffer_size=128M
```

Para bases muy grandes puede marcar una diferencia importante.

## 4. Comprimir el dump

Si el archivo está comprimido (`.gz`), evita descomprimirlo primero:

**Linux**:

```bash
gunzip < backup.sql.gz | mysql -u usuario -p nombre_base
```

Así reduces I/O de disco.

## 5. Desactivar índices temporalmente (MyISAM)

Si las tablas son MyISAM:

```sql
ALTER TABLE tabla DISABLE KEYS;
```

y luego:

```sql
ALTER TABLE tabla ENABLE KEYS;
```

Para InnoDB normalmente no aplica.

## 6. Revisar si el dump tiene INSERTs individuales

**Lento**:

```sql
INSERT INTO usuarios VALUES (...);
INSERT INTO usuarios VALUES (...);
INSERT INTO usuarios VALUES (...);
```

**Rápido**:

```sql
INSERT INTO usuarios VALUES (...), (...), (...), (...);
```

Los dumps generados con:

```bash
mysqldump --extended-insert
```

son mucho más rápidos de restaurar.

## 7. Usar MySQL Shell para cargas masivas

Si trabajas con MySQL 8:

```bash
mysqlsh
```

y herramientas como:

```js
util.loadDump()
```

pueden ser considerablemente más rápidas que el cliente tradicional.

## 8. Identificar el cuello de botella

¿Cuánto pesa el dump?

- Menos de 1 GB → debería importar relativamente rápido.
- 5–10 GB → puede tardar varios minutos.
- Más de 20 GB → ya conviene optimizar buffers y configuración.
