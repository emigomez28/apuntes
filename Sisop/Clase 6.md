# Memoria

> Address Space: Es el conjunto de todas las direcciones de memoria posibles.

## Virtualización de la Memoria

- Pasar de una dirección virtual a una dirección física.
- Esta tarea debe ser eficiente.
- Debe ser transparente, el usuario no tene porque saber que existe la memoria virtual.
- Debe ser flexible, ya que todo proceso cargado en memoria puede reubicarse a otro espacio de memoria.
- Por otro lado, debe poder proteger la memoria.

> Todo esto se hace por **HARDWARE**, si se hiciese por **SOFTWARE** sería muy poco eficiente.

## Address Translation

Con esta técnica de hardware transforma cada acceso a memoria, transformando la dirección de memoria virtual a una dirección de memoria física.

- Por cada una de las direcciones virtuales se debe poder mapear una memoria física, por eso debe ser eficiente.

El sistema operativo debe:

1. Setear al hardware de forma correcta para que se pueda dar lugar a la traducción.
2. Manejar la memoria, manteniendo información de en que lugar hay áreas libres y en que lugar hay áreas en uso.
3. Intervenir de manera criteriosa como mantener el control sobre toda la memoria usada.

> El SO configura al **HARDWARE** para que haga las traducciones.

# Implementaciones de memoria virtual.

## Base and Bound (Segmentación)

- Usaba 2 registros de control de procesador (Base y Bound), donde **Base** era "donde empieza", por lo que todas las direcciones podían verse como un offset desde la base, mientras que **Bound** era el límite superior, donde si por algún motivo el programa se pasaba del mismo, se levantaba una excepción (mecanismo de seguridad).

- Esto venía a solucionar el poder alocar dinamicamente programas en memoria. En tiempo de ejecución, el SO asignaba una dirección **Base** al programa ya compilado, luego este realizaría todas sus operaciones como desplazamientos desde la base, si ese desplazamiento era demasiado grande se levantaba una excepción utilizando **Bound**.

## Address Translation con Tabla de Segmentos

Es una mejora a la implementación anterior, la idea es que cada programa pueda tener varios segmentos. Acá los segmentos entre programas podían solaparse entre sí, por lo que si 2 tablas apuntaban a la misma memoria física podían compartir recursos, como por ejemplo librerías.

## Memoria Paginada

Las páginas y frames tienen el mismo tamaño $\rightarrow$ 4 KB.

> Páginas hace refencia a porciones de la memoria virtual mientras que Frames hace referencia a porciones de la memoria física.

Con la paginación la memoria es reservada en pedazos, que en la **page table** esta bien organizado, ahora si se mira la memoría física ese orden no tiene por que cumplirse.

### Como funciona la traducción

> Todas las direcciones físicas de n bits (32, 64 o 128 dependiendo la arquitectura) se divide principalmente en 2 partes:

- **Page Number**: Indica que página se está referenciando. Donde tiene la dirección de memoria física que le corresponde, es decir, el Frame.

- **Offset**: El byte al que se quiere acceder, de estar bien armada la traducción este valor debe ser exactamente igual que uno en el frame.

El problema con esto es que se deben mantener muchas direcciones las cuales podrían estar vacías.

## Memoria Paginada Multinivel

- Cada página esta compuesta por distintos niveles, los cuales son sub tablas. Luego, ciertos datos podrían estar guardados en distintos niveles, por lo que cad nivel tiene una referencia a su siguiente. Después del tercer nivel se accede al frame.

- Esto sirve para resolver el problema anterior, si la tabla de nivel 1 tiene una referencia a la tabla de nivel 2 y sucede que esta vacía, esto significa que al menos 1000 espacios de memoria estan vacíos, por lo que no es necesario mantenerlos.

# Ejemplo de traducción en x86

Dada la dirección de memoria **0x01FBD000** traducir la dirección de memoria.

- Paso todo a binario: **0x01FBD000** = **00000001111110111101000000000000**
- Agrupo los bits en 3 partes: **0000000111 1110111101 000000000000**
- Pasando los números a decimal se obtiene: 7, 957 y 0.

Cada parte representa:
1. **Page Directory Entry**: Es el indice de la tabla de nivel 1. Lo que tiene principalmente es la dirección de la siguiente página de tablas.

2. **Page Table Entry**: Es el indice de la tabla de nivel 2. Lo que tiene principalmente es la dirección de la pagina que nos interesa.

3. **Offset**: Es el desplazamiento dentro de la página que estamos buscando.

# Ejemplo de parcial
## Enunciado

> Considere un sistema x86 de memoria virtual paginada de 2 niveles con un espacio de direcciones de 32 bits, donde cada página tiene un tamaño de 4096 bytes.

> Un entero ocupa 4 bytes y se tiene un array de 50.000 enteros que comienzan en la dirección virutal 0x01FBD000

> El arreglo se recorre completamente, accediendo a cada elemento una vez. En este proceso ¿a cuántos frames de memoria física distintos (no la cantidad total de accesos) necesita acceder el sistema operativo para conseguir eso?

> Recuerde contar las tablas de páginas intermedias, no solo las páginas que contienen los elementos del array.

## Resolución

- Cantidad total de bytes que ocupa el array: 50.000 $\times$ 4 = 200.000 bytes.
- Posición del primer byte: 0x01FBD000 (por enunciado)
- Posición del últimobyte: 0x01FBD000 + (50.000 $\times$ 4 - 1) = 0x01FEDD3F

Entonces, el rango de direcciones que el sistema operativo va a recorrer es: 0x01FBD000 - 0x01FEDD3F

> Ahora convertimos ambos valores a binario:

- 0x01FB0000 = 0000000111 1110111101 000000000000
- 0x01FEDD3F = 0000000111 1110111110 110111001111

> Expresados en decimal para mayor claridad:

- 0x01FB0000 = 7, 957, 0
- 0x01FEDD3F = 7, 1005, 3391 

En el primer nivel (el directorio de páginas) se usa un solo registro en el índice 7. Este contiene la dirección (y metadata) de la **única tabla de páginas** que será necesaria en el segundo nivel. Si se requieren más tablas de páginas de segundo nivel, este indice cambiaría para algunas direcciones involucradas en el rango. Esto noocurre para el rango propuesto en este ejercicio.

Entonces, como en el segundo nivel siempre se indexa en la misma página la cantidad total de páginas a utilizar es 1.

# Translation Lookaside Buffer (TLB)

Es una caché la cual contiene el **page number** y el frame que corresponde. Es una optimización para no ir a buscar los datos directamente a memoria.
