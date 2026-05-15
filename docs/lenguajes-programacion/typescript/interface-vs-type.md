# Type (Tipo)

Un type en TypeScript es como una etiqueta o alias que puedes crear para describir cualquier forma de dato que puedas imaginar. Es una definición única y cerrada que agrupa características. Piensa en ella como un molde desechable: lo usas una vez, defines la forma exacta, y no puedes volver a abrirlo para agregar más cosas después.

# Interface (Interfaz)

Una interface es como un contrato o plano que describe cómo debe ser un objeto. A diferencia del type, una interfaz es abierta y acumulativa: puedes definir una parte hoy, otra parte mañana, y TypeScript las combinará automáticamente. Es como un acuerdo vivo que puede crecer con el tiempo.

# Diferencias fundamentales

**1. Capacidad de extensión (la más importante)**

- Type: Es como una caja sellada. Una vez que defines qué contiene, no puedes volver a abrir esa misma caja para agregar más cosas. Si necesitas más características, tienes que crear una caja nueva que combine la anterior con lo nuevo.

- Interface: Es como una carpeta de archivos abierta. Puedes agregar más hojas (propiedades) a la misma carpeta en diferentes momentos, y todo se junta automáticamente.

**2. Qué pueden describir**

- Type: Puede describir cualquier cosa - no solo objetos. Puede representar un texto específico ("activo" o "inactivo"), una combinación de varios tipos, una lista ordenada con posiciones fijas, o incluso transformar tipos existentes creando reglas condicionales.

- Interface: Solo puede describir objetos y funciones. No puede representar directamente textos específicos, combinaciones de varios tipos, ni hacer transformaciones complejas.

**3. Relaciones entre ellos**

- Type: Para combinar dos types, usas una operación de intersección - es como decir "quiero algo que tenga TODAS las características del primero Y TODAS del segundo". Es una operación matemática que junta conjuntos.

- Interface: Para extender una interfaz, usas herencia - es como decir "esta nueva interfaz es como la anterior, pero además tiene estas características extra". Es una relación más jerárquica.

**4. Comportamiento con errores**
- Type: Cuando cometes un error, TypeScript te mostrará mensajes que pueden volverse muy largos y confusos, especialmente si usaste muchas operaciones de combinación. Es como recibir el historial completo de operaciones matemáticas que llevaron al error.

- Interface: Los mensajes de error suelen ser más directos y legibles, enfocándose en qué propiedad específica falta o está mal. Es más amigable para principiantes.

# Analogías para entender mejor

- Type es como una receta de cocina específica: dice exactamente qué ingredientes y en qué cantidad, pero no puedes modificarla después. Si quieres una receta similar, creas una nueva desde cero.

- Interface es como un formulario de registro: tiene campos definidos, pero puedes agregar más secciones al formulario después, y todo sigue siendo el mismo formulario unificado.

- Type es como decir "esto es exactamente esto" - una definición precisa y única.

- Interface es como decir "cualquier cosa que cumpla con esto" - un estándar al que otros pueden adaptarse.

# Diferencias

## Extensibilidad

- Interface: Es extensible (se puede volver a abrir)

```typescript
interface Persona {
  nombre: string;
}

interface Persona {
  edad: number;
}
// Resultado: Persona tiene nombre y edad
```

- Type: No es extensible (no se puede redeclarar)

```typescript
type Persona = { nombre: string };
type Persona = { edad: number }; // ❌ Error: identificador duplicado
```

## Extensión / Herencia

- Interface: Usa extends

```typescript
interface Animal {
  nombre: string;
}

interface Perro extends Animal {
  ladrar(): void;
}
```

- Type: Usa intersección (&)

```typescript
type Animal = { nombre: string };
type Perro = Animal & { ladrar(): void };
```
## Capacidades exclusivas

- Type puede hacer cosas que interface no puede:

```typescript
// Primitivas
type Nombre = string;

// Uniones
type Estado = "activo" | "inactivo";

// Tuplas
type Punto = [number, number];

// Tipos condicionales
type NonNullable<T> = T extends null | undefined ? never : T;

// Mapped types
type SoloLectura<T> = { readonly [P in keyof T]: T[P] };
```

- Interface es mejor para objetos que se extenderán:

```typescript
// Declaración de merging (útil para librerías)
interface Config {
  api: string;
}

// Más tarde, otro archivo puede agregar:
interface Config {
  timeout: number;
}
// Ambos se fusionan automáticamente
```

## Cuándo utilizar cada uno

**Utiliza type cuando**

- Necesitas uniones o intersecciones: Como \(string \mid number\), o combinar distintos tipos.
- Creas alias de tipos primitivos o tuplas: Ej. type ID = string; o type Coords = [number, number];.
- Diseñas lógica compleja: Como tipos mapeados o tipos condicionales

**Utiliza interface cuando**

- Estás definiendo la forma de un objeto o una clase: Especialmente si es la base de datos de tu aplicación.
- Necesitas herencia (Extender): Cuando un objeto hereda de otro, la palabra clave extends es más rápida y predecible para el compilador que el operador & de los tipos.
- Diseñas librerías o APIs públicas: Permite la fusión de declaraciones (declaration merging), lo que hace que tu código sea extensible por otros desarrolladores