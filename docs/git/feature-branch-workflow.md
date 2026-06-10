# Feature Branch Workflow

El **Feature Branch Workflow** es una estrategia de trabajo con Git donde cada nueva funcionalidad, corrección o mejora se desarrolla en una rama independiente, en lugar de hacerlo directamente sobre la rama principal (`main` o `master`).

## Flujo básico

1. **Partir desde la rama principal**

   ```bash
   git checkout main
   git pull origin main
   ```

2. **Crear una rama para la funcionalidad**

   ```bash
   git checkout -b feature/login-with-google
   ```

3. **Desarrollar la funcionalidad**

   Realizar cambios y crear commits frecuentes y descriptivos.

   ```bash
   git add .
   git commit -m "Add Google OAuth login"
   ```

4. **Subir la rama al repositorio remoto**

   ```bash
   git push origin feature/login-with-google
   ```

5. **Crear un Pull Request (PR) o Merge Request (MR)**

   - Se revisa el código.
   - Se ejecutan pruebas automáticas.
   - Se reciben comentarios del equipo.

6. **Merge a la rama principal**

   Una vez aprobada la revisión:

   ```bash
   git checkout main
   git pull origin main
   git merge feature/login-with-google
   ```

   O, más comúnmente, el merge se realiza desde GitHub, GitLab o Bitbucket.

7. **Eliminar la rama**

   ```bash
   git branch -d feature/login-with-google
   git push origin --delete feature/login-with-google
   ```

## Ventajas

- Aísla el trabajo de cada funcionalidad.
- Reduce conflictos entre desarrolladores.
- Facilita las revisiones de código.
- Permite probar cambios antes de integrarlos.
- Mantiene la rama principal siempre estable.

## Ejemplo visual

```
main
 │
 ├───────────────┐
 │               │
 │        feature/login
 │               │
 │        Commit A
 │        Commit B
 │        Commit C
 │               │
 └───────────────┘
        Merge
```

## Buenas prácticas

- Una rama por tarea o historia de usuario.
- Mantener las ramas pequeñas y enfocadas.
- Actualizar periódicamente la rama con cambios recientes de `main`.
- Usar nombres descriptivos:
  - `feature/user-profile`
  - `fix/login-error`
  - `refactor/payment-service`

## Diferencia con GitFlow

El **Feature Branch Workflow** es más simple:

- Solo requiere una rama principal (`main`) y ramas de características.
- Es ideal para equipos pequeños y despliegues frecuentes.

**GitFlow**, en cambio, añade ramas como:

- `develop`
- `release/*`
- `hotfix/*`

y resulta más adecuado para proyectos con ciclos de liberación más formales.
