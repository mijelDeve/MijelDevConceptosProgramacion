# Inyección de Dependencias

## Propósito

En lugar de que un objeto cree sus propias dependencias, estas se le "inyectan" desde fuera. Mejora la testabilidad y flexibilidad del código.

## Ejemplo

```java
// Sin DI
class UserService {
    private UserRepository repo = new MySQLUserRepository();
}

// Con DI
class UserService {
    private UserRepository repo;

    public UserService(UserRepository repo) {
        this.repo = repo;  // La dependencia se inyecta
    }
}
```

## Beneficios

- **Acoplamiento débil**: Las clases dependen de interfaces, no de implementaciones concretas.
- **Testabilidad**: Puedes inyectar mocks en los tests fácilmente.
- **Flexibilidad**: Cambiar la implementación concreta no requiere modificar el código cliente.

## Implementaciones Comunes

- **Spring Boot**: `@Autowired`, constructor injection.
- **Laravel**: Service Container con binding en `AppServiceProvider`.
- **Angular**: El sistema de DI está integrado en el framework.

## Cuándo Usarlo

- Siempre que tengas dependencias externas (bases de datos, APIs, servicios).
- Cuando quieras escribir tests unitarios limpios.
- En proyectos grandes donde el acoplamiento se vuelve problemático.
