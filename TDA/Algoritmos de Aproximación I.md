# Estado de la situación

> P = NP o P $\neq$ NP

> Como diseñar algoritmos para problemas en los que el tiempo polinomial es probablemente inconseguible?

# Algoritmos de Aproximación

> Diseñados para correr en tiempo polinomial.

> ENcuentras soluciones que garantizan ser cercanas a la óptima.

> Se puede demostrar que encuentran soluciones cercanas a la óptima.

> La solución optima es muy dificil de calcular (tiempo exponencial $\rightarrow$ no termina nunca de ejecutar).

> Aún asi se puede demostrar la cercanía de la solución aproximada.

# Métodologías para los Algoritmos de Apriximación

> Algortimos Greedy.

> Pricing Method.

> Programación lineal, entera y mixta.

> Programación dinámica y de red.

# Problema del Balanceo de Carga

Dadas $m$ máquinas y un conjunto de $n$ trabajos, donde cada trabajo $j$ toma un tiempo $T_j$, se desea asignar el trabajo en las máquinas de forma balanceada. Dada una asignación $A(i)$ para la maquina $i$, su tiempo de trabajo es: $T_i = \sum_{j \in A(i)} t_j$.

Queremos encontrar la asignación que minimice el máximo valor de $T_i$, que tambien representa el tiempo que tardará en finalizar todos los trabajos. El problema es NP-Hard.

## Metodología 1: Greedy

1. Iterar sobre todos los trabajos.
2. Para cada trabajo, asignarlo a la maquina que menos trabajo tiene la momento.
3. Mantengo el orden de las maquinas según el trabajo que tiene.

### Análisis de la solución Greedy

> Se tiene que comparar esta solución contra la solución óptima, donde $T$ es nuestra solución y la óptima es $T*$.

> No conocemos al solución óptima.

> La mejor asignación posible sería repartir para que cada máquina haga exactamente la misma cantidad de trabajo.

> Nunca se puede terminar antes que el trabajo más largo.

> Entonces: $T* \geq \frac{1}{m} \sum_{j} t_j$. Es decir, la solución óptima es mayor o igual que hacer que todos los trabajos se distribuyan de forma perfecta.

> Por otro lado, $T* \geq t_{max}$ donde $t_{max}$ es el trabajo más largo. No puedo tardar menos que el trabajo más largo.

### Demostración de cuanto nos aproximamos

> Necesitamos comparar nuestra solución con lo que sabemos del óptimo.

> Una de nuestras máquinas en T tuvo mas carga que cualquier otra, es decir, hay una que es la última en terminar.

> Habrá habido un último trabajo $T_j$ en ser colocado en esa máquina.

> Si ese trabajo $T_j$ es bastante grande usamos $T* \geq t_{max}$. Si no, podemos acotar con $T* \geq \frac{1}{m} \sum_j t_j$.

> Como siempre asigna trabajo a la máquina con menor asignación hay forma de relacionar el tiempo total $T_i$ con $T_j$.

> $T_i - T_j$ era el menor tiempo $T_k$ para cualquier máquina k $\rightarrow$ $T_k \geq T_i - T_j$.
> ![[Captura de pantalla 2024-08-21 a la(s) 11.57.35.png]]

> Sumando todos los tiempos asignados de las máquinas: $\sum_k T_k \geq m \cdot (T_i - t_j)$. A izquierda, sumo $m$ veces y a derecha acoto por $m$.

> Entonces llegamos a: $T_i - t_j \leq \frac{1}{m} \sum_k T_k$.

> La parte de la derecha era una de las que habíamos acotado antes: $T* \geq \frac{1}{m} \sum_j t_j$.

> Entonces, por como son las direcciones de las cotas podemos garantizar qué $T_i - t_j \leq T*$

> Nos interesa acotar el valor de $T_i$ porque nos condiciona a nuestra solución T. Además, tenemos acotado $T_j$, para sacarlo de esta ecuación.

> Si sumamos $t_j$ a ambos miembros de la ec. y acotamos el lado derecho con $T*$ obtenemos qué: $T_i - t_j + t_j \leq T* + t_j$. En el miembro de la izq se cancelan los $t_j$ y en el derecho reemplazo por $T*$ ya qué $T_j \leq T*$. Entonces obtengo qué: $T_i \leq 2 T*$

