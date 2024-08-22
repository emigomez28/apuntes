# Interfaces del Sistema operativo

> El trabajo de un sistema oprativo es compartir una computadora entre programas y proveer un set de servicios más útil que solo el Hardware que lo soporta.

> Un sistema operativo maneja y abstrae el hardware de bajo nivel (low-level hardware).

> Comparte el hardware entre multiples programas asi estos corren (o parecen que corren) al mismo tiempo.

> Proveen modos controlados a los programas para que interactuen entre sí.

Un sistema operativo provee servicios al usuario del programa a traves de una interfaz. Diseñar una interfaz puede ser dificil, la idea es diseñar pocos mecanismos que puedan ser combinados para crear "generalidad".

El libro habla en particular de un SO (xv6) que es "Unix like".

- XV6 usa la forma tradicional del _kernel_, un programa especial que provee servicios a los programas corriendo
- Cada programa corriendo, llamado _proceso_, tiene memoria que contiene instrucciones, data y un "stack".
- Las _instrucciones_ implementan la computación del programa, la _data_ son las variables en las cuales el computo actua y el _stack_ organiza las "procedure calls" del programa.

> Por lo general, una computadora tiene un solo kernel y muchos procesos.

- Cuando un proceso necesita invocar un servicio del kernel, invoca lo que es conocido como un _system call_, que es una de las llamas en la interfaz del sistema operativo.
- Un proceso alterna entre correr en _user space_ y _kernel space_.

El kernel usa mecanismos de hardware provistos por el CPU para asegurar que cada proceso ejecutado en _user space_ pueda acceder solo a su propia memoria.

El kerner se ejecuta con _hardware privileges_ los cuales son requeridos para implementar estas protecciones, los programas de usuario no.

> El shell es un programa que lee comandos del usuario y los ejecuta. Es parte de los programas de usuario y no del kernel.

> Esto muestra el "poder" de la "system call interface", ya que puede ser reemplazada.

## Procesos y memoria

XV6 "comparte tiempo" entre procesos. De forma transparente cambia el CPU entre el set de procesos en espera de ejecución. Mientras un proceso no se esta ejecutando, xv6 guarda los registros del proceso y los restaura cuando se lo ejecuta.

El kernel asocia un proceso con un identificador unico llamado _PID_ (Process Identifier).

Un proceso puede lelgar a crear un nuevo proceso usando el _fork system call_. Lo que hace es darle al nuevo proceso una copia exacta de la memoria del proceso llamador, tanto instrucciones como data.

> En el proceso original, fork retorna el PID del nuevo proceso, en el nuevo proceso, fork retorna 0.

> Se suele dar nombres de padre e hijo a los procesos respectivamente.

### Algunos syscalls

```c
    int pid = fork();
    if (pid > 0) {
        printf("parent: child=%d\n", pid);
        pid = wait((int *) 0);
    } else if (pid == 0) {
        printf("child: exiting\n");
        exit(0);
    } else {
        printf("fork error\n");
    }
```

> En este caso, la _syscall_ `exit` hace que el proceso llamador termine de ejecutar y libera recursos como por ejemplo memoria o archivos abiertos. Recibe un int como argumento siendo 0 caso de exito o 1 caso de error.

> Por otro lado, la _syscall_ `wait` retorna el PID del proceso hijo que termino de ejecutar (o le hicieron `kill`). Si no hay procesos hijos, `wait` retorna -1.

Si bien el proceso hijo tiene una copia de la memoria del padre, el kernel mantiene la memoria y registros separados. Esto es, si el hijo modifica la memoria, el padre no se ve afectado.

> La _syscall_ `exec`remplaza la memoria del proceso llamador con una nueva imagen de un programa.

```c
    char *argv[3];

    argv[0] = "echo";
    argv[1] = "hello";
    argv[2] = 0;
    exec("/bin/echo", argv);
    printf("exec error\n");")
```

En este caso el proceso llamador se reemplaza poir una instancia del programa `/bin/echo` el cual corre con el argumento `echo hello`.

## I/O y File Descriptors

> Un _file descriptor_ es un integer que representa un objeto manejado por el kernel del cual un proceso puede llegar a leer o escribir.

> Se pueden obtener abriendo un file, directorio o dispositov, creando un pipe o duplcando uno existente.

> Por simplicidad, usualmente se va a llamar `file` al objeto que refiere a un file descriptor.

> La interfaz del file descriptor abstrae las diferencias entre files, pipes y devices, haciendolos ver como un stream de bytes.

Internamente, xv6 usa a los file descriptors como un indice en una tabla de procesos, entonces cada proceso tiene un espacio privado de file descriptors empezando en 0.

Por convención, un proceso lee desde el file descriptor 0 (stdin), escribe al 1 (stdout) y escribe mensajes de error al 2 (stderr)

