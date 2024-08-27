# Ownership

- Busca eliminar categorías completas de bugs (punteros nulos, uso después de liberación, liberación doble, etc).
- Busca restringir los programas que son considerados válidos.
- No se paga un costo en tiempo de ejecución.
- Es implementado a nivel de tipos, se chequea en tiempo de compilación.

> Además de chequear los tipos, chequea el ownership de los datos. Algunos programas que son válidos en C/C++ en Rust no lo son.

### Como surgén los bugs.

- Aliasing: Mas de un puntero a una misma cosa para poder modificarla.
- Mutación: Cambiar memoria en común.

## Reglas de Ownership

1. Cada valor en Rust tiene un valor que es su dueña.

```rust
    let s = String::from("hello");
    // s es la dueña de la cadena "hello"
```

2. Solo puede existir un dueño a la vez.
3. Cuando la dueña sale de scope, el valor será liberado.

- Cada valor tiene un único dueño que determina su lifetime, cuando el dueño es liberado el valor adueñado tambien.
- Una var es dueña de su valor, si sale de scope la var se destruye y su valor también.

```rust
fn toma_ownership(s: String) {
    println!("{}", s);
}

fn main() {
    let nombre = String::new("Hola");

    toma_ownership(nombre);
    // Aca ya tira error el compilador, porque
    // después de la primer llamada se pierde el ownership.
    // Despues de la llamada, toma_ownership es dueña de "Hola".
    toma_ownership(nombe);
}
```

Para resolver esto se puede hacer:

```rust
fn foo(s: &String) {
    println!("{}", s);
}

fn main() {
    let nombre = String::new("Hola");

    // De esta forma se pasa una referencia compartida (que no es mutable).
    // Entonces no se pierde el ownership.

    foo(&nombre);
    foo(&nombre);
}
```

En cambio, si se hace:

```rust
fn foo(s: &mut String) {
    s.push("a");
    println!("{}", s);
}

fn main() {
    let nombre = String::new("Hola");

    // Si se hace esto se pierde el ownership.
    // Aliasing y mutación.
    foo(&mut nombre);
    foo(&mut nombre);
}
```

Otro ejemplo:

```rust
fn foo(s: &mut String) {
    s.push("a");
    println!("{}", s);
}

fn main() {
    let nombre = String::new("Hola");

    // Aca no se pierde el ownership, se muta Hola a Holaa.
    // y se imprime 2 veces.
    foo(&mut nombre);
    println!("{}", nombre);
}
```

### Caso de uso de referencias mutables.

```rust
#[derive(Debug, Clone)]

struct Saldo(u8);

#[derive(Debug)]
struct A {
    campo: Saldo,
}

impl A {
    pub fn new(campo: Saldo) -> A {
        A { campo }
    }

    pub fn incrementar_en(&mut self, cantidad: u8) {
        self.campo = Saldo(self.campo.0 + cantidad);
    }

    // Devuelvo una copia del saldo.
    pub fn copiar_saldo(&self) -> Saldo {
        self.campo.clone()
    }

    // Asi devuelvo el saldo original, porq no puedo tener 2 punteros a lo mismo.
    // Devolvi el ownership a main.
    pub fn destruir(self) -> Saldo {
        self.campo
    }
}

fn main() {
    let saldo = Saldo(0);
    let a = A::new(saldo);
    println!("{:?}", a);

    let saldo_recuperado: Saldo = a.destruir();
    let saldo_recuperado_2: Saldo = a.copiar_saldo();
    println!("{:?}", saldo_recuperado);
}
```

### Mas ejemplos sobre ownership.

```rust
{
    let point = Box::new((0.625, 0.5)); // Esto es un smart pointer, puede pasar del heap al stack.
    let label = format!("{:?}", point); // label asignado.
    assert_eq!(label, "(0.625, 0.5)");
} // Ambos se liberan, salen de scope
```

> Un Box<T> es un puntero a un valor de tipo T almacenado en el Heap.
> Invocando Box::new(v) se asigna espacio en el heap, se mueve el valor a ese espacio, y retorna un Box que apunta a ese espacio.
> Un Box es el dueño del espacio al que apunta, de tal manera que cuando el box es destruido, se libera su espacio también.

## Movimientos

Se dice que un valor se mueve cuando se cambia el ownership.

- La librería estandar provee tipos de punteros que llevan conteo de referencias (como Rc, Arc). Esto les permite tener múltiples dueños, bajo ciertas restricciones.
- Se puede "tomar prestada una referencia" (Borrow) a un valor. Las referencias son punteros que no adueñan los valores a los que apuntan, y tienen _lifetimes_ limitados.

### Operaciones que mueven.

- Para la mayoría de los tipos, operaciones como asignar un valor a una variable, pasarla a una función o retornarla de una función no copian el valor, mueven el ownership.
- El destinatario ahora controla el lifetime de ese valor, una vez este pierda scope, el valor también.

```rust
let mut a = Person {
    name: String::from("Alice"),
    birth: 1990,
};

a = Person {
    name: String::from("Bob"),
    birth: 1991,
}; // Se destriye el valor de Alice.
```

```rust
let x = vec![1, 2, 3];
let y = x; // Se mueve el ownership de x a y.
let z = x; // Error, x ya no es dueño de la memoria.
```

### Movimientos y control de flujo.

