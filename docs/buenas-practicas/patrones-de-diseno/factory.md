# Factory

## Propósito

Crear y devolver diferentes tipos de objetos o componentes basándose en una condición o tipo de entrada.

## Ejemplo Backend

```java
interface Notification {
    void send(String message);
}

class EmailNotification implements Notification { ... }
class SMSNotification implements Notification { ... }

class NotificationFactory {
    public static Notification create(String type) {
        return switch (type) {
            case "email" -> new EmailNotification();
            case "sms"   -> new SMSNotification();
            default -> throw new IllegalArgumentException();
        };
    }
}
```

## Ejemplo React

```jsx
function Button({ variant, children }) {
    const variants = {
        primary:   <button className="btn-primary">{children}</button>,
        secondary: <button className="btn-secondary">{children}</button>,
        danger:    <button className="btn-danger">{children}</button>,
    };

    return variants[variant] ?? variants.primary;
}
```

## Cuándo Usarlo

- Cuando la creación de objetos es compleja o condicional.
- Cuando quieres centralizar la lógica de creación.
- Para ocultar la lógica de instanciación al cliente.
