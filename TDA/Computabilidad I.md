# ¿Que es un modelo de Computabilidad?

Objeto mátematico que permite analizar características, propiedades, capacidad y limitaciones de la computabilidad $\rightarrow$ Que es lo que puedo hacer.

## Alfabeto, Strings y Lenguaje

- Alfabeto: Conjunto de símboloes finito y no vacio.
- String: Secuencia finita de símbolos de un alfabeto.
- Lenguaje: Conjunto de strings de un alfabeto.

## Autómata Finito

1. No tiene memoria.
2. Reconoce una cantidad finita de cadenas.
3. Se encuentra en todo momento en un estado.
4. Tenemos un conjunto finito de estados.
5. Tenemos un alfabeto.
6. Tenemos una función de transición que dado un símbolo y un estado (actual), nos indique el estado siguiente.
7. Tenemos un estado inicial $Q_0$.
8. Tenemos un conjunto de estados de aceptación.

### Ejemplo de la Escalera Mecánica.

- Hay un senso que dice si alguien está en la escalera o no.
- Si hay alguien en la escalera, esta se mueve.
- Si no hay nadie en la escalera, esta se detiene.

> Este tipo de autómata es finito y determístico.

## Autómata Finito No Determinístico

- Consideramos que se pueda pasar de un estado a más de uno. Cada ramificación se computa en paralelo.

- El autómata anterior es un caso particular de este.

- $\epsilon$ es una convención para indicar que no se consume ningún símbolo.

## ¿Alguno de estos modelos tiene más poder de cómputo?

- Dos máquinas son equivalentes si ambos reconocen el mismo lenguaje

- Para todo autómata finito determinístico tenemos un atómata finito no determinístico.

### Explicación

- Podemos ver que una vez llegamos a un estado, hasta cierta lectura, todolo que siga a ese estado va a ser lo mismo, no importa de donde vine (no hay memoria).
- Podemos considerar cada posible estado ejecutandolo o no $\rightarrow$ $2^n$.
- Creamos una máquina finita determinística que tenga cada transición como equivalencia
- Los estados de finalización en la nueva máquina son aquellos estados donde los nodos de finalización de la original estarían activados.

> En resumen, no hay diferencia en el poder de cómputo entre un autómata finito determinístico y uno no determinístico.

## Lenguajes Regulares

Un lenguaje es regular si existe un autómata finito que lo reconozca.

- Se pueden aplicar operaciones a estos lenguajes $\rightarrow$ Operaciones regulares $\rightarrow$ resultado = Lenguaje Regular.

### Operaciones Regulares

- Union: $A \cup B = \{x / x \in A \and x \in B \}$
- Concatenación: $A \cdot B = \{xy / x \in A \and y \in B \}$
- Estrella: $A^* = \{x_1x_2...x_n / x_i \in A \}$

## Expresiones Regulares

El uso de operaciones regulares para obtener un nuevo lenguaje regular se lo llama **expresiones regulares**.

Una expresión es regular sí:

1. Es un elemento del alfabeto.
2. Es una cadena vacía.
3. No hay string.
4. Es una unión, concatenación o estrella de expresiones regulares.

> Podemos crear nuestros autómatas usandolos de building blocks.

## Automata de Pila

Se le agrega algo de memoria al modelo.

En cada iteración:

- Leemos algo del input, o no.
- Leemos algo de la pila, o no.
- Se modifica el estado o nos quedamos en el mismo.
- Se guarda algo en la pila o no.
- Se quita algo de la pila o no.

## Gramáticas

> Un conjunto de reglas de sustitución (derivación) que a partir de una variable se convierte en una variable y/o terminales.

- Un string del lenguaje esta conformado por terminales.
- El conjunto de los strings del lenguaje conforman la gramática.

## Lenguajes Libres de Contexto

- Gramática libre de contexto: Todos los strings del lenguaje pueden generarse por derivación.

- Un lenguaje que puede generarse por una gramática libre de contexto.

- Si hay un string que puede derivarse de más de una manera $\rightarrow$ gramática ambigua.

- Un lenguaje es libre de contexto si y solo si existe un autómata de pila que lo reconozca.

- Un lenguaje regular es un lenguaje libre de contexto.
