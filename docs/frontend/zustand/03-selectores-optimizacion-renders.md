# Selectores y Optimización de Renders en Zustand

---

## ¿Qué es un Selector?

Un selector es una función que le dices a Zustand:

> "Solo quiero esta parte del estado, no todo".

En lugar de traer todo el store, traes solo lo que necesitas.

---

## Ejemplo Básico

Imagina este store:

```tsx
const useBearStore = create((set) => ({
  bears: 5,
  fish: 10,
  birds: 3,
  user: { name: "Carlos", age: 28 },
  settings: { darkMode: true, language: "es" }
}))
```

**Sin selector (mala práctica):**

```tsx
const store = useBearStore()           // ❌ Trae TODO el estado
console.log(store.bears)
```

Esto hace que cualquier cambio en el store (aunque sea `fish` o `settings`) provoque que este componente se vuelva a renderizar.

**Con selector (buena práctica):**

```tsx
const bears = useBearStore((state) => state.bears)           // ✅ Solo traigo bears
const userName = useBearStore((state) => state.user.name)
```

> **Ventaja:** Este componente solo se re-renderiza cuando `bears` o `user.name` cambian.

---

## ¿Por qué es tan importante la optimización?

- En aplicaciones grandes, un componente puede depender de solo 1 o 2 datos.
- Si no usas selectores, cualquier cambio en el store provoca renders innecesarios → app más lenta.
- Los selectores ayudan a React a ser más eficiente.

---

## Ejemplos Prácticos

### 1. Selector simple

```tsx
function BearCounter() {
  const bears = useBearStore((state) => state.bears)     // solo bears
  const increment = useBearStore((state) => state.increment)
  
  return <h1>Osos: {bears}</h1>
}
```

### 2. Selector con objeto (cuidado)

```tsx
// ❌ Peligroso
const user = useBearStore((state) => state.user)

// ✅ Mejor
const userName = useBearStore((state) => state.user.name)
const userAge = useBearStore((state) => state.user.age)
```

¿Por qué es malo el primero? Aunque solo cambie `user.name`, se crea un nuevo objeto cada vez → React piensa que cambió todo el `user`.

### 3. Solución Avanzada: `useShallow`

Zustand incluye una herramienta llamada `useShallow` para seleccionar múltiples valores de forma segura.

```tsx
import { useShallow } from 'zustand/shallow'

function Profile() {
  const { name, age, city } = useBearStore(
    useShallow((state) => ({
      name: state.user.name,
      age: state.user.age,
      city: state.user.address?.city
    }))
  )

  // Este componente solo se re-renderiza si name, age o city cambian
}
```

`useShallow` compara los valores de forma superficial y evita renders innecesarios cuando las referencias cambian.

---

## Reglas Prácticas

- **Siempre usa selectores:** `(state) => state.algo`
- Si solo necesitas un **valor primitivo** (string, number, boolean) → selector normal.
- Si necesitas **varios valores** → usa `useShallow`.
- **Evita seleccionar objetos completos** si no es necesario.
- Puedes seleccionar también **acciones** (funciones):

```tsx
const increment = useBearStore((state) => state.increment)
```

---

## Resumen Final

| Concepto | Descripción |
|----------|-------------|
| Selector | Función que extrae solo la parte del estado que necesitas |
| Objetivo | Evitar re-renders innecesarios → mejor rendimiento |
| Herramienta recomendada | `useShallow` cuando seleccionas varios datos |
