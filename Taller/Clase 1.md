# Introducción a Rust

## ¿Qué es Rust?

> Objetivos de Rust: velocidad, seguridad en el uso de memoria y concurrencia.

> Tiene construcciones de alto nivel (ej: iteradores) mientras que alcanza una muy alta performance a ser de bajo nivel $\rightarrow$ genera un binario.

> Adhiere al principio de "zero-cost abstractions" $\rightarrow$ Solo paga por lo que usa.

## Funcionalidades que lo distinguen

### Seguridad

> Prevenir el acceso a datos/memoria inválida, en tiempo de compilación (memory safe sin costo de tiempo de ejecución) $\rightarrow$ buffer overflow.

> No existen "punteros sueltos" (dangling pointers), las referencias a datos inválidos son descartados.

> Previene condiciones de carrera sobre los datos al usar concurrencia.

### Productividad

> Funcionalidades ergonómicas para el desarrolador, ayudas del compilador, tipos de datos sofisticados, pattern matching, etc. $\rightarrow$ **mantenibilidad**.

## Cargo

> El compilador se llama **rustc** y lo acompaña una familia de herramientas. **Cargo** es el gestor de paqutes y el sistema de construcción $\rightarrow$ similar a make.

> Cargo internamente invoca a rustc.

### Algunos comandos

1. Crear un nuevo proyecto: `cargo new nombre_proyecto`.
2. Crear un proyecto sobre un directorio que ya existe: `cargo init`.
3. Compilar un proyecto: `cargo build`.
4. Compilar el proyecto en modo release: `cargo build --release`.
5. Ejecutar el proyecto: `cargo run`.
6. Ejecutar los tests: `cargo test`.
7. Crear la documentación (HTML): `cargo doc`.
8. Analizar el proyecto sin compilar: `cargo check`.
9. Formatear el código: `cargo fmt`.
10. Linter: `cargo clippy`.

## Sintaxis

Rust es un lenguaje fuertemente tipado, es decir, el tipo de dato es fijo para las variables definidas.

### Tipos númericos

> i8, i16, i32, i64, i128

> u8, u16, u32, u64, u128

> f32, f64

> usize, isize

### Otros tipos nativos

> bool

> char (Unicode)

> Arrays: [u8, 3] $\rightarrow$ tamaño fijo, mismo tipo de dato, se ubican en el Stack.

> Tuplas: (char, u8, i32) $\rightarrow$ tamaño fijo, distintos tipos de datos, ubicados en el Stack.

> Tipos dinámicos: Vec<T>, String $\rightarrow$ tamaño variable, ubicados en el Heap.

## Variables

> Las variables son inmutables por default, si quiero que sea mutable utilizo el modificador **mut**.

```rust
    let mi_var = 5; // inmutable
    let mut mi_var = 5; // mutable
```

> shadow: Se puede reutilizar el nombre de la variable anterior, se descarta el valor anterior.

> Rust implementa inferencias de tipos.

```rust
    fn foo() -> i32 {
        42 // Si es la última línea no va ni return ni ; al final.
    }

    let v = foo();
```

## Estructuras

> Son un conjunto de elementos puestos juntos, que son tratados como una unidad. Se le pueden definir operaciones.

### Variante 1

```rust
struct Persona {
    nombre: String,
    apellido: String
}
```

### Variante 2: Estructura tipo tupla

```rust
    struct Celsius(f32);
    struct Fahrenheit(f32);
```

> Permiten representar cosas como:

```rust
    fn convertir_a_fahrenheit(c: Celsius) -> Fahrenheit {
        Fahrenheit{...}
    }
```

### Variante 3: Estructura tipo unit

```rust
    struct AlgunaCosa;
```

> Similar al void de C. Se le pueden implementar métodos pero no tiene atributos. Por ejemplo puede ser utilizado para implementar el patrón builder.

## Enums

> Tipo de dato que puede tener como valor alguna de las variantes definidas.

### Variante 1: Enumeración de ítems

```rust
    enum Palo{
        Oro,
        Copa,
        Espada,
        Basto
    }

    let palo: Palo = Palo::Oro;
```

### Variate 2: Tipo tupla

```rust
    enum Carta {
        Comodin,
        Oro(u8),
        Copa(u8),
        Espada(u8),
        Basto(u8)
    }
    let mi_carta: Carta = Carta::Oro(9);
    let mi_comodin: Carta = Carta::Comodin;
```

### Variante 3: Tipo estructura

```rust
    enum Jugada{
        Carta {palo: Palo,  numero: u8},
        Paso;
    }
    let mi_jugada: Jugada = Jugada::Carta{palo: Palo::Copa, numero: 6};
```

## Option

> Un elemento que puede contener algún valor o nada / None. T es un generic, puede ser cualquier tipo de valor

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

> Representa el estado de error, donde T es un generic que puede ser cualquier valor y E es un generic que puede ser cualquier error.

```rust
    pub  enum Result<T, E> {
        Ok(T),
        Err(E)
    }

    fn contar_lineas_de_archivo(path: &str) -> Result<u64, String>  {
        ...
        Ok(42)
    }
```

## Operaciones útiles

1. Operador ? $\rightarrow$ ternario de C.
2. unwrap(), unwrap*or(42) $\rightarrow$ Evitar utilizarlo \_unwrap()* porq levanta un _panic_.
3. expect("..." ) $\rightarrow$ Evitar utilizarlo.
4. is_some(), is_none(), is_ok(), is_err().

## Pattern matching

> **match** provee pattern matching (similar al switch de C), se ejecuta la primera rama que cumple la condición.

```rust
    match mi_carta {
        Carta::Oro(valor) => println!("La carta es de Oro de valor {}", valor);
    }
```

