# Git Cherry-Pick — Copia commits específicos

La idea clave es:

> No traes toda la rama; traes únicamente los commits que tú eliges.

Al finalizar esta clase sabrás:

- Qué es `git cherry-pick`.
- En qué se diferencia de `git merge` y `git rebase`.
- Cómo copiar uno o varios commits a otra rama.
- Cómo aplicar un rango de commits.
- Cómo resolver conflictos y cancelar un cherry-pick.
- Cómo usarlo en un flujo con Pull Requests.

## 1. Imagina este escenario

Tienes:

```
main
  │
  A──B──C
       \
        D──E──F  feature
```

Estás trabajando en `feature` y tienes:

- `D` → cambio de login
- `E` → cambio de estilos
- `F` → arreglo de un bug

Pero ahora estás en `main` y solo quieres traer el arreglo del bug (`F`).

Normalmente harías:

```bash
git checkout main
```

y luego:

```bash
git cherry-pick F
```

El resultado sería:

```
main
  │
  A──B──C────F'
       \
        D──E──F  feature
```

Fíjate en algo importante:

> `F'` NO es exactamente el mismo commit que `F`.

Git crea un nuevo commit en `main` que contiene los mismos cambios que `F`.

## 2. ¿Qué hace realmente cherry-pick?

Supongamos que tienes:

```
A ── B ── C ── D
              ↑
          quiero este
```

Si estás parado en `C`:

```bash
git cherry-pick D
```

Git básicamente dice:

> "Voy a tomar los cambios introducidos por `D` y aplicarlos sobre mi posición actual."

Entonces:

```
A ── B ── C ── D'
```

Donde `D'` contiene los cambios de `D`.

## 3. Un ejemplo más real con ramas

Imagina que tienes:

```
main
  │
  A──B──C
       \
        D──E──F
             feature
```

En `feature` hiciste:

```bash
git commit -m "feat: add users page"
```

Después:

```bash
git commit -m "feat: add user filters"
```

Y finalmente:

```bash
git commit -m "fix: fix user filter"
```

Ahora quieres llevar solo el fix a `main`.

Primero:

```bash
git checkout main
```

Después buscas el commit:

```bash
git log --oneline
```

Podrías ver:

```
a8f32d1 fix: fix user filter
72ab921 feat: add user filters
31c4e21 feat: add users page
...
```

Entonces:

```bash
git cherry-pick a8f32d1
```

Ahora:

```
main
  │
  A──B──C──a8f32d1'
       \
        D──E──F
             feature
```

Y `main` tiene únicamente el fix.

## 4. ¿Por qué es útil?

Principalmente cuando no quieres fusionar toda una rama.

Por ejemplo:

`feature-payment` tiene:

```
A ── B ── C ── D ── E
         ↑       ↑
       feature  otro cambio
```

Pero descubres que el commit `C` tiene un bugfix que necesitas urgentemente en producción.

En lugar de:

```bash
git merge feature-payment
```

puedes hacer:

```bash
git cherry-pick <commit-C>
```

Y solamente llevas ese cambio.

## 5. Diferencia entre merge, rebase y cherry-pick

Esta es probablemente la parte más importante.

### merge

Dice:

> "Quiero juntar estas dos ramas."

```
A──B──C────M
    \     /
     D──E
```

Usas:

```bash
git merge feature
```

### rebase

Dice:

> "Quiero mover mis commits para que parezca que empecé desde otro punto."

```
A──B──C
       \
        D──E
```

puede convertirse en:

```
A──B──C──D'──E'
```

Usas:

```bash
git rebase main
```

### cherry-pick

Dice:

> "De esa rama solo quiero ESTE commit."

```
A──B──C
    \
     D──E──F
```

Quieres `E`:

```
A──B──C──E'
    \
     D──E──F
```

Usas:

```bash
git cherry-pick <hash-de-E>
```

## 6. ¿Qué pasa si necesito varios commits?

Puedes hacer:

```bash
git cherry-pick abc123
git cherry-pick def456
git cherry-pick ghi789
```

O también:

```bash
git cherry-pick abc123 def456 ghi789
```

Por ejemplo:

```bash
git cherry-pick a1b2c3 d4e5f6
```

Aplicará ambos commits.

## 7. También puedes hacer un rango

Supongamos:

```
A──B──C──D──E──F
```

Quieres:

```
D
E
F
```

Puedes hacer:

```bash
git cherry-pick D^..F
```

El `^` es importante porque Git interpreta los rangos de una forma particular.

## 8. ¿Y qué pasa si hay conflictos?

Esta es una situación muy común.

Supongamos:

```
main:
A──B──C

feature:
   D
```

Intentas:

```bash
git cherry-pick D
```

pero el cambio de `D` entra en conflicto con modificaciones que hiciste en `C`.

Git puede decirte algo como:

```
CONFLICT (content): Merge conflict in users.ts
```

En ese momento:

**1. Revisas los conflictos**

```bash
git status
```

**2. Resuelves manualmente el archivo**

Por ejemplo:

```
<<<<<<< HEAD
código de main
=======
código del commit que estás aplicando
>>>>>>> D
```

Dejas el código correcto.

**3. Marcas el archivo como resuelto**

```bash
git add users.ts
```

**4. Continúas el cherry-pick**

```bash
git cherry-pick --continue
```

## 9. ¿Y si me arrepiento?

