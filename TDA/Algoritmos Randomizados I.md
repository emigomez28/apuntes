# Algoritmos Randomizados I

- La idea es aplicar algoritmos que usen el azar para resolver problemas y llegar a una solución.
- Algortimos diseñados para enfrentarse a cualquier entrada.
- Se le va a permitir al algoritmo hacer elecciones aleatorias. El rol de esta aleatoriedad es un factor completamente interno al algoritmo.

## Motivo

- En resúmen, sirve para poder crear modelos más potentes.
- Un _algoritmo detérministico eficiente_ que calcula la respuesta correcta puede verse como un caso particular de un _algoritmo randomizado_ que da la _respuesta correcta con alta probabilidad_.
- Un algoritmo randomizado puede ser más eficiente que un algoritmo determinístico.

## Algunos conceptos de Probabilidad

- La probabilidad de que un evento ocurra se denomina _p_, el cual es un valor entre 0 y 1.
- La probabilidad de que un evento no ocurra se denomina _1-p_.
- Esperanza, valor esperado de un evento aleatorio. $E[X] = \sum_{j = 0}^{\infty} j \cdot Pr[X = j]$ en el modelo discreto.
- Linealidad de la Esperanza: $E[X + Y] = E[X] + E[Y]$

## Problema de Encontrar la Mediana

Para un conjunto de números $S = \{a_1, a_2, \ldots, a_n\}$ la Mediana es aquel valor que estaría en la posición del medio si el conjunto de números estuviera ordenado. La mediana está en la posición k del arreglo ordenado tal que:

- k es $\frac{n + 1}{2}$ si la cantidad n es impar.
- k es $\frac{n}{2}$ si la cantidad n es par.

Este problema se puede resolver fácil ordenando (se puede reducir a ordenar)

### Problema de Selección

Para un conjunto de números $S = \{a_1, a_2, \ldots, a_n\}$ se quiere seleccionar aquel valor que estaría en la posición k si el conjunto de números estuviera ordenado.

> Idem arriba, se resuelve fácil ordenando.

Para los casos especificos k = 1 y k = n es trivial la solución en $\mathcal{O}(n)$ para la búsqueda del mínimo o máximo del arreglo.

Una solución randomizada en $\mathcal{O}(n)$ se puede implementar por DyC.

## Algoritmo Randomizado de Selección

Se elige algún valor $a_i$ como el pivot. Podemos partir el conjunto de valores en dos, $S^-$ con cualquier valor a $a_i$ y $S^+$ con valores mayores.

En función de la cnatidad de elementos de los conjuntos se puede saber donde buscar la seleccion deseada de k.

```python
def select(S, k):
    ai = elegir_pivot(S, k)
    S_menores = [elem for elem in S if elem < ai]
    S_mayores = [elem for elem in S if elem > ai]

    if len(S_menores) == k - 1:
        return ai

    if len(S_menores) >= k:
        return select(S_menores, k)

    return select(S_mayores, k - len(S_menores) - 1)
```

Sin importar que pivote agarre, esto siempre funciona, el problema es que puede tardar mucho.

## Complejidad

- Costo de buscar Pivot + Costo lineal de separar Sets + Costo de llamada recursiva.
- El tiempo de la llamada recursiva depende del nuevo tamaño del Set a seleccionar.
- Ideal: Los conjuntos tengan el mismo tamaño y que el Pivot sea siempre la mediana.

Entonces: $T(n) = T(\frac{n}{2}) + c \cdot n$, donde c es una constante. En este caso ideal la complejidad es de $\mathcal{O}(n)$.

### Elegir un buen Pivot

- No podemos poner como condición elegir la Mediana ya que es lo que estamos buscando.

- En realidad necesitamos un Pivot _bastante bien centrado_, que deje bastantes elementos en los conjuntos. Si achicamos cada set al menos por un factor $1 - \epsilon$.

- Para ello, buscamos que $T(n) \leq T((1 - \epsilon) \cdot n) + c \cdot n$. Esto se convierte en una geometrica, la cual converge en $c \cdot n \leq \frac{1}{\epsilon} \cdot c \cdot n$.

- El problema sería elegir siempre un mal pivote, que solamente ahorre un elemento a la vez

#### Elegir el Pivot al Azar

- Podemos decir que la mayoría de los valores están bastante bien centrados, elegir al azar _seguramente_ da un buen Pivot.

#### Demostración

- Decimos que el algoritmo está en fase j (llamado recursivo j o nivel de profundidad j), si la cantidad de elementos del set en consideración está entre $n(\frac{3}{4})^j$ y $n(\frac{3}{4})^{j+1}$.

- Para pasar de fase j a la fase j + 1, necesitamos al menos desestimar $\frac{1}{4}$ del total de elementos. Se logra la mitad central de Pivots, con probabilidad $\frac{1}{2}$.

- La cantidad esperada de iteraciones para avanzar de fase es 2.

- Podemos definir que X es la cantidad de trabajo del algoritmo hasta atravesar todas las fases, será $X = X_0 + X_1 + X_2 + \ldots$

