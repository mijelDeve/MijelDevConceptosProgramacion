# Sistemas de Numeración y Códigos Digitales

Esta sección explica cómo se representan las cantidades mediante diferentes bases numéricas y cómo estas son interpretadas por el hardware de una computadora.

## Sistemas de Numeración

Se presentan cuatro sistemas fundamentales:

- **Decimal (base 10):** Sistema numérico de uso cotidiano, compuesto por los dígitos del 0 al 9.
- **Binario (base 2):** Sistema fundamental en computación, compuesto por los dígitos 0 y 1.
- **Octal (base 8):** Compuesto por los dígitos del 0 al 7.
- **Hexadecimal (base 16):** Compuesto por los dígitos del 0 al 9 y las letras A-F.

## Fundamento del Hardware

El sistema binario (introducido por Leibniz) es el estándar en computación porque se basa en dos estados estables: 0 (apagado) y 1 (encendido).

## Unidades de Medida

- **Bit:** Unidad mínima de información.
- **Nibble:** 4 bits.
- **Byte:** 8 bits.

Las unidades de almacenamiento superiores (KB, MB, GB, TB) se calculan en múltiplos de 1024.

## Conversiones entre Bases

- **Decimal a otra base:** Se utilizan divisiones sucesivas.
- **Cualquier base a decimal:** Se utiliza la suma de pesos o potencias de la base.

### Fórmula de Generalización

Es la base para convertir cualquier número de base `b` a decimal:

```
N = Σ aᵢ · bⁱ
```

## Aritmética Binaria

Se introducen las reglas para realizar operaciones básicas (suma, resta, etc.) directamente en binario.

### Sustracción por Complemento a 2 (C₂)

En hardware la resta `A - B` se procesa como la suma de `A` más el complemento a 2 de `B`:

```
C = A + C₂(B)
```

### LSB y MSB

- **LSB (Least Significant Bit):** Bit menos significativo, ubicado a la derecha.
- **MSB (Most Significant Bit):** Bit más significativo, ubicado a la izquierda.

## Códigos Digitales

### BCD (Binary Coded Decimal)

Representa cada dígito decimal con 4 bits. Variantes:

- **BCD Natural:** Codificación directa del dígito decimal.
- **BCD Aiken:** Código ponderado con simetría y complemento a 9.
- **BCD Exceso 3:** Código que desplaza el valor en 3 unidades, útil para operaciones de resta.

### Código Gray

Código no ponderado, progresivo y cíclico donde solo cambia un bit entre una combinación y la siguiente. Se utiliza para evitar errores en industria y robótica.

### ASCII

Código alfanumérico estándar para representar caracteres.

---

## Puntos Fundamentales para el Examen

Para aprobar el examen, debes dominar estos conceptos técnicos específicos:

1. **Fórmula de Generalización:** Base para convertir cualquier número de base `b` a decimal.
2. **LSB y MSB:** Identificar el bit menos significativo (derecha) y el bit más significativo (izquierda).
3. **Sustracción por Complemento a 2 (C₂):** La resta `A - B` se procesa como `A + C₂(B)`.
4. **Características del Código Gray:** Código no ponderado, progresivo y cíclico donde solo cambia un bit entre estados.
5. **Simetría en BCD:** Identificar la simetría y el complemento a 9 en los códigos Aiken y Exceso 3.

## Tipos de Preguntas de Examen

### 1. Ejercicios de Conversión (Prácticos)

- "Convertir el número `151₁₀` a binario" (divisiones sucesivas por 2).
- "Convertir el número `31F₁₆` a decimal" (multiplicando cada dígito por potencias de 16).
- "Convertir `4750₈` a decimal".

### 2. Operaciones Aritméticas (Prácticos)

- "Calcular la sustracción binaria de `57 - 34` utilizando el método de complemento a 2".
- "Realizar la suma binaria de las siguientes cifras: `1011 + 100 + 10`".

### 3. Preguntas Teóricas y de Definición

- "Defina Bit, Nibble y Byte".
- "¿Cuál es la principal ventaja del Código Gray en la industria y robótica?" — Asegura menos posibilidades de error al cambiar solo un dígito entre estados.
- "¿En qué se basa el sistema binario para ser utilizado en máquinas electrónicas?" — En dos estados estables, 0 y 1.
