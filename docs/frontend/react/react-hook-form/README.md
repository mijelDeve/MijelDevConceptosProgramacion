# React Hook Form

- [React Hook Form](#react-hook-form)
- [useFieldArray](01-usefieldarray.md)

---

## 1. ¿Qué es React Hook Form?

**React Hook Form** es una librería (no un hook nativo de React) que te ayuda a manejar formularios de forma mucho más eficiente, simple y con mejor rendimiento.

- No es un *Built-in Hook* de React.
- Es una librería de terceros (la más popular actualmente para formularios en React).
- Se instala con: `npm install react-hook-form`

Su objetivo principal: **simplificar el manejo de formularios complejos** (validaciones, registro de campos, *submit*, errores, etc.) con mínimo *re-renders*.

---

## 2. Diferencia entre `useState` y React Hook Form

| Aspecto | Solo con `useState` | Con React Hook Form |
|---|---|---|
| **Cantidad de código** | Mucho (muy verboso) | Muy poco y limpio |
| **Re-renders** | Alto (cada tecla causa re-render) | Muy bajo (solo renderiza lo necesario) |
| **Validaciones** | Las tienes que hacer manualmente | Incluye validaciones integradas (yup, zod, etc.) |
| **Manejo de errores** | Manual | Automático y fácil |
| **Performance** | Malo en formularios grandes | Excelente incluso con 50+ campos |
| **Funcionalidades** | Básicas | `register`, `watch`, `handleSubmit`, `reset`, `formState`, etc. |
| **Complejidad** | Fácil para formularios pequeños | Ideal para formularios medianos y grandes |

---

## 3. Ejemplo Comparativo

### Versión con `useState` (tradicional)

```tsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState({});

  const handleSubmit = (e) => {
    e.preventDefault();
    // validaciones manuales...
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} />
      <input value={password} onChange={e => setPassword(e.target.value)} />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Versión con React Hook Form (recomendada)

```tsx
import { useForm } from 'react-hook-form';

type LoginData = {
  email: string;
  password: string;
};

function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm<LoginData>();

  const onSubmit = async (data: LoginData) => {
    console.log(data);
    // Aquí puedes llamar a tu API
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("email", {
          required: "El email es obligatorio",
          pattern: { value: /^\S+@\S+$/i, message: "Email inválido" }
        })}
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register("password", {
          required: "La contraseña es obligatoria",
          minLength: { value: 6, message: "Mínimo 6 caracteres" }
        })}
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Enviando..." : "Iniciar Sesión"}
      </button>
    </form>
  );
}
```

---

## 4. Ventajas principales de React Hook Form

- Usa `register` para conectar los *inputs* (menos código `onChange`).
- Mejor rendimiento (usa `ref` internamente, no actualiza estado en cada tecla).
- Integración fácil con **Zod**, **Yup** o **Joi** para validaciones complejas.
- Soporta campos dinámicos, *arrays*, formularios grandes, etc.
- Muy ligero (~10kb).

---

## 5. Caso especial: `trigger()` y el modo de validación

Al usar `trigger(fields)` manualmente en un wizard o validación por pasos, los errores dejan de limpiarse automáticamente al escribir.

### Causa raíz

Por defecto `useForm` usa `mode: 'onSubmit'`. Al llamar `trigger()` explícitamente, los errores quedan "fijos" porque RHF no re-valida en cada cambio a menos que se lo configures.

```tsx
const form = useForm<UserFormValues>({
  resolver: zodResolver(CreateUserSchema),
  defaultValues,
  // ❌ Sin mode definido → mode: 'onSubmit' por defecto
  //    trigger() marca errores, pero no se limpian al escribir
});
```

### Solución

Agrega `mode: 'onChange'` o `'onTouched'` en `useForm`:

```tsx
const form = useForm<UserFormValues>({
  resolver: zodResolver(CreateUserSchema),
  defaultValues,
  mode: 'onChange', // 👈 re-valida al escribir y limpia errores automáticamente
});
```

Si `onChange` es muy agresivo (marca errores antes de que el usuario termine de escribir), usa `onTouched`:

```tsx
const form = useForm<UserFormValues>({
  resolver: zodResolver(CreateUserSchema),
  defaultValues,
  mode: 'onTouched', // 👈 valida al salir del campo (blur) y luego onChange
});
```

### Comparativa de modos

| mode | Cuándo valida |
|---|---|
| `onSubmit` | Solo al hacer submit (default) |
| `onChange` | Cada keystroke después del primer trigger o submit |
| `onTouched` | Al primer blur del campo, luego en cada cambio |
| `onBlur` | Solo al perder el foco |

### ¿Por qué funciona?

Con `onTouched` obtienes el mejor UX para formularios multi-step: no molesta al usuario mientras escribe por primera vez, pero sí limpia el error en cuanto corrige el valor.

---

## 6. Respuesta para entrevista

> *"React Hook Form es una librería especializada en el manejo de formularios. A diferencia de usar solo `useState`, que provoca muchos re-renders y requiere escribir mucha lógica manual, React Hook Form es mucho más performante, reduce boilerplate y facilita validaciones.*
>
> *Lo uso cuando el formulario es mediano o grande. Para formularios muy simples (1 o 2 campos) a veces todavía uso `useState`, pero en la mayoría de casos prefiero React Hook Form."*
