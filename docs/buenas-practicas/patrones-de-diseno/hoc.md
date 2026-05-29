# Higher-Order Component (HOC)

## Propósito

Función que toma un componente y devuelve un nuevo componente "mejorado". Útil para lógica transversal como autenticación o logging.

## Ejemplo

```jsx
function withAuth(Component) {
    return function AuthenticatedComponent(props) {
        const user = useAuth();

        if (!user) {
            return <Redirect to="/login" />;
        }

        return <Component {...props} user={user} />;
    };
}

// Uso
function Dashboard({ user }) {
    return <h1>Bienvenido, {user.name}</h1>;
}

const ProtectedDashboard = withAuth(Dashboard);
```

## Cuándo Usarlo

- Lógica transversal (autenticación, logging, métricas).
- Cuando múltiples componentes comparten la misma funcionalidad.
- Para envolver componentes con providers o configuraciones.

> **Nota**: En React moderno, los Custom Hooks suelen ser preferibles sobre HOCs por ser más simples y componibles. Sin embargo, HOCs siguen siendo útiles para ciertos casos como integración con librerías externas.
