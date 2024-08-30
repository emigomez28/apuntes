# Tecnicas de diseño

Patrón para resolver una familia de problemas.

## Receta DyC

1. Dividimos el problema en subproblemas.
2. Resolvemos los subproblemas recursivamente.
3. Combinamos las soluciones de cada subproblemas.

Para analizar complejidad vamos a usar la ec. de recurrencia que representa al algoritmo.

## Teorema Maestro

Si tenemos un algoritmo cuya ecuación de recurrencia es de la forma $T(n) = A \cdot T(\frac{n}{B}) + \mathcall{O}(n^C)$ donde:

- A: Cantidad de llamados recursivos.
- B: Proporción del tamaño original con el que llamamos recursivamente.
- $\mathcall{O}(n^C)$: Costo de partir y juntar $\rightarrow$ Todo lo que no son llamados recursivos.

Finalmente, la complejidad del algoritmo es:

$\begin{cases}
    \mathcall{O}(n^C) & \text{si } log_B(A) < C \\
    \mathcall{O}(n^C \cdot log_B(n)) & \text{si } log_B(A) = C \\
    \mathcall{O}(n^{log_B(A)}) & \text{si } log_B(A) > C
\end{cases}$

### Condiciones para aplicar el Teorema Maestro

1. _A_ tiene que ser un número natural.
2. _B_ tiene que ser un número real mayor a 1 y constante $\rightarrow$ si es menor que uno agrando el problema.
3. El caso base es _constante_. No puede depender de la cantidad de los elementos.

# Problemas avanzados de DyC

## Problema 1: Multiplicación de números _muy_ grandes.

> Quiero multiplcar n y m siendo estos números muy grandes.

### Primer enfoque

La idea es ir calculando productos parciales por cada dígito, multiplicando por la base (10) y sumando.

Tomamos cada _m_ dígito y lo multiplicamos contra los _n_ dígitos, y luego sumamos los _n_ resultados parciales.$\rightarrow$ $\mathds{O}(n \times m)$, es decir, tiende a ser cuadrático., es decir, tiende a ser cuadrático.

El problema con esto es que no todas las multiplcaciones son necesarias.

### Un algoritmo mejor

Escribimos la multiplicación como si estuvieramos trabajando en base 2, y separamos la primer mitad de la segunda mitad.

> Ejemplo, si tuviera el 1234 en base 10 sería 12 y 34 en base 10, esta misma idea trasladarla a la base de trabajo.

Entonces: $x = x_1 \cdot 2^{\frac{n}{2}} + x_0$.

- n: cantidad de dígitos.
- $x_1$: primeros $\frac{n}{2}$ dígitos.
- $x_0$: últimos $\frac{n}{2}$ dígitos.

> Se trabaja en base 2 porque es más fácil dividir por 2, haciendo shifts.

Ahora, con esta forma de representar los números, la multiplicación se puede hacer de la siguiente forma:

$x \cdot y = (x_1 \cdot 2^{\frac{n}{2}} + x_0) \cdot (y_1 \cdot 2^{\frac{n}{2}} + y_0)$.

Luego, aplicando distributiva:

$x_1 \cdot y_1 \cdot 2^n + (x_1 \cdot y_0 + x_0 \cdot y_1) \cdot 2^{\frac{n}{2}} + x_0 \cdot y_0$

> Se puede ver que se hacen 4 multiplicaciones, pero se pueden hacer 3. De hacer las 4 multiplicaciones la ec. de recurrencia no mejora.

Para poder realizar 3 multiplicaciones, primero hay que ver lo siguiente:

$(x_1 + x_0) \cdot (y1 + y_0) = x_1 \cdot y_1 + x_1 \cdot y_0 + x_0 \cdot y_1 + x_0 \cdot y_0 $

> La gracia de esto es que se ven todos los terminos que vimos antes. En una sola multiplicación calculamos dos terminos, aunque sumemos demás los otros 2 (que se pueden calcular para restar).

```python
def multiplicacion_big_int(x, y):
    if len(x) == 1 or len(y) == 1:
        return int(x) * int(y)

    n = max(len(x), len(y))
    n_2 = n // 2

    x_1 = x[:-n_2]
    x_0 = x[-n_2:]
    y_1 = y[:-n_2]
    y_0 = y[-n_2:]

    z0 = multiplicacion_big_int(x_0, y_0)
    z1 = multiplicacion_big_int(x_1, y_1)
    z2 = multiplicacion_big_int(str(int(x_1) + int(x_0)), str(int(y_1) + int(y_0))) - z0 - z1

    return z1 * 10**(2*n_2) + z2 * 10**n_2 + z0
```

Con esto, la ec de recurrencia queda: $T(n) = 3 \cdot T(\frac{n}{2}) + \mathcall{O}(n)$, lo que da como resultado $\mathcall{O}(n^{log_2 3}) \approx  \mathcall{O}(n^{1,6})$.

## Problema 2: Obtener extremo de un polígono.

Tenemos n vértices $V = (v_0, v_1, ..., v_n)$, dispuestos en sentido antihorario. Obtener el vértice extremo (puede ser tanto máximo como mínimo) respecto a un eje (eje arbitario, llamemosle u).

### Algoritmo sencillo

Itero el arreglo y proyecto todos, me quedo con el mejor $\rightarrow$ $\mathcall{O}(n)$.

### Algoritmo mejor

> Este algoritmo se puede aplicar si y solo sí el polígono es convexo. Un polígono es convexo cuando una recta no puede cortarlo más de 2 veces.

- Tenemos $e_i$ como el segmento que va de $v_i$ a $v_{i+1}$, $e_i = v_{i + 1} - v_i$.
- Lo que importa es la dirección a la que apunta el segmento con respecto al eje.

