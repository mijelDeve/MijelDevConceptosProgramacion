# Estado en Next.js

## ¿Es necesario un estado global?

La respuesta corta es: **depende de qué tipo de estado necesitas compartir**. Antes de elegir una solución, conviene distinguir entre dos categorías de estado.

---

## Dos tipos de estado

### 1. Estado del servidor
Datos que vienen de tu backend: usuarios, productos, sesiones, etc.

### 2. Estado del cliente
UI state: modales abiertos, tema claro/oscuro, carrito temporal, preferencias locales.

> Mezclarlos es el error más común y lleva a soluciones sobredimensionadas.

---

## Estado del servidor: no uses un store global

Next.js con App Router tiene una solución nativa superior a cualquier store global para datos del backend.

### Herramientas nativas de Next.js

- **React Server Components + fetch con caché** → los datos fluyen del servidor al cliente sin stores
- **`cache()` de React** → deduplica requests dentro del mismo render
- **`revalidatePath` / `revalidateTag`** → invalidas caché cuando mutan datos

### Ejemplo

```ts
// app/products/page.tsx — sin estado global, sin useEffect
async function getProducts() {
  const res = await fetch('https://tu-api/products', {
    next: { revalidate: 60 } // ISR: revalida cada 60s
  });
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts(); // simple, tipado, cacheado
  return <ProductList products={products} />;
}
```

Para mutaciones, los **Server Actions** actualizan y revalidan datos en un solo paso, sin ningún store.

---

## Estado del cliente: cuándo sí tiene sentido un store

Casos donde un estado global de cliente tiene sentido real:

- Carrito de compras (antes de persistir en backend)
- Tema claro/oscuro
- Usuario autenticado visible en el cliente
- UI state complejo que muchos componentes necesitan al mismo tiempo

### Opciones recomendadas

| Opción | Cuándo usarla |
|---|---|
| **Zustand** | La más recomendada hoy. Simple, ligera, sin boilerplate |
| **Jotai** | Ideal si prefieres estado atómico (estilo Recoil pero mejor) |
| **Context API** | Solo para estado que cambia poco (tema, auth básico) |
| **Redux Toolkit** | Solo si ya lo tienes o el equipo lo conoce bien. Overkill para la mayoría |

---

## La arquitectura que funciona bien

```
Server Components  →  traen datos del backend (sin store)
       ↓
Client Components  →  reciben datos como props, manejan UI state local
       ↓
Zustand / Jotai    →  solo para estado de cliente que cruza múltiples árboles
```

---

## Resumen

| Situación | Solución |
|---|---|
| Dato que viene del servidor | `fetch` en Server Component |
| UI state local a un componente | `useState` |
| UI state compartido entre muchas partes | Zustand o Jotai |
| Estado global complejo con historial | Redux Toolkit (solo si es necesario) |
