# File System

## Describa cuales son los componentes de FFS y que función cumple FFS en el kernel de linux.

- Fast File System de Unix ilustra ideas importantes tanto para indexar la información de un archivo de bloques para que puedan ubicarse rapidamente y para colocar datos en el disco para obtener una buena ubicación.

- Utiliza una estructura de tipo árbol para localizar cualquier bloque en un archivo y es eficiente tanto para grandes como para pequeños archivos.

- FFS fue diseñado con la idea de incrementar la performance dentro de los sistemas unix. A día de hoy, si bien la estructura interna de un FS puede cambiar, se mantiene la interfaz (la misma API incluyendo  `open()`, `read()`, `write()`, `close()` entre otras).

## Describir el proceso de acceso (lectura de inodos y bloques) de `/home/darthmendez/opt/tool/sisop.txt` suponiendo que el archivo está vacío. Además describa para que sirven todas las estructuras involucradas.

- La estructura del file system se puede visualizar de la siguiente manera:

```
root
|---> home
  |---> darthmendez
    |---> opt
      |---> tool
        |---> sisop.txt (vacío)
```

Donde el proceso de acceso es:

1. **Inodo root:** Bloque de datos con dentry ("dir", inodo)
2. **Inodo dir:** Bloque de datos con dentry ("darthmendez", inodo)
3. **Inodo darthmendez:** Bloque de datos con dentry ("opt", inodo)
4. **Inodo opt:** Bloque de datos con dentry ("tool", inodo)
5. **Inodo tool:** Bloque de datos con dentry ("sisop.txt", inodo)
6. **Inodo sisop.txt:** Bloque de datos vacío.

- **Inodo:** Estructura que contiene los metadatos de un archivo, como el tamaño, la fecha de creación, el dueño, los permisos, etc.

- **Bloque de datos:** Estructura que contiene los datos del archivo.

# Scheduling, Memoria y Concurrencia

## Describa detalladamente con un esquema cuál es la diferencia estructural entre un proceso y un thread ¿Cual es la implementación de estos en linux?

- Si bien un proceso y un thread son una abstracción que provee el sistema operativo, su principal diferencia es que un proceso necesita de un thread para correr. Por un lado, los procesos son independientes y no pueden compartir memoria entre sí, mientras que los threads sí, por lo que un thread es la menor unidad a la cual un procesador puede asignarle tiempo.

## Describa como fué variando la estructura del address space respecto a memoria física en: base y bound, tabla de registros y paginación. Expliqué con diagramas.

- **Tabla de registros:** Se utiliza una tabla de registros para mapear las direcciones virtuales a direcciones físicas. Cada proceso tiene su propia tabla de registros.

- Se tiene un registro base (ej. CR3 en x86) el cuál señala el inicio de las páginas activas.

- Se tienen componentes de hardware (como TLB o MMU) los cuales permiten las traducciónes de memoria virtual a memoria física de forma eficiente y segura.

> [!WARNING]
> Los otros mecanismos para tratar la memoría fueron respondidos en otros parciales.

## ¿Cuál es la mejora respecto a MLFQ que introduce el scheduler de Linux?

- El scheduler de Linux introduce el concepto de **CFS (Completely Fair Scheduler)**, el cual es un algoritmo de scheduling que garantiza que cada proceso reciba un time slice justo de tiempo de CPU. A su vez, utiliza un árbolo rojo y negro, el cuál permite realizar operaciones en $\mathcal{O}(1)$.

# Proceso y kernel

## Escriba un programa en C que simule el funcionamiento de una shell primitiva ¿Qué debería agregarse para poder encadenar dos comandos por las salidas y entradas estándar?

```c
char *PROMPT = "$";
int BUFFER_SIZE = 256;
int CMD_SIZE = 16;

int main(int argc, char[] *argv) {
  char buffer[BUFFER_SIZE];
  char *cmd[CMD_SIZE];

  while (1) {
    if (getcwd(buffer, BUFFER_SIZE) == NULL) return 1;
    printf("%s %s", PROMPT, buffer);
    if (fgets(buffer, BUFFER_SIZE, stdin) == NULL) break;
    if (strcmp(buffer, EXIT)) break;

    into_tokens(cmd, buffer);

    if (strcmp(cmd[0], CD)) {
      cd(cmd);
      continue;
    }

    if (fork() == 0) {
      if(execvp(cmd[0], cmd) == -1) {
        perror("Command failed");
        exit(EXIT_FAILURE);
      }
    }
    wait(NULL);
  }
  return 0;
}

void cd(char[] *cmd, int argc) {
  if (argc == 1) {
      chdir("~");
  } else if (chdir(cmd[1] == -1)) {
      perror("Directory not found");
  }
}
```

- Para poder encadenar comandos se debe dar soporte a los pipes, los cuales permiten redirigir la salida de un comando a la entrada de otro. Esto se puede lograr modificando la función que tokeniza agregando casos para los carácteres especiales de los pipes y después de la llamada a fork ejecutar los comandos de la forma que corresponda.
