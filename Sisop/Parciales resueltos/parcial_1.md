# Memoria virtual

## ¿Que es la memoria virtual?
- La memoria virtual es la abstracción que provee el SO sobre la memoria física. Esta abstracción permite la multiprogramación, es decor, que el SO puede tener en memoria más programas que la cantidad de memoria física disponible.

Para esto, el SO cumple un rol de `ilusionista` en el cual *hace creer* a los procesos que tienen toda la memoría física disponible para usar, cuando en realidad no es así.

Para poder realizar esta abstracción el SO debe proveer:

1. Transparencia: Los procesos no deben notar que es lo que está ocurriendo y no deben cambiar nada de su implementación debido a esto.

2. Eficiencia: El SO debe ser capaz de realizar esta abstracción de forma eficiente, es decir, que no debe ser costoso en términos de tiempo y recursos.

3. Protección: El SO debe ser capaz de proteger a los procesos de otros procesos y del mismo SO. Es decir, que un proceso no pueda acceder a la memoria de otro proceso.

## ¿Que mecanismos conoce, describa los 3 que le parezcan mas relevantes?

### Base and Bounds

- **Base and Bounds** es un mecánismo en el cual se divide a la memoria utilizando dos registros de procesador, el registro `base` y el registro `bound`. 

- El registro `base` indica la dirección de inicio de la memoria del proceso y el registro `bound` indica el tope de la memoria del proceso. De esta forma, cuando un proceso intenta acceder a una dirección de memoria, el SO verifica que la dirección de memoria esté dentro del rango de memoria del proceso, y de no hacerlo lanza un error.

- Si bien esta forma no es muy eficiente, es sencilla de implementar.

### Segmentación

- La idea de segmentación es muy similar a **Base and Bounds**. En particular se puede notar que un proceso puede estar compuesto de un stack, un heap y un código (los datos que lo componen). Por lo que se puede dividir la memoria en segmentos, cada uno correspondiente a una parte del proceso. 

- Es decir, el procesador debe proveer 3 pares de registros de tipo base and bounds para poder implementar segmentación, donde cada par contiene el rango válido de direcciones de memoria para el stack, el heap y el código/texto.

- Para saber que segmento se debe usar, se toma los primeros 2 bits para indicar el segmento y los demás como un offset.

- Hoy en día se sigue utilizando, aunque cada vez menos. El problema que tiene la `segmentación` es la **fragmentación externa** ya que pueden quedar espacios contiguos libres que no sean lo suficientemente grandes como para asignarlos a un segmento. Si bien hay formas de resolver este problema (**compactación**) esta operación es muy costosa.

![Imagen fragmentación externa](img/frag_externa_p1.png)

### Memoria paginada de nivel 1

- En este macanismo se divide la memoria en bloques de tamaño fijo (suelen ser de 4kb). Para este mecánismo se le debe indicar a la **Memory Management Unit**(MMU) donde se encuentra la **Page Table** donde se encuentra la información para traducir las direcciones virtuales en físicas.

- La traducción consta de tomar los 20 bits más altos (teniendo direcciones de 32 bits y págines de 4kb) para indicar la página, luego los 12 restantes son el offset dentro de la misma.

- Si bien este mecanismo resuelve los problemas anteriores de fragmentación de la memoria, por otro lado trae un nuevo problema. Al tener que guardar la Page Table, esta constará de un arreglo de $2^{20}$ entradas por proceso, lo cual es muy costoso.

> ![NOTE]
> La MMU es un componente del procesador que se encarga de traducir las direcciones virtuales a direcciones físicas.

## Explicar el mecanismo de address translation **memoria virtual paginada** de tres niveles de indirección de 32 bits. Indique la cantidad de direcciones de memoria que provee la virtual address de la imágen con tablas de registros de 4 bytes.


El esquema de **memoria virtual paginada de tres niveles** es el siguiente:

![Esquema memoria paginada de 3 niveles](img/esquema_memoria_paginada_p1.png)

Los primeros 20 bits indican las tablas mientras que los últimos 12 el desplazamiento dentro de la página.

- La cantidad de direcciones de memoria que provee es de $2^{32}$ ya que en el primer y segundo nivel se tendrán $2^{7}$ entradas, y en el tercero $2^{6}$

