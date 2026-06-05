# Configuración de Proyectos React con Vite

## 1. TypeScript

TypeScript es simplemente JavaScript con:

- Tipado estático
- Interfaces
- Types
- Generics
- Mejor autocompletado
- Detección de errores antes de ejecutar

```tsx
function sum(a: number, b: number): number {
  return a + b;
}
```

---

## 2. TypeScript + Compilador (SWC / esbuild / Babel)

Normalmente se refiere a TypeScript acompañado de un compilador como `tsc`, SWC, esbuild o Babel.

### En Vite, por defecto

TypeScript **NO** compila con `tsc`. Vite usa:

- **esbuild** (rápido) durante desarrollo
- **Rollup** en build final

### Diferencia importante

| Herramienta | Type checking | Compilación TS → JS | Velocidad |
|---|---|---|---|
| `tsc` | ✅ Sí | ✅ Sí | Lenta pero completa |
| esbuild / SWC | ❌ No | ✅ Sí | Ultra rápida |

Por eso en proyectos modernos se suele usar:

```
vite + typescript + vite build
```

y por separado:

```
tsc --noEmit
```

para validar tipos.

---

## 3. RSC (React Server Components)

RSC es una de las mayores evoluciones modernas de React.

### Problema que intenta resolver

En React tradicional, todo el componente se envía al navegador: mucho JavaScript, bundle pesado.

### Con RSC

Parte del componente se ejecuta **solo en el servidor**. El navegador recibe:

- HTML
- Datos serializados
- Menos JS

### Ventajas

- Mejor performance
- Menos bundle
- Mejor SEO
- Fetching directo desde servidor
- Streaming

### Ejemplo conceptual

```tsx
// Server Component
async function Products() {
  const data = await db.products.findMany();

  return <List data={data} />;
}
```

Ese código nunca llega al cliente.

### Limitaciones

Los Server Components **NO** pueden usar:

- `useState`
- `useEffect`
- `window`
- `document`

Si necesitas eso → `"use client"`.

### Frameworks que usan RSC

- Next.js
- RedwoodSDK
- React experimental setups

---

## 4. React Router v7

React Router es el router "oficial" del ecosistema React.

### Antes (v5/v6)

Solo hacía navegación y rutas.

### React Router v7

Ahora incluye ideas inspiradas en Remix:

- loaders
- actions
- SSR
- streaming
- data fetching
- mutations
- forms

```tsx
export async function loader() {
  return fetch("/api/users");
}
```

### Ventajas

- Muy estable
- Gran ecosistema
- Familiar
- Bueno para SPA y SSR

### Desventajas

- APIs algo complejas
- Menos type-safe
- Nested routing puede complicarse

---

## 5. TanStack Router

Router moderno creado por el ecosistema TanStack. Muy enfocado en type safety y DX.

### Diferencia clave vs React Router

React Router es más tradicional. TanStack Router es extremadamente **type-safe**:

```tsx
navigate({
  to: "/users/$id",
  params: { id: "123" }
});
```

Si falta un parámetro → TypeScript marca error.

### Ventajas

- Excelente integración TS
- Search params tipados
- Muy moderno
- Excelente DX
- Data loading integrado

### Desventajas

- Comunidad más pequeña
- Menos tutoriales
- Más complejo al inicio

---

## 6. Vike

Antes llamado `vite-plugin-ssr`. Es un framework SSR minimalista para Vite.

### Qué permite

- SSR
- SSG
- Streaming
- Routing

sin obligarte a una estructura gigante tipo Next.js.

### Filosofía

"Usa Vite directamente". Muy flexible.

### Ideal para

Developers que quieren control total, arquitectura personalizada, performance y evitar frameworks pesados.

### Desventajas

- Más manual
- Menos "baterías incluidas"
- Más arquitectura por tu cuenta

---

## 7. RedwoodSDK

Evolución moderna de RedwoodJS. Muy orientado a React Server Components, Cloudflare, fullstack React y edge rendering.

### Diferencia vs RedwoodJS clásico

| Aspecto | RedwoodJS (clásico) | RedwoodSDK (moderno) |
|---|---|---|
| GraphQL | Pesado | Minimalista |
| Enfoque | Fullstack monolítico | RSC, Edge-first |
| Filosofía | Prisma + GraphQL | Web standards |

### Ventajas

- Fullstack moderno
- Edge rendering
- Muy buena performance
- RSC nativo

### Desventajas

- Ecosistema pequeño
- Aún madurando
- Menos comunidad / documentación

---

## 8. Comparativa rápida

| Tecnología | Qué es | Enfoque |
|---|---|---|
| **TypeScript** | Tipado | Seguridad y DX |
| **TS + Compiler** | Compilación TS | Performance build |
| **RSC** | Arquitectura React | Menos JS y SSR |
| **React Router v7** | Routing tradicional moderno | Estabilidad |
| **TanStack Router** | Router type-safe | DX + TS |
| **Vike** | Framework SSR para Vite | Flexibilidad |
| **RedwoodSDK** | Framework fullstack React | RSC + Edge |

---

## 9. ¿Qué elegir en React + Vite?

| Proyecto | Recomendación |
|---|---|
| **SPA normal** | Vite + TypeScript + TanStack Router o React Router |
| **Proyecto grande moderno** | TanStack Router + RSC (+ SSR si el framework lo soporta) |
| **SSR flexible y minimalista** | Vike |
| **Fullstack moderno estilo Next.js** | RedwoodSDK |

---

## 10. Recomendación de aprendizaje

1. TypeScript
2. React Router v7
3. TanStack Router
4. SSR
5. RSC
6. Vike
7. RedwoodSDK

Routing y TypeScript son la base; RSC y SSR son temas más avanzados de arquitectura React moderna.