```rust
    let punto: (i32, i32, i32) = (0, 1, 2)
    match punto {
        (0, y, z) => println!("En el eje x, y: {}, z: {}", y, z),
        (x, 0, z) => println!("En el eje y, x: {}, z: {}", x, z),
        ...
    }
```

## if let

```rust
    let number = Some(42);

    if let Some(i) = numer {
        println("valor: {}!", i)
    }
```

## Loop

> loop permite indicar un loop infinto $\rightarrow$ while true.

```rust
    loop {
        ...
    }
```

## for

> for permite iterar sobre un iterador $\rightarrow$ similar al de Python.

```rust
    for i in 0..10 { // itera hasta el 9, tipo el range de Python.
        ...
    }
```

> Ejemplo con vectores:

```rust
    let v = vec![1, 2, 3, 4, 5]; // Esta macro genera el vector.
    for num in vector.iter() {
        println!("{}", num);
    }
```

# Practica primer clase

> El proyecto tiene 2 directorios, src y tests, en src esta el código fuente mientras que el directorio tests contiene tests de integración (los tests unitarios suelen estar en el directorio src)

> Un archivo Cargo.toml donde se manejan las dependencias y la configuración del proyecto.

```rust
pub fn saludar() {
    let stdin = io::stdin();
    let mut nombre = String::new(); /// Variable String vacia
    println!("Ingrese su nombre");
    stdin.read_line(&mut nombre).unwrap();
    println!("Hola, {:?}", nombre); /// :? utiliza una representación de debug -> leakea como esta
    /// implementado
}

/// Imprime por stdout un saludo

pub fn saludar_nombre(nombre: &String) {
    println!("Hola, {:?}", nombre);
```

> Para este caso, Display y Debug son traits, donde Debug proporciona info para debuggin y display es "lo que espera el usuario"

> println! es un macro, no una función. Para distinguir un macro de una función se utiliza "!".

> La macro en tiempo de compilación se expande a un código que se ejecuta en tiempo de ejecución.

```rust
    #[derive(Debug)] /// Recibe métodos del trait x, "ganamos" comportamiento gratis -> por el compilador

    struct Coord {
        x: i32,
        y: i32,
    }

    impl Coord {
        ...
    }
```

> Se puede implementar un trait para una estructura, en este caso se implementa el trait Debug.

> Display no es un trait ya que el compilador no sabe "que es lo mejor" para el usuario.

> Debug si se puede derivar, ya que muestra la estructura interna del objeto.

> En el campo **struct** se define el tipo de dato y los nombres de los atributos, mientras que en **impl** se definen los métodos.

> Si por ejemplo quiero implementar el método new sería algo así:

```rust
    impl Coord {
        fn new(x: i32, y: i32) -> Self {
            Self {x, y}
        }
    }
```

> Donde **Self** es el tipo de dato que se está implementando. En caso de utilizar **self** se refiere a la instancia de la estructura.

## Ejemplo de test en Rust:

```rust
    #[test]

    pub fn test_coord() {
        let coordenada = Coord::new(3, 4);
        let modulo = coordenada.modulo();
        assert_eq!(5, modulo);
        assert!(modulo == 5);
        assert_ne!(6, modulo);
    }

    pub fn otro_test() {
        let coordendas = vec![Coord::new(3, 4), Coord::new(5, 12)];

        for c in coordenadas.iter() {
            assert_eq!(5, c.modulo()); /// Si falla, se detiene la ejecución,
        }                              /// entonces no esta bueno para testear
    }
```

## Representación de errores en Rust

> No existen excepciones ni bloques try-catch, los errores son valores como cualquier otro $\rightarrow$ tipo Go.

```rust
    pub enum ArithError {
        DivisionByZero,
        ResultNotInteger
    }

    pub fn dividir(dividendo: i32, divisor i32) -> Result<i32, ArithError> {
        // Esto devuelve un result, que en caso de exito es un i32
        // y en caso de erorr es un ArithError

        if divisor == 0 {
        // Err es un constructor de Result, donde Result es un enum de generics
            Err(ArithError::DivisionByZero)
        }
        if dividendo % divisor != 0 {
            Err(ArithError::ResultNotInteger)
        }
        Ok(dividendo / divisor)
    }
```

> Supongamos que llamamos a la función dividir, si utilizamos **unwrap()** nos va a devolver el valor de Ok(), no un Ok(_valor_). En caso de erorr levanta un panic, que corta toda la ejecución.

> En los tests no es tan importante, de hecho puede seguir ya que muestra todo el stack trace, pero no se usa en codigo en producción.

> Se puede assertear el tipo del error utilizando la macro _matches!()_.

```rust
    #[test]
    pub fn test_dividir() {
        let mut resultado = dividir(10, 2);
        assert!(matches!(resultado, Ok(5));
        assert_eq!(5, resultado.unwrap());
        resultado = dividir(10, 0);
        assert!(matches!(resultado, Err(ArithError::DivisionByZero)));
        assert!(matches!(operacion, Err(ArithError::DivisionByZero { dividendo }) if dividendo > 10));
    }
```

## Implementación similar a las excepciones

> Rust no tiene excepciones, pero si se pueden "simular" utilizando el operador "?".

```rust
    pub fn son_iguales_rebuscado(a: i32, b: i32) -> Result<(), ArithError> {
        let resultado = dividir(a, b)?; // Es como si hubiese hecho el unwrap.

        dividir_enteros(b, a)?;
        Ok(())
    }

        // Es equivalente a hacer esto:

    pub fn son_iguales_rebuscado(a: i32, b: i32) -> Result<(), ArithError> {
        let resultado = match dividir_enteros(a, b) {
            Ok(valor) => valor, // "=>" sirve para indicar los casos dentro del match
            Err(error) => return Err(error)
        };
    }
```
