# Cuestionario

## 1. ¿En un sistema de domótica, la clase 'ControladorLuces' instancia directamente un objeto 'BombillaPhilips' en su constructor para encender la luz. ¿Qué principio SOLID se está violando principalmente??

- A) Principio de Responsabilidad única
- B) Principio de inversión de dependencias
- C) Principio de segregación de interfaces
- D) Principio de sustitución de Liskov

---


# Respuestas

1. B) Principio de inversión de dependencias

```typescript

// Por qué
// Imagina este ejemplo
class BombillaPhilips {

    public void encender() {
        System.out.println("Bombilla Philips encendida");
    }
}

class ControladorLuces {

    private BombillaPhilips bombilla;

    public ControladorLuces() {
        // Dependencia directa
        bombilla = new BombillaPhilips();
    }

    public void encenderLuz() {
        bombilla.encender();
    }
}

public class Main {
    public static void main(String[] args) {

        ControladorLuces controlador = new ControladorLuces();
        controlador.encenderLuz();
    }
}

// Ahora imagina que quieres cambiar a una bombilla xiaomi
// Esto lo solucionamos creando una abastracción (interfaz)

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

    // Inyección de dependencia
    public ControladorLuces(Bombilla bombilla) {
        this.bombilla = bombilla;
    }

    public void encenderLuz() {
        bombilla.encender();
    }
}

public class Main {

    public static void main(String[] args) {

        Bombilla bombilla = new BombillaPhilips();

        ControladorLuces controlador = new ControladorLuces(bombilla);

        controlador.encenderLuz();
    }
}
```

