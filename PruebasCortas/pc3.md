# Prueba Corta # 3 & 4

**Fecha de entrega**: *30/09/22 9:59 pm*

**Estudiante:** *David José Espinoza Soto - 2016012024*

## Responda

### -------------------- + 1 + --------------------

Explique el concepto de Write Concern en MongoDB. (10 pts)

Segun mis notas, el write concert define la consistencia, mide el nivel de consistencia de la base de datos. Una defición más certera seria que describe el nivel de reconocimiento solicitado de MongoDB para operaciones de escritura en un archivo independiente o en un archivo.

    { w: value, j: value, wtimeout: value}

W: Es el numero de nodos, para una consistencia fuerte la mitad +1 de los nodos del servidor de mongo deben reconocer las solicitudes de escritura.

J: Es un valor booleano, que pregunta sobre las escrituras a disco, si es **true** debe asegurarse de que los datos resistan un reinicio del sistema, y si es **false** le basta con solo mandar los datos.

Wtimeout: Es el tiempo que pasa despues de realizar una escritura para tener consistencia.

### -------------------- + 2 + --------------------

Explique diferencias entre bases de datos NoSQL y SQL, tome como ejemplo las bases de datos estudiadas en clase y utilizadas en proyectos y tareas: (40 pts)

- MongoDB
- Elasticsearch
- SQL Server
- MySQL
- PostgreSQL

#### SQL

A) Tiene una forma muy clara de mostrar y acceder a la información, crea tablas con un identificador unico por columna y establece relaciones entre diferentes tablas. Basicamente almacenan datos de manera estructurada.

B) Su capacidad de escalar es baja, lo planeas una vez y asi se queda, el costo de agrandar el sistema más haya de lo originalmente planeado es complejo y costoso. No se adaptan muy bien a los cambios.

C) Estan estandarizadas y proporcionan consistencia en los datos (integridad).

D) Los datos estructurados son más fáciles de analizar que los no estructurados.

#### NoSQL

A) Es mucho más flexible, no tiene tablas sino que maneja la información en colecciones, que pueden tener la estructura que mejor le sirva a la aplicación o a la lógica del negocio.

B) la capacidad de escalar es muy alta ya que las bases NoSQL están pensadas para grandes volúmenes de información como el Big Data. Son muy flexibles y se adaptan bien a los cambios.

C) No son Homogeneas y al buscar la rapidez, no les interesa la integridad.

D) Los datos no estructurados al ser más flexibles no les afectan los cambios de arquitectura, caso contrario de los datos estructurado.

### -------------------- + 3 + --------------------

Desde un punto de vista de una base de datos de series de tiempo, ¿Porqué la localidad de datos es relevante para la escogencia del hardware a utilizar?, puede justificar su respuesta utilizando los data tiers de algún motor de bases de datos como Elasticsearch. (40 pts)

La localidad es cuando al sistema le piden un bloque de datos o información y no solo utiliza los datos buscados sino que tambien toma bloques de datos de alrededor porque asume que esta información puede estar relacionada con la información solicitada y pueden que en el corto plazo pidan más información relacionada con la ultima solicitada, por esto es importante saber como se almacena la información.

### -------------------- + 4 + --------------------

Explique el concepto de Federated Queries y el impacto que tienen estas en el rendimiento de bases de datos. (10 pts)

Este es un concepto de la bases de datos distribuidas, en un sistema donde existen varias bases repartidas en diferentes regiones una consulta federada es una super consulta que se ejecuta en la central y que requiere datos de 2 o más regiones.

Las regiones puden ser un lugar geográfico especifico como Estados-Unidos, o estar más repartidos como decir Estados-Unidos: Nueva-York, Estados-Unidos: California, ect.

Como las bases de datos estan tan separadas fisicamente unas de otras, exiten limitaciones en las solicitudas: la cantidad de bytes permitidos, el tiempo que le toma viajar de un lado a otro, la cantidad de vez que se piden los datos, cuantos usuarios piden la información a la vez. Todos estos procesos son lentos y muy caros por la separación entre servidores, y se deberian usar lo menos posible, solo en situaciones muy necesarias.
