# Capítulo 5: Programación orientada a objetos

## 1. ¿Qué es realmente la Orientación a Objetos (OO)?

Aunque comúnmente se define como "la combinación de datos y función", Robert C. Martin examina esta definición a través de tres pilares fundamentales: encapsulación, herencia y polimorfismo. La sorpresa es que dos de ellos no son exclusivos de la OO.

> *"La OO no se trata de objetos. Se trata del control de dependencias a través del polimorfismo."* — Robert C. Martin

## 2. Encapsulación y Herencia: No son nuevas

### Encapsulación

Lenguajes no orientados a objetos, como el C, ya permitían una encapsulación perfecta mediante archivos de encabezado y estructuras. De hecho, muchos lenguajes OO modernos han debilitado la encapsulación al obligar a exponer detalles internos en las clases.

```c
// C: Encapsulación perfecta con header + struct
// punto.h (interfaz pública)
typedef struct Punto Punto;
Punto* crearPunto(int x, int y);
int obtenerX(const Punto* p);
int obtenerY(const Punto* p);
void destruirPunto(Punto* p);

// punto.c (implementación oculta)
struct Punto {
    int x;
    int y;
};
```

### Herencia

Antes de los lenguajes OO, los programadores ya usaban trucos (como el enmascaramiento de estructuras de datos) para lograr algo similar a la herencia. La OO simplemente hizo que este proceso fuera mucho más seguro y conveniente.

## 3. El Polimorfismo: El aporte fundamental

El polimorfismo ya existía mediante el uso de punteros a funciones (por ejemplo, en el sistema operativo UNIX para manejar dispositivos de entrada y salida). Sin embargo, usar punteros manualmente era peligroso y propenso a errores. Los lenguajes OO hicieron que el polimorfismo fuera seguro y trivial, permitiendo que cualquier dependencia de código fuente pueda ser **invertida**.

## 4. El mayor beneficio: Inversión de Dependencia

Este es el concepto más crítico para la arquitectura. En los sistemas tradicionales, las dependencias de código siguen el flujo de control (el módulo A llama al módulo B, por lo tanto A depende de B). Gracias al polimorfismo seguro de la OO, el arquitecto puede invertir la dirección de la dependencia para que apunte en contra del flujo de control. Esto otorga un poder absoluto:

- **Independencia de la lógica**: Las reglas de negocio no dependen de los detalles técnicos (interfaz de usuario, base de datos).
- **Implementación independiente**: Los diferentes componentes pueden ser desarrollados, compilados y desplegados por equipos distintos sin interferir.

> *"El poder de la OO es la capacidad de controlar las dependencias del código fuente a través del polimorfismo."*

## 5. ❌ Sin POO: Enfoque procedural

Imagina un sistema de gestión de figuras geométricas. Sin POO, usamos tipos y funciones separadas:

```typescript
// ❌ SIN POO: Enfoque procedural con tipos y funciones separadas
type TipoFigura = 'circulo' | 'rectangulo' | 'triangulo';

interface FiguraData {
    tipo: TipoFigura;
    radio?: number;      // para círculo
    ancho?: number;      // para rectángulo
    alto?: number;       // para rectángulo
    base?: number;       // para triángulo
    altura?: number;     // para triángulo
}

function calcularArea(figura: FiguraData): number {
    switch (figura.tipo) {
        case 'circulo':
            if (!figura.radio) return 0;
            return Math.PI * figura.radio ** 2;
        case 'rectangulo':
            if (!figura.ancho || !figura.alto) return 0;
            return figura.ancho * figura.alto;
        case 'triangulo':
            if (!figura.base || !figura.altura) return 0;
            return (figura.base * figura.altura) / 2;
        default:
            return 0;
    }
}

function calcularPerimetro(figura: FiguraData): number {
    switch (figura.tipo) {
        case 'circulo':
            if (!figura.radio) return 0;
            return 2 * Math.PI * figura.radio;
        case 'rectangulo':
            if (!figura.ancho || !figura.alto) return 0;
            return 2 * (figura.ancho + figura.alto);
        case 'triangulo':
            if (!figura.base || !figura.altura) return 0;
            const lado = Math.sqrt((figura.base/2)**2 + figura.altura**2);
            return figura.base + 2 * lado;
        default:
            return 0;
    }
}

function dibujar(figura: FiguraData): string {
    switch (figura.tipo) {
        case 'circulo':
            return `⚪ Círculo de radio ${figura.radio}`;
        case 'rectangulo':
            return `📐 Rectángulo de ${figura.ancho}x${figura.alto}`;
        case 'triangulo':
            return `🔺 Triángulo de base ${figura.base} y altura ${figura.altura}`;
        default:
            return 'Figura desconocida';
    }
}

// Uso
const miCirculo: FiguraData = { tipo: 'circulo', radio: 5 };
const miRectangulo: FiguraData = { tipo: 'rectangulo', ancho: 10, alto: 20 };

console.log(calcularArea(miCirculo));        // 78.54
console.log(calcularPerimetro(miRectangulo)); // 60
console.log(dibujar(miCirculo));              // ⚪ Círculo de radio 5
```

