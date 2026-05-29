# Capítulo 12: Componentes

## 1. ¿Qué es un componente?

Los componentes son las **unidades de despliegue**. Son las entidades más pequeñas que pueden ser implementadas como parte de un sistema.

- En Java, son archivos `.jar`.
- En .NET, son archivos DLL.
- En Ruby, son archivos de gemas.

Independientemente del lenguaje, los componentes son el "gránulo de implementación" y, si están bien diseñados, siempre conservan la capacidad de ser **desarrollados y desplegados de forma independiente**.

## 2. Una breve historia de la evolución tecnológica

El capítulo relata cómo la forma en que manejamos el código ha cambiado drásticamente debido a las limitaciones del hardware antiguo:

### Inicios y direccionamiento físico

En los primeros días, los programadores debían decidir la dirección de memoria donde se cargaría el código. Las bibliotecas de funciones se cargaban en direcciones conocidas, pero si la biblioteca crecía demasiado, chocaba con la aplicación, obligando a fragmentar el código en segmentos, lo cual era difícil de gestionar.

### Reubicabilidad

Para solucionar esto, surgieron los **binarios reubicables**. El compilador generaba metadatos que permitían al cargador mover el código en la memoria según fuera necesario. Sin embargo, a medida que los programas crecían, este proceso se volvió extremadamente lento.

### Enlazadores (Linkers)

Surgieron para permitir que los programadores dividieran sus programas en módulos compilados por separado que luego se unían en un archivo ejecutable. Con el tiempo, incluso los enlazadores se volvieron lentos (llegando a tardar horas en sistemas grandes).

## 3. La era actual: El enlazado dinámico

Gracias a la **Ley de Moore**, la velocidad de las computadoras aumentó más rápido que el tamaño de los programas. Esto permitió que el tiempo de enlazado se redujera de horas a segundos. Hoy en día, archivos vinculados dinámicamente que se conectan en tiempo de ejecución son los componentes de software de nuestras arquitecturas. Lo que antes era un "esfuerzo hercúleo" de gestión de memoria, ahora es una práctica cotidiana y sencilla.

## Conclusión del capítulo

Hemos llegado a un punto donde la **arquitectura de complementos (plugins)** es el estándar. Los componentes son los bloques que permiten que un sistema sea escalable y mantenible, siempre que se respete su capacidad de ser **unidades de despliegue independientes**.
