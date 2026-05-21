# Requisitos Requisitos

- PHP 8.1 o superior
- Composer (versión 2.2 o superior)
- Extensiones de PHP recomendadas: BCMath, Ctype, Fileinfo, JSON, Mbstring, OpenSSL, PDO, Tokenizer, XML
- (Opcional pero recomendado) Node.js + NPM para el frontend (Vite)

## Cómo instalar composer (Windows)

**1. Descarga el instalador oficial**
- Ve a esta página: https://getcomposer.org/download/
- Descarga el archivo Composer-Setup.exe

**2. Ejecuta el instalador**
- Abre el archivo Composer-Setup.exe que descargaste.
- Dale permisos si te pregunta Windows.
- Elige "Install for all users" (recomendado).
- El instalador buscará automáticamente tu instalación de PHP (XAMPP, Laragon, PHP standalone, etc.).
- Si no detecta PHP, tendrás que indicarle manualmente la ruta (normalmente C:\xampp\php o C:\laragon\bin\php\php-8.x.x).

**3. Finaliza la instalación**
- Sigue los pasos (Next → Next → Install).
- Marca la opción para agregar Composer al PATH (el instalador suele hacerlo por defecto).

# Crear proyecto con composer (Recomendado)

Abre una terminal (Command Prompt, PowerShell, Terminal, etc.) y ejecuta:

```bash
composer create-project laravel/laravel mi-proyecto "10.*"
```

- mi-proyecto → nombre de tu carpeta/proyecto (cámbialo por lo que quieras).
- "10.*" → fuerza la instalación de Laravel 10 (la versión más reciente de la serie 10).

Este comando descarga Laravel 10 y todas sus dependencias automáticamen

# Entrar al proyecto e iniciar el superior

```bash
cd mi-proyecto
php artisan serve
```

# Comándos útiles

```bash
# Generar clave de aplicación
php artisan key:generate

# Ver rutas
php artisan route:list

# Crear un controlador
php artisan make:controller MiControlador

# Crear un modelo
php artisan make:model MiModelo

# Correr migraciones (base de datos)
php artisan migrate
```
