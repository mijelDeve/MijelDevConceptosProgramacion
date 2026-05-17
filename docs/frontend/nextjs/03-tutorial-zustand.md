# Tutorial: Zustand en Next.js

## ¿Qué es Zustand?

Zustand es una librería de estado global para React, minimalista y sin boilerplate. Es la opción más recomendada hoy para manejar estado de cliente en Next.js por su simplicidad y buen soporte con el App Router.

---

## Instalación

```bash
npm install zustand
```

---

## Conceptos básicos

Un **store** en Zustand es un hook que contiene:
- El **estado** (los datos)
- Las **acciones** (funciones que modifican el estado)

Todo en un solo lugar, sin reducers, sin actions, sin providers obligatorios.

---

## Ejemplo 1: Store simple (contador)

```ts
// store/counter.ts
import { create } from 'zustand';

interface CounterStore {
  count: number;
  increment: () => void;
  decrement: () => void;
  reset: () => void;
}

export const useCounterStore = create<CounterStore>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));
```

Usarlo en un componente:

```tsx
// components/Counter.tsx
'use client'; // ← obligatorio en Next.js App Router

import { useCounterStore } from '@/store/counter';

export default function Counter() {
  const { count, increment, decrement, reset } = useCounterStore();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

## Ejemplo 2: Carrito de compras (caso real)

```ts
// store/cart.ts
import { create } from 'zustand';

interface CartItem {
  id: string;
  name: string;
  price: number;
  quantity: number;
}

interface CartStore {
  items: CartItem[];
  addItem: (item: Omit<CartItem, 'quantity'>) => void;
  removeItem: (id: string) => void;
  updateQuantity: (id: string, quantity: number) => void;
  clearCart: () => void;
  total: () => number;
}

export const useCartStore = create<CartStore>((set, get) => ({
  items: [],

  addItem: (item) => set((state) => {
    const existing = state.items.find(i => i.id === item.id);
    if (existing) {
      // Si ya existe, aumenta la cantidad
      return {
        items: state.items.map(i =>
          i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
        ),
      };
    }
    return { items: [...state.items, { ...item, quantity: 1 }] };
  }),

  removeItem: (id) => set((state) => ({
    items: state.items.filter(i => i.id !== id),
  })),

  updateQuantity: (id, quantity) => set((state) => ({
    items: state.items.map(i =>
      i.id === id ? { ...i, quantity } : i
    ),
  })),

  clearCart: () => set({ items: [] }),

  // get() accede al estado actual desde dentro de una acción
  total: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),
}));
```

Usarlo en componentes:

```tsx
// components/AddToCartButton.tsx
'use client';

import { useCartStore } from '@/store/cart';

export default function AddToCartButton({ product }) {
  const addItem = useCartStore((state) => state.addItem); // selector ← más eficiente

  return (
    <button onClick={() => addItem(product)}>
      Agregar al carrito
    </button>
  );
}
```

```tsx
// components/CartSummary.tsx
'use client';

import { useCartStore } from '@/store/cart';

export default function CartSummary() {
  const items = useCartStore((state) => state.items);
  const total = useCartStore((state) => state.total);

  return (
    <div>
      <p>{items.length} productos</p>
      <p>Total: ${total()}</p>
    </div>
  );
}
```

---

## Selectores: la práctica más importante

En lugar de desestructurar todo el store, usa **selectores** para suscribirte solo a lo que necesitas. Esto evita re-renders innecesarios.

```tsx
// ❌ Malo: el componente re-renderiza si CUALQUIER cosa del store cambia
const { items, total, addItem, removeItem } = useCartStore();

// ✅ Bueno: solo re-renderiza si `items` cambia
const items = useCartStore((state) => state.items);
```

---

## Persistencia con localStorage

Zustand tiene middleware de persistencia incluido:

```ts
// store/theme.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface ThemeStore {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

export const useThemeStore = create<ThemeStore>()(
  persist(
    (set) => ({
      theme: 'light',
      toggleTheme: () =>
        set((state) => ({ theme: state.theme === 'light' ? 'dark' : 'light' })),
    }),
    {
      name: 'theme-storage', // key en localStorage
    }
  )
);
```

---

## Zustand con Next.js App Router: consideraciones

### ⚠️ El problema de hidratación

Si usas `persist`, puede haber un mismatch entre el HTML del servidor y el estado guardado en `localStorage`. La solución es montar el componente solo en el cliente:

```tsx
// components/ThemeToggle.tsx
'use client';

import { useState, useEffect } from 'react';
import { useThemeStore } from '@/store/theme';

export default function ThemeToggle() {
  const [mounted, setMounted] = useState(false);
  const { theme, toggleTheme } = useThemeStore();

  useEffect(() => setMounted(true), []);

  // Evita mismatch de hidratación
  if (!mounted) return null;

  return (
    <button onClick={toggleTheme}>
      Tema actual: {theme}
    </button>
  );
}
```

### ✅ Stores sin persistencia no tienen este problema

Si tu store no usa `persist`, no hay riesgo de hidratación. Puedes usarlo directamente.

---

## Estructura de archivos recomendada

```
src/
├── app/
│   └── ...
├── store/
│   ├── cart.ts        # Store del carrito
│   ├── auth.ts        # Store de autenticación del cliente
│   └── ui.ts          # Store de UI (modales, sidebars, etc.)
└── components/
    └── ...
```

---

## Resumen

| Concepto | Descripción |
|---|---|
| `create()` | Crea el store con estado y acciones |
| `set()` | Modifica el estado |
| `get()` | Lee el estado desde dentro de una acción |
| Selectores | Suscríbete solo a lo que necesitas para evitar re-renders |
| `persist` | Middleware para guardar en localStorage |
| `'use client'` | Obligatorio en todos los componentes que usen el store |
