# 1. Arquitectura por defecto de Laravel (MVC)

Laravel viene estructurado principalmente bajo el patrón MVC (Modelo-Vista-Controlador), pero es muy flexible.
Aquí está la estructura principal de carpetas de un proyecto Laravel 10:

| Carpeta / Archivo         | Propósito principal                              |
|---------------------------|--------------------------------------------------|
| `app/Models`              | Modelos Eloquent (tablas de la base de datos)    |
| `app/Http/Controllers`    | Controladores (lógica de la aplicación)          |
| `resources/views`         | Vistas Blade (HTML)                              |
| `routes/web.php`          | Rutas para aplicaciones web                      |
| `routes/api.php`          | Rutas para APIs (prefijo `/api` automático)      |
| `database/migrations`     | Migraciones de base de datos                     |
| `database/seeders`        | Datos de prueba                                  |
| `app/Providers`           | Proveedores de servicios (configuración)         |
| `config/`                 | Archivos de configuración                        |
| `resources/js`            | Frontend (Vite + Vue/React)                      |
| `tests/`                  | Pruebas unitarias y de características           |


**Flujo típico MVC:**
Request → Route → Controller → Model → Controller → View (o Resource para API) → Response

# 2. Arquitectura Recomendada para Crear una API con Laravel

Para APIs (especialmente si el proyecto va a crecer), no recomiendo usar MVC puro. Es mejor usar una arquitectura más limpia y mantenible.
Mi recomendación principal (2026): Arquitectura por Capas (Layered Architecture)
Es la más usada en proyectos profesionales de Laravel hoy en día. Es equilibrada, fácil de entender y escalable.
Estructura recomendada:

```
app/
├── Models/                  # Solo Eloquent models + relaciones
├── Http/
│   ├── Controllers/         # Controladores delgados (thin controllers)
│   ├── Requests/            # Form Requests (validaciones)
│   ├── Resources/           # API Resources (transformar datos de salida)
│   └── Middleware/
├── Services/                # Lógica de negocio (recomendado)
│   └── UserService.php
├── Actions/                 # Clases pequeñas y específicas (muy popular)
│   └── CreateUserAction.php
├── Repositories/            # (Opcional) Abstraer consultas a BD
├── DTOs/                    # Data Transfer Objects (opcional pero recomendado)
├── Enums/
└── Exceptions/
```

Ventajas de esta estructura:

- Controladores muy limpios (solo orquestan)
- Lógica de negocio reutilizable
- Fácil de testear
- Preparado para crecer