> Lo que quiere decir esto es qué, si el óptimo es $T* = 8hs$, con la solución Greedy se va a cumplir qué $T \leq 2 \cdot T*$ $\rightarrow$ $T \leq 16hs$. Es decir, lo que calculé la solución Greedy no puede ser peor que el doble de la solución óptima.

> Por otro lado, ordenar cambia la cota de la aproximación. Si ordeno de menor a mayor, podría dejar para lo último un trabajo muy grande, lo cual haga que $T = 2 \cdot T*$, viendo esto parece ser "mejor" ordenar de mayor a menor.

> Si $m \geq n$ donde $m$ es la cantidad de máquinas y $n$ la cantidad de trabajos, Greedy es siempre óptimo, puesto que el menor $T_i$ será $t_{max}$.

> Si $n > m$, para estos trabajos en orden, los primeros $m + 1$ trabajos cuestan lo mismo o mas que el trabajo $m + 1$. Cualquier solución óptima debe asignar dos de esos trabajos a la misma maquina (demostrable por palomas y nidos).

> Esto implica una nueva cota: $T* \geq 2 \cdot t_{m + 1}$

> Un análisis análogo es que una de nuestras maquines en T tuvo mas carga que cualquier otra, habrá habido un último trabajo $t_j$ en ser colocado en esa máquina. Ese trabajo debe tener índice mayor o igual a $m + 1$, porque los primeros $m$ trabajos se asignan a máquinas distintas, entonces: $t_j \leq t_{m + 1} \leq \frac{1}{2} T*$.

> Retomando cotas anteriores: $T_i - t_j \leq T*$ y $t_j \leq \frac{1}{2} T*$ $\rightarrow$ $T_i \leq \frac{3}{2} T*$

# Problema de Set Cover

Tenemos un Set $U$ de $n$ elementos y un listado de $S_1, S_2, ..., S_m$ de Subsets de $U$. Un Set Cover es una colección de estos Subsets tal que la unión es U. El uso de cada Subset $S_i$ tiene nu costo $w_i$. Queremos minimizar: $\sum_{S_i \in C}w_i$.

## Metodología 1: Greedy

Como en este caso tenemos 2 variables, siendo estas el costo y los elementos a cubrir, buscamos utilizar un coeficiente.

> Si analizamos los Subsets de a uno por vez, nos interesa que el siguiente Subset a agregar a la solución sea aquel que más nos ayuda a acercarnos a la solución $\rightarrow$ seleccionar aquel que nos ayude con mayor progreso.

> La medidad de progreso debería considerar:

- El peso de los subsets.
- Los elementos que ese Subset me ayuda a cubrir de U.

> Empezando por el conjunto de Subsets vacío.