**Problemas de este enfoque:**
- Datos y comportamiento separados — las funciones están desconectadas de los datos
- Difícil de extender — para agregar `cuadrado`, hay que modificar TODAS las funciones
- Campos opcionales confusos — `radio` solo existe para círculo, pero está en todas
- Violación de encapsulamiento — cualquiera puede hacer `figura.radio = -5`
- Código repetido — cada función tiene su propio `switch`
- Sin relación semántica — ¿qué funciones operan sobre qué figuras? No está claro

## 6. ✅ Con POO: Solución elegante

```typescript
// ✅ CON POO: Encapsulamiento, herencia y polimorfismo

abstract class Figura {
    private id: string;
    protected color: string;

    constructor(color: string = 'blanco') {
        this.id = Math.random().toString(36).substr(2, 9);
        this.color = color;
    }

    abstract calcularArea(): number;
    abstract calcularPerimetro(): number;
    abstract dibujar(): string;

    obtenerId(): string {
        return this.id;
    }

    cambiarColor(nuevoColor: string): void {
        this.color = nuevoColor;
        console.log(`Figura ${this.id} ahora es ${nuevoColor}`);
    }
}

class Circulo extends Figura {
    private radio: number;

    constructor(radio: number, color: string = 'blanco') {
        super(color);
        if (radio <= 0) throw new Error('El radio debe ser positivo');
        this.radio = radio;
    }

    calcularArea(): number {
        return Math.PI * this.radio ** 2;
    }

    calcularPerimetro(): number {
        return 2 * Math.PI * this.radio;
    }

    dibujar(): string {
        return `⚪ Círculo ${this.color} de radio ${this.radio}`;
    }

    obtenerDiametro(): number {
        return this.radio * 2;
    }
}

class Rectangulo extends Figura {
    private ancho: number;
    private alto: number;

    constructor(ancho: number, alto: number, color: string = 'blanco') {
        super(color);
        if (ancho <= 0 || alto <= 0) throw new Error('Dimensiones deben ser positivas');
        this.ancho = ancho;
        this.alto = alto;
    }

    calcularArea(): number {
        return this.ancho * this.alto;
    }

    calcularPerimetro(): number {
        return 2 * (this.ancho + this.alto);
    }

    dibujar(): string {
        return `📐 Rectángulo ${this.color} de ${this.ancho}x${this.alto}`;
    }

    esCuadrado(): boolean {
        return this.ancho === this.alto;
    }
}

class Triangulo extends Figura {
    private base: number;
    private altura: number;
    private lado1: number;
    private lado2: number;

    constructor(base: number, altura: number, lado1: number, lado2: number, color: string = 'blanco') {
        super(color);
        if (base <= 0 || altura <= 0) throw new Error('Base y altura deben ser positivas');
        this.base = base;
        this.altura = altura;
        this.lado1 = lado1;
        this.lado2 = lado2;
    }

    calcularArea(): number {
        return (this.base * this.altura) / 2;
    }

    calcularPerimetro(): number {
        return this.base + this.lado1 + this.lado2;
    }

    dibujar(): string {
        return `🔺 Triángulo ${this.color} de base ${this.base} y altura ${this.altura}`;
    }
}

// Uso con POO
const circulo = new Circulo(5, 'rojo');
const rectangulo = new Rectangulo(10, 20, 'azul');
const triangulo = new Triangulo(8, 6, 5, 5, 'verde');

// Polimorfismo en acción
const figuras: Figura[] = [circulo, rectangulo, triangulo];

figuras.forEach(figura => {
    console.log(figura.dibujar());
    console.log(`Área: ${figura.calcularArea().toFixed(2)}`);
    console.log(`Perímetro: ${figura.calcularPerimetro().toFixed(2)}`);
    console.log(`ID: ${figura.obtenerId()}`);
    console.log('---');
});

console.log(`Diámetro del círculo: ${circulo.obtenerDiametro()}`);
console.log(`¿Es cuadrado?: ${rectangulo.esCuadrado()}`);

circulo.cambiarColor('negro');
// circulo.radio = -10;  // ❌ Error: Property 'radio' is private
```

