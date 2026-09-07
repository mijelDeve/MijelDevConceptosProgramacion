# Rutas y navegación en React Native

## 1. React (Web) — cómo estás acostumbrado

En React web normalmente usas React Router:

```jsx
// App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/habits" element={<Habits />} />
        <Route path="/habits/:id" element={<HabitDetail />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </BrowserRouter>
  );
}
```

- Las rutas están definidas manualmente en un archivo.
- Se basan en la URL del navegador (`/habits`, `/habits/123`).
- Navegas con `<Link to="/habits">` o `useNavigate()`.

---

## 2. React Native — cómo funciona realmente

En React Native no existe una URL real como en el navegador (excepto cuando usas deep linking).

La navegación se maneja con un sistema de navegadores (Stack, Tabs, Drawer, etc.).

Hay dos formas principales de hacerlo actualmente:

### A) Forma moderna y recomendada con Expo → Expo Router (File-based routing)

Es la que más se parece a Next.js y es la que te recomiendo usar.

La estructura de carpetas es el sistema de rutas:

```text
app/
├── _layout.tsx          ← Layout principal (como un App.tsx + navegador)
├── index.tsx            ← Ruta "/" (pantalla de inicio)
├── habits/
│   ├── index.tsx        ← Ruta "/habits"
│   └── [id].tsx         ← Ruta "/habits/123" (dinámica)
├── profile.tsx          ← Ruta "/profile"
└── (tabs)/              ← Grupo de tabs (opcional)
    ├── _layout.tsx
    ├── index.tsx
    └── settings.tsx
```

Ejemplo de navegación:

```tsx
import { Link, useRouter } from 'expo-router';

// Navegar con Link (parecido a React Router)
<Link href="/habits">Ver hábitos</Link>
<Link href={`/habits/${habit.id}`}>Detalle</Link>

// Navegar programáticamente
const router = useRouter();
router.push('/habits');
router.push({ pathname: '/habits/[id]', params: { id: '123' } });
router.back();
```

Ventajas de Expo Router:

- Muy similar a Next.js / file-based routing.
- Soporte nativo de deep linking.
- Type-safe (con TypeScript).
- Layouts anidados muy potentes.
- Es lo que Expo recomienda oficialmente desde hace varios SDKs.

### B) Forma clásica → React Navigation (la más usada históricamente)

Aquí defines las rutas de forma imperativa (parecido a React Router, pero con navegadores):

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Habits" component={HabitsScreen} />
        <Stack.Screen name="HabitDetail" component={HabitDetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

Navegación:

```tsx
navigation.navigate('Habits');
navigation.navigate('HabitDetail', { id: 123 });
navigation.goBack();
```

---

## Comparación directa

| Concepto | React (Web + React Router) | React Native (Expo Router) | React Native (React Navigation) |
|---|---|---|---|
| Definición de rutas | Manual en JSX | Por estructura de carpetas | Manual en JSX |
| Basado en URL | Sí | Sí (simulado + deep linking) | No (solo nombres de pantalla) |
| Navegación | `<Link>` / `useNavigate` | `<Link>` / `useRouter` | `navigation.navigate` |
| Layouts anidados | Sí (con `Outlet`) | Sí (muy potente) | Sí (pero más verboso) |
| Deep linking | Nativo | Excelente | Hay que configurarlo |
| Curva de aprendizaje | Baja | Baja-Media (si vienes de Next.js) | Media |
| Recomendado en 2026 | — | Sí (oficial de Expo) | Todavía muy usado |