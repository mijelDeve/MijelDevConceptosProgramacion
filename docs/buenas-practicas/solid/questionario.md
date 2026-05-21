# Cuestionario — Principios SOLID

## 1. ¿En un sistema de domótica, la clase 'ControladorLuces' instancia directamente un objeto 'BombillaPhilips' en su constructor para encender la luz. ¿Qué principio SOLID se está violando principalmente?

- A) Principio de Responsabilidad única
- B) Principio de inversión de dependencias
- C) Principio de segregación de interfaces
- D) Principio de sustitución de Liskov

---

## 2. Se tiene una clase 'GeneradorReporte' que contiene lógica para consultar la base de datos, procesar cálculos estadísticos y dar formato HTML al resultado. ¿Cuál es el riesgo de este diseño según el SRP?

- A) Aumento del acoplamiento con interfaces externas.
- B) Imposibilidad de heredar de la clase generadora.
- C) Reducción innecesaria del rendimiento del sistema.
- D) Tener múltiples razones para cambiar el código.

---

## 3. Un desarrollador utiliza un bloque 'switch' dentro de una clase 'CalculadoraImpuestos' para verificar el tipo de país (EEUU, España, México) y aplicar la tasa correspondiente. ¿Cómo mejoraría esto siguiendo el Principio Abierto/Cerrado (OCP)?

- A) Creando una interfaz 'IEstrategiaImpuesto' y una implementación por cada país.
- B) Moviendo el bloque 'switch' a una clase privada dentro del mismo archivo.
- C) Utilizando herencia para que 'CalculadoraEspaña' herede de 'CalculadoraImpuestos'.
- D) Cambiando el 'switch' por una serie de sentencias 'if-else' más legibles.

---

## 4. La interfaz 'IAve' define el método 'volar()'. La clase 'Avestruz' implementa 'IAve' pero lanza una excepción en 'volar()' porque no puede realizar esa acción. ¿Qué principio se viola?

- A) Principio de Responsabilidad Única
- B) Principio de Segregación de Interfaces
- C) Principio de Sustitución de Liskov
- D) Principio de Inversión de Dependencia

---

## 5. Una interfaz 'IProcesadorArchivos' incluye métodos para 'leer()', 'escribir()', 'comprimir()' y 'encriptar()'. Una clase 'LectorSimple' solo necesita leer y escribir. ¿Cuál es la solución sugerida por el ISP?

- A) Implementar una excepción 'NotImplementedException' en los métodos no usados.
- B) Dividir 'IProcesadorArchivos' en interfaces más pequeñas como 'ILector' y 'ICompresor'.
- C) Hacer que 'LectorSimple' herede de una clase base que ya tiene implementaciones por defecto.
- D) Dejar los métodos innecesarios vacíos en la clase 'LectorSimple'.

---

## 6. En un videojuego, la clase 'Guerrero' depende de la clase concreta 'EspadaHierro'. Para permitir que el guerrero use un 'Hacha' o 'Arco', ¿qué cambio refleja mejor el DIP?

- A) Usar un objeto global para gestionar todas las armas disponibles.
- B) Añadir métodos 'usarEspada()' y 'usarArco()' dentro de la clase 'Guerrero'.
- C) Hacer que 'Guerrero' dependa de una interfaz 'IArma'.
- D) Crear subclases como 'GuerreroConEspada' y 'GuerreroConArco'.

---

## 7. Se desea implementar un sistema de exportación de datos. Si aplicamos correctamente el OCP, ¿qué debería ocurrir cuando necesitemos exportar a un nuevo formato como XML?

- A) Deberíamos usar herencia para sobreescribir el método de exportación en una subclase 'ExportadorXML'.
- B) Deberíamos añadir un nuevo parámetro al método 'exportar' de la clase principal.
- C) Deberíamos actualizar la lógica central para incluir el nuevo formato en el flujo principal.
- D) Deberíamos crear una nueva clase que implemente una interfaz de exportación sin tocar las existentes.

---

## 8. La clase 'SistemaNotificaciones' envía mensajes por SMS. Se decide que ahora también debe guardar una copia en una base de datos SQL. Si el desarrollador añade el código SQL en la misma función de envío, está fallando en:

