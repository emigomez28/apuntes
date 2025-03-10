# Retardos, perdidas y tasa de transferencia en las redes de conmutación de paquetes

## Retardos

- Los paquetes se inician en un host, atraviesan una serie de router y terminan en otro host. En el medio, el paquete sufre distintos tipos de retardos.

- Los más importantes son el **retardo de procesamiento nodal**, el **retardo de cola**, el **retardo de transmisión** y el **retardo de propagación**.

- La suma de todos estos retardos se conoce como **retardo nodal total**.

![Ejemplo de retardo nodal](img/retardo_nodal_ejemplo.png)

### Retardo de procesamiento

- Tiempo requerido para examinar el header del paquete y determinar donde hay que enviarlo.

- Puede incluir otros factores como: Tiempo necesario para comprobar los errores de nivel de bit del paquete que se hayan producido al transmitir los bits.

- Una vez efectuado el procesamiento nodal, el router dirige el paquete a la cola situada antes del enlace que lleva al siguiente router.

### Retardo de cola

- Se da cuando el paquete espera en el buffer para ser transmitido a través del siguiente link.

- La duración de este retardo depende de cuantos paquetes esten encolados antes.

### Retardo de transmisión

- Los paquetes suelen ser transmitidos en orden de llegada.

- El **retardo de transmisión** será igual a $\frac{L}{R}$ siendo L la longitud del paquete en bits y R la velocidad de transmisión del enlace del router origen al router destino.

### Retardo de propagación

- Una vez un bit del paquete entró al enlace, el retardo de propagación es el tiempo que tarda en propagarse desde el router origen al destino. Esto depende fuertmente del medio físico del enlace

- Se calcula como $\frac{d}{s}$ siendo d la distancia entre los routers y s la velocidad de propagación del enlace.

| Retardo de transmisión | Retardo de propagación |
| ---------------------- | ----------------------
| Cantidad de tiempo necesario para que el router saque fuera el paquete| Tiempo que tarda un bit en propagarse de un router al siguiente|

Entonces:

$$d_{\text{nodal}} = d_{\text{proc}} + d_{\text{cola}} + d_{\text{trans}} + d_{\text{prop}}$$

## Retardo de cola y pérdida de paquetes

- Depende del tráfico de la red. Si 10 paquetes llegan al mismo tiempo, el primer paquete encolado no tendra $d_{\text{cola}}$, sin embargo, el último tendrá muchisimo.

- Hay casos donde el retarde de cola puede ser despreciable o no, esto depende de velocidad media de transmisión La.

- Si R es la velocidad de transmisión, se denomina **intensidad de tráfico** a $\frac{La}{R}$.

1. Si $\frac{La}{R} > 1$ entonces la velocidad media de los bits que llegan a la cola excede la velocidad a la que los bits pueden ser transmitidos $\rightarrow$ cuello de botella.

> [!WARNING]
> Siempre la red debe ser diseñada con $\frac{La}{R} \leq 1$

2. Para el caso de $\frac{La}{R} \leq 1$ la naturaleza del tráfico entrante influye sobre el retardo de la cola.

- Si cada paquete llega periodicamente ($\frac{L}{R}$) a una cola vacia entonces el retardo es 0.

- Si cada paquete llega periodicamente en ráfagas el primer paquete tendrá retardo 0, el segundo $\frac{L}{R}$ y el paquete n tendrá $(n - 1) \cdot \frac{L}{R}$

> [!NOTE]
> Estos casos son improbables ya que los datos llegan aleatoriamente, pero sí dan una idea de que ocurre con el **retardo medio**.

### Pérdida de paquetes

- Un paquete se pierde cuando el buffer esta lleno y el router decide eliminar el paquete entrante.

- El porcentaje de paquetes perdidos aumenta a medida que crece la intensidad del tráfico. El rendimiento de un nodo no suele medirse solo a partir del retardo sino tambien en función de la probabilidad de pérdida de paquetes.

## Retardo extremo a extremo

> Retardo total entre el origen y el destino.

$$d_{\text{extremo-extremo}} =  N \cdot (d_{\text{proc}} + d_{\text{trans}} + d_{\text{prop}})$$

> [!HINT]
> Es una generalización del retardo nodal y tomando condiciones "perfectas".

### Traceroute

> Para ver el orden de magnitud del retardo extremo a extremo de una red utilizar Traceroute

### Retardos de los sistemas terminales, de las aplicaciones y otros.

- En los sistemas terminales pueden existir retardos adicionales significativos.

Ejemplo:

1. Transmitir paquetes por medios compartidos $\rightarrow$ modem por cable o WiFi.

2. Retardo de empaquetamiento del medio en aplicaciones de Voz sobre IP (VoIP, Voice Over IP). El lado emisor debe relenar un paquete con voz digitalizada codificada antes de enviar el paquete, el tiempo en el que se tarda en rellenar ese paquete puede influir sobre la calidad recibida.


## Tasa de transferencia en las redes de computadoras.

- **Tasa de transferencia instantanea:** Velocidad a la que el host destino recibe un paquete.

- **Tasa media de transmisión:** $\frac{F}{T}$ bits/s siendo F la cantidad de bits y T la duración de la transferencia.

















