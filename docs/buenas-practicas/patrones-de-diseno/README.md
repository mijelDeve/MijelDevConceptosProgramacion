# Patrones de Diseño

Los patrones de diseño son soluciones reutilizables para problemas comunes en el desarrollo de software. Esta sección los organiza por categorías y los compara entre el desarrollo backend y React.

---

## 🏗️ Arquitectura y Estructura

Definen la estructura general de la aplicación.

| Patrón | Backend | React |
|---|---|---|
| **Arquitectura Limpia / Hexagonal** | Separa el código en capas (dominio, aplicación, infraestructura) para aislar la lógica de negocio de detalles externos como bases de datos o APIs | — |
| **MVC (Model-View-Controller)** | Clásico para web apps, separa los datos (Modelo), la lógica de control (Controlador) y la interfaz (Vista) | — |
| **Container & Presentational** | — | Separa la lógica y los datos (Container) de la pura presentación visual (Presentational) |
| **Componentes Compuestos (Compound)** | — | Permite crear componentes flexibles que trabajan juntos y comparten un estado implícito (como un `<Select>` con sus `<Option>`) |

[Detalle: Arquitectura Limpia / Hexagonal](arquitectura-limpia-hexagonal) · [MVC](mvc) · [Container & Presentational](container-presentational) · [Componentes Compuestos](componentes-compuestos)

---

## 🧩 Creación de Objetos

Centralizan cómo se crean las instancias de los objetos.

| Patrón | Backend | React |
|---|---|---|
| **Inyección de Dependencias** | En lugar de que un objeto cree sus propias dependencias, estas se le "inyectan" desde fuera. Mejora la testabilidad y flexibilidad. Base de frameworks como Spring | — |
| **Factory** | Se utiliza para crear y devolver diferentes tipos de objetos basándose en una condición | Se utiliza para crear y devolver diferentes tipos de componentes o elementos basándose en una condición o tipo de entrada |
| **Hooks Personalizados** | — | Permiten "crear" y reutilizar lógica con estado entre múltiples componentes |

[Detalle: Inyección de Dependencias](inyeccion-dependencias) · [Factory](factory) · [Hooks Personalizados](hooks-personalizados)

---

## 🔄 Comportamiento y Comunicación

Definen cómo los objetos interactúan y se comunican entre sí.

| Patrón | Backend | React |
|---|---|---|
| **Observer** | Permite que un objeto (el "sujeto") notifique automáticamente a otros ("observadores") sobre cambios en su estado. Base de muchos sistemas de eventos | — |
| **Chain of Responsibility** | Da a múltiples objetos la oportunidad de procesar una petición. Los "filtros" (filters) en un middleware son un ejemplo perfecto | — |
| **Observer / Flux** | — | Principio en el corazón de bibliotecas de estado como Redux o Zustand: un cambio de estado notifica a los componentes para que se actualicen |
| **Render Props** | — | Patrón para compartir código entre componentes usando una prop cuyo valor es una función |

[Detalle: Observer](observer) · [Chain of Responsibility](cadena-responsabilidad) · [Flux / Redux](flux-redux) · [Render Props](render-props)

---

## 🛠️ Estructuración y Mejora del Código

Añaden funcionalidades o limpian el código existente.

| Patrón | Backend | React |
|---|---|---|
| **Repository** | Actúa como intermediario entre la lógica de negocio y el acceso a datos. Hace el código más limpio y facilita cambiar de base de datos | — |
| **Decorator** | Permite añadir nuevas responsabilidades a un objeto de forma dinámica, sin modificar su estructura original | — |
| **Higher-Order Component (HOC)** | — | Función que toma un componente y devuelve un nuevo componente "mejorado". Útil para lógica transversal como autenticación |
| **Portal** | — | Permite renderizar hijos en un nodo del DOM fuera de la jerarquía del componente padre, ideal para modales o tooltips |

[Detalle: Repository](repository) · [Decorator](decorator) · [HOC](hoc) · [Portal](portal)

---

## 💡 Consejos para Aplicarlos

- **No son una receta mágica**: Aplicar un patrón donde no es necesario añade complejidad innecesaria. El objetivo es resolver un problema, no usar patrones por usarlos.
- **Los patrones se "esconden"**: Los frameworks modernos ya implementan muchos patrones por ti. Por ejemplo, `@Bean` en Spring Boot es un Singleton, y `React.lazy` con `Suspense` implementan Lazy Loading.
- **Concéntrate en el problema**: Empieza siempre por entender el problema que quieres resolver (ej: "necesito que un componente se actualice cuando otro cambie").
- **Busca la señal**: Cuando te encuentres repitiendo la misma lógica en varios lugares (código espagueti) o una parte del código se vuelve muy compleja, es buena señal de que un patrón puede ayudarte a organizarlo mejor.
