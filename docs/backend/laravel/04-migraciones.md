# Migraciones en Laravel

Las migraciones son el control de versiones de tu base de datos. Permiten definir y modificar el esquema de forma programática en PHP, compartirlo con el equipo y mantener un historial de cambios.

---

## Crear una migración

```bash
php artisan make:migration create_clientes_table
```

Laravel genera un archivo en `database/migrations/` con un timestamp (`2025_01_01_000000_create_clientes_table.php`).

### Convenciones de nombre

| Comando | Tipo de migración |
|---|---|
| `create_clientes_table` | Creación de tabla (`Schema::create`) |
| `add_telefono_to_clientes_table` | Modificación de tabla (`Schema::table`) |
| `update_clientes_table` | Modificación genérica |
| `drop_clientes_table` | Eliminación de tabla |

Si sigues las convenciones, Laravel autocompleta el `Schema::create` o `Schema::table`.

---

## Estructura de una migración

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('clientes', function (Blueprint $table) {
            $table->id();
            $table->string('nombre');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('clientes');
    }
};
```

- **`up()`** — Define los cambios a aplicar (crear/modificar tablas)
- **`down()`** — Revierte los cambios del método `up()`

---

## Tipos de columnas

```php
$table->id();                      // BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
$table->bigIncrements('id');
$table->bigInteger('votes');
$table->binary('data');
$table->boolean('confirmed')->default(false);
$table->char('name', 100);
$table->date('birth_date');
$table->dateTime('created_at', precision: 0);
$table->decimal('amount', total: 10, places: 2);
$table->double('distance', 8, 2);
$table->enum('status', ['active', 'inactive']);
$table->float('score', 8, 2);
$table->foreignId('user_id');      // BIGINT UNSIGNED (para FK)
$table->foreignUuid('user_id');
$table->integer('age');
$table->json('options');
$table->jsonb('data');
$table->longText('description');
$table->mediumInteger('counter');
$table->mediumText('notes');
$table->morphs('taggable');        // taggable_id BIGINT + taggable_type STRING
$table->nullableMorphs('taggable');
$table->nullableTimestamps();
$table->rememberToken();
$table->smallInteger('quantity');
$table->softDeletes();             // deleted_at TIMESTAMP NULL
$table->softDeletesTz();
$table->string('title', 255);
$table->text('body');
$table->time('sunrise');
$table->timestamp('added_at');
$table->timestamps();              // created_at + updated_at
$table->timestampTz('created_at');
$table->tinyInteger('rating');
$table->uuid('id')->primary();
$table->year('birth_year');
```

---

## Modificadores de columna

Se encadenan después del tipo:

```php
$table->string('email')->nullable();              // Permitir NULL
$table->string('email')->unique();                // Índice único
$table->string('name')->default('John');          // Valor por defecto
$table->string('name')->comment('Nombre completo');// Comentario en BD
$table->integer('age')->unsigned();               // Solo positivos
$table->string('email')->after('name');           // Posición después de columna
$table->string('email')->first();                 // Mover al inicio
```

### Modificadores disponibles

| Modificador | Descripción |
|---|---|
| `->after('column')` | Colocar después de otra columna (MySQL) |
| `->autoIncrement()` | AUTO_INCREMENT |
| `->charset('utf8mb4')` | Charset |
| `->collation('utf8mb4_unicode_ci')` | Collation |
| `->comment('texto')` | Comentario en la BD |
| `->default($value)` | Valor por defecto |
| `->first()` | Colocar al inicio (MySQL) |
| `->from($integer)` | Valor inicial AUTO_INCREMENT |
| `->nullable()` | Permitir NULL |
| `->nullable(false)` | Forzar NOT NULL |
| `->unsigned()` | Sin signo (solo integer) |
| `->unique()` | Índice único |
| `->useCurrent()` | Usar CURRENT_TIMESTAMP por defecto |

---

## Índices

```php
$table->primary('id');                    // Clave primaria
$table->primary(['id', 'parent_id']);     // Clave primaria compuesta
$table->unique('email');                  // Índice único
$table->unique(['email', 'tenant_id']);   // Único compuesto
$table->index('status');                  // Índice normal
$table->index(['status', 'type']);        // Índice compuesto
$table->fullText('content');              // Índice FULLTEXT (MySQL)
$table->spatialIndex('location');         // Índice espacial
```

### Eliminar índices

```php
$table->dropPrimary('table_id_primary');
$table->dropUnique('table_email_unique');
$table->dropIndex('table_status_index');
$table->dropFullText('table_content_fulltext');
$table->dropSpatialIndex('table_location_spatialindex');
```

---

## Foreign Keys

```php
// Forma corta (asume convención: user_id → tabla users, columna id)
$table->foreignId('user_id')->constrained()->onDelete('cascade');