Puedes cancelar el cherry-pick:

```bash
git cherry-pick --abort
```

Esto es muy útil si el conflicto es demasiado complicado.

Te devuelve al estado anterior al cherry-pick.

## 10. Algo MUY importante con los PR

Como trabajas con Pull Requests, aquí es donde cherry-pick empieza a tener mucho sentido.

Imagina:

```
main
 │
 A──B──C
      \
       D──E──F
          feature
```

Creas un PR:

```
feature → main
```

Pero antes de aprobarlo descubres que `E` contiene un bugfix independiente que necesitas también en otra rama:

```
hotfix
```

Puedes hacer:

```bash
git checkout hotfix
git cherry-pick <hash-de-E>
git push
```

Y después crear un PR:

```
hotfix → main
```

Esto permite reutilizar un commit específico sin fusionar toda la rama.

## 11. Una situación MUY común

Supongamos que tienes:

```
main
   A──B──C

develop
   A──B──C──D──E──F

feature
   A──B──C──G──H
```

En `develop` hiciste:

- `D` = nueva funcionalidad
- `E` = cambio de UI
- `F` = bugfix crítico

Y quieres llevar solamente `F` a `feature`.

Haces:

```bash
git checkout feature
```

Luego:

```bash
git cherry-pick <hash-F>
```

Resultado:

```
main
   A──B──C

develop
   A──B──C──D──E──F

feature
   A──B──C──G──H──F'
```

Esto es exactamente el tipo de problema para el que cherry-pick es muy útil.

## 12. La forma mental más sencilla

Quédate con esta comparación:

| Comando | Significado |
|---|---|
| `merge` | "Quiero toda la rama" |
| `rebase` | "Quiero reorganizar mis commits" |
| `cherry-pick` | "Quiero ESTE commit" |

O todavía más simple:

> cherry-pick = copiar el cambio de un commit y convertirlo en un nuevo commit en mi rama actual.

Y algo fundamental:

```bash
git cherry-pick <commit>
```

siempre se aplica sobre la rama en la que estás parado.

Por eso normalmente haces:

```bash
git checkout rama-destino
git cherry-pick <commit>
```

No importa tanto de qué rama vino originalmente el commit; lo importante es dónde estás parado cuando ejecutas cherry-pick.

## Ejemplo completo: llevar un solo commit de `rama-A` a `main`

Si tienes un commit en `rama-A` y quieres llevar solo ese commit a `main`, el proceso sería:

Ejemplo:

```
main
A ── B ── C

rama-A
A ── B ── C ── D ── E
                 ↑
          quiero solamente D
```

Quieres llevar `D` a `main`, pero NO quieres llevar `E`.

### 1. Obtén el hash del commit

Estando en `rama-A`:

```bash
git log --oneline
```

Por ejemplo:

```
8f31abc feat: agregar filtro de usuarios
92ab123 fix: corregir validación
```

Supongamos que quieres `8f31abc`.

### 2. Cámbiate a main

```bash
git checkout main
```

O:

```bash
git switch main
```

Esto es importante: el cherry-pick se aplica sobre la rama en la que estás actualmente.

### 3. Actualiza main

Si trabajas con remoto:

```bash
git pull origin main
```

Así te aseguras de estar trabajando sobre el `main` más reciente.

### 4. Haz el cherry-pick

```bash
git cherry-pick 8f31abc
```

Git tomará los cambios que hizo `8f31abc` y los aplicará sobre `main`.

Ahora tendrás:

```
main
A ── B ── C ── D'

rama-A
A ── B ── C ── D ── E
```

`D'` es un nuevo commit en `main`.

### 5. Subes main

```bash
git push origin main
```

Aunque en un flujo con PR normalmente no deberías hacer push directamente a `main`. Lo habitual sería crear una rama para ese cherry-pick:

```bash
git switch main
git pull origin main
git switch -c hotfix/fix-usuarios
git cherry-pick 8f31abc
git push -u origin hotfix/fix-usuarios
```

Y luego haces:

```
hotfix/fix-usuarios
        │
        │ PR
        ↓
       main
```

### En resumen

Si tienes:

```
rama-A:  A ── B ── C ── D ── E
                         ↑
                       quiero
```

Harías:

```bash
git switch main
git pull origin main
git switch -c hotfix/mi-cambio
git cherry-pick <hash-de-D>
git push -u origin hotfix/mi-cambio
```

Y creas el PR de `hotfix/mi-cambio` → `main`.

Así `E` se queda en `rama-A` y solo `D` viaja hacia `main`.

## Resumen mental

- **merge**: "Une dos historias". Fusiona toda la rama.
- **rebase**: "Toma mis commits y vuelve a reproducirlos encima de otra base". Reescribe el historial.
- **cherry-pick**: "Copia solo este commit y aplícalo sobre mi rama actual". Crea un commit nuevo.

## Comandos más importantes

| Comando | Descripción |
|---|---|
| `git cherry-pick <hash>` | Copia un commit a la rama actual. |
| `git cherry-pick <hash1> <hash2>` | Copia varios commits. |
| `git cherry-pick <inicio>^..<fin>` | Copia un rango de commits. |
| `git cherry-pick --continue` | Continúa tras resolver conflictos. |
| `git cherry-pick --abort` | Cancela el cherry-pick y vuelve al estado original. |
| `git log --oneline` | Busca el hash del commit que quieres copiar. |
