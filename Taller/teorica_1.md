# Clase 1

# Cargo

> [!NOTE]
> El compilador se llama **rustc** y lo acompaña una familia de herramientas. El gestor de paquetes y conctructor del sistema es **cargo**.

#### Comandos de cargo

1. `cargo new` $\rightarrow$ Crear proyecto.
2. `cargo init` $\rightarrow$ Crear un proyecto en un directorio existente.
3. `cargo build` $\rightarrow$ Compilar el proyecto.
4. `cargo build --release` $\rightarrow$ Idem anterior pero en modo release.
5. `cargo run` $\rightarrow$ Ejecutar el proyecto.
6. `cargo test` $\rightarrow$ Ejecutar los test.
7. `cargo doc` $\rightarrow$ Generar documentación (HTML).
8. `cargo check` $\rightarrow$ Analizar el proyecto sin compilarlo.
9. `cargo fmt` $\rightarrow$ Formatear el código.
10. `cargo clippy` $\rightarrow$ Ejecutar el linter.

> [!WARNING]
> Todas las entregas deben estar libres de **warnings** de **cargo fmt** y **cargo clippy**

# Sintaxis

- Enteros: i8 a i64 entero con signo, u8 a u64 enteros sin signo
- Flotantes: f32 y f64
- Bools
- char (Unicode) $\rightarrow$ No suele ser muy utilizado como sí lo es en C.

> [!NOTE]
> **usize** e **isize** dependen de la lóngitud de la arquitectura en la que esta trabajando (64 o 32 bits).

- Arrays: \[u8; 3\] Tamaño fijo (almacenado en el stack)
- Tuplas: (char, u8, i32)
- Tipos dinámicos: Vec\<T\>, String

## Variables

>[!WARNING]
> Las variables son **INMUTABLES** por default. Las llamamos **bindings**.

``` rust
let t = true; 

let mut punto = (1_u8, 2_u8);
```

- **Shadow:** Se puede reutilizar el nombre de la variable, se descarta el valor anterior. **NO SE RECOMIENDA UTILIZAR**

``` rust
fn retornar_entero() -> i32 {
  42 // Notar que la última linea va sin return y sin ";"
}

let v = retornar_entero();
```

## Estructuras

Tipos de datos que son tratados como una unidad. Se les puede definir operaciones (métodos).


1. Estructura "común"

```rust
struct Persona {
  nombre: String,
  apellido: String
}
```

2. Estructura de tipo tupla.

```rust
struct Celsius(f32);
struct Fahrenheit(f32);
```
Los atributos no tienen nombre, tiene solo posición. 

> [!NOTE]
> Los atributos se acceden por **posición**. Ejemplo: self.0

Permiten representar cosas como:

```rust
fn convertir_a_fahrenheit(temp: Celsius) -> Fahrenheit {
  ...
}
```

3. Estructura tipo unit

```rust
struct AlgunaCosa;
```
> [!NOTE]
> Similar al void de C. Se le pueden implementar métodos pero no tiene atributos. Puede ser utilizado para implementar el patrón `Builder`.

## Enums

Dentro de un conjunto de opciones, el **enum** toma ún valor.

1. Enumeración de items.

```rust
enum Palo {
  Oro,
  Copa,
  Espada,
  Basto
}

let palo: Palo = Palo::Oro; // Toma la variante Oro del enum.
```

2. Tipo tupla.

```rust
enum Carta{
  Comodin,
  Oro(u8),
  Copa(u8),
  Espada(u8),
  Basto(u8)
}

let palo: Palo = Palo::Oro; // Toma la variante Oro del enum.
```

3. Tipo estructura

```rust
enum Jugada{
  Carta {palo: Palo, numero: u8},
  Paso
}

let mi_jugada: Jugada = Jugada::Carta{palo: Palo::Copa, numero: 6}
```

## Option

> Indicar la **ausencia de valor**

Un elemento que puede contener algún valor o nada (None).
T es un generic, puede ser cualquier tipo de valor.

> [!NOTE]
> Una manera de representar el NULL.

```rust
enum Option<T> {
  Some(T),
  None
}

fn dividir(num: f64, den: f64) -> Option<f64> {
  if den == 0.0 {
    None
  } else {
    Some(num / den)
  }
}
```

## Result

> Representar el estado de **error**

T es un generic
E es un generic que puede ser cualquier error

```rust
pub enum Result<T, E> {
  Ok(T),
  Err(E)
}

fn contar_lineas_archivo(path: &str) -> Result<u64, String> { ... }
```

## Operadores útiles

- operador `?` $\rightarrow$ Azucar sintactico para retonar un **Result**.
- `unwrap()`, `unwrap_or()` $\rightarrow$ Utilizar **unwrap_or()** ya que permite cambiar el tipo de resultado en caso de None/Err y **no levanta panic**
- `expect("...")` $\rightarrow$ Idem **unwrap()**
- `is_some()`, `is_none()`, `is_ok()`, `is_err()`

> [!WARNING]
> No usar **unwrap()** ni **expect()** salvo para debugear. Obtiene lo que hay dentro de los tipos **Result** u **Option** y de ser un **Error** **None** levanta un panic cortando la ejecución del programa.

## Match

> El keyword **match** provee **pattern matching**. Se ejecuta la primer rama que cumple la condición. Hay que chequear todas las opciones.

```rust
let mi_carta: Carta = Carta::Oro(9);

match mi_carta {
  Carta::Oro(valor) => println!("La carta es Oro de valor {}", valor),
  _ => {} // Esto es el caso default, sin esto no compila.
}
```

