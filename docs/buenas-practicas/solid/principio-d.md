
Este principio establece que los módulos de alto nivel no deben depender de los de bajo nivel, sino que ambos deben depender de abstracciones (como interfaces), asegurando que los detalles técnicos dependan de conceptos generales y no al revés.

El último componente del acrónimo SOLID es el Principio de Inversión de Dependencia (Dependency Inversion Principle - DIP). Este principio es fundamental para desacoplar el código y permitir que sea flexible frente a cambios.

**1. Concepto Fundamental**

El DIP establece dos reglas principales:

- Los módulos de alto nivel (la lógica principal o "partes grandes" del programa) no deben depender de los módulos de bajo nivel (detalles técnicos o tareas específicas). Ambos deben depender de abstracciones.
- Las abstracciones no deben depender de los detalles; son los detalles los que deben depender de las abstracciones.

En términos sencillos: tus clases deben depender de interfaces o clases abstractas en lugar de depender directamente de clases concretas o funciones específicas.

**2. Relación con el Principio Abierto-Cerrado (OCP)**

Según Robert J. Martin (el "Tío Bob"), si el principio OCP establece el objetivo de la arquitectura, el DIP establece el mecanismo principal para lograrlo. Al depender de interfaces, puedes extender el comportamiento del sistema (abierto a la extensión) sin modificar las clases de alto nivel que ya funcionan (cerrado a la modificación).

**3. Ejemplos Prácticos de las Fuentes**

El Administrador de Persistencia: En lugar de que una clase AdministradorPersistencia dependa directamente de una clase concreta como MySQLDatabase, esta debe depender de una interfaz general como FacturaPersistencia. De esta forma, el administrador puede trabajar con cualquier base de datos (MySQL, MongoDB, etc.) siempre que estas sigan las reglas de la interfaz.
El Equipo de Desarrollo (Ejemplo de la Vida Real): Imagina un equipo de desarrolladores que utiliza un sistema de control de versiones (como Git).

- Abstracción: La interfaz IVersionControl define operaciones básicas como hacer commit, push y pull.
- Implementación (Bajo nivel): La clase GitVersionControl maneja los detalles internos de cómo Git hace esas tareas
- Equipo (Alto nivel): La clase DevelopmentTeam depende de la interfaz general. Esto permite que el equipo trabaje con cualquier sistema de control de versiones sin que los desarrolladores tengan que conocer o depender de los detalles internos técnicos de la herramienta

**4. Beneficios Principales**

- Desacoplamiento (Loose Coupling): Las clases son menos dependientes entre sí, lo que minimiza el impacto de los cambios.
- Facilidad de Pruebas: Permite sustituir implementaciones reales por objetos simulados (mocks) durante las pruebas de software.
- Flexibilidad: Facilita el cambio de componentes (como cambiar un proveedor de servicios o una base de datos) sin romper el resto del sistema

# Ejemplo sin DIP

```typescript
// Módulo de bajo nivel - EmailService concreto
class EmailService {
	sendEmail(to: string, subject: string, body: string): void {
		console.log('Enviando email')	
		// Lógica específica de enǘio de email
	}
}

// Modulo de alto nivel - Depende directamente de de EmailService (violación)
class NotificationService {
	private emailService: EmailService;
	
	constructor(){
		this.emailService = new EmailService();	
	}
	
	sendAlert(message: string): void{
		this.emailService.sendEmail("mijel.dev@gmail.com", "Alerta", message)	
	}
}


// Problema: ¿Qué pasaría si queremos cambiar a SMS
// Tendríamos quemodificar EmailService
```

```typescript
// Otro ejemplo común - Dependencia directa de implementaciones concretas
class MySQLDatabase {
	connect(): void {
		console.log("Conectando a mysql...")	
	}
	
	query(sql: string): any[] {
		console.log("Ejecutando script sql")
		return []
	}
}


class UserRepository {
	private database: MySQLDatabase;
	
	constructor() {
		this.database = new MySQLDatabase();	
	}
	
	getUsers(): any[] {
		this.database.connect();
		return this.database.query("Select * from users")	
	}
}

// Cambiar a PostgressSQL requería modificar UserRepository
```

# Ejemplo con DIP