## 7. Extensibilidad: Sin POO vs Con POO

### Sin POO (agregar Cuadrado)

```typescript
// ❌ Sin POO: Hay que modificar TODO
type TipoFigura = 'circulo' | 'rectangulo' | 'triangulo' | 'cuadrado';

interface FiguraData {
    tipo: TipoFigura;
    radio?: number;
    ancho?: number;
    alto?: number;
    base?: number;
    altura?: number;
    lado?: number;  // NUEVO
}

// Hay que modificar TODAS estas funciones:
function calcularArea(figura: FiguraData): number {
    switch (figura.tipo) {
        case 'cuadrado':
            if (!figura.lado) return 0;
            return figura.lado ** 2;
        // ... casos existentes también aquí ...
    }
}

function calcularPerimetro(figura: FiguraData): number {
    switch (figura.tipo) {
        case 'cuadrado':
            if (!figura.lado) return 0;
            return 4 * figura.lado;
        // ...
    }
}

function dibujar(figura: FiguraData): string {
    switch (figura.tipo) {
        case 'cuadrado':
            return `⬛ Cuadrado de lado ${figura.lado}`;
        // ...
    }
}
```

### Con POO (agregar Cuadrado)

```typescript
// ✅ CON POO: Solo agregar nueva clase, sin tocar nada existente
class Cuadrado extends Rectangulo {
    constructor(lado: number, color: string = 'blanco') {
        super(lado, lado, color);
    }

    dibujar(): string {
        return `⬛ Cuadrado ${this.color} de lado ${this.ancho}`;
    }

    obtenerDiagonal(): number {
        return this.ancho * Math.sqrt(2);
    }
}

const cuadrado = new Cuadrado(7, 'amarillo');
console.log(cuadrado.dibujar());            // ⬛ Cuadrado amarillo de lado 7
console.log(cuadrado.calcularArea());       // 49
console.log(cuadrado.obtenerDiagonal());    // 9.899...
```

## 8. Caso práctico: Sistema de Recursos Humanos

Escenario: Imagina que necesitas calcular salarios para diferentes tipos de empleados, cada uno con reglas distintas.

### 8.1 Encapsulamiento (Primer pilar)

Problema: Evitar que los datos internos sean modificados incorrectamente desde fuera.

