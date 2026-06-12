# Capítulo 14: Acoplamiento de componentes

## 1. El problema: El "Síndrome del día después"

**Concepto:** Este síndrome ocurre cuando muchos desarrolladores modifican los mismos archivos y, al llegar por la mañana, descubren que su código ya no funciona porque alguien más cambió una dependencia de la que dependían.

**Solución arquitectónica:** Dividir el sistema en componentes liberables con números de versión, permitiendo que cada equipo decida cuándo integrar las nuevas versiones de los demás.

## 2. ADP: El Principio de las Dependencias Acíclicas

**Regla de oro:** No se deben permitir ciclos en el gráfico de dependencias de los componentes.

**Estructura ideal:** Las dependencias deben formar un **Gráfico Acíclico Dirigido (DAG)**. Si existen ciclos, los componentes involucrados se vuelven dependientes entre sí de forma inseparable, dificultando enormemente las pruebas y el despliegue.

**Cómo romper ciclos:** Se puede usar el Principio de Inversión de Dependencia (DIP) mediante interfaces o crear un nuevo componente intermedio del que ambos dependan.

## 3. SDP: El Principio de las Dependencias Estables

**Concepto:** "Dependa en la dirección de la estabilidad".

**Estabilidad:** En arquitectura, un componente es estable si es difícil de cambiar. Esto sucede cuando es "responsable" (muchos dependen de él) e "independiente" (él no depende de casi nada).

**Métrica de Inestabilidad (I):** Se calcula como el ratio de dependencias salientes sobre el total de dependencias. Un valor de `I=0` es máxima estabilidad e `I=1` es máxima inestabilidad. Los componentes destinados a cambiar a menudo deben ser inestables y depender de componentes estables.

## 4. SAP: El Principio de las Abstracciones Estables

**Concepto:** "Un componente debe ser tan abstracto como estable sea".

**Lógica:** Las políticas de alto nivel deben ser estables, pero si son estables son difíciles de cambiar. Para resolver esto, deben ser abstractas (interfaces), permitiendo que su comportamiento se extienda sin modificar el componente estable.

## 5. La Secuencia Principal y las Zonas de Exclusión

El autor propone un gráfico que relaciona la Abstracción (A) con la Inestabilidad (I) para evaluar la salud de los componentes:

- **La Zona del Dolor:** Componentes muy estables (I bajo) pero muy concretos (A bajo). Son difíciles de cambiar pero no son abstractos, lo que genera rigidez (ejemplo: esquemas de base de datos volátiles).

- **La Zona de Inutilidad:** Componentes muy abstractos pero sin nadie que dependa de ellos. Es código "muerto" o abstracciones innecesarias.

- **La Secuencia Principal:** Es la línea ideal donde los componentes están equilibrados. Un buen arquitecto busca que sus componentes se ubiquen lo más cerca posible de esta línea.

## Conclusión del capítulo

La arquitectura de componentes no es estática; las dependencias deben ser monitoreadas continuamente para evitar ciclos y asegurar que la estabilidad y la abstracción estén correctamente balanceadas según la evolución del proyecto.
