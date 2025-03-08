# The Network Core

## Packet Switching (Multiplexación de paquetes)

- En una aplicación de red los end systems intercambian **mensajes** entre sí. 

- Para enviar un mensaje de un lugar a otro, la fuente transforma mensajes grandes en partes mas pequeñas llamadas **paquetes**.

- El paquete viaja entre links de comunicación y "packet switches" $\rightarrow$ Para esto hay 2 tipos predominantes, **routers** y **link-layer switches**.

- Los paquetes son transmitidos al ratio igual al total del ratio de transmisión del link.

> [!NOTE]
> Si un source end system o un packet switch envia un paquete de L bits por un link que tiene una transmición de R bit/sec, entonces el tiempo de transmisión del paquete es de $\frac{L}{R} \text{ s}$.

### Store and Forward Transmission.

- La mayoría de packet switches usan una transmisión **store and forward**.

- Esto significa que el packet switch debe recibir el paquete entero antes de empezar a transmitir el primer bit del paquete a otro link.

#### Ejemplo sencillo de delay con 1 solo paquete

> Si mandamos L bits a un ratio de R bits por segundo, la fuente empieza a transmitir en el segundo 0 y al segundo $\frac{L}{R}$ termina de enviar el paquete. Como el router ya recibió todo el paquete puede empezar a enviarlo y al segundo $2 \cdot \frac{L}{R}$ el paquete llega a destino. El delay total es de $2 \cdot \frac{L}{R}$. En caso de que apenas llega el primer bit al router se empieza a enviar el paquete, el delay total sería de $\frac{L}{R}$ (Esto último no sería Store and Forward)

#### Ejemplo sencillo de delay con 3 paquetes

> Al igual que antes, sabemos que el tiempo en el que se empieza a transmitir el primer paquete es el segundo 0 y llega completo al router en el segundo $\frac{L}{R}$ y empezará a enviar el segundo paquete, ahora, como el primer paquete esta completo este se empieza a enviar a destino y llegará en un tiempo $2 \cdot \frac{L}{R}$ y, a su vez, el segundo paquete llegará a destino en $3 \cdot \frac{L}{R}$. Finalmente todos los paquetes llegaran en un tiempo total de $4 \cdot \frac{L}{R}$.


Entonces, para el caso general de N links podemos decir qué:

$$d_{\text{end to end}} = N \cdot \frac{L}{R}$$

### Queuing Delays and Packet Loss

- Cada paquet switch tiene multiples links y por cada uno tiene un **output buffer** o **output queue**, el cual almacena paquetes que el router esta por re-enviar a un link.

- En caso de que ese link este ocupado debe esperar en el output buffer $\rightarrow$ **queuing delays**.

- Como el tamaño del buffer es limitado, si el paquete encuentra que el buffer esta completo ocurrirá **packet loss**, lo cual significa que o bien el mimso paquete que llego o uno que ya estaba esperando en la fila será descartado.


![Ejemplo de Queuing](img/queuing_ejemplo.png)

### Forwarding Tables and Routing Protocols.

- Muchas maneras de hacer forwarding y elegir el link para hacer forward a un paquete.

- Todo end system tiene una dirección IP y cuando un source system quiee enviar un paquete a un destino, la fuente incluye la dirección IP en el header del paquete. 

- Una vez llegado al router, examina la dirección destino y lo re-envia a un router adyacente. Cada router tiene una **forwarding table** que mapea direcciones destino con el link de salida del router.

- Existen **routing protocols** que son usados para automaticamente crear **forwarding tables**.

### Conmutación de Circuitos.

- 2 metodos fundamentales para transportar datos a traves de una red de enlaces y coinmutadores $\rightarrow$ **conmutación de circuitos** y **conmutación de paquetes**.

| Conmutación de Circuitos|Conmutación de Paquetes|
| ------------- |----------|
| Los recursos necesarios a traves de la ruta (buffers, velocidad de tranmissión del link, etc) que permiten establecer comunicación entre los end system están reservados durante el tiempo que dura la sesión| Estos recursos no están reservados, los mensajes de una sesión utilizan los recursos bajo petición y pueden tener que esperar para poder acceder a un link de comunicación|


#### Multiplexación en redes de conmutación de circuitos

- Los circuitos de enlace se implementan mediante **multiplexación por división de frecuencia (FDM)** o mediante **multiplexación por división de tiempo (TDM)**.


![FDM vs TDM](img/fdm_vs_tdm.png)

- La conmutación de paquetes es mas eficiente en ciertos casos, ya que al ser por demanda no se desperdician recursos.

## Una red de redes




