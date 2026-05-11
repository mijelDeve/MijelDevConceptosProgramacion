# Mascara de tarjeta de crédito

Normalmente, cuando compras algo, te preguntan si tu número de tarjeta de crédito, número de teléfono o la respuesta a tu pregunta secreta sigue siendo correcta. Sin embargo, como alguien podría mirar por encima de tu hombro, no quieres que esa información aparezca en la pantalla. En su lugar, la ocultamos.

Tu tarea es escribir una función llamada maskify, que cambie todos los caracteres excepto los últimos cuatro por #.

Ejemplos (entrada --> salida):

```
"4556364607935616" --> "############5616"
"64607935616"      --> "#######5616"
"1"                --> "1"
""                 --> ""

// "¿Cómo se llamaba tu primera mascota?"
"Skippy" --> "##ippy"

"Nananananananananananananananana Batman!"
--> "####################################man!"
```


# Solución

```typescript
// return masked string
export function maskify(cc: string): string {
  const cantidadDigitos = cc.length;
  console.log(cantidadDigitos)
  
  if (cantidadDigitos <= 4) {
    return cc
  }
  
  // obtener los últimos 4 dígitos del string
  const ultimos4 = cc.slice(-4);
  console.log(ultimos4)
  
  // obtener la cantidad de dígitos
  
  const cantidadSinDigitos = cantidadDigitos - 4;
  console.log(cantidadSinDigitos)
  // Crear nuevo string y pushear la cantidad total - 4
  const nuevoString = "#".repeat(cantidadSinDigitos)
  console.log("Nuevo String: ", nuevoString)
  
  // Pushear los 4 dígitos restantes
  const stringEnmascarado = nuevoString + String(ultimos4)
  console.log(stringEnmascarado)
  
  return stringEnmascarado;
}
```
