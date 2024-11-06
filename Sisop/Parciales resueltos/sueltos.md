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

- Como $1 \text{Kb} = 1024 \text{bytes}$, el tamaño de cada bloque es de $4 \times 1024 \text{bytes} = 4096\text{bytes}$.
- Como es necesario guardar metadata, debe haber la misma cantidad de inodos que de bloques, por lo que la cantidad de inodos por bloque sabiendo que este ocupa $256 text{bytes}$ es de: $\frac{4096 \text{bytes}}{256 \text{bytes}} = 16$.
- La cantidad de bloques de inodos debe ser de $\frac{512}{16} = 32$ ya que en cada bloque entran 16 inodos.
- Teniendo todo esto en cuenta obtenemos qué el sistema de archivos queda definido como:

1. 1 super bloque
2. 1 bloque de bitmap de inodos
3. 1 bloque de bitmap de bloques
3. 32 bloques de inodos
4. 477 bloques de datos $\rigtarrow$ $477 + 1 + 1 + 1 + 32 = 512$

> [!NOTE]
> El ejercicio no lo pide, pero como se tienen 477 bloques de datos, el tamaño de archivo más grande es de $477 \times 4 \times 1024 \text{bytes} = 1953792 \text{bytes} = 1.95 \text{Mb}$

## Suponiendo que BigFS es una variante de FSS, el clasico sistema de archivos de unix, el cual posee 12 referencias inodos directas, 1 indirecta, 1 doble indirecta, una triple indirecta y una cuadruple indirecta. Asumiendo bloques de 4KB, 8 byte por puntero a bloques ¿Cuál es el máximo tamaño de archivo que se soporta? Expresar en Terabytes

> [!WARNING]
> Preguntar.

# Memoria

## Explicar el mecanismo de address translation de memoria virtual paginada de tres niveles de  indirección de 32 bits. Indique la cantidad de direcciones de memoria que provee, una virtual address: 

![Virtual Address](img/virtual_address_suelto.png)

> [!WARNING]
> Hacer.

## Traducir las direcciones virtuales a físicas: [00000000], [2000220002], [10222002], [00015555] suponiendo lo siguiente:

- 4 bit para el segment number.
- 12 bit para el page number.
- 16 bit para el offset.

![Tabla de traducciones](img/tabla_traducciones_sueltos.png)

Las direcciones de memoria estan en hexadecimal, por lo que:
- El primer dígito representa el Segment table.
- Los siguientes 3 dígitos representan el Page table.
- El resto de los dígitos representan el offset.

Entonces:

- 00000000 $\rightarrow$ Mapea a la Página B, donde el page table es F00, entonces la dirección física es F00 + 0000 = F0000000.
- 20022002 $\rightarrow$ Como el primer dígito no mapea a ninguna página del segment table, la dirección es invalida.
- 10022002 $\rightarrow$ Mapea a la Página A, donde el page table es BEEF, entonces la dirección física es BEEF + 2202 = BEEF2202.
- 00015555 $\rightarrow$ Mapea a la Página B, donde el page table es D8BF, entonces la dirección física es D8BF + 5555 = D8BF5555.


## Considere un sistema x86 de memoria virtual paginada de dos niveles con un espacio de direcciones de 32 bits, donde cada pagina tiene un tamaño de 4096 bytes. Un entero ocupa 4 bytes y se tiene un array de 50,000 enteros que comienza en la dirección virtual 0x01FBD000. Si el arreglo se recorre completamente accediendo a cada elemento una vez ¿A cuantas páginas distintas necesita acceder el sistema operativo para conseguir esto?

> [!WARNING]
> Preguntar.

## Dado un espacio de direcciones virtuales con direcciones de 8 bits y páginas de 16 bytes, asume un array de 12 enteros (cada uno de 4 bytes) comenzando en la dirección virtual 100. Calcula el patrón de aciertos y fallos en la TLB cuando se accede a todos los elementos del array en un bucle. Asume que inicialmente, la TLB está vacı́a.

- La dirección virtual inicial es 100, mientras que la final es 100 + 12 * 4 = 148.
- Pasando las direcciones virtuales a binario obtengo qué son las siguientes 100 = 0110 0100 y 148 = 1001 0100.
- Cómo los primeros 4 bits representan el page number puedo decir que la primer página accedida es la 0110 y la última es la 1001. Pasando estos números de binario a decimal obtengo que son 6 y 9 respectivamente.
- Finalmente, el arreglo de enteros accede a las páginas 6, 7, 8 y 9. Por lo que el patrón de aciertos y fallos en la TLB es el siguiente:
1. 4 Fallos ya que cada uno corresponde a la primera vez que se accede a una de las páginas.
2. 8 aciertos que corresponde a la diferencia entre la cantidad de accesos totales y los fallos, es decir, $12 - 4 = 8$.
