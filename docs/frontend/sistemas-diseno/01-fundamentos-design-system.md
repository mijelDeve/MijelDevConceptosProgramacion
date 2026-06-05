# Fundamentos de un Design System

## 1. ¿Qué es un Sistema de Diseño?

Un **sistema de diseño** (*Design System*) es una colección reutilizable de componentes UI, patrones, guías de estilo, *tokens* de diseño (colores, tipografía, espaciados, etc.) y documentación que asegura **consistencia**, **escalabilidad** y **eficiencia** en productos digitales.

Facilita el trabajo entre diseñadores y desarrolladores, reduce duplicación y acelera el desarrollo.

**Ejemplos populares:** Material Design (Google), Fluent (Microsoft), o sistemas personalizados como los de Shopify o Airbnb.

---

## 2. Propiedades y Estados Comunes en Componentes

Los componentes (Button, Input, Card, etc.) se diseñan con *props* y estados para ser flexibles y predecibles:

| Propiedad | Descripción | Ejemplo |
|---|---|---|
| **Variant** | Define estilos semánticos (primary, secondary, ghost, destructive, outline) | `variant="primary"` |
| **Size** | Controla dimensiones (small, medium, large, icon-only) | `size="md"` |
| **Disabled** | Estado no interactivo, cambia opacidad y cursor | `disabled={true}` |
| **Loading** | Indica acción en progreso, deshabilita temporalmente | `isLoading={true}` |
| **Children** | Contenido hijo (texto, iconos, otros componentes) | `{children}` |

### Buenas prácticas

- Usa **TypeScript** para tipar *props* estrictamente.
- Maneja estados con CSS (pseudo-clases `:disabled`, `:focus`) + clases condicionales.
- Soporta **accesibilidad (WCAG)**: *focus management*, ARIA, contraste.
- Prueba *edge cases*: loading + disabled, responsive, *dark mode*.

---

## 3. Integración con Tailwind CSS

Tailwind CSS es *utility-first*, ideal para *design systems* por su velocidad y personalización. No escribas clases manuales largas; usa abstracciones.

### Herramientas clave

#### clsx

Combina clases condicionalmente de forma limpia. Ignora valores *falsy*.

```tsx
import clsx from 'clsx';

const classes = clsx(
  'base-class',
  isActive && 'active',
  variant === 'primary' && 'bg-blue-500'
);
```

#### tailwind-merge (twMerge)

Resuelve conflictos de clases Tailwind (ej. `p-4 p-6` → queda la última).

#### cn utility (común en shadcn/ui)

Combina `clsx` + `twMerge`:

```tsx
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

#### CVA (Class Variance Authority)

La mejor para *variants*. Define base, variants, *compound variants* y *defaults* de forma declarativa. Perfecta para *design systems*.

```tsx
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded font-medium transition-colors", // base
  {
    variants: {
      variant: {
        primary: "bg-blue-600 text-white hover:bg-blue-700",
        secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
        ghost: "hover:bg-gray-100",
      },
      size: {
        sm: "h-8 px-3 text-sm",
        md: "h-10 px-4",
        lg: "h-12 px-6",
      },
      disabled: { true: "opacity-50 cursor-not-allowed" },
    },
    compoundVariants: [
      // Ej: loading + primary
    ],
    defaultVariants: { variant: "primary", size: "md" },
  }
);

type ButtonProps = VariantProps<typeof buttonVariants> & {
  isLoading?: boolean;
  children: React.ReactNode;
};

const Button = ({ variant, size, disabled, isLoading, children, ...props }: ButtonProps) => {
  return (
    <button
      className={cn(buttonVariants({ variant, size, disabled: disabled || isLoading }))}
      disabled={disabled || isLoading}
      {...props}
    >
      {isLoading ? <Spinner /> : children}
    </button>
  );
};
```

Esto genera clases limpias, **tipado automático** y fácil mantenimiento.

---

## 4. Patrones Avanzados de Componentes

### 4.1 Compound Components

Componentes que trabajan juntos compartiendo estado implícitamente (vía Context). Ideal para Accordion, Tabs, Select, Modal, Dropdown. Evita *prop drilling*.

```tsx
const Accordion = ({ children }) => { /* Context provider */ };
Accordion.Item = ({ value, children }) => { /* ... */ };
Accordion.Trigger = ...;
Accordion.Content = ...;

// Uso:
<Accordion>
  <Accordion.Item value="1">
    <Accordion.Trigger>Header</Accordion.Trigger>
    <Accordion.Content>Body</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

**Ventajas:** API intuitiva como HTML nativo, flexible.

### 4.2 Render Props

Pasa una función como *prop* que recibe estado y devuelve JSX. Útil para lógica reutilizable dejando UI al consumidor (ej. listas filtradas, *data fetching*).

```tsx
const DataList = ({ renderItem, ... }) => {
  const data = useData();
  return <ul>{data.map(item => renderItem(item))}</ul>;
};

// Uso: <DataList renderItem={item => <CustomCard item={item} />} />
```

Menos común ahora con hooks, pero poderoso para composición.

### 4.3 Controlled vs Uncontrolled

| Enfoque | Quién maneja el estado | Ideal para |
|---|---|---|
| **Controlled** | El padre (props `value` + `onChange`) | Forms complejos, más predecible |
| **Uncontrolled** | El componente internamente (`useState` / `defaultValue`) | Casos simples, más rápido |

Muchos componentes (como inputs en Radix UI o shadcn) soportan ambos para máxima flexibilidad.

---

## 5. Storybook

**Storybook** es la herramienta estándar para desarrollar, documentar, testear y visualizar componentes en aislamiento. Es esencial en *design systems*.

- Crea *stories* (`.stories.tsx`) para cada variante / estado.
- Addons: Controls (interactuar con props), Actions, Accessibility, Viewport, Dark mode, etc.
- Documentación con MDX.
- *Visual testing*, Chromatic (para reviews).
- Integra con Figma, Tailwind, etc.

```tsx
import { Button } from './Button';

export default { component: Button, title: 'Components/Button' };

export const Primary = { args: { variant: 'primary', children: 'Click me' } };
export const Loading = { args: { isLoading: true } };
```

---

## 6. Recomendaciones para construir tu Design System

| Aspecto | Recomendación |
|---|---|
| **Tokens** | Usa CSS variables + Tailwind config (`theme.extend`) |
| **Estructura** | Carpeta `/components/ui/` con archivos atómicos |
| **Starter** | shadcn/ui (copia-pega componentes con CVA + Tailwind) |
| **Accesibilidad** | a11y, React Testing Library, Interaction tests en Storybook |
| **Temas** | Dark mode con *class strategy* |
| **Documentación** | Zeroheight, Storybook o Notion |
| **Versionado** | Publica como paquete npm |
