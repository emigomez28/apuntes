# Independent Set por PLE

> Definimos $Y_i$ que representa si el vertice i es parte del independent set o no.

> Como por como es el problema no podemos tener 2 vertices adyacentes como parte de la solucíon, entonces definimos $\rightarrow$ $\forall i, Y_i + \sum_{k \in adyacentes(i)} Y_k \leq 1 Y_k \leq 1$.

> $R = \sum_i Y_i$ $\rightarrow$ Maximizar R.

> La segunda condición tiene un problema, ya que si por ejemplo tomamos $Y_0$ podría pasar que solo uno de los adyacentes sea parte del IS sin necesidad de que $Y_0$ sea parte del IS, lo cual no es correcto. Para corregir esto utilizamos el truco de la M (Big-M).

> $\forall i, Y_i + \sum_{k \in adyacentes(i)} Y_k \leq 1 + M(1 - Y_i)$. Donde $M = |V| + 1$.

> Esta ecuación nos permite cumplir la condición sobre los adyancentes, pero si $\sum_{k \in adyacentes(i) Y_k \leq 1 + M}$ donde M es un número muy grande, esta restricción habilita a usar los vertices que sean necesarios, en cambio, si vale 0 no hace nada.

# Problema del cambio por PL

> $v_i$: valor de la moneda de denominación i. (cte).

> $C$: Cambio que queremos llegar (cte).

> $M_i$: Cantidad de monedas de denominación i que usamos para el cambio.

> $\sum_i v_i \cdot M_i = C$ $\rightarrow$ Minimizar $\sum_i M_i$.

# Problema del viajante por PLE

1. Agregamos virtualmente las aristas faltantes con peso F = "infinito" (= sumatoria de todas las aristas existentes + 1).

2. Vamos a construir nuestras variables $Y_{i, j} = 1$ si usamos la arista i $\rightarrow$ j, 0 sino.

3. $\sum_j Y_{i, j} = 1$ $\rightarrow$ de todos salgo. $\sum_i Y_{i, j} = 1$ $\rightarrow$ a todos entro.

4. Entonces, queremos minimizar $\sum_i \sum_j peso(i, j) \cdot Y_{i, j}$.

## Agregamos mas restricciones

> Como lo anterior solo no alcanza, ya que obliga a entrar y salir de todas pero no da óptimo.

5. $p_i$: número de secuencia en la cual se visito a la ciudad i. Si uso la arista i, entonces tiene que pasar que el orden de i es menor al orden de j. Donde la diferencia tiene que ser exactamente 1.

6. $p_i - p_j + n \cdot Y_{i, j} \leq n - 1$.

## Método simplex

> En un problema de PL los óptimos van a estar sí o sí en vertices del polígono que se genera.

> En el caso lineal continuo $\rightarrow$ $\mathcal{O}(n^9)$

## Branch and Bounds

> Es esencialmente backtracking, aplicando branching para ver diferentes alternativas, con bounds (restricciones) como podas.
