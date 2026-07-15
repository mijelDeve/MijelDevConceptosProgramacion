# Git Stash — Guarda cambios sin hacer un commit

Git Stash es una de las herramientas más útiles cuando estás desarrollando y necesitas cambiar de contexto rápidamente sin hacer un commit incompleto.

Al finalizar esta clase sabrás:

- Qué es git stash.
- Cuándo utilizarlo.
- Cómo guardar, listar, recuperar y eliminar stashes.
- Casos de uso reales.
- Buenas prácticas.

## ¿Qué es Git Stash?

Imagina que estás trabajando en una funcionalidad.

```
feature/login
```

Has modificado varios archivos:

- Login.tsx
- AuthService.ts
- styles.css

Pero de repente tu jefe te dice:

> "Necesitamos corregir un bug urgente en producción."

No puedes cambiar de rama porque Git te dirá que tienes cambios sin guardar.

Tampoco quieres hacer un commit porque tu trabajo aún no está terminado.

Aquí es donde entra **Git Stash**.

Git Stash guarda temporalmente tus cambios y deja el proyecto exactamente como estaba en el último commit.

Es como poner tu trabajo en una mochila para continuar después.

## ¿Cómo funciona?

Supongamos este estado.

```bash
git status
```

Resultado

```
Changes not staged for commit:

modified: Login.tsx
modified: AuthService.ts
modified: styles.css
```

Ahora ejecutas:

```bash
git stash
```

Git hace dos cosas:

1. Guarda todos esos cambios.
2. Limpia tu directorio de trabajo.

Ahora:

```bash
git status
```

Resultado

```
nothing to commit, working tree clean
```

Ahora sí puedes cambiar de rama.

### Primer ejemplo completo

**Antes:**

```
feature/login

Login.tsx (modificado)
AuthService.ts (modificado)
```

Guardamos:

```bash
git stash
```

Cambiamos de rama:

```bash
git checkout hotfix/error-500
```

Arreglamos el bug.

Hacemos commit.

Volvemos:

```bash
git checkout feature/login
```

Recuperamos el trabajo:

```bash
git stash pop
```

Todo vuelve exactamente como estaba.

## Ver los stashes guardados

Puedes tener varios.

```bash
git stash list
```

Ejemplo:

```
stash@{0}: WIP on feature/login
stash@{1}: WIP on dashboard
stash@{2}: WIP on report
```

Cada stash tiene un identificador.

### Darle un nombre

En lugar de dejar el mensaje automático:

```bash
git stash
```

Puedes hacer:

```bash
git stash push -m "Formulario de login"
```

Luego:

```bash
git stash list
stash@{0}: On feature/login: Formulario de login
```

Muchísimo más fácil de identificar.

## Recuperar el último stash

Existen dos comandos importantes.

### git stash apply

`git stash apply` recupera el stash. Pero **NO lo elimina**.

La lista sigue siendo:

```
stash@{0}
```

Puedes volver a usarlo.

### git stash pop

`git stash pop` hace dos cosas:

1. Recupera el stash.
2. Lo elimina.

Es el comando que más se utiliza.

### Diferencia entre apply y pop

```
Apply
┌─────────┐
│  Stash  │
└────┬────┘
     ▼
 Se aplica
     ▼
 El stash sigue existiendo

Pop
┌─────────┐
│  Stash  │
└────┬────┘
     ▼
 Se aplica
     ▼
 Se elimina
```

## Recuperar un stash específico

Supongamos:

```
stash@{0}
stash@{1}
stash@{2}
```

Quieres recuperar el segundo.

```bash
git stash apply stash@{1}
```

O

```bash
git stash pop stash@{1}
```

## Eliminar un stash

Eliminar uno específico:

```bash
git stash drop stash@{0}
```

Eliminar todos:

```bash
git stash clear
```

> ⚠️ Ten cuidado con `clear`, porque elimina todos los stashes y recuperarlos puede ser muy difícil.

## ¿Qué guarda Git Stash?

