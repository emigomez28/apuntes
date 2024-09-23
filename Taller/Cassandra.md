# Bases de datos distribuidas

> Una base de datos distribuida es aquella en la cual los datos estan almacenados de forma distribuida, es decir, almacenada a lo largo de diferentes lugares físicos. Esto puede ser tanto en multiples computadoras ubicadas en el mismo lugar o dispersos en una red de computadoras interconectadas.

## Cassandra Query Language (CQL)

**CQL** ofrece un modelo similar a **SQL**, los datos estan almacenados en **tablas** las cuales contienen **filas** y **columnas**.

### Identificadores

- Usa identificadores (o nombres) para identificar las tablas, columnas y otros objetos.

> Identificador: Token que matchea la regex `[a-zA-Z_][a-zA-Z0-9_]*`

### Keywords

- Algunos identificadores son palabras reservadas (keywords) como **SELECT** o **with**.A

- Los identificadores y los keywords son **case-insensitive**. Por convención, los keywords son escritos en mayúsculas y los identificadores en minúscula..

### Quoted identifiers.

- Son un tipo de identificador (nunca keywords) los cuales contienen una serie de caracteres no nula entre comillas dobles. Por ejemplo `"my table"`.

- Estos identificadores son **case-sensitive**.

### Constantes

- String constant es una secuencia arbitraria de caracteres entre comillas simples. Otra alternativa es usar signos de dolar ($), en este caso la comilla simple sola no se escapa del string. `'It''s raining today'` es lo mismo qué `It's raining today` entre signo de dolar.

- Luego boolean, int y float estan definidos como se esperaría. Es importante notar que float admite constantes especiales como `NaN` o `Infinity`.

- CQL soporta constantes UUID.

- CQL soporta constantes de tipo blob. Blob es una constante que representa un número hexadecimal.

- La constante NULL es soportada.

### Definición de Datos

- CQL almacena la data en **tablas**.
- Las tablas estan almacenadas en un **keyspaces**.
- Un **keyspace** define opciones que aplican a todas las tablas del mismo.

> Replication Strategy: Define el factor de replica para que los datos sean esparcidos a lo largo de todo un cluster.

- Una regla general es un solo keyspace por aplicación.
- Es común para un cluster definir solo un keyspace para una aplicación activa.

### Replication Strategy

#### SimpleStrategy

- No es buena para producción ya que podría variar la latencia de las querys pero es sencilla.

#### NetworkTopologyStrategy

- Es mejor para producción, setea el factor de replica independientemente para cada data-center. El resto son pares clave-valor donde la clave es el nombre del data-center y el valor es el factor de replica.

### Más sobre keyspaces

- El statement **USE** permite cambiar el keyspace actual al seleccionado.
- **ALTER KEYSPACE** permite modificar las opciones de un keyspace.
- **DROP KEYSPACE** permite eliminar un keyspace.
- **CREATE TABLE** permite crear una tabla en un keyspace.

### Definición de Tablas

#### Definición de Columnas

- Cada fila en CQL tiene predefinida columnas definidas en la creación de la tabla.
- Una **column_definition** es compuesto del nombre de la columna y su tipo, restringiendo los valores que acepta.
- Una **column_definition** puede tener los modificadores:
  - **STATIC**: Define una columna estática, una estática será compartida por todas las filas en la misma "porción".
  - **PRIMARY KEY**: Define la clave primaria de la tabla. Una fila es identificada por su clave primaria. Esta compuesta por **partition key** y **clustering columns**.

### Manipulación de Datos

- Mismas operaciones que SQL (INSERT, SELECT, UPDATE y DELETE).
- Múltiples operaciónes de INSERT, UPDATE y DELETE pueden ser ejecutadas en una sola query agrupandolas con **BATCH**.

### Dynamic Data Masking (DDM)

- DDM es una técnica que permite ocultar datos sensibles en una tabla. No altera los datos almacenados, solo los presenta de otra forma para las querys tipo SELECT.

### Seguridad

Las bases CQL usan roles que representan grupos de usuarios.

#### CREATE ROLE

- Permite crear un rol.
- Un rol puede ser un superusuario o un usuario normal.
- Por default los roles no poseen privilegios de **LOGIN** or **SUPERUSER**.
- Se pueden utilizar credenciales para autenticarse.

#### ALTER ROLE

- Permite modificar un rol.

#### DROP ROLE

- Permite eliminar un rol.

#### REVOKE ROLE

- Permite quitarle permisos a un rol.

## Gossip Protocol

Al ser distribuido, ningún servidor central mantiene la información, sino que los **peers** esparcen la información entre ellos manteniendo la ultima información.

- Dentro de un cluster, un nodo elige otro nodo para compartir la información. Ahora estos nodos repiten el proceso y asi se propaga la información dentro de un cluster.

Consideraciones a tener en cuenta:

1. Un nodo elige entre 1 y 3 nodos para compartir la información.
2. La información es compartida cada 1 segundo.
3. Los nodos no trackean con quien compartieron la información.
4. Hay nodos llamados **seed nodes** que son los nodos con mayor probabilidad de ser elegidos para compartir la información.

Los nodos comparten solo metadata del nodo, nunca datos del cliente.

### Estructura de un nodo

- **Endpoint State**: Contiene información sobre el nodo
  - **Heartbeat State**: Contiene cuando fue generado (timestamp) y la versión.
  - **Application State**: Contiene información sobre el nodo que el protocolo esparce por el cluster.
    - **STATUS**: Contiene información sobre el estado del nodo.
    - **DC**: Contiene información sobre el datacenter del nodo.
    - **SCHEMA**: Contiene información sobre el schema del nodo.
    - **LOAD**: Contiene información sobre la carga del nodo.

### Mensajes dentro del Gossip Protocol

- **SYN**: Es un mensaje que contiene un resumen de la información del nodo.
- **ACK**: Es un mensaje que contiene un resumen de la información del nodo y es enviado como respuesta a un **GossipDigestSyn**.

## Read Repair

Los nodos pueden romperse o estar ocupados, por lo que la información puede no estar actualizada. Para solucionar esto, Cassandra usa **Read Repair**.

- Considerando el mejor **Consistency Level** (CL) en un cluster de 4 nodos, llega una query y un nodo orquesta el procesamiento de esta, como el CL es el mejor posible (en este caso 3), pide la una replica de la info a todos los nodos y se fija si matchea o no. Luego, responde la consulta.
- Considerando un caso donde los checksums no matchean, el nodo orquesta envía un mensaje a los nodos que no matchean para que actualicen su información. Utilizando el timestamp mayor, manda replicas a los demás nodos para que se actualicen y devuelve el resultado al cliente.

## Replication Factor

El **Replication Factor** es la cantidad de nodos que contienen una copia de la información. Si el RF es 3, entonces 3 nodos contienen la misma información.

- Si el RF es de 1, cada partición tiene una porción de toda la data, cuando el nodo orquestador recive una query, la hashea y, de no ser suya, de forma asincrona mueve los datos a donde corresponde.

- Con un RF de 2, el nodo además de tener sus datos, tiene información de su vecino.

Se recomienda un RF de 3 para producción ya que el hardware falla y la probabilidad de que 3 nodos fallen en simultaneo es baja.
