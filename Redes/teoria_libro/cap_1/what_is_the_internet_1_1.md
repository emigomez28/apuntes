# ¿Que es el internet?

Hay varias formas de ver el Internet, desde un punto de vista practico (el autor lo llama 'Nuts and Bolts') teniendo en cuenta los componentes basicos tanto de hardware como de software.

Por otro lado, se puede describir al Internet como una infrastructura de red la cual provee servicios para aplicaciones distribuidas.

## Internet visto desde un punto de vista practico.

> El Internet es una red de computadoras que conecta muchos dispositivos informaticos a lo largo del mundo.

Estos dispositivos informaticos fueron "creciendo". En un principio eran PCs, Linux workstations y servidores, luego, fueron incorporados celulares (smartphones), tablets, TVs, etc.

> [!NOTE]
> Todos estos dispositivos son llamados **hosts** o **end systems**. end system == host.


- Los **End Systems** estan conectados entre si por una red de **Communication links** y **Packet switches**. Diferentes links pueden transmitir diferente data a distinto ratio (**Transmission rate** medido en $\frac{\text{bits}}{\text{s}}$).

- Cuando un **end system** tiene data para enviar a otro, este segmenta los datos y añade **header bytes** a cada segmento. Esto es conocido como **paquete**.

- Un **packet switch** recibe un paquete a traves de uno de sus **communication links** y lo reenvia a traves de otro comm. link.

> [!NOTE]
> Hoy en día hay muchos packet switches, pero los mas pominentes son **routers** y **link-layer switches**. Ambos reenvian el paquete hasta su destino.


| Link-Layer Switches|Routers|
| -------------------|--------|
| Suelen ser utilizados **access networks**| Suelen ser utilizados en el **network core** |


- El camino que siguen los paquetes para llegar a destino es llamado **ruta**.

> [!HINT]
> Es fácil pensar al Internet como una autopista, donde hay conexiones, desvios y autos.

- Los **End Systems** acceden al Internet a traves de **Internet Service Providers (ISPs)**, los cuales pueden ser empresas de telefonia/cable, universidades ISPs, ISPs que proveen WiFi (aeropuertos, hoteles, cafes, etc)

> [!HINT]
> Cada ISP es en si misma una red de packet switches y communication links.

- Cada pieza del Internet corre por **protocolos** que controlan la información que se envía y recibe. Dos de los mas importantes son **Transmission Control Protocol (TCP)** e **Internet Protocol (IP)**.


> **IP**: Especifica el formato de los paquetes que son enviados y recibidos entre routers y end systems.

## Internet visto como servicio.

El internet además de ser todo lo mencionado, tambien puede ser visto como un servicio, ya que hoy en día a traves de internet se pueden realizar cosas como enviar mensajes entre smartphones (ej Whatsapp), ver  y mapear el trafico en tiempo real (ej Google Maps o Waze), musica, streaming desde la nube, entre otros.

Este tipo de aplicaciones se las conoce como distribuidas, ya que involucran multiples end systems que intercambian datos entre sí.

> [!IMPORTANT]
> Todos las aplicaciones de internet corren en un end system. Si bien los packet switches facilitan el intercambio de datos entre end systems, no les preocupa la aplicación fuente o el receptor de datos.


- Todos los end systems conectados a internet proveen un **socket interface** que especifica como un programa pide a la infrastructura de internet que delivere data a un destino especifico.

- Es un conjunto de reglas que el programa debe seguir para que la red de internet pueda enviar datos de un lugar a otro.

## ¿Que es un Protocolo? - Protocolos de Red

> Define el formato y el orden de los mensajes intercambiados entre 2 o mas entidades de comunicación, tambien como las acciones tomadas por la transmisión y/o el recibo del mensaje u otro evento.

El internet, y redes de computadoras en general, hace uso de muchos protocolos. Distintos protocolos son utilizados para cumplir distintas tareas de comunicación.
