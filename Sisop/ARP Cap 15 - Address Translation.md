# Mechanism: Address Translation

- **Limited Direct Execution (LDE)**: Dejar correr el programa directamente en Hardware y en ciertos momentos clave (el proceso llama a una syscall o hay un timer interrupt) se cede el control al sistema operativo. Esto se hace para evitar que el proceso haga cosas que no debería, como acceder a memoria que no le corresponde.

Con el **address translation** el hardware transforma cada acceso a memoria cambiando su **virtual address** a una **physical address** donde realmente esta ubicada la información que se necesita.

Para poder virtualizar la memoria tambien hay que manejar dinamicamente la memoria, es decir, los procesos deben poder ser realocados.

## Dynamic (Hardware-based) Realocation

- **Base and Bounds**: Se tiene un registro que guarda la dirección base de la memoria que se le asigna al proceso y otro que guarda el tamaño de la memoria que se le asigna al proceso. Cada vez que se accede a memoria se suma la dirección base a la dirección virtual y se chequea que la dirección virtual sea menor que la dirección base + el tamaño de la memoria asignada.

> Es así como se protege a la memoria y se la virtualiza, utilizando 2 registros del procesador.

- **Memory Management Unit (MMU):** Parte del procesador que ayuda con la traducción de direcciones virtuales a físicas.