Por defecto guarda:

- archivos modificados
- archivos preparados (`git add`)
- cambios rastreados (tracked)

Pero **NO guarda** archivos nuevos sin seguimiento (untracked).

Ejemplo:

```
Login.tsx        ✔
Auth.ts          ✔
nuevoArchivo.ts  ❌
```

### Guardar también archivos nuevos

Usa:

```bash
git stash -u
```

o

```bash
git stash --include-untracked
```

Ahora sí guarda:

```
nuevoArchivo.ts
```

### Guardar absolutamente todo

Incluso archivos ignorados (`.gitignore`):

```bash
git stash -a
```

o

```bash
git stash --all
```

## Crear una rama desde un stash

Muy útil.

Supongamos que hiciste un stash hace varios días.

Ahora prefieres convertirlo en una rama nueva.

```bash
git stash branch nueva-feature
```

Git hace:

1. Crea la rama.
2. Aplica el stash.
3. Elimina el stash.

Todo automáticamente.

## Caso práctico 1

Estás desarrollando:

```
feature/payment
```

Has trabajado 3 horas.

Tu líder dice: _Corrige un bug urgente._

Haces:

```bash
git stash
```

Luego:

```bash
git checkout main
```

Después:

```bash
git checkout -b hotfix/error
```

Corriges.

Commit.

Merge.

Regresas:

```bash
git checkout feature/payment
```

Recuperas:

```bash
git stash pop
```

Continúas donde te quedaste.

## Caso práctico 2

Tienes varios desarrollos:

- Login
- Dashboard
- Reportes
- Notificaciones

Guardas varios:

```bash
git stash push -m "Login"
git stash push -m "Dashboard"
git stash push -m "Reportes"
```

Lista:

```
stash@{0}: Reportes
stash@{1}: Dashboard
stash@{2}: Login
```

Puedes recuperar cualquiera.

## Conflictos

Sí.

Los stashes también pueden producir conflictos.

Ejemplo:

Guardaste:

```ts
const name = "Miguel";
```

Mientras tanto alguien hizo:

```ts
const name = "Carlos";
```

Cuando haces:

```bash
git stash pop
```

Git puede responder:

```
CONFLICT
```

Se resuelve igual que cualquier merge.

## Buenas prácticas

✅ Pon nombres a tus stashes:

```bash
git stash push -m "Refactor formulario"
```

✅ No acumules muchos.

Si tienes:

```
stash@{0}
stash@{1}
stash@{2}
stash@{3}
stash@{4}
stash@{5}
```

Probablemente alguno ya no sirve.

✅ Usa `apply` si no estás seguro.

Así puedes volver a intentarlo.

✅ Usa `pop` cuando estés seguro.

✅ Si el trabajo ya está listo, haz un commit.

Git Stash no reemplaza los commits.

## Flujo visual

```
Trabajando
     │
     ▼
Cambios sin commit
     │
     ▼
git stash
     │
     ▼
Working Tree limpio
     │
     ▼
Cambiar de rama
     │
     ▼
Trabajar
     │
     ▼
Volver
     │
     ▼
git stash pop
     │
     ▼
Continuar desarrollo
```

## Comandos más importantes

| Comando | Descripción |
|---|---|
| `git stash` | Guarda los cambios actuales. |
| `git stash push -m "mensaje"` | Guarda los cambios con una descripción. |
| `git stash list` | Lista todos los stashes. |
| `git stash apply` | Aplica el último stash sin eliminarlo. |
| `git stash apply stash@{n}` | Aplica un stash específico. |
| `git stash pop` | Aplica el último stash y lo elimina. |
| `git stash pop stash@{n}` | Aplica y elimina un stash específico. |
| `git stash drop stash@{n}` | Elimina un stash específico. |
| `git stash clear` | Elimina todos los stashes. |
| `git stash -u` | Incluye archivos no rastreados. |
| `git stash -a` | Incluye también archivos ignorados. |
| `git stash branch <nombre>` | Crea una rama a partir de un stash. |
