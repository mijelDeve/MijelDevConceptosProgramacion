# Prop Drilling

## 1. ¿Qué es Prop Drilling?

**Prop Drilling** es el problema que ocurre cuando tienes que pasar *props* a través de **muchos niveles de componentes** que no las necesitan, solo para llegar al componente que sí las usa.

## 2. Ejemplo visual

```jsx
<App>
  <Layout>
    <Sidebar>
      <UserMenu>
        <Avatar user={user} />   {/* user tuvo que pasar por 4 niveles */}
      </UserMenu>
    </Sidebar>
  </Layout>
</App>
```

Aunque `Layout` y `Sidebar` no usan la prop `user`, tienen que recibirla y volverla a pasar hacia abajo.

## 3. Problemas del Prop Drilling

- Código repetitivo y difícil de mantener.
- Rompe el principio de encapsulación.
- Hace los componentes menos reutilizables.
- Dificulta el escalado de la aplicación.

## 4. ¿Cómo se soluciona?

Existen varias soluciones según el tamaño y complejidad del proyecto:

### Context API (Solución más usada y recomendada para casos medios)

```jsx
const UserContext = createContext();

function App() {
  return (
    <UserContext.Provider value={user}>
      <Layout />
    </UserContext.Provider>
  );
}

// Componente profundo
function Avatar() {
  const user = useContext(UserContext);   // ← No necesita props
  return <img src={user.avatar} />;
}
```

**Ventaja:** Evitas pasar props manualmente.
**Desventaja:** Puede causar re-renders innecesarios si no se usa con cuidado.

### Zustand (Recomendado para la mayoría de proyectos)

```jsx
const useUserStore = create((set) => ({
  user: null,
  // ...
}));

function Avatar() {
  const user = useUserStore(state => state.user);
  // ...
}
```

Muy limpio, mínimo *boilerplate* y excelente rendimiento.

### Redux / Redux Toolkit

Para aplicaciones muy grandes o con lógica compleja, usando `useSelector`.

### Otras soluciones avanzadas

- **Compound Components** (cuando es un grupo de componentes relacionados)
- **Render Props / Children as Function**
- **Custom Hooks** (para lógica compartida)
- **TanStack Query** (cuando es *Server State*)

## 5. Respuesta corta y profesional (para entrevista)

> El Prop Drilling es cuando se pasan props a través de múltiples niveles de componentes solo para llegar al componente que realmente las necesita. Esto genera código verboso y componentes poco reutilizables.
>
> Se puede solucionar principalmente con **Context API** para casos medianos, o con gestores de estado como **Zustand** (mi preferido por su simplicidad) o **Redux** en aplicaciones grandes. También se puede mitigar usando *custom hooks* o patrones como *Compound Components*.

## 6. Pregunta extra

**¿Cuándo NO usar Context y mejor usar Zustand / Redux?**

- Si solo es un dato simple (tema, usuario) → Context está bien.
- Si el estado es usado en muchos lugares, se actualiza frecuentemente o tiene lógica compleja → **Zustand** o **Redux**.