- Si es posible que a una variable se le haya movido el valor, y no ha sido asignado un valor nuevo de manera definitoria, se considera _no inicializada_. Si una variable aún tiene un valor despues de evaluar la expresión de la condición de un if, entonces podemos usarlo en ambas ramas.

```rust
let x = vec![1, 2, 3];
if c {
    f(x); // Esta OK mover el ownership de x a f
} else {
    g(x); // Y aca tambien.
}
h(x) // Acá no, x ya queda sin inicializar si cualquier rama la usa.
```

- Mover una variable en un bucle esta _prohibido_.

```rust
let v = vec![1, 2, 3];
while cond {
    f(v); // Error, v se mueve en la primera iteración.
}         // Para la segunda se considera no inicializada.
```

> Es común intentar vencer al borrow-checker $\rightarrow$ clonando lo que se necesita, el problema obvio con esto es el consumo de memoria.

### Usos corectos de movimientos.

1. Saco el último elemento de un vector

```rust
let v = vec![1, 2, 3];
let n = v.pop().unwrap(); // Se mueve el ownership de v a n.
```

2. Mover el valor del medio de un vector, y reemplazarlo por otro.

```rust
let mut v = vec![1, 2, 3];
let n = v.swap_remove(1); // Se mueve el ownership de v[1] a n.
```

3. Intercambiar otro valor por el que estamos moviendo.

```rust
let mut v = vec![1, 2, 3];
let n = std::mem::replace(&mut v[1], 4); // Se mueve el ownership de v[1] a n.
```

4. Otro ejemplo con vectores, suelen ofrecer metodos para poder usar sus elementos en un bucle.

```rust
let v = vec![1, 2, 3];

for mut s in v { // Esto mueve el vector v,
    s.push("!"); // dejando a v en no inicializado
    println!("{}", s);
}
```

## Tipos que se copian.

Suelen ser tipos que no suelen ser muy grandes (enteros, floats, bools, etc). Copiarlos no repercute en el programa.

- Asignar un valor de un tipo que es _Copy_ el valor pero no lo mueve.
- El origen de la asignación permanece inicalizado y usable, con el mismo valor que antes.
- Pasar tipos _Copy_ a funciones y constructores opera de forma similar.
- Cada movimiento es una copia no profunda (shallow copy) byte a byte. Las copias son similares, solo que dejan al origen inicializado.
- Una tupla o arreglo de tamaño fijo cuyos componentes son _Copy_, estos tambien lo son.
- Se puede derivar el tipo _Clone_ y _Copy_ haciendo \#[derive(Clone, Copy)].

## Referencias y Borrowing.

- Las referencias en Rust son otro tipo de puntero que no se adueñan del valor al que apunta, sino que lo toma prestado ("borrow").
- Las referencias no tienen efecto sobre los lifetimes de sus referentes y no deben sobrevivir a sus referentes.
- Tomar prestado (borrowing) == crear una referencia a un valor.
- No son más que direcciones de memoria. Permiten acceder a un valor sin afectar el ownership.

### Tipos de referencias.

#### Referencia compartida.

- Permite leer pero no modificar. Pueden existir tantas a la vez como se desee.

#### Referencia mutable.

- Permite leer y modificar. Solo puede existir una a la vez.

> No existen referencias nulas en Rust. Para indicar la ausencia de valor se usa Option<T>.

### Seguridad de referencias.

- Rust a cada referencia le asigna un _lifetime_, es decir, el scope.
- No se declaran, el compilador los infiere.

```rust
{
    let r; // r no es inicializado.
    {
        let x = 42;
        r = &x; // r es inicializado.
    } // x sale de scope, r no es válido.
}
```

### Recibiendo referencias por parametro.

Teniendo el siguiente código:

```rust
// Esto no compila.
static mut STASH: &i32; // No esta inicializada, no existe "NULL".
                        // Si es global se puede acceder desde cualquier lado -> inseguro..
                        // Solo se puede mutar si se usa el keyword unsafe
fn f(p: &i32) { STASH = p; }
```

Ahora, si se tiene:

```rust
static mut STASH: &i32 = &128 // "static" es el lifetime mas grande -> todo el programa
fn f(p: &i32) {
    unsafe {
        STASH = p; // El lifetime es distinto, nada asegura que p siga vivo.
    }
}
```

Para que esto si funcione, debe hacerse así:

```rust
static mut STASH: &i32 = &128;

fn f(p: &'static i32) { // De esta forma el compilador se asegura de que
    unsafe {            // los lifetimes sean iguales.
        STASH = p;
    }
}
```

### Structs con parametros de lifetime

> Si un struct contiene un campo que es una referencia, se deben nombrar los lifetimes de esas referencias.

```rust
struct Foo<'a> {
    x: &'a i32,
}
```

## Genericidad y Traits

- Soporte de polimorfismo $\rightarrow$ Generis y Traits.
- Un _Generic_ es un tipo que puede ser parametrizado por otros tipos.
- Un _Trait_ es la versión de Rust de una _interfaz_. Define un conjunto de métodos que pueden ser implementados para un tipo dado.

### Implementar un Trait.

```rust
trait Shape {
    fn area(&self) -> f64;
}

struct Circle {
    radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}
```

### Generics.

```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let p = Point { x: 1, y: 2 };
    let q = Point { x: 1.0, y: 2.0 };
}
```
