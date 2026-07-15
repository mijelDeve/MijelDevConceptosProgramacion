# Colas en Laravel

## ¿Qué son las colas?

Las colas (Queues) permiten ejecutar tareas en segundo plano, evitando que el usuario tenga que esperar a que terminen procesos costosos.

Por ejemplo:

- Enviar un correo.
- Generar un PDF.
- Procesar una imagen.
- Importar un Excel.
- Consumir una API externa.
- Enviar miles de notificaciones.

**Sin colas:**

```
Usuario
   │
   ▼
Petición HTTP
   │
   ├── Guardar usuario
   ├── Enviar correo (3 segundos)
   ├── Generar PDF (5 segundos)
   └── Subir archivos (4 segundos)

Tiempo de respuesta: 12 segundos
```

**Con colas:**

```
Usuario
   │
   ▼
Petición HTTP
   │
   ├── Guardar usuario
   ├── Encolar correo
   ├── Encolar PDF
   └── Encolar imágenes

Respuesta: 150 ms

Mientras tanto...

Worker
   ├── Envía correo
   ├── Genera PDF
   └── Procesa imágenes
```

El usuario recibe una respuesta rápida mientras Laravel procesa las tareas en segundo plano.

---

## Arquitectura de una cola

```
Controller
      │
      ▼
dispatch(new SendEmailJob())
      │
      ▼
Queue (Redis, Database, SQS...)
      │
      ▼
Queue Worker
      │
      ▼
handle() del Job
```

---

## Drivers disponibles

Laravel soporta varios drivers.

| Driver       | Uso                                      |
|--------------|------------------------------------------|
| `sync`       | Ejecuta inmediatamente (sin cola)        |
| `database`   | Guarda los Jobs en una tabla             |
| `redis`      | Muy rápido (el más usado)                |
| `sqs`        | Amazon SQS                               |
| `beanstalkd` | Servidor de colas                        |
| `null`       | Ignora todos los Jobs                    |

En el `.env`:

```
QUEUE_CONNECTION=database
```

o

```
QUEUE_CONNECTION=redis
```

---

## Crear un Job

```bash
php artisan make:job SendInvoiceJob
```

Laravel crea:

```
app/
   Jobs/
      SendInvoiceJob.php
```

Ejemplo:

```php
class SendInvoiceJob implements ShouldQueue
{
    use Queueable;

    public function __construct(
        public Invoice $invoice
    ) {}

    public function handle()
    {
        // lógica pesada
    }
}
```

---

## Enviar un Job

```php
SendInvoiceJob::dispatch($invoice);
```

Ese Job se guarda en la cola.

No se ejecuta todavía.

---

## Ejecutar las colas

### Worker normal

```bash
php artisan queue:work
```

El worker queda escuchando permanentemente.

```
Job agregado
      │
      ▼
queue:work detecta el Job
      │
      ▼
handle()
```

### Escuchar continuamente

```bash
php artisan queue:listen
```

Es similar a `queue:work`, pero reinicia Laravel en cada Job.

Es mucho más lento.

Actualmente se recomienda `queue:work`.

### Procesar un único Job

```bash
php artisan queue:work --once
```

Procesa:

```
Job 1
↓
Finaliza
```

### Procesar durante un tiempo

```bash
php artisan queue:work --stop-when-empty
```

Procesa todos los Jobs pendientes y termina.

Muy útil en Docker.

### Reiniciar Workers

Cuando modificas un Job:

```bash
php artisan queue:restart
```

Los workers actuales terminan el Job que están ejecutando y vuelven a iniciarse.

---

## Ejecutar Jobs inmediatamente

### Driver sync

En `.env`:

```
QUEUE_CONNECTION=sync
```

Ahora:

```php
SendInvoiceJob::dispatch($invoice);
```

no entra en una cola.

Laravel ejecuta:

```
dispatch()
│
▼
handle()
│
▼
Fin
```

Todo ocurre durante la petición HTTP.

Ideal para desarrollo.

### dispatchSync()

```php
SendInvoiceJob::dispatchSync($invoice);
```

Ignora completamente la cola.

