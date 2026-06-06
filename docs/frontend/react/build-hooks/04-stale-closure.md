# Stale Closure

## 1. ¿Qué es el "Stale Closure"?

Es cuando una función (closure) dentro de un hook captura una **versión antigua** de una variable o estado, en lugar de la versión actual.

Esto pasa porque las funciones en JavaScript "capturan" las variables del *scope* en el momento en que se crean. Si esa función se ejecuta después (por ejemplo, dentro de un `useEffect`, `setTimeout`, evento, etc.), puede estar trabajando con datos desactualizados.

---

## 2. Ejemplo clásico con useEffect

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      console.log('Count actual:', count); // ← Aquí está el problema
      setCount(count + 1);                 // Siempre suma 1 desde el valor inicial
    }, 1000);

    return () => clearInterval(interval);
  }, []); // ← Dependencias vacías

  return <div>Count: {count}</div>;
}
```

**¿Qué pasa?** Aunque `count` cambie, el `setInterval` siempre ve el valor `0` (el que tenía cuando se creó el efecto). Esto es *stale closure*.

---

## 3. Otro ejemplo común con useCallback

```jsx
function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  const search = useCallback(() => {
    // Esta función captura la versión antigua de 'query'
    fetch(`/api/search?q=${query}`)
      .then(res => res.json())
      .then(setResults);
  }, []); // Dependencia vacía → stale

  return <button onClick={search}>Buscar</button>;
}
```

---

## 4. ¿Por qué ocurre tanto en hooks?

Porque los hooks (`useEffect`, `useCallback`, `useMemo`) deciden cuándo volver a crear sus funciones según sus **dependencias**. Si no incluyes una variable en las dependencias, la función mantiene la versión que tenía en el primer render.

---

## 5. Soluciones más usadas

### 5.1 Añadir las dependencias correctas (la más directa)

```jsx
useEffect(() => {
  const interval = setInterval(() => {
    setCount(c => c + 1); // ← Functional update (recomendado)
  }, 1000);
}, []); // Ya no necesitamos 'count'
```

### 5.2 Usar useRef para mantener valores actualizados

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  // Actualizamos el ref cada vez que cambia count
  useEffect(() => {
    countRef.current = count;
  }, [count]);

  useEffect(() => {
    const interval = setInterval(() => {
      setCount(countRef.current + 1); // Usamos el ref
    }, 1000);

    return () => clearInterval(interval);
  }, []);

  return <div>Count: {count}</div>;
}
```

### 5.3 Functional updates (la más limpia en muchos casos)

```jsx
setCount(prevCount => prevCount + 1);
```

Esto siempre recibe el valor más reciente.

---

## 6. Ejemplo completo con useEffect + API (muy común)

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let isMounted = true;

    const fetchUser = async () => {
      const res = await fetch(`/api/users/${userId}`);
      const data = await res.json();

      if (isMounted) setUser(data);
    };

    fetchUser();

    return () => { isMounted = false; };
  }, [userId]); // ← userId debe estar aquí

  return <div>{user?.name}</div>;
}
```

---

## 7. Reglas prácticas para evitar Stale Closures

- Siempre revisa el **array de dependencias** del `useEffect` y `useCallback`.
- Usa la ESLint rule `exhaustive-deps` (viene por defecto en Create React App y Vite).
- Prefiere **functional updates** (`setState(prev => ...)`).
- Usa `useRef` cuando necesites un valor mutable que no provoque re-renders.
- Para funciones que se pasan a hijos: combina `useCallback` + dependencias correctas.

---

## 8. Mini ejercicio rápido

Crea un componente que:

- Tenga un contador que aumente cada segundo.
- Tenga un botón "Guardar valor actual".
- Al hacer clic en guardar, guarde el valor del contador en una lista.
- Usa `setInterval` dentro de `useEffect`.
