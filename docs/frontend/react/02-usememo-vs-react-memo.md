# useMemo vs React.memo

## 1. React.memo (Memoización de componentes)

`React.memo` es un *Higher-Order Component (HOC)* que envuelve un componente funcional para evitar que se vuelva a renderizar si sus *props* no han cambiado (comparación superficial por defecto).

### ¿Cuándo usarlo?

- Cuando un componente es "puro" (mismos *props* → mismo *output*).
- Cuando el componente es caro de renderizar (listas grandes, gráficos, etc.).
- Especialmente útil cuando el padre se re-renderiza frecuentemente pero los *props* del hijo no cambian.

```jsx
import React from 'react';

// Componente memoizado
const ExpensiveList = React.memo(({ items, onSelect }) => {
  console.log('ExpensiveList se renderizó');

  return (
    <ul>
      {items.map(item => (
        <li key={item.id} onClick={() => onSelect(item.id)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
});

// Uso en padre
function Parent() {
  const [count, setCount] = useState(0);
  const items = useMemo(() => [...], []); // importante

  return (
    <>
      <button onClick={() => setCount(c => c + 1)}>
        Re-render padre: {count}
      </button>
      <ExpensiveList items={items} onSelect={handleSelect} />
    </>
  );
}
```

> **Importante:** Si pasas funciones u objetos *inline* como props (`onClick={() => ...}` o `style={{...}}`), `React.memo` no funcionará porque se crean nuevos en cada render. Ahí es donde entran `useCallback` y `useMemo`.

---

## 2. useMemo (Memoización de valores)

`useMemo` es un *hook* que memoiza el resultado de una computación cara. Solo vuelve a ejecutar la función si cambian las dependencias.

### ¿Cuándo usarlo?

- Cálculos pesados (filtrados, mapeos grandes, formateos).
- Crear objetos o arrays estables (para evitar re-renders innecesarios en hijos o en dependencias de `useEffect`).
- Evitar recrear referencias que rompan `React.memo` o `useEffect`.

```jsx
import React, { useMemo, useState } from 'react';

function ExpensiveComponent({ data }) {
  const filteredAndSorted = useMemo(() => {
    console.log('Cálculo pesado ejecutándose...');
    return data
      .filter(item => item.active)
      .sort((a, b) => a.price - b.price);
  }, [data]); // Solo se recalcula si 'data' cambia

  return <div>...</div>;
}
```

### Ejemplo más completo con objetos

```jsx
function UserProfile({ user }) {
  // Memoizamos el objeto de estilos o configuración
  const userStyles = useMemo(() => ({
    backgroundColor: user.theme === 'dark' ? '#333' : '#fff',
    color: user.theme === 'dark' ? '#fff' : '#000',
    padding: '20px'
  }), [user.theme]);

  // Memoizamos una función (mejor usar useCallback)
  const handleSave = useCallback(() => {
    saveUser(user);
  }, [user]);

  return <ExpensiveCard styles={userStyles} onSave={handleSave} />;
}
```

---

## 3. Comparación clave: useMemo vs React.memo

| Aspecto | React.memo | useMemo |
|---|---|---|
| **Propósito** | Memoizar componentes | Memoizar valores |
| **Nivel** | Componente completo | Expresión/dato dentro del componente |
| **Qué compara** | Props (*shallow comparison*) | Array de dependencias |
| **Uso típico** | Evitar re-renders de hijos | Evitar cálculos pesados |
| **Referencias** | Mantiene misma instancia del componente | Mantiene misma referencia del valor |
| **Performance** | Impacto mayor (evita render tree) | Impacto menor (solo evita cálculo) |

---

## 4. Recomendaciones

- **No abuses** de `React.memo` ni `useMemo` desde el principio. React es muy rápido. Úsalos cuando identifiques cuellos de botella (con *React DevTools Profiler*).
- **Combínalos frecuentemente:**
  - `React.memo` + `useMemo` (para props objetos/arrays)
  - `React.memo` + `useCallback` (para funciones)

### Regla de oro

- Si un componente recibe objetos/arrays/funciones como props → usa `useMemo`/`useCallback` en el padre.
- Si el componente es pesado y sus props son primitivos o estables → `React.memo`.

### Caso común que rompe todo

```jsx
// MALO (rompe React.memo)
<Child data={data.filter(...)} onClick={() => handleClick(id)} />

// BUENO
const filtered = useMemo(() => data.filter(...), [data]);
const handleClick = useCallback((id) => {...}, []);
<Child data={filtered} onClick={handleClick} />
```

---

## 5. Preguntas Frecuentes

### 1. ¿Cuándo se utiliza onSelect en un onClick, se vuelve a renderizar el componente?

Sí, pero depende de cómo esté implementado.

Si pasas una función nueva en cada render (*inline*):

```jsx
// ❌ Esto rompe React.memo
<ExpensiveList
  items={items}
  onSelect={(id) => handleSelect(id)}   // ← Nueva función cada render
/>
```

Aunque el componente esté envuelto en `React.memo`, se volverá a renderizar porque la prop `onSelect` cambia en cada render del padre (referencia diferente).

**Solución correcta:**

```jsx
const handleSelect = useCallback((id) => {
  console.log('Seleccionado:', id);
  // lógica...
}, []); // dependencias

<ExpensiveList items={items} onSelect={handleSelect} />
```

Con `useCallback`, la referencia de la función se mantiene estable → `React.memo` funciona correctamente.

### 2. ¿React.memo y useMemo siempre se utilizan en conjunto con useCallback?

No siempre, pero es muy común y recomendado en la mayoría de casos reales.

- `React.memo` → para componentes
- `useMemo` → para valores/objetos/arrays caros
- `useCallback` → para funciones (especialmente las que se pasan como props)

**Regla práctica:** Si usas `React.memo` en un hijo y le pasas funciones u objetos, casi siempre necesitarás `useCallback` + `useMemo` en el padre.

### 3. Explicación clara sobre onSelect / onClick

En el ejemplo anterior:

```jsx
const ExpensiveList = React.memo(({ items, onSelect }) => {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id} onClick={() => onSelect(item.id)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
});
```

**¿Qué pasa aquí?** `onSelect` es una prop que recibe el componente (debería ser estable gracias a `useCallback`). Dentro del `map`, creamos una *arrow function inline* `() => onSelect(item.id)` — esta función se crea nueva en cada render del `ExpensiveList`.

**¿Esto rompe el memo?** No rompe el memo del `ExpensiveList` (porque el render ya ocurrió), pero sí puede causar renders innecesarios en componentes más profundos si tuvieras más memoización dentro de la lista.

**Mejor práctica (optimizado):**

```jsx
const ExpensiveList = React.memo(({ items, onSelect }) => {
  return (
    <ul>
      {items.map(item => (
        <ListItem
          key={item.id}
          item={item}
          onSelect={onSelect}
        />
      ))}
    </ul>
  );
});

const ListItem = React.memo(({ item, onSelect }) => {
  const handleClick = useCallback(() => {
    onSelect(item.id);
  }, [onSelect, item.id]);

  return <li onClick={handleClick}>{item.name}</li>;
});
```

### 4. Caso de uso más común de useMemo

Los más frecuentes son:

- Filtrados y ordenamientos caros (el clásico).
- Crear objetos o arrays estables para pasar como props.
- Cálculos derivados de datos (totales, estadísticas, etc.).
- Prevenir renders en cadena cuando un valor se usa en dependencias de `useEffect` o como prop.

**Ejemplo muy común:**

```jsx
const filteredUsers = useMemo(() => {
  return users
    .filter(u => u.active && u.name.toLowerCase().includes(searchTerm))
    .sort((a, b) => a.name.localeCompare(b.name));
}, [users, searchTerm]);
```

**Otro caso muy usado:**

```jsx
// Evitar recrear configuración
const chartConfig = useMemo(() => ({
  data: processedData,
  options: { responsive: true, ... }
}), [processedData]);
```
