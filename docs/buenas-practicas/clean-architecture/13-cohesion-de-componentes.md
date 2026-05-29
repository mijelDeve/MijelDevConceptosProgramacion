# Capítulo 13: Cohesión de componentes

## 1. REP: El Principio de Equivalencia de Reutilización/Liberación

**Concepto:** "El grano de la reutilización es el grano de la liberación".

**Explicación:** Para que un componente sea reutilizable de forma efectiva, debe ser rastreado mediante un proceso de lanzamiento y contar con números de versión.

**Implicación arquitectónica:** Las clases y módulos que se agrupan en un componente deben poder publicarse juntos, compartir el mismo seguimiento y estar incluidos en la misma documentación. Básicamente, si las clases tienen un propósito común de reutilización, deben estar juntas.

## 2. CCP: El Principio de Cierre Común

**Concepto:** "Reúna en el mismo componente aquellas clases que cambian por las mismas razones y en los mismos momentos".

**Relación con SOLID:** Es el equivalente al Principio de Responsabilidad Única (SRP) pero aplicado a componentes.

**Objetivo:** Minimizar el impacto de los cambios. Si surge una necesidad de modificación, lo ideal es que afecte a un solo componente en lugar de estar dispersa por todo el sistema. Esto facilita enormemente el despliegue y las pruebas.

## 3. CRP: El Principio de Reutilización Común

**Concepto:** "No obligues a los usuarios de un componente a depender de cosas que no necesitan".

**Relación con SOLID:** Es la versión genérica del Principio de Segregación de Interfaz (ISP).

**Explicación:** Este principio nos ayuda a decidir qué clases **no** deben estar juntas. Cuando dependemos de un componente, dependemos de cada una de sus clases; por lo tanto, no queremos que un cambio en una clase que ni siquiera usamos nos obligue a recompilar e implementar todo el componente de nuevo.

## 4. El Diagrama de Tensión de la Cohesión

El autor explica que estos tres principios están en conflicto constante:

- **REP y CCP** son principios **inclusivos**: tienden a hacer que los componentes sean más grandes para favorecer la reutilización y la mantenibilidad frente a cambios.
- **CRP** es un principio **exclusivo**: tiende a fragmentar los componentes para evitar dependencias innecesarias.

Un buen arquitecto debe encontrar el punto de equilibrio en este "triángulo de tensión" basándose en el estado actual del proyecto. Por ejemplo, al inicio de un desarrollo se suele priorizar el CCP para facilitar el avance rápido, pero a medida que el proyecto madura, la tensión se desplaza hacia el REP para facilitar su reutilización por otros equipos.

## Conclusión del capítulo

La cohesión de los componentes **no es un atributo estático**. La forma en que dividimos el sistema debe evolucionar con el tiempo, adaptándose a la balanza entre la facilidad de desarrollo y la facilidad de reutilización.