```typescript
// ✅ ENCAPSULAMIENTO: Los datos están protegidos
class Empleado {
    // Privados: solo accesibles dentro de la clase
    private _nombre: string;
    private _salarioBase: number;
    private _añosAntiguedad: number;
    private _historialSalarios: number[] = [];
    
    // Protegido: accesible en herencia
    protected _bonoPersonal: number = 0;
    
    constructor(nombre: string, salarioBase: number, añosAntiguedad: number) {
        this._nombre = nombre;
        this._salarioBase = salarioBase;
        this._añosAntiguedad = añosAntiguedad;
        this._historialSalarios.push(salarioBase);
    }
    
    // Getter público (solo lectura controlada)
    get nombre(): string {
        return this._nombre;
    }
    
    get salarioBase(): number {
        return this._salarioBase;
    }
    
    get añosAntiguedad(): number {
        return this._añosAntiguedad;
    }
    
    // Setter con validaciones (encapsulamiento en acción)
    set salarioBase(nuevoSalario: number) {
        // Regla de negocio: no puede bajar más del 10%
        const reduccionMaxima = this._salarioBase * 0.9;
        if (nuevoSalario < reduccionMaxima) {
            throw new Error(`No se puede reducir el salario más del 10% (mínimo: ${reduccionMaxima})`);
        }
        
        if (nuevoSalario > this._salarioBase * 2) {
            throw new Error(`No se puede duplicar el salario de una vez (máximo: ${this._salarioBase * 2})`);
        }
        
        this._historialSalarios.push(nuevoSalario);
        this._salarioBase = nuevoSalario;
        console.log(`✅ Salario actualizado: ${this._nombre} ahora gana $${nuevoSalario}`);
    }
    
    // Método privado (solo uso interno)
    private registrarPago(monto: number): void {
        console.log(`[LOG] Pago registrado: $${monto} para ${this._nombre}`);
    }
    
    // Método protegido (para herencia)
    protected calcularAntiguedadBonus(): number {
        return this._añosAntiguedad * 50; // $50 por año
    }
    
    // Método público (interfaz)
    calcularSalario(): number {
        const salarioFinal = this._salarioBase + this.calcularAntiguedadBonus() + this._bonoPersonal;
        this.registrarPago(salarioFinal);
        return salarioFinal;
    }
    
    mostrarInfo(): string {
        return `${this._nombre} (${this.constructor.name}) - Salario: $${this.calcularSalario()}`;
    }
    
    // Método para ver historial (solo lectura)
    verHistorialSalarios(): number[] {
        return [...this._historialSalarios]; // Copia defensiva
    }
}

// ❌ Lo que NO se puede hacer desde fuera:
const empleado = new Empleado("Ana", 3000, 5);
// empleado._salarioBase = 1000;  // ❌ Error: private
// empleado._nombre = "Ana María"; // ❌ Error: private
// empleado.registrarPago(5000);   // ❌ Error: private
// console.log(empleado._historialSalarios); // ❌ Error: private

// ✅ Lo que SÍ se puede hacer:
console.log(empleado.nombre);  // "Ana" (getter)
empleado.salarioBase = 3200;   // ✅ Setter con validación
// empleado.salarioBase = 1500;   // ❌ Error: viola regla del 10%
console.log(empleado.verHistorialSalarios()); // [3000, 3200]
```

### 8.2 Herencia (Segundo pilar)

Problema: Reutilizar código y crear jerarquías lógicas.

