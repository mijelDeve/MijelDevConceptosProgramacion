El **Principio de Responsabilidad Única (SRP)** establece que una clase debe tener **una sola tarea, trabajo o propósito** dentro de un sistema de software.

En términos más simples, esto significa que una clase debe tener **una única razón para cambiar**. Si una clase realiza múltiples funciones (como procesar datos y también imprimirlos), se considera una violación de este principio.

**1. Introducción y Contexto Histórico**

Los principios **SOLID** representan las directrices fundamentales para el diseño de arquitectura orientada a objetos. Este marco conceptual fue introducido inicialmente por el científico informático **Robert J. Martin** (conocido como **Uncle Bob**) en su artículo del año 2000, aunque el acrónimo que los popularizó fue acuñado posteriormente por **Michael Feathers**.

Como **Arquitecto de Software**, es imperativo comprender que el propósito de estos principios trasciende la mera organización; su objetivo es mitigar la rigidez del software para crear código **comprensible, legible y comprobable**. Un beneficio arquitectónico central de su implementación es el fomento del **Acoplamiento Débil** (Loose Coupling), un mecanismo que reduce la interdependencia entre módulos, elevando drásticamente la **estabilidad** y la **reutilización** de los componentes en entornos de desarrollo colaborativo.

**2. Definición Técnica del Principio de Responsabilidad Única (SRP)**

El **Principio de Responsabilidad Única** (SRP) dicta que **"una clase debe hacer una sola cosa y, por lo tanto, debe tener una única razón para cambiar"**.

Desde una perspectiva técnica, esto implica que solo una modificación en la especificación del software (como un cambio en los requisitos de la **persistencia de datos** o en la **lógica de registro**) debería tener el poder de afectar la definición de la clase. La violación de este principio reduce la **cohesión** del sistema y genera **dependencias innecesarias**. Para aplicar este estándar, debemos distinguir claramente entre:

- **Contenedor de Datos:** Entidades como **Libro** o **Estudiante**, cuyo único propósito es definir el modelo de datos y sus campos. Su única razón para cambiar es una modificación en la estructura del modelo.
- **Lógica de Dominio y Procesamiento:** Clases especializadas en la manipulación de información, cálculos complejos o gestión de estados, las cuales deben residir fuera de los contenedores de datos para evitar el acoplamiento.

**3. Impacto en el Mantenimiento y Control de Versiones**

La adopción del **SRP** es un requisito para garantizar la integridad del código cuando múltiples equipos intervienen en un mismo repositorio. Al fragmentar las responsabilidades, logramos un **aislamiento de fallos** que impide que un error en una funcionalidad periférica comprometa el núcleo del sistema.

|Aspecto|Impacto del SRP en la Gestión de Proyectos|
|---|---|
|**Mantenibilidad**|El **Acoplamiento Débil** permite que los cambios sean granulares. Al aislar módulos, un error introducido en la **Capa de Persistencia** no corrompe ni requiere modificaciones en la **Lógica de Dominio**, garantizando un mantenimiento seguro.|
|**Control de Versiones**|Optimiza la trazabilidad en herramientas como **GitHub**. Al tener archivos con una sola responsabilidad, los _commits_ reflejan cambios precisos. Esto minimiza los **conflictos de fusión** (merge conflicts) y, en caso de ocurrir, facilita una resolución rápida al limitar el alcance del conflicto a una única funcionalidad.|

**4. Analogía del Mundo Real: El Caso del Panadero**

Para ilustrar la importancia de la especialización, consideremos la analogía de un panadero. En un diseño deficiente, el panadero asume todas las cargas operativas, lo que degrada la calidad del producto final. Las responsabilidades que entran en conflicto son:

- Horneado de pan.
- Gestión de inventario.
- Gestión de pedidos de suministros.
- Servicio al cliente.
- Limpieza del local.

De acuerdo con los estándares del **SRP**, este sistema debe refactorizarse en roles o clases independientes para asegurar el enfoque y la efectividad:

- **BreadBaker**: Responsable exclusivo de la producción y calidad del pan.
- **InventoryManager**: Encargado de gestionar las existencias de ingredientes.
- **SupplyOrder**: Clase dedicada a la gestión y logística de pedidos con proveedores.
- **CustomerService**: Punto de contacto centrado en la interacción y ventas.
- **BakeryCleaner**: Encargado de mantener los estándares higiénicos del entorno.

**5. Aplicación Técnica: Refactorización de un Sistema de Facturación**

