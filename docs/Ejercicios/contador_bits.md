# Contador de bits

Escribe una función que tome un número entero como entrada y devuelva la cantidad de bits que son iguales a uno en la representación binaria de ese número. Puedes asumir que la entrada es un número no negativo.

Ejemplo: La representación binaria de 1234 es 10011010010, por lo tanto la función debería devolver 5 en este caso.

# Solución

```typescript
function decimalABinario(numero) {
  let binario = "";
  let numeroDividido = null;
  let numeroCurrent = numero;
  let resultadoDivision = null;
  
  while (resultadoDivision > 0) {
    let residuo = numeroCurrent % 2;
    numeroCurrent = numeroCurrent / 2;
    
  }
}


export function countBits(n: number): number {
  // Verificar que el número sea no negativo
  if (n < 0) {
    return 0
  }
  
  
  // Convertir número a binario
  const numeroBinario = n.toString(2);
  console.log("Numero binario: ", numeroBinario)
  
  
  //contar caracteres 1
  const cantidad = numeroBinario.split("1").length - 1;
  console.log("Cantidad: ", cantidad)
  
  // Retornar cantidad
  return cantidad
}
```
