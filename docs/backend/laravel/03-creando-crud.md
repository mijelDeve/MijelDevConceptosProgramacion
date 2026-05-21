# Diseño de Base de Datos (Clientes y Vehículos)

## Tablas principales

- `clientes` (Clientes)
- `vehiculos` (Vehículos)
- `marcas` (Opcional pero muy recomendable)

## Estructura recomendada

### Tabla `clientes`

| Campo | Tipo | Notas |
|---|---|---|
| id | bigInteger | Auto incremental |
| nombre | string | |
| apellido | string | |
| dni | string | Único |
| telefono | string | |
| email | string | Único y nullable |
| direccion | text | Nullable |
| fecha_nacimiento | date | Nullable |
| notas | text | Nullable |
| activo | boolean | Default: true |
| timestamps | | created_at, updated_at |

### Tabla `marcas` (para vehículos)

| Campo | Tipo | Notas |
|---|---|---|
| id | bigInteger | Auto incremental |
| nombre | string | Ej: Toyota, Honda, Ford |
| timestamps | | created_at, updated_at |

### Tabla `vehiculos`

| Campo | Tipo | Notas |
|---|---|---|
| id | bigInteger | Auto incremental |
| cliente_id | foreignId | FK → clientes |
| marca_id | foreignId | FK → marcas, nullable |
| modelo | string | Ej: Corolla, Civic |
| placa | string | Único |
| vin | string | Nullable (chasis) |
| año | integer | Nullable |
| color | string | Nullable |
| kilometraje | integer | Nullable |
| tipo_combustible | enum | gasolina, diesel, electrico, hibrido |
| notas | text | Nullable |
| activo | boolean | Default: true |
| timestamps | | created_at, updated_at |

## Relaciones

- Un **Cliente** puede tener muchos **Vehículos** (One to Many)
- Un **Vehículo** pertenece a un **Cliente** (Belongs To)
- Una **Marca** puede tener muchos **Vehículos** (One to Many)

---

## Migraciones

### Crear las migraciones

```bash
php artisan make:migration create_clientes_table
php artisan make:migration create_marcas_table
php artisan make:migration create_vehiculos_table
```

### Código de las migraciones

**1. `create_clientes_table.php`**

```php
public function up(): void
{
    Schema::create('clientes', function (Blueprint $table) {
        $table->id();
        $table->string('nombre');
        $table->string('apellido');
        $table->string('dni')->unique();
        $table->string('telefono');
        $table->string('email')->unique()->nullable();
        $table->text('direccion')->nullable();
        $table->date('fecha_nacimiento')->nullable();
        $table->text('notas')->nullable();
        $table->boolean('activo')->default(true);
        $table->timestamps();
    });
}
```

**2. `create_marcas_table.php`**

```php
public function up(): void
{
    Schema::create('marcas', function (Blueprint $table) {
        $table->id();
        $table->string('nombre')->unique();
        $table->timestamps();
    });
}
```

**3. `create_vehiculos_table.php`**

```php
public function up(): void
{
    Schema::create('vehiculos', function (Blueprint $table) {
        $table->id();
        $table->foreignId('cliente_id')->constrained('clientes')->onDelete('cascade');
        $table->foreignId('marca_id')->nullable()->constrained('marcas');
        $table->string('modelo');
        $table->string('placa')->unique();
        $table->string('vin')->nullable()->unique(); // Número de chasis
        $table->integer('año')->nullable();
        $table->string('color')->nullable();
        $table->integer('kilometraje')->nullable();
        $table->enum('tipo_combustible', ['gasolina', 'diesel', 'electrico', 'hibrido'])->default('gasolina');
        $table->text('notas')->nullable();
        $table->boolean('activo')->default(true);
        $table->timestamps();
    });
}
```

### Ejecutar migraciones

```bash
php artisan migrate
```

---

## Modelos

### Crear los modelos

```bash
php artisan make:model Cliente
php artisan make:model Marca
php artisan make:model Vehiculo
```

### Código de los modelos

**1. `app/Models/Cliente.php`**

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Cliente extends Model
{
    use HasFactory;

    protected $table = 'clientes';

    protected $fillable = [
        'nombre',
        'apellido',
        'dni',
        'telefono',
        'email',
        'direccion',
        'fecha_nacimiento',
        'notas',
        'activo',
    ];

    protected $casts = [
        'fecha_nacimiento' => 'date',
        'activo' => 'boolean',
    ];

    public function vehiculos(): HasMany
    {
        return $this->hasMany(Vehiculo::class);
    }

    public function getNombreCompletoAttribute(): string
    {
        return "{$this->nombre} {$this->apellido}";
    }
}
```

**2. `app/Models/Marca.php`**

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Marca extends Model
{
    use HasFactory;

    protected $table = 'marcas';

    protected $fillable = ['nombre'];

    public function vehiculos(): HasMany
    {
        return $this->hasMany(Vehiculo::class);
    }
}
```