Un anti-patrón recurrente en sistemas legados es la "Clase Todopoderosa". Consideremos una clase **Factura** que integra los métodos `calculaTotal`, `imprimeFactura` y `guardaArchivo`. Este diseño viola el **SRP** al mezclar **lógica de dominio**, **presentación** y **persistencia** en una sola unidad de despliegue.

El proceso de refactorización arquitectónica sigue estos pasos:

1. **Identificación de Cohesión Débil:** Se aíslan los métodos que no pertenecen al dominio del cálculo, como `imprimeFactura` (formateo de salida) y `guardaArchivo` (almacenamiento).
2. **Delegación a Clases Satélites:** Se crean los componentes **FacturaImpresion** y **FacturaPersistencia**.

Este desacoplamiento asegura que una migración de base de datos o un cambio en el diseño de los reportes no requiera recompilar ni testear nuevamente la lógica crítica de cálculo de la **Factura**.

**6. Mejora de la Calidad mediante la Separación de Capas**

La aplicación del **SRP** facilita una arquitectura multicapa, esencial para la robustez del software en contextos de gestión de librerías y facturación:

- **Capa de Negocio (Dominio):** Contiene la lógica esencial, como el método `calculaTotal` para los libros de la factura. Su enfoque es puramente algorítmico y permanece ajeno a cómo se visualizan o almacenan los datos.
- **Capa de Persistencia:** Responsable de la interacción con el almacenamiento, como el método `guardaArchivo`. Permite que la aplicación sea flexible ante cambios tecnológicos, permitiendo migrar de archivos planos a bases de datos o **APIs** externas sin alterar el negocio.
- **Capa de Presentación:** Gestiona la interfaz de salida, ejecutando tareas como `imprimeFactura`. Su rol es transformar la información del **Libro** y la factura en formatos visuales (consola, PDF o web) sin interferir con las reglas de cálculo.

**7. Conclusión y Recomendaciones de Implementación**

El **Principio de Responsabilidad Única** no es una sugerencia estética, sino un pilar para la **escalabilidad** y la **estabilidad** de cualquier sistema profesional. Su implementación garantiza un código **limpio, extensible y comprobable**, facilitando la evolución del software sin el miedo a efectos secundarios imprevistos.

Como recomendación de arquitectura, el **SRP** debe ser el filtro principal durante el diseño, la codificación y la refactorización. Identificar y fragmentar las "**clases gordas**" o interfaces generalistas protege al sistema de **dependencias innecesarias**, permitiendo que cada componente evolucione a su propio ritmo y cumpla su propósito con excelencia técnica.



# Ejemplo incorrecto 

```typescript
class UserManager {
  private user: { name: string; email: string }[] = [];

  addUser(name: string, email: string): void{
    this.user.push({ name, email});
  }

  validateEmail(email: string): boolean {
    return email.includes('@') && email.includes('.');
  }

  saveToDataBase(): void {
    console.log('Guardando en la base de datos')
  }

  sendWelcomeEmail(email: string): void {
    console.log(`Enviando email a ${email}`)
  }
}
```

# Ejemplo aplicando el Principio SRP

```typescript
class UserRepository {
  private user: { name: string; email: string }[] = [];

  addUser(name: string, email: string): void{
    this.user.push({ name, email});
  }

  getAll(): { name: string; email: string} []{
    return [...this.user]
  }
}


class EmailValidator {
  isValid(): boolean {
    return email.includes('@') && email.includes('.');
  }
}

class DatabaseService {
  save(users: { name: string; email: string}[]) {
    console.log('Guardando usuarios')
  }
}

class EmailService {
  sendWelcome(email: string): void {
    console.log(`Enviando email de bienvenida a ${email}`);
  }
}

class UserRegistrationService {
  constructor(
    private userRepo: UserRepository,
    private emailValidator: EmailValidator,
    private dbService: DatabaseService,
    private emailService: EmailService,
  ) {}


  registerUser(name: string, email: string): boolean {
    if (!this.emailValidator.isValid(email)) {
      console.log('Email inválido');
      return false;
    }

    this.userRepo.add({ name, email });
    this.dbService.save(this.userRepo.getAll());
    this.emailService.sendWelcome(email);
    return true;
  }
}

const userRepo = new UserRepository();
const validator = new EmailValidator();
const db = new DatabaseService();
const emailService = new EmailService();

const registration = new UserRegistrationService(
  userRepo, validator, db, emailService
);

registration.registerUser('Pepe', 'pepe@gmail.com');
```