- La cantidad de trabajo en una fase es alguna constante veces el tamaño del set en esa fase, que es como mucho $n(\frac{3}{4})^j$, esperable sean hasta 2 iteraciones.

Analizando la esperanza:

- $E[X_j] \leq 2 \cdot c \cdot n(\frac{3}{4})^j$

- Por linealidad: $E[X] = \sum\_{j} E[X_j] \leq \sum_j 2cn(\frac{3}{4})^j = 2cn \sum_j (\frac{3}{4})^j \leq 8cn$

Nos queda la complejidad esperada de $\mathcal{O}(n)$.

#### Otra alternativa

Elegir 3 elementos al azar y quedarnos con la mediana.

## QuickSort

```python
def quicksort(S):
    if len(S) <= 2:
        return S

    ai = elegir_pivot(S)

    S_menores = [elem for elem in S if elem < ai]
    S_mayores = [elem for elem in S if elem > ai]

    return quicksort(S_menores) + [ai] + quicksort(S_mayores)
```

La complejidad esperada es $\mathcal{O}(n log n)$ que es cuando partimos todo a la mitad, en el peor de los casos es $\mathcal{O}(n^2)$ que es cuando vamos partiendo de a un elemento.

Otra idea es:

```python
def quicksort(S):
    if len(S) <= 2:
        return S
    ai = elegir_pivot(S)

    while not pivot_es_central(S, ai):
        ai = elegir_pivot(S)
        S_menores = [elem for elem in S if elem < ai]
        S_mayores = [elem for elem in S if elem > ai]

        if len(S_menores) > len(S) // 4 and len(S_mayores) > len(S) // 4:
            # Si esta condición se cumple el pivot es central.
            return quicksort(S_menores) + [ai] + quicksort(S_mayores)
```

#### Conclusiones

- El tiempo del algoritmo es independiente de la entrada.
- No hay conjunto de datos de entrada que pueda garantizar el peor caso.
- El peor caso solamente surge de un mal caso de selecciones aleatorias realizadas.

## Estructuras de datos con aleatoriedad

### ABB

Dado un conjunto de elementos ordenarlos exclusivamente utilziando un árbol binario de busqueda.

1. Insertar los elementos en el árbol.
2. Hacer recorrido in-order.

> Comparado con QuickSort, tiene los mismos casos. Si elijo el primer elemento como el Pivot en QS, su arbol de recursividad será el mismo que el del ABB.
> Como dentro no se realizan opearciones aleatorias dentro del árbol y, si se puede garantizar que el orden de los elementos a insertar es al azar, el árbol se espera que sea balanceado y su altura esperada será logaritmica.
> Si puedo garantizar que la aleatoridad esta fuera de la estructura, puedo asegurar lo mencionado.

### Listas Ordenadas

Queremos tener una lista de datos ordenada dinámicamente.

- Los arreglos ordenados permiten usar BS para buscar.
- El mantenimiento de un arreglo ordenado es lineal (guardar y eliminar memoria).

### Skip Lists

- Estructuras que incorporan una randomización para permitir de forma eficiente, con _alta probabilidad_, la busqueda, guardado y borrado en una lista ordenada.

Ordenes temporales promedio, con _alta probabilidad_: 1. Busqueda: $\mathcal{O}(log n)$ 2. Inserción: $\mathcal{O}(log n)$ 3. Borrado: $\mathcal{O}(log n)$

Orden espacial en el peor de los casos: $\mathcal{O}(n log n)$.

### Treaps

Treaps = Tree + Heap
Antes se habló sobre ABB y como mantienen operaciones logaritmicas siempre y cuando esten balanceados, para mantener el balanceo existen:

- AVL
- Arboles Red-Black

Estos árboles asignan una prioridad aleatoria, manteniendo la propiedad de Heap. El orden aleatorio de las prioridades mantendrá balanceado el árbol, esperando asi operaciones logarítmicas.

Cuando se inserta, se le asigna una prioridad aleatoria, se inserta como en un ABB y luego se hace una rotación. Si queremos sacar un elemento, se le asigna prioridad $-\infty$, se generan varias rotaciones y se saca el elemento.

### Arbol binario de busqueda aleatorio

Aprovecha que la altura esperada de un arbol creado en orden aleatorio es logaritmica.

> Cuando hay que hacer una inserción, por cada nodo visitado se lanza una moneda cargada con probablidad de insertar ahi o no, luego se realizan rotaciones para balancear el árbol.

- Para mantener la uniformidad _p_, cada nodo mantiene la cuenta de sus _k_ descendientes (contandose a si mismo).

- La probabilidad p en cada paso es de $\frac{1}{k + 1}$.

### Generación de números aleatorios

Se busca generar una secuencia de números que no pueda predecirse mejor que por mero azar.

#### Métodos de generación

- True Random: En base a mediciones de Hardware de procesos impredecibles.
- Pseudo Random: Algoritmos que emulan el azar mediante procesos determinísticos dado una semilla inicial.
