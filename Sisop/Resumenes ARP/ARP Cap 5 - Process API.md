# Process API

## Fork

- `fork()` es una **syscall** que se usa para crear procesos. Estos nuevos procesos son **una copia exacta del proceso llamador** (tambien llamados padre e hijo)

- El resultado de un programa que utiliza esta syscall no es determinístico.


## Wait

- `wait()` es una **syscall** que se usa para esperar a que un proceso hijo termine.

## Exec

- `exec()` es una **syscall** que se usa para reemplazar la imagen de un proceso por otra.

### Extra

- Un **shell** suele utilizar estas syscalls para ejecutar comandos de usuario, utilizando redirecciones I/O y pipes.

- Un **shell** es un programa que permite a los usuarios interactuar con el sistema operativo.

- El control de los procesos esta disponible en forma de **señaes** (signals).
