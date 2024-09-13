# Máquina de Turing

- Pensada para el problema de decisión.

- Modelo que parte del AF, pero con memoria infinita.

- Tiene una cinta infinita de celdas, con el input inicialmente en la cinta y el resto en blanco.

- En cada iteración puede leer o grabar la secuencia. Puede moverse a izquierda o derecha en la cinta.

- Como el AF tiene una cantidad finita de estados

- Hay estados especiales llamados Halting (aceptación o rechazo) que podría nunca alcanzar.

## Cómputo de una TM

- Un lenguaje es Turing reconocible si y solo si existe una TM que la reconozca.

El cómputo de ua TM puede terminar con:

- Aceptación
- Rechazo
- Loop infinito

> Decidor: TM que nunca loopea, no importa el input

- Un lenguaje es Turing decidible si y solo si existe una TM que lo decida.

## Complejidad Temporal par TM.

Se define que M es una f(n)-Maquina de Turing si tiene complejidad $\mathcal{O}(f(n))$.

## Modelos Polinomialmente Equivalentes

Todos los modelos vistos tienen una capacidad menor o igual a TM.

Se dice que un modelo es equivalente a otro si puedo transformar de uno a otro incrementando el tiempo de ejecución en un tiempo polinomial.

## Clases de Complejidad

- P: Corresponde a los lenguajes que son decidibles en tiempo polinómico usando una **Maquina de Turing Determinística**.
- NP: Corresponde a los lenguajes que son decidibles en tiempo polinómico usando una **Maquina de Turing No Determinística**.

## Funciones Computables

Una función es computable si existe una TM tal que finaliza su ejecución sólo con f(w) en su cinta, para cualquier w.

# David Hilbert

Plantea 23 problemas a resolver durante el siglo, uno de ellos es:

> Encontrar un algoritmo que dada una ecuación con 2 o más incognitas y coeficientes enteros, determine si hay solución entera.

Otro problema es:

> Existe un algoritmo/maquina que dada cualquier fórmula de cálculo de primer orden nos indique si es un teorema (si es válida)?

Turing propone su máquina automática y Church el cálculo lambda, donde Turing demuestra que son equivalentes.

## Tesis de Church-Turing

Algo es un algoritmo si y solo si es equivalente a una máquina de Turing.

Solo se puede generar un algoritmo a un problema si y solo si existe una máquina de Turing que reconozca el lenguaje del problema.

### El problema de antes pero mas sencillo

Si trabajamos con una sola variable, podemos plantear un polinomio genérico. Este problema es infinito y podría no terminar núnca.

#### Se puede acotar el rango

Se demostró qué si hay raiz entera, se encunetra si o si con: $|x| \leq \frac{|c_{max}| \cdot k}{|c_1|}$

Gracias a esto, ahora el problema es Turing decidible.

### Volviendo al problema original

Con mas de una variable, es Turing Reconocible pero no es Turing Decidible.

## Maquina de Turing Universal

Una TM que puede simular cualquier otra TM, parte del input es otra TM.

## Halting Problem

¿Existe una TM que agarre otra y diga si esta termina (decide, no loopea) para todo input?

### Demostracion que Halting Problem no es Turing Decidible

- Asumimos que dicha maquina existe y la llamamos $H$, donde $H(<M, w>)$ acepta si $M$ acepta $w$ y rechaza en el caso contrario, si $M$ rechaza o loopea.

- Definims a $D$ como una TM que usa $H$, entonces, $D<M> = H<M, <M>>$, donde acepta si $M$ no acepta a $<M>$ y rechaza si acepta a $<M>$

- Si aplico $D$ a si misma obtenemos qué: $D<D>$ acepta si $D$ si no acepta a $<D>$ y rechaza si $D$ no acepta a $<D>$

- Como $D$ no puede aceptar ni rechazar es absurdo.

## Algunos problemas no computables

- Halting Problem.
- Post Correspondence Problem.
- Wang Tiles.
- El juego de la vida (dados 2 patrones, definir si partiendo de uno puede que el segundo aparezca en alguna generación).
- 10mo problema de Hilbert.

## Lenguajes Turing No Reconocibles

### Tamaño de los infinitos

Cantor dice que los conjuntos infinitos tambien se pueden contar.

Planteo que dos conjuntos son correspondientes si existe una función biyectiva que mapee elementos de A a B.

Entonces, 2 conjuntos infinitos tienen mismo tamaño si puedo plantear una correspondencia.

- Hay misma cantidad de naturales que de primos y racionales, es decir, esos infinitos son iguales

- Esto no aplica para los reales, hay más reales que naturales, entonces su infinito es mayor.

- Supongamos una secuencia binario infinita, esta es mapeable a los reales y por ende es incontable.

- Planteamos la UTM, donde TM era un parametro y lo podemos plantear como un string en un alfabeto con n símbolos, entonces con k caracteres puedo reprensetar $n^k$ TMs.

- Con esto, podemos hacer una correspondencia con los naturales.

Entonces las TMs son contables. Si a cada **problema** lo puedo representar como un binario infinito, ese infinito no es contable.

- Hay mas problemas que TMs que puedan reconocerlo.
- Hay problemas que no son Turing Reconocibles.
