# Introducción a concurrencia en Rust

> Programación concurrente: Diferentes partes de un programa se ejecutan independientemente.
> Programación Paralela: Diferentes partes de un programa se ejecutan exactamente al mismo tiempo.

En concurrencia hay intercalamiento.

## Definiciones

> Programa concurrente: Consiste en un conjunto finito de procesos secuenciales.
> Procesos: Están compuestos por un conjunto finito de instrucciones atómicas.
> Ejecución del programa concurrente: Resulta al ejecutar una secuencia de instrucciones atómicas que se obtiene de intercalar arbitrariamente las instrucciones atómicas de los procesos que lo componen.
> El intercalamiento es _arbitriario_ $\rightarrow$ Scheduler del SO.

## Desafios

> Sincronización: Coordinación temporal entre distintos procesos.
> Comunicación: Los datos que necesitan compartir los procesos para cumplir la función del programa.

Esto puede dar problemas al debuggear, ya que algunos pueden ocurrir segun un _schedule_ particular.

# Problemas de la concurrencia

## Condiciones de carrera

- Los hilos de ejecución del programa acceden a datos o a recursos en un orden inconsistente
- La salida del programa depende del orden de ejecución de las instrucciones atómicas de los procesos. La salida no es determinística.

## Deadlocks

- Dos o más procesos se bloquean mutuamente esperando que el otro libere un recurso.
- Aparición de bugs que se manifiestan en un escenario particular y son difíciles de reproducir.

# Procesos y Threads

## Procesos en UNIX

Un proceso esta formado por:

- Programa: Instrucciones a ejecutar
- Datos de usuario: Espacio de memoria modificable por el usuario, datos propios del programa.
- Pila del Sistema: Se utiliza para almacenar las llamadas a funciones y las variables locales.
- Estructuras de datos del kernel.

> Podemos decir que un proceso es una instancia de un programa en ejecución

## Threads

Un _thread_ es una unidad de ejecución que vive dentro de un proceso.

> Un proceso con varios threads se lo conoce como _multithread_
> Los threads que viven dentro de un proceso se ejecutan en forma concurrente.
> Un thread es similar a un proceso, pero con una menor carga de contexto propio.
> Los threads que viven dentro de un proceso comparten contexto entre sí.

## Concurrencia en Rust

> El mecanismo de ownership y el sistema de tipos de Rust previene los problemas de la concurrencia en tiempo de compilación.
> Los errores se manifiestan en tiempo de compilación.

Existen 2 modelos de mapear threads, _green_thread_ y usar threads del sistema operativo (el modelo de Rust).

## Threads en Rust

- Rust permite crear Threads del SO.

```rust
let join_handle: thread::JoinHandle<_> = thread::spawn(|| {
    println!("Hello from a thread!");
});
```

- El thread se crea con la función `spawn` del módulo `thread`. Recibe como parámetro un _moving closure_ (toma ownership) y retorna el _handle_ del thread.
- Los threads son puestos en ejecución por el scheduler del SO.
- No se puede asegurar nada sobre el orden en el cual se ejecutarán las instrucciones de los threads.

Un thread padre puede esperar a que un thread hijo termine utilizando `join`.

```rust
// child es el handler del thread hijo.
child.join();
```

Otro ejemplo:

```rust
use std::thread;

static NTHREADS: i32 = 10;

fn main() {
    let mut children = vec![];

    for i in 0..NTHREADS {
        children.push(thread::spawn(move || {
            println!("this is thread number {}", i);
        }));
    }
}
```

> `thread::sleep()` suspende la ejecución del thread durante un tiempo dado. El tiempo se especifica con funciones de `std::time::Duration`. Evitar su uso en la materia $\rightarrow$ "anti-patron".
> `thread::yield_now()` le dice al scheduler que permita que otro thread se ejecute.

Los threads pueden tener un nombre, sirve para indetificar un thread que ejecuta `panic!`. El nombre puede obtenerse y usarse.

```rust
let builder = thread::Builder::new().name("nombre".to_string());

let handler = builder.spawn(|| {
    assert_eq!(thread::current().name(), Some("nombre"));
}).unwrap();
```

> Tambien se puede configurar el tamaño del stack del thread con `stack_size()`.

## Introducción a Traits Send y Sync.

### Send

> El trait marker `Send` indica que el ownership del tipo que lo implementa puede ser transferido entre threads.
> Casi todos los tipos de Rust son `Send`.
> Los tipos compuestos qeu estan formados por tipos `Send` también son `Send`.

### Sync

> El trait marker `Sync` indica que es seguro para el tipo que implementa `Sync` ser referenciado desde múltiples threads.
> T es `Sync` si `&T` es `Send`.
> Los tipos primitivos son `Sync` y los tipos compuestos que estan formados por tipos `Sync` también lo son.

## Channels

- Un canal tiene 2 extremos, un _sender_ y un _receiver_.
- Una parte del código invoca métodos sobre el transmisor, con los datos a enviar, otra parte chequea el extremo de recepción.
- Múltiples productores, un consumidor.
- Para crear múltiples productores se clona el extremo de envío.

  > Transfieren el ownership del elemento enviado.

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("Hola");
        tx.send(val).unwrap();
    });

    let received = rx.recv().unwrap();
    println!("Recibido: {}", received);
}
```

Cuando utilizamos canales, debe tenerse en cuenta: - La comunicación entre distintos threads a través de un acanal es asincrónica. - Es posible utilizar canales para sincronizar la ejecución de varios threads.

## Locks

Sirven para realizar la exclusión mutua entre procesos.

- Un proceso puede tener 2 estados, bloqueado o desbloqueado.
- Se implementan con variables de tipo _lock_, que contienen el estado del mismo.
- Los métodos disponibles son `lock()` y `unlock()`.

> Para la implementación necesita soporte tanto del hardware como del sistema operativo.

### Locks en Rust

Rust provee locks compartidos (de lectura) y locks exclusivos(de escritura) en el módulo `std::sync::RwLock`.

```rust
use std::sync::RwLock;

fn main() {
    let lock = RwLock::new(1);

    let n = lock.read().unwrap();

    println!("Leyendo: {}", *n);

    assert!(lock.try_write().is_err());
}
```

> Se dice que un lock está _envenenado_ cuando un thread lo toma de forma exclusiva y mientras tiene tomado el lock, ejecuta _panic!_. Las llamadas posteriores a `read()` y `write()` sobre el mismo lock devolverán _Error_.
