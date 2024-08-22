> Es una tecnica de diseño que permite resolver problemas de optimización de un sistema de ecuaciones/inecuaciones lineales en varias variables.

_Ejemplo:_
$cte_0 \cdot x_0 + ... + cte_n \cdot x_n \geq cte$

Y luego maximizar/minimizar una fórmula particular.

> **EVITAR**: $x_0 \cdot x_1 \leq 10$

## Componentes de programación lineal

1. Variables, primero continuas pero pueden ser enteras.
2. Ecaciones e inecuaciones **lineales** que definen restricciones sobre dichas variables:
   $\sum a_i \cdot x_i \geq b$

3. Buscamos maximizar/minimizar una función objetivo: max $\sum c_i \cdot x_i$.
4. Luego aplicamos un algoritmo que resuelva el modelo lineal $\rightarrow$ **Simplex**

### Ejemplo 0

Suponiendo que ya tenemos el sistema de ecuaciones:

$$
\begin{cases}
    4x_1 - x_2 \leq 8 \\
    2x_1 + x_2 \leq 10 \\
    5x_1 - 2x_2 \geq -2 \\
    x_1, x_2 \geq 0
\end{cases}
$$

Y queremos maximizar $x_1 + x_2$
![[Captura de pantalla 2024-08-19 a la(s) 12.36.02.png]]

> En problemas **lineales continuos** la solución óptima siempre se encuentra en un vértice.

### Ejemplo 1

TeoAlgoSoft vende 2 comestibles por kilo: $c_1$ y $c_2$. Por cuestiones normativas no se pueden vender $c_2$ en mas kilos que el triple de $c_1$. Al mismo tiempo, cantidad de $c_1$ con el doble de $c_2$ no puede exceder los 14kgr. Luego, la cantidad de kilos que exceda $c_1$ a $c_2$ no puede ser mayor a 2kgr. La ganancia del día puede verse por \$5/kgr del producto $c_1$ + \$3/kgr del producto $c_2$.

#### Restricciones

$$
\begin{cases}
    3x - y \geq 0 \\
    x + 2y \leq 14 \\
    x - y \leq 2 \\
\end{cases}
$$

Y buscamos maximizar $5x + 3y$
![[Captura de pantalla 2024-08-19 a la(s) 13.21.44.png]]

> Como estamos vendiendo por kilo deberían existir las condiciones $x \geq 0$ e $y \geq 0$.

> En este caso no es problema ya que estamos buscando maximizar.

> En este caso las posibles soluciones son los vertices del triangulo, como hay un vertice que nos da ganancia negativa no es una solución óptima.

> Igualmente, teniendo en cuenta las restricciones $x \geq 0$ e $y \geq 0$ podriamos utilizar mas vertices, que en este caso tampoco serían soluciones óptimas.

```python
def ejemplo():
    x = pulp.LpVariable("x")
    y = pulp.LpVariable("y")

    problem = pulp.LpProblem("Ejemplo", pulp.LpMaximize)

    problem += 3 * x - y >= 0
    problem += x + 2 * y <= 14
    problem += x - y <= 2
    problem += 5 * x + 3 * y

    problem.solve()

    return pulp.value(x), pulp.value(y)
```

### Ejemplo 2

Nuestra empersa de cereales tiene que cumplir con un pedido de al menos 100 kgr del producto estrella de la empresa para el final de la semana, para ser vendido en diferentes supermercados. Para elaborar el producto necesita de amaranto y frutos secos (misma cantidad de cada uno). Para esto contamos con dos proveedores:

1. Valle Patagua: Nos vende a \$1 el kilo de amaranto (máximo 40kgr).
2. Salud Sustentable: Nos vende cajas de 2kgr de amaranto y 5kgr de frutos secos por \$6 (máximo 30 cajas).

Buscamos un modelo para **minimizar** costos.

#### Modelo

$$
\begin{cases}
    VP \leq 40 \\
    SS \leq 30 \\
    5 \cdot SS \geq 50 \\
    VP + 2 \cdot SS \geq 50 \\
\end{cases}
$$

Y buscamos minimizar: $\$1 \cdot VP + \$6 \cdot SS$

```python
def ejemplo():
    vp = pulp.LpVariable("vp")
    ss = pulp.LpVariable("ss")

    problem = pulp.LpProblem("Ejemplo", pulp.LpMinimize)

    problem += vp <= 40
    problem += ss <= 30
    problem += 5 * ss >= 50
    problem += vp + 2 * ss >= 50
    problem += vp + 6 * ss

    problem.solve()

    return pulp.value(vp), pulp.value(ss)
```

> Por como está definido el problema la solución óptima admite valores con coma, por ejemplo si se cambian las ecuaciones

$$
\begin{cases}
    5 \cdot SS \geq 50 \\
    VP + 2 \cdot SS \geq 50 \\
