# useFieldArray

[Volver a React Hook Form](README.md)

---

## 1. ¿Qué es useFieldArray?

`useFieldArray` es un hook de React Hook Form que sirve para manejar **campos dinámicos repetibles** dentro de un formulario. Es decir, cuando el usuario puede agregar, eliminar, mover o duplicar elementos de una lista.

Sin `useFieldArray`, tendrías que gestionar manualmente un estado con `useState` y sincronizarlo con React Hook Form. Con este hook, todo queda integrado con el formulario, la validación y el rendimiento.

---

## 2. ¿Cuándo usarlo?

Úsalo cuando tengas campos como:

- Lista de teléfonos.
- Lista de correos electrónicos.
- Productos de una factura.
- Integrantes de un equipo.
- Experiencias laborales.
- Direcciones de envío.

Por ejemplo:

```
Empleado
 ├── Nombre
 └── Teléfonos
      ├── 999999999
      ├── 988888888
      └── [+ Agregar teléfono]
```

Cada teléfono es un elemento del array.

---

## 3. Ejemplo básico

Supongamos que quieres registrar varios correos.

### Schema

```ts
type FormValues = {
  emails: {
    email: string;
  }[];
};
```

### useForm

```ts
const form = useForm<FormValues>({
  defaultValues: {
    emails: [
      { email: "" }
    ]
  }
});
```

### useFieldArray

```ts
const { fields, append, remove } = useFieldArray({
  control: form.control,
  name: "emails",
});
```

### Renderizar

```tsx
{fields.map((field, index) => (
  <div key={field.id}>
    <input
      {...form.register(`emails.${index}.email`)}
    />

    <button
      type="button"
      onClick={() => remove(index)}
    >
      Eliminar
    </button>
  </div>
))}
```

Agregar un nuevo email:

```tsx
<button
  type="button"
  onClick={() => append({ email: "" })}
>
  Agregar
</button>
```

---

## 4. ¿Qué devuelve?

Los más usados son:

### fields

Es el arreglo de elementos que debes renderizar.

```ts
fields
```

Ejemplo:

```json
[
  {
    "id": "abc123",
    "email": "a@gmail.com"
  },
  {
    "id": "xyz456",
    "email": "b@gmail.com"
  }
]
```

Observa que cada elemento tiene un `id` único.

### append

Agrega un elemento al final.

```ts
append({
  email: ""
});
```

### prepend

Lo agrega al inicio.

```ts
prepend({
  email: ""
});
```

### insert

Inserta en una posición específica.

```ts
insert(1, {
  email: ""
});
```

### remove

Elimina un elemento.

```ts
remove(0);
```

También puedes eliminar varios:

```ts
remove([0, 2]);
```

### swap

Intercambia dos posiciones.

```ts
swap(0, 1);
```

### move

Mueve un elemento.

```ts
move(3, 0);
```

Muy útil para drag & drop.

### update

Actualiza un elemento completo.

```ts
update(0, {
  email: "nuevo@gmail.com"
});
```

### replace

Reemplaza todo el arreglo.

```ts
replace([
  { email: "a@gmail.com" },
  { email: "b@gmail.com" }
]);
```

---

## 5. ¿Por qué `field.id` y no el índice?

Siempre debes usar:

```tsx
key={field.id}
```

y no

```tsx
key={index}
```

Porque React Hook Form genera ese `id` para mantener el estado correcto de cada campo cuando agregas, eliminas o reordenas elementos. Si usas el índice como key, React puede reutilizar componentes incorrectamente y terminar mostrando valores mezclados.

---

## 6. Integración con Controller

Si utilizas componentes controlados (por ejemplo, de MUI, shadcn o React Select), simplemente combina `useFieldArray` con `Controller`:

```tsx
{fields.map((field, index) => (
  <Controller
    key={field.id}
    control={form.control}
    name={`emails.${index}.email`}
    render={({ field }) => (
      <Input {...field} />
    )}
  />
))}
```

---

## 7. ¿Cuándo no usar useFieldArray?

No es necesario si:

- La cantidad de campos es fija.
- Solo tienes un objeto (no un arreglo).
- Solo ocultas o muestras un campo de forma condicional.

Por ejemplo:

```
Nombre
Apellido
Correo
```

Aquí basta con `useForm`; `useFieldArray` no aporta beneficios.

---

## 8. Resumen

Usa `useFieldArray` cuando el usuario pueda modificar la cantidad de elementos de una colección dentro del formulario. Sus ventajas principales son:

- Manejo integrado de listas dinámicas con React Hook Form.
- Mejor rendimiento al minimizar re-renderizados.
- Métodos listos para agregar, eliminar, mover y actualizar elementos.
- Compatibilidad con validación y componentes controlados mediante `Controller`.

En resumen, si en tu formulario existe un campo cuyo valor es un arreglo (`emails[]`, `phones[]`, `products[]`, etc.) y el usuario puede cambiar la cantidad de elementos, `useFieldArray` es la herramienta adecuada.
