# Tutorial completo de Zustand: Gestión de estado simple y potente en React

Zustand es una librería ligera, rápida y minimalista para manejar el estado en React (y también en entornos vanilla JS). Es conocida por su API basada en hooks, su bajo boilerplate y su excelente rendimiento. No requiere Providers ni reducers complejos como Redux.

---

## Instalación

```bash
npm install zustand
# o
yarn add zustand
# o
pnpm add zustand
```

> Versión actual aproximada: ~5.x (verifica en npm para la última).

---

## 1. Tu primer Store (Ejemplo básico: Contador)

Crea un archivo como `store.ts` (o `.js`):

```ts
import { create } from 'zustand'

interface CounterState {
  count: number
  increment: () => void
  decrement: () => void
  reset: () => void
}

export const useCounterStore = create<CounterState>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}))
```

**Explicación:**

- `create` recibe una función que devuelve el estado inicial + acciones.
- `set` actualiza el estado (hace merge shallow por defecto).
- Puedes usar el estado anterior dentro de `set` para actualizaciones seguras.

---

## 2. Uso en componentes

```tsx
import { useCounterStore } from './store'

function Counter() {
  // Seleccionamos solo lo que necesitamos (evita re-renders innecesarios)
  const count = useCounterStore((state) => state.count)
  const increment = useCounterStore((state) => state.increment)
  const decrement = useCounterStore((state) => state.decrement)

  return (
    <div>
      <h1>Contador: {count}</h1>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </div>
  )
}
```

¡Listo! No necesitas envolver tu app en ningún Provider.

---

## 3. Actualizaciones de estado avanzadas

### Actualizar objetos y estado anidado

```ts
const useBearStore = create((set) => ({
  bears: 0,
  fish: { count: 10, type: 'salmon' },
  
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  
  // Actualización inmersiva (recomendado para objetos anidados)
  eatFish: () => set((state) => ({
    fish: { ...state.fish, count: state.fish.count - 1 }
  })),
}))
```

### Usando Immer (muy recomendado para mutaciones "inmutables" fáciles)

```bash
npm install immer
```

```ts
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'  // middleware incluido

const useStore = create(
  immer((set) => ({
    bears: 0,
    addBear: () => set((state) => { state.bears += 1 }), // mutación directa
  }))
)
```

---

## 4. Selectores y optimización de renders

```ts
// Selector simple
const bears = useBearStore((state) => state.bears)

// Selector con shallow (evita re-renders cuando cambian referencias de objetos)
import { useShallow } from 'zustand/shallow'

const { bears, fish } = useBearStore(
  useShallow((state) => ({ bears: state.bears, fish: state.fish }))
)
```

---

## 5. Middlewares útiles

Zustand incluye middlewares potentes:

### Persistencia (localStorage)

```ts
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

const useStore = create(
  persist(
    (set) => ({
      bears: 0,
      increase: () => set((state) => ({ bears: state.bears + 1 })),
    }),
    {
      name: 'bears-storage', // clave en localStorage
      storage: createJSONStorage(() => localStorage),
    }
  )
)
```

### DevTools (Redux DevTools)

```ts
import { devtools } from 'zustand/middleware'

const useStore = create(
  devtools((set) => ({
    // ...
  }))
)
```

---

## 6. TypeScript (recomendado)

```ts
interface BearState {
  bears: number
  increase: () => void
}

export const useBearStore = create<BearState>()((set) => ({
  bears: 0,
  increase: () => set((state) => ({ bears: state.bears + 1 })),
}))
```

Puedes extraer tipos:

```ts
type BearStore = ReturnType<typeof useBearStore.getState>
```

---

## 7. Ejemplo práctico: Todo List

```ts
import { create } from 'zustand'

type Todo = { id: number; text: string; completed: boolean }

interface TodoStore {
  todos: Todo[]
  addTodo: (text: string) => void
  toggleTodo: (id: number) => void
  removeTodo: (id: number) => void
}

export const useTodoStore = create<TodoStore>((set) => ({
  todos: [],
  addTodo: (text) =>
    set((state) => ({
      todos: [...state.todos, { id: Date.now(), text, completed: false }],
    })),
  toggleTodo: (id) =>
    set((state) => ({
      todos: state.todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ),
    })),
  removeTodo: (id) =>
    set((state) => ({
      todos: state.todos.filter((todo) => todo.id !== id),
    })),
}))
```

---

## 8. Patrones avanzados

- **Slices Pattern**: Divide tu store grande en partes reutilizables.
- **createStore (vanilla)**: Para usar fuera de React.
- **Sincronización con URL, testing, inicialización con props**, etc. (ver docs oficiales).

---

## Ventajas de Zustand

| Ventaja | Descripción |
|---------|-------------|
| Muy ligero | ~1-2KB |
| Excelente rendimiento | Solo re-renderiza componentes que usan el estado seleccionado |
| Fácil de aprender y escalar | API minimalista |
| Compatible | React Server Components, Next.js, etc. |

---

## Recursos oficiales

- [Documentación](https://zustand.docs.pmnd.rs/)
- [Tutorial interactivo (Tic-Tac-Toe)](https://zustand-demo.pmnd.rs/)
- [Repositorio GitHub](https://github.com/pmndrs/zustand)
