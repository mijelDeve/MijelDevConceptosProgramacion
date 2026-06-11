# Formulario por pasos (Wizard)

Si lo piensas desde arquitectura y UX, un formulario por pasos (wizard) debe ser un flujo controlado por estado donde cada paso conoce:

- Qué datos muestra.
- Qué validaciones ejecuta.
- Cuándo puede avanzar.
- Cuándo puede retroceder.
- Qué hacer al finalizar.

## Flujo general

```
Inicio
  ↓
Paso 1
  ↓
Validar
  ↓
Paso 2
  ↓
Validar
  ↓
Paso N
  ↓
Validar
  ↓
Confirmación
  ↓
Submit
```

## Estado principal

Lo mínimo que necesitas:

```ts
interface FormWizardState {
  currentStep: number;
  data: Record<string, any>;
  completedSteps: number[];
}
```

```ts
{
  currentStep: 2,
  data: {
    name: "Miguel",
    email: "miguel@gmail.com",
    role: "admin"
  },
  completedSteps: [1]
}
```

## Definición de pasos

Es recomendable que los pasos sean configurables.

```ts
const steps = [
  {
    id: 1,
    title: 'Información personal',
    fields: ['name', 'email']
  },
  {
    id: 2,
    title: 'Rol',
    fields: ['role']
  },
  {
    id: 3,
    title: 'Confirmación'
  }
];
```

Esto permite generar:

- Progress bar.
- Breadcrumbs.
- Navegación.
- Validaciones.

## Lógica para avanzar

Cuando el usuario presiona "Siguiente":

```ts
const nextStep = async () => {
  const isValid = await validateStep(currentStep);

  if (!isValid) return;

  setCurrentStep(prev => prev + 1);
};
```

## Validación por paso

No deberías validar todo el formulario. Solo el paso actual.

```ts
const validateStep = (step: number) => {
  switch (step) {
    case 1:
      return !!data.name && !!data.email;

    case 2:
      return !!data.role;

    default:
      return true;
  }
};
```

Con React Hook Form:

```ts
await trigger(['name', 'email']);
```

## Lógica para retroceder

Retroceder nunca debería validar.

```ts
const previousStep = () => {
  setCurrentStep(prev => prev - 1);
};
```

## Persistencia de datos

Todos los pasos comparten un único estado.

```ts
{
  name,
  email,
  role,
  permissions
}
```

Aunque el usuario cambie de paso, la información permanece.

## Salto entre pasos

Si permites clic directo:

```ts
goToStep(step);
```

Debes verificar:

```ts
if (step <= highestCompletedStep + 1)
```

Para evitar que alguien vaya directamente al paso final.

## Confirmación final

Antes del submit:

```
Paso 1 ✓
Paso 2 ✓
Paso 3 ✓

¿Desea guardar?
```

Muestras un resumen.

```ts
{
  name: "Miguel",
  email: "miguel@gmail.com",
  role: "Admin"
}
```

## Submit

Solo en el último paso.

```ts
const onSubmit = async () => {
  await createUser(formData);
};
```

Estados recomendados:

```ts
{
  isSubmitting,
  isSuccess,
  isError
}
```

## Arquitectura recomendada en React

Si estás usando una arquitectura por módulos:

```
features/
└── users/
    ├── components/
    │   ├── UserWizard.tsx
    │   ├── StepPersonal.tsx
    │   ├── StepRole.tsx
    │   └── StepConfirmation.tsx
    │
    ├── hooks/
    │   └── useWizard.ts
    │
    ├── schemas/
    │   ├── personal.schema.ts
    │   └── role.schema.ts
    │
    └── pages/
        └── UserCreatePage.tsx
```

## Hook reutilizable

```ts
const {
  currentStep,
  nextStep,
  previousStep,
  goToStep,
  isFirstStep,
  isLastStep,
} = useWizard({
  totalSteps: 3,
});
```

```ts
export const useWizard = ({ totalSteps }: Props) => {
  const [currentStep, setCurrentStep] = useState(1);

  const nextStep = () => {
    if (currentStep < totalSteps) {
      setCurrentStep(prev => prev + 1);
    }
  };

  const previousStep = () => {
    if (currentStep > 1) {
      setCurrentStep(prev => prev - 1);
    }
  };

  return {
    currentStep,
    nextStep,
    previousStep,
    isFirstStep: currentStep === 1,
    isLastStep: currentStep === totalSteps,
  };
};
```

## Con React Hook Form

La estrategia más escalable es:

```tsx
<FormProvider {...form}>
  <StepPersonal />
  <StepRole />
  <StepConfirmation />
</FormProvider>
```

Con:

```ts
const form = useForm({
  resolver: zodResolver(schema),
});
```

Y cada paso consume:

```ts
const { register } = useFormContext();
```

De esta forma tienes:

- Un único formulario.
- Un único estado.
- Un único submit.
- Validaciones por paso usando `trigger()`.
- Componentes desacoplados.

Es la solución que suele verse en aplicaciones empresariales grandes (ERP, CRM, módulos de configuración, onboarding, creación de usuarios, credenciales, roles y permisos, etc.).
