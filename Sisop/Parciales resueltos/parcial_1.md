# Memoria virtual

## ¿Que es la memoria virtual?
- La memoria virtual es la abstracción que provee el SO sobre la memoria física. Esta abstracción permite la multiprogramación, es decor, que el SO puede tener en memoria más programas que la cantidad de memoria física disponible.

Para esto, el SO cumple un rol de `ilusionista` en el cual *hace creer* a los procesos que tienen toda la memoría física disponible para usar, cuando en realidad no es así.

Para poder realizar esta abstracción el SO debe proveer:

1. **Transparencia:** Los procesos no deben notar que es lo que está ocurriendo y no deben cambiar nada de su implementación debido a esto.

2. **Eficiencia:** El SO debe ser capaz de realizar esta abstracción de forma eficiente, es decir, que no debe ser costoso en términos de tiempo y recursos.

3. **Protección:** El SO debe ser capaz de proteger a los procesos de otros procesos y del mismo SO. Es decir, que un proceso no pueda acceder a la memoria de otro proceso.

## ¿Que mecanismos conoce, describa los 3 que le parezcan mas relevantes?

### Base and Bounds

- **Base and Bounds** es un mecánismo en el cual se divide a la memoria utilizando dos registros de procesador, el registro `base` y el registro `bound`. 

- El registro `base` indica la dirección de inicio de la memoria del proceso y el registro `bound` indica el tope de la memoria del proceso. De esta forma, cuando un proceso intenta acceder a una dirección de memoria, el SO verifica que la dirección de memoria esté dentro del rango de memoria del proceso, y de no hacerlo lanza un error.

- Si bien esta forma no es muy eficiente, es sencilla de implementar.

### Segmentación

- La idea de segmentación es muy similar a **Base and Bounds**. En particular se puede notar que un proceso puede estar compuesto de un stack, un heap y un código (los datos que lo componen). Por lo que se puede dividir la memoria en segmentos, cada uno correspondiente a una parte del proceso. 

- Es decir, el procesador debe proveer 3 pares de registros de tipo base and bounds para poder implementar segmentación, donde cada par contiene el rango válido de direcciones de memoria para el stack, el heap y el código/texto.

- Para saber que segmento se debe usar, se toma los primeros 2 bits para indicar el segmento y los demás como un offset.

- Hoy en día se sigue utilizando, aunque cada vez menos. El problema que tiene la `segmentación` es la **fragmentación externa** ya que pueden quedar espacios contiguos libres que no sean lo suficientemente grandes como para asignarlos a un segmento. Si bien hay formas de resolver este problema (**compactación**) esta operación es muy costosa.

![Imagen fragmentación externa](img/frag_externa_p1.png)

### Memoria paginada de nivel 1

- En este macanismo se divide la memoria en bloques de tamaño fijo (suelen ser de 4kb). Para este mecánismo se le debe indicar a la **Memory Management Unit**(MMU) donde se encuentra la **Page Table** donde se encuentra la información para traducir las direcciones virtuales en físicas.

- La traducción consta de tomar los 20 bits más altos (teniendo direcciones de 32 bits y págines de 4kb) para indicar la página, luego los 12 restantes son el offset dentro de la misma.

- Si bien este mecanismo resuelve los problemas anteriores de fragmentación de la memoria, por otro lado trae un nuevo problema. Al tener que guardar la Page Table, esta constará de un arreglo de $2^{20}$ entradas por proceso, lo cual es muy costoso.

> [!NOTE]
> La MMU es un componente del procesador que se encarga de traducir las direcciones virtuales a direcciones físicas.

## Explicar el mecanismo de address translation **memoria virtual paginada** de tres niveles de indirección de 32 bits. Indique la cantidad de direcciones de memoria que provee la virtual address de la imágen con tablas de registros de 4 bytes.


El esquema de **memoria virtual paginada de tres niveles** es el siguiente:

![Esquema memoria paginada de 3 niveles](img/esquema_memoria_paginada_p1.png)

Los primeros 20 bits indican las tablas mientras que los últimos 12 el desplazamiento dentro de la página.

- La cantidad de direcciones de memoria que provee es de $2^{32}$ ya que en el primer y segundo nivel se tendrán $2^{7}$ entradas, y en el tercero $2^{6}$

