# Screaming Architecture

## Propósito

La estructura del proyecto debe "gritar" de qué trata el negocio, no qué tecnologías utiliza. Concepto propuesto por Robert C. Martin (Uncle Bob).

## Ejemplo de arquitectura que NO "grita" el negocio

```
src/
├── controllers/
├── services/
├── repositories/
├── models/
└── utils/
```

Al verla, no sabes si el sistema es un e-commerce, una biblioteca o un banco. Lo único que sabes es que usa controladores, servicios y repositorios.

## Ejemplo de Screaming Architecture

```
src/
├── orders/
│   ├── create-order.ts
│   ├── cancel-order.ts
│   └── order-repository.ts
├── customers/
│   ├── create-customer.ts
│   └── customer.ts
└── products/
    ├── create-product.ts
    └── product.ts
```

Ahora, apenas abres el proyecto, queda claro que trata de pedidos, clientes y productos.

## Principios Clave

- **Organizar por funcionalidades o casos de uso**, no por capas técnicas.
- **El dominio debe ser el protagonista.**
- **Los frameworks** (Laravel, React, Express, etc.) deben ser detalles de implementación.
- Facilita entender el negocio sin conocer la tecnología utilizada.

## Relación con Clean Architecture

Screaming Architecture no reemplaza a Clean Architecture; la complementa. Puedes tener algo así:

```
src/
├── orders/
│   ├── application/
│   ├── domain/
│   └── infrastructure/
├── customers/
└── products/
```

Aquí sigues aplicando capas (domain, application, infrastructure), pero agrupadas por feature en lugar de por tipo técnico.

## Resumen en una frase

Cuando alguien abre tu proyecto por primera vez, la estructura de carpetas debería decirle qué problema de negocio resuelve, no qué framework o patrón estás usando.
