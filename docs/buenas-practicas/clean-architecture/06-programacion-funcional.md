# Capítulo 6: Programación funcional

## 1. La esencia de la Programación Funcional

A diferencia de los lenguajes imperativos o de programación orientada a objetos (como Java), donde los programas usan bucles y variables que cambian de valor, la programación funcional utiliza expresiones y funciones que evitan el cambio de estado. La afirmación más sorprendente del capítulo es que, en este paradigma, **las variables no varían**.

## 2. Inmutabilidad y Arquitectura

Para un arquitecto de software, el aspecto más importante de la programación funcional es la **inmutabilidad**.

- **Eliminación de problemas de concurrencia:** Todas las condiciones de carrera, interbloqueos (*deadlocks*) y problemas de actualización simultánea ocurren debido a variables mutables.
- **Simplicidad:** Si ninguna variable se actualiza, es imposible tener problemas de concurrencia. Esto es vital para aplicaciones que requieren múltiples subprocesos y procesadores.

## 3. Gestión de la mutabilidad en el mundo real

Dado que la inmutabilidad total requeriría recursos infinitos de memoria y procesador, el autor propone estrategias para aplicarla de forma práctica:

- **Segregación de la mutabilidad:** Dividir el sistema en componentes que son puramente funcionales (inmutables) y componentes que permiten la mutación bajo condiciones estrictas (como los "átomos" en Clojure). Los componentes inmutables realizan la lógica de negocio y se comunican con los componentes mutables para cambiar el estado.
- **Obtención de Eventos (Event Sourcing):** En lugar de almacenar el estado actual (como el saldo de una cuenta), se almacenan todas las transacciones (eventos) desde el inicio. Cuando se necesita el estado, simplemente se vuelven a aplicar todas las transacciones. Esto elimina la necesidad de borrar o actualizar datos, convirtiendo el sistema en algo puramente funcional.

## 4. La restricción del paradigma

Al igual que los otros dos paradigmas, la programación funcional nos quita algo en lugar de darnos una nueva capacidad: **nos quita la asignación de variables**. Esta restricción es la que impone la disciplina necesaria para crear sistemas más robustos y predecibles.

## Conclusión

Los arquitectos deben esforzarse por empujar la mayor cantidad de lógica posible hacia componentes inmutables, dejando la mutación solo en áreas controladas, para proteger al sistema de los errores lógicos derivados de la concurrencia.

## Ejemplo comparativo: los tres paradigmas

### 🟢 Programación Estructurada (sin goto, con if/while)

```typescript
// Enfoque estructurado: procedimiento lineal
function calcularPrecioEstructurado(
    precioBase: number, 
    descuentoPorcentaje: number, 
    impuestoPorcentaje: number
): number {
    // Validación
    if (precioBase <= 0) {
        console.log("Error: Precio inválido");
        return 0;
    }
    
    if (descuentoPorcentaje < 0 || descuentoPorcentaje > 100) {
        console.log("Error: Descuento inválido");
        return 0;
    }
    
    // Cálculo paso a paso
    let precioConDescuento = precioBase;
    
    if (descuentoPorcentaje > 0) {
        precioConDescuento = precioBase * (1 - descuentoPorcentaje / 100);
    }
    
    let precioFinal = precioConDescuento * (1 + impuestoPorcentaje / 100);
    
    return precioFinal;
}

// Uso
const resultado = calcularPrecioEstructurado(100, 10, 21);
console.log(`Estructurado: $${resultado}`); // $108.90
```

### 🔵 Programación Orientada a Objetos (encapsulamiento + herencia)

