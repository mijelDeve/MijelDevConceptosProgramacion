# Conventional Commits

**Conventional Commits** es una convención para escribir mensajes de commit de forma estandarizada, facilitando la lectura del historial, la generación automática de changelogs y el versionado semántico (SemVer).

## Estructura

```
<tipo>[ámbito opcional]: <descripción>

[cuerpo opcional]

[footer opcional]
```

### Ejemplos

```
feat(auth): agregar autenticación con Google
fix(api): corregir error al validar tokens expirados
docs: actualizar guía de instalación
```

## Tipos más comunes

| Tipo       | Descripción                                          |
| ---------- | ---------------------------------------------------- |
| `feat`     | Nueva funcionalidad                                  |
| `fix`      | Corrección de errores                                |
| `docs`     | Cambios en documentación                             |
| `style`    | Formato de código (espacios, indentación, etc.)      |
| `refactor` | Reestructuración sin cambiar comportamiento           |
| `test`     | Agregar o modificar pruebas                          |
| `chore`    | Tareas de mantenimiento (dependencias, configuración) |
| `build`    | Cambios relacionados con compilación o dependencias   |
| `ci`       | Cambios en pipelines de integración continua         |
| `perf`     | Mejoras de rendimiento                               |

## Breaking Changes

Cuando un cambio rompe compatibilidad hacia atrás:

```
feat!: cambiar formato de autenticación
```

o

```
feat(auth): cambiar formato de autenticación

BREAKING CHANGE: los tokens anteriores ya no son válidos
```

## Relación con Semantic Versioning

| Tipo                                    | Incremento SemVer |
| --------------------------------------- | ----------------- |
| `fix`                                   | PATCH (`1.0.1`)   |
| `feat`                                  | MINOR (`1.1.0`)   |
| `BREAKING CHANGE` o `!`                 | MAJOR (`2.0.0`)   |

## Beneficios

- Historial de Git más legible.
- Facilita revisiones de código.
- Permite generar changelogs automáticamente.
- Automatiza el versionado de aplicaciones.
- Hace más predecibles los releases.

## Ejemplos reales

```
feat(users): agregar búsqueda por correo
fix(payments): corregir cálculo de impuestos
refactor(auth): simplificar validación de JWT
test(api): agregar pruebas para endpoint de login
docs(readme): actualizar requisitos de instalación
chore: actualizar dependencias de Laravel
```

> **Regla práctica:** si agregas una funcionalidad usa `feat`, si corriges un bug usa `fix`, y para cambios internos que no afectan el comportamiento usa `refactor` o `chore` según corresponda.
