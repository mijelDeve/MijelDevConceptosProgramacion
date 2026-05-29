# Componentes Compuestos (Compound Components)

## Propósito

Permite crear componentes flexibles que trabajan juntos y comparten un estado implícito, como un `<Select>` con sus `<Option>`.

## Ejemplo

```jsx
function Accordion({ children }) {
    const [openIndex, setOpenIndex] = useState(null);

    return (
        <AccordionContext.Provider value={{ openIndex, setOpenIndex }}>
            {children}
        </AccordionContext.Provider>
    );
}

Accordion.Item = function Item({ index, children }) {
    const { openIndex, setOpenIndex } = useContext(AccordionContext);
    return (
        <div onClick={() => setOpenIndex(index)}>
            {children}
            {openIndex === index && <div>Contenido expandido</div>}
        </div>
    );
};

// Uso
<Accordion>
    <Accordion.Item index={0}>Sección 1</Accordion.Item>
    <Accordion.Item index={1}>Sección 2</Accordion.Item>
</Accordion>
```

## Cuándo Usarlo

- Componentes con subcomponentes que deben compartir estado.
- APIs declarativas y flexibles (Select, Tabs, Accordion, Menu).
- Cuando necesitas que el usuario controle la estructura visual sin perder la lógica interna.
