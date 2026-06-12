# Set en JavaScript

## 1. ¿Qué es Set?

`Set` es una estructura de datos introducida en ES6 que permite almacenar **valores únicos** de cualquier tipo, ya sean primitivos u objetos. A diferencia de los arrays, un `Set` no permite duplicados y ofrece mejor rendimiento para operaciones de búsqueda y eliminación.

```js
const mySet = new Set();

mySet.add(1);
mySet.add(2);
mySet.add(2); // ignorado, ya existe
mySet.add('hola');

console.log(mySet); // Set(3) { 1, 2, 'hola' }
```

También se puede inicializar desde un array:

```js
const numbers = new Set([1, 2, 3, 3, 4, 4, 5]);
console.log(numbers); // Set(5) { 1, 2, 3, 4, 5 }
```

---

## 2. Métodos principales

| Método | Descripción | Ejemplo |
|---|---|---|
| `add(valor)` | Agrega un valor (si no existe) | `set.add(1)` |
| `has(valor)` | Verifica si existe → `boolean` | `set.has(1)` |
| `delete(valor)` | Elimina un valor → `boolean` | `set.delete(1)` |
| `clear()` | Vacía el Set | `set.clear()` |
| `size` | Propiedad: cantidad de elementos | `set.size` |

```js
const set = new Set([1, 2, 3]);

console.log(set.has(2));   // true
console.log(set.has(5));   // false

set.delete(2);
console.log(set.size);     // 2

set.clear();
console.log(set.size);     // 0
```

---

## 3. Iteración sobre un Set

Los Sets son iterables y se pueden recorrer de varias formas:

```js
const frutas = new Set(['manzana', 'pera', 'naranja']);

// for...of
for (const fruta of frutas) {
  console.log(fruta);
}

// forEach
frutas.forEach(fruta => console.log(fruta));

// Convertir a array
const arrayFrutas = [...frutas];
console.log(arrayFrutas); // ['manzana', 'pera', 'naranja']
```

---

## 4. Casos de uso comunes

### Eliminar duplicados de un array

```js
const conDuplicados = [1, 2, 2, 3, 4, 4, 5, 5, 5];
const unicos = [...new Set(conDuplicados)];
console.log(unicos); // [1, 2, 3, 4, 5]
```

### Unión de conjuntos

```js
const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
const union = new Set([...a, ...b]);
console.log(union); // Set(5) { 1, 2, 3, 4, 5 }
```

### Intersección de conjuntos

```js
const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
const interseccion = new Set([...a].filter(x => b.has(x)));
console.log(interseccion); // Set(1) { 3 }
```

### Diferencia de conjuntos

```js
const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
const diferencia = new Set([...a].filter(x => !b.has(x)));
console.log(diferencia); // Set(2) { 1, 2 }
```

---

## 5. Ejemplos en React

### 5.1 Selección múltiple con toggle de IDs

```jsx
import { useState } from 'react';

function SelectorItems() {
  const [selected, setSelected] = useState(new Set());

  const toggleItem = (id) => {
    setSelected(prev => {
      const next = new Set(prev);
      if (next.has(id)) {
        next.delete(id);
      } else {
        next.add(id);
      }
      return next;
    });
  };

  const items = [
    { id: 1, name: 'React' },
    { id: 2, name: 'Vue' },
    { id: 3, name: 'Angular' },
  ];

  return (
    <div>
      <h2>Selecciona tecnologías:</h2>
      {items.map(item => (
        <label key={item.id} style={{ display: 'block', margin: '8px 0' }}>
          <input
            type="checkbox"
            checked={selected.has(item.id)}
            onChange={() => toggleItem(item.id)}
          />
          {item.name}
        </label>
      ))}
      <p>Seleccionados: {[...selected].join(', ') || 'ninguno'}</p>
    </div>
  );
}
```

### 5.2 Tags únicos desde una lista

```jsx
function TagsAutomaticos({ posts }) {
  // Extraer tags únicos de una lista de posts
  const uniqueTags = [...new Set(posts.flatMap(p => p.tags))];

  return (
    <div>
      <h3>Todos los tags</h3>
      {uniqueTags.map(tag => (
        <span key={tag} style={{ margin: '4px', padding: '4px 8px', background: '#eee' }}>
          #{tag}
        </span>
      ))}
    </div>
  );
}
```

### 5.3 Filtrado con Set para evitar duplicados

```jsx
function ListaAutoresUnicos({ libros }) {
  // Set garantiza autores únicos
  const autores = [...new Set(libros.map(libro => libro.autor))];

  return (
    <ul>
      {autores.map(autor => (
        <li key={autor}>{autor}</li>
      ))}
    </ul>
  );
}
```

### 5.4 Formulario dinámico con elementos únicos

```jsx
import { useState } from 'react';

function FormularioTags() {
  const [tags, setTags] = useState(new Set());
  const [input, setInput] = useState('');

  const agregarTag = () => {
    const tag = input.trim().toLowerCase();
    if (!tag) return;

    setTags(prev => {
      if (prev.has(tag)) {
        alert(`El tag "${tag}" ya existe`);
        return prev;
      }
      return new Set(prev).add(tag);
    });
    setInput('');
  };

  const eliminarTag = (tag) => {
    setTags(prev => {
      const next = new Set(prev);
      next.delete(tag);
      return next;
    });
  };

  return (
    <div>
      <h3>Agregar tags</h3>
      <input
        value={input}
        onChange={e => setInput(e.target.value)}
        onKeyDown={e => e.key === 'Enter' && agregarTag()}
        placeholder="Escribe un tag..."
      />
      <button onClick={agregarTag}>Agregar</button>

      <div style={{ marginTop: 12 }}>
        {[...tags].map(tag => (
          <span key={tag}
            style={{ margin: 4, padding: '4px 8px', background: '#e3f2fd', cursor: 'pointer' }}
            onClick={() => eliminarTag(tag)}>
            {tag} ✕
          </span>
        ))}
      </div>
    </div>
  );
}
```

---

## 6. Resumen

- `Set` almacena valores **únicos**.
- Es **iterable** y tiene métodos eficientes (`has`, `add`, `delete`).
- Ideal para eliminar duplicados y operaciones entre conjuntos.
- En React es útil para manejar selecciones, tags, filtros y cualquier caso donde necesites garantizar unicidad.
