# Render Props

## Propósito

Patrón para compartir código entre componentes usando una prop cuyo valor es una función.

## Ejemplo

```jsx
function MouseTracker({ render }) {
    const [position, setPosition] = useState({ x: 0, y: 0 });

    useEffect(() => {
        const handleMouseMove = (e) => {
            setPosition({ x: e.clientX, y: e.clientY });
        };
        window.addEventListener('mousemove', handleMouseMove);
        return () => window.removeEventListener('mousemove', handleMouseMove);
    }, []);

    return render(position);
}

// Uso
<MouseTracker
    render={({ x, y }) => (
        <h1>El ratón está en ({x}, {y})</h1>
    )}
/>
```

## Cuándo Usarlo

- Cuando quieres compartir lógica entre componentes sin usar HOCs.
- Para dar flexibilidad al consumidor sobre cómo renderizar el contenido.
- Casos donde el componente hijo necesita acceso a datos gestionados por el padre.

> **Nota**: En React moderno, los Custom Hooks suelen ser preferibles a Render Props por ser más legibles y componibles.