> Es una "Busqueda Binaria" en 2 dimensiones.

- La complejidad es $\mathcall{O}(log n)$

## Problema 3: Encontrar el punto más cercano a un conjunto de puntos.

Dado n puntos en un plano, buscar la pareja que se encuentre más cercana.

### Algoritmo sencillo

Comparar todos contra todos y quedarme con el mas cercano $\rightarrow$ $\mathcall{O}(n^2)$.

### Algoritmo mejor

> Asumimos que ningun par de puntos comparten la misma coordenada x o y. No es estrictamente necesario, pero es para simplificar algunas cosas.

#### Pensar el algoritmo en una sola dimension.

- Lo mas sencillo para encontrar los mas cercano es ordenarlos y comparamos por parejas.

> Si bien esto no resuelve el problema en sí, pero algunas nociones de MergeSort pueden servir.

- Vamos a buscar la pareja mas cercana del lado izquierdo, luego la otra del derecho y luego en tiempo lineal buscamos los mas cercanos $\rightarrow$ $\mathcall{O}(n log n)$

1. Teniendo el arreglo p de puntos, construyo $p_x$ y $p_y$ ordenados por x e y respectivamente.
2. Vamos a tener un "punto de quiebre", entonces ahi parto a la mitad y llamo recursivamente. Esto lo que hace es construir 4 arreglos nuevos $Q_x$, $Q_y$, $R_x$ y $R_y$.
3. Para construir $Q_x$ recorro $p_x$ y la primer mitad va allí, luego la segunda mitad a $R_x$.
4. Para construir $Q_y$ recorro $p_y$ y miro su coordenada x, si es menor al "x de quiebre" va a $Q_y$, sino a $R_y$.
5. El caso base, si tengo 3 puntos, ya puedo saber cual es la pareja. Tomo a 3 como caso base porque si el caso base fuera 2, cuando llame con 3 me quedaría un punto suelto y no podría compararlo contra ninguno.
6. Luego de llamar y encontrar 2 candidatos, me quedo con los que esten a menor distancia d. Creo un nuevo arreglo $S_y$ donde me voy a quedar con aquellos puntos que esten a una distancia menor o igual que d, tomando desde el "punto de quiebre" ordenado crecientemente por el eje y.
7. Luego, comparo los puntos contra una cantidad constante (15) de puntos, ya que los puntos no pueden estar a menor distancia que $\frac{3}{2}d$. Entonces, en el peor de los casos, este paso es lineal.

## Problema 4: Multiplcación de matrices.

> Es similar al ejercicio de multiplicación de numeros grandes.

### Algoritmo sencillo

Multiplicar todas las matrices $\rightarrow$ $\mathcall{O}(n^3)$..

### Primer idea

Podemos dividir a cada matriz de $n \times n $ en 4 sub-matrices de $\frac{n}{2} \times \frac{n}{2}$ (por simplicidad se asume que n es potencia de 2).

Ej:

- $A = \begin{bmatrix} A_{11} & A_{12} \\ A_{21} & A_{22} \end{bmatrix}$
- $B = \begin{bmatrix} B_{11} & B_{12} \\ B_{21} & B_{22} \end{bmatrix}$
- $C = \begin{bmatrix} C_{11} & C_{12} \\ C_{21} & C_{22} \end{bmatrix}$
- $D = \begin{bmatrix} D_{11} & D_{12} \\ D_{21} & D_{22} \end{bmatrix}$

Donde cada $A_{i, j}$ es una matriz de $\frac{n}{2} \times \frac{n}{2}$.

Entonces, la ec de recurrencia es: $T(n) = 8 \cdot T(\frac{n}{2}) + \mathcall{O}(1)$, que es $\mathcall{O}(n^3)$.

### Algoritmo mejor (Strassen)

En vez de hacer 8 llamados recursivos, hacemos 7. Haciendo algo similar a Karatsuba-Offman para multiplicar números grandes (con cierta multiplicación se ahorra otra).

Entonces, la ec de recurrencia es: $T(n) = 7 \cdot T(\frac{n}{2}) + \mathcall{O}(1)$ $\approx$ $\mathcall{O}(n^{2,8})$.

## Problema 5: FFT - Transformada rápida de Fourier.

Problema: Tenemos dos vectores A y B, y queremos obtener la convolución entre ambos.

> Convolución: $(f * g) (t) = \int_{-\infty}^{\infty} f(n)g(t - n) dn$

## Problema 6: Conteo de inversiones

Tengo un conjuto de n elementos + 2 arreglos ordenados por diferentes criterios. Quiero dar una medida de semejanza entre dichos arreglos.

1. Numeramos lo elementos en A con 1, 2, ..., n y B como correspondiente a eso.
2. ¿Que tan diferente es el orden B (de forma ascendente) respecto a A? Si $\forall b_i < b_{i + 1}$ $\rightarrow$ B esta ordenado $\rightarrow$ A y B son iguales.
3. Inversiones: Dos elementos están invertidos si $b_i > b_j$ para $i < j$. Tengo un elemento mas grande antes que uno mas chico.

### Algoritmo sencillo

Los comparo todos contra todos y cuento las inversiones $\rightarrow$ $\mathcall{O}(n^2)$.

### Algoritmo mejor

- Para esto nos centramos en MergeSort. En la etapa de merge, al meter elementos de un arreglo en el otro, de cierto modo estamos contando inversiones.
- Si mi arreglo tiene un elemento o esta ordenado, no tengo inversiones.
- Ordenamos y contamos, entonces asi ya tenemos la solución.
- El arreglo que esta ordenado al reves es el que tiene mas inversiones.
- La complejidad es $\mathcall{O}(n log n)$.
