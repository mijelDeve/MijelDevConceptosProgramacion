# Chain of Responsibility (Cadena de Responsabilidad)

## Propósito

Da a múltiples objetos la oportunidad de procesar una petición. Cada "eslabón" decide si procesa la petición o la pasa al siguiente.

## Ejemplo en Middleware

```php
class RequestHandler {
    private $handlers = [];

    public function addHandler(callable $handler) {
        $this->handlers[] = $handler;
    }

    public function handle($request) {
        foreach ($this->handlers as $handler) {
            $result = $handler($request);
            if ($result !== null) return $result;
        }
        return null;
    }
}

// Uso
$handler = new RequestHandler();
$handler->addHandler(new AuthMiddleware());
$handler->addHandler(new ValidationMiddleware());
$handler->addHandler(new LoggingMiddleware());
$response = $handler->handle($request);
```

## Aplicaciones Comunes

- Middleware en frameworks web (Laravel, Express, ASP.NET).
- Filtros de autenticación, logging, validación.
- Pipe de procesamiento de datos.
- Manejo de eventos encadenados.

## Cuándo Usarlo

- Cuando múltiples objetos pueden procesar una misma petición.
- Cuando el orden de procesamiento es importante.
- Cuando quieres añadir o quitar pasos de procesamiento dinámicamente.
