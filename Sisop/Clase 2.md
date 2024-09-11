# La abstraccion User/Kernel

## Ejecución Directa

> En un sistema de ejecución directa, _los programas tienen acceso completo a todo el hardware del sistema_.

> El SO provee rutinas a modo de bibliotea, actua principalmente como _pegamento_ pero no _provee aislamiento_.

EJ: MS-DOS

Se crear una entrada en la lista de procesos, se aloca memoria para el programa y se lo carga, se ejecuta main() sobre el hardware.

> El programa es el dueño de los recursos $\rightarrow$ no hay protección.

## Ejecución Directa Limitada

1. El _hardware_ va a limitar ciertas operaciones.
2. El programa especial y privilegiado va a arbitrar las operaciones riesgosas.

> El rol central de un SO es la protección.

## Kernel Land y User Land

- **User Land**: No se puede confiar en los programas. Donde viven las aplicaiones de usuario $\rightarrow$ procesos.
- **Kernel Land**: Todo es confiable.

Un _proceso_ o programa en ejecución posee memoria con las intrucciones, datos, el stack y el heap.

El kernel arbitra el acceso a los recursos del sistema.

### User Space

- Una aplicación puede ejecutarse solo en _modo usuario_.
- No puede ejecutar instrucciones privilegiadas.
- No puede acceder a la memoria del kernel.
- Se dice que se ejecuta en _user space_.

### Kernel Space

- _Solo el Kernel_ se ejecuta en modo supervisor.
- Puede ejecutar instrucciones privilegiadas.
- Se dice que se ejecuta en _kernel space_.

### Más sobre User Space

Los procesos se ejecutan en un _contexto_:

- Aislado
- Protegido
- Restringido

Mediante el uso de funciones que se encuentran el las bibliotecas pueden utilizar los servicios de acceso al hardware o _recursos que proporciona el kernel_

> Hay un mecanismo de hardware que permite que hay 2 sets de instrucciones, uno privilegiado y otro para usuario.

> Cuando un programa llama a una syscall debe haber un cambio de contexto.

## System Calls

- Una System call es un punto de entrada controlado al kernel, permitiendo a un proceso solicitar que el kernel realice alguna operación en su nombre.

## Privilegios

- Estan representadas por los _rings_. El paso de ring 3 a ring 0 es un cambio de contexto. Es un bit.

- Int n (Software interrupt) genera un insterrupción de software pero no es privilegiada, su manejo depende del vector de iterrupción, que puede implicar coódigo privilegiado.

## Protección de Memoria

- Mecanismo que se implementa con hardware que lo que hace es chequear que un proceso este operando sobre su memoria asignada.

## Timer Interrupts

- Casi todos los procesadores contienen un dispositivo llamado _Hardware Timer_, cada timer interrumpe un determinado procesador mediante una interrupción de hardware.

- Cuando una interrupción por tiempo se dispara se transfiere el control al kernel.

## Cambios de contexto

Cuando se crea un proceso, se crea en un entorno aislado, luego se van generando cambios de contexto.

> Hay un ping-pong constante entre el kernel y los procesos, donde se pasa de un contexto a otro.

### Cosas que inducen un cambio de User Mode a Kernel Mode

- Interrupciones(dispositivos I/O y el timer)
- Excepciones (división por cero, acceso a memoria no permitido)
- System Calls (que en el fondo son interrupciones)

## Interrupciones

Es una señal asincrónica hacia el procesador avisando que algún evento externo require su atención.

### Orden de importancia

- Errores de Máquina
- Timers
- Discos
- Network devices
- Terminales
- Interrupciones de Software

## Excepciones

Es un evento de hardware causado po una aplicación de usuario que causa la transferencia del control al Kernel. Se suele manejar con un mecanismo similar o igual que las interrupciones.

## System Calls

- Son una forma de comunicación entre el proceso y el kernel.
- Se utilizan para solicitar servicios al kernel.

### System Calls: x86

- Desde el punto de vista de un programa se llamar a una syscall es como invocar una función. Estas son wrappers de las syscalls de verdad, las cuales en el fondo proporcionan ciertos argumentos en registros particulares al Kernel para poder ejecutarse.

El orden de los argumentos en los registros es el siguiente:

- eax: syscall number
- ebx: arg1
- ecx: arg2
- edx: arg3
- esi: arg4
- edi: arg5
- ebp: arg6
- De haber más argumentos $\rightarrow$ stack.

> Existe una tabla de syscalls que se encuentra en el kernel. Es un array de punteros a funciones.

## Tipos de Kernel

- **Monolítico**: Todo el kernel vive en el espacio de kernel.
- **Microkernel**: Solo las funciones más básicas del kernel viven en el espacio de kernel, el resto vive en el espacio de usuario.

### Convención de llamadas

Reglas que deben seguir una parte del programa para comunicarse con otra parte del mismo.

1. Pasar argumentos.
2. Recibir valores de retorno.
3. Guardar info. importante.

## Convención de llamadas en RISC-V

- Los argumentos se pasan en los registros a0-a7.
- Si hay una unidad de punto flotante disponible (hardware de punto flotante) se pasan en fa0-fa7.
- Si los argumentos no caben en los registros, se pasan por la pila.
- El valor de retorno se guarda en a0 y a1, si hay soporte de hardware para BPF $\rightarrow$ fa0 y fa1.
- Registros preservados(s0 a s11): Deben conservar su valor original durante la ejecución de una función
- Registros temporales(t0 a t6): Pueden ser usados por una función sin necesidad de conservar su valor original.
- Se debe alinear la pila a 16 bytes.
