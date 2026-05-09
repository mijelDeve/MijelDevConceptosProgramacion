# Número Narcisista (o Número Armstrong)

Un **Número Narcisista** (o **Número Armstrong**) es un número positivo que es igual a la suma de sus propios dígitos, cada uno elevado a la potencia del número total de dígitos del número, en una base determinada.  
En este Kata, nos limitaremos al sistema decimal (base 10).

Por ejemplo, tomemos **153** (3 dígitos), que sí es narcisista:

```text
1^3 + 5^3 + 3^3 = 1 + 125 + 27 = 153
```

Y 1652 (4 dígitos), que no lo es:

```text
1^4 + 6^4 + 5^4 + 2^4 = 1 + 1296 + 625 + 16 = 1938
```

El desafío

Tu código debe devolver true o false (no las cadenas 'true' y 'false') dependiendo de si el número dado es un Número Narcisista en base 10.

Esto puede ser True y False dependiendo del lenguaje, por ejemplo en PHP.

No es necesario realizar validaciones para cadenas de texto u otras entradas inválidas; únicamente se recibirán enteros positivos distintos de cero.


# Solución

```typescript
function narcissistic(value) {
  // Obtener la cantidad de números
  const cantidadNumeros = Math.abs(value).toString().length;
  // Obtener cada digito de un número 
  const digitos = value.toString().split("").map(Number);
  
  // Recorrer cada digito y elevarlo a la cantidad
  const digitosElevados = digitos.map((digito) => digito ** cantidadNumeros)
  
  // Sumar todos los digitos transformados
  
  const suma = digitosElevados.reduce((acc, n) => acc + n, 0)
  
  return suma == value
}
```
