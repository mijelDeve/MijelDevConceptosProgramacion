# nuqs — Estado en la URL

nuqs es una librería para sincronizar el estado de una aplicación con los parámetros de la URL de forma sencilla y tipada.

Su principal objetivo es evitar que tengas que manipular manualmente `URLSearchParams`, `window.location.search` o las APIs de navegación de frameworks como React, Next.js o Remix.

## Problema que resuelve

Sin nuqs, para guardar un filtro en la URL podrías hacer algo así:

```js
const params = new URLSearchParams(window.location.search);
params.set("page", "2");
window.history.pushState({}, "", `?${params.toString()}`);
```

Esto se vuelve repetitivo y difícil de mantener cuando tienes muchos filtros.

### Con nuqs

```js
import { useQueryState, parseAsInteger } from 'nuqs';

const [page, setPage] = useQueryState(
  'page',
  parseAsInteger.withDefault(1)
);
```

Ahora:

```js
setPage(2);
```

actualiza automáticamente la URL:

```
?page=2
```

y si alguien abre esa URL, el estado se recupera automáticamente.

## Casos de uso comunes

### Paginación

```
/products?page=3
```

```js
const [page, setPage] = useQueryState(
  'page',
  parseAsInteger.withDefault(1)
);
```

### Búsqueda

```
/products?search=laptop
```

```js
const [search, setSearch] = useQueryState('search');
```

### Filtros

```
/products?category=electronics&sort=price
```

```js
const [category, setCategory] = useQueryState('category');
const [sort, setSort] = useQueryState('sort');
```

## Ventajas

### URLs compartibles

Si un usuario copia la URL:

```
/products?page=5&category=books
```

otro usuario verá exactamente el mismo estado de la aplicación.

### Navegación del navegador

Los botones Atrás y Adelante funcionan correctamente porque el estado está en la URL.

### Tipado

Puedes definir parsers:

- `parseAsInteger`
- `parseAsBoolean`
- `parseAsString`
- `parseAsArrayOf(...)`

Por ejemplo:

```js
const [enabled] = useQueryState(
  'enabled',
  parseAsBoolean.withDefault(false)
);
```

### Integración con Next.js

nuqs fue diseñado pensando en Next.js y funciona muy bien con:

- App Router
- Server Components
- Client Components
- SSR

## Ejemplo real

Supongamos una tabla de usuarios:

```
/users?page=2&search=miguel&role=admin
```

```js
const [page, setPage] = useQueryState(
  'page',
  parseAsInteger.withDefault(1)
);

const [search, setSearch] = useQueryState('search');

const [role, setRole] = useQueryState('role');
```

La URL se convierte en la fuente de verdad del estado de los filtros.

## Resumen

nuqs es una librería que convierte los parámetros de la URL en estado React tipado y reactivo.

Es especialmente útil para:

- Filtros
- Búsquedas
- Paginación
- Ordenamientos
- Cualquier estado que quieras que sea persistente y compartible mediante URL

La idea principal es:

> "La URL es parte del estado de la aplicación, y nuqs hace que trabajar con ella sea tan fácil como usar `useState`."
