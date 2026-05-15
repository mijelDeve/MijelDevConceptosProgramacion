**Es preferible tener muchas interfaces pequeñas y específicas para cada cliente que una sola interfaz general, evitando así obligar a las clases a implementar métodos que no necesitan.**

"No obligues a nadie a depender de cosas que no necesita usar".

En términos muy sencillos:
- Divide y vencerás: Es mucho mejor tener muchas interfaces pequeñas y específicas que una sola interfaz "gorda" o gigante que intente hacerlo todo.
- Evita lo innecesario: Una clase no debería verse obligada a implementar funciones o métodos que no le sirven para nada solo porque están en una interfaz general


**1. Concepto Fundamental**

El principio establece que muchas interfaces específicas para cada cliente son mejores que una sola interfaz de propósito general. En esencia, no se debe obligar a los clientes a depender de métodos que no utilizan. El objetivo principal es evitar las llamadas "interfaces gordas" o voluminosas, dividiéndolas en interfaces más pequeñas y específicas que tengan una responsabilidad clara.

**2. ¿Por qué es importante?**

- Evita implementaciones irrelevantes: Cuando una interfaz es muy grande, las clases que la implementan a menudo se ven obligadas a escribir código para funciones que no necesitan.
- Reduce el acoplamiento: Al tener interfaces pequeñas, los cambios en una parte del sistema no afectan a los clientes que solo utilizan otras partes
- Facilita el mantenimiento: El código se vuelve más limpio y fácil de entender al proporcionar solo la funcionalidad que el cliente realmente requiere

**3. Ejemplos Prácticos de las Fuentes**

El Estacionamiento (Ejemplo Técnico):

Imagina una interfaz de Estacionamiento que incluye lógica para aparcar, sacar el coche y también para procesar pagos. Si queremos crear un EstacionamientoGratis, esta clase se vería obligada a implementar los métodos de pago, aunque sean irrelevantes para ella.

**La solución**: Segregar la interfaz original en dos: una para la lógica de estacionar y otra para la lógica de pago. Así, el estacionamiento gratuito solo implementa lo que necesita.

El Menú del Restaurante (Ejemplo de la Vida Real):

Si un cliente vegetariano entra a un restaurante y recibe un menú único que mezcla platos de carne, bebidas y postres, está recibiendo información que no necesita.
La solución: Dividir el menú común en menús específicos: IVegetarianMenu, INonVegetarianMenu e IDrinkMenu.

De esta forma, cada cliente (o clase) interactúa solo con la parte del "menú" que le corresponde, eliminando dependencias innecesarias.

**4. Conclusión del Principio**

Aplicar el ISP hace que el sistema sea mucho más flexible y extensible, ya que los clientes no necesitan cargar con lógica irrelevante, permitiendo que cada pieza del software sea lo más ligera y específica posible.


# Sin aplicar ISP

```typescript
// Interfaz demasiado amplia
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
  attendMeeting(): void;
  submitReport(): void;
}

// Robot solo puede trabajar, pero obligado a implementar todo
class Robot implements Worker {
  work(): void {
    console.log("Robot trabajando...");
  }
  
  eat(): void {
    // Los robots no comen - método forzado e inútil
    throw new Error("Los robots no comen");
  }
  
  sleep(): void {
    // Los robots no duermen
    throw new Error("Los robots no duermen");
  }
  
  attendMeeting(): void {
    // Los robots no asisten a reuniones
    throw new Error("Los robots no atienden reuniones");
  }
  
  submitReport(): void {
    console.log("Robot generando reporte automático");
  }
}

// Humano necesita todo, pero igual sufre con la interfaz grande
class Human implements Worker {
  work(): void {
    console.log("Humano trabajando");
  }
  
  eat(): void {
    console.log("Humano comiendo");
  }
  
  sleep(): void {
    console.log("Humano durmiendo");
  }
  
  attendMeeting(): void {
    console.log("Humano en reunión");
  }
  
  submitReport(): void {
    console.log("Humano entregando reporte");
  }
}
```


# Aplicando ISP