```typescript
// Clase base
abstract class Producto {
    constructor(protected nombre: string, protected precio: number) {}
    
    abstract calcularPrecioFinal(impuesto: number): number;
    
    getInfo(): string {
        return `${this.nombre}: $${this.precio}`;
    }
}

// Producto con descuento
class ProductoConDescuento extends Producto {
    constructor(nombre: string, precio: number, private descuento: number) {
        super(nombre, precio);
    }
    
    calcularPrecioFinal(impuesto: number): number {
        const precioConDescuento = this.precio * (1 - this.descuento / 100);
        return precioConDescuento * (1 + impuesto / 100);
    }
}

// Producto normal (sin descuento)
class ProductoNormal extends Producto {
    calcularPrecioFinal(impuesto: number): number {
        return this.precio * (1 + impuesto / 100);
    }
}

// Uso
const laptop = new ProductoConDescuento("Laptop", 1000, 15);
const mouse = new ProductoNormal("Mouse", 50, 0);

console.log(`OOP: ${laptop.getInfo()} → $${laptop.calcularPrecioFinal(21)}`); // $1031.50
console.log(`OOP: ${mouse.getInfo()} → $${mouse.calcularPrecioFinal(21)}`);   // $60.50
```

### 🟡 Programación Funcional (funciones puras + inmutabilidad)

```typescript
// Funciones puras
type PrecioParams = {
    readonly precioBase: number;
    readonly descuento: number;
    readonly impuesto: number;
};

const aplicarDescuento = (params: PrecioParams): number => {
    const { precioBase, descuento } = params;
    return precioBase * (1 - descuento / 100);
};

const aplicarImpuesto = (precio: number, impuesto: number): number => {
    return precio * (1 + impuesto / 100);
};

// Composición
const calcularPrecioFuncional = (params: PrecioParams): number => {
    const conDescuento = aplicarDescuento(params);
    return aplicarImpuesto(conDescuento, params.impuesto);
};

// Uso (inmutabilidad pura)
const producto = { precioBase: 100, descuento: 10, impuesto: 21 };
const precioFinal = calcularPrecioFuncional(producto);

console.log(`Funcional: $${precioFinal}`); // $108.90
console.log(`Original inmutable: ${producto.precioBase}`); // 100 (no cambió)
```

### 📊 Comparación rápida

| Paradigma | Código | Cuándo usarlo |
|-----------|--------|---------------|
| Estructurada | `if`, `let`, cálculo paso a paso | Algoritmos simples, scripts pequeños |
| OOP | `class`, `extends`, `protected` | Sistemas grandes con estado y comportamiento juntos |
| Funcional | `const`, funciones puras, `readonly` | Transformaciones de datos, pipelines, inmutabilidad |

### 🎯 El mismo problema — 3 enfoques diferentes

```typescript
// ENTRADA
const precio = 100;
const descuento = 10; // 10%
const impuesto = 21;  // 21%

// 1️⃣ ESTRUCTURADO
function calcStruct(precio: number, desc: number, imp: number): number {
    if (precio <= 0) return 0;
    let total = precio;
    if (desc > 0) total = precio * (1 - desc/100);
    return total * (1 + imp/100);
}

// 2️⃣ OOP
class Product {
    constructor(private price: number, private discount: number) {}
    finalPrice(tax: number): number {
        return this.price * (1 - this.discount/100) * (1 + tax/100);
    }
}

// 3️⃣ FUNCIONAL
const applyDiscount = (p: number, d: number) => p * (1 - d/100);
const applyTax = (p: number, t: number) => p * (1 + t/100);
const calcFunc = (p: number, d: number, t: number) => applyTax(applyDiscount(p, d), t);

// RESULTADO (los 3 dan lo mismo)
console.log(calcStruct(100, 10, 21));  // 108.9
console.log(new Product(100, 10).finalPrice(21)); // 108.9
console.log(calcFunc(100, 10, 21)); // 108.9
```

**¿Cuál es mejor? Depende:**

- **Estructurada:** Para scripts rápidos
- **OOP:** Cuando los datos tienen comportamiento asociado
- **Funcional:** Cuando trabajas con transformaciones de datos en pipelines

---

[Navegación](../README.md)
