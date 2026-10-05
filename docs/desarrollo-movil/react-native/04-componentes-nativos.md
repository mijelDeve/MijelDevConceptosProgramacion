# Componentes nativos de React Native

React Native trae un conjunto de componentes que se renderizan como elementos nativos del sistema (no HTML como en la web).

## Componentes principales

| Componente | Para qué sirve | Equivalente en web |
|---|---|---|
| `<View>` | Contenedor base (cajas, layout) | `<div>` |
| `<Text>` | Mostrar texto | `<p>`, `<span>`, `<h1>`, ... |
| `<TextInput>` | Campo de texto editable | `<input>` / `<textarea>` |
| `<Pressable>` | Elemento con toque (botón) | `<button>` |
| `<ScrollView>` | Área con scroll | `<div>` con `overflow: auto` |
| `<FlatList>` | Listas largas y eficientes | `map()` con elementos |
| `<SectionList>` | Listas con encabezados por sección | — |
| `<Image>` | Mostrar imágenes | `<img>` |
| `<Modal>` | Ventana modal | `<dialog>` |
| `<ActivityIndicator>` | Indicador de carga (spinner) | — |
| `<Switch>` | Interruptor on/off | `<input type="checkbox">` |

> A diferencia de la web, en React Native **no existen** etiquetas como `<div>`, `<span>` o `<button>` ni estilos CSS por clases. Todo se compone con estos componentes y se estiliza con `StyleSheet`.

## FlatList

`FlatList` es un componente para mostrar listas de elementos de forma eficiente, especialmente cuando tienes muchos datos.

```tsx
<FlatList
  data={usuarios}
  renderItem={({ item }) => (
    <Text>{item.nombre}</Text>
  )}
  keyExtractor={(item) => item.id.toString()}
/>
```

Partes de la prop:

- **`data`**: contiene los datos de la lista.
- **`renderItem`**: indica cómo se dibuja cada elemento.
- **`item`**: es el elemento actual.
- **`keyExtractor`**: proporciona una clave única para cada elemento.

### ¿Por qué FlatList y no un `map()`?

| Característica | `map()` dentro de `<ScrollView>` | `FlatList` |
|---|---|---|
| Render de los elementos | Todos a la vez | Solo los visibles (virtualizado) |
| Rendimiento con miles de filas | Deja de ser fluido | Se mantiene fluido |
| Memoria | Crece con la lista | Constante |
| Scroll eficiente | No | Sí (reutiliza filas) |

### Ejemplo completo

```tsx
import { View, Text, FlatList, StyleSheet } from 'react-native';

const usuarios = [
  { id: 1, nombre: 'Ana' },
  { id: 2, nombre: 'Luis' },
  { id: 3, nombre: 'Marta' },
];

export default function ListaUsuarios() {
  return (
    <View style={styles.container}>
      <FlatList
        data={usuarios}
        renderItem={({ item }) => (
          <Text style={styles.item}>{item.nombre}</Text>
        )}
        keyExtractor={(item) => item.id.toString()}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 16,
  },
  item: {
    fontSize: 16,
    paddingVertical: 12,
    borderBottomWidth: 1,
    borderBottomColor: '#e2e8f0',
  },
});
```

## Otras props útiles de FlatList

| Prop | Qué hace |
|---|---|
| `ItemSeparatorComponent` | Componente entre cada fila (separador) |
| `ListHeaderComponent` | Encabezado al inicio de la lista |
| `ListFooterComponent` | Pie al final de la lista |
| `onRefresh` / `refreshing` | Pull-to-refresh |
| `onEndReached` | Cargar más al llegar al final (infinite scroll) |
| `numColumns` | Número de columnas |
| `horizontal` | Poner la lista en horizontal |