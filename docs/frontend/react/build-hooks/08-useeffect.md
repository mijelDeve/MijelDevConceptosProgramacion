# useEffect — Guía Completa

## 1. useEffect sin array de dependencias

```jsx
useEffect(() => {
  console.log('Este efecto se ejecuta...');
});
```

### Comportamiento

Se ejecuta **después de cada render** del componente (incluyendo el primer render). Es equivalente a combinar `componentDidMount` + `componentDidUpdate` en clases.

### ¿Cuándo se usa?

**Casi nunca se recomienda.** Suele causar bucles infinitos si dentro del efecto haces un `setState`.

---

## 2. useEffect con array de dependencias vacío `[]`

```jsx
useEffect(() => {
  console.log('Este efecto se ejecuta solo una vez');

  // Ejemplo típico: fetch inicial, suscripciones, etc.
  fetchData();
}, []);   // ← Array vacío
```

### Comportamiento

Se ejecuta **solo una vez**, después del primer render (montaje). Es el equivalente a `componentDidMount` en componentes de clase.

### Usos más comunes

- Fetch de datos inicial
- Configurar event listeners, timers, WebSockets
- Inicializar librerías de terceros (mapas, charts, etc.)

### Cleanup (muy importante)

```jsx
useEffect(() => {
  const interval = setInterval(() => {}, 1000);

  return () => {
    clearInterval(interval);   // Cleanup al desmontar
  };
}, []);
```

---

## 3. useEffect con dependencias (array con variables)

```jsx
useEffect(() => {
  console.log('Se ejecuta cuando cambia userId o searchTerm');
  fetchUser(userId);
}, [userId, searchTerm]);   // ← Dependencias
```

### Comportamiento

- Se ejecuta después del **primer render**.
- Se vuelve a ejecutar cada vez que **cambia alguna de las dependencias** (comparación con `Object.is`).
- React compara las dependencias entre renders.

### Ejemplo práctico

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setUser);
  }, [userId]);   // ← Se vuelve a ejecutar si cambia userId

  return <div>{user?.name}</div>;
}
```

---

## 4. Resumen Comparativo

| Caso | Cuándo se ejecuta | Equivalente en Clases | Riesgo común | Recomendado |
|---|---|---|---|---|
| **Sin array** (`useEffect(() => {})`) | Cada render | `componentDidMount` + `DidUpdate` | Bucles infinitos | Casi nunca |
| **Array vacío** `[]` | Solo una vez (montaje) | `componentDidMount` | — | Muy común |
| **[dep1, dep2]** | Al montar + cuando cambian deps | `componentDidMount` + `DidUpdate` | *Stale closures* | Más común |

---

## 5. Reglas de oro

- **Nunca omitas** el array de dependencias (a menos que sepas muy bien lo que haces).
- Usa la regla de ESLint `exhaustive-deps` (te ayuda a detectar errores).
- Si usas una variable o función dentro del `useEffect`, debe estar en el array de dependencias (o usar `useCallback` / `useMemo`).
- Para evitar *stale closures* → usa *functional updates* o `useRef`.

---

## 6. Pregunta extra común en entrevistas

**¿Qué pasa si pones una función u objeto directamente en las dependencias?**

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  // ...
}, [someFunction]); // ← Mala práctica
```

→ Como la función se recrea en cada render, el efecto se ejecutará **en cada render**.

**Solución:** Envolver la función en `useCallback`.
