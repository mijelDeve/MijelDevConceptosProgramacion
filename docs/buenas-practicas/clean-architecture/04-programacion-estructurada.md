# Capítulo 4: Programación estructurada

## 1. El descubrimiento de Dijkstra

Edsger Dijkstra descubrió que la programación es difícil y que los programadores no suelen hacerlo muy bien. En su investigación, se dio cuenta de que el uso de la sentencia `goto` impedía que los módulos se descompusieran recursivamente en unidades más pequeñas, lo que dificultaba aplicar el enfoque de "divide y vencerás" necesario para realizar pruebas razonables.

> *"La capacidad de los programadores para razonar sobre el código se ve gravemente perjudicada cuando el flujo de control puede saltar arbitrariamente de un lugar a otro."* — Edsger Dijkstra

## 2. Las tres estructuras fundamentales

Dijkstra identificó, basándose en el trabajo previo de Böhm y Jacopini, que todos los programas pueden construirse a partir de solo tres estructuras de control:

### Secuencia

Instrucciones ejecutadas una tras otra, en orden lineal.

```typescript
// Secuencia: paso a paso
function procesarPedido(producto: string, cantidad: number, precio: number): string {
    const subtotal = cantidad * precio;
    const impuesto = subtotal * 0.21;
    const total = subtotal + impuesto;
    return `Pedido: ${producto} x ${cantidad} = $${total.toFixed(2)}`;
}
```

### Selección

Estructuras que permiten elegir entre diferentes caminos (`if`/`then`/`else`, `switch`).

```typescript
// Selección: elegir un camino según una condición
function obtenerDescuento(tipoCliente: 'regular' | 'vip' | 'corporativo'): number {
    if (tipoCliente === 'vip') {
        return 0.20;
    } else if (tipoCliente === 'corporativo') {
        return 0.15;
    } else {
        return 0;
    }
}
```

### Iteración

Estructuras que repiten un bloque de código (`while`, `for`, `do/while`).

```typescript
// Iteración: repetir hasta cumplir condición
function sumarValores(hasta: number): number {
    let suma = 0;
    for (let i = 1; i <= hasta; i++) {
        suma += i;
    }
    return suma;
}

// Equivalente con while
function sumarValoresWhile(hasta: number): number {
    let suma = 0;
    let i = 1;
    while (i <= hasta) {
        suma += i;
        i++;
    }
    return suma;
}
```

Estas tres estructuras son especiales porque permiten que un módulo sea demostrable mediante enumeración e inducción matemática.

## 3. La batalla contra el Goto

En 1968, Dijkstra publicó su famosa carta *"Go To Statement Considered Harmful"*. Su postura era que los "buenos" usos de `goto` correspondían a las estructuras de selección e iteración, y que eliminar el uso ilimitado de saltos permitía subdividir el código en unidades cuya corrección podía ser verificada. Con el tiempo, Dijkstra ganó esta batalla y los lenguajes modernos restringieron o eliminaron el `goto` a favor de la programación estructurada.

### Ejemplo con GOTO (C)

```c
// EJEMPLO MALO: Código spaghetti con goto
int procesarArchivo() {
    FILE *archivo = fopen("datos.txt", "r");
    if (!archivo) goto error_apertura;
    
    char linea[100];
    while (fgets(linea, 100, archivo)) {
        int valor = atoi(linea);
        if (valor < 0) goto error_negativo;
        if (valor > 1000) goto error_demasiado_grande;
        procesarValor(valor);
    }
    
    fclose(archivo);
    return 0;
    
error_apertura:
    printf("No se pudo abrir archivo\n");
    return 1;
    
error_negativo:
    printf("Valor negativo encontrado\n");
    fclose(archivo);
    return 2;
    
error_demasiado_grande:
    printf("Valor excede límite\n");
    fclose(archivo);
    return 3;
}
```

**Problemas:**
- El flujo es como un "spaghetti": saltos desordenados.
- Difícil de seguir el camino de ejecución.
- Fácil olvidar liberar recursos (como el `fclose` faltante en `error_apertura`).
- Si agregas nueva lógica, debes actualizar todos los saltos.

### Equivalente simulado en TypeScript (patrón feo)

TypeScript no tiene `goto`, pero podemos simular el caos:

```typescript
// EJEMPLO MALO: Simulando el desorden del GOTO en TypeScript
function procesarArchivoGotoSimulado(datos: string[]): number {
    let errorTipo = 0;
    let idx = 0;
    let archivoAbierto = false;
    
    if (!datos || datos.length === 0) {
        errorTipo = 1;
        gotoLabel('errorApertura');
    }
    archivoAbierto = true;
    
    while (idx < datos.length) {
        const valor = Number(datos[idx]);
        
        if (isNaN(valor)) {
            errorTipo = 2;
            gotoLabel('errorNegativo');
        }
        
        if (valor > 1000) {
            errorTipo = 3;
            gotoLabel('errorDemasiadoGrande');
        }
        
        procesarValor(valor);
        idx++;
        continue;
        
        function gotoLabel(label: string): never {
            switch(label) {
                case 'errorApertura':
                    console.log("Error: No se pudo abrir archivo");
                    throw new Error(`GOTO ${label}`);
                case 'errorNegativo':
                    console.log("Error: Valor negativo");
                    if (archivoAbierto) console.log("Cerrando archivo...");
                    throw new Error(`GOTO ${label}`);
                case 'errorDemasiadoGrande':
                    console.log("Error: Valor demasiado grande");
                    if (archivoAbierto) console.log("Cerrando archivo...");
                    throw new Error(`GOTO ${label}`);
            }
            throw new Error("Label inválida");
        }
    }
    
    return 0;
}
```

