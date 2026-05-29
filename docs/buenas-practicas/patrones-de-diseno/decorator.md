# Decorator

## Propósito

Permite añadir nuevas responsabilidades a un objeto de forma dinámica, sin modificar su estructura original.

## Estructura

```
Componente        → Interfaz común
ComponenteConcreto → Implementación base
DecoradorBase     → Envuelve un componente y delega
DecoradoresConcretos → Añaden comportamiento antes/después de delegar
```

## Ejemplo

```java
interface Notifier {
    void send(String message);
}

class EmailNotifier implements Notifier {
    public void send(String message) {
        System.out.println("Enviando email: " + message);
    }
}

// Decorador
abstract class NotifierDecorator implements Notifier {
    protected Notifier wrapped;

    public NotifierDecorator(Notifier wrapped) {
        this.wrapped = wrapped;
    }
}

class SMSNotifierDecorator extends NotifierDecorator {
    public SMSNotifierDecorator(Notifier wrapped) { super(wrapped); }

    public void send(String message) {
        wrapped.send(message);  // Envía email
        System.out.println("Enviando SMS: " + message);  // + SMS
    }
}

// Uso
Notifier notifier = new SMSNotifierDecorator(new EmailNotifier());
notifier.send("Hola");  // Envía email y SMS
```

## Cuándo Usarlo

- Cuando necesitas añadir funcionalidades a objetos de forma dinámica.
- Para evitar clases con muchas combinaciones de herencia.
- Cuando quieres añadir comportamientos como logging, caché, o control de acceso.
