# Genéricos: restricción con extends

```typescript
<T extends { id: number }>
```

Es un **genérico con restricción** en TypeScript. Se lee así:

- **`T`** — un placeholder que representa "cualquier tipo"
- **`extends { id: number }`** — una restricción: `T` debe tener al menos una propiedad `id` de tipo `number`

Básicamente: *"No me importa qué tipo seas, mientras tengas un `id` numérico."*

---

## Ejemplo práctico: `updateArrayItem`

Imagina una función que reemplaza un elemento dentro de un array buscándolo por su `id`:

```typescript
function updateArrayItem<T extends { id: number }>(
  items: T[],
  updatedItem: T
): T[] {
  return items.map(item =>
    item.id === updatedItem.id ? updatedItem : item
  );
}
```

La función no necesita saber qué forma tiene `T`. Solo necesita poder comparar `item.id === updatedItem.id`.

---

## Inferencia automática

Cuando llamas a la función, TypeScript infiere `T` automáticamente según el tipo que le pases:

```typescript
// ICredential tiene { id: number } → T = ICredential
const credenciales = await api.obtenerCredenciales();
updateArrayItem(credenciales, response.data);
//              ICredential[]     ICredential

// IUser tiene { id: number } → T = IUser
const usuarios = await api.obtenerUsuarios();
updateArrayItem(usuarios, usuarioActualizado);
//              IUser[]          IUser
```

---

## Sin genérico vs con genérico

Sin el genérico, tendrías que escribir una función para cada tipo:

```typescript
// ❌ Sin genérico — una función por cada tipo
function updateCredentialItem(creds: ICredential[], item: ICredential) { ... }
function updateUserItem(users: IUser[], item: IUser) { ... }
function updateProductItem(products: IProduct[], item: IProduct) { ... }
// Y así para cada tipo que tenga id...
```

Con el genérico, una sola función sirve para todos:

```typescript
// ✅ Con genérico — una función para cualquier tipo con id: number
function updateArrayItem<T extends { id: number }>(items: T[], item: T) { ... }

// Sirve para ICredential, IUser, IProduct, y cualquier otro
// que tenga una propiedad id: number
```

---

## Analogía

`<T extends { id: number }>` es como un **vallado VIP**:

- **`T`** — "No me importa quién seas, puedes entrar"
- **`extends { id: number }`** — "... siempre y cuando tengas una credencial con un número de ID válido"

No necesitas saber el nombre de cada persona que va a entrar. Solo necesitas saber que todas tienen un pase con número.

---

## En resumen

| Concepto | Significado |
|----------|-------------|
| `T` | Placeholder: representa cualquier tipo |
| `extends { id: number }` | Restricción: `T` debe tener `id: number` |
| Inferencia | TS deduce `T` automáticamente al llamar la función |
| Beneficio | Una función reusable para cualquier tipo que cumpla la restricción |