> Encontrar que Subset me conviene agregar según la proporción: $\frac{peso}{\#elem}$

> Iterar, recalculando la medida de progreso teniendo únicamente en cuenta aquellos elementos de $U$ que no se han logrado incorporar.

> Completar la iteración cuando ya no quedan elementos por cubrir.

### ¿Que tanto peor puede ser el resultado de este Algoritmo Greedy con comparación a la solución óptima?

Se puede demostrar que el peso obtenido es como mucho $\mathcal{O}(log n)$ veces el peso óptimo, para un set $U$ de n cantidad de elemetos.

De hecho, es una función $H(d)$ (la función armónica) de veces el peso óptimo como mucho, la cual es acotada por $\mathcal{O}(log n)$. Siendo $d$ el Subset más grande.

Se ha demostrado que ningún algoritmo de complejidad polinomial puede dar una mejor aproximación $\rightarrow$ $P = NP$.

# Problema de Vertex Cover

Dado un Grafo, un Vertex Cover es el mínimo Subset de los vertices tal que se cubran todas las aristas. Es decir, dada una arista al menos uno de sus vertices debe pertenecer a a la solución.

El uso de cada vértice $V_i$ tiene un costo $w_i$. Queremos minimizar: $w(S) = \sum_{i \in S} w_i$.

> Se puede reducir VC a Set Cover.

> Por lo mencionado anteriormente, la caja negra que resuelve Set Cover tambien puede resolver VC y además, la aproximación no puede ser peor que $H(d)$ veces la óptima, donde $d$ es el vértice de mayor grado. A su vez, como $H(d) \leq \mathcal{O}(log n)$ la solución no puede ser peor que $\mathcal{O}(log n)$ veces la óptima.

# Problema de Independent Set

> Relación entre VC e IS: Si tengo un VC, su complemento IS, y viceversa.

Dada la definición de Vertex Cover anterior ¿Cómo minimizamos la cantidad de vértices utilizados?

> Para ello ajustamos todos los pesos a 1 $\rightarrow$ $w_i = 1$ $\rightarrow$ El resultado me dice la cantidad de vértices que usé.

# Independent Set: Aproximación

> Aprovechamos la solución de VC aproximada para obtener una aproximación de IS.

Supongamos que tenemos un Grafo que tiene la misma cantidad de vertices que pertenecen al VC y al IS, es decir, $\frac{|V|}{2}$ pertenecen al VC y lo restante al IS, donde $|V|$ es la cantidad de vertices totales del Grafo.

Sucede que dado el margen de error de la aproximación de VC, perfectamente podría ocurrir que el VC obtenido sea exactamente $|V|$. Entonces, como el IS es el complemento de VC obtenemos que el IS aproximado es un set vacío.

# Vertex Cover: Una mejor aproximación

> La siguiente aproximación se basa en una perspectiva económica.

Pensaremos los pesos de los nodos como costos a pagar por la utilización del nodo, y cada arista deberá pagar su parte del costo para el VC encontrado.

Las diferentes aristas serían agentes dispuestos a pagar por el nodo que las cubre.

El algoritmo encuentra el VC, y los precios que cada arista paga. La sumatoria de esos precios serña el peso total de VC Aproximado.

## Métodología 2: Princing Method

> Utiliza esta perspectiva económica de precios para llegar a la solución por AProximación.

> Si las aristas son agentes de esta economía, debe haber algñun tipo de idea que establezca qué precios son acordes (fairness) y están dispuestos a pagar, y que precios serían excesivos (unfair)

### Precio acorde y excesivo

En este caso, los precios son acordes sí, para cada vértice $i$, las aristas adyacentes a este vértice no tienen que pagar más que el costo de ese vértice:

$\sum_{e = (i, j)} p_e \leq w_i$

> La métodología además nos provee una forma de medir quñe tan buena es la aproximación. Para cualquier solución $S*$, veamos si podemos ajustar la suma de los pesos utilizando:

> Entonces podemos acotar la solución. $\sum_{e = (i, j)} p_e \leq w_i$ $\rightarrow$ $\sum_{i \in S*} \sum_{e = (i, j) p_e \leq \sum_{i \in S*} w_i = w(S*)}$

> Entonces: $\sum_{e \in E} p_e \leq \sum_{i \in S*} \sum_{e = (i, j)} p_e$. Es decir, la sumatoria de todos los pesos de las aristas no puede ser mayor a la suma de los pesos de las aristas que son parte de la solución.

> Como es un vertex cover, del lado derecho voy a tener la suma de todos los pesos de las aristas, ahora, del lado izquierdo, puede pasar que tenga hasta 2 veces el peso de todas las aristas, puesto que 2 vertices pueden cubrir 2 veces a la misma arista

> Dicho esto, $\sum_{e \in E} p_e \leq w(S*)$.

## VC: Algoritmo por Pricing Method

Buscamos un algoritmo que busque la aproximación y tambié fije los precios.

> La forma de asignar los precios es Greedy

> Un vértice está pago cuando ya se alcanzó la sumatoria del peso a través de los costos de sus aristas incidentes. $\sum_{e = (i, j)p_e = w_i}$

### Asignación de precios

- Asignar todos los precios en 0.
- Mientras haya una arista uniendo 2 vértices que no esten "pagos".
  - Seleccionar esa arista.
  - Incrementar el precio de esa arista dentro de un precio acorde, no excesivo.
- La solución $S$ serña el set de todos los vértices "pagos".

> Como el algoritmo no deja de iterar hasta que cada arista tenga al menos uno de sus extremos completamente pagos, esto hace que encuentre un VC válido.

## Demostración: Cuanto nos aproximamos

En nuestro análisis económico postualmos que las aristas son agentes de esta economía, debe haber algún tipo de idea que establezca precios acordes (fairness).

No parece justo ("fair") que una arista pague por ambos de sus vértices. Pero ese "unfairness" es acotada, como mucho a cada arista se le cobra 2 veces. Esto también acotará que tan lejos estamos de la solución óptima.

$\sum_{e = (i, j) p_e = w_i}$ $\rightarrow$ $w(S) = \sum_{i \in S} w_i = \sum_{i \in S} \sum_{e=(i, j) p_e}$

Ahora, como mucho una arista puede ser incluida en el miembro derecho de la ec. 2 veces, entonces:

$w(S) = \sum_{i \in S} \sum_{e=(i, j) } p_e \leq 2 \cdot \sum_{e \in E} p_e$.

Con esto ya estamos en condiciones de comparar contra el óptimo.

$\sum_{e \in E} p_e \leq w(S*)$ $\rightarrow$ $w(S) \leq 2 \cdot \sum_{e \in E} p_e \leq 2 \cdot w(S*)$

> En definitiva, la aproximación es una 2-Aproximación.

## Métodología 3: Programación Lineal

### PL como problema de decisión

Como problema de decisión, podemos plantear decidir si los valores de x pueden satisfacer todas las inecuaciones definidas, y que el objetivo a minimizar sea menor a cierto valor.

> Tenemos algoritmos par resolver este problema en tiempo polinomial

### Vertex Cover: PLE

> Misma definición que antes.

> Definimos una variable binaria $x_i$ para cada vértice $i$. Para una arista entre vertices $x_i$ y $x_j$ tiene que estar al menos uno de los 2.

> Entonces proponemos qué: $x_i + x_j \geq 1$.

> Buscamos min $\sum_{i \in V} w_i \cdot x_i$.

### VC: Algoritmo por Programación Lineal

Ahora intentamos resolver el problema, donde las variables pueden tomar cualquier valor entre 0 y 1.

> A las restricciones de antes agregamos: $1 \geq x \geq 0$.

Esta metodología siempre encuentra un valor de la función objetivo mejor o igual a la solución óptima de Vertex Cover. $w_{PL} \leq w(S*)$. Esto se debe a que tiene mas grado de libertad en sus variables.

Igualmente, así como está no soluciona el VC, es decir, que $x = 0.3$ ¿que significa?

La solución trivial es redondear, es decir, si $x = 0.3$ redondeamos a 0, si $x = 0.7$ redondeamos a 1. El criterio que se usa para redondear es que el coeficiente sea mayor o igual a $\frac{1}{2}$, esto se debe a que la restricción $x_i + x_j \geq 1$ representa que se use al menos uno de los vertices de la arista, por lo qué $x_i \geq \frac{1}{2}$ o $x_j \geq \frac{1}{2}$, o ambos cumplen la condición.

Llamamos $x_j* \geq \frac{1}{2}$ a la solución redondeada.

### Demostración: Cuanto nos aproximamos

Podemos relacionar el valor del Vertex Cover redondeado con el valor obtenido por PL. PL paga $\frac{1}{2}$ o mas por $w_i$, la solución $S$ paga $w_i$.

$w_{LP} \cdot w^t \cdot x* = \sum_i w_i x_i^* \geq \sum_{i \in S} w_i x_i^* \geq \frac{1}{2} \sum_{i \in S} w_i = \frac{1}{2} \cdot w(S)$.

Como mucho, la solución de PL es el doble de la solución óptima. Por otro lado, la teníamos acotada respecto al óptimo ya qué $w_{LP} \leq w(S*)$

Nuevamente logramos una 2-Aproximación.

### Problema de Scheduling

Tengo un aula/sala donde quiero dar charlas, las charlas tienen horario de inicio y fin. Quiero utilizar el aula para dar la mayor cantidad de carlas.

> En nuestros intentos de hacerlo Greedy surgieron al menos 2 soluciones:

    - Óptina: Ordenar por horario de fin. Siempre seleccionar la que termina antes sin superponerse.
    - No Óptima: Siempre seleccionar la charla mas corta sin superponerse.

Con esto, vamos a ver cuanto nos aproximamos.

### Demostración: Cuanto nos aproximamos

> El problema de elegir el más corto: Quizas hay otros dos más largos que van mejor ¿Habrá una peor situación?

> Una charla de la solución Greedy solo puede chocar con 2 charlas que pertenecen a la solución óptima, puesto que si fueran 3 necesariamente necesito una charla mas corta, que sería parte de la solución elegida.

> Demostrar: Un intervalo de la solución A choca como máximo con 2 intervalos de la solución óptima O.

> Cada intervalo en O puede estar también en A, o choca con un intervalo en A.

Contemos todos los intervalos de O según su relación con A (dentro o chocando): - el intervalo está dentro de A: 1. - el intervalo choca con A: 2.

Entonces, en el peor de los casos, el conjunto A tiene la mitad de los elementos que O, entonces es una 2-Aproximación.
