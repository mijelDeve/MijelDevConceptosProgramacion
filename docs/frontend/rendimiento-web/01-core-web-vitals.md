# Core Web Vitals

## 1. ¿Qué es Web Performance?

**Web Performance** (Rendimiento Web) es la disciplina que se encarga de hacer que una página web sea rápida, fluida y agradable de usar.

No se trata solo de que "cargue rápido", sino de la experiencia completa del usuario:

- ¿Cuánto tarda en ver el contenido principal?
- ¿Puede interactuar con la página sin que se sienta lenta?
- ¿Se mueve todo el contenido de forma inesperada mientras navega?
- ¿La página se siente estable y *responsive*?

Un buen rendimiento web:

- Mejora la experiencia del usuario (UX).
- Aumenta la conversión (ventas, registros, tiempo en página).
- Mejora el SEO (Google lo usa como factor de ranking).

---

## 2. ¿Qué son los Core Web Vitals?

Los **Core Web Vitals** son un conjunto de métricas específicas que Google creó para medir la experiencia real de los usuarios en la web. Son parte de las *"Page Experience Signals"* (señales de experiencia de página).

Google considera que estas métricas son tan importantes que afectan directamente el **posicionamiento SEO** en móviles y desktops.

Actualmente (2026) los Core Web Vitals principales son tres:

| Métrica | Nombre completo | Qué mide | Valor "Bueno" |
|---|---|---|---|
| **LCP** | Largest Contentful Paint | Velocidad de carga del contenido principal | ≤ **2.5 segundos** |
| **INP** | Interaction to Next Paint | Capacidad de respuesta a interacciones | ≤ **200 milisegundos** |
| **CLS** | Cumulative Layout Shift | Estabilidad visual (movimientos inesperados) | ≤ **0.1** |

> **Nota:** FID (First Input Delay) fue reemplazado por INP en 2024 porque INP es una métrica mucho más completa.

---

## 3. LCP — Largest Contentful Paint

### ¿Qué es?

Mide cuánto tarda en cargarse el **elemento más grande** de la parte visible de la pantalla (el *"hero"* o contenido principal).

- Normalmente es una imagen grande, un bloque de texto grande, un video, etc.
- No es el tiempo total de carga de la página, sino el momento en que el usuario ve el contenido más importante.

### Valores de referencia

| Estado | Tiempo |
|---|---|
| ✅ Bueno | ≤ 2.5s |
| ⚠️ Necesita mejora | 2.5s – 4s |
| ❌ Pobre | > 4s |

### Causas comunes de LCP malo

- Imágenes sin optimizar (pesadas, sin `width` / `height`).
- JavaScript que bloquea el renderizado (*render-blocking scripts*).
- Fuentes web personalizadas que tardan en cargar.
- Servidor lento o hosting barato.

---

## 4. INP — Interaction to Next Paint (reemplazó a FID)

### ¿Qué es?

Mide la **capacidad de respuesta** de la página a las interacciones del usuario (clics, toques, pulsaciones de teclas).

INP mira el tiempo que pasa desde que el usuario interactúa hasta que la página puede pintar el siguiente frame (es decir, responde visualmente).

### Valores de referencia

| Estado | Tiempo |
|---|---|
| ✅ Bueno | ≤ 200ms |
| ⚠️ Necesita mejora | 200ms – 500ms |
| ❌ Pobre | > 500ms |

### Por qué es importante

Un usuario puede hacer clic en un botón y sentir que "no pasa nada" durante medio segundo. Eso genera frustración.

### Causas comunes

- JavaScript pesado ejecutándose en el hilo principal.
- *Event listeners* mal optimizados.
- Animaciones complejas o librerías grandes.

---

## 5. CLS — Cumulative Layout Shift

### ¿Qué es?

Mide **cuánto se mueve el contenido** de forma inesperada mientras el usuario está en la página.

**Ejemplo clásico:** estás leyendo un artículo, haces clic en un enlace y de repente todo salta porque cargó un anuncio o una imagen grande sin dimensiones definidas.

### Valores de referencia

| Estado | Puntuación |
|---|---|
| ✅ Bueno | ≤ 0.1 |
| ⚠️ Necesita mejora | 0.1 – 0.25 |
| ❌ Pobre | > 0.25 |

### Causas comunes

- Imágenes o videos sin `width` y `height` (o `aspect-ratio`).
- Anuncios, iframes o widgets que se cargan tarde.
- Contenido inyectado dinámicamente sin reservar espacio.

---

## 6. Resumen rápido

Para recordar:

| Métrica | Pregunta clave |
|---|---|
| **LCP** | ¿Qué tan rápido veo el contenido principal? |
| **INP** | ¿Qué tan *responsive* es la página cuando interactúo? |
| **CLS** | ¿La página se mueve de forma molesta? |

---

## 7. ¿Cómo medirlos?

- **PageSpeed Insights** (recomendado para principiantes): [https://pagespeed.web.dev](https://pagespeed.web.dev)
- Chrome DevTools → pestaña Performance (grabando interacción)
- Web Vitals Chrome Extension (muy útil)
- Lighthouse (en DevTools)
- En producción: **Google Search Console** te muestra los datos reales de usuarios
