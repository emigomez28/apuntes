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