Siempre ejecuta inmediatamente.

---

## Colas pesadas

Cuando hablamos de colas pesadas, normalmente nos referimos a Jobs que consumen mucho tiempo, CPU o memoria.

Ejemplos:

- Importar un Excel de 500 MB.
- Generar 10 000 PDFs.
- Procesar videos.
- Enviar un millón de correos.
- Procesar IA.
- Generar reportes enormes.

Estas tareas pueden durar varios minutos.

**¿Por qué son un problema?**

Supongamos:

```
Job A

↓

40 minutos
```

Mientras tanto:

```
Job B
Job C
Job D
```

Todos esperan.

Esto genera un cuello de botella.

### Solución 1: Colas separadas

En lugar de una sola cola `default`, crear varias.

```
default
```
```
emails
```
```
reports
```
```
heavy
```
```
images
```

Al despachar:

```php
GeneratePdfJob::dispatch()->onQueue('reports');
SendEmailJob::dispatch()->onQueue('emails');
ProcessVideoJob::dispatch()->onQueue('heavy');
```

Ahora cada worker puede escuchar una cola distinta.

Ejecutar un worker para una cola específica:

```bash
php artisan queue:work --queue=emails
```

Otro:

```bash
php artisan queue:work --queue=reports
```

Otro:

```bash
php artisan queue:work --queue=heavy
```

Así una tarea pesada no bloquea el resto.

### Prioridad de colas

Puedes indicar varias colas:

```bash
php artisan queue:work --queue=high,default,low
```

Laravel siempre intentará procesar:

```
high
│
▼
default
│
▼
low
```

### Múltiples Workers

Puedes ejecutar varios workers.

```
Worker 1   Worker 2   Worker 3   Worker 4
```

Todos consumen la misma cola.

Ejemplo:

```
Job 1 -> Worker 1
Job 2 -> Worker 2
Job 3 -> Worker 3
```

Esto mejora el rendimiento en servidores con varios núcleos.

### Configurar timeout

Si un Job tarda mucho:

```bash
php artisan queue:work --timeout=300
```

300 segundos.

También:

```php
public $timeout = 300;
```

Dentro del Job.

### Número de intentos

```php
public $tries = 5;
```

Si falla:

```
Intento 1
│
▼
Intento 2
│
▼
Intento 3
```

Hasta llegar al límite.

### Reintento automático

También puedes configurar:

```php
public function backoff()
{
    return [10, 30, 60];
}
```

Intentará nuevamente:

```
10 s
│
▼
30 s
│
▼
60 s
```

### Jobs fallidos

Laravel guarda los Jobs fallidos.

```bash
php artisan queue:failed
```

Verás algo similar a:

```
ID     Connection  Queue  Exception
```

### Reintentar un Job

```bash
php artisan queue:retry 5
```

O todos:

```bash
php artisan queue:retry all
```

### Eliminar Jobs fallidos

```bash
php artisan queue:flush
```

---

## Workers con cola heavy en producción

`heavy` no es una característica especial de Laravel. Es simplemente el nombre de una cola (igual que `emails`, `reports` o `default`). La "cola pesada" existe porque tú decides enviar determinados Jobs a esa cola y asignarle workers dedicados para aislar las tareas de larga duración.

Cuando despachas el Job:

```php
ProcessVideoJob::dispatch()->onQueue('heavy');
```

o desde el propio Job:

```php
public function __construct()
{
    $this->onQueue('heavy');
}
```

Ese Job quedará almacenado en la cola llamada `heavy`.

### Iniciar un worker para la cola heavy

```bash
php artisan queue:work --queue=heavy
```

Ese worker solo procesará los Jobs de la cola `heavy`.

### Tener varios workers al mismo tiempo

Es una práctica muy común tener varias terminales o procesos ejecutándose.

**Terminal 1:**
```bash
php artisan queue:work --queue=default
```
**Terminal 2:**
```bash
php artisan queue:work --queue=emails
```
**Terminal 3:**
```bash
php artisan queue:work --queue=heavy
```

Arquitectura:

