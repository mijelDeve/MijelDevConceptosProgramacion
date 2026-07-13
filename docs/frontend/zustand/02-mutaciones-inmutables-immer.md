# Mutaciones Inmutables en Zustand + Immer

---

## 1. ¿Qué es la Inmutabilidad?

**Inmutabilidad** = No modificar directamente los datos existentes. En su lugar, crear una copia nueva con los cambios.

En React y Zustand es obligatorio actualizar el estado de forma inmutable.

**Ejemplo mutable (incorrecto):**

```js
state.count = 5          // ❌ Malo
state.user.name = "Ana"  // ❌ Malo
```

**Ejemplo inmutable (correcto):**

```js
const newState = {
  ...state,
  count: 5,
  user: { ...state.user, name: "Ana" }
}
```

---

## 2. ¿Por qué es importante?

React compara el estado anterior y el nuevo usando `===` (comparación por referencia).

- Si mutas el objeto directamente, React **no detecta** el cambio → no hay re-render.
- Al crear un nuevo objeto/array, React detecta el cambio correctamente.

---

## 3. Cómo actualizar estado en Zustand

### Opción A: Sin Immer (manual)

Usas el operador spread (`...`) para copiar todo.

### Opción B: Con Immer

Escribes código como si mutaras el estado, pero Immer genera automáticamente la versión inmutable.

---

## Comparación Práctica

### Ejemplo: Aumentar cantidad en un carrito

**Sin Immer (versión manual):**

```ts
increaseQuantity: (id) => 
  set((state) => ({
    ...state,                                 // copiar todo
    items: state.items.map(item => 
      item.id === id 
        ? { ...item, quantity: item.quantity + 1 } 
        : item
    ),
    total: state.total + (state.items.find(i => i.id === id)?.price || 0)
  }))
```

**Con Immer (recomendado):**

```ts
increaseQuantity: (id) => 
  set((state) => {
    const item = state.items.find(i => i.id === id)
    if (item) {
      item.quantity += 1      // ← parece mutable
      state.total += item.price
    }
  })
```

---

## Ventajas y Desventajas

| Aspecto | Sin Immer | Con Immer |
|---------|-----------|-----------|
| Legibilidad | Baja | Muy alta |
| Código | Largo y repetitivo | Corto y natural |
| Errores | Fácil olvidar copiar algo | Muy difícil equivocarse |
| Estados anidados | Difícil | Muy fácil |
| Rendimiento | Igual | Igual |
| Cuándo usarlo | Estados simples | Estados complejos (recomendado) |

---

## Cómo usar Immer en Zustand

### Instalar

```bash
npm install immer
```

### Importar y usar el middleware

```tsx
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

const useStore = create(
  immer((set) => ({
    // estado...
    
    miAccion: () => set((state) => {
      // Aquí puedes "mutar" libremente:
      state.usuario.nombre = "Nuevo Nombre"
      state.carrito[0].cantidad = 5
      state.items.push(nuevoItem)
    })
  }))
)
```

---

## Resumen Final

- Siempre debes actualizar el estado de forma **inmutable** en Zustand/React.
- Para estados **simples** → puedes usar spreads normales.
- Para estados **anidados o complejos** → usa Immer. Te ahorra mucho código y dolores de cabeza.
- Immer **no cambia** cómo funciona Zustand, solo hace más fácil escribir actualizaciones.
