# Estado Global en React

## 1. Comparación General

| Característica | Context API | Redux | Zustand |
|---|---|---|---|
| **Complejidad** | Baja | Alta (mucho *boilerplate*) | Muy baja |
| **Boilerplate** | Poco | Mucho | Casi ninguno |
| **Rendimiento** | Bueno (con cuidado) | Excelente (con Reselect) | Excelente |
| **Tamaño del bundle** | Cero | Grande | Muy pequeño |
| **DevTools** | Básico | Excelentes | Buenos |
| **Curva de aprendizaje** | Fácil | Empinada | Muy fácil |
| **Mejor para** | Apps medianas / simples | Apps muy grandes / complejas | La mayoría de proyectos actuales |
| **Middleware / Async** | Manual | Redux Thunk / Saga | Integrado y simple |

---

## 2. Context API (Solución nativa de React)

Ideal para estado global simple (usuario, tema, carrito pequeño, etc.).

```jsx
// context/AuthContext.js
import { createContext, useState, useContext } from 'react';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => useContext(AuthContext);
```

**Uso:**

```jsx
function Navbar() {
  const { user, logout } = useAuth();

  return (
    <div>
      Bienvenido {user?.name}
      <button onClick={logout}>Salir</button>
    </div>
  );
}
```

**Problema común:** Si el contexto cambia, todos los componentes que lo consumen se re-renderizan (aunque no usen todos los valores).

**Solución:** Dividir en varios contextos pequeños o usar `useMemo`.

---

## 3. Redux (El clásico)

Muy potente, pero hoy en día se usa menos en proyectos nuevos por la cantidad de código.

```jsx
// store.js
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';

export const store = configureStore({
  reducer: { user: userReducer }
});
```

```jsx
// userSlice.js
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'user',
  initialState: null,
  reducers: {
    login: (state, action) => action.payload,
    logout: () => null
  }
});

export const { login, logout } = userSlice.actions;
export default userSlice.reducer;
```

**Uso con hooks:**

```jsx
import { useSelector, useDispatch } from 'react-redux';

function Profile() {
  const user = useSelector(state => state.user);
  const dispatch = useDispatch();

  return (
    <button onClick={() => dispatch(login(userData))}>
      Login
    </button>
  );
}
```

**Ventajas:** Predictibilidad, DevTools increíbles, middleware potente, gran ecosistema.

**Desventajas:** Mucho código repetitivo.

---

## 4. Zustand (La opción más recomendada actualmente)

Es la que más recomiendo en 2025–2026 para la mayoría de proyectos. Simple, rápido y potente.

```bash
npm install zustand
```

```jsx
// store.js
import { create } from 'zustand';

export const useStore = create((set) => ({
  user: null,
  cart: [],

  login: (userData) => set({ user: userData }),
  logout: () => set({ user: null }),

  addToCart: (product) => set((state) => ({
    cart: [...state.cart, product]
  }))
}));
```

**Uso (muy limpio):**

```jsx
function Navbar() {
  const { user, logout } = useStore();

  return (
    <div>
      Bienvenido {user?.name}
      <button onClick={logout}>Salir</button>
    </div>
  );
}

function Cart() {
  const cart = useStore(state => state.cart);      // Solo suscribe a 'cart'
  const addToCart = useStore(state => state.addToCart);
}
```

**Ventajas de Zustand:**

- Solo se re-renderiza el componente que usa esa parte del estado.
- No necesitas *providers* en la mayoría de casos.
- Muy fácil de usar con TypeScript.
- Soporta middleware (*persist*, *devtools*, etc.).

---

## 5. Recomendación según tamaño de proyecto

| Tipo de proyecto | Recomendación |
|---|---|
| Proyecto pequeño / mediano | Zustand (o Context si es muy simple) |
| Proyecto grande / enterprise | Zustand o Redux Toolkit (si ya tienes equipo acostumbrado a Redux) |
| Necesitas mucha lógica asíncrona compleja + DevTools avanzados | Redux Toolkit + RTK Query |
| Solo tema / idioma / autenticación | Context API |

---

## 6. Mi recomendación actual (2026)

Empieza con **Zustand**. Es la mejor relación simplicidad / potencia del mercado ahora mismo.
