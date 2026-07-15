# Pull Request desde la Terminal

Crear un Pull Request (PR) desde la terminal es más rápido que usar la interfaz web, especialmente cuando trabajas con tareas repetitivas o scripts automatizados.

La herramienta estándar para esto es **GitHub CLI** (`gh`), la CLI oficial de GitHub.

## Prerrequisitos

1. Tener [GitHub CLI](https://cli.github.com/) instalado
2. Autenticarse:

```bash
gh auth login
```

Sigue las instrucciones interactivas para autenticarte vía navegador o token.

## Flujo completo

### 1. Crear una rama y hacer commits

```bash
git checkout -b feature/mi-funcionalidad
# hacer cambios...
git add .
git commit -m "feat: agregar mi funcionalidad"
git push origin feature/mi-funcionalidad
```

### 2. Crear el PR

El comando base es `gh pr create`. Opciones más usadas:

```bash
gh pr create \
  --title "feat: agregar mi funcionalidad" \
  --body "Descripción detallada de los cambios" \
  --base main
```

#### PR en borrador (draft)

```bash
gh pr create --draft --title "WIP: mi funcionalidad" --body "Todavía en desarrollo"
```

#### PR con template por defecto

```bash
gh pr create --fill
```

`--fill` toma el título y cuerpo del commit o del mensaje del primer commit de la rama.

#### Asignar reviewers y labels

```bash
gh pr create \
  --title "feat: agregar mi funcionalidad" \
  --body "Descripción" \
  --reviewer @usuario \
  --label "feature"
```

### 3. Ver el PR creado

```bash
gh pr view
```

Abre el PR en el navegador:

```bash
gh pr view --web
```

### 4. Listar PRs abiertos

```bash
gh pr list
```

Con más detalles:

```bash
gh pr list --state all --label "bug"
```

### 5. Revisar y hacer merge

```bash
gh pr checkout <número>   # cambia a la rama del PR
gh pr review <número>     # aprobar o solicitar cambios
gh pr merge <número>      # merge con squash por defecto
gh pr merge <número> --merge   # merge commit
gh pr merge <número> --squash  # squash (por defecto)
gh pr merge <número> --rebase  # rebase
```

### 6. Cerrar un PR sin merge

```bash
gh pr close <número>
```

## Ejemplo práctico completo

```bash
# 1. Partir de main actualizado
git checkout main
git pull origin main

# 2. Crear rama de funcionalidad
git checkout -b fix/validar-email

# 3. Hacer cambios y commit
echo "console.log('validar email')" >> email.js
git add email.js
git commit -m "fix: agregar validación de email"

# 4. Subir rama
git push origin fix/validar-email

# 5. Crear PR
gh pr create \
  --title "fix: agregar validación de email" \
  --body "Se agrega validación del formato de email en el formulario de registro." \
  --base main \
  --label "bug" \
  --reviewer "@equipo-backend"

# 6. Abrir en navegador para verificar
gh pr view --web
```

## gh vs hub vs git

| Herramienta       | Comando para crear PR                       | Estado        |
| ----------------- | ------------------------------------------- | ------------- |
| `gh` (GitHub CLI) | `gh pr create`                              | Oficial       |
| `hub`             | `hub pull-request`                          | Deprecado     |
| `git` puro        | No es posible, necesitas API manual         | —             |

> **`hub`** fue la herramienta predecesora de `gh`. GitHub recomienda migrar a `gh`.

## Comandos útiles resumen

```bash
gh pr create                      # crear PR
gh pr create --draft              # crear PR en borrador
gh pr create --fill               # tomar título/cuerpo del commit
gh pr list                        # listar PRs abiertos
gh pr view                        # ver detalles del PR actual
gh pr view --web                  # abrir en navegador
gh pr checkout <número>           # cambiar a la rama del PR
gh pr diff                        # ver diff del PR
gh pr review <número> --approve   # aprobar PR
gh pr review <número> --comment   # comentar sin aprobar
gh pr review <número> --request-changes  # solicitar cambios
gh pr merge <número>              # mergear PR (squash)
gh pr merge <número> --merge      # mergear con merge commit
gh pr close <número>              # cerrar PR sin merge
gh pr reopen <número>             # reabrir PR cerrado
gh pr ready <número>              # marcar draft como listo
```

## Configuraciones útiles

```bash
# Usar siempre squash al mergear
gh config set git_protocol ssh
gh config set editor code --wait   # editor para --body
```

## Referencias

- [GitHub CLI manual](https://cli.github.com/manual/gh_pr_create)
- [Migrar de hub a gh](https://github.com/github/hub)