- Las syscalls `read` y `write` leen y escriben bytes desde y hacia un file descriptor.
- `read(fd, buf, n)` lee hasta `n` bytes desde `fd` al buffer `buf` y retorna la cantidad de bytes leidos.
- `write(fd, buf, n)` escribe `n` bytes desde `buf` al file descriptor `fd` y retorna la cantidad de bytes escritos.

Ejemplo en C (que sigue la esencia del comando `cat`):

```c
    char buf[512];
    int n;
    for (;;) {
        n = read(0, buf, sizeof(buf));

        if (n == 0) break;

        if (n < 0) {
            fprintf(2, "read error\n");
            exit(1);
        }

        if (write(1, buf, n) != n) {
            fprintf(2, "write error\n");
            exit(1);
        }
    }
```

Lo importante de esto es que `cat` no sabe de donde esta leyendo (un file, consola o pipe), ni a donde esta escribiendo (consola u archivo). El uso de file descriptors y convenciones facilitan la implementación de cat.

La syscall `close` libera un file descriptor, haciendo que sea libre de uso para un futuro `open`, `pipe` o `dup`. Un nuevo file descriptor alocado tiene el numero más bajo sin usar dentro del proceso.

> File descriptors y fork interactual para acr la redirección IO sencilla. Fork copia el file descriptor table del padre junto con su memoria, asi el hijo tiene los mismos files abiertos que el padre, luego la syscall exec reemplaza la memoria del proceso llamador pero mantiene la file table. Esto permite implementar redireccion I/O con forking.

Pequeño ejemplo con `cat < input.txt`:

```c
char *argv[2];

argv[0] = "cat";
argv[1] = 0;

if (fork() == 0) {
    close(0);
    open("input.txt", O_RDONLY);
    // El segundo argumento es un flag (expresado como bits)
    // que indica como se tiene q comportar open,
    // esta definido en el kernel
    exec("cat", argv);
}
```

> La syscall `dup` duplica un file descriptor existente y retorna uno nuevo que referencia al mismo objeto I/O.

## Pipes

Un _pipe_ es un pequeño buffer del kernel expuesto a procesos como un par de file descriptors, uno para lectura y otro para escritura. Proveen una forma de comunicarse entre procesos. Escribir al final de un pipe deja disponible esos datos para la otra punta del mismo.

```c
    int p[2];
    char *argv[2];

    argv[0] = "wc";
    argv[1] = 0;

    pipe(p);
    if (fork() == 0) {
        close(0);
        dup(p[0]);
        close(p[0]);
        close(p[1]);
        exec("/bin/wc", argv);
    } else {
        close(p[0]);
        write(p[1], "hello world\n", 12);
        close(p[1]);
    }
```

El programa llama a `pipe`, quien crea un nuevo pipe y guarda los file descriptors de `read` y `write` en el array `p`.

Despues de `fork` el hijo y el padre tienen un file descriptor que referencia al pipe, el hijo cierra la llamada con `close` y `dup` para que el file descriptor 0 referencie al pipe de lectura.

## File System

El file system de xv6 contiene:

- Data files: Contienen un array de bytes sin interpretación.
- Directorios: Contienen referencias nombradas a data files u otros directorios.

Los directorios forman un arbol (tree), el cual empieza desde un directorio especial (root)

Por ejemplo, un _path_ como `/a/b/c` refiere al file o directorio llamdo `c` que esta contenido en `b`, el cual esta contenido en `a`, el cual esta contenido en `root`. Los _paths_ que no empiezan con `/` se buscan de forma relativa al proceso que los llama en el directorio actual (current directory).

```c
    chdir("/a"); //chdir -> change directory
    chdir("b");
    open("c", O_RDONLY);

    // Esto es equivalente a

    open("/a/b/c", O_RDONLY);
```

Hay syscalls para crear directorios.

```c
    mkdir("/dir");
    fd = open("dir/file", O_CREATE | O_WRONLY);
    close(fd);
    mknod("/console", 1, 1); // Crea un file especial
                             // que referencia a un dispositivo
```

- El nombre de un file es distinto del file en si mismo, el file es llamado _inode_, y puede tener muchos nombres, llamados _links_.
- Cada link consiste en una entrada en un directorio, la entrada contiene un file name y una referencia a un inode.
- Un inode contiene _metadata_ sobre el file, incluyendo su tipo (file, directory o device), su longitud, donde se ubica el contenido del file en disco y el numero de links al file.

> fstat es una syscall que retorna metadata sobre un file descriptor. Lo hace completando los campos de un struct `stat`.

```c
    struct stat st;
    fstat(fd, &st);
    printf(1, "type %d, size %d\n", st.type, st.size);
```

> link es una syscall que crea un nuevo link a un file existente.

```c
    link("a", "b");
```

> unlink es una syscall que remueve un link a un file. Si el file no tiene mas links, el file es removido.

```c
    unlink("a");
```
