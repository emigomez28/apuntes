# Introducción a Redes y Sockets en Rust

## Introducción

Es una herramienta que provee el SO que permite la comunicación entre distintos procesos.

> Los sockets pueden estar en la misma máquina o en máquinas distintas.
> Se usan en aplicaciones que implementan el modelo cliente-servidor.

- Cliente: Es activo porque inicia la interacción con el servidor.
- Servidor: Es pasivo porque espera a que el cliente inicie la interacción. Se dice que es previo al cliente ya que debe existir antes de que el cliente lo contacte. Es reactivo porque responde a los pedidos del cliente.

## Modelos de capas

> Un modelo de capa n es un modelo de red que divide las funciones de red en n capas.
> Cada capa realiza una función específica y se comunica con las capas adyacentes virtualmente.
> El conjunto de reglas con el que realizan esta comunicación se llama _protocolo_.

## Tipos de servicios

### Sin conexión

- Los datos se envian al recepto y no hay control de flujo ni control de errores.
- Los datos que no entren en el buffer son descartados.

### Sin conexión con ACK

- Por cada dato recibido, el receptor envia un acuse de recibo conocido como _ACK_.
- _ACK_ significa _Acknowledgement_.

### Con conexión

- Tiene 3 fases:

  1. Establecimiento de la conexión.
  2. Intercambio de datos.
  3. Cierre de la conexión.

- Hay control de flujo y control de errores.

## Modelo OSI (Open Systems Interconnection)

Es un modelo de red que divide las funciones de red en 7 capas, donde cada capa
ofrece servicios a la capa de arriba y recibe servicios de la capa de abajo.

1. **Capa física**: Define las características eléctricas, mecánicas y funcionales de los dispositivos de red. Se encarga de la comunicación de bits a través de un medio físico. Ej: Ethernet.
2. **Capa de enlace de datos**: Se encarga de la comunicación de tramas a través de un medio físico. Se encarga de la detección y corrección de errores. Suele estar unido a la capa física. Ej: Ethernet.
3. **Capa de red**: Se encarga de la comunicación de paquetes a través de un medio físico. Se encarga de la conmutación de paquetes y de la determinación de la ruta que seguirán los paquetes. Proveen fragmentación y reensamblado de paquetes, ya que una red puede no tener la misma capacidad que la red anterior. Su unidad es el paquete o datagrama(este se suele utilizar cuando se habla de un protocolo sin conexión) Ej: IP.
4. **Capa de transporte**: Se encarga de la comunicación de segmentos a través de un medio físico. Se encarga de la segmentación y reensamblado de datos, del control de flujo y del control de errores. Ej: TCP, UDP.
5. **Capa de sesión**: Se encarga de la comunicación de datos a través de un medio físico. Se encarga de la sincronización de la comunicación, del control de diálogo y de la administración de sesiones. Ej: NetBIOS.
6. **Capa de presentación**: Se encarga de la comunicación de datos a través de un medio físico. Se encarga de la traducción, compresión y cifrado de datos. Ej: JPEG, MPEG.
7. **Capa de aplicación**: Se encarga de la comunicación de datos a través de un medio físico. Se encarga de la comunicación de datos de usuario y de la comunicación de datos de aplicación. Ej: HTTP, FTP.

## Tipos de Socket

El lenguaje provee una abstracción(Wrapper) del Socket que provee el SO.

- Stream Sockets: Usan el protocolo TCP $\rightarrow$ Entrega garantizada del flujo de bytes.
- Datagram Sockets: Usan el protocolo UDP $\rightarrow$ Entrega no garantizada del flujo de bytes, servicio sin conexión.
- Raw Sockets: Permiten a las aplicaciones enviar paquetes IP.
- Sequenced packet sockets: Similares a los _Stream sockets_, pero preservan los delimitadores de registro. Utilizan el protocolo SPP (Sequenced Packet Protocol). Estan en desuso.

## Sockets en Rust

### Servidor TCP

> Los nombres de los protocolos en Rust son los mismos que las syscalls de Unix.

La biblioteca `std::net` de Rust provee una biblioteca de networking.

1. Se asocia el Socket a una dirección. El método _bind_ crea un nuevo `TcpListener` y lo asocia a una dirección específica.

```rust
pub fn bind<A: ToSocketAddrs>(addr: A) -> Result<TcpListener>

let listener = TcpListener::bind("127.0.0.1:80")
```

2. Obtener las conexiones establecidas.

Sobre la estructura TcpListener se obtienen conexiones establecidas. El método _incoming_ retorna un iterador que devuelve una secuencia de tipo TcpStream.

```rust
pub fn incoming(&self) -> Incoming<'_'>

for stream in listener.incoming() {
    let stream = stream.unwrap();
    println!("Connection established!");
}
```

> La iteración es sobre `intentos de conexiones` $\rightarrow$ Puede retornar _Err_.
> El iterador es una cola de intentos de conexiones.

- Otra forma de hacer esto es obteniendo conexiones establecidas con _accept_.

```rust
pub fn accept(&self) -> Result<(TcpStream, SocketAddr)>

match listener.accept() {
    Ok((stream, addr)) => println!("New client: {:?}", addr),
    Err(e) => println!("Error: {:?}", e),
};
```

3. Leer datos del Socket con _read_.

TcpStream implementa el método _read_ (del trait `std::io::Read`)

```rust
pub fn read(&mut self, buf: &mut [u8]) -> Result<usize>

let mut buffer = [0; 1024];
stream.read(&mut buffer).unwrap();
```

4. El servidor envía una respuesta a una petición del cliente.

TcpStream implementa el método _write_ (del trait `std::io::Write`)

```rust
pub fn write(&mut self, buf: &[u8]) -> Result<usize>

let response = "Respuesta\n";
stream.write(response.as_bytes()).unwrap();
stream.flush().unwrap();
```

El método _flush_ realiza una espera, previniendo que el programa continue sin haber escrito en la conexión todos los bytes.

### Cliente TCP

1. El cliente debe estableceer una conexión con el servidor.

> Construir la dirección destino a partir de una dirección IP:

```rust
use std::net::{IpAddr, Ipv4Addr, SocketAddr};
let socket = SocketAddr::new(IpAddr::V4(Ipv4Addr::new(127, 0, 0, 1)), 80);
```

> Construir la dirección de destino a partir de un nombre (método _to_socket_addrs_):

```rust
fn to_socket_addrs(&self) -> Result<Self::Iter>
// Devuelve un iterador de direcciones
let mut addrs_iter = "localhost:443".to_socket_addrs().unwrap();
```

2. El cliente debe conectarse al servidor utilizando el método _connect_.

```rust
pub fn connect<A: ToSocketAddrs>(addr: A) -> Result<TcpStream>

let addrs = [
    SocketAddr::from(([127, 0, 0, 1], 8080)),
    SocketAddr::from(([127, 0, 0, 1], 8081)),
];

if let Ok(stream) = TcpStream::connect(&addrs[..]) {
    println!("Connected to the server!");
} else {
    println!("Couldn't connect to the server...");
}
```

3. Para enviar y recibir datos el cliente ejecuta los métodos _read_ y _write_ al igual quer el servidor.

4. Para cerrar la conexión TCP puede ser realziado de forma individual, es decir, puede ser ejecutada por cualquiera de las dos partes (cliente o servidor).

> La conexión establecida con TcpStream se cierra cuando el valor ejecuta _drop_. Esto inicia el envío del mensaje _close_ de TCP.
> El método _shutdown_ puede cerrar el extremo de escritura, lectura o ambos.
