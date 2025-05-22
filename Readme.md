# Flexbox Avanzado: Guía Completa

Esta guía cubre los conceptos avanzados de Flexbox vistos en clase, explicando cada propiedad, sus valores y cómo calcular las dimensiones finales de los elementos flex.

## Índice
- [Flexbox vs Inline-Flex](#flexbox-vs-inline-flex)
- [Propiedades de Alineación](#propiedades-de-alineación)
  - [align-items](#align-items)
  - [align-self](#align-self)
  - [align-content](#align-content)
- [Propiedades de Crecimiento y Encogimiento](#propiedades-de-crecimiento-y-encogimiento)
  - [flex-basis](#flex-basis)
  - [flex-grow](#flex-grow)
  - [flex-shrink](#flex-shrink)
  - [La propiedad shorthand flex](#la-propiedad-shorthand-flex)

## Flexbox vs Inline-Flex

La diferencia entre `display: flex` y `display: inline-flex` radica en cómo se comporta el contenedor con respecto a los elementos circundantes:

| `display: flex` | `display: inline-flex` |
|-----------------|------------------------|
| Ocupa todo el ancho disponible (elemento de bloque) | Ocupa solo el espacio necesario (elemento en línea) |
| Fuerza un salto de línea antes y después | No fuerza saltos de línea |
| El contenedor es un elemento de bloque | El contenedor es un elemento en línea |

## Propiedades de Alineación

### align-items

Controla la alineación de los elementos a lo largo del eje transversal (cross-axis) dentro de cada línea.

```css
.container {
    display: flex;
    align-items: flex-start | flex-end | center | stretch | baseline;
}
```

- `flex-start`: Alinea elementos al inicio del eje transversal
- `flex-end`: Alinea elementos al final del eje transversal
- `center`: Centra elementos en el eje transversal
- `stretch` (valor predeterminado): Estira elementos para llenar el contenedor
- `baseline`: Alinea elementos según la línea base del texto

### align-self

Permite sobrescribir el valor de `align-items` para elementos individuales.

```css
.item {
    align-self: flex-start | flex-end | center | stretch | baseline;
}
```

### align-content

Controla la distribución de las líneas (filas o columnas) en el eje transversal cuando hay espacio extra y múltiples líneas de elementos.

```css
.container {
    display: flex;
    flex-wrap: wrap;
    align-content: flex-start | flex-end | center | space-between | space-around | space-evenly | stretch;
}
```

- `flex-start`: Líneas agrupadas al inicio
- `flex-end`: Líneas agrupadas al final
- `center`: Líneas centradas
- `space-between`: Espacio uniforme entre líneas, sin espacios en los extremos
- `space-around`: Espacio uniforme entre líneas, con espacios menores en los extremos
- `space-evenly`: Espacio uniforme entre líneas, incluidos los extremos
- `stretch` (valor predeterminado): Líneas estiradas para llenar el espacio

## Propiedades de Crecimiento y Encogimiento

### flex-basis

Define el tamaño inicial de un elemento flex antes de que se apliquen `flex-grow` o `flex-shrink`.

```css
.item {
    flex-basis: auto | 0 | <length> | <percentage> | content;
}
```

- `auto` (valor predeterminado): Usa el `width` si está definido, si no usa el contenido
- `0`: Empieza desde cero (ignora el contenido)
- `<length>`: Valor fijo en px, em, etc.
- `<percentage>`: Porcentaje del contenedor flex
- `content`: Basado en el contenido del elemento

#### Ventajas de flex-basis sobre width
- Es específico para flexbox
- Funciona mejor con flex-grow y flex-shrink
- Se adapta a flex-direction (row/column)
- Es fácil de sobrescribir con flex-grow/shrink

### flex-grow

Define la capacidad de un elemento para crecer si hay espacio disponible.

```css
.item {
    flex-grow: 0 | <número>;
}
```

- `0` (valor predeterminado): No crece
- `<número>`: Factor de crecimiento proporcional a otros elementos

#### Cálculo del ancho final con flex-grow

```
Ancho final = tamaño base + (espacio disponible × proporción de flex-grow)
```

Donde:
1. **Tamaño base**: El tamaño inicial (según flex-basis)
2. **Espacio disponible**: Ancho del contenedor - suma de tamaños base
3. **Proporción de flex-grow**: Valor de flex-grow del elemento ÷ suma total de todos los valores flex-grow

#### Ejemplo paso a paso

Para un contenedor de 500px con tres elementos:
- Item1: flex-grow: 3, contenido: ~20px
- Item2: flex-grow: 2, contenido: ~20px
- Item3: flex-grow: 1, contenido: ~20px

1. **Tamaños base**: 20px + 20px + 20px = 60px
2. **Espacio disponible**: 500px - 60px = 440px
3. **Suma total flex-grow**: 3 + 2 + 1 = 6
4. **Proporción para cada elemento**:
   - Item1: 3/6 = 0.5 (50%)
   - Item2: 2/6 = 0.33 (33.33%)
   - Item3: 1/6 = 0.17 (16.67%)
5. **Distribución del espacio**:
   - Item1: 440px × 0.5 = 220px
   - Item2: 440px × 0.33 = 146.67px
   - Item3: 440px × 0.17 = 73.33px
6. **Ancho final**:
   - Item1: 20px + 220px = 240px
   - Item2: 20px + 146.67px = 167px
   - Item3: 20px + 73.33px = 93px

### flex-shrink

Define la capacidad de un elemento para encogerse si no hay suficiente espacio en el contenedor.

```css
.item {
    flex-shrink: 1 | <número>;
}
```

- `1` (valor predeterminado): Se encoge proporcionalmente
- `0`: No se encoge
- `<número>`: Factor de encogimiento proporcional

#### Cálculo con flex-shrink

El cálculo es más complejo que con flex-grow, porque considera tanto el valor de flex-shrink como el tamaño base del elemento:

1. **Calcular espacio faltante**: 
   - Espacio faltante = suma de tamaños base - ancho del contenedor
   
2. **Calcular pesos**: 
   - Peso de cada elemento = flex-shrink × tamaño base
   - Peso total = suma de todos los pesos
   
3. **Calcular proporciones**:
   - Proporción = peso del elemento ÷ peso total
   
4. **Calcular reducción**:
   - Reducción = espacio faltante × proporción
   
5. **Tamaño final**: 
   - Tamaño final = tamaño base - reducción

#### Ejemplo paso a paso

Para un contenedor de 400px con tres elementos de 200px:
- Item1: flex-shrink: 1
- Item2: flex-shrink: 3
- Item3: flex-shrink: 1

1. **Espacio faltante**: (200px + 200px + 200px) - 400px = 200px
2. **Pesos**:
   - Item1: 1 × 200px = 200
   - Item2: 3 × 200px = 600
   - Item3: 1 × 200px = 200
   - Peso total: 1000
3. **Proporciones**:
   - Item1: 200/1000 = 0.2
   - Item2: 600/1000 = 0.6
   - Item3: 200/1000 = 0.2
4. **Reducción**:
   - Item1: 200px × 0.2 = 40px
   - Item2: 200px × 0.6 = 120px
   - Item3: 200px × 0.2 = 40px
5. **Tamaño final**:
   - Item1: 200px - 40px = 160px
   - Item2: 200px - 120px = 80px
   - Item3: 200px - 40px = 160px

### La propiedad shorthand flex

La propiedad `flex` combina `flex-grow`, `flex-shrink`, y `flex-basis` en una sola declaración:

```css
.item {
    flex: <flex-grow> <flex-shrink> <flex-basis>;
}
```

Valores comunes:

- `flex: 1` = `flex: 1 1 0%` (crece y se encoge, ignorando tamaño inicial)
- `flex: auto` = `flex: 1 1 auto` (crece y se encoge basado en su contenido)
- `flex: none` = `flex: 0 0 auto` (no crece ni se encoge)
- `flex: 0 auto` = `flex: 0 1 auto` (no crece, pero puede encogerse)

## Nota importante

Con `flex-basis: 0`, los cálculos son más simples y predecibles. El tamaño final corresponde exactamente con la proporción de `flex-grow`. Esta es la razón por la que muchos desarrolladores prefieren usar `flex: 1` para distribuir el espacio de manera uniforme.

---

Este documento cubre los conceptos avanzados de Flexbox vistos en clase. Para práctica adicional, revisen los ejercicios y ejemplos ubicados en las diferentes carpetas del proyecto.