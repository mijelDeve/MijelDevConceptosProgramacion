El Principio de Sustitución de Liskov (LSP) se puede definir así de simple:
Una clase hija debe poder usarse en lugar de su clase padre sin que el programa falle o se comporte de forma extraña

En términos muy sencillos:
- Sustitución total: Si tienes una clase "Padre" y una clase "Hija", deberías poder intercambiarlas en cualquier parte de tu código y todo debería seguir funcionando perfectamente
- No romper promesas: La clase hija puede añadir funciones nuevas, pero nunca debe quitar o cambiar el comportamiento básico que el padre ya prometía

El ejemplo más claro: Si un método de tu programa espera un Rectángulo para calcular su área, pero tú le envías un Cuadrado y los resultados son incorrectos (porque al cambiar el ancho del cuadrado también se cambia el alto sin que el programa lo espere), estás violando este principio. La hija (Cuadrado) no puede sustituir al padre (Rectángulo) de forma segura

El Principio de Sustitución de Liskov (LSP), introducido por Barbara Liskov en 1987, es el tercer principio del acrónimo SOLID y se centra en el uso correcto de la herencia en la programación orientada a objetos

A continuación, se presenta un resumen detallado de este principio basándose en las fuentes:

**1. Definición Fundamental**

El principio establece que las subclases deben ser sustituibles por sus clases base. Esto significa que si tienes una clase B que es hija de una clase A, deberías poder usar un objeto de la clase B en cualquier lugar donde se espere uno de la clase A, sin que el programa se comporte de forma inesperada o falle

**2. La Regla de Oro de la Herencia**

Al usar la herencia, se asume que la clase hija hereda todo lo de la superclase. Según el LSP, la clase secundaria puede extender el comportamiento de la superclase, pero nunca debe reducirlo ni romperlo. Si una subclase no puede realizar las mismas funciones básicas que su padre de la manera esperada, está violando este principio

**3. El Problema Clásico: El Rectángulo y el Cuadrado**

Las fuentes ilustran este principio con un ejemplo matemático muy común:
- La Premisa: Geométricamente, un cuadrado es un tipo especial de rectángulo donde el ancho y el alto son iguales. Por ello, se podría pensar en heredar Cuadrado de Rectángulo.
- La Violación: En una clase Rectángulo, se espera poder cambiar el ancho sin que el alto se vea afectado. Sin embargo, en la clase Cuadrado, al intentar mantener los lados iguales, el programador suele sobrescribir los métodos para que, al cambiar el ancho, también cambie el alto automáticamente
- El Resultado: Si un método espera un Rectángulo y recibe un Cuadrado, al modificar solo el ancho obtendrá un área inesperada, rompiendo la lógica del programa que confiaba en el comportamiento base del rectángulo

**4. Importancia en el Desarrollo**

Evita errores difíciles de detectar: Las violaciones del principio de Liskov suelen generar comportamientos "extraños" que no lanzan errores de compilación pero sí errores de lógica muy complejos de encontrar.

Garantiza la consistencia: Asegura que cualquier método que utilice una clase base pueda trabajar de forma segura con cualquier futura subclase que se cree.

En resumen, el LSP nos dice que una subclase no debe simplemente "parecerse" a su padre, sino que debe "comportarse" como él en todos los contextos donde el padre sea requerido

# Sin aplicar LSP

```typescript
// Ejemplo incorrecto

class Bird {
    fly(): string {
        return 'Volando...'
    }

    eat(): string {
        return 'Comiendo...'
    }
}

// Sub clase que respeta el LSP
class Sparrow extends Bird {
    fly(): string {
        return 'Gorrión volando bajo'
    }
}

// Sub clase que rompe con el LSP
class Penguin extends Bird {
    fly(): string {
        throw new Error("Los pinguinos no pueden volar")
    }

    eat(): string {
        return 'Comiendo pescado bajo el agua'
    }
}

function makeBirdFly(bird: Bird): void {
    console.log(bird.fly()) // Esto fallará con penguin
}

// Demostración del problema
const birds : Bird[] = [new Sparrow(), new Penguin()]

birds.forEach(bird => {
    makeBirdFly(bird)
})
```

# Aplicando LSP

