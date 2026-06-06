# TanStack Query

## 1. ¿Qué es TanStack Query?

TanStack Query es una librería de manejo de **estado asíncrono** (especialmente para datos del servidor).

No es un reemplazo de Zustand o Redux. Es un **complemento** especializado en:

- Fetching de datos
- Caching (guardar en caché)
- Sincronización automática
- Manejo de *loading*, *error*, *refetching*, etc.

---

## 2. Server State vs Client State

TanStack Query no es un gestor de estado como Redux o Zustand. Su especialidad es manejar el **Server State** (datos que vienen del servidor).

| Tipo de Estado | Qué es | Dónde se maneja mejor | Ejemplos |
|---|---|---|---|
| **Client State** | Estado de la UI | Zustand / Context / useState | Tema dark/light, formulario abierto, paso del wizard |
| **Server State** | Datos que vienen del backend | **TanStack Query** | Lista de productos, usuario actual, posts, órdenes |

TanStack Query actúa como una **capa de caché inteligente** entre tu frontend y el servidor.

---

## 3. Problema que resuelve

Sin TanStack Query, cuando haces `fetch` en React tienes que manejar manualmente:

- *Loading state*
- *Error state*
- Caché (para no volver a pedir los mismos datos)
- Refetch cuando cambias de página o actualizas
- Invalidación de caché
- Retries automáticos
- *Background refetching*

TanStack Query hace todo eso por ti de forma automática y muy eficiente.

---

## 4. Conceptos principales

| Concepto | Qué es | Hook principal |
|---|---|---|
| **Query** | Petición de datos (GET) | `useQuery` |
| **Mutation** | Petición que modifica datos (POST, PUT, DELETE) | `useMutation` |
| **QueryClient** | El "cerebro" que maneja toda la caché | — |
| **QueryProvider** | Proveedor que envuelve tu app | `<QueryClientProvider>` |

---

## 5. Instalación

```bash
npm install @tanstack/react-query
# o
yarn add @tanstack/react-query
```

---

## 6. Configuración básica (QueryClientProvider)

```tsx
// main.tsx o index.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5,   // 5 minutos
      gcTime: 1000 * 60 * 10,     // 10 minutos (antes cacheTime)
      retry: 3,
    },
  },
});

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <TuAplicacion />
    </QueryClientProvider>
  );
}
```

---

## 7. useQuery — Flujo completo

`useQuery` es el hook principal para obtener datos (GET).

### Paso a paso

1. Componente se monta → `useQuery` se ejecuta.
2. TanStack Query mira la `queryKey` (ej: `['products', 'electronics']`).
3. Si ya existe en caché y está fresco → devuelve los datos inmediatamente (sin ir al servidor).
4. Si no existe o está *stale* (obsoleto) → ejecuta la `queryFn` (el fetch).
5. Guarda el resultado en la caché interna del `QueryClient`.
6. El componente recibe `{ data, isLoading, error, isFetching... }`.
7. Si otro componente pide la misma `queryKey`, **comparte la misma caché** (no hace fetch duplicado).

> **Importante:** No necesitas crear un "store" manual. El `QueryClient` es el store global de caché, y las `queryKey` son las claves que identifican cada dato.

### Ejemplo básico

```tsx
import { useQuery } from '@tanstack/react-query';

function ProductsList() {
  const { data, isLoading, error, isFetching } = useQuery({
    queryKey: ['products'],           // ← Clave única para esta query
    queryFn: async () => {
      const res = await fetch('https://fakestoreapi.com/products');
      return res.json();
    },
  });

  if (isLoading) return <p>Cargando...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      {data?.map(product => <p key={product.id}>{product.title}</p>)}
      {isFetching && <p>Actualizando en segundo plano...</p>}
    </div>
  );
}
```

### Valores que devuelve `useQuery`

- `data`: los datos (undefined mientras carga)
- `isLoading`: `true` solo la primera vez
- `isFetching`: `true` cuando está haciendo cualquier petición (incluso en background)
- `error`: el error si falló
- `refetch`: función para volver a pedir los datos manualmente

---

## 8. Dependencias (queryKey)

```tsx
// Query con parámetros
const { data } = useQuery({
  queryKey: ['products', category, searchTerm], // Cambia automáticamente
  queryFn: () => fetchProducts(category, searchTerm),
});
```

TanStack Query automáticamente vuelve a *fetchear* cuando cambia la `queryKey`.

---

## 9. useMutation

`useMutation` es para operaciones que **modifican datos** en el servidor (POST, PUT, DELETE, PATCH).

### Flujo típico de Mutation

1. Usuario hace clic en "Crear producto" o "Eliminar".
2. `useMutation` ejecuta la función que hace el fetch (POST/PUT/etc).
3. Mientras está cargando → `isPending = true`.
4. Cuando termina con éxito:
   - Puedes actualizar manualmente la caché (`queryClient.setQueryData`)
   - O **invalidar consultas** (`queryClient.invalidateQueries`) → hace que las queries afectadas se vuelvan a fetch automáticamente.

La UI se actualiza sola gracias a la invalidación.