# Scheduling

## ¿Que es un **context switch**? En un context switch cuales de las siguientes cosas deben/no deben ser guardadas y y porqué: 

1. Registros de proposito general
2. Translation lookaside buffer (TLB)
3. Program counter
4. Page Directory Entry (PDE)
5. Process Control Block Entry (PCB)

- Un **context switch** es el mecanismo que realiza un SO cuando decide dejar de correr un proceso para correr otro. Para ello, el SO debe guardar el estado del proceso actual y cargar el estado del proceso siguiente. Dentro de la lista mencionada, se deben guardar:

1. Registros de proposito general ya que son los registros que estaba utilizando el proceso antes de realizar el cambio de contexto, por lo que para volver al estado anterior necesariemente necesita guardarlos y recuperarlos.

2. No es necesario ya que es interno del MMU, por lo que no es necesario guardarlo en un context switch.

3. El program counter es neceesario guardarlo ya que indica la proxima instrucción a ejecutar, por lo que, de no guardarlo, la ejecución que estaba llevando a cabo el proceso se rompería.

4. El PDE es necesario guardarlo ya que cada proceso tiene su propio address space y la información traducida se encuentra ahí.

5. Guardar el PCB es necesario, ya que de no hacerlo se perdería toda la información anterior.

> [!NOTE]
> Un **context switch** tambien se da dentro del pasaje de `modo usuario` a `modo kernel` (y viceversa) dentro de un SO. Este se da cuando desde el espacio de usuario se necesita acceder a kernel land (por ejemplo para ejecutar una **syscall**), para ello, se cede el control al kernel y luego se vuelve al espacio de usuario recuperando toda la información antes del cambio.

## Explique la política de scheduling **MLFQ** detalladamente. Sea 1 proceso, cuyo tiempo de ejecución total es de 40 ms, el time slice por cola es de 2ms/c pero el mismo se incremente en 5ms por cola. Cuantas veces se interrumpe y en que cola termina su ejecución.

- La política de scheduling **Multi-Level Feedback Queue** consiste en tener distintos niveles de colas de prioridad (usualmente 8) para poder dar un orden a los jobs. La política de scheduling cumple los siguientes requerimientos:

1. Si Prioridad(A) > Priordad(B) entonces A se ejecuta antes que B.
2. Si Prioridad(A) == Prioridad(B) entonces A y B se ejecutan por Round Robin.
3. Todos los procesos empiezan en la cola de prioridad mas alta.
4. Si un proceso se ejecuta por un tiempo mayor al time slice, entonces su prioridad disminuye.
5. Cada cierto tiempo se incrementa la prioridad de todos los procesos, yendo estos a la primer cola de prioridad (la de mayor prioridad).

- Esta política de scheduling busca minimizar tanto $T_{\text{turnAround}}$ como $T_{\text{response}}$ haciendo que el SO se sienta interactivo mientras que se mantiene el intervalo de tiempo en el que un proceso es cargado hasta que este finaliza su ejecución. 

- Esto lo logra dandole mas prioridad a aquellos jobs que son interactivos y terminan antes (muy similar a la política **Shortest Job to Finish**) mientras que disminuye la prioridad de aquellos que requieren mas tiempo de CPU (CPU intensive) y aumentando la prioridad de todos cada cierto tiempo para evitar **starvation** (la prioridad de un proceso es tan baja que no llega a ejecutarse nunca, los procesos mas interactivos monopolizan el CPU) 

![Gráfico de MLFQ](img/ejercicio_mlfq_p1.png)

- Como se puede ver en el gráfico, el proceso se interrumpe 4 veces y termina de ejecutarse en la cola de prioridad 5.

# Procesos

## ¿Que es el **stack**? Explique el mecanismo de funcionamiento del stack para x86 de la función detallada abajo. Como se pasan los parámetros a la función, dirección de retorno, etc. 

```c
int read(void *buff, size_t len, size_t num, int fd);
```

- El **stack** es una región del espacio de dierecciones el cual cumple el rol de una pila. Utilizando las intrucciones (en x86) `pop` y `push` se pueden interactuar con la misma, sacando y agregando elementos a la misma respectivamente.