- A) Principio de Sustitución de Liskov
- B) Principio de Responsabilidad Única
- C) Principio de Segregación de Interfaces
- D) Principio de Inversión de Dependencia

---

## 9. Al diseñar una jerarquía de 'Vehiculos', la clase 'Bicicleta' hereda de 'VehiculoMotorizado'. El método 'arrancarMotor()' no tiene sentido para la bicicleta. Esto es una violación de:

- A) Principio Abierto/Cerrado
- B) Principio de Responsabilidad Única
- C) Principio de Sustitución de Liskov
- D) Principio de Inversión de Dependencia

---

## 10. En el contexto de SOLID, ¿qué significa que una clase debe estar 'cerrada a la modificación'?

- A) Que los atributos de la clase deben ser todos privados y constantes.
- B) Que la clase debe ser declarada como 'final' o estática para que nadie la use.
- C) Que no se permite añadir nuevos métodos a la clase en versiones futuras.
- D) Que no se debe alterar su código fuente una vez que ha sido probado y estabilizado.

---

## 11. Si una clase 'Pedido' necesita enviar un correo electrónico de confirmación, ¿cuál es la mejor forma de aplicar SOLID?

- A) Crear un método 'enviarEmail()' dentro de la clase 'Pedido'.
- B) Usar una clase estática 'UtilidadesEmail' dentro del método de procesar pedido.
- C) Hacer que 'Pedido' herede de una clase 'EmailSender'.
- D) Pasar una interfaz 'IServicioEmail' al 'Pedido' mediante el constructor.

---

## 12. Un sistema de autenticación admite 'LoginGoogle' y 'LoginFacebook'. Para añadir 'LoginApple' sin violar el OCP, deberíamos:

- A) Implementar una nueva clase que cumpla con la interfaz 'IAutenticador'.
- B) Eliminar las clases específicas y usar una sola clase con muchos parámetros opcionales.
- C) Modificar el controlador de login para añadir un nuevo caso en el validador.
- D) Crear una base de datos compartida para todos los métodos de login.

---

## 13. ¿Cuál es el beneficio principal de aplicar el Principio de Segregación de Interfaces (ISP)?

- A) Permite que una clase haga muchas tareas de forma simultánea.
- B) Evita que los cambios en métodos irrelevantes afecten a clases que no los usan.
- C) Asegura que todas las clases tengan al menos una interfaz.
- D) Reduce drásticamente el número de clases en el proyecto.

---

## 14. En la frase 'Abstracciones no deben depender de detalles. Detalles deben depender de abstracciones', ¿a qué principio se refiere?

- A) Principio de Inversión de Dependencia
- B) Principio de Responsabilidad Única
- C) Principio Abierto/Cerrado
- D) Principio de Sustitución de Liskov

---

## 15. Si una función espera un objeto de tipo 'Figura' para calcular su área, y al pasarle un objeto 'Triangulo' (que hereda de Figura) el cálculo falla por una validación interna inesperada, se viola:

- A) Principio de Segregación de Interfaces
- B) Principio de Responsabilidad Única
- C) Principio de Inversión de Dependencia
- D) Principio de Sustitución de Liskov

---

## 16. Imagina que tienes una clase 'ManejadorDeArchivos' que abre conexiones a red y también parsea archivos JSON. ¿Cuál es el problema principal de diseño?

- A) Es imposible usar el polimorfismo con esta clase.
- B) Viola el ISP al no tener una interfaz de red separada.
- C) Tiene baja cohesión al mezclar infraestructura de red con lógica de datos.
- D) No permite que otros archivos sean leídos (viola OCP).

---

## 17. Un sistema de reproducción de música tiene una interfaz 'IReproductor' con 'play()', 'stop()' y 'record()'. Unos auriculares Bluetooth solo pueden reproducir y parar. ¿Qué principio sugiere separar 'record()'?

- A) Principio de Segregación de Interfaces
- B) Principio de Responsabilidad Única
- C) Principio de Inversión de Dependencia
- D) Principio de Sustitución de Liskov