```rust
let punto: (i32, i32, i32) = (0, -2, 3);

match punto {
  (0, y, z) => println!("En el eje x, y: {}, z: {}", y, z),
  (x, 0, z) => println!("En el eje y, x: {}, z: {}", x, z),
  (_, _, z) => println!("El punto esta sobre el eje z"),
  _ => println!("Las coordenadas son x: {}, y:{}, z{}", x, y, z),
}
```

>[!NOTE]
> `println!()` es una macro ya que las funciones de rust son tipadas. De esta forma `println!()` puede recibir multiples argumentos de distinto tipo.

## If let

> [!NOTE] 
> Es como una variable del match que sirve para chequear una única rama.


```rust
let number = Some(28);
if let Some(i) = number {
  println!("valor {}", i);
}
```

## Loop

Permite ejecutar un loop infinito.

```rust
loop {
  count += 1;
  if count == 3 {
    println!("hola");
    continue;
  }

  if count == 10 {
    break;
  }
}
```

## For

Permite iterar sobre un `iterador`.

> [!NOTE]
> Muy similar al for de python.

```rust
for n in 1..101 {
  ... // Itera entre 1 y 100
}
```

```rust
// Macro para construir un vector dinámico alocado en el heap.
let mut nombres = vec!["Juan", "Pedro", "Felipe"];
for nombre in nombres.iter() {
  ...
}
```

- `while` realiza el bucle siempre que la condición sea verdadera.
- `while let` realiza un bucle mientras la condición `if let` sea verdadera

```rust
while let Some(i) = mi_opcional { ... }
```

## Impls

Permite definir métodos a `enums` y `structs`.

```rust
struct Persona { ... }

impl Persona {
  // Trabaja con una referencia inmutable a lo q lo invoca.
  fn nombre_completo(&self) -> String {
    format!("{} {}", self.nombre, self.apellido)
  }
}

let nombre = persona.nombre_completo();
```

## Funciones asociadas

En otros lenguajes se llaman funciones estáticas.

```rust
impl Persona {
fn new(nombre: String, apellido: String) -> Persona {
    Persona {
      nombre: nombre,
      apellido: apellido
    }
  }
}

let p = Persona::new("Emi".to_string(), "Gomez".to_string());
```

## Traits

Permiten definir comportamiento común a las estructuras y/o enums. Similar a las interfaces en otros lenguajes.

```rust
trait NombreCompleto {
  fn nombre_completo(&self) -> String;
}

impl NombreCompleto for Persona {
  fn nombre_completo(&self) -> String {
    format!("{} {}", self.nombre, self.apellido)
  }
}
```
## Traits y Generics

Permiten realizar polimorfismo.

```rust
fn mostrar_nombre<W: NombreCompleto>(v: W) { ... }
```

## Traits utilitarios

En la biblioteca standard existen varios traits que ya vienen definidos para usarlos.

**Default** permite definir una construcción de un elemento con valores default.

```rust

impl std::default::Default for Persona {
  fn default() -> Persona {
    nombre: "".string(), // Coloca en el heap un string vacio
    apellido: "".string(),
  }
}
```

>[!NOTE]
> En este caso se puede hacer mas fácil así

```rust
#[derive(Default, Debug)] 
// Esto se puede hacer solo sí TODOS los atributos implementan Default y Debug
```

## From e Into

Son traits para convertir de un tipo de dato a otro.

```rust
impl From<Sring> for Persona {
  fn from(s: String) -> Persona {
    Persona { ... }
  }
}
```

# Organización del código

## Módulos

Son una forma de declarar un espacio de nombre.

```rust
fn main() {
  saludos::hola();
  self::chau();
}

mod saludos { // defino el módulo en el mismo archivo
  // Por default son privados
  pub fn hola() {
    println!("Hola mundo!");
  }
  pub fn chau() {
    println!("Chau mundo");
  }
}
```
> [!NOTE]
> En este caso se usa la ruta completa, pero se pueden usar modificadores. `self` se refiere al mismo módulo y `super` al padre.

### Multiples archivos en mismo directorio.

```rust
// main.rs 

mod saludos // importo el módulo en main

fn main() {
saludos::hola();
}

// saludos.rs

pub fn hola() { // Notar que debe ser público para exportarlo
  println!("Hola Mundo!");
}
```
### En otro archivo de un subdirectorio.

```rust
// -----------------------
//        main.rs
// -----------------------

mod frases; // Esto va a buscar el archivo mod.rs en el directorio frases

fn main() {
  frases::saludos::hola();
}

// -----------------------
//     frases/mod.rs
// -----------------------

pub mod saludos;

// -----------------------
//    frases/saludos.rs
// -----------------------

pub fn hola() {
  println!("Hola mundo!");
}
```

### Crates

Importar crates externos en el archivo `Cargo.toml`

```toml
[dependencies]
log = "0.4"
```

Y para usarlo

```rust
use log::info;

info!("hacer algo");
```

> [!IMPORTANT]
> No se puede usar cualquier crate externo, consultar antes de usar alguno.

# Testing

- Funciones que verifican que el resto del código funciona de manera esperada.
- Se utiliza `#[test]` antes de `fn` para indicar que es un test.
- Para las validaciones se utilizan macros como `assert!(persona.edad > 18);`
- Los test se organizan en un **módulo test**

```rust
#[cfg(test)]
mod tests { ... }
```

- Los test unitarios van en el mismo archivo de lo que se va a testear.
- Los test de integración se colocan en el directorio `tests/`, a la misma altura que `src/`.

> [!WARNING]
> Se compila cada archivo como un `crate` separado así que hay que incluir el `crate` en el código. No pueden testear funciones de `src/main.rs`. Se ejecutan en simultaneo (en varios hilos).