- Para esto existe un registro llamado `stack pointer` que indica la dirección de memoria en la que se encuentra el tope de la pila, por lo que al apilar se incrementa el valor de este registro en 4 y se decrementa con el mimso valor al desapilar.

- Para pasar los parámetros en la función se siguen las `calling conventions` de la arquitectura x86. Para este caso es:

1. Se pushean al stack los registros `caller saved` (eax, ebx, ecx, edx), los registros de uso general de la arquitectura.

2. Se pushean los parámetros en orden inverso (fd $\rightarrow$ num $\rightarrow$ len $\rightarrow$ buff).

3. Se ejecuta la instrucción `call` que pushea la dirección de retorno y realiza un `jmp` a la función para ejecutarla.

- Luego de esto, la función `read` debe asegurarse de mantener el estado del stack antes de retornar, para ello debe:

1. Guardar los registros `callee- saved` y restaurarlos antes de la intrsutrcción `ret`.

2. Manipular la pila con push y pop para poder acceder a los parámetros y dejar la pila en el estado anterior.

- Luego, al ejecutar la instrucción `ret` se hace un `pop` de la dirección de retorno y se vuelve a la rutina llamadora con el stack en el estado antes de llamar.

## ¿Que es el **Address Space**? ¿Que partes tiene? ¿Para que sirve? Describa el/los mecanismos para crear procesos en unix, sus syscalls, ejemplifique.


- El **Address space** es una abstracción que provee el SO el cual hace referencia a, como su nombre indica, el espacio de direcciones de memoria. Esta abstracción sirve para *hacer creer* a los procesos que tienen disponible toda la memoria física, cuando no es así.

- El address space se compone de 4 partes:

1. **text**: Región donde se encuentra el código.
2. **data**: Constantes globales.
3. **heap**: Memoria dinámica que el proceso le pide al SO.
4. **stack:** Región de memoria que se comporta como una pila.

- El address space sirve para poder aislar a los procesos y proteger la memoria.

- Para poder crear un procceso en sistemas Unix el SO se encarga de fabricar el espacio de direcciones con las 4 regiones mencionadas copiando y mapeando el código y las constantes a las secciones text y data resepectivamente, y reservando memoria tanto para el heap y el stack.

- Las syscalls involucradas para crear un proceso son `fork()` y `exec()`.

- Mientras que la primera se encarga de copiar un proceso (datos, heap, stack y file descriptors) y crear uno nuevo a partir de lo copiado. Se los suee diferenciar entre proceso padre e hijo. 

- Devuelve un process id (PID), en caso de ser el hijo retorna 0 y si es el padre retorna el PID del proceso nuevo, la segunda se encarga de cargar un nuevo programa en memoria y ejecutarlo.

```c
pid = fork();
if (pid == 0) {
  printf("Soy el proceso hijo");
} else {
  printf("Soy el proceso padre");
}
```

- Por otro lado, la syscall `exec` se encarga de reemplazar el programa que se está ejecutando en el proceso actual por otro, para esto se setea un nuevo heap, un nuevo stack y se reemplaza el código en text por el nuevo programa. Por como es la syscall, la llamada no debería devolver nunca.

```c
int pid = fork();
if (pid == 0) {
  printf("Soy el proceso hijo");
  exec("bin/ls"); // Reemplaza el programa actual por ls
  printf("Esto no se ejecuta nunca, exec no debería retornar");
} else {
  printf("Soy el padre");
}
```

# Concurrencia

## Describa que es un thread y use su API para crear un programa que use 5 threads para incrementar una variable compartida por todos en 7 unidades por thread hasta llegar a 1000.

- Un `thread` es una abstracción que provee el SO para un único proceso en ejecución. Se puede pensar a un thread como un proceso, con la salvedad de que los threads comparten el address space, y por consecuencia, pueden acceder a los mismos datos y modificarlos. Son útiles para ejecutar varias secciones de código de forma concurrente y atómica.