```typescript
// 1. Definir abastracciones (interfaces) que ambos niveles usarán
interface MessageSender {
	sendMessage(to: string, content: string): void;
}

// 2. Implementaciones concretas a bajo nivel

class EmailService implements MessageSender() {
	sendMessage(to: string, content: string): void {
		console.log("Enviando mensaje)
	}	
}

class SMSService implements MessageSender() {
	sendMessage(to: string, content: string): void {
		console.log("Enviando mensaje)	
	}
}

class WhatsAppService implements MessageSender {
  sendMessage(to: string, content: string): void {
    console.log(`💬 WhatsApp a ${to}: ${content}`);
    // Lógica real de WhatsApp
  }
}

class PushNotificationService implements MessageSender {
  sendMessage(to: string, content: string): void {
    console.log(`🔔 Push notification a ${to}: ${content}`);
    // Lógica real de push
  }
}

// 3. Módulo de alto nivel depende de la abrasracción, no de la implementacioń
class NotificationService {
	constructor(private messageSender: MessageSender) {
	}
	
	sendAlert(userId: string, message: string): void {
	    this.messageSender.sendMessage(userId, `🔴 ALERTA: ${message}`);
	}
	
	sendWelcome(userId: string): void {
		this.messageSender.sendMessage(userId, "¡Bienvenido a nuestro sistema! 🎉");
	}
}

// 4. Configuración en tiempo de ejecución (Composición Root)
class Aplication {
	private notificationService: NotificationService;
	
	constructor() {
		const sender: MessageSender = this.getEnvironmentSpecificSender();
		this.notificationService = new NotificationService(sender);
	}
	
	private getEnvironmentSpecificSender(): MessageSender {
		const env = process.env.NOTIFICATION_CHANEL || "email";
		
		switch (env) {
			case "sms": return new SMSService();
		    case "whatsapp": return new WhatsAppService();
		    case "push": return new PushNotificationService();
		    default: return new EmailService();	
		}
	}
	
	run(): void {
	    this.notificationService.sendWelcome("user123");
	    this.notificationService.sendAlert("admin", "Sistema funcionando correctamente");
	}
}

```


# Ejemplo más completo: Sistema de persistencia

```typescript
// Abstracciones
interface DatabaseConnection {
  connect(): Promise<void>;
  disconnect(): Promise<void>;
}

interface Repository<T> {
  findById(id: string): Promise<T | null>;
  save(entity: T): Promise<void>;
  delete(id: string): Promise<void>;
}

// Implementaciones concretas de bajo nivel
class PostgreSQLConnection implements DatabaseConnection {
  async connect(): Promise<void> {
    console.log("🔵 Conectando a PostgreSQL...");
    // Lógica real de conexión
  }
  
  async disconnect(): Promise<void> {
    console.log("🔵 Desconectando de PostgreSQL...");
  }
}

class MongoDBConnection implements DatabaseConnection {
  async connect(): Promise<void> {
    console.log("🟢 Conectando a MongoDB...");
  }
  
  async disconnect(): Promise<void> {
    console.log("🟢 Desconectando de MongoDB...");
  }
}

// Repositorio concreto que depende de abstracciones
class UserRepository implements Repository<User> {
  constructor(
    private dbConnection: DatabaseConnection,
    private serializer: DataSerializer<User>
  ) {}
  
  async findById(id: string): Promise<User | null> {
    await this.dbConnection.connect();
    // Lógica de búsqueda usando la conexión
    console.log(`Buscando usuario ${id}`);
    return null;
  }
  
  async save(user: User): Promise<void> {
    await this.dbConnection.connect();
    const data = this.serializer.serialize(user);
    console.log(`Guardando usuario: ${data}`);
  }
  
  async delete(id: string): Promise<void> {
    await this.dbConnection.connect();
    console.log(`Eliminando usuario ${id}`);
  }
}

// Otra abstracción útil
interface DataSerializer<T> {
  serialize(entity: T): string;
  deserialize(data: string): T;
}

// Implementación específica
interface User {
  id: string;
  name: string;
  email: string;
}

class JSONUserSerializer implements DataSerializer<User> {
  serialize(user: User): string {
    return JSON.stringify(user);
  }
  
  deserialize(data: string): User {
    return JSON.parse(data);
  }
}

// Configuración centralizada (Composition Root)
class ApplicationContainer {
  private dbConnection: DatabaseConnection;
  private userSerializer: DataSerializer<User>;
  private userRepository: UserRepository;
  
  constructor() {
    // Configuramos las dependencias en un solo lugar
    this.dbConnection = new PostgreSQLConnection();
    this.userSerializer = new JSONUserSerializer();
    this.userRepository = new UserRepository(this.dbConnection, this.userSerializer);
  }
  
  getUserRepository(): UserRepository {
    return this.userRepository;
  }
  
  async shutdown(): Promise<void> {
    await this.dbConnection.disconnect();
  }
}

// Uso
async function main() {
  const container = new ApplicationContainer();
  const userRepo = container.getUserRepository();
  
  await userRepo.save({
    id: "1",
    name: "Juan Pérez",
    email: "juan@email.com"
  });
  
  await container.shutdown();
}
```
