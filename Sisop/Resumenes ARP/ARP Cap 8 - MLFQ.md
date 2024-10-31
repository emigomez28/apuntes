# Scheduling: The Multi-Level Feedback Queue

- La MLFQ busca resolver 2 problemas, por un lado optimizar el $T_{\text{turnAround}}$ y por otro hacer que el sistema se sienta lo más interactivo posible, es decir, minimizar $T_{\text{response}}$.


## Reglas

- Una MLFQ tiene distintas colas numeradas con distinto nivel de prioridad.

1. Si Prioridad(A) > Prioridad(B), A se ejecuta antes que B.
2. Si Prioridad(A) = Prioridad(B), A y B se ejecutan por RR.

Lo que hace MLFQ es *aprender* de los procesos que ya corrieron para predecir el futuro. Esto lo logra manejando las prioridades según sus operaciones. Por ejemplo, si un proceso es CPU intensive, mantendrá su prioridad baja, por otro lado, si un proceso realiza mucho I/O, su prioridad subirá o se mantendrá alta, para que el OS se sienta interactivo.

![Ejemplo de MLFQ]("img/mlfq_arp_cap_8.png")

## Criterios de manejo de prioridad

3. Cuando un job entra al sistema, se posiciona con la priordad mas alta (la cola con mayor prioridad)

4. (a) Si un job usa todo un time slice mientras corre, su prioridad se reduce (se mueve de una cola de mayor prioridad a una de menor) $\rightarrow$ Si usa todo un time slice, significa que puede ser CPU intensive lo que aumenta $T_{\text{response}}$.

4. (b) Si un job devuelve la CPU antes de que su time slice termine, mantiene su prioridad $\rightarrow$ Si termina antes, significa que mantiene o no altera de forma significativa $T_{\text{response}}$.

Si bien esto funciona, el problema es que si hay muchos jobs que realizan I/O (interactive jobs) aquellos que sean CPU intensive no serán ejecutados nunca (starvation). Para ello se agrega la siguiente regla.

A su vez, los jobs durante su ejecución pueden pasar de CPU intensive a interactive.

5. Despues de un período de tiempo **S**, se crea un boost de prioridad. Por ejemplo, subir a todos los jobs a la cola de prioridad mas alta.

Otro problema a tener en cuenta es que se puede jugar con los procesos, de forma que usen I/O para mantener su prioridad alta y monopolizar el uso del CPU, para resolver esto se actualizan las reglas 4a y 4b en una sola:

4. Una vez un proceso usa todo su time slice en un nivel de prioridad, su prioridad debe bajar.

## Resúmen de manejo de prioridades

1. Si Prioridad(A) > Prioridad(B) $\rightarrow$ Se ejecuta A.
2. Si Prioridad(A) = Prioridad(B) $\rightarrow$ Se ejecutan por RR usando el time slice de la cola.
3. Si un job entra al sistema, se posiciona en la cola de mayor prioridad.
4. Si un job usa todo su time slice, su prioridad baja sin importar cuantas veces haya renunciado al CPU.
5. Despues de un tiempo **S**, se hace un boost de prioridad, donde todos los procesos pasan a la cola de prioridad mas alta.
