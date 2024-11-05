# File System
## Diseñe un vsfs para un sistema de 1024 bloques, cada bloque ocupa 4kb y cada inodo 256 bytes. Asumir que en un disco de N bloques entran N archivos.

- **Cantidad de inodos necesarios:** 1024.
- **Cantidad de inodos por bloque:** $\frac{4096}{256} = 16$ ($1024 \times 4 = 4096$).
- **Cantidad de bloques de inodos:** $\frac{1024}{16} = 64$.
- **Cantidad de bitmap:** $4096 \times 8 = 32768$. (Sobra! El disco tiene 1024 bloques y 1024 inodos)

Entonces:

- 1 Superbloque.
- 1 bloque de bitmap de inodos.
- 1 bloque de bitmap de bloques.
- 64 bloques de inodos.
- 957 bloques de datos ($1024 - 64 - 1 - 1 = 957$)

## Sea un disco que posee 512 bloques de 4kb y un sistema operativo cuyos inodos son de 256 bytes, definir un sistema de archivos FFS.

> [!WARNING]
> Preguntar.

## Suponiendo que BigFS es una variante de FSS, el clasico sistema de archivos de unix, el cual posee 12 referencias inodos directas, 1 indirecta, 1 doble indirecta, una triple indirecta y una cuadruple indirecta. Asumiendo bloques de 4KB, 8 byte por puntero a bloques ¿Cuál es el máximo tamaño de archivo que se soporta? Expresar en Terabytes

> [!WARNING]
> Preguntar.

# Memoria

## Explicar el mecanismo de address translation de memoria virtual paginada de tres niveles de  indirección de 32 bits. Indique la cantidad de direcciones de memoria que provee, una virtual address: 

![Virtual Address](img/virtual_address_suelto.png)

> [!WARNING]
> Hacer.

## Traducir las direcciones virtuales a físicas: [0000000], [2000220002], [10222002], [00015555] suponiendo lo siguiente:

- 4 bit para el segment number.
- 12 bit para el page number.
- 16 bit para el offset.

![Tabla de traducciones](img/tabla_traducciones_sueltos.png)

> [!WARNING]
> Hacer.

## Considere un sistema x86 de memoria virtual paginada de dos niveles con un espacio de direcciones de 32 bits, donde cada pagina tiene un tamaño de 4096 bytes. Un entero ocupa 4 bytes y se tiene un array de 50,000 enteros que comienza en la dirección virtual 0x01FBD000. Si el arreglo se recorre completamente accediendo a cada elemento una vez ¿A cuantas páginas distintas necesita acceder el sistema operativo para conseguir esto?

> [!WARNING]
> Preguntar.

## Dado un espacio de direcciones virtuales con direcciones de 8 bits y páginas de 16 bytes, asume un array de 12 enteros (cada uno de 4 bytes) comenzando en la dirección virtual 100. Calcula el patrón de aciertos y fallos en la TLB cuando se accede a todos los elementos del array en un bucle. Asume que inicialmente, la TLB está vacı́a.

> [!WARNING]
> Preguntar.