```typescript
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

interface MeetingAttendable {
  attendMeeting(): void;
}

interface Reportable {
  submitReport(): void;
}

// Robot que solo implementa lo necesario
class Robot implements Workable, Reportable {
  
  work(): void {
    console.log("Trabajando como robot")
  }

  submitReport(): void {
    console.log("Enviando reporte pero sigo siendo un robot uu...")
  }
}

class Human implements Workable, Eatable, Sleepable, MeetingAttendable, Reportable {
  work(): void {
    console.log("Humano trabajando");
  }
  
  eat(): void {
    console.log("Humano comiendo");
  }
  
  sleep(): void {
    console.log("Humano durmiendo");
  }
  
  attendMeeting(): void {
    console.log("Humano en reunión");
  }
  
  submitReport(): void {
    console.log("Humano entregando reporte");
  }
}

class Freelancer implements Workable, Reportable, Eatable {
  work(): void {
    console.log("Freelancer trabajando desde casa");
  }
  
  submitReport(): void {
    console.log("Freelancer enviando factura");
  }
  
  eat(): void {
    console.log("Freelancer almuerzo rápido");
  }
}

function performWork(worker: Workable): void {
  worker.work();
}

function takeLunchBreak(eater: Eatable): void {
  eater.eat();
}

function conductMeeting(attendee: MeetingAttendable): void {
  attendee.attendMeeting();
}

// Uso - sin riesgos
const robot = new Robot();
const human = new Human();
const freelancer = new Freelancer();

performWork(robot);     // ✅ Robot trabajando
performWork(human);     // ✅ Humano trabajando
performWork(freelancer); // ✅ Freelancer trabajando

takeLunchBreak(human);     // ✅ Humano comiendo
takeLunchBreak(freelancer); // ✅ Freelancer almuerzo rápido
// takeLunchBreak(robot);   // ❌ Error de compilación - Robot no es Eatable

conductMeeting(human);     // ✅ Humano en reunión
// conductMeeting(robot);  // ❌ Error de compilación
```

# Ejemplo más real: Sistema de notificaciones 

## Sin aplicar ISP

```typescript
// Interfaz violando el ISP
interface NotificacionService {
  sendEmail(to: string, subject: string, body: string): void;
  sendSMS(phoneNumber: string, message: string): void;
  sendPushNotification(deviceToken: string, title: string, body: string): void;
  sendWhastssApp(phoneNumber: string, message: string): void;
  getNotificationHistory(userId: string): any[];
  markAsRead(notificationId: string): void;
}

class EmailOnlyService implements NotificacionService {
  sendEmail(to: string, subject: string, body: string): void {
    console.log(`Email a ${to}: ${subject}`);
  }
  
  sendSMS(phoneNumber: string, message: string): void {
    throw new Error("Este servicio solo envía emails");
  }
  
  sendPushNotification(deviceToken: string, title: string, body: string): void {
    throw new Error("Este servicio solo envía emails");
  }
  
  sendWhatsApp(phoneNumber: string, message: string): void {
    throw new Error("Este servicio solo envía emails");
  }
  
  getNotificationHistory(userId: string): any[] {
    throw new Error("No implementado");
  }
  
  markAsRead(notificationId: string): void {
    throw new Error("No implementado");
  }
}
```

## Aplicando ISP

```typescript
// ✅ Aplicando ISP - interfaces segregadas

// Interfaces específicas
interface EmailSender {
  sendEmail(to: string, subject: string, body: string): void;
}

interface SMSSender {
  sendSMS(phoneNumber: string, message: string): void;
}

interface PushNotifier {
  sendPushNotification(deviceToken: string, title: string, body: string): void;
}

interface WhatsAppSender {
  sendWhatsApp(phoneNumber: string, message: string): void;
}

interface NotificationHistory {
  getHistory(userId: string): NotificationRecord[];
  markAsRead(notificationId: string): void;
}

type NotificationRecord = {
  id: string;
  userId: string;
  message: string;
  read: boolean;
};

// Implementación solo para emails
class EmailService implements EmailSender {
  sendEmail(to: string, subject: string, body: string): void {
    console.log(`📧 Enviando email a ${to}: ${subject}`);
    // Lógica real de email
  }
}

// Implementación multi-canal (puede implementar varias interfaces)
class MultiChannelNotificationService implements EmailSender, SMSSender, PushNotifier {
  sendEmail(to: string, subject: string, body: string): void {
    console.log(`📧 Email a ${to}`);
  }
  
  sendSMS(phoneNumber: string, message: string): void {
    console.log(`📱 SMS a ${phoneNumber}: ${message}`);
  }
  
  sendPushNotification(deviceToken: string, title: string, body: string): void {
    console.log(`🔔 Push a ${deviceToken}: ${title}`);
  }
}

// Cliente solo depende de lo que necesita
class NewsletterManager {
  constructor(private emailSender: EmailSender) {}
  
  sendNewsletter(to: string, content: string): void {
    this.emailSender.sendEmail(to, "Newsletter semanal", content);
  }
}

class AlertSystem {
  constructor(
    private smsSender: SMSSender,
    private pushNotifier: PushNotifier
  ) {}
  
  sendCriticalAlert(phone: string, deviceToken: string, message: string): void {
    this.smsSender.sendSMS(phone, message);
    this.pushNotifier.sendPushNotification(deviceToken, "ALERTA", message);
  }
}

// Uso flexible
const emailService = new EmailService();
const multiChannel = new MultiChannelNotificationService();

const newsletter = new NewsletterManager(emailService);
newsletter.sendNewsletter("cliente@email.com", "Ofertas especiales");

const alerts = new AlertSystem(multiChannel, multiChannel);
alerts.sendCriticalAlert("+123456789", "device-token-123", "Sistema caído!");
```