// Forma explícita
$table->unsignedBigInteger('user_id');
$table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');

// Tabla y columna personalizada
$table->foreignId('cliente_id')->constrained('clientes')->onDelete('cascade');

// Con nullable (opcional)
$table->foreignId('marca_id')->nullable()->constrained('marcas');

// Eliminar FK
$table->dropForeign(['user_id']);
$table->dropConstrainedForeignId('user_id');
```

### Acciones de borrado/actualización

| Método | Comportamiento |
|---|---|
| `->onDelete('cascade')` | Elimina registros hijos |
| `->onDelete('restrict')` | Bloquea eliminación si hay hijos |
| `->onDelete('set null')` | Pone NULL en la FK |
| `->onDelete('no action')` | No hace nada (depende de motor) |
| `->onUpdate('cascade')` | Actualiza FK si cambia PK |

---

## Modificar tablas existentes

Requiere instalar `doctrine/dbal`:

```bash
composer require doctrine/dbal
```

### Agregar columnas

```php
Schema::table('clientes', function (Blueprint $table) {
    $table->string('telefono')->after('nombre');
    $table->text('notas')->nullable();
});
```

### Modificar columnas

```php
Schema::table('clientes', function (Blueprint $table) {
    $table->string('nombre', 100)->change();           // Cambiar longitud
    $table->string('email')->nullable()->change();     // Hacer nullable
    $table->string('email')->nullable(false)->change();// Quitar nullable
});
```

### Renombrar columnas

```php
Schema::table('clientes', function (Blueprint $table) {
    $table->renameColumn('nombre', 'first_name');
});
```

### Eliminar columnas

```php
Schema::table('clientes', function (Blueprint $table) {
    $table->dropColumn('telefono');
    $table->dropColumn(['telefono', 'notas']);  // Múltiples columnas
});
```

---

## Comandos de migración

```bash
# Ejecutar migraciones pendientes
php artisan migrate

# Ejecutar migraciones forzado (sin confirmación en producción)
php artisan migrate --force

# Ver estado de las migraciones
php artisan migrate:status

# Revertir el último lote de migraciones
php artisan migrate:rollback

# Revertir un número específico de pasos
php artisan migrate:rollback --step=3

# Revertir todas las migraciones
php artisan migrate:reset

# Revertir y ejecutar de nuevo todas las migraciones
php artisan migrate:refresh

# Revertir y ejecutar de nuevo (con seeders)
php artisan migrate:refresh --seed

# Eliminar todas las tablas y ejecutar migraciones
php artisan migrate:fresh

# Eliminar todo y ejecutar migraciones + seeders
php artisan migrate:fresh --seed
```

---

## Buenas prácticas

1. **Nunca editar migraciones ya ejecutadas** — Crea una nueva migración para el cambio
2. **Siempre implementar `down()`** — Toda migración debe ser reversible
3. **Una migración, un propósito** — No mezcles cambios no relacionados
4. **Usar `--force` solo en producción** — Siempre revisar antes de migrar en producción
5. **Versionar las migraciones** — Se incluyen en el repositorio (Git)
6. **Nombrar descriptivamente** — `add_telefono_to_clientes_table` en vez de `update_clientes`
7. **Preferir `foreignId()->constrained()`** — Es más limpio que escribir la FK manualmente
8. **Usar `nullable()` para columnas opcionales** — Evita errores al insertar datos incompletos
