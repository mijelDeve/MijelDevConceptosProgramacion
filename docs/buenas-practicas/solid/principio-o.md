Tu código debe estar **abierto para crecer (extensión)**, pero **cerrado para ser cambiado (modificación)**.

En términos muy sencillos:

- **Abierto para extensión:** Significa que debes poder **añadir nuevas funciones** o comportamientos a tu programa.
- **Cerrado para modificación:** Significa que **no deberías editar el código que ya escribiste** y que ya está funcionando bien.

**¿Por qué es importante?** Porque cada vez que cambias código antiguo que ya ha sido probado, corres el riesgo de **romper algo o crear nuevos errores**. Lo ideal es que, si quieres añadir algo nuevo (como un nuevo método de pago o una nueva base de datos), simplemente **agregues una pieza nueva** en lugar de "abrir y parchar" la anterior.

La **Unidad 3** se centra en el **Principio Abierto-Cerrado (Open-Closed Principle - OCP)**, el cual es fundamental para diseñar software que pueda crecer sin romperse. Aquí tienes el resumen detallado:

**1. Definición Fundamental**

Este principio establece que las entidades de software (clases, módulos, funciones, etc.) deben estar **abiertas para la extensión, pero cerradas para la modificación**.

- **Abierta para la extensión:** Significa que debemos poder agregar nuevas funcionalidades o comportamientos a la clase.
- **Cerrada para la modificación:** Significa que no debemos alterar el código fuente de una clase que ya ha sido probada y está en producción, ya que esto conlleva el riesgo de introducir errores o "bugs" en un sistema que ya funciona.

**2. ¿Cómo se aplica?**

La clave para cumplir con este principio suele ser el uso de **interfaces y clases abstractas**. En lugar de escribir código rígido que dependa de implementaciones específicas, diseñamos estructuras que permiten "enchufar" nuevos comportamientos mediante el polimorfismo.

**3. Ejemplos Prácticos de las Fuentes**

- **El Sistema de Persistencia:** Si inicialmente una clase guarda facturas solo en archivos, pero luego el jefe pide guardarlas en una base de datos, lo ideal no es modificar la clase original. La solución es convertir la persistencia en una **interfaz** (`FacturaPersistencia`). Así, para añadir una base de datos nueva (como MySQL o MongoDB), simplemente creas una clase nueva que implemente esa interfaz sin tocar el código existente.
- **El Procesador de Pagos:** Imagina un sistema que solo acepta tarjetas de crédito. Si quieres añadir soporte para **PayPal**, en lugar de entrar a la clase `PaymentProcessor` y modificarla, creas una nueva clase llamada `PayPalPaymentProcessor` que extienda la funcionalidad base. Esto permite que el sistema use cualquier método de pago sin necesidad de cambiar la lógica de los procesadores anteriores.

**4. Beneficios Principales**

- **Estabilidad:** Evitas tocar código de producción confiable.
- **Flexibilidad:** El sistema se vuelve mucho más fácil de adaptar a nuevos requisitos.
- **Escalabilidad:** Permite que el software crezca de forma organizada añadiendo piezas nuevas en lugar de "parchar" las viejas.


# Sin aplicar OCP

```typescript
class AreaCalculator {
    // Cada figura require modificar el método

    calculateArea(shape: { type: string; radius?: number; width?: number; hieght?: number }): number {
        switch (shape.type) {
            case 'circle':
                return Math.PI * (shape.radius! ** 2);
            case 'rectangle':
                return shape.width! * shape.hieght!;
            case 'triangle':
                return (shape.width! * shape.hieght!) / 2;
            default:
                return 0;
        }
    }
}

const calculator = new AreaCalculator();
console.log(calculator.calculateArea({ type: 'circle', radius: 5 }));

```


# Aplicando OCP

```typescript
interface Shape {
    calculateArea(): number;
}

// Implementaciones concretas (exrensiones)
class Circle implements Shape {
    constructor(private radius: number) {}

    calculateArea(): number {
        return Math.PI * this.radius ** 2;
    }
}

class Rectangle implements Shape {
    constructor(private width: number, private height: number) {}

    calculateArea(): number {
        return this.width * this.height;
    }
}

class Triangle implements Shape {
  constructor(private base: number, private height: number) {}
  
  calculateArea(): number {
    return (this.base * this.height) / 2;
  }
}

class Square implements Shape {
  constructor(private side: number) {}
  
  calculateArea(): number {
    return this.side ** 2;
  }
}

class AreaCalculator {
    calculateAreas(shapes: Shape[]): number[] {
        return shapes.map(shape => shape.calculateArea());
    }

    calculateTotalarea(shapes: Shape[]): number {
        return shapes.reduce((total, shape) => total + shape.calculateArea(), 0);
    }
}

const shapes: Shape[] = [
    new Circle(5),
    new Rectangle(4, 6),
    new Triangle(3, 4),
    new Square(5)
]

const calculator = new AreaCalculator();
console.log('Áreas individuales: ', calculator.calculateAreas(shapes))
console.log('Area total: ', calculator.calculateTotalarea(shapes))
```
