# Arquitectura Limpia / Hexagonal

## Propósito

Separar el código en capas (dominio, aplicación, infraestructura) para aislar la lógica de negocio de detalles externos como bases de datos o APIs.

## Principios Clave

- **Independencia del framework**: La lógica de negocio no depende de librerías externas.
- **Testabilidad**: Cada capa puede probarse de forma aislada.
- **Independencia de la UI**: La interfaz de usuario puede cambiar sin afectar el negocio.
- **Independencia de la base de datos**: Puedes cambiar de motor de BD sin modificar la lógica de negocio.

## Estructura Típica

```
src/
  dominio/        → Entidades, value objects, interfaces de repositorio
  aplicacion/     → Casos de uso, puertos
  infraestructura/ → Controladores, repositorios concretos, bases de datos
```

## Cuándo Usarlo

- Aplicaciones con lógica de negocio compleja.
- Proyectos que necesitan mantenerse a largo plazo.
- Equipos que requieren alta testabilidad.
