# Git Rebase — Reescribe tu historial

`git rebase` suele ser uno de los comandos que más confusión genera al principio, pero una vez entiendes qué hace con el historial, todo empieza a tener sentido.

Al finalizar esta clase sabrás:

- Qué es git rebase.
- En qué se diferencia de `git merge`.
- Por qué los commits cambian de hash.
- Cómo resolver conflictos durante un rebase.
- Qué es el rebase interactivo y sus acciones principales.
- Cuándo usar merge y cuándo usar rebase.
- El flujo típico de trabajo con rebase.

## ¿Qué hace git rebase?

Imagina este escenario:

```
main
A --- B --- C

feature
       \
        D --- E
```

`main` tiene los commits A, B y C.
Desde B creaste una rama `feature`.
En `feature` hiciste D y E.

Mientras trabajabas, otro desarrollador hizo un commit F en `main`:

```
main
A --- B --- C --- F

feature
       \
        D --- E
```

Ahora quieres actualizar tu rama.

### Opción 1: git merge

Si haces:

```bash
git checkout feature
git merge main
```

Obtendrás algo así:

```
A --- B --- C --- F
       \         \
        D --- E --- M
```

Donde M es un **Merge Commit**.

**Ventajas:**

- No modifica el historial.
- Es muy seguro.

**Desventajas:**

- El historial puede llenarse de commits de merge.

### Opción 2: git rebase

Si haces:

```bash
git checkout feature
git rebase main
```

Git hace algo muy distinto.

**Paso 1**

"Quita" temporalmente tus commits.

```
A --- B --- C --- F

(feature sin D y E)
```

**Paso 2**

Mueve la base de tu rama hasta el último commit de `main`.

**Paso 3**

Vuelve a aplicar tus commits uno por uno.

**Resultado:**

```
A --- B --- C --- F --- D' --- E'
```

Observa que ya no son exactamente D y E.

Ahora son:

```
D'
E'
```

Porque Git los volvió a crear encima de F.

Por eso se dice que **rebase reescribe la historia**.

## ¿Por qué los commits cambian?

Cada commit tiene un hash.

Por ejemplo:

```
D
hash: a91d4c

E
hash: f12abc
```

Después del rebase:

```
D'
hash: 7ca21d

E'
hash: 98ef12
```

El contenido puede ser el mismo, pero el padre cambió, por lo que Git genera nuevos hashes.

### Visualmente

**Antes:**

```
main
A --- B --- C

feature
       \
        D --- E
```

**Después del rebase:**

```
A --- B --- C --- D --- E
```

Parece que siempre hubieras trabajado sobre `main`.

Ese es precisamente el objetivo.

## ¿Qué hace internamente?

Cuando ejecutas:

```bash
git rebase main
```

Git realiza algo equivalente a:

1. Encuentra el ancestro común.
2. Guarda tus commits (D, E).
3. Mueve la rama a `main`.
4. Aplica D.
5. Aplica E.

Es como hacer un `git cherry-pick` de cada commit automáticamente.

## ¿Qué ocurre si hay conflictos?

Supón que tanto tú como otra persona modificaron el mismo archivo.

Durante el rebase aparecerá algo como:

```
CONFLICT (content)
```

Git se detiene.

Debes:

```bash
# resolver conflictos

git add .

git rebase --continue
```

Si vuelve a haber otro conflicto:

1. Resolver.
2. `git add .`
3. `git rebase --continue`

Y así hasta terminar.

### ¿Y si me equivoqué?

Puedes cancelar todo:

```bash
git rebase --abort
```

Todo vuelve exactamente como estaba.

## ¿Qué significa "rebase interactivo"?

Es una versión mucho más potente.

```bash
git rebase -i HEAD~4
```

Abre los últimos cuatro commits.

Por ejemplo:

```
pick a1 Primer commit
pick b2 Segundo commit
pick c3 Tercer commit
pick d4 Cuarto commit
```

Puedes cambiar `pick` por distintas acciones.

### pick

Deja el commit tal cual.

```
pick a1 Primer commit
```

### reword

Cambiar únicamente el mensaje.

```
reword a1
```

### edit

Detenerte para modificar el contenido de ese commit.

```
edit b2
```

### drop

Eliminar un commit.

```
drop b2
```

### squash

Fusionar commits.

**Antes:**

```
Agregar login
Corregir login
Cambiar botón
```

**Después:**

```
Agregar login completo
```

### fixup

Como `squash`, pero descarta el mensaje del commit que se fusiona.

Muy utilizado para limpiar historial antes de abrir un Pull Request.

## ¿Cuándo usar merge y cuándo rebase?

**Usa rebase cuando:**

- Quieres un historial limpio y lineal.
- Vas a actualizar tu rama con los cambios más recientes de `main`.
- Aún no has compartido tus commits (o sabes lo que implica reescribir el historial).

**Usa merge cuando:**

- Quieres preservar exactamente cómo evolucionó el historial.
- Estás integrando ramas compartidas.
- No quieres reescribir commits existentes.

### Regla importante

> ⚠️ No hagas rebase sobre commits que otras personas ya están usando.

Por ejemplo:

1. Subes una rama con 10 commits.
2. Tu compañero la descarga.
3. Tú haces:

   ```bash
   git rebase
   git push --force
   ```

Ahora tu compañero tiene un historial distinto al del servidor y tendrá que resolver esa divergencia. Por eso, el rebase sobre ramas compartidas debe hacerse con cuidado.

## Flujo típico de trabajo

Supongamos que estás desarrollando una funcionalidad y `main` recibió nuevos cambios:

```bash
git checkout main
git pull origin main

git checkout feature

git rebase main
```

Si hay conflictos:

```bash
# resolver conflictos
git add .

git rebase --continue
```

Al finalizar, si tu rama ya estaba publicada en el remoto:

```bash
git push --force-with-lease
```

Se recomienda `--force-with-lease` en lugar de `--force`, porque verifica que no sobrescribas cambios remotos que no conoces.

## Resumen mental

Piensa en estas dos operaciones así:

- **merge**: "Une dos historias". Mantiene ambas ramas y crea un commit de unión.
- **rebase**: "Toma mis commits y vuelve a reproducirlos encima de otra base". El historial queda lineal, pero los commits se recrean con nuevos identificadores.

Esa diferencia conceptual explica casi todos los comportamientos de `git rebase`. Una vez entiendes que no mueve los commits, sino que los vuelve a crear sobre otra base, el resto (cambios de hash, conflictos, `push --force-with-lease`, etc.) resulta mucho más intuitivo.

## Comandos más importantes

| Comando | Descripción |
|---|---|
| `git rebase main` | Reaplica tus commits encima de `main`. |
| `git rebase -i HEAD~4` | Rebase interactivo sobre los últimos 4 commits. |
| `git rebase --continue` | Continúa el rebase tras resolver conflictos. |
| `git rebase --abort` | Cancela el rebase y vuelve al estado original. |
| `git push --force-with-lease` | Fuerza la actualización del remoto de forma segura. |