### Ejemplo

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function NewProductForm() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newProduct) =>
      fetch('/api/products', {
        method: 'POST',
        body: JSON.stringify(newProduct)
      }),

    onSuccess: () => {
      // Invalidamos la caché para que se vuelva a pedir la lista
      queryClient.invalidateQueries({ queryKey: ['products'] });
    },
  });

  return (
    <button
      onClick={() => mutation.mutate({ title: 'Nuevo producto' })}
      disabled={mutation.isPending}
    >
      {mutation.isPending ? 'Creando...' : 'Crear producto'}
    </button>
  );
}
```

---

## 10. Mejores prácticas: Custom Hooks

En proyectos reales **no se recomienda** poner `useQuery` directamente dentro del componente. Lo ideal es extraer la lógica en un **custom hook**.

### Ejemplo correcto y profesional

```tsx
// hooks/queries/useProducts.ts
import { useQuery } from '@tanstack/react-query';

type Product = {
  id: number;
  title: string;
  price: number;
  category: string;
};

export const useProducts = (category?: string, search?: string) => {
  return useQuery({
    queryKey: ['products', category, search],     // clave inteligente
    queryFn: async () => {
      let url = 'https://fakestoreapi.com/products';
      const res = await fetch(url);
      return res.json() as Promise<Product[]>;
    },
    staleTime: 1000 * 60 * 5,   // 5 minutos
  });
};
```

Ahora el componente queda mucho más limpio:

```tsx
// components/ProductList.tsx
import { useProducts } from '@/hooks/queries/useProducts';

function ProductList({ category, search }: { category?: string; search?: string }) {
  const {
    data: products,
    isLoading,
    error,
    isFetching
  } = useProducts(category, search);

  if (isLoading) return <p>Cargando productos...</p>;
  if (error) return <p>Error al cargar productos</p>;

  return (
    <div>
      {products?.map(product => (
        <div key={product.id}>
          <h3>{product.title}</h3>
          <p>${product.price}</p>
        </div>
      ))}

      {isFetching && <p>Actualizando...</p>}
    </div>
  );
}
```

### Ventajas de usar Custom Hooks

| Beneficio | Explicación |
|---|---|
| **Reutilización** | Puedes usar `useProducts()` en varios componentes |
| **Separación de responsabilidades** | Lógica de datos separada de la UI |
| **Más fácil de testear** | Puedes testear el hook por separado |
| **Código más limpio** | Los componentes quedan más simples |
| **Mejor organización** | Todo el fetching de productos está en un solo archivo |

> **Regla recomendada:** Todos los `useQuery` y `useMutation` deberían estar dentro de custom hooks (en carpeta `hooks/queries/`). Los componentes solo consumen esos hooks.

---

## 11. Ejemplo práctico completo

### Custom hooks

```tsx
// hooks/queries/useProducts.ts
export const useProducts = () => {
  return useQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  });
};

// hooks/mutations/useCreateProduct.ts
export const useCreateProduct = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: createProductOnServer,

    onSuccess: () => {
      // Opción 1: Invalidar (re-fetchea automáticamente)
      queryClient.invalidateQueries({ queryKey: ['products'] });

      // Opción 2: Actualizar optimistamente (más avanzado)
      // queryClient.setQueryData(['products'], oldData => [...oldData, newProduct])
    }
  });
};
```

### Componente que los consume

```tsx
function ProductPage() {
  const { data: products } = useProducts();
  const createMutation = useCreateProduct();

  const handleCreate = () => {
    createMutation.mutate(newProductData);
  };

  return (
    <>
      {/* Lista actualizada automáticamente después de crear */}
      {products?.map(p => <ProductCard key={p.id} product={p} />)}

      <button onClick={handleCreate} disabled={createMutation.isPending}>
        Crear Producto
      </button>
    </>
  );
}
```

---

## 12. Resumen del Flujo General

```
Usuario → Componente → useQuery / useMutation
                        ↓
                  QueryClient (Caché global)
                        ↓
             queryKey identifica el dato
                        ↓
      ¿Datos frescos en caché? → Sí → Devuelve datos
                        ↓ No
                  Ejecuta queryFn o mutationFn
                        ↓
                  Actualiza caché
                        ↓
      Todos los componentes que usan esa queryKey se actualizan
```

---

## 13. ¿Por qué no se ve un "store" como en Zustand?

Porque no lo necesitas. TanStack Query usa una **caché interna basada en llaves** (`queryKey`). Es como un `Map` gigante:

```js
cache.set(['products', 'electronics'], { data: [...], timestamp: ... })
cache.set(['user', 123], { data: {...}, timestamp: ... })
```

Esto es mucho más potente y eficiente que un store tradicional para datos del servidor.

---

## 14. Ventajas más importantes

- **Caching automático** — no vuelve a pedir datos si ya los tiene recientes
- **Background refetching** — actualiza datos en segundo plano
- **Deduping** — múltiples componentes pidiendo lo mismo → solo una petición
- **Optimistic Updates** — actualizar UI antes de que responda el servidor
- **Infinite Queries y Pagination** muy fáciles
- **Devtools increíbles** — instala `@tanstack/react-query-devtools`

---

## 15. Comparación rápida

| Enfoque | Característica |
|---|---|
| `useState` + `fetch` | Control manual, mucho *boilerplate* |
| Zustand / Redux | Bueno para estado de UI, no ideal para datos del servidor |
| **TanStack Query** | Especializado en datos del servidor (el mejor en esto) |

---

## 16. Recomendación de Arquitectura

| Herramienta | Para qué usarla |
|---|---|
| **Zustand** | Estado de cliente (carrito temporal, UI, filtros locales, etc.) |
| **TanStack Query** | Todo lo que viene del backend |

A veces se combinan (ej: guardar en Zustand el usuario que viene de TanStack Query).
