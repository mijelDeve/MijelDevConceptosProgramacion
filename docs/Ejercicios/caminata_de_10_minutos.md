# Caminata de 10 minutos

Vives en la ciudad de Cartesia, donde todas las calles están organizadas en una cuadrícula perfecta. Llegaste diez minutos antes a una cita, así que decidiste aprovechar la oportunidad para dar un pequeño paseo.

La ciudad proporciona a sus ciudadanos una App Generadora de Caminatas en sus teléfonos: cada vez que presionas el botón, la app envía un arreglo de cadenas de un solo carácter que representan direcciones para caminar (por ejemplo: `['n', 's', 'w', 'e']`).

Siempre caminas exactamente una cuadra por cada letra (dirección) y sabes que te toma un minuto recorrer una cuadra. Por lo tanto, crea una función que devuelva `true` si la caminata que proporciona la app:

- dura exactamente diez minutos (¡no quieres llegar ni temprano ni tarde!), y
- además te devuelve al punto de partida.

En cualquier otro caso, debe devolver `false`.

**Nota:** siempre recibirás un arreglo válido que contiene una combinación aleatoria de letras de dirección (`'n'`, `'s'`, `'e'` o `'w'` únicamente). Nunca recibirás un arreglo vacío (eso no es una caminata, ¡es quedarse quieto!).

## Ejemplos

```text
isValidWalk(['n','s','n','s','n','s','n','s','n','s']) => true  (10 min, mismo punto)
isValidWalk(['w','e','w','e','w','e','w','e','w','e','w','e']) => false  (12 min)
isValidWalk(['w']) => false  (1 min)
isValidWalk(['n','n','n','s','n','s','n','s','n','s']) => false  (no vuelve al inicio)
```

# Solución

```typescript
export function isValidWalk(walk: string[]) {
  if (walk.length !== 10) return false;

  let x = 0;
  let y = 0;

  for (let dir of walk) {
    if (dir == 'n') y++;
    if (dir == 's') y--;
    if (dir == 'e') x++;
    if (dir == 'w') x--;
  }

  return x === 0 && y === 0;
}
```
