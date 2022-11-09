# Prueba Corta #9

**Fecha de entrega**: *09/11/22 9:00 am*

**Estudiante:** *David José Espinoza Soto - 2016012024*

**Suponiendo que un sistema de bases de datos relacional que presenta un read-heavy workload y los queries son muy diferentes, explique detalladamente ¿porque el uso de caches puede afectar el rendimiento del sistema de forma negativa? (30 pts)**

La cache se utiliza como intermediario entre la memoria y la base de datos, se guarda información relevante en la cache para ahorrar tiempo de busqueda en la memoria principal. Ahora si la cantidad de información solicitada es mucha la cache no podra almacenarla toda, solo comtemplará una porción, y si la siguiente consulta solicita información diferente a la que previamente se reviso, el sistema primero tendra que leer toda la cache para darse cuenta que no le sirve la información que actualmente guarda para luego tener que buscarla en la memoria principal.
Para resumir, la cachar sirve para agilizar busquedas que sean repetitivas, y que no maneje grandes volumenes de datos que exedan la capacidad de la cache.

**El particionamiento de tablas en bases de datos relacionales es un concepto muy parecido al de shards en bases de datos NoSQL, explique detalladamente ¿Cómo afecta el particionamiento y el sharding en el rendimiento de bases de datos SQL y NoSQL? (30 pts)**

Son técnicas que se utilizan cuando el volumen de datos crece mucho, tanto que para tener una buen rendimiento debemos partir los datos en pedazos manejables. Como la información se reparte en diferentes nodos, cada uno debe funcionar como una base de datos independiente y solo comunicarse cuando les solicitan una consulta global o que involucren a varios nodos. Por eso lo importante es saber como repartir la información, eligiendo correctamente se mejora el rendimiento.
Si se elige mal la manera de repartir los datos entre los nodos, o el coste de administrar y comunicar a los nodos es superior a la busqueda de la información, nuestro tiempo de respuesta cae abruptamente.

**En un sistema de bases de datos con Strong Consistency cuyo workload es de read-heavy y write-heavy, ¿Cómo afectan los exclusive locks el rendimiento de las bases de datos NoSQL? (20 pts)**

Los exclusive locks bloquean las operaciones de lectura y escritura, al estar en un Strong Consistency los demas procedimientos no podran acceder de ninguna forma a las tareas de lectura y escritura, por lo que se atrazaria constantemente. La base de datos NoSQL bajará su rendimiento porque siempre debera validar todos los cambios que se van realizando.

**Explique detalladamente, ¿Cómo afecta la selección de discos físicos el rendimiento de una base de datos SQL y NoSQL? (20 pts)**

Supongo que tendria que ver con la manera en la que se guardan los datos, en un formato físico creo que una estructura sujeta a pocos cambios se beneficiaria con la manera de guardar los datos de forma fisica y por lo tanto su lectura y escritura serian más rápidas. Por otro lado, la estructura dinámica de las collecciones de las bases NoSQL obligaria al sistema operativo a alterar constantemente el almacenamiento de la información.