### ✅ La solución estructurada correcta

Con solo las tres estructuras (secuencia, selección, iteración), el `goto` es innecesario:

```typescript
// ✅ EJEMPLO BUENO: Programación estructurada sin GOTO
type ResultadoProcesamiento = 
    | { exito: true; mensaje?: string }
    | { exito: false; error: 'apertura' | 'formato' | 'rango'; mensaje: string };

function procesarArchivoEstructurado(datos: string[]): ResultadoProcesamiento {
    // Validación inicial (selección)
    if (!datos || datos.length === 0) {
        return {
            exito: false,
            error: 'apertura',
            mensaje: 'No se pudo abrir o el archivo está vacío'
        };
    }
    
    let valoresProcesados = 0;
    
    // Iteración estructurada
    for (let i = 0; i < datos.length; i++) {
        const valor = Number(datos[i]);
        
        // Selección anidada
        if (isNaN(valor)) {
            return {
                exito: false,
                error: 'formato',
                mensaje: `Valor inválido en línea ${i + 1}: "${datos[i]}"`
            };
        }
        
        if (valor < 0) {
            return {
                exito: false,
                error: 'rango',
                mensaje: `Valor negativo no permitido: ${valor}`
            };
        }
        
        if (valor > 1000) {
            return {
                exito: false,
                error: 'rango',
                mensaje: `Valor excede límite de 1000: ${valor}`
            };
        }
        
        // Procesamiento normal (secuencia)
        procesarValor(valor);
        valoresProcesados++;
    }
    
    // Éxito (secuencia)
    return {
        exito: true,
        mensaje: `Procesados ${valoresProcesados} valores correctamente`
    };
}

function procesarValor(valor: number): void {
    console.log(`Procesando: ${valor}`);
}

// Uso
const datos = ["10", "500", "25", "999", "1500"];
const resultado = procesarArchivoEstructurado(datos);

if (resultado.exito) {
    console.log("✅", resultado.mensaje);
} else {
    console.log("❌", resultado.mensaje);
}
```

## 4. Del teorema de Böhm-Jacopini al método científico

El **teorema de Böhm-Jacopini (1966)** demuestra que cualquier algoritmo puede escribirse usando solo secuencia, selección e iteración. El `goto` es sintácticamente innecesario. Por eso los lenguajes modernos (TypeScript, Python, Java, Rust, etc.) ni siquiera lo incluyen.

Aunque Dijkstra soñaba con construir una jerarquía euclidiana de teoremas para demostrar que cada función era correcta, el mundo de la programación nunca adoptó ese nivel de formalidad matemática. En su lugar, el desarrollo de software adoptó el **método científico**:

- **Falsificabilidad**: La ciencia no demuestra que algo es verdad, sino que intenta demostrar que es falso. Si no se puede demostrar que es falso tras repetidos intentos, se considera "lo suficientemente correcto".
- **Pruebas (Testing)**: Las pruebas de software funcionan igual; solo demuestran la presencia de errores, nunca su ausencia. Un programa estructurado es aquel que puede ser descompuesto en funciones pequeñas que son falsificables (comprobables).

> *"La prueba de programas puede usarse para mostrar la presencia de errores, pero nunca para mostrar su ausencia."* — Edsger Dijkstra

## 5. Caso real: validación de formulario

### Versión NO estructurada (con GOTO mental)

```typescript
// Versión NO estructurada (con GOTO mental)
function validarFormularioMalo(formulario: any): boolean {
    if (!formulario.nombre) goto errorNombre;
    if (!formulario.email) goto errorEmail;
    if (formulario.edad < 18) goto errorEdad;
    if (!formulario.terminos) goto errorTerminos;
    return true;
    
    errorNombre: { console.log("Falta nombre"); return false; }
    errorEmail: { console.log("Falta email"); return false; }
    errorEdad: { console.log("Menor de edad"); return false; }
    errorTerminos: { console.log("Acepte términos"); return false; }
}
```

### ✅ Versión estructurada

```typescript
// ✅ Versión estructurada
function validarFormulario(formulario: any): { valido: boolean; errores: string[] } {
    const errores: string[] = [];
    
    if (!formulario.nombre) errores.push("El nombre es obligatorio");
    if (!formulario.email) errores.push("El email es obligatorio");
    if (formulario.edad < 18) errores.push("Debe ser mayor de 18 años");
    if (!formulario.terminos) errores.push("Debe aceptar los términos");
    
    return {
        valido: errores.length === 0,
        errores: errores
    };
}
```

## Conclusión del capítulo

La lección para los arquitectos de software es que deben esforzarse por definir módulos, componentes y servicios que sean fácilmente falsificables (probables). Al restringir el flujo de control a estructuras simples, permitimos que nuestro software sea analizado y verificado de la misma manera que una teoría científica.

### Resumen comparativo

| Concepto | Sin estructura (GOTO / spaghetti) | Con estructura (programación estructurada) |
|----------|----------------------------------|------------------------------------------|
| Flujo | Impredecible, saltos arbitrarios | Lineal, predecible |
| Depuración | Dificultad para encontrar bugs | Fácil de razonar línea por línea |
| Recursos | Liberación manual propensa a errores | Liberación automática al salir del bloque |
| Mantenibilidad | Hay que reestructurar saltos | Solo cambias el bloque específico |
| Composición | No se puede componer fácilmente | Las funciones se pueden componer |
| Testing | Múltiples caminos difíciles de seguir | Cada camino en una estructura clara |

La programación estructurada te da disciplina, legibilidad y mantenibilidad al eliminar saltos arbitrarios. El `goto` es como un "teletransporte" en medio de una novela: rompe el flujo esperado y confunde al lector.

---

[Navegación](../README.md)
