# date-fns v4 — Fechas en JavaScript

date-fns es una librería de utilidades para manejar fechas en JavaScript. A diferencia de Moment.js, sus funciones son inmutables, modulares y permiten importar solo lo que necesitas. En la versión 4, la principal novedad es el soporte nativo para zonas horarias.

## Instalación

```sh
npm install date-fns
```

Si vas a trabajar con zonas horarias:

```sh
npm install @date-fns/tz
```

La versión 4 introdujo soporte oficial para timezones mediante `@date-fns/tz`.

## 1. Formatear fechas

### Formato básico

```js
import { format } from 'date-fns';

const date = new Date();

console.log(format(date, 'dd/MM/yyyy'));
// 10/06/2026
```

### Tokens más usados

| Token | Ejemplo   |
|-------|-----------|
| `dd`  | 01, 02, 10|
| `MM`  | 01, 06, 12|
| `yyyy`| 2026      |
| `HH`  | 00-23     |
| `mm`  | 00-59     |
| `ss`  | 00-59     |

```js
format(new Date(), 'dd/MM/yyyy HH:mm:ss');
// 10/06/2026 14:30:45
```

## 2. Parsear fechas

Convertir texto a `Date`.

```js
import { parse } from 'date-fns';

const date = parse(
  '10/06/2026',
  'dd/MM/yyyy',
  new Date()
);
```

## 3. Comparaciones

### ¿Es hoy?

```js
import { isToday } from 'date-fns';

isToday(new Date());
```

### ¿Es ayer?

```js
import { isYesterday } from 'date-fns';

isYesterday(date);
```

### Comparar fechas

```js
import { isAfter, isBefore } from 'date-fns';

isAfter(date1, date2);
isBefore(date1, date2);
```

## 4. Operaciones matemáticas

### Agregar días

```js
import { addDays } from 'date-fns';

const result = addDays(new Date(), 7);
```

### Restar meses

```js
import { subMonths } from 'date-fns';

const result = subMonths(new Date(), 2);
```

### Agregar años

```js
import { addYears } from 'date-fns';

const result = addYears(new Date(), 1);
```

## 5. Diferencias entre fechas

### Días

```js
import { differenceInDays } from 'date-fns';

differenceInDays(
  new Date('2026-06-20'),
  new Date('2026-06-10')
);
// 10
```

### Horas

```js
import { differenceInHours } from 'date-fns';
```

### Minutos

```js
import { differenceInMinutes } from 'date-fns';
```

## 6. Rangos

### Inicio del mes

```js
import { startOfMonth } from 'date-fns';

startOfMonth(new Date());
```

### Fin del mes

```js
import { endOfMonth } from 'date-fns';

endOfMonth(new Date());
```

### Inicio de semana

```js
import { startOfWeek } from 'date-fns';

startOfWeek(new Date());
```

## 7. Fechas relativas

Muy útil para UI.

```js
import { formatDistanceToNow } from 'date-fns';
import { es } from 'date-fns/locale';

formatDistanceToNow(
  new Date('2026-06-09'),
  {
    addSuffix: true,
    locale: es
  }
);
// hace 1 día
```

## 8. Localización (i18n)

```js
import { format } from 'date-fns';
import { es } from 'date-fns/locale';

format(
  new Date(),
  "EEEE d 'de' MMMM",
  { locale: es }
);
// miércoles 10 de junio
```

## 9. Timezones (Nueva característica importante de v4)

Antes se necesitaba `date-fns-tz`. Ahora date-fns incorpora soporte oficial mediante `@date-fns/tz` y la opción `in` en muchas funciones.

### Crear fecha en una zona horaria

```js
import { TZDate } from '@date-fns/tz';

const limaDate = new TZDate(
  2026,
  5,
  10,
  'America/Lima'
);
```

### Formatear en una zona específica

```js
import { format } from 'date-fns';
import { tz } from '@date-fns/tz';

format(
  new Date(),
  'yyyy-MM-dd HH:mm:ss',
  {
    in: tz('America/Lima')
  }
);
```

## 10. Casos comunes en React

### Mostrar fecha de API

```js
import { format } from 'date-fns';

const createdAt =
  '2026-06-10T14:00:00Z';

format(
  new Date(createdAt),
  'dd/MM/yyyy'
);
```

### Mostrar fecha relativa

```js
import { formatDistanceToNow } from 'date-fns';
import { es } from 'date-fns/locale';

formatDistanceToNow(
  new Date(createdAt),
  {
    addSuffix: true,
    locale: es
  }
);
```

### Validar expiración

```js
import { isAfter } from 'date-fns';

const expired = isAfter(
  new Date(),
  expirationDate
);
```

## Funciones que más usarás en proyectos React

- `format()`
- `parse()`
- `addDays()`
- `subDays()`
- `addMonths()`
- `differenceInDays()`
- `differenceInHours()`
- `isAfter()`
- `isBefore()`
- `isToday()`
- `startOfMonth()`
- `endOfMonth()`
- `formatDistanceToNow()`

Si trabajas con formularios, tablas y filtros (como los CRUDs y dashboards que has estado desarrollando), estas funciones cubren aproximadamente el 90% de los casos de uso. Además, para filtros por rango de fechas suelen combinarse con `startOfDay`, `endOfDay`, `startOfMonth` y `endOfMonth`.