```typescript
// ✅ HERENCIA: EmpleadoBase es la clase padre
abstract class EmpleadoBase extends Empleado {
    protected comision: number = 0;
    protected metaCumplida: boolean = false;
    
    constructor(nombre: string, salarioBase: number, añosAntiguedad: number) {
        super(nombre, salarioBase, añosAntiguedad);
    }
    
    // Método adicional para empleados con comisión
    establecerComision(porcentaje: number, metaCumplida: boolean): void {
        this.comision = porcentaje;
        this.metaCumplida = metaCumplida;
    }
    
    // Sobrescritura parcial (usa super)
    calcularSalario(): number {
        let salario = super.calcularSalario();
        if (this.metaCumplida) {
            salario += salario * (this.comision / 100);
        }
        return salario;
    }
}

// Herencia: Vendedor extiende EmpleadoBase
class Vendedor extends EmpleadoBase {
    private ventasDelMes: number = 0;
    private cuotaMinima: number = 10000;
    
    constructor(nombre: string, salarioBase: number, añosAntiguedad: number) {
        super(nombre, salarioBase, añosAntiguedad);
        // Los vendedores tienen bono personal por defecto
        this._bonoPersonal = 200;
    }
    
    // Método específico de Vendedor
    registrarVentas(monto: number): void {
        this.ventasDelMes = monto;
        const metaCumplida = monto >= this.cuotaMinima;
        const comision = metaCumplida ? 5 : 0; // 5% si cumple meta
        this.establecerComision(comision, metaCumplida);
        
        console.log(`📊 ${this.nombre} vendió $${monto} - ${metaCumplida ? '✅ Meta cumplida' : '❌ No cumplió meta'}`);
    }
    
    // Sobrescritura completa
    calcularSalario(): number {
        const salarioBase = super.calcularSalario();
        const bonoExtra = this.ventasDelMes > 20000 ? 500 : 0;
        return salarioBase + bonoExtra;
    }
    
    mostrarInfo(): string {
        return `${super.mostrarInfo()} | Ventas: $${this.ventasDelMes}`;
    }
}

// Herencia: Gerente extiende Empleado
class Gerente extends Empleado {
    private empleadosACargo: number;
    private bonificacionGerencial: number;
    
    constructor(nombre: string, salarioBase: number, añosAntiguedad: number, empleadosACargo: number) {
        super(nombre, salarioBase, añosAntiguedad);
        this.empleadosACargo = empleadosACargo;
        this._bonoPersonal = 1000; // Bono especial para gerentes
        this.bonificacionGerencial = empleadosACargo * 100;
    }
    
    // Sobrescritura con lógica específica
    calcularSalario(): number {
        const salario = super.calcularSalario();
        return salario + this.bonificacionGerencial;
    }
    
    mostrarInfo(): string {
        return `${super.mostrarInfo()} | A cargo: ${this.empleadosACargo} empleados`;
    }
}

// Herencia múltiple simulada (TypeScript no tiene herencia múltiple, usa mixins o interfaces)
interface Entrenable {
    entrenar(): void;
}

class Becario extends Empleado implements Entrenable {
    private horasEstudio: number;
    
    constructor(nombre: string, horasEstudio: number) {
        super(nombre, 1000, 0); // Salario base bajo
        this.horasEstudio = horasEstudio;
        this._bonoPersonal = 50;
    }
    
    entrenar(): void {
        console.log(`📚 ${this.nombre} está en entrenamiento por ${this.horasEstudio} horas`);
    }
    
    calcularSalario(): number {
        // Los becarios ganan según horas de estudio
        const salarioBase = super.calcularSalario();
        return salarioBase + (this.horasEstudio * 10);
    }
}
```

### 8.3 Polimorfismo (Tercer pilar)

Problema: Tratar objetos de diferentes clases de manera uniforme.