**3. `app/Models/Vehiculo.php`**

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Vehiculo extends Model
{
    use HasFactory;

    protected $table = 'vehiculos';

    protected $fillable = [
        'cliente_id',
        'marca_id',
        'modelo',
        'placa',
        'vin',
        'año',
        'color',
        'kilometraje',
        'tipo_combustible',
        'notas',
        'activo',
    ];

    protected $casts = [
        'año' => 'integer',
        'kilometraje' => 'integer',
        'activo' => 'boolean',
    ];

    public function cliente(): BelongsTo
    {
        return $this->belongsTo(Cliente::class);
    }

    public function marca(): BelongsTo
    {
        return $this->belongsTo(Marca::class);
    }
}
```

### Verificar relaciones con Tinker

```bash
php artisan tinker
```

```php
Cliente::first()?->vehiculos;
Vehiculo::first()?->cliente;
Marca::first()?->vehiculos;
```

---

## Form Requests (Validaciones)

### Crear los Form Requests

```bash
php artisan make:request Cliente/StoreClienteRequest
php artisan make:request Cliente/UpdateClienteRequest
php artisan make:request Vehiculo/StoreVehiculoRequest
php artisan make:request Vehiculo/UpdateVehiculoRequest
```

### Código de los Form Requests

**`app/Http/Requests/Cliente/StoreClienteRequest.php`**

```php
<?php

namespace App\Http\Requests\Cliente;

use Illuminate\Foundation\Http\FormRequest;

class StoreClienteRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'nombre'           => 'required|string|max:255',
            'apellido'         => 'required|string|max:255',
            'dni'              => 'required|string|unique:clientes,dni|max:20',
            'telefono'         => 'required|string|max:20',
            'correo'           => 'nullable|email|unique:clientes,correo|max:255',
            'direccion'        => 'nullable|string',
            'fecha_nacimiento' => 'nullable|date|before:today',
        ];
    }
}
```

**`app/Http/Requests/Cliente/UpdateClienteRequest.php`**

```php
<?php

namespace App\Http\Requests\Cliente;

use Illuminate\Foundation\Http\FormRequest;

class UpdateClienteRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        $clienteId = $this->route('cliente');

        return [
            'nombre'           => 'required|string|max:255',
            'apellido'         => 'required|string|max:255',
            'dni'              => 'required|string|max:20|unique:clientes,dni,' . $clienteId,
            'telefono'         => 'required|string|max:20',
            'correo'           => 'nullable|email|unique:clientes,correo,' . $clienteId,
            'direccion'        => 'nullable|string',
            'fecha_nacimiento' => 'nullable|date|before:today',
        ];
    }
}
```

---

## API Resources

### ClienteResource

```bash
php artisan make:resource ClienteResource
```

`app/Http/Resources/ClienteResource.php`:

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class ClienteResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'                => $this->id,
            'nombre'            => $this->nombre,
            'apellido'          => $this->apellido,
            'nombre_completo'   => $this->nombre_completo,
            'dni'               => $this->dni,
            'telefono'          => $this->telefono,
            'correo'            => $this->correo,
            'direccion'         => $this->direccion,
            'fecha_nacimiento'  => $this->fecha_nacimiento?->format('Y-m-d'),
            'activo'            => $this->activo,
            'vehiculos_count'   => $this->whenCounted('vehiculos'),
            'created_at'        => $this->created_at?->format('Y-m-d H:i'),
            'updated_at'        => $this->updated_at?->format('Y-m-d H:i'),
        ];
    }
}
```

### ClienteCollection (opcional)

```bash
php artisan make:resource ClienteCollection
```

`app/Http/Resources/ClienteCollection.php`:

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\ResourceCollection;

class ClienteCollection extends ResourceCollection
{
    public function toArray(Request $request): array
    {
        return [
            'data' => $this->collection,
            'meta' => [
                'total' => $this->collection->count(),
            ]
        ];
    }
}
```

---

## Controlador

### Crear el controlador

```bash
php artisan make:controller ClienteController
```

### Código completo

`app/Http/Controllers/ClienteController.php`:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Cliente;
use App\Http\Requests\Cliente\StoreClienteRequest;
use App\Http\Requests\Cliente\UpdateClienteRequest;
use App\Http\Resources\ClienteResource;
use App\Http\Resources\ClienteCollection;

class ClienteController extends Controller
{
    public function index()
    {
        $clientes = Cliente::withCount('vehiculos')
                        ->orderBy('apellido')
                        ->orderBy('nombre')
                        ->paginate(15);

        return new ClienteCollection($clientes);
    }

    public function store(StoreClienteRequest $request)
    {
        $cliente = Cliente::create($request->validated());

        return response()->json([
            'message' => 'Cliente creado exitosamente',
            'data' => new ClienteResource($cliente)
        ], 201);
    }

    public function show(Cliente $cliente)
    {
        $cliente->load('vehiculos');

        return new ClienteResource($cliente);
    }

    public function update(UpdateClienteRequest $request, Cliente $cliente)
    {
        $cliente->update($request->validated());

        return response()->json([
            'message' => 'Cliente actualizado exitosamente',
            'data' => new ClienteResource($cliente)
        ]);
    }

    public function destroy(Cliente $cliente)
    {
        $cliente->delete();

        return response()->json([
            'message' => 'Cliente eliminado exitosamente'
        ]);
    }
}
```

### Explicación de métodos

- **`index()`** — Lista todos los clientes con paginación (15 por página), ordenados por apellido y nombre. Incluye conteo de vehículos
- **`store()`** — Crea un nuevo cliente con datos validados
- **`show()`** — Muestra un cliente específico con sus vehículos cargados
- **`update()`** — Actualiza un cliente existente
- **`destroy()`** — Elimina un cliente

---

## Rutas

`routes/api.php`:

```php
<?php

use App\Http\Controllers\ClienteController;

Route::apiResource('clientes', ClienteController::class);
```
