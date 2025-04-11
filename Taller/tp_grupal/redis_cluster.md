# Objetivos

- Dar una buena performance.
- Grado aceptable de seguridad en escrituras. El sistema intenta retener todas las escrituras de los clientes que esten conectados a un nodo master en un modo **best-effort**. Puede haber perdidas de **acknowledge writes**.
- Disponibilidad. Con el sistema de promociones da una alta disponibilidad de los datos.

> [!NOTE]
> **best-effort**: Significa que hace lo mejor que puede y que no garantiza que siempre salga bien $\rightarrow$ simil al modelo de internet.
> **Acknowledge writes**: Redis usa el protocolo TCP y por como es el protocolo cada vez que el servidor recibe un mensaje de un cliente devuelve un **ACK*(acknowledge) si todo salió bien, la idea es que puede pasar que el cliente reciba un ACK para una escritura y que esa escritura se pierda.


# Implemented Subset

- Redis cluster implementa todos los **single key commands** que estan disponibles en su versión no-distribuida.

- Redis cluster implementa un concepto llamado **hash tags** $\rightarrow$ forzar a que algunas keys sean almacenadas en el mismo **hash slot**.

- Redis Cluster no soporta multiples bases de datos y el comando **SELECT** no está permitido.

# Los roles del Cliente y el Servidor en el Protocolo de Redis Cluster

- Los nodos del cluster son responsables de almacenar la data, mantener el estado del cluster, incluyendo mapeos de keys a otros nodos.

- Los nodos pueden auto-descubrir otros nodos, detectar si un nodo tiene fallas y promover nodos replica a master.

> [!NOTE]
> Un cluster es simil a un clique dentro de un grafo, son nodos que se conocen todos con todos.


- Para poder hacer esto todos los nodos del cluster son concetados usando un bus TCP y un protocolo binario $\rightarrow$ **Redis Cluster Bus**.

- Los nodos usan un **protocolo gossip** para propagar información sobre el cluster.

- Los nodos no funcionan como un proxy, entonces puede ocurrir que los clientes sean redireccionados a otro nodo.

> [!NOTE]
> Si el nodo fuera un proxy contestaría la consulta del cliente "simulando" que tiene la data, en este caso redirecciona al cliente al nodo que realmente tiene la data.

# Seguridad de escritura

- El último master dataset eventualmente gana y re-emplaza a todas las replicas. Siempre hay una ventana de tiempo en que es posible perder escrituras durante particiones pero son muy diferentes dependiendo de si el cliente esta conectado a la mayoría de nodos master o a la minoría.

- Redis Cluster intenta mantener las escrituras hechas por aquellos clientes que estan conectados a la mayoría de nodos master.

> [!NOTE]
> Cuantos mas nodos master reciban la escritura, mas chances hay de que ese dato se replique (gossip) y de que el ACK no sea un falso positivo. Si un solo nodo master recibe la escritura y muere antes de propagar el dato, el ACK será un falso positivo y la escritura perdida.

# Disponibilidad

- Por el feature **replicas migration** la disponibilidad del cluster mejora en muchos escenarios.