---

## 18. ¿Cómo ayuda el DIP a facilitar las pruebas unitarias (unit testing)?

- A) Obliga a que todas las funciones devuelvan un valor booleano.
- B) Garantiza que el código se ejecute más rápido durante los tests.
- C) Elimina la necesidad de escribir aserciones en los tests.
- D) Permite sustituir implementaciones reales (como bases de datos) por objetos simulados (mocks).

---

## 19. Si una clase 'A' usa la clase 'B' internamente, pero queremos que 'A' sea agnóstica a los cambios de 'B', ¿cuál es la técnica recomendada?

- A) Hacer que 'B' sea una clase estática accesible desde cualquier lugar.
- B) Copiar los métodos necesarios de 'B' dentro de la clase 'A'.
- C) Hacer que 'A' herede todas las propiedades de 'B'.
- D) Inyección de dependencias mediante una interfaz que 'B' implemente.

---

## 20. En un sistema de envío, 'ProcesadorEnvios' tiene un método para cada empresa: 'enviarPorFedex()', 'enviarPorUPS()'. ¿Qué violación de OCP es más evidente?

- A) La clase tiene demasiados métodos para una sola responsabilidad.
- B) Las empresas de transporte no comparten la misma base de datos.
- C) La clase debe modificarse internamente para añadir una nueva empresa de transportes.
- D) No se pueden realizar envíos internacionales con este esquema.

---

# Respuestas

1. B) Principio de inversión de dependencias
2. D) Tener múltiples razones para cambiar el código
3. A) Creando una interfaz 'IEstrategiaImpuesto' y una implementación por cada país
4. C) Principio de Sustitución de Liskov
5. B) Dividir 'IProcesadorArchivos' en interfaces más pequeñas como 'ILector' y 'ICompresor'
6. C) Hacer que 'Guerrero' dependa de una interfaz 'IArma'
7. D) Deberíamos crear una nueva clase que implemente una interfaz de exportación sin tocar las existentes
8. B) Principio de Responsabilidad Única
9. C) Principio de Sustitución de Liskov
10. D) Que no se debe alterar su código fuente una vez que ha sido probado y estabilizado
11. D) Pasar una interfaz 'IServicioEmail' al 'Pedido' mediante el constructor
12. A) Implementar una nueva clase que cumpla con la interfaz 'IAutenticador'
13. B) Evita que los cambios en métodos irrelevantes afecten a clases que no los usan
14. A) Principio de Inversión de Dependencia
15. D) Principio de Sustitución de Liskov
16. C) Tiene baja cohesión al mezclar infraestructura de red con lógica de datos
17. A) Principio de Segregación de Interfaces
18. D) Permite sustituir implementaciones reales (como bases de datos) por objetos simulados (mocks)
19. D) Inyección de dependencias mediante una interfaz que 'B' implemente
20. C) La clase debe modificarse internamente para añadir una nueva empresa de transportes

---

# Material de apoyo — Ejemplo DIP (Pregunta 1)

```java
// ❌ Violación: ControladorLuces depende de una clase concreta
class BombillaPhilips {
    public void encender() {
        System.out.println("Bombilla Philips encendida");
    }
}

class ControladorLuces {
    private BombillaPhilips bombilla;

    public ControladorLuces() {
        bombilla = new BombillaPhilips(); // Dependencia directa
    }

    public void encenderLuz() {
        bombilla.encender();
    }
}

// ✅ Solución: abstracción + inyección de dependencias
interface Bombilla {
    void encender();
}

class BombillaPhilips implements Bombilla {
    @Override
    public void encender() {
        System.out.println("Bombilla Philips encendida");
    }
}

class BombillaXiaomi implements Bombilla {
    @Override
    public void encender() {
        System.out.println("Bombilla Xiaomi encendida");
    }
}

class ControladorLuces {
    private Bombilla bombilla;

    public ControladorLuces(Bombilla bombilla) { // Inyección de dependencia
        this.bombilla = bombilla;
    }

    public void encenderLuz() {
        bombilla.encender();
    }
}
```
