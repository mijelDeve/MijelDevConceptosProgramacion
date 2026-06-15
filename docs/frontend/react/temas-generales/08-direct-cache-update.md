# Actualización Directa de Caché (Direct Cache Update)

En TanStack Query hay dos estrategias para mantener la caché actualizada después de una mutación:

- **Invalidación** (`invalidateQueries`) — marca los datos como obsoletos y deja que se refetcheen
- **Actualización directa** (`setQueryData` / `setQueriesData`) — escribes el dato actualizado directo en la caché, cero llamadas de red

---

## ¿Qué es?

La actualización directa de caché consiste en modificar manualmente el contenido de la caché de TanStack Query cuando ya sabes cómo se ve el dato actualizado.

No esperas a que el servidor responda para después volver a pedir los datos. Tú mismo pones el dato nuevo en la caché y la UI se actualiza al instante.

```typescript
queryClient.setQueryData(['products'], (oldData) => [...oldData, newProduct]);
```

---

## Invalidación vs Actualización Directa

| Estrategia | Cómo funciona | Viaja a red | Cuándo usarla |
|---|---|---|---|
| `invalidateQueries` | Marca la query como *stale* → se refetchea automáticamente | ✅ Sí (hace un GET) | No sabes exactamente qué cambió, o el cambio es complejo |
| `setQueryData` | Reemplazas el dato en caché manualmente | ❌ No | Sabes exactamente cómo queda el dato después de la mutación |

---

## `setQueryData`

Actualiza el dato de una query específica.

```typescript
import { useQueryClient } from '@tanstack/react-query';

const queryClient = useQueryClient();

// Después de crear un producto
queryClient.setQueryData(['products'], (oldData: Product[]) => {
  return [...oldData, newProduct];
});

// Después de actualizar un producto
queryClient.setQueryData(['products'], (oldData: Product[]) => {
  return oldData.map(p => p.id === updatedProduct.id ? updatedProduct : p);
});

// Después de eliminar un producto
queryClient.setQueryData(['products'], (oldData: Product[]) => {
  return oldData.filter(p => p.id !== deletedId);
});
```

El callback recibe los datos viejos y debe retornar los nuevos. Si la query no existe en caché, `oldData` será `undefined`.

### Con valor fijo

También puedes pasarle un valor directo en lugar de un callback:

```typescript
queryClient.setQueryData(['product', 5], { id: 5, title: 'Actualizado' });
```

---

## `setQueriesData`

Actualiza **todas las queries** que coincidan con un filtro. Útil cuando una mutación afecta varias listas.

```typescript
// Actualiza cualquier query que tenga ['products'] en su queryKey
queryClient.setQueriesData(
  { queryKey: ['products'] },
  (oldData: Product[]) => oldData?.filter(p => p.id !== deletedId)
);
```

El primer argumento es un filtro: pueden ser `queryKey`, `type: 'active'` / `type: 'inactive'`, etc.

---

## Write-through cache

Este patrón también se conoce como **Write-through cache**: la mutación escribe al servidor y, al confirmarse, escribe directo al caché local.

```
Cliente → [POST /api/products] → Servidor
                                → 201 OK (con el producto creado)
         ← setQueryData ← caché actualizada inmediatamente
```

```typescript
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: (newProduct) =>
    fetch('/api/products', {
      method: 'POST',
      body: JSON.stringify(newProduct),
    }).then(res => res.json()),

  onSuccess: (dataFromServer) => {
    // dataFromServer es el producto ya con el id asignado por el backend
    queryClient.setQueryData(['products'], (old) => [...(old ?? []), dataFromServer]);
  },
});
```

---

## Optimistic Update vs Direct Update

| Aspecto | Optimistic Update | Direct Cache Update |
|---|---|---|
| Cuándo se aplica | Antes de que responda el servidor | Después de que el servidor confirma |
| Riesgo | Si el server rechaza, hay que hacer rollback | Ninguno (el server ya aceptó) |
| Experiencia de usuario | Instantánea (sin esperar nada) | Instantánea (sin refetch) |
| Código | Más complejo (onMutate + onError + onSettled) | Simple (solo onSuccess) |

El **Direct Cache Update** es el punto intermedio ideal: no necesitas manejar rollback (porque el server ya aprobó), pero evitas el refetch innecesario.

---

## Cuándo usar cada uno

### Usa `invalidateQueries` cuando

- El server puede devolver datos diferentes a los que enviaste (ej: el server calcula campos)
- Varias queries pueden verse afectadas y no quieres rastrearlas manualmente
- El volumen de datos es bajo y el refetch es barato

### Usa `setQueryData` cuando

- Sabes exactamente cómo se ve el dato después de la mutación
- Quieres evitar una llamada GET adicional
- El servidor devuelve el objeto actualizado en la respuesta

---

## Resumen

| Concepto | Descripción |
|---|---|
| `invalidateQueries` | Marca como *stale* → refetch automático |
| `setQueryData` | Escribe directo en la caché de una query específica |
| `setQueriesData` | Escribe directo en múltiples queries que coincidan con un filtro |
| Write-through | Mutación escribe al server → al confirmar, escribe al caché local |
| Direct Cache Update | Alternativa a invalidación cuando sabes exactamente qué cambió |
