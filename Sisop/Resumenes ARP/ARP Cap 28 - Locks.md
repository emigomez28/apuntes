# Locks

> [!NOTE]
> Los locks sirven para resolver el problema anterior, ejecutar un conjunto de instrucciones de forma atómica.

- Lo que hace el lock es mantener el estado del mismo, *disponible* o *bloqueado*. Se suele decir que un thread es dueño del lock, ya que será el quien bloquee o desbloquee el mismo.

- El nombre **mutex** proviene de **mutual exclusion** ya que busca eso, proveer la exclusión mutua entre threads.


