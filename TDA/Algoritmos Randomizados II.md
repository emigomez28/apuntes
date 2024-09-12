# Diccionarios y Hashing

## Diccionarios

- Un diccionario es una estructura de datos que permite almacenar pares clave-valor.
- Mapean claves y valores mediante una función de hash.
- Para $u \neq v, h(u) \neq h(v)$ Sino, hay colisión.
- Las funciones de Hashing debem distribuir los elementos del Universo U de la mejor manera posible en las n posiciones de la tabla.

### Problema

Para cualquier $|U| > n²$, existen n elementos que coinciden en el valor de la función h, creando colisión para todos ellos, y se degrada la complejidad a $\mathcal{O}(n)$.

### Funciones de Hashing

- Podríamos tomar los primeros bits del elemento $\rightarrow$ según el dominio del problema podrían repetirse con frecuencia.
- Tomar siempre módulo n $\rightarrow$ Igual que arriba, en especial para potencias de 2
- En general, cualquier selección fija puede estar sujeta a colisiones y malas distribuciones.

### Randomizar la Función de Hashing

- Al insertar un elemento $u$, tomamos $h(u)$ como valor al azar entre 0 y n - 1. La probabilidad de colisión es de $\frac{1}{n}$.
- El problema con esto es que no podemos buscar un elemento en $\mathcal{O}(1)$, ya que no sabemos dónde está.
- Para resolver el problema anterior necesitariamos otra estructura de tipo diccionario, lo cual no tiene sentido.

### Clases de Funciones de Hash

Vamos a elegir, al azar, una función de Hash dentro de una Clase de Funciones $H$.

Propiedades deseables:

1. Para cualquier par de elementos $u,v$ en el Universo $U$, la probabilidad de colisión sea como mucho $\frac{1}{n}$ $\rightarrow$ La Clase es Universal.
2. La función $h$ puede ser representada de forma compacta, y podemos calcular el valor $h(u)$ de manera eficiente.

Si la Clase es Universal, las funciones $h$ dentro de la clase cumplen que:

> Para cualquier Set $S$ en $U$ de tamaño hasta $n$, sería esperable que la cantidad de elementos en $S$ que colisionan un elemento $u$ sea a lo sumo 1.

- $E[X_S] = P[X_S = 1] \leq \frac{1}{n}$
- $E[X] = \sum_{s \in S} E[X_s] \leq |S| \cdot \frac{1}{n} \leq 1$

### Diseñando una Clase Universal

- Buscamos un número primo $p$ similar al valor de $n$ como tamaño de tabla de Hash. Cada función dentro de la clase se identifica como $a = (a_1, a_2, \ldots, a_r) con $0 \leq a_i \leq p$

- Para un Universo de N elementos, $r \approx \frac{log N}{log n}$, tomando un conjunto de bits en c/u.

- Definimos el valor de F Hash para algun vector $x$, con $0 \leq x_i \leq p$ **al azar**.

- La representación de cada F Hash cumple con ser compacta.

$h_a(x) = (\sum_{i = 1}^{r} a_i \cdot x_i) % p$.

### Demostración.

Centrandose en la coordenada $a_j$ donde se sabe que x e y difieren:

- Sea $h_a(x) = (\sum_{i = 1}^r a_i \cdot x_i) % p$ y $h_a(y) = (\sum_{i = 1}^r a_i \cdot y_i) % p$.

- $a_j \cdot (y_j - x_j) = \sum_{i \neq j} a_i \cdot (x_i - y_i) % p$

- De lado derecho se obtiene algún numero $m % p$.

- Con $z = y_j - x_j$, la igualdad es $a_j \cdot z = m % p$. Siendo que $0 \leq a_j \leq p$ y que p es un número primo, hay solamente un valor que satisface la igualdad, entonces la probabilidad de seleccionar ese valor es $\frac{1}{p}$.

### Aplicación: Perfect Hashing

#### FKS Hashing

- Se aplica cuando sabemos cuales/cuantos elementos habrán.

- Tabla de tamaño número primo, con prob de colisión $\frac{1}{n}$ $\rightarrow$ Clase Universal.

- Para los elementos que colisionan se crea una nueva sub-tabla de Hash solamanete apra esos elementos, de tamaño $n_{i}^2$

- Para esta nueva sub-tabla se usa cualquier función de la clase universal que cumpla que no genera colisiones para los $n_i$ elementos.

- Al tomar cualquier función al azar de la clase, la cantidad esperada de colisiones es $\frac{1}{2}$.

- El espacio utilizado es $\mathcal{O}(n)$.

## Las Vegas

> Metodología de crear algoritmos randomizados los cuales siempre dan la respuesta correcta y el tiempo en encontrar dicha solución puede variar aleatoriamente.

## Montecarlo

> Metodología para crear algoritmos randomizados la cual puede dar la respuesta incorrecta con cierta probabilidad y tiene un tiempo de ejecución acotado polinomialmente siempre. Como parte de un esquema, se puede correr varias veces para incrementar la confianza de la solución.

## Algoritmo de Karge: Ejemplo de Montecarlo

El algoritmo contrae aristas al azar para encontrar el corte mínimo.

- La probabolidad de que una corrida del algoritmo encuentre el corte mínimo es de $\binom{n}{2}^{-1}$

- Repitiendo el algoritmo una cantidad de veces: $\binom{n}{2} \cdot ln n$.

- Quedandose siempre con el mejor resultado, la probabilidad de **no** encontrar el corte mínimo esta acotada por $\frac{1}{n}$.

## Heurísticas

- Métodos para aproximarse más rápidamente a una solución.
- Una función heurística es ua función que permite rankear diferentes opciones basada en la información disponible.
- Los algoritmos Greedy pueden verse como heurísticas que tratan de aproximas la solución óptima.

## Algoritmo A\*

- Extensión de Dijkstra.

- Mientras que Dijkstra encuentra todos los caminos mínimos desde un vértice a todos los demás, A\* busca camino mínimo hasta un solo destino.

- Intenta encontrar ese camino lo más rapido posible con el uso de heurísticas.

- Realiza busquedas informadas, siempre buscando según la heurística.

### Pasos de A\*

1. Empezando por el vértice origen
2. Evalúa las posibilidades que tiene, empezando primero por aquella que tenga mejores psobilidades de dar buen resultado según la Heurística.

> $H(n)$ función Heurística de costo estimado hasta el vértice destino

3. Se elige el camino con mejor suma $g(n)$, distancia ya calculada, más $h(n)$.
4. Matiene este valor en el Heap (como Dijkstra) y sigue iterando, buscando por el mejor $g(n) + h(n)$.
5. $h(d)$ donde $d$ es el destino debería ser 0. Al desecolar el destino se encontró el camino.
6. Si la Heurística es admisible, el camino encontrado es el óptimo.

> Una Heurística es admisible si nunca sobreestima el costo entre 2 vertices.

## Posibles Heuristicas

- Distancia Manhattan.
- Distancia Euclidiana.
- Distancia de Chebyshev.

## Hill Climbing

Busqueda de optimización local, toma una posición inicial aleatoria y hace movimientos en direccion de mejorar la optimización, encuentra el óptimo local pero no necesariamente el global.

## Random Walks

Es un proceso aleatorio que describe un camino que consiste en dar pasos al azar.
