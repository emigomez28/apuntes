# Scheduling: Introduction

> Una política de scheduling es un algoritmo que determina cuándo un proceso debe ser ejecutado por el CPU. El objetivo principal de un algoritmo de scheduling es maximizar la utilización del CPU y minimizar el tiempo de espera de los procesos.

## Simplificaciones del workload

> El **workload** es, como su nombre indica, la carga de trabajo de un sistema.

1. Todos los trabajos corren la misma cantidad de tiempo.
2. Todos los trabajos llegan al mismo tiempo.
3. Todos los trabajos corren hasta completarse.
4. No hay I/O.
5. Cuanto tarda un proceso en completarse es conocido.

> [!NOTE] 
> Esto no se cumple en un sistema real, son simplificaciones para introducir de forma sencilla algunas políticas de scheduling.

## Métricas de Scheduling

> $T_{\text{turnAround}} = T_{\text{completion}} - T_{\text{arrival}}$

Por las simplificaciones de que el tiempo de llegada es el mimo para todos, podemos simplificar la ecuación a: $T_{\text{turnAround}} = T_{\text{completion}} $

> [!NOTE] 
> Esta métrica es de **performance**. Existen otras métricas que miden, por ejemplo, el *uso justo del cpu* (**fairness**).

### First In, First Out (FIFO)

- Es simple de implementar y junto con las simplifaciones mencionadas funciona bastante bien.

- Si tenemos los procesos A, B y C donde cada uno toma 10 segundos en ejecutar se obtiene qué: $T_{\text{turnAround}} = \frac{10 + 20 + 30}{3}$.

Ahora, sacando la restricción **(1)** el resultado de turnAround tenderá a estar mas cerca del proceso que más tiempo tarda en completarse.

### Shortest Job First (SJF)

La idea es simple, empezar por el proceso que antes termine, esperando así minimizar el turnAround total.

- En este caso, si mejora el caso anterior donde hay un proceso que tarda mucho más que otros en completarse.

- Si se relaja la restricción **(2)**, si llega un proceso muy largo antes que otros 2 muy cortos, estos últimos deberan esperar si o si (ya que el proceso más largo era el único, y por ende el más corto) por lo que se rompe la idea de minimizar el turnAround.

### Shortest Time-to-Completion First (STCF)

- La idea es muy similar al anterior, solo que puede decidir de cambiar entre un proceso y otro si encuentra uno que sea más corto para completar que el actual (para esto debe relajarse la restricción **(3)**).

## Response Time como métrica

> $T_{\text{response}} = T_{\text{firstRun}} - T_{\text{arrival}}$

> Otra métrica interesante a tener en cuenta es **response time**. Surge junto con la multiprogramación y la necesidad de interactividad, es decir, esta métrica mide cuanto tiempo tarda en volver a interactuar con el usuario.


### Round Robin (RR)

- Para solucionar el problema anterior, esta política de scheduling lo que hace es asignarle un **time slice** a cada proceso para ejecutar, es decir, no corre los procesos hasta terminarlos (a no ser que terminen dentro del time slice).


### I/O

Al incorporar I/O, lo que ocurre es que algunos procesos quedan **bloqueados** ya que deben esperar a que la operación de I/O termine. Por lo tanto, el CPU puede ser utilizado por otro proceso.



