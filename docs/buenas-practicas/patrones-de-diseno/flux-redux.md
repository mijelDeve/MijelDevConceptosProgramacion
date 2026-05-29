# Observer / Flux (Redux, Zustand)

## Propósito

Un cambio de estado notifica a los componentes para que se actualicen. Es el principio en el corazón de bibliotecas de estado como Redux o Zustand.

## Flujo de Datos (Unidireccional)

```
Acción → Dispatcher → Store → Vista
                           ↓
                       (notifica)
```

## Ejemplo con Zustand

```jsx
import { create } from 'zustand';

const useStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
}));

function Counter() {
    const { count, increment } = useStore();
    return <button onClick={increment}>Clicks: {count}</button>;
}
```

## Cuándo Usarlo

- Estado global que muchos componentes necesitan compartir.
- Cuando el estado es complejo y pasa por varios niveles de componentes.
- Para evitar "prop drilling" (pasar props por componentes intermedios innecesariamente).
