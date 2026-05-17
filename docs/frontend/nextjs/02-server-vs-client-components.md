# Server Components vs Client Components en Next.js

## ¿Qué son?

Con el App Router de Next.js, todos los componentes son **Server Components por defecto**. Solo se convierten en Client Components cuando agregas la directiva `'use client'` al inicio del archivo.

---

## Server Components

Corren **exclusivamente en el servidor**. Su output (HTML) es enviado al cliente, pero el código JavaScript nunca llega al bundle del navegador.

### Capacidades

- ✅ `async/await` directamente en el componente
- ✅ Acceso directo a base de datos, sistema de archivos, variables de entorno
- ✅ Fetch de datos sin `useEffect`
- ✅ Reducen el bundle de JavaScript enviado al cliente

### Limitaciones

- ❌ No pueden usar `useState`, `useReducer`, `useEffect` u otros hooks
- ❌ No pueden manejar eventos del browser (`onClick`, `onChange`, etc.)
- ❌ No tienen acceso a APIs del browser (`localStorage`, `window`, etc.)

### Ejemplo

```tsx
// app/users/page.tsx — Server Component (por defecto)
async function UsersPage() {
  // Acceso directo a la DB o fetch al backend
  const users = await db.getUsers();

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UsersPage;
```

---

## Client Components

Corren en el **navegador** (aunque también se pre-renderizan en el servidor para el HTML inicial). Necesitan la directiva `'use client'` al tope del archivo.

### Capacidades

- ✅ Hooks de React (`useState`, `useEffect`, `useRef`, etc.)
- ✅ Event listeners (`onClick`, `onChange`, `onSubmit`, etc.)
- ✅ APIs del browser (`localStorage`, `window`, `navigator`, etc.)
- ✅ Librerías que dependen del DOM

### Limitaciones

- ❌ No pueden ser `async` directamente
- ❌ Aumentan el bundle de JavaScript enviado al cliente
- ❌ No tienen acceso directo a la base de datos

### Ejemplo

```tsx
'use client';

import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      Clicks: {count}
    </button>
  );
}

export default Counter;
```

---

## Comparación rápida

| Característica | Server Component | Client Component |
|---|---|---|
| Directiva necesaria | Ninguna (es el default) | `'use client'` |
| Hooks de React | ❌ | ✅ |
| Event handlers | ❌ | ✅ |
| `async/await` directo | ✅ | ❌ |
| Acceso a DB / FS | ✅ | ❌ |
| APIs del browser | ❌ | ✅ |
| Impacto en bundle JS | Ninguno | Sí |

---

## La regla simple

> ¿Necesitas hooks o interactividad? → **Client Component**
> ¿Solo muestras datos? → **Server Component**

---

## El patrón más recomendado

**Bajar el `'use client'` lo más abajo posible** en el árbol de componentes. Así maximizas los Server Components y minimizas el JS enviado al cliente.

```tsx
// Page.tsx → Server Component (trae los datos)
async function Page() {
  const data = await fetchData();

  return (
    <div>
      <h1>Mi página</h1>
      {/* Le pasa datos como props al componente interactivo */}
      <InteractiveWidget data={data} />
    </div>
  );
}
```

```tsx
// InteractiveWidget.tsx → Client Component (solo maneja interacción)
'use client';

import { useState } from 'react';

function InteractiveWidget({ data }) {
  const [selected, setSelected] = useState(null);

  return (
    <ul>
      {data.map(item => (
        <li key={item.id} onClick={() => setSelected(item)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}
```

### ¿Por qué este patrón?

- `Page` corre en el servidor → accede a datos directamente, sin fetch desde el cliente
- `InteractiveWidget` es client solo donde se necesita → bundle más pequeño
- Los datos bajan como **props**, no como estado global

---

## Regla de composición importante

Un **Server Component puede importar y renderizar Client Components**, pero un Client Component **no puede importar Server Components directamente**.

```tsx
// ✅ Correcto: Server importa Client
import ClientButton from './ClientButton'; // 'use client'

export default function ServerPage() {
  return <ClientButton />;
}
```

```tsx
// ❌ Incorrecto: Client importando Server
'use client';
import ServerComponent from './ServerComponent'; // Esto no funciona como esperas
```

La excepción es pasar Server Components como **children** o **props** a Client Components, lo cual sí está permitido.
