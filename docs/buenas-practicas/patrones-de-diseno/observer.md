# Observer

## Propósito

Permite que un objeto (el "sujeto") notifique automáticamente a otros ("observadores") sobre cambios en su estado.

## Estructura

```
Sujeto        → Mantiene lista de observadores, define métodos para agregar/eliminar/notificar
Observador    → Interfaz con método de actualización
Observadores  → Implementaciones concretas que reaccionan a las notificaciones
```

## Ejemplo

```java
interface Observer {
    void update(String event);
}

class Subject {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer o) { observers.add(o); }
    public void notifyObservers(String event) {
        for (Observer o : observers) {
            o.update(event);
        }
    }
}
```

## Aplicaciones Comunes

- Sistemas de eventos (event listeners, event emitters).
- Publicación/suscripción (Pub/Sub).
- Notificaciones en tiempo real.
- Actualización de UI cuando cambian los datos.

## Cuándo Usarlo

- Cuando un cambio en un objeto requiere actualizar otros sin acoplarlos.
- Cuando no sabes de antemano cuántos objetos necesitan ser notificados.