```c
int compartida = 0;
pthread_mutex_t lock;

void incrementar() {
  pthread_mutex_lock(&lock);
  while (1) {
    if (*compartida + 7 <= 1000) {
      *compartida += 7;
    }
  }
  pthread_mutex_unlock(&lock);
}

int main() {
  pthread_t t1, t2, t3, t4, t5;

  pthread_create(&t1, NULL, incrementar, &compartida);
  pthread_create(&t2, NULL, incrementar, &compartida);
  pthread_create(&t3, NULL, incrementar, &compartida);
  pthread_create(&t4, NULL, incrementar, &compartida);
  pthread_create(&t5, NULL, incrementar, &compartida);

  pthread_join(t1, NULL);
  pthread_join(t2, NULL);
  pthread_join(t3, NULL);
  pthread_join(t4, NULL);
  pthread_join(t5, NULL);
}
```

## Defina y de ejemplos de race condition, heisenbug, deadlock e interleave.

- **Race condition:** Ocurre cuando dos o más threads intentan modificar una variable compartida sin sincronización. Por ejemplo, si dos threads intentan incrementar una variable compartida sin sincronización, el resultado final puede ser distinto al esperado. En otras palabras, el resultado depende de cuando se `schedulean` los procesos y no de la lógica del programa. Por ejemplo:

```c
int compartida = 0;
pthread_mutex_t lock;

void incrementar() {
  if (*compartida + 7 <= 10000000000) {
    *compartida += 7;
  }
}

int main() {
  pthread_t t1, t2

  pthread_create(&t1, NULL, incrementar, &compartida);
  pthread_create(&t2, NULL, incrementar, &compartida);

  pthread_join(t1, NULL);
  pthread_join(t2, NULL);
}
```
> [!NOTE]
> Como los threads no se sincronizan, el valor puede ser distinto al esperado.

- **Heisenbug:** Ocurre cuando un bug desaparece al intentar debuggearlo. Esto puede ocurrir por que los threads no corren de forma determinística, por lo que el orden de ejecución de los mismos podría generar un bug que es dificil de recrear.

- **Deadlock:** Ocurre cuando se utilizan locks para sincronizar los procesos, pero ocurre que todos quedan bloqueados, entonces todos los procesos estan esperando a que otro termine, por lo que no termina nunca.

```c
int compartida = 0;
pthread_mutex_t lock_1;
pthread_mutex_t lock_2;

void incrementar() {
  pthread_mutex_lock(&lock_1);

  sleep(1);

  pthread_mutex_lock(&lock_2);
  if (*compartida + 7 <= 10000000000) {
    *compartida += 7;
  }

  pthread_mutex_unlock(&lock_2);
  pthread_mutex_unlock(&lock_1);
}

void incrementar_2() {
  pthread_mutex_lock(&lock_2);

  sleep(1);

  pthread_mutex_lock(&lock_1);
  if (*compartida + 7 <= 10000000000) {
    *compartida += 7;
  }

  pthread_mutex_unlock(&lock_1);
  pthread_mutex_unlock(&lock_2);
}

int main() {
  pthread_t t1, t2

  pthread_create(&t1, NULL, incrementar, &compartida);
  pthread_create(&t2, NULL, incrementar_2, &compartida);

  pthread_join(t1, NULL);
  pthread_join(t2, NULL);
}
```
> [!NOTE]
> En este caso se puede ver el deadlock ya que **incrementar_2** e **incrementar** bloquean los mutex en distinto orden, por lo que se quedan esperando a que el otro libere el mutex.

- **Interleave:** Se refiere a la mezcla de las operaciones realizadas por multiples threads en un sistema. Dado a que varios threads pueden estar ejecutandose de forma concurrente, las operaciones entre estos pueden mezclarse en diferentes ordenes cada vez que se ejecuta el programa.

```c
int contador = 0; 

void* incrementar(void* arg) {
    for (int i = 0; i < 1000000; i++) {
        contador++; 
    }
    return NULL;
}

int main() {
    pthread_t thread1, thread2;

    pthread_create(&thread1, NULL, incrementar, NULL);
    pthread_create(&thread2, NULL, incrementar, NULL);

    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("Valor final de contador: %d\n", contador);
    return 0;
}
```
> [!NOTE]
> En este caso como no se controla la concurrencia, el valor final de contador puede ser distinto al esperado ya que los threads pueden intercalar sus operaciones.
