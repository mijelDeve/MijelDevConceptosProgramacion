# useDebounce

## 1. ¿Qué es useDebounce y para qué sirve?

**Debounce** es una técnica que retarda la ejecución de una función hasta que haya pasado un cierto tiempo sin que se vuelva a llamar.

### Uso más común

- Búsquedas en tiempo real (*search*)
- Validaciones de formularios mientras el usuario escribe
- Llamadas a APIs mientras se escribe (evitar *spam* de requests)
- Actualizar filtros, etc.

---

## 2. Implementación Profesional de useDebounce

```tsx
// hooks/useDebounce.ts
import { useState, useEffect } from 'react';

export function useDebounce<T>(value: T, delay: number = 500): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    // Configuramos un timer
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // Cleanup: si el valor cambia antes de que pase el delay, cancelamos el timer anterior
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}
```

---

## 3. Ejemplo Real: Búsqueda con Debounce + TanStack Query

```tsx
// components/ProductSearch.tsx
import { useState } from 'react';
import { useQuery } from '@tanstack/react-query';
import { useDebounce } from '@/hooks/useDebounce';

export default function ProductSearch() {
  const [searchTerm, setSearchTerm] = useState('');

  // Aquí aplicamos el debounce
  const debouncedSearchTerm = useDebounce(searchTerm, 600); // 600ms

  const { data, isLoading } = useQuery({
    queryKey: ['products', debouncedSearchTerm],
    queryFn: () => fetchProducts(debouncedSearchTerm),
    enabled: debouncedSearchTerm.length > 0, // Solo busca si hay texto
  });

  return (
    <div>
      <input
        type="text"
        placeholder="Buscar productos..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        className="search-input"
      />

      {isLoading && <p>Buscando...</p>}

      <div>
        {data?.map((product: any) => (
          <div key={product.id}>
            <h4>{product.title}</h4>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 4. Versión Mejorada: useDebounceCallback

A veces también es útil tener una versión que debouncee una **función** en lugar de un valor:

```tsx
// hooks/useDebounceCallback.ts
import { useCallback, useRef, useEffect } from 'react';

export function useDebounceCallback<T extends (...args: any[]) => void>(
  callback: T,
  delay: number = 500
) {
  const timeoutRef = useRef<NodeJS.Timeout | null>(null);

  const debouncedCallback = useCallback(
    (...args: Parameters<T>) => {
      if (timeoutRef.current) {
        clearTimeout(timeoutRef.current);
      }

      timeoutRef.current = setTimeout(() => {
        callback(...args);
      }, delay);
    },
    [callback, delay]
  );

  // Cleanup
  useEffect(() => {
    return () => {
      if (timeoutRef.current) {
        clearTimeout(timeoutRef.current);
      }
    };
  }, []);

  return debouncedCallback;
}
```

---

## 5. ¿Cuándo usar useDebounce?

### ✅ Sí usar

- Input de búsqueda
- Filtros en tiempo real
- Autocompletado
- Validación de formularios mientras escribes

### ❌ No usar

- Cuando necesitas respuesta inmediata (ej: validación de contraseña en tiempo real)
- En animaciones o interacciones que requieren *feedback* instantáneo
