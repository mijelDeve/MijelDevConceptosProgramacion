# DevTools en Zustand

---

## ¿Para qué sirve el middleware devtools?

El middleware `devtools` te permite conectar tu store de Zustand con la extensión **Redux DevTools** del navegador.

Es una de las herramientas más útiles durante el desarrollo, porque te da superpoderes para inspeccionar y depurar tu estado.

---

## ¿Qué puedes hacer con DevTools?

- Ver el **estado actual completo** en tiempo real.
- Ver el **historial** de todas las acciones que han modificado el estado.
- Ver exactamente **qué cambió** en cada acción (diff).
- **Viajar en el tiempo** (time travel): saltar hacia atrás o adelante entre estados.
- **Despachar acciones** manualmente.
- **Exportar / importar** el estado.
- Ver **logs** de forma bonita.

---

## Cómo usarlo (Ejemplo práctico)

### 1. Instalar (solo en desarrollo)

No necesitas instalar nada extra. El middleware `devtools` ya viene incluido en Zustand.

### 2. Aplicarlo al store

```tsx
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useCartStore = create(
  devtools(                    // ← Envolvemos con devtools
    (set, get) => ({
      items: [],
      total: 0,
      discountCode: null,

      addToCart: (product) =>
        set(
          (state) => {
            state.items.push(product)
            state.total += product.price
          },
          false,           // opcional: skip logging
          'addToCart'      // nombre de la acción (aparece en DevTools)
        ),

      applyDiscount: (code) =>
        set((state) => {
          state.discountCode = code
        }, false, 'applyDiscount')
    }),
    { name: 'Cart Store' }     // ← Nombre que aparecerá en DevTools
  )
)
```

---

## Cómo verlo en el navegador

1. Instala la extensión **Redux DevTools** en Chrome/Firefox.
2. Abre tu aplicación.
3. Abre las DevTools del navegador (`F12`) → pestaña **Redux**.
4. Verás tu store con el nombre que pusiste (`Cart Store`).

---

## Ventajas principales

| Beneficio | Descripción |
|-----------|-------------|
| Depuración rápida | Ves exactamente qué acción cambió qué cosa |
| Time Travel | Puedes volver a un estado anterior |
| Nombre de acciones | Ayuda a identificar qué acción se disparó |
| Múltiples stores | Puedes ver todos tus stores en la misma herramienta |
| Muy ligero | Solo se usa en desarrollo |

---

## Recomendación de uso

```tsx
// En desarrollo
const useStore = create(
  devtools( ... tu store ... )
)

// En producción (opcional)
const useStore = create(
  process.env.NODE_ENV === 'development' 
    ? devtools(...) 
    : ...
)
```

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| `devtools` | Middleware que conecta Zustand con Redux DevTools |
| Uso principal | Depurar durante el desarrollo |
| Funciones clave | Ver estado, historial de acciones, time travel |
| Implementación | Solo envuelves tu store con `devtools()` |
| Recomendación | Altamente recomendado usarlo en todos tus proyectos |
