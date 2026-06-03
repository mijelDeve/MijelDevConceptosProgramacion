# Circuitos Aritméticos

## 1. Comparadores

Estos circuitos verifican la relación de igualdad entre magnitudes binarias.

- **Fundamento:** Se basan en la función XNOR, que entrega un "1" lógico solo cuando las entradas son iguales.
- **Comparador de 4 bits:** Se utilizan compuertas XNOR en paralelo conectadas a una compuerta AND final que valida la igualdad de todos los bits.
- **Versión comercial:** Se presenta el chip 7485, un comparador de magnitudes de 4 bits utilizado en la industria.

## 2. Adición Binaria (Suma)

La suma digital sigue reglas específicas basadas en el sistema de base 2:

- **Reglas básicas:** 0+0=0; 0+1=1; 1+0=1; y 1+1=10₂ (resultado 0 con acarreo de 1).
- **Semi-sumador (Half Adder):** Circuito que suma dos operandos de 1 bit. Sus ecuaciones son:
  - Suma (S): A⊕B
  - Acarreo (C): A·B
- **Sumador Completo (Full Adder):** A diferencia del anterior, este circuito permite sumar números de varios bits al considerar un acarreo de entrada (Cᵢₙ) proveniente de la etapa anterior.
  - Ecuación de Suma: S = A ⊕ B ⊕ Cᵢₙ
  - Ecuación de Acarreo de salida (Cₒᵤₜ): AB + ACᵢₙ + BCᵢₙ

## 3. Sustracción Binaria (Resta)

Para optimizar el hardware, la resta no se realiza directamente, sino que se transforma en una suma utilizando métodos de complementación:

- **Complemento a 2 (C₂):** Es el método estándar. Se obtiene invirtiendo los bits del número (NOT) y sumando 1 (N' + 1).
- **Reglas para interpretar el resultado (A − B):**
  - Si hay acarreo final (Cₛ = 1): El resultado es positivo y se lee directamente.
  - Si NO hay acarreo final (Cₛ = 0): El resultado es negativo y el valor real se encuentra representado en su forma de complemento a 2.
- **Restador Completo:** Circuito que resta dos bits considerando una deuda de entrada (Dᵢₙ), produciendo un bit de Resta (R) y uno de Deuda de salida (Dₒᵤₜ).

## 4. ALU (Unidad Aritmética Lógica) Básica

La clase concluye explicando cómo integrar estos conceptos en una pequeña ALU para sumar y restar 2 bits.

- Utiliza un sumador de 2 bits como núcleo.
- Implementa una línea de control (k): cuando k = 0 el sistema realiza una suma, y cuando k = 1 realiza una resta mediante la inversión de los bits y el uso del acarreo de entrada para completar el complemento a 2.

## 5. Ejercicios

### Multiplicador de 2 bits

<iframe width="560" height="315" src="https://www.youtube.com/embed/E4GiJalVHpo" title="Multiplicador de 2 bits" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
