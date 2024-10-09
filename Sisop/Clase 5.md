# Scheduling

## Ejercicio de práctica

Se tine un scheduler MLFQ, de 3 nivles de prioridad y un timeslice de 20ms. Asumir que no usa boost (regla 5). Existe un programa A en el sistema que hace un uso constante del CPU (nada de I/O) y que inició hace mucho tiempo.

Se quiere ejecutar un programa B que realiza el siguiente ciclo de ejecucición:

1. Utiliza el CPU para un cálculo de 15ms.
2. Accede a I/O y se bloquea 1ms
3. Utiliza el CPU un total de 65ms.

¿Cuanto tarda el programa en terminar (turn around)?

## Repaso de MLFQ

1. Si Prio(A) > Prio(B) -> A se ejecuta (B no se ejecuta)
2. Si Prio(A) = Prio(B)

## Esquema de un Scheduler

```c
void scheduler() {
    while (true) {
        struct proc *candidate = select_next();

        if (candidate != NULL) {
            candidate->status = RUNNING;
            candidate->runtime += switch(candidate);
        } else {
            idle();
        }
    }
}
```

- Ejemplo de parcial: Implementar un Scheduler que elija un proceso al azar para correr.

```c
struct proc *select_next() {
    int i = rand() % NPROC;
    if (&ptable.proc[i].status == RUNNABLE) {
        return &ptable.proc[i];
    }
    return NULL;
}

void scheduler() {
    while (true) {
        struct proc *candidate = select_next();

        if (candidate != NULL) {
            candidate->status = RUNNING;
            candidate->runtime += switch(candidate);
        } else {
            idle();
        }
    }
}
```

El problema con esta forma de seleccionar es que genera mucho idle time, por lo que no siempre elije un proceso runnable.

- Otra solución para elegir el siguiente es:

```c
struct proc *select_next() {
    int next_rnd = -1;
    struct proc *next = NULL;

    for (int i = 0; i < NUMPROC; i++) {
        if (proc[i].status == RUNNABLE) {
            int random = rand();
            if (random > next_rnd) {
                next_rnd = random;
                next = &proc[i];
            }
        }
    }
    return next;
}
```

En este caso, si bien la elección del proceso es aleatoria, no tiene idle time, por lo que de forma aleatoria se garantiza que siempre se elige un proceso runnable.

## Scheduler caso de estudio: Linux

### Completely Fair Scheduler (CFS)

> Fairness: Si hay n procesos cada proceso se ejecutaría en el procesador todo el timpo a $\frac{1}{n} de la potencia de este.$

En la práctica, el procesador se ejecuta cada tarea durante $\frac{1}{n}$.

El CFS busca ofrecer a cada proceso el mismo tiempo de CPU basándose en el concepto de equidad.

### Algoritmo de selección

Cada vez que tenga que elegir un proceso, el scheduler elige siempre el que esté más atras en el vruntime consumido.

### Principios básicos del CFS

> El reloj de los procesos con más prioridad va a ir mas lento.

Como consumen menos vruntime van a ser seleccionados mas frecuentemente. En tiempo real si van a tener más pesos, pero en vruntime van a ser iguales.

El cálculo de runtime se calcula dividiendo por el peso. $vruntime += \frac{delta_exec}{weight}$

### Runqueue

Una **runqueue** es una cola de procesos que están listos para ejecutar. En linux esta implementado con un **red-black-tree**.

### Funcionamiento del CFS

1. **Seleccion de Proceso**: El scheduler siempre intenta ejecutar el proceso con **menor vruntime**, de esta forma asegura que todos los procesos tengan la opprtunidad de ejecutarse de manera justa.
2. **Uso de Red-Black Trees**: Para organizar eficientemente los procesos por su vruntime.
3. **Sleeping and Waking Up**: Cuando un proceso se bloquea, se lo saca de la runqueue y se lo pone en una cola de bloqueados. Cuando se desbloquea, se lo vuelve a poner en la runqueue.
4. Balance de Carga: Se encarga de equilibrar la carga de trabajo entre múltiples CPUs.
5. Soporte para Multi-threading: Maneja los hilos de ejecución de manera similar a como maneja los procesos. Cada hilo es tratado como un proceso separado con su propio vruntime.

### Linux: Nice

El valor **nice** es una característica del SO Linux que se usa para ajustar la prioridad de los procesos en cuanto a su planificación para el uso del CPU.

### Funcionamiento de Nice

- **Rango de valores**: El valor de nice puede variar entre -20 y 19, siendo -20 el mas favorable.

**Peso de Prioridad (Weight)**: Cada proceso tiene un peso asignado basado en su prioridad de nice. Estos valores de nice se mapean.

# Threads

- Proceso: Un programa en ejecución.

- Thread: Un hilo de ejecución de un proceso.

Un programa es visto como threads ejecutándose.

## Dificultades de la concurrencia

- Integilibilidad.
- Predecibilidad.
- No-Determinismo.

## Threds en Linux

Esta abstracción tiene:

- Thread ID.
- Valores de registro
- Stack propio
- Una política y prioridad de ejecución
- Un propio errno
- Datos específicos del thread

> Un thread es **scheduleable**.

## Thread Scheduler

> En linux el scheduler de threads es el mismo que el de procesos.

## API de Threads en Linux

### Crear un Thread

```c
#include <pthread.h>
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);
```