```typescript
// ✅ POLIMORFISMO: Misma interfaz, comportamientos diferentes

// Función polimórfica
function procesarNómina(empleados: Empleado[]): void {
    console.log("\n" + "=".repeat(50));
    console.log("PROCESANDO NÓMINA MENSUAL");
    console.log("=".repeat(50));
    
    let totalNomina = 0;
    
    empleados.forEach(empleado => {
        // 👇 POLIMORFISMO: cada empleado calcula su salario según su tipo
        const salario = empleado.calcularSalario();
        totalNomina += salario;
        
        // 👇 POLIMORFISMO: cada empleado muestra su información diferente
        console.log(empleado.mostrarInfo());
        
        // 👇 Type Guard para acceder a métodos específicos
        if (empleado instanceof Vendedor) {
            console.log(`   🏆 Bono por ventas aplicado`);
        } else if (empleado instanceof Becario) {
            (empleado as Becario).entrenar();
        }
    });
    
    console.log("-".repeat(50));
    console.log(`💰 TOTAL NÓMINA: $${totalNomina.toFixed(2)}`);
    console.log(`📊 PROMEDIO: $${(totalNomina / empleados.length).toFixed(2)}`);
}

// DEMOSTRACIÓN COMPLETA
function main(): void {
    // Crear diferentes tipos de empleados
    const vendedor1 = new Vendedor("Carlos López", 2500, 3);
    const vendedor2 = new Vendedor("María García", 2800, 5);
    const gerente = new Gerente("Laura Fernández", 5000, 10, 8);
    const becario = new Becario("Javier Ruiz", 80);
    
    // Configurar específicos
    vendedor1.registrarVentas(15000);  // Cumple meta (5% comisión)
    vendedor2.registrarVentas(8500);   // No cumple meta (0% comisión)
    
    // Simular cambios (encapsulamiento protege)
    console.log("\n📈 Actualizando salario de Carlos...");
    vendedor1.salarioBase = 2700;  // ✅ OK (aumento permitido)
    
    try {
        console.log("\n⚠️ Intentando reducción ilegal...");
        gerente.salarioBase = 2500;  // ❌ Error: reducción del 50%
    } catch (error) {
        console.log(`❌ Error: ${error.message}`);
    }
    
    // Array polimórfico (todos son Empleado)
    const empleados: Empleado[] = [vendedor1, vendedor2, gerente, becario];
    
    // Procesar nómina (polimorfismo en acción)
    procesarNómina(empleados);
    
    // Demostrar que cada uno tiene comportamientos diferentes
    console.log("\n" + "=".repeat(50));
    console.log("COMPORTAMIENTO POLIMÓRFICO DETALLADO");
    console.log("=".repeat(50));
    
    empleados.forEach(emp => {
        console.log(`\n${emp.nombre} (${emp.constructor.name}):`);
        console.log(`  Método calcularSalario(): $${emp.calcularSalario()}`);
        console.log(`  Método mostrarInfo(): ${emp.mostrarInfo()}`);
    });
}

// Ejecutar
main();
```

### Salida esperada del programa

```
📊 Carlos López vendió $15000 - ✅ Meta cumplida
📊 María García vendió $8500 - ❌ No cumplió meta

📈 Actualizando salario de Carlos...
✅ Salario actualizado: Carlos López ahora gana $2700

⚠️ Intentando reducción ilegal...
❌ Error: No se puede reducir el salario más del 10% (mínimo: 4500)

==================================================
PROCESANDO NÓMINA MENSUAL
==================================================
Carlos López (Vendedor) - Salario: $3555 | Ventas: $15000
María García (Vendedor) - Salario: $2850 | Ventas: $8500
Laura Fernández (Gerente) - Salario: $6600 | A cargo: 8 empleados
Javier Ruiz (Becario) - Salario: $1750
--------------------------------------------------
💰 TOTAL NÓMINA: $14755.00
📊 PROMEDIO: $3688.75

==================================================
COMPORTAMIENTO POLIMÓRFICO DETALLADO
==================================================

Carlos López (Vendedor):
  Método calcularSalario(): $3555
  Método mostrarInfo(): Carlos López (Vendedor) - Salario: $3555 | Ventas: $15000

María García (Vendedor):
  Método calcularSalario(): $2850
  Método mostrarInfo(): María García (Vendedor) - Salario: $2850 | Ventas: $8500

Laura Fernández (Gerente):
  Método calcularSalario(): $6600
  Método mostrarInfo(): Laura Fernández (Gerente) - Salario: $6600 | A cargo: 8 empleados

Javier Ruiz (Becario):
  Método calcularSalario(): $1750
  Método mostrarInfo(): Javier Ruiz (Becario) - Salario: $1750
```

## 9. Principios POO que resuelven problemas específicos

### Tabla comparativa: 3 pilares en acción

| Principio | Problema que resuelve | Ejemplo en Figuras | Ejemplo en RH | Beneficio |
|-----------|----------------------|-------------------|---------------|-----------|
| Encapsulamiento | Datos inconsistentes | `private radio` + validación en constructor | `private _salarioBase` + setter con validación | No se pueden asignar valores inválidos |
| Abstracción | Código duplicado | Clase abstracta `Figura` con métodos concretos | `Empleado` como clase base con métodos compartidos | Comportamiento común centralizado |
| Herencia | Código repetido | `Cuadrado` hereda de `Rectangulo` | `Vendedor extends Empleado` | Reutiliza lógica de `calcularAntiguedadBonus()` |
| Polimorfismo | Switches/tipos explícitos | `figura.calcularArea()` sin importar el tipo | `empleado.calcularSalario()` según su tipo | Cada clase sabe calcular su propio valor |