\end{cases}
$$

> por lo siguiente:

$$
\begin{cases}
    5 \cdot SS \geq 51 \\
    VP + 2 \cdot SS \geq 51 \\
\end{cases}
$$

> Se obtendran resultados con decimales.

Ahora, si se quiere que los valores sean enteros se debe plantear el problema de la siguiente forma:

```python
def ejemplo():
    vp = pulp.LpVariable("vp")
    ss = pulp.LpVariable("ss", cat = "Integer")

    problem = pulp.LpProblem("Ejemplo", pulp.LpMinimize)

    problem += vp <= 40
    problem += ss <= 30
    problem += 5 * ss >= 50
    problem += vp + 2 * ss >= 50
    problem += vp + 6 * ss

    problem.solve()

    return pulp.value(vp), pulp.value(ss)
```

> Como pedir que las cajas sean enteras es una mayor restricción, esto aumentará el costo en el caso de cambiar las ecuaciones mencionadas anteriormente, de manterse el resultado no varía.

## Programación lineal entera

Hay casos donde las variables sólo pueden tomar valores enteros. Hay que indicar eso, y puede cambiar drasticamente la solución $\rightarrow$ El algoritmo para buscar la solución es diferente.

### Retomando el ejemplo 2

> [[#Ejemplo 2]].

> Ahora, como estamos usando PLE la solución óptima puede que no esté en un vertice, ya que ese par de pnutos podría tomar valores que no sean enteros.

### Utilidad de PLE

> Aparte de lo mencionado, nos permite tener "Variables booleanas/binarias" $\rightarrow$ Variables que valgan 0 o 1 $\rightarrow$ Sirven para problemas de decisión.

### Ejemplo con variables Binarias

Tenemos que decidir si lanzamos diferentes productos (tenemos 6). Se deben lanzar al menos 3. Cada producto tiene un determinado costo o ganancia (y tenemos un presupuesto):

$$
\begin{cases}
    \sum_i y_i \geq 3 \\
    z_{min} = \sum_i c_i \cdot y_i \\
\end{cases}
$$

Ahora, teniendo en cuenta nuevas restricciones:

> Si lanzamos el producto 5 o 6, debemos lanzar el producto 1 si o si.

$$
\begin{cases}
    y_1 \geq y_5 \\
    y_1 \geq y_6 \\
\end{cases}
$$

> Si se lanzan juntos los productos 3 y 4 se ahorra \$100. Para esto creamos una nueva variable llamada $y_{ahorro}$.

$$
\begin{cases}
    y_{ahorro} \leq y_3 \\
    y_{ahorro} \leq y_4 \\
\end{cases}
$$

> Otra forma de representarlo es: $y_{ahorro} \leq y_3 + y_4$

> En definitiva, esto muestra como escribir un OR o un AND en PLE.

## Generalización de OR y AND

### OR de n variables

> $Y_{or} \leq \sum_{i = 1}^{n} y_i \leq n \cdot Y_{or}$

### AND de n variables

> $n \cdot Y_{and} \leq \sum_{i = 1}^{n} y_i \leq (n - 1) + Y_{and}$

Donde n siempre es una constante para el problema.

## Resolución al problema de la mochila utilizando PL

Tenemos un conjunto de elementos a guardar en una mochila, cada uno con valor $v_i$ y peso $w_i$, no podemos excedernos en peso W. Obtener el máximo valor que podemos guardar en la mochila. (nota: $v_i$ y $w_i$ son constantes).

Definimos las variables $Y_i$ como variables booleanas que representan a los elementos a guardar en la mochila donde $\sum w_i \cdot Y_i \leq W$.
Por otro lado, la función objetivo es maximizar $\sum v_i \cdot Y_i$

### Ejemplo

```python
def mochila_pl(v, w, W):
    y = []
    for i in range(len(v)):
        y.append(pulp.LpVariable(f"y_{i}", cat = "Binary"))

    problem = pulp.LpProblem("Mochila", pulp.LpMaximize)
    problem += pulp.LpAffineExpression([(y[i], w[i]) for i in range(len(y))]) <= W
    problem += pulp.LpAffineExpression([(y[i], v[i]) for i in range(len(y))])

    problem.solve()

    return list(map(lambda yi: pulp.value(yi), y)
```

## Coloreo en Grafos

> Vamos a definir las variables del estilo $X{pais, color}$ como booleanas.

> Para hacer que cada país tenga un solo color debemos hacer: $\forall$ $pais: \sum_c X_{pais, c} = 1$

> Para hacer que dos adyacentes no tengan el color rojo: $X_{1, rojo} + X_{2, rojo} \leq 1$

> Entonces, para que cualquier par de adyacentes no tengan el mismo color: $\forall E(p_1, p_2) \in G: \forall c: X_{p_1, c} + X_{p_2, c} \leq 1$
