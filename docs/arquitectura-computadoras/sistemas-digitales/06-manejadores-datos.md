# Manejadores de Datos

<iframe width="560" height="315" src="https://www.youtube.com/embed/5qtIdZZ7Ae0" title="Manejadores de Datos" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## 1. Codificadores y Decodificadores

![Codificadores y Decodificadores](https://electronicaonline.net/wp-content/uploads/2021/05/codificadores-y-decodificadores.jpg)

### 1.1 Decodificadores

Un **decodificador** es un circuito que convierte un código binario de entrada de n bits en una de m líneas de salida.

<iframe width="560" height="315" src="https://www.youtube.com/embed/T8vqZqTPOdU" title="Decodificadores" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

- **Funcionamiento:** Para cada combinación de entrada, una y solo una de las salidas se activa (valor lógico 1 o bajo, según el diseño).
- **Relación de líneas:** Se rige por la fórmula 2ⁿ ≥ m.
- **Aplicaciones:** Se utilizan para direccionar memorias y periféricos, convertir códigos e implementar funciones lógicas.
- **Dato técnico:** Se pueden conectar en paralelo para construir decodificadores más grandes (por ejemplo, dos de 3×8 para formar uno de 4×16).

### 1.2 Codificadores

Realizan la función inversa del decodificador: presentan en su salida el código binario correspondiente a la entrada que ha sido activada.

<iframe width="560" height="315" src="https://www.youtube.com/embed/GszoK0A1zBU" title="Codificadores" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

- **Estructura:** Tienen 2ⁿ entradas y n salidas (el número de entradas es mayor al de salidas).
- **Codificadores de Prioridad:** Son un tipo especial (como el chip comercial 74LS148) que soluciona el conflicto cuando se activan dos entradas al mismo tiempo, asignando la salida a la entrada con mayor peso o prioridad.

### 1.3 MSB y LSB en Codificadores y Decodificadores

**Decodificadores:** Toman un código binario donde el bit de la izquierda es el **MSB** y activan la salida correspondiente. Si el MSB cambia, el decodificador saltará a una dirección de salida lejana (por ejemplo, del 0 al 8 en un DECO de 4 a 16).

**Codificadores de Prioridad:** Si se activan varias entradas a la vez, el circuito genera el código de la entrada con **mayor peso (MSB)**, ignorando las de menor peso o **LSB**.

### Comparativa: Codificadores vs. Decodificadores

Este par se encarga de la **traducción de códigos**.

| Característica | Decodificador | Codificador |
|---|---|---|
| **Alias** | Traductor | Resumidor |
| **Entradas vs. Salidas** | Pocas entradas → Muchas salidas | Muchas entradas → Pocas salidas |
| **Función principal** | Traducir un código binario a una línea específica | Convertir una línea activa en un código binario |
| **Descripción** | Toma un código binario de `n` bits y activa una sola de sus `m` líneas de salida (`2ⁿ ≥ m`). "Detecta" una combinación y dice *"esta es la opción seleccionada"*. | Recibe muchas entradas y genera un código binario corto que representa cuál se activó. Tiene `2ⁿ` entradas y `n` salidas. |
| **Uso típico** | Direccionar memorias y periféricos | Compactar señales en un código |

## 2. Multiplexores y Demultiplexores

![Multiplexores y Demultiplexores](http://electronicaonline.net/wp-content/uploads/2021/04/Mux-y-Demux.jpg)

### 2.1 Multiplexores o Selectores de Datos

Es un dispositivo que permite seleccionar información digital de varias fuentes y enviarla a una única línea de salida.

<iframe width="560" height="315" src="https://www.youtube.com/embed/galAEs4yOcE" title="Multiplexores" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

- **Mecanismo:** La selección de la entrada se realiza mediante líneas de selección (n bits para seleccionar entre 2ⁿ entradas).
- **Uso avanzado:** Son extremadamente útiles para implementar funciones booleanas directamente desde una tabla de verdad sin necesidad de muchas compuertas adicionales. El chip 74LS157 es un ejemplo comercial de un cuádruple multiplexor 2×1.

#### Tipos de Multiplexores

Existen varios tipos de multiplexores (MUX) según el número de entradas que manejan:

##### Multiplexor 2×1 (2 a 1)

- **Entradas:** 2
- **Líneas de selección:** 1
- **Funcionamiento:** Si la selección es 0, pasa la primera entrada; si es 1, pasa la segunda
- **Ejemplo comercial:** 74LS157 (cuádruple MUX 2×1)

![Multiplexor 2:1](https://www.allelcoelec.es/upfile/images/21/20250227145824325.png)

##### Multiplexor 4×1 (4 a 1)

- **Entradas:** 4
- **Líneas de selección:** 2 (A, B)
- **Funcionamiento:** Las combinaciones 00, 01, 10, 11 determinan cuál de las 4 entradas se conecta a la salida
- **Ejemplo comercial:** 74LS153 (doble MUX 4×1)

![Multiplexor 4:1](https://areatecnologia.com/electronica/imagenes/multiplexor-4-a-1.jpg)

##### Multiplexor 8×1 (8 a 1)

- **Entradas:** 8
- **Líneas de selección:** 3 (2³ = 8)
- **Funcionamiento:** 3 bits de selección eligen entre 8 entradas
- **Ejemplo comercial:** 74LS151

![Multiplexor 8:1](https://www.allelcoelec.es/upfile/images/63/20250227152132771.png)

> **Para entenderlo visualmente:** imagina un interruptor rotatorio — las entradas son los cables que llegan al interruptor, la salida es el cable que sale de él, y las líneas de selección son tu mano moviendo la perilla para decidir qué cable conectar.

### 2.2 Demultiplexores o Distribuidores de Datos

Realizan la operación contraria al multiplexor: toman una única entrada y la encaminan hacia una de las múltiples salidas disponibles (m salidas).

<iframe width="560" height="315" src="https://www.youtube.com/embed/63-ALPSB5u0" title="Demultiplexores" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

- **Mecanismo:** Al igual que el multiplexor, utiliza líneas de selección para determinar la ruta del dato.
- **Relación con los Decodificadores:** Una característica importante es que un decodificador puede ser utilizado como demultiplexor si se usa su pin de habilitación (Enable) como la entrada de datos seriales.

### 2.3 Línea de Selección o Control

![Línea de Selección o Control](https://electronicaonline.net/wp-content/uploads/2021/04/Demultiplexor-o-demux.jpg)

El **bit de línea de selección** (o simplemente línea de selección) es una señal de control fundamental en los circuitos manejadores de datos, específicamente en los **Multiplexores (MUX)** y **Demultiplexores (DEMUX)**.

Su función principal depende del tipo de circuito en el que se utilice:

- **En un Multiplexor (Selector de datos):** Los bits de selección actúan como un interruptor que decide cuál de las múltiples fuentes de entrada (\(2^{n}\)) se encaminará hacia la única línea de salida. Por ejemplo, en un multiplexor 2x1, si el bit de selección S es 0, se selecciona la entrada A; si es 1, se selecciona la entrada B.

- **En un Demultiplexor (Distribuidor de datos):** Funcionan de manera inversa, determinando por cuál de las múltiples salidas disponibles se enviará el dato de la única entrada.

**Características clave:**

- **Relación matemática:** El número de bits de selección (\(n\)) determina cuántas entradas o salidas se pueden gestionar, siguiendo la fórmula \(2^{n}\). Por ejemplo:
  - 1 bit de selección permite elegir entre 2 opciones (\(2^{1}\)).
  - 2 bits de selección permiten elegir entre 4 opciones (\(2^{2}\)).
  - 3 bits de selección permiten elegir entre 8 entradas de datos (como en el chip comercial 74LS151).

- **Control de flujo:** Estos bits no contienen los datos en sí, sino que controlan el paso de la información a través del circuito lógico.

- **Implementación de funciones:** Al manipular estos bits de selección y compararlos con las variables de entrada, es posible implementar funciones booleanas complejas directamente con un multiplexor sin necesidad de usar múltiples compuertas lógicas individuales.

En resumen, los bits de línea de selección son las "instrucciones" que le indican al hardware digital qué camino debe seguir el flujo de datos en un momento determinado.

### 2.4 MSB y LSB en las Líneas de Selección

En circuitos como los **Multiplexores (MUX)** y **Demultiplexores (DEMUX)**, se utilizan bits de selección para decidir qué camino toma el dato.

- **Identificación del peso:** En un chip con varias entradas de selección (como \(S_{0}, S_{1}, S_{2}\)), generalmente \(S_{0}\) es el **LSB** (menor peso) y la de índice más alto es el **MSB** (mayor peso).

- **Implementación de funciones:** Al usar un multiplexor para realizar una función lógica, se deben tomar las variables más significativas (**MSB**) de la tabla de verdad y conectarlas a las líneas de control del MUX.

- **Regla de Oro:** ¡Cuidado con el orden! Intercambiar el MSB con el LSB en las líneas de selección cambiaría completamente la lógica de salida del circuito.

### Comparativa: Multiplexores vs. Demultiplexores

Este par se encarga del **enrutamiento o flujo de los datos**.

| Característica | Multiplexor (MUX) | Demultiplexor (DEMUX) |
|---|---|---|
| **Alias** | Selector | Distribuidor |
| **Entradas vs. Salidas** | Muchas entradas → Una salida | Una entrada → Muchas salidas |
| **Rol** | Selector de datos (selecciona *quién* pasa) | Distribuidor de datos (selecciona *a dónde* va) |
| **Descripción** | Funciona como un embudo o interruptor rotatorio. Selecciona información de varias fuentes (`2ⁿ` entradas) y la envía a una única línea de salida. | Toma una única entrada y la encamina hacia una de las muchas salidas disponibles, usando líneas de selección. |
| **Dato curioso** | — | Un decodificador puede funcionar como demultiplexor usando su pin de *Enable* como entrada de datos.

## 3. Ejercicios

### Reducción de MUX 8:1 a 4:1

<iframe width="560" height="315" src="https://www.youtube.com/embed/qKoaj0ea7js" title="Reducción de MUX 8:1 a 4:1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### De MUX 8:1 a dos MUX 4:1

<iframe width="560" height="315" src="https://www.youtube.com/embed/tUb6qafPmnA" title="De MUX 8:1 a dos MUX 4:1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
