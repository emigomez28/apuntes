# Programación Dinámica y Redondeos

Hay casos donde las aproximaciones son muy buenas pero no son suficientes. Ejemplo: Set Cover, la 2-Aproximación de Vertex Cover.

Igualmente, se siente estar muy lejos de la solución óptima. Se puede utilizar una técnica donde el error es un número entero arbitrario (distinto que 0 $\rightarrow$ la complejidad se va a exponencial) que tiene su motivación en Programación Dinámica para dar aproximaciones arbitrariamente buenas.

### Esquema de Aproximación en Tiempo Polinomial (PTAS)

- El nivel de Aproximación alcanzable es parametrizable por $\epsilon$, es decir, la aproximación será una $1 + \epsilon$ Aproximación.

## El problema de la Mochila

- El problema es NP-Difícil. $\rightarrow$ Cuando se habla de NP-Difícil, se habla de problemas de optimización.
- Tenemos una mochila con capacidad $W$. Hay elementos a guardar. Cada elemento tiene un peso $w_i$ y un valor $v_i$. Queremos maximizar el valor de lo que llevamos sin excedernos de la capacidad.
- Se agrega un parámetro de aproximación $\epsilon$, medida de precisión.
- La solución tiene que entregar los elementos que no superen en peso a W, y tenga una suma de valores como mucho $1 + \epsilon$ de distancia al óptimo.
- El algoritmo corre polinomialmente para un valor fijo de $\epsilon$, pero muestra crecimiento exponencial a medida que disminuye $\epsilon$.

- Nuestra solución es Pseudo-Polinomial, ya que depende de $W$. $\mathcal{O}(n \cdot W)$.
- Se puede obtener una solución rápida si los valores son pequeños, incluso si $W$ y los pesos son valores muy grandes.
- Si los valores son muy grandes y tienen mínimo común múltiplo podemos dividir a todos los valores por ese número y obtener mayor performance.
- La conclusión será qué: $\mathcal{O}(n² \cdot v^*)$ donde $v^* = max_i v_i$

### Mochila aproximada.

- Se puede decir que si los pesos son pequeños el algoritmo es polinomial.
- Si los valores son muy grandes, podemos dividirlos por algún valor y luego redondear.
- Forzamos a que todos los números sean múltiplos de un valor arbitrario y aumentamos el valor oríginal $\rightarrow$ si se redujiece, podríamos estar guardando elementos que no entren en la mochila.
- $\hat{V_i} = \frac{(V_i \cdot b)}{b}$, donde $V_i \leq \hat{V_i} \leq V_i + b$.
- Encontrar una solución con los valores re-escalados al dividir por b es sinónimo de encontrar una solución al problema real.
- Asumimos que $\epsilon^{-1}$ es un valor entero.

### Algoritmo de Aproximación

1. Definimos $b = \frac{\epsilon}{2 \cdot n} \cdot maxv_i$
2. Encontar el problema con los valores re-escalados aproximadamente.
3. La solución aproximada al problema original es el conjunto de elementos escontrados por la solución (que es óptima) del problema redondeado.

> Todo lo que tenga que ver con pesos no se toca, solo los _valores_.

- La idea del algoritmo es en cada paso buscar la mochila de menor tamaño para llegar a un valor objetivo, sin tener en cuenta el peso.
- Guardamos en OPT(i, V) usando a lo sumo los primeros i elementos y para alcanzar (al menos) el valor V, la capacidad mas baja de W lo logra.
- El tamaño de la matriz para aplicar memoization en este problema es de tamaño $\mathcal{O}(n^2 \cdot v^*)$

$OPT(i, V) = min
\begin{cases} 
    \text{no usar el elemento: } & OPT(n-1, V) \\
    \text{usar el elemento: } & P_i + OPT(n-1, max(V - v_i, 0)) \\
\end{cases}$

```python
def mochila(valor, peso, N, W):
    T = sum(valor)
    opt = [[0 for _ in range(T+1)] for _ in range(N+1)]

    for i in range(0, N+1):
        opt[i][0] = 0

    for i in range(1, N+1):
        sum_valor = sum(valor[:i])
        for V in range(1, sum_valor):
            if V > sum_valor - valor[i]:
                # El elemento tiene que ser parte de la solución si o sí.
                # Sin este elemento no puedo llegar a V.
                opt[i][V] = peso[i] + opt[i-1][max(V - valor[i], 0)]
            else:
                opt[i][V] = min(opt[i-1][V], peso[i] + opt[i-1][max(V - valor[i], 0)])
    # Esto es si y solo si opt[N][V] <= W, sino debo buscar en toda la matriz el mayor que cumpla esto
    return opt[N][W]
```

- Retomando el valor de $b = \frac{\epsilon}{2 \cdot n} \cdot max v_i$, el mayor valor $v^*$ será entonces $max_i \hat{v_i} = \hat{v_j} = [\frac{v_j}{b] = n \cdot \epsilon^{-1}}$.
- Reemplazando obtenemos que la complejidad del algoritmos es: $\mathcal{n^3 \cdot \epsilon^{-1}}$.
- Como el algoritmo es semi-polinomial, la complejidad depende de $\epsilon$. Con esta aproximación, la solución aproximada diverge un $\epsilon$% de la solución óptima.

## Ejercicio: Maximizar sumatoria

Dado un límite W y un conjunto de valores enteros V, se busca un subconjunto de V que maximice la sumatoria de valores sin exceder el valor W.

### Solución Greedy

Agregar "como vienen", siempre y cuando no exceda a W. $\rightarrow$ es una W-Aproximación. Ej: [1, 100] y W = 100, la solución será 1, una W-Aproximación.

### Solución de 2-Aproximación

- Ordenar los valores de mayor a menor. $\mathcal{O}(n log n)$

### Solucion 2-Aproximación en $\mathcal{O}(n)$

Idem al primer greedy, itero y guardo siempre que los valores sean menores a W. Va a llegar un punto donde el elemento $a_j$ exceda la sumatoria W. En ese momento tenemos
una sumatoria que no excede a W y un elemento $a_j$ que tampoco lo excede. Debe cumplirse entonces que o bien la sumatoria sin incluir a $a_j$ es mayor a $\frac{W}{2}$ o bien $a_j$ es mayor a $\frac{W}{2}$. La condición que se cumpla será mi solución.

```python
def maximizar_sumatoria(V, W):
    sumatoria = 0
    for i in range(len(V)):
        if sumatoria + V[i] <= W:
            sumatoria += V[i]
        else:
            if sumatoria > W/2:
                return sumatoria
            if V[i] > W/2:
                return V[i]
    return sumatoria
```
