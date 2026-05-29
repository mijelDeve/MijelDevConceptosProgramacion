# Repository

## Propósito

Actúa como intermediario entre la lógica de negocio y el acceso a datos. Hace el código más limpio y facilita cambiar de base de datos.

## Estructura

```
Interfaz Repositorio  ← Define las operaciones (find, save, delete...)
  ↑
Implementación Concreta  ← Acceso a BD real (MySQL, PostgreSQL, MongoDB...)
```

## Ejemplo

```java
// Interfaz
interface UserRepository {
    User findById(int id);
    List<User> findAll();
    void save(User user);
}

// Implementación concreta
class MySQLUserRepository implements UserRepository {
    public User findById(int id) { /* SQL query */ }
    public List<User> findAll()  { /* SQL query */ }
    public void save(User user)  { /* INSERT */ }
}

// Uso
UserRepository repo = new MySQLUserRepository();
User user = repo.findById(1);
```

## Cuándo Usarlo

- Cuando quieres aislar la lógica de acceso a datos.
- Para facilitar el cambio de base de datos (ej: MySQL → MongoDB).
- Para escribir tests unitarios sin tocar la base de datos real.
- En combinación con Inyección de Dependencias para mayor flexibilidad.