## Conclusión del capítulo

Desde la perspectiva de un arquitecto, la programación orientada a objetos es la capacidad de utilizar el polimorfismo para obtener el control total sobre todas las dependencias del código fuente de un sistema. Esto permite crear una estructura de complementos (plugins) donde los detalles de bajo nivel dependen de las políticas de alto nivel, y no al revés.

### La POO en Clean Architecture

Estos tres pilares se aplican directamente en una arquitectura limpia:

| Pilar | Aplicación en Clean Architecture | Ejemplo |
|-------|--------------------------------|---------|
| Encapsulamiento | Entidades protegen sus invariantes | `private _precio` + validación en métodos |
| Herencia | Casos de uso específicos heredan de clase base | `class CreateOrderUseCase extends UseCase` |
| Polimorfismo | Repositorios intercambiables vía interfaces | `PostgresUserRepository implements UserRepository` |

```typescript
// Encapsulamiento: Entidades protegen sus invariantes
class Producto {
    private _precio: Money;

    addToCart(cantidad: number) {
        if (cantidad > this.stock) throw new Error('Stock insuficiente');
    }
}

// Herencia: Casos de uso específicos
abstract class UseCase<TInput, TOutput> {
    abstract execute(input: TInput): Promise<TOutput>;
    protected logError(error: Error): void { /* ... */ }
}

class CreateOrderUseCase extends UseCase<OrderDTO, Order> {
    // Lógica específica del caso de uso
}

// Polimorfismo: Repositorios intercambiables
interface UserRepository {
    findById(id: string): Promise<User>;
}

class PostgresUserRepository implements UserRepository {
    async findById(id: string): Promise<User> {
        // Implementación con PostgreSQL
    }
}

class InMemoryUserRepository implements UserRepository {
    async findById(id: string): Promise<User> {
        // Implementación en memoria para tests
    }
}
```

Además, la POO brilla en los *boundaries* (fronteras) de la arquitectura:

```typescript
// Entidad (reglas de negocio críticas)
class Pedido {
    private items: ItemPedido[] = [];

    agregarItem(producto: Producto, cantidad: number): void {
        if (cantidad <= 0) throw new Error('Cantidad inválida');
        this.items.push(new ItemPedido(producto, cantidad));
    }

    calcularTotal(): Money {
        return this.items.reduce((total, item) => total.add(item.subtotal()), Money.zero());
    }
}

// Caso de uso (orquestación)
class ProcesarPedidoUseCase {
    constructor(
        private pedidoRepository: PedidoRepository,
        private emailService: EmailService,
        private logger: Logger
    ) {}

    async ejecutar(pedidoId: string): Promise<void> {
        const pedido = await this.pedidoRepository.obtenerPorId(pedidoId);
        const total = pedido.calcularTotal();

        await this.pedidoRepository.guardar(pedido);
        await this.emailService.enviarConfirmacion(pedido);
        this.logger.info(`Pedido ${pedidoId} procesado: $${total.valor}`);
    }
}
```

La POO te permite depender de **abstracciones**, no de implementaciones concretas. Cambiar `EmailService` por `SmsService` sin tocar el caso de uso.

### Resumen comparativo

| Aspecto | Sin POO (procedural) | Con POO |
|---------|---------------------|---------|
| Datos + comportamiento | Separados en funciones sueltas | Unificados en objetos |
| Extensibilidad | Modificar N funciones + tipo | Agregar 1 clase nueva |
| Encapsulamiento | Campos públicos, sin validación | `private` con invariantes |
| Polimorfismo | `switch` infinito por tipo | Método abstracto resuelto en cada clase |
| Relación semántica | No hay vínculo función↔dato | El objeto "sabe" lo que hace |
| Dependencias | Siguen el flujo de control | Se pueden invertir vía interfaces |

---

[Navegación](../README.md)
