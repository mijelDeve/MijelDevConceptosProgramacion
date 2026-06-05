# Accesibilidad Web (a11y)

## 1. ¿Qué es la Accesibilidad Web?

La **accesibilidad web** (abreviado como **a11y**, donde "a" + 11 letras + "y") es la práctica de diseñar y desarrollar sitios web y aplicaciones para que **todas las personas** puedan usarlos, independientemente de sus capacidades o discapacidades.

---

## 2. ¿A quién beneficia?

- Personas con **discapacidades visuales** (usan lectores de pantalla como NVDA, VoiceOver, JAWS).
- Personas con **discapacidades motoras** (usan teclado, voz o *switches* en vez de ratón).
- Personas con **discapacidades auditivas** (necesitan subtítulos o transcripciones).
- Personas con **discapacidades cognitivas** o de aprendizaje.
- Personas mayores, usuarios de móviles, conexiones lentas, etc.

---

## 3. Estándar principal — WCAG

El estándar internacional es **WCAG** (*Web Content Accessibility Guidelines*) de la W3C. Actualmente está en la versión 2.2 (y 3.0 en borrador). Se basa en **4 principios clave** (POUR):

| Principio | Significa que… |
|---|---|
| **Perceptible** | Se puede ver/escuchar |
| **Operable** | Se puede usar con teclado, voz, etc. |
| **Comprensible** | Es fácil de entender |
| **Robusto** | Funciona con diferentes tecnologías (incluyendo tecnologías asistivas) |

---

## 4. ¿Qué es ARIA?

**ARIA** = *Accessible Rich Internet Applications*

Es un conjunto de **atributos** (roles, estados y propiedades) que se añaden al HTML para mejorar la accesibilidad, especialmente en interfaces dinámicas y componentes personalizados creados con JavaScript.

> **Importante:** ARIA no cambia la apariencia ni el comportamiento visual del elemento. Solo proporciona información semántica extra a los lectores de pantalla y tecnologías asistivas.

### Regla de oro de ARIA

> *"No uses ARIA si no es necesario"*

Primero intenta usar **elementos HTML nativos** (`<button>`, `<nav>`, `<main>`, `<dialog>`, etc.), porque ya tienen semántica y comportamiento accesible incorporado. Usa ARIA solo cuando no haya un elemento HTML equivalente.

---

## 5. Roles (role)

Indican qué tipo de componente es un elemento.

```html
<div role="button">Click me</div>           <!-- botón personalizado -->
<div role="dialog">...</div>                <!-- modal -->
<div role="tablist">...</div>               <!-- grupo de pestañas -->
<div role="tabpanel">...</div>              <!-- contenido de pestaña -->
<div role="alert">Error: algo falló</div>   <!-- mensaje importante -->
```

### Roles más usados

- `button`, `link`, `checkbox`, `radio`
- `menu`, `menuitem`
- `tab`, `tabpanel`
- `dialog`, `alertdialog`
- `progressbar`, `slider`
- `banner`, `navigation`, `main`, `complementary`, `contentinfo` (*landmarks*)

---

## 6. aria-label

Proporciona un **nombre accesible** (*label*) a un elemento cuando no hay texto visible o el texto visible no es suficientemente descriptivo.

```html
<!-- Icono solo -->
<button aria-label="Cerrar">
  <svg aria-hidden="true">...</svg>
</button>

<!-- Imagen decorativa -->
<img src="like.png" alt="" aria-label="Me gusta">
```

### Cuándo usarlo

- Botones con solo iconos
- Imágenes decorativas o que ya tienen contexto
- Elementos que necesitan un nombre más claro

> **No usar** si ya hay un texto visible (mejor usar `<label>` o `aria-labelledby`).

---

## 7. aria-describedby

Apunta a otro elemento que **describe** o da más información sobre el elemento actual.

```html
<input type="password" id="pass"
       aria-describedby="pass-hint">

<div id="pass-hint">
  La contraseña debe tener al menos 8 caracteres
</div>
```

### Usos comunes

- Mensajes de error o ayuda
- Descripciones largas
- Información adicional (ej. "este campo es obligatorio")

> También existe `aria-labelledby` (similar pero para **nombrar** en vez de describir).

---

## 8. Otros atributos ARIA importantes

| Atributo | Uso principal |
|---|---|
| `aria-hidden="true"` | Oculta elemento a lectores de pantalla |
| `aria-live="polite"` | Anuncia cambios dinámicos automáticamente |
| `aria-expanded="true"` | Indica si un menú / acordeón está abierto |
| `aria-current="page"` | Indica la página actual en navegación |
| `aria-required="true"` | Campo obligatorio |
| `aria-invalid="true"` | Campo con error |

---

## 9. Buenas prácticas

- **Primero HTML semántico, luego ARIA.**
- Prueba siempre con **lector de pantalla** (VoiceOver en Mac, NVDA en Windows, TalkBack en Android).
- Usa herramientas automáticas:
  - **WAVE** (extensión de Chrome)
  - **axe DevTools**
  - **Lighthouse** (en Chrome DevTools)
- No abuses de ARIA (puede empeorar la accesibilidad si se usa mal).
- Mantén sincronizado el estado (ej. si cambias `aria-expanded` con JS, hazlo siempre).

---

## 10. Ejemplo práctico completo

```html
<!-- Modal accesible -->
<div role="dialog"
     aria-labelledby="modal-title"
     aria-describedby="modal-desc"
     aria-modal="true">

  <h2 id="modal-title">Confirmar eliminación</h2>
  <p id="modal-desc">¿Estás seguro? Esta acción no se puede deshacer.</p>

  <button aria-label="Cancelar">Cancelar</button>
  <button>Eliminar</button>
</div>
```
