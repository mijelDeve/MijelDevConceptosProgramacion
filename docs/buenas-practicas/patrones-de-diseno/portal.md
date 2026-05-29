# Portal

## Propósito

Permite renderizar hijos en un nodo del DOM que está fuera de la jerarquía del componente padre, ideal para modales, tooltips o dropdowns.

## Ejemplo

```jsx
import { createPortal } from 'react-dom';

function Modal({ children, isOpen }) {
    if (!isOpen) return null;

    return createPortal(
        <div className="modal-overlay">
            <div className="modal-content">
                {children}
            </div>
        </div>,
        document.getElementById('modal-root')
    );
}

// Uso
function App() {
    const [isOpen, setIsOpen] = useState(false);

    return (
        <div>
            <button onClick={() => setIsOpen(true)}>Abrir Modal</button>
            <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
                <h2>Contenido del modal</h2>
            </Modal>
        </div>
    );
}
```

## Beneficios

- Evita problemas de z-index y desbordamiento (overflow).
- El evento "burbujea" normalmente (event bubbling) en React, aunque el DOM esté fuera.
- Ideal para modales, tooltips, dropdowns y notificaciones.

## Cuándo Usarlo

- Modales y diálogos que deben aparecer sobre todo el contenido.
- Tooltips que podrían recortarse por `overflow: hidden`.
- Notificaciones fijas en la esquina de la pantalla.
