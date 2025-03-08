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

# La frontera de la red.

- Los hosts se pueden dividir en 2 categorias $\rightarrow$ cliente y servidor. Donde el cliente suele ser una PC, un smartphone, etc y el server tiende a ser una maquina mas potente que almacena y distribuye paginas web, servicios de streaming de video, entre otros.

## Redes de acceso (Access Networks)

> La red que se conecta fisicamente a un end system con el primer router (edge router) a un camino entre el end system y cualquier otro end system.


- En la actualidad los tipos de acceso residencial de banda ancha mas frecuentes son la Linea de Abonado Digital (Digital Subscriber Line o DSL) y cable.

### DSL

- Una residencia en general obtiene acceso a internet DSL a través de la misma compañía de telefonía local que provee su propio servicio cableado de telefono.

- Cada modem DSL usa una linea existente de telefono para intercambiar data con un **Digital Subscriber Line Access Multiplexer (DSLAM)** localizado en la oficina central de la compañía.

- Esto se logra cambiando con las frecuencias en la que se transmiten los paquetes, utilizando un único link.


![DSL](img/DSL.png)

### Internet por cable.

- Por otro lado, el **acceso a internet por cable** hace uso de la infraestructura cableada de la compañia de televisión.

- Como en este sistema se utilizan cables de fibra y coaxiales (fiber and coaxial cables) suele ser referida como Hybrid Fiber Coax network (HFC).

- Este tipo de acceso a internet requiere de modems especiales (cable modems).

- Se suele conectar a una PC via Ethernet. Tiene dos canales uno de subida y otro de bajada.

- Algo particular de este tipo de red es que el medio de transmisión es compartido. Cada paquete enviado desde una casa viaja en el canal de subida hasta el final, y viceversa, es por esta razon que si muchos usuarios estan descargando videos a la vez el ratio de descarga sea menor.

![Internet Por Cable](img/internet_por_cable.png)

### FTTH

Si bien estas alternativas son las mas usadas, existe una nueva tecnología que provee velocidades de internet mas altas: **Fiber To The Home (FTTH)**.

- El concepto es simple, proveer un camino de fibra optica desde la oficina central hasta la casa.

- En general una misma fibra es compartida entre varias casas, pero, una vez "cerca" se dividen para cada una. Hay 2 arquitecturas que compiten para hacer esta división: **Active Optical Networks (AOP)** y **Passive Optica Networks (PON)**.

![FTTH](img/ftth.png)


#### Arquitectura PON

En la imagén se puede ver la arquitectura PON, cada casa tiene un **Optical Network Terminator (ONT)** que esta conectado por fibra optica dedicada a un splitter dentro de la zona residencial.

El Splitter combina un cierto numero de casas en una sola, compartiendo la fibra optica, la cual se coencta a una Optical Line Terminator (OLT) que se encuentra en la oficina central de la empresa.


#### Acceso en la empresa (y en el hogar): Ethernet y WiFi.

En muchas lugares se usa una red LAN (Local Area Network) para concetar un end system con un edge router. Hay muchas teconologías LAN, sin embargo, la mas usada es Ethernet.

![Ethernet](img/ethernet.png)

Otro tipo de red LAN es la wireless LAN, conocido como **WiFi**.

#### Acceso Wireless en areas extensas: 3G y LTE.

A diferencia del WiFi el usuario necesita estar a unos pocos kilometros de la estación base.


## El medio físico.

- Cuando un bit pasa por varios routers y links, pasando así por varios pares transmisor-receptor. Por cada par, el bit propaga ondas electromagneticas o pulsos opticos a traves de  un **medio físico**.


El medio físico tiene 2 categorias: **guided media** y **unguided media**.


| Guided Media|Unguided Media|
| -------------------|--------|
| Las ondas son guiadas a través del medio solido, como el cable de fibra optica o cable coaxial| Las ondas se propagan en la atmosfera y el espacio, como en Wireless LAN (WiFi) o un canal digital satelital |

> [!NOTE]
> El costo de un link físico (cable de cobre, cable de fibra optica, etc) suele ser menor comparado con otros costos de redes.

### Twisted-Pair Copper Wire.

- El medio mas barato y utilizado de transmisión guiada.

> [!HINT]
> Un ejemplo fácil de recordar es el teléfono de cable.

- 2 conductores de cobre en paralelo.

- Se usa también para internet residencial.

### Coaxial Cable.

- 2 conductores de cobre concentricos

- Utilizado en sistemas de cableado de televisión.

- Puede ser utilizado como un **medio guiado compartido**.


### Fiber Optics.

- La fibra óptica es un medio flexible y fino que conduce pulsos de luz, donde cada pulso representa un bit.

- Son inmunes a la interferencia electromagnetica.

### Terrestrial Radio Channels.

- Los canales de radio llevan señales en el espectro electromagnetico.

- NO requiren cable físico para ser instalados, pueden atravesar paredes y proveer conectividad a los usuarios mobile.

- Dependen del medio de propagación, lo cual puede aumentar y/o disminuir la fuerza de la señal.

Los canales de radio terrestres se pueden clasificar en 3:

1. Aquellos que operan en distancias cortas $\rightarrow$ 1 o 2 metros.

2. Aquellos que operan en areas locales $\rightarrow$ generalmente entre decenas y uno pocos cientos de metros.

3. Aquellos que operan en un gran area $\rightarrow$ generalmente entre decenas de kilometros.

## The Network Core.

























