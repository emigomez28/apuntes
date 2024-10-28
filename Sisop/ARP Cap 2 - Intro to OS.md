# Introduction to Operating Systems

- Programa $\rightarrow$ Ejecuta instrucciones
- Ciclo de procesador $\rightarrow$ Fetch, Decode, Execute

# Virtualización

> Técnica que usa el SO para asegurarse de que el mismo funcione correcta y eficientemente en una forma simple de uso.

- El OS toma un recurso físico (memoria, CPU, etc) y lo transforma en su forma **virtual**, la cual es una forma más **general**, **poderosa** y **fácil** de usar.

- Muchas veces solemos referirnos a un SO como **virtual machine**.

- Para que los usuarios puedan decirle al OS que hacer, el mismo provee una API $\rightarrow$ **system calls**. Tambien se puede decir que el OS provee una **standard library**.

## Virtualización de CPU

Suponiendo el siguiente programa:

```c
int main(int argc, char *argv[]) {
  if (argc != 2) {
    fprintf(stderr, "Usage: %s <string>\n", argv[0]);
    exit(1);
  }

  char *str = argv[1];
  while(1) {
    Spin(1);
    printf("%s\n", str);
  }
  return 0;
}
```

- Corriendo un el programa en un solo CPU no tiene nada interesante, es solo un loop infinito. 

- Corriendolo con varios CPUs, se puede ver que el programa se ejecuta en paralelo. Es decir, parece que hay varios CPUs corriendo el programa de forma aislada, cuando en realidad solo tenemos un CPU físico $\rightarrow$ **virtualización del CPU**.

> En este caso, se puede ver como el SO toma el rol de **ilusionista** ya que hace creer que el sistema tiene muchos CPUs virtuales. 

> Convierte un único CPU (o un set reducido de CPUs) en lo que parece ser infinitos CPUs virtuales $rightarrow$ virtualización del CPU

## Virtualización de Memoria

- Hoy en día se puede entender a la memoria como un array de bytes, para **leer** y **escribir** en memoria solo se necesita especificar la **dirección de memoria**.

Suponiendo que se corre el siguiente programa:

```c
int main(int argc, char *argv[]) {
  int *p = malloc(sizeof(int));
  assert(p != NULL);
  printf("(%d) address pointed to by p: %p\n", getpid(), p);
  *p = 0;
  while(1) {
    Spin(1);
    *p = *p + 1;
    printf("(%d) p: %d\n", getpid(), *p);
  }
  return 0;
}
```

- En un solo CPU se mostrará la dir. de memoria (por ejemplo 0x200000)y después los valores de p.

- Al correrlo con 2 CPUs se puede observar como ambos procesos al mostrar la dirección de memoria a la que apuntan muestran la misma y parecen modificarla de forma independiente, esto se debe a la **virtualización de la memoria**.

- Cada proceso tiene acceso a su propio **virtual address space**, el cual el OS mantiene mapeado a la memoria física.

## Concurrencia

Suponiendo el siguiente programa:

```c
volatile int counter = 0;
int loops;

void *worker(void *arg) {
  int i;
  for (int i = 0; i < loops; i++) {
    counter++;
  }
  return NULL;
}

int main(int argc, char *argv[]) {
  if (argc != 2) {
    fprintf(stderr, "usage: threads <value>\n");
    exit(1);
  }

  loops = atoi(argv[1]);
  pthread_t p1, p2;
  printf("Initial value: %d\n", counter);

  Pthread_create(&p1, NULL, worker, NULL);
  Pthread_create(&p2, NULL, worker, NULL);

  Pthread_join(p1, NULL);
  Pthread_join(p2, NULL);

  printf("Final value: %d\n", counter);
  return 0;
}
```

- Los threads **p1** y **p2** incrementan la variable `counter` en `loops` veces. Como los threads comparten el mismo espacio de memoria, se dice que el valor esperado de `counter` es `2 $\times$ loops`.

> Al correr este programa con un valor de 1000 para la variable `loops` el resultado es 2000, ya que cada thread incrementa en 1000 la varibale. 

> Al tomar valores mas grandes, los valores divergen y pueden no ser los esperados. Esto se debe a que las instrucciones no se ejecutan de forma **atómica**.


## Persistencia

> Además de guardar valores de forma temporal/volátil (como en DRAM) tambien se quiere guardar datos de forma persistente (en disco o SSD).

> El software que se encarga de esto es el **file system**.

- A diferencia de la CPU y la memoria, el OS no virtualiza el disco, sino que este es compartido entre todos los archivos.

## Objetivos de Diseño en un SO 

- **Recursos físicos** $\rightarrow$ **virtualizados**.
- Capacidad para **persistir** datos.
- **Concurrencia** $\rightarrow$ **threads** y **locks**.
- Proveer **abstracciones** para facilitar el uso del mismo.
- **Protección** entre las aplicaciones mediante **aislamiento** $\rightarrow$ **seguridad**.


