# Estrategias de Optimización Frontend

## 1. LCP — Largest Contentful Paint

### Teoría

Mide el tiempo que tarda en renderizarse el **elemento más grande visible** en la *viewport* (generalmente el *hero image*, título principal o bloque grande de texto).

No es el tiempo de carga total de la página, sino cuándo el usuario **percibe** que el contenido principal ya está visible.

- ✅ **Buen valor:** ≤ 2.5 segundos (75th percentile de usuarios reales)

### Factores que lo afectan

- Tiempo de respuesta del servidor (TTFB)
- Recursos bloqueantes (CSS/JS en `<head>`)
- Imágenes pesadas o sin optimizar
- *Render-blocking resources*

### Mejoras prácticas (ordenadas por impacto)

#### Optimiza la imagen LCP (el mayor impacto)

- Nunca uses `loading="lazy"` en la imagen LCP.
- Usa `fetchpriority="high"` y `preload`:

```html
<link rel="preload" href="hero.jpg" as="image" fetchpriority="high">
<img src="hero.jpg" width="1200" height="600" alt="..." fetchpriority="high" loading="eager">
```

#### Imágenes modernas

- Convierte a **WebP** o **AVIF**.
- Usa `srcset` + `sizes`.
- Comprime con herramientas como Squoosh, ImageOptim o Sharp.

#### Reduce TTFB (Time to First Byte)

- Hosting rápido + CDN.
- Caching (*edge caching* si usas Vercel, Cloudflare, etc.).
- SSR o SSG (Next.js, Nuxt, etc.).

#### Elimina recursos bloqueantes

- *Inline critical CSS*.
- `Defer` JS no crítico: `<script defer>` o `type="module"`.
- Minimiza y *tree-shake* tu bundle.

#### Otras

- Preload fuentes críticas.
- Usa `font-display: swap`.
- Optimiza *third-party scripts*.

> **Herramientas:** PageSpeed Insights + Lighthouse (pestaña Performance en DevTools).

---

## 2. INP — Interaction to Next Paint

### Teoría

Mide la **capacidad de respuesta** de la página durante toda la sesión del usuario (no solo la primera interacción como hacía FID).

Calcula el tiempo desde que el usuario interactúa (click, tap, tecla) hasta que el navegador pinta el siguiente *frame*.

- ✅ **Buen valor:** ≤ 200 milisegundos

### Factores que lo afectan

- JavaScript bloqueando el hilo principal (*long tasks* > 50ms)
- *Event handlers* pesados
- DOM grande o *layout thrashing*
- *Third-party scripts* (analytics, chats, ads)

### Mejoras prácticas

#### Identifica interacciones lentas

- Usa la extensión **Web Vitals** de Chrome.
- En DevTools → Performance → graba una interacción.
- Busca *long tasks* en el *flame graph*.

#### Divide el trabajo del main thread

- Usa `requestIdleCallback` para tareas no críticas.
- Divide tareas largas con `setTimeout` o `scheduler.yield()` (nuevo API).
- Mueve trabajo pesado a **Web Workers**.

#### Optimiza event handlers

```js
// Malo
button.addEventListener('click', heavyFunction);

// Mejor
button.addEventListener('click', () => {
  requestIdleCallback(heavyFunction);
});
```

#### Otras técnicas potentes

- Reduce tamaño del DOM (evita miles de nodos).
- Usa `will-change` con cuidado y prefiere animaciones con `transform` y `opacity`.
- Defer o *lazy-load* scripts de terceros.
- **Code splitting** agresivo (`React.lazy`, *dynamic imports*).
- Evita *layout thrashing* (lee/escribe estilos en batch).

#### En frameworks (React, Vue, etc.)

- Memoiza componentes (`React.memo`, `useMemo`, `useCallback`).
- Evita re-renders innecesarios.
- Usa virtualización (`react-window`) para listas largas.

---

## 3. CLS — Cumulative Layout Shift

### Teoría

Mide la suma de **movimientos inesperados** del contenido visible. Cada vez que un elemento se mueve sin que el usuario lo provoque, se suma un *"shift score"*.

- ✅ **Buen valor:** ≤ 0.1

### Factores que lo afectan

- Imágenes, videos o iframes sin dimensiones reservadas
- Contenido dinámico (anuncios, comentarios, carousels)
- Fuentes web que cargan tarde y cambian el tamaño del texto
- Contenido inyectado por JS sin reservar espacio

### Mejoras prácticas

#### Reserva espacio siempre (la más importante)

```html
<img src="..." width="800" height="600" alt="...">
```

O con CSS:

```css
img {
  aspect-ratio: 800 / 600; /* moderno y recomendado */
  width: 100%;
  height: auto;
}
```

#### Fuentes

- `font-display: swap` (o `optional`).
- Preload fuentes críticas.
- *Self-host* fonts cuando sea posible.
- Usa `size-adjust` para igualar *fallback fonts*.

#### Contenido dinámico

- Reserva espacio mínimo para ads, iframes, widgets.
- Usa **skeletons UI** (*placeholders*) que coincidan con el tamaño final.
- Evita insertar contenido encima del *fold* visible.

#### Animaciones

- Usa solo `transform` y `opacity` (no cambian layout).
- Evita animar `height`, `width`, `margin`, `top`, etc.

#### En SPAs (React, etc.)

- Mantén layouts estables durante navegación.
- Usa skeletons que coincidan exactamente con el contenido final.

---

## 4. Plan de acción recomendado

1. **Audita** tu sitio actual con PageSpeed Insights y Lighthouse.
2. **Prioriza:**
   - **LCP** primero (más impacto en percepción y SEO)
   - **CLS** segundo (muy molesto para usuarios)
   - **INP** tercero (más complejo)
3. **Monitorea en producción:**
   - Google Search Console (datos reales)
   - Web Vitals library + tu analytics
4. **Itera y mide** después de cada cambio.
