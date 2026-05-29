# Hooks Personalizados (Custom Hooks)

## Propósito

Permiten "crear" y reutilizar lógica con estado entre múltiples componentes en React.

## Ejemplo

```jsx
function useWindowSize() {
    const [size, setSize] = useState({
        width: window.innerWidth,
        height: window.innerHeight,
    });

    useEffect(() => {
        const handleResize = () => {
            setSize({
                width: window.innerWidth,
                height: window.innerHeight,
            });
        };

        window.addEventListener('resize', handleResize);
        return () => window.removeEventListener('resize', handleResize);
    }, []);

    return size;
}

// Uso en cualquier componente
function Dashboard() {
    const { width } = useWindowSize();
    return <div>Ancho: {width}px</div>;
}
```

## Cuándo Usarlo

- Cuando repites la misma lógica con estado en varios componentes.
- Para aislar efectos secundarios y lógica compleja.
- Para compartir funcionalidad entre componentes sin usar HOCs o Render Props.
