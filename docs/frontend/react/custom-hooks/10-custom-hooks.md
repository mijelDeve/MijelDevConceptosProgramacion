# Custom Hooks

## 1. ¿Qué es un Custom Hook?

Un **Custom Hook** es una función de JavaScript que empieza con la palabra `use` y que puede llamar a otros hooks de React (`useState`, `useEffect`, `useMemo`, `useCallback`, `useRef`, etc.).

Su propósito principal: extraer y reutilizar lógica de estado y efectos entre componentes.

---

## 2. ¿Cuándo debes crear un Custom Hook?

Crea un *custom hook* cuando:

- Tienes lógica compleja que se repite en varios componentes.
- Quieres separar la lógica de negocio de la UI (principio de responsabilidad única).
- Necesitas reutilizar la misma lógica en diferentes partes de la aplicación.
- Quieres hacer tu código más limpio, *testable* y mantenible.

**Regla práctica:** Si tienes más de 2-3 hooks (`useState` + `useEffect`) dentro de un componente y la lógica es reutilizable → extrae a un *custom hook*.

---

## 3. Ejemplo real: useForm

Este es uno de los *custom hooks* más usados en aplicaciones reales (formularios de login, registro, creación de productos, etc.).

### 3.1 Creación del Custom Hook

```tsx
// hooks/useForm.ts
import { useState, useCallback } from 'react';

type FormErrors<T> = Partial<Record<keyof T, string>>;

export function useForm<T extends object>(initialValues: T) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<FormErrors<T>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  // Actualizar un campo
  const handleChange = useCallback((e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));

    // Limpiar error del campo cuando el usuario escribe
    if (errors[name as keyof T]) {
      setErrors(prev => ({ ...prev, [name]: undefined }));
    }
  }, [errors]);

  // Validación simple
  const validate = useCallback((validationRules: (values: T) => FormErrors<T>) => {
    const newErrors = validationRules(values);
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  }, [values]);

  // Resetear formulario
  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setIsSubmitting(false);
  }, [initialValues]);

  return {
    values,
    errors,
    isSubmitting,
    handleChange,
    setIsSubmitting,
    validate,
    reset,
    setValues,
  };
}
```

### 3.2 Uso en un componente real (LoginForm)

```tsx
// components/LoginForm.tsx
import { useForm } from '@/hooks/useForm';

type LoginData = {
  email: string;
  password: string;
};

export default function LoginForm() {
  const {
    values,
    errors,
    isSubmitting,
    handleChange,
    validate,
    reset,
  } = useForm<LoginData>({
    email: '',
    password: '',
  });

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    const isValid = validate((data) => {
      const errs: any = {};
      if (!data.email) errs.email = "El email es requerido";
      if (!data.password) errs.password = "La contraseña es requerida";
      if (data.password.length < 6) errs.password = "Mínimo 6 caracteres";
      return errs;
    });

    if (!isValid) return;

    setIsSubmitting(true);

    try {
      // Llamada a API (aquí podrías usar TanStack Query)
      await loginApi(values);
      alert("Login exitoso");
      reset();
    } catch (error) {
      alert("Error al iniciar sesión");
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        type="email"
        value={values.email}
        onChange={handleChange}
        placeholder="Email"
      />
      {errors.email && <p style={{ color: 'red' }}>{errors.email}</p>}

      <input
        name="password"
        type="password"
        value={values.password}
        onChange={handleChange}
        placeholder="Contraseña"
      />
      {errors.password && <p style={{ color: 'red' }}>{errors.password}</p>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Iniciando sesión..." : "Iniciar Sesión"}
      </button>
    </form>
  );
}
```

---

## 4. Otros Custom Hooks muy usados en proyectos reales

- `useLocalStorage`
- `useDebounce` (para búsquedas)
- `useWindowSize`
- `useClickOutside`
- `useToggle`
- `useFetch` (aunque hoy en día se usa más TanStack Query)
- `useAuth`
- `useCart`

---

## 5. Beneficios de usar Custom Hooks

- **Reutilización** de lógica
- Código más **limpio** en los componentes
- Más fácil de **testear** (puedes testear el hook directamente)
- Mejor **separación de preocupaciones**
- Facilita el **mantenimiento** cuando la app crece

---

## 6. Consejo profesional

Empieza creando *custom hooks* cuando veas que estás **copiando y pegando** la misma lógica en varios componentes.
