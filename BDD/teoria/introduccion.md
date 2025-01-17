# **Definición de Base de Datos**

> Una **base de datos** es un conjunto de datos interrelacionados.

- **Dato:** Un dato es un hecho que puede ser representado y almacenado de alguna forma, y que tiene un sentido implícito.

## **Bases de datos tradicionales y no tradicionales

> Las bases de datos **tradicionales** almacenan datos de **texto o numéricos**, que pueden enunciarse a través de **proposiciones**.

**Ejemplo:**

- Juan Martín Del Potro ganó el Abierto de Estados Unidos en 2009.
- Gabriela Sabatini  ganó el Abierto de Estados Unidos en 1990.
- Roger Federer ganó el Abierto de Australia en 2018. 

![NOTE]
> Se puede ver como la estructura es: 
> *persona* ganó *torneo* en *año*
> Un conjunto de proposiciones que tienen la misma estructura puede tipificarse a travès de un **predicado**.


- **Predicado:** Función que toma uno o más argumentos y devuelve un valor de verdad.


Entonces, si tuvieramos el predicado GanadorDelGrandSlam(persona, torneo, año) podríamos ver que proposiciones son verdaderas.


![WARNING]
> Las bases de datos sólo almacenan proposiciones verdaderas, de lo contrario se tendrìan que guardar potencialmente infinitos datos.

Actualmente las bases de datos también almacenan tipos de datos màs complejos como imágenes, audio, video o datos geoespaciales $\rightarrow$ **BDD no tradicionales**.


## Sistemas de Gestión de Bases de Datos (SGBD)


> Es un conjunto de programas que gestiona y controla la **creación**, **manipulación** y **acceso** a la base de datos.


El **SGBD** provee un nivel de **abstracción** entre los programas o sistemas de información y los datos, resolviendo el problema conocido como **dependencia de datos.**

- **Independencia de los datos:** Es la propiedad del **SGBD** consistente en que cambios en la estructura de la base de datos no repercutan en los programas o sistemas de información que utilizan.

## Arquitectura de 3 capas ANSI/SPARC

> Arquitectura de 3 niveles de abstracción para la descripción/representación de los datos de una BDD.


![Arquitectura de 3 capas]("img/arquitectura_3_capas.png")

- **Nivel Interno**: Como se organiza la información de forma fisica. Tipos de archivo, tamaños de bloque, etc. $\rightarrow$ Como perciben los datos el SO y el SGBD.

- **Nivel Conceptual:** Que datos va a tener la BDD y en base a esto el nivel interno creará archivos y tamaños correspondientes. $\rightarrow$ Semàntica de los datos abstrayendose de la implementación. Describe entidades, tipos de datos, operaciones y restricciones de seguridad e integridad.

- **Nivel Externo:** Distintos subconjuntos de todos los datos disponibles para el uso que le quiere dar el usuario de esa vista. $\rightarrow$ La forma en que los usuarios perciben los datos.

## Funciones de los SGBDs

1. **Almacenamiento y Consulta:** Ofrecer estructuras eficientes y un lenguaje de consulta.

2. **Integridad**: Asegurar la integridad de datos a través de restricciones.

3. **Seguridad:** Evitar accesos no autorizados.

4. **Concurrencia:** Permitir el acceso en simultáneo de muchos usuarios.

5. **Recuperación:** Ofrecer herramientas para la recuperación ante fallas.

6. **Soporte transaccional**
