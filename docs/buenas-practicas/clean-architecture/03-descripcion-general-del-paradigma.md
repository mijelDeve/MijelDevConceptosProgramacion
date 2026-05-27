# Capítulo 3: Descripción general del paradigma

## 1. Los tres paradigmas principales

Robert C. Martin explica que solo existen tres paradigmas de programación que han sido ampliamente adoptados:

### Programación estructurada

Descubierta por Dijkstra, se enfoca en imponer disciplina sobre el flujo de control (reemplazando el uso de `goto` por estructuras como `if/then/else` y `do/while`). Se basa en secuencia, selección (`if`/`switch`) e iteración (bucles). El código se organiza en funciones y bloques.

```typescript
// Ejemplo: Calcular promedio de calificaciones válidas
function calcularPromedio(calificaciones: number[]): number {
    let suma = 0;
    let contador = 0;

    for (let i = 0; i < calificaciones.length; i++) {  // Iteración
        if (calificaciones[i] >= 0 && calificaciones[i] <= 10) {  // Selección
            suma += calificaciones[i];
            contador++;
        }
    }

    if (contador === 0) {  // Selección
        return 0;
    } else {
        return suma / contador;
    }
}

// Uso
const notas = [8, -1, 10, 15, 7, 9];
console.log(calcularPromedio(notas));  // (8+10+7+9)/4 = 8.5
```

Características: Datos y funciones separadas, flujo de control lineal.

### Programación orientada a objetos

Se enfoca en imponer disciplina sobre la transferencia indirecta de control. Organiza el código en objetos que contienen datos (propiedades) y comportamiento (métodos). Pilares: encapsulamiento, herencia, polimorfismo.

```typescript
// Clase base
abstract class Animal {
    constructor(protected nombre: string, protected edad: number) {}

    abstract hacerSonido(): string;

    presentarse(): string {
        return `Soy ${this.nombre} y tengo ${this.edad} años.`;
    }
}

// Herencia
class Perro extends Animal {
    constructor(nombre: string, edad: number, private raza: string) {
        super(nombre, edad);
    }

    // Polimorfismo: implementación concreta
    hacerSonido(): string {
        return "¡Guau guau!";
    }

    getRaza(): string {
        return this.raza;
    }
}

class Gato extends Animal {
    hacerSonido(): string {
        return "Miau";
    }
}

// Uso
const miPerro = new Perro("Rex", 5, "Labrador");
console.log(miPerro.presentarse());     // Soy Rex y tengo 5 años.
console.log(miPerro.hacerSonido());     // ¡Guau guau!
console.log(miPerro.getRaza());         // Labrador

const animales: Animal[] = [miPerro, new Gato("Misi", 3)];
animales.forEach(a => console.log(a.hacerSonido())); // Guau guau / Miau
```

### Programación funcional

Se enfoca en imponer disciplina sobre la asignación de variables (inmutabilidad). Se basa en funciones puras (sin efectos secundarios), inmutabilidad, y composición. Evita estado compartido y mutación.

```typescript
// Funciones puras (no modifican entrada, no dependen de estado externo)
const duplicar = (x: number): number => x * 2;
const esPar = (x: number): boolean => x % 2 === 0;
const sumar = (a: number, b: number): number => a + b;

// Inmutabilidad: usar spread operator o métodos que retornan nuevo array
const numeros = [1, 2, 3, 4, 5];

// map, filter, reduce (funciones de orden superior)
const duplicados = numeros.map(duplicar);        // [2,4,6,8,10]
const pares = numeros.filter(esPar);             // [2,4]
const sumaTotal = numeros.reduce(sumar, 0);      // 15

// Composición de funciones
const componer = <T>(...fns: ((arg: T) => T)[]) => 
    (valor: T): T => fns.reduce((acc, fn) => fn(acc), valor);

const multiplicarPor2 = (n: number) => n * 2;
const sumar3 = (n: number) => n + 3;
const transformar = componer(multiplicarPor2, sumar3); // Primero *2, luego +3

console.log(transformar(5));  // (5*2)+3 = 13

// Ejemplo práctico: procesar pedidos (sin mutar)
type Pedido = { id: number; total: number; impuesto: number };

const aplicarImpuesto = (pedido: Pedido): Pedido => ({
    ...pedido,
    total: pedido.total + pedido.total * pedido.impuesto
});

const formatearPedido = (pedido: Pedido): string => 
    `Pedido ${pedido.id}: $${pedido.total.toFixed(2)}`;

const procesarPedido = (pedido: Pedido): string =>
    formatearPedido(aplicarImpuesto(pedido));

console.log(procesarPedido({ id: 1, total: 100, impuesto: 0.21 }));
// "Pedido 1: $121.00"
```

## 2. La naturaleza de las restricciones

Una de las conclusiones más sorprendentes del capítulo es que cada paradigma nos quita algo en lugar de darnos nuevas capacidades:

- La **programación estructurada** nos quita los saltos directos (`goto`).
- La **programación orientada a objetos** nos quita los punteros a funciones.
- La **programación funcional** nos quita la asignación de variables.

Ninguno de estos paradigmas ha aumentado nuestra potencia de procesamiento; simplemente han restringido aspectos de cómo escribimos código para hacerlo más manejable y predecible.

## 3. Historia y permanencia

El autor señala que estos tres paradigmas fueron descubiertos en un periodo de solo diez años, entre 1958 y 1968, y que en las décadas siguientes no se han añadido nuevos. Esto sugiere que probablemente estos sean los únicos paradigmas fundamentales que veremos, ya que cada uno aborda una de las tres grandes preocupaciones de la computación:

- **Programación estructurada**: Se encarga de la **función**.
- **Programación orientada a objetos**: Se encarga de la **separación de componentes**.
- **Programación funcional**: Se encarga de la **gestión de datos**.

## Conclusión del capítulo

El capítulo concluye que, independientemente del lenguaje o las herramientas modernas que utilicemos, el software sigue estando hecho del mismo material básico (sentencias `if`, asignaciones y bucles) y que las reglas eternas e inmutables de la arquitectura se derivan de estas restricciones fundamentales impuestas por los paradigmas.

### Resumen comparativo con TypeScript

| Paradigma | TypeScript: ejemplos clave |
|-----------|---------------------------|
| Estructurada | `if`, `for`, `while`, funciones simples |
| OOP | `class`, `extends`, `private`, `abstract`, `this` |
| Funcional | `const`, funciones flecha, `map`/`filter`/`reduce`, inmutabilidad (spread) |

TypeScript permite mezclar paradigmas (multiparadigma). En Clean Architecture, normalmente:

- **Estructurada** para algoritmos internos
- **Funcional** para transformaciones de datos (casos de uso, mapeadores)
- **OOP** para *boundaries* (repositorios, presentadores, controladores) y entidades complejas

---

[Navegación](../README.md)
