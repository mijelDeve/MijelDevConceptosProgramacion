# MVC (Model-View-Controller)

## Propósito

Separar la aplicación en tres componentes interconectados: Modelo (datos), Vista (interfaz) y Controlador (lógica de control).

## Componentes

| Componente | Responsabilidad |
|---|---|
| **Modelo** | Gestiona los datos, la lógica de negocio y las reglas de validación |
| **Vista** | Presenta los datos al usuario en un formato determinado |
| **Controlador** | Recibe entrada del usuario, actualiza el modelo y selecciona la vista |

## Cuándo Usarlo

- Aplicaciones web tradicionales con formularios y vistas.
- Frameworks como Laravel, Rails o Spring MVC lo implementan por defecto.
- Cuando necesitas una separación limpia entre datos, UI y lógica de control.

## Ejemplo (Laravel)

```php
// Controlador
class UserController extends Controller {
    public function index() {
        $users = User::all();           // Modelo
        return view('users.index', [    // Vista
            'users' => $users
        ]);
    }
}
```
