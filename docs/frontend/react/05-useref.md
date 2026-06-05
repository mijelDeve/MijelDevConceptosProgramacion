# useRef

## 1. ¿Qué es useRef?

`useRef` te devuelve un objeto mutable (`{ current: valor }`) que persiste durante toda la vida del componente.

**Característica clave:** Cambiar `ref.current` **NO** provoca un re-render del componente.

```jsx
const miRef = useRef(valorInicial);
```

---

## 2. Diferencias: useRef vs useState

| Aspecto | useState | useRef |
|---|---|---|
| **Propósito** | Manejar estado que afecta la UI | Guardar valores mutables sin afectar la UI |
| **Cambiar el valor** | Provoca re-render | No provoca re-render |
| **Persistencia** | Persiste entre renders | Persiste entre renders |
| **Uso recomendado** | Datos que se muestran en pantalla | Referencias DOM, valores auxiliares, timers |
| **Inicialización** | Solo en primer render | Solo en primer render |
| **Asincronía** | Actualización asíncrona | Actualización inmediata y mutable |

### Regla fácil de recordar

- ¿El cambio debe actualizar lo que ve el usuario? → `useState`
- ¿Solo necesito guardar algo "por detrás" (DOM, contador, valor anterior, flag)? → `useRef`

---

## 3. Ejemplos prácticos

### 3.1 Acceder a elementos del DOM (el uso más clásico)

```jsx
function FocusInput() {
  const inputRef = useRef(null);

  const handleFocus = () => {
    inputRef.current.focus();
    inputRef.current.style.backgroundColor = 'yellow';
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Escribe aquí..." />
      <button onClick={handleFocus}>Enfocar input</button>
    </div>
  );
}
```

### 3.2 Guardar valores mutables sin re-render

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null); // ← Guardamos el ID del intervalo

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    return () => clearInterval(intervalRef.current);
  }, []);

  const stopTimer = () => {
    clearInterval(intervalRef.current); // Accedemos al valor actual
  };

  return (
    <div>
      <p>Segundos: {seconds}</p>
      <button onClick={stopTimer}>Detener</button>
    </div>
  );
}
```

### 3.3 Guardar el valor anterior de un estado

```jsx
function PreviousValueExample() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef(0);

  useEffect(() => {
    prevCountRef.current = count; // Actualizamos el ref
  }, [count]);

  return (
    <div>
      <p>Valor actual: {count}</p>
      <p>Valor anterior: {prevCountRef.current}</p>
      <button onClick={() => setCount(c => c + 1)}>Incrementar</button>
    </div>
  );
}
```

### 3.4 Evitar Stale Closure

```jsx
function CounterWithRef() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  useEffect(() => {
    countRef.current = count; // Siempre mantenemos el valor actual
  }, [count]);

  useEffect(() => {
    const id = setInterval(() => {
      console.log('Count actual (con ref):', countRef.current);
      setCount(c => c + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []); // Dependencias vacías sin stale closure

  return <div>Count: {count}</div>;
}
```

### 3.5 Formulario con validación

```jsx
function FormWithRef() {
  const [value, setValue] = useState('');
  const inputRef = useRef(null);
  const renderCount = useRef(0); // Para debugging

  renderCount.current += 1; // No causa re-render

  const focusAndSelect = () => {
    inputRef.current.focus();
    inputRef.current.select();
  };

  return (
    <div>
      <p>Renderizado: {renderCount.current} veces</p>
      <input
        ref={inputRef}
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <button onClick={focusAndSelect}>Enfocar y seleccionar</button>
    </div>
  );
}
```

---

## 4. Recomendaciones

- **No abuses** de `useRef` para guardar estado que deba mostrarse en pantalla. Eso es responsabilidad de `useState`.
- Úsalo mucho para:
  - Referencias DOM (focus, scroll, librerías como Chart.js, Leaflet, etc.)
  - Almacenar valores que no deben disparar renders
  - Guardar instancias de timers, subscriptions, etc.
  - Mantener valores entre renders sin *stale closures*
- `useRef` se inicializa solo una vez (en el primer render), igual que `useState`.

---

## 5. Mini ejercicio para practicar

Crea un componente llamado `Stopwatch` que tenga:

- Botón Start / Pause
- Botón Reset
- Muestre los segundos transcurridos
- Use `useRef` para guardar el `intervalId` y el `startTime`
