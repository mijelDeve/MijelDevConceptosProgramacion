# Container & Presentational

## Propósito

Separar la lógica y los datos (Container) de la pura presentación visual (Presentational).

## Componentes

| Tipo | Responsabilidad |
|---|---|
| **Container** | Maneja estado, lógica de negocio, llamadas a APIs. No se preocupa por el estilo visual |
| **Presentational** | Recibe datos via `props`, renderiza la UI. No tiene estado propio ni lógica |

## Ejemplo

```jsx
// Container
function UserListContainer() {
    const [users, setUsers] = useState([]);

    useEffect(() => {
        fetch('/api/users').then(r => r.json()).then(setUsers);
    }, []);

    return <UserList users={users} />;
}

// Presentational
function UserList({ users }) {
    return (
        <ul>
            {users.map(u => <li key={u.id}>{u.name}</li>)}
        </ul>
    );
}
```

## Cuándo Usarlo

- Componentes que mezclan lógica y presentación.
- Cuando quieres reutilizar la misma UI con diferentes fuentes de datos.
- Para facilitar el testing de la lógica y la UI por separado.