```typescript
// Abstracción correcta - no asume que todas las aves vuelan

interface Bird {
	eat(): string;
	
	getMovement(): string;
}

  

// Aves que pueden volar

interface FlyingBird extends Bird {
	fly(): string;
}

  

// Aves que nadan

interface SwimmingBird extends Bird {
	swim(): string;
}

  

// Implementaciones correctas

class Sparrow implements FlyingBird {
	
	eat(): string {
		return 'Gorrion comiendo semillas'
	}
	
	fly(): string {
		return 'Gorrión volando por el aire'
	}
	
	getMovement(): string {
		return this.fly();
	}
}

  

class Penguin implements SwimmingBird {

	eat(): string {
		return 'Pinguino comiendo pescado'
	}

  

	swim(): string {
		return 'Pinguino nadando por el mar'
	}
	
	getMovement(): string {
		return this.swim()
	}

}

  

// Ahora podemos sustituir sin problema

function describeBird(bird: Bird): void {
	console.log(bird.eat())
	console.log(`Se mueve: ${bird.getMovement()}`)
}

function makeBirdFly(bird: FlyingBird): void {
	console.log(bird.fly())
}

  

const sparrow = new Sparrow();

const penguin = new Penguin();

  

describeBird(sparrow); // ✅ Funciona

describeBird(penguin); // ✅ Funciona

  

makeBirdFly(sparrow); // ✅ Solo aves voladoras

// makeBirdFly(penguin); // ❌ Error de compilación - TypeScript lo impide
```


# Ejemplo más común: Rectángulo vs Cuadrado

## Sin aplicar el LSP
```typescript
// Violación clásica de LSP
class Rectangle {
    constructor(protected width: number, protected height: number) {

    }

    setWidth(width: number): void {
        this.width = width;
    }

    setHeight(height: number): void {
        this.height = height;
    }

    getArea(): number {
        return this.width * this.height
    }
}

class Square extends Rectangle {
    constructor(side: number){
        super(side, side)
    }

    setWidth(width: number): void {
        this.width = width;
        this.height = width; // Violación: cambia comportamiento adecuado
    }

    setHeight(height: number): void {
        this.height = height;
        this.width = height; // Violación: cambia comportamiento adecuado
    }
}

//Función que espera un rectangular—around
function resizeRectangle(rect: Rectangle): void {
    rect.setWidth(5);
    rect.setHeight(10);
    console.log(`Área esperada: 50, Área real: ${rect.getArea()}`);
}

const rectangle = new Rectangle(2, 3);
resizeRectangle(rectangle); // Area: 50

const square = new Square(4);
resizeRectangle(square) // Area: 100 (Porque ancho y alto son iguales)
```
## Aplicando LSP

```typescript
interface Shape {
	getArea(): number;
}

  

class Rectangle implements Shape {
	constructor(private width: number, private height: number){}
	
	  
	
	setWidth(width: number): void {
		this.width = width;
	}

	setHeight(height: number): void {
		this.height = height;
	}

	getArea(): number {
		return this.width * this.height;
	}
	
}

  

class Square implements Shape {

	constructor(private side: number){}
	
	setSide(side: number): void {
		this.side = side;
	}
	
	getArea(): number {
		return this.side ** 2;
	}
}

  

// Ahora cada uno tiene su propia interfaz específica

function describeShape(shape: Shape): void {
	console.log(`Área: ${shape.getArea()}`);
}

  

const rectangle = new Rectangle(4, 5);

const square = new Square(4);

  

describeShape(rectangle); // ✅ Área: 20

describeShape(square); // ✅ Área: 16
``` 


# Reglas prácticas para cumplir el LSP
1. No fortalecer las pre condiciones (no exigir más de lo que pedía la clase base)
2. No debilitar las post condiciones (no prometer menos de lo que la clase base debe garantizar)
3. Mantener invariantes (propiedades que siempre deben cumplirse)
4. No lanzar nuevas excepciones que la clase base no lance

```typescript
interface UserRepository {
    findUser(id: string): User | null;
    saveUser(user: User): void;
}

// ✅ Implementación que respeta LSP
class InMemoryUserRepository implements UserRepository {
  private users = new Map<string, User>();
  
  findUser(id: string): User | null {
    // Respeta: puede devolver null si no existe
    return this.users.get(id) || null;
  }
  
  saveUser(user: User): void {
    // Respeta: no lanza excepciones inesperadas
    this.users.set(user.id, user);
  }
}

// ✅ Otra implementación que también respeta LSP
class ApiUserRepository implements UserRepository {
  async findUser(id: string): Promise<User | null> {
    // ¡Cuidado! Cambiar la firma a Promise violaría LSP
    // Mejor mantener la misma firma o usar sobrecargas
    const response = await fetch(`/users/${id}`);
    if (response.status === 404) return null;
    return response.json();
  }
  
  async saveUser(user: User): Promise<void> {
    await fetch(`/users/${user.id}`, { method: 'POST', body: JSON.stringify(user) });
  }
}
```

