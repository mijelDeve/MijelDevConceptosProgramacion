# Tipos de datos y coerción

JavaScript es un lenguaje **dinámicamente tipado** y **débilmente tipado**. Esto significa que las variables no tienen un tipo fijo y que el lenguaje hace conversiones automáticas (coerción).

---

## 1. Primitivos (7 tipos)

Son valores **inmutables** (no se pueden modificar) y se pasan **por valor**.

| Tipo | Ejemplo | `typeof` | Notas |
|---|---|---|---|
| **String** | `"hola"`, `'mundo'` | `"string"` | |
| **Number** | `42`, `3.14`, `Infinity`, `NaN` | `"number"` | |
| **BigInt** | `9007199254740991n` | `"bigint"` | |
| **Boolean** | `true`, `false` | `"boolean"` | |
| **Undefined** | `let x;` | `"undefined"` | |
| **Null** | `null` | `"object"` | ← *bug histórico* |
| **Symbol** | `Symbol('id')` | `"symbol"` | Único e inmutable |

> **Nota importante:** `typeof null` devuelve `"object"`. Es un error del lenguaje que nunca se corrigió por compatibilidad.

---

## 2. Objetos (Reference Types)

Todo lo que **no es primitivo** es un **Objeto**.

- `Object` (incluyendo arrays, funciones, fechas, regex, etc.)
- `Array`
- `Function`
- `Date`, `RegExp`, `Map`, `Set`, `Promise`, etc.

### Características clave

- Se pasan **por referencia**
- Son **mutables**
- Tienen prototipo (`__proto__`)

```js
let obj1 = { nombre: "Juan" };
let obj2 = obj1;

obj2.nombre = "Pedro";
console.log(obj1.nombre); // "Pedro" → ambos apuntan al mismo objeto
```

---

## 3. Diferencias Primitivos vs Objetos

| Característica | Primitivos | Objetos |
|---|---|---|
| **Mutabilidad** | Inmutables | Mutables |
| **Paso en funciones** | Por valor | Por referencia |
| **Propiedades/métodos** | No (excepto *auto-boxing*) | Sí |
| **Comparación (`===`)** | Por valor | Por referencia |
| **`typeof`** | Devuelve tipo real | `"object"` o `"function"` |

### Auto-boxing (envoltorio automático)

```js
let nombre = "Carlos";
console.log(nombre.toUpperCase()); // JS crea temporalmente un String object
```

---

## 4. El operador `typeof`

Devuelve un *string* con el tipo.

```js
console.log(typeof 42);                    // "number"
console.log(typeof "texto");               // "string"
console.log(typeof true);                  // "boolean"
console.log(typeof undefined);             // "undefined"
console.log(typeof null);                  // "object" ← ¡cuidado!
console.log(typeof {});                    // "object"
console.log(typeof []);                    // "object"
console.log(typeof function(){});          // "function"
console.log(typeof Symbol('x'));           // "symbol"
console.log(typeof 123n);                  // "bigint"
```

### Truco útil para arrays

```js
Array.isArray([]);        // true  (mejor que typeof)
```

---

## 5. El operador `instanceof`

Verifica si un objeto es instancia de una clase/constructor (recorre la cadena de prototipos).

```js
function Persona() {}
const juan = new Persona();

console.log(juan instanceof Persona);     // true
console.log(juan instanceof Object);      // true
console.log([] instanceof Array);         // true
console.log([] instanceof Object);        // true
```

### Limitaciones

- No funciona bien con primitivos (excepto con `new String()`, etc.)
- Problemas con `instanceof` entre ventanas/*iframes* (diferentes contextos)

---

## 6. Coerción de Tipos (Type Coercion)

Es la conversión automática que hace JavaScript.

### Coerción Implícita (la más peligrosa)

```js
console.log(5 + "5");      // "55"  (number → string)
console.log("5" - 3);      // 2     (string → number)
console.log("5" * 2);      // 10
console.log("5" == 5);     // true  (== hace coerción)
console.log(0 == false);   // true
console.log([] == false);  // true  ← trampa famosa
```

### Casos famosos (gotchas)

```js
console.log([] == ![]);           // true
console.log(" " == 0);            // true
console.log("\t\r\n" == 0);       // true
console.log(null == undefined);   // true
console.log(NaN == NaN);          // false ← siempre!
```

---

## 7. Coerción Explícita (Recomendada)

```js
// Strings
String(123)          // "123"
123 + ""             // "123"

// Números
Number("123")        // 123
+"123"               // 123
parseInt("123.45")   // 123
parseFloat("123.45") // 123.45

// Booleanos
Boolean(1)           // true
!!"texto"            // true
Boolean("")          // false
```

---

## 8. Buenas Prácticas (Nivel Senior)

- Usa siempre `===` y `!==` (*strict equality*) para evitar coerción.
- Valida tipos explícitamente cuando sea crítico.
- Evita comparaciones raras con arrays, objetos o strings vacíos.
- Usa `Array.isArray()`, `Object.prototype.toString.call()` para chequeos robustos.

### Función útil para chequeo de tipo avanzado

```js
function getType(value) {
  if (value === null) return 'null';
  if (value === undefined) return 'undefined';
  return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}

console.log(getType([]));         // "array"
console.log(getType(null));       // "null"
console.log(getType(new Date())); // "date"
```
