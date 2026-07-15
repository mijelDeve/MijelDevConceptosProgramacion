# useShallow en Zustand

---

## ¿Qué es useShallow?

`useShallow` es una utilidad de Zustand que te ayuda a optimizar renders cuando seleccionas varios valores o objetos del store.

Es la solución al problema común de las referencias nuevas.

---

## El Problema que resuelve

Cuando haces esto:

```tsx
const { name, age, city } = useBearStore((state) => ({
  name: state.user.name,
  age: state.user.age,
  city: state.user.address.city
}))
```

Aunque solo cambie `name`, Zustand crea un **nuevo objeto** cada vez que el store se actualiza. React ve que la referencia del objeto cambió y re-renderiza el componente aunque los valores reales no hayan cambiado.

Esto causa renders innecesarios.

---

## ¿Qué hace useShallow?

`useShallow` compara los valores de forma superficial (`shallow` = poco profunda) y solo permite un re-render si **realmente** cambió alguno de los valores seleccionados.

---

## Ejemplo Comparativo

### Sin useShallow (puede causar renders innecesarios)

```tsx
function UserProfile() {
  const userData = useBearStore((state) => ({
    name: state.user.name,
    age: state.user.age,
    city: state.user.address.city
  }))

  console.log("Componente re-renderizado") // ← Se ejecuta demasiado
  return <div>{userData.name} - {userData.city}</div>
}
```

### Con useShallow (recomendado)

```tsx
import { useShallow } from 'zustand/shallow'

function UserProfile() {
  const { name, age, city } = useBearStore(
    useShallow((state) => ({
      name: state.user.name,
      age: state.user.age,
      city: state.user.address.city
    }))
  )

  console.log("Componente re-renderizado") // ← Solo cuando realmente cambie algo
  return <div>{name} - {city}</div>
}
```

---

## ¿Cuándo debes usar useShallow?

### Úsalo cuando:

- Seleccionas **más de un valor** del store.
- Seleccionas **objetos o arrays**.
- Quieres evitar renders innecesarios.

### No es necesario cuando:

- Solo seleccionas un **valor primitivo** (number, string, boolean).

```tsx
// No necesitas useShallow aquí
const count = useStore(state => state.count)
```

---

## Ejemplo más completo (Carrito)

```tsx
import { useShallow } from 'zustand/shallow'

function CartSummary() {
  const { total, itemCount, discountCode } = useCartStore(
    useShallow((state) => ({
      total: state.total,
      itemCount: state.items.length,
      discountCode: state.discountCode
    }))
  )

  return (
    <div>
      <p>Total: ${total}</p>
      <p>Artículos: {itemCount}</p>
      {discountCode && <p>Código: {discountCode}</p>}
    </div>
  )
}
```

Este componente solo se re-renderiza si cambia `total`, `itemCount` o `discountCode`.

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| `useShallow` | Comparador superficial que evita renders innecesarios |
| Se usa dentro de | `useStore()` o `useBearStore()` |
| Caso de uso ideal | Cuando devuelves un objeto con varios datos |
| Beneficio | Mejora significativamente el rendimiento en componentes medianos/grandes |
| Importación | `import { useShallow } from 'zustand/shallow'` |