```
                +----------------+
                |   Laravel App  |
                +-------+--------+
                        |
      +-----------------+-----------------+
      |                 |                 |
      ▼                 ▼                 ▼
 default           emails            heavy
      |                 |                 |
      ▼                 ▼                 ▼
 Worker 1          Worker 2          Worker 3
```

Así, si un `ProcessVideoJob` tarda 20 minutos, no bloqueará el envío de correos ni el procesamiento de los Jobs normales.

### Dar prioridad a varias colas

También puedes hacer que un mismo worker escuche varias colas en orden de prioridad:

```bash
php artisan queue:work --queue=high,default,heavy
```

Laravel intentará procesar en este orden:

```
high
│
▼
default
│
▼
heavy
```

Solo procesará un Job de `heavy` cuando no haya Jobs pendientes en `high` o `default`.

### Ejecutar varios workers para una cola pesada

Si los Jobs de `heavy` son muy demandantes, puedes iniciar varios workers sobre la misma cola:

```bash
php artisan queue:work --queue=heavy
php artisan queue:work --queue=heavy
php artisan queue:work --queue=heavy
```

Resultado:

```
                heavy
                  │
      ┌───────────┼───────────┐
      ▼           ▼           ▼
 Worker 1    Worker 2    Worker 3
      │           │           │
 Job A       Job B       Job C
```

Cada worker tomará un Job diferente, permitiendo procesar varias tareas pesadas en paralelo.

### En producción (Supervisor)

En un servidor Linux no se dejan abiertas varias terminales. Se configura Supervisor para mantener los workers siempre activos.

Ejemplo:

```ini
[program:laravel-heavy]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/app/artisan queue:work --queue=heavy --sleep=3 --tries=3 --timeout=600
numprocs=3
autostart=true
autorestart=true
```

Con esta configuración, Supervisor mantendrá 3 workers dedicados a la cola `heavy` y los reiniciará automáticamente si se detienen.

---

## Supervisar colas (Horizon)

Si usas Redis, Laravel ofrece Horizon, una herramienta para administrar y monitorear colas.

Con Horizon puedes:

- Ver Jobs en tiempo real.
- Saber cuáles fallaron.
- Medir tiempos de ejecución.
- Configurar múltiples workers.
- Balancear carga automáticamente.
- Reiniciar workers desde una interfaz web.

Es la opción recomendada para proyectos en producción con Redis.

---

## Buenas prácticas

- Usa `queue:work` en lugar de `queue:listen`.
- Utiliza `redis` en producción cuando sea posible.
- Separa las tareas pesadas en colas independientes (`heavy`, `reports`, etc.).
- Configura `timeout`, `tries` y `backoff` para Jobs largos o propensos a fallar.
- Mantén los Jobs enfocados en una única responsabilidad.
- Usa `dispatchSync()` solo cuando realmente necesites ejecutar un Job inmediatamente.
- En producción, ejecuta los workers con un administrador de procesos como Supervisor (Linux) o systemd para que se reinicien automáticamente si fallan.

---

## Resumen de comandos

| Comando                                      | Descripción                                        |
|----------------------------------------------|----------------------------------------------------|
| `php artisan make:job NombreJob`             | Crear un Job                                       |
| `dispatch()`                                 | Enviar un Job a la cola                            |
| `dispatchSync()`                             | Ejecutar un Job inmediatamente                     |
| `php artisan queue:work`                     | Iniciar un worker                                  |
| `php artisan queue:work --once`              | Procesar un solo Job                               |
| `php artisan queue:work --stop-when-empty`   | Procesar todos los Jobs pendientes y salir         |
| `php artisan queue:work --queue=heavy`       | Iniciar un worker para la cola heavy               |
| `php artisan queue:work --queue=high,default`| Worker con prioridad de colas                      |
| `php artisan queue:restart`                  | Reiniciar los workers                              |
| `php artisan queue:failed`                   | Listar Jobs fallidos                               |
| `php artisan queue:retry all`                | Reintentar todos los Jobs fallidos                 |
| `php artisan queue:flush`                    | Eliminar todos los Jobs fallidos                   |
| `php artisan horizon`                        | Iniciar Horizon (si está instalado)                |
