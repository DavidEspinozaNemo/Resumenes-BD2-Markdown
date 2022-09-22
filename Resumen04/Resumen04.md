# Resumen: "Schema-Agnostic Indexing with Azure DocumentDB"

**Fecha de entrega**: *21/09/22 7:58 pm*

**Estudiante:** *David José Espinoza Soto - 2016012024*

**Instrucciones:** Del documento adjunto implementar un resumen de máximo 3 páginas.

## Introducción

Azure DocumentDB es el servicio de base de datos distribuida de múltiples inquilinos de Microsoft para administrar documentos JSON a escala de Internet.
Caracteristicas del subsistema de indexación de DocumentDB:

- El subsistema de indexación debe ser compatible con la indexación automática de documentos sin necesidad de un esquema o índices secundarios.
- El lenguaje de consulta de DocumentDB.
- Consultas consistentes en tiempo real frente a tasas de ingesta de documentos altas y sostenidas.
- Tenencia múltiple bajo presupuestos de recursos extremadamente frugales.
- Sigue proporcionando garantías de rendimiento predecibles y sigue siendo rentable.

### Descripción general de las capacidades

DocumentDB se basa en el modelo de datos JSON y el lenguaje JavaScript directamente dentro de su motor de base de datos. Creemos que esto es crucial para eliminar el "desajuste de impedancia" entre los lenguajes de programación de aplicaciones/sistemas de tipos y el esquema de la base de datos. Específicamente, este enfoque habilita las siguientes capacidades de DocumentDB:

- El lenguaje de consulta admite consultas ricas relacionales y jerárquicas. Tiene sus raíces en el sistema de tipos de JavaScript, la evaluación de expresiones y el modelo de invocación de funciones.
- El motor de la base de datos está optimizado para atender consultas consistentes frente a escrituras de documentos de alto volumen sostenido. De forma predeterminada, el motor de la base de datos indexa automáticamente todos los documentos sin requerir un esquema o índices secundarios.
- Ejecución transaccional de la lógica de la aplicación proporcionada a través de procedimientos almacenados y disparadores. Aprovecha el soporte nativo para los valores JSON comunes tanto al tiempo de ejecución del lenguaje JavaScript como al motor de la base de datos de varias maneras.
- Como un sistema de base de datos geodistribuido, DocumentDB ofrece niveles de coherencia bien definidos y ajustables y con las garantías de rendimiento correspondientes.
- Como un servicio de base de datos en la nube de múltiples inquilinos totalmente administrado, toda la administración de máquinas y recursos se abstrae de los usuarios. A los inquilinos se le ofrece la capacidad de escalar de manera elástica tanto el rendimiento como el almacenamiento de documentos respaldados por SSD, y asume la responsabilidad total de la administración de recursos, de una manera rentable.

### Modelo de recursos

Un arrendatario de DocumentDB comienza aprovisionando una cuenta de base de datos. Una cuenta de base de datos administra una o más bases de datos DocumentDB. Una base de datos DocumentDB, a su vez, gestiona un conjunto de entidades: **usuarios, permisos y colecciones**.

Una colección de DocumentDB es un contenedor independiente del esquema de documentos arbitrarios generados por el usuario. También administra procedimientos almacenados, activadores, funciones definidas por el usuario (UDF) y archivos adjuntos.

Las entidades bajo la cuenta de base de datos del inquilino: bases de datos, usuarios, colecciones, documentos, etc. se denominan recursos. Cada recurso se identifica de forma única mediante un URI lógico y se representa como un documento JSON.

Los inquilinos pueden escalar de manera elástica un recurso de un tipo dado simplemente creando nuevos recursos que se colocan en particiones de recursos.

Una partición de recursos se vuelve altamente disponible mediante un conjunto de réplicas.

### Topología del sistema

El servicio DocumentDB se manifiesta como una red superpuesta de máquinas, denominada **federación**. Cada réplica alberga una instancia del motor de base de datos de DocumentDB, que administra los recursos (por ejemplo, documentos), así como el índice asociado. El motor de la base de datos DocumentDB, a su vez, consta de componentes que incluyen la máquina de estado replicada (RSM) para la coordinación, el tiempo de ejecución del lenguaje JavaScript, el procesador de consultas y los subsistemas de almacenamiento e indexación responsables del almacenamiento transaccional y la indexación de documentos.

Para brindar durabilidad y alta disponibilidad, el motor de la base de datos de DocumentDB conserva los datos en los SSD locales y los replica entre las instancias del motor de la base de datos dentro del conjunto de réplicas, respectivamente.

### Objetivos de diseño para la indexación

- **Indexación automática**: los documentos dentro de una colección de DocumentDB podrían basarse en esquemas arbitrarios. De manera predeterminada, el subsistema de indexación indexa automáticamente todos los documentos sin requerir que los desarrolladores especifiquen un esquema o índices secundarios.
- **Compensaciones de rendimiento/almacenamiento configurables**: aunque los documentos se indexan automáticamente de forma predeterminada, se pueden dar concesiones detalladas entre la sobrecarga de almacenamiento del índice, la coherencia de las consultas y el rendimiento de escritura/consulta mediante una política de indexación personalizada.
- **Consultas jerárquicas y relacionales, ricas y eficientes**: el índice debe admitir de manera eficiente la riqueza de las API de consulta de DocumentDB, incluida la compatibilidad con proyecciones jerárquicas y relacionales y composición con UDF de JavaScript.
- **Consultas consistentes frente a un volumen sostenido de escrituras de documentos**: para cargas de trabajo de alto rendimiento de escritura que requieren consultas consistentes, el índice debe actualizarse de manera eficiente y sincrónica con las escrituras de documentos.
- **Tenencia múltiple**: la tenencia múltiple requiere una gestión cuidadosa de los recursos. Las actualizaciones de índices deben realizarse dentro del presupuesto estricto de recursos del sistema (CPU, memoria, almacenamiento e IOPS) asignados por réplica.

## Indexación Agnostica del esquema

El esquema de un documento describe la estructura y el sistema de tipos del documento independientemente de la instancia del documento. No existe un estándar de esquema ampliamente adoptado para JSON.

DocumentDB aprovecha la simplicidad de JSON y su falta de especificación de esquema. No hace suposiciones sobre los documentos y permite que los documentos dentro de una colección de DocumentDB varíen en el esquema, además de los valores específicos de la instancia. A diferencia de otras bases de datos de documentos, el motor de base de datos de DocumentDB funciona directamente en el nivel de la gramática JSON, siendo independiente del concepto de un esquema de documento y desdibujando el límite entre la estructura y los valores de instancia de los documentos. Esto, a su vez, le permite indexar documentos automáticamente sin necesidad de esquemas o índices secundarios.

### Documentos como árboles

La representación de documentos JSON como árboles a su vez normaliza tanto la estructura como los valores de instancia en los documentos en un concepto unificador de una estructura de ruta codificada dinámicamente. Para representar un documento JSON como un árbol, cada etiqueta (incluidos los índices de matriz) en un documento JSON se convierte en un nodo del árbol. Tanto los nombres de propiedad como sus valores en un documento JSON se tratan de manera homogénea, como etiquetas en la representación de árbol. Creamos un (pseudo) nodo raíz que genera el resto de los nodos (reales) correspondientes a las etiquetas en el documento debajo.

### Índice como documento

Con la indexación automática, se indexa cada ruta en un árbol de documentos. Cada actualización de un documento a una colección DocumentDB conduce a la actualización de la estructura del índice (es decir, provoca la adición o eliminación de nodos).

Hay dos asignaciones posibles del documento y las rutas: asignación de índice hacia adelante, que mantiene un mapa de tuplas (id del documento, ruta) y asignación de índice invertido, que mantiene un mapa de tuplas (ruta, id del documento).

### DocumentDB Queries

El lenguaje de consulta proporcionara proyecciones y filtros relacionales, consultas espaciales, navegación jerárquica entre documentos e invocación de UDF escritos completamente en JavaScript.

Tanto las consultas de SQL como las de JavaScript se traducen a un lenguaje de consulta intermedio interno llamado DocumentDB Query IL. Query IL admite proyecciones, filtros, agregados, clasificación, operadores planos, expresiones (aritméticas, lógicas y diversas transformaciones de datos), funciones intrínsecas proporcionadas por el sistema y funciones definidas por el usuario (UDF).

Query IL está diseñado para explotar la integración del lenguaje JSON y JavaScript dentro del motor de base de datos de DocumentDB, tiene su raíz en el sistema de tipos JavaScript, sigue la semántica del lenguaje JavaScript para la evaluación de expresiones y la invocación de funciones y está diseñado para ser un objetivo de traducción desde múltiples interfaces de lenguaje de consulta (actualmente, SQL y JavaScript).

## Organización del índice lógico

Para que la persistencia en el disco sea rentable, el árbol de índice debe convertirse en una representación eficiente del almacenamiento. La representación lógica del índice se puede ver como un conjunto ordenado de tuplas clave-valor, cada una de las cuales se denomina entrada de índice.  La clave consta de un término que representa la información de ruta codificada del nodo en el árbol de índice y un PES que ayuda a dividir las publicaciones horizontalmente.  El valor consta de una lista de publicaciones que representan colectivamente los identificadores de documentos codificados (o fragmentos de documentos).

### Mapas de bits como listas de publicaciones

Una lista de publicaciones captura los identificadores de todos los documentos que contienen el término dado. El tamaño de la lista de publicaciones es una función de la frecuencia del documento: la cantidad de documentos en la colección que contiene un término determinado, así como el patrón de ocurrencia de los identificadores de documentos en la lista de publicaciones.

Para una representación de una lista de publicaciones se requiere que sea dinámica, compacta (por lo tanto, minimice la sobrecarga de almacenamiento) y capaz de calcular operaciones de configuración rápida, por ejemplo, para probar presencia del documento durante el procesamiento de la consulta. Para ello, aplicamos dos técnicas:

- **Partitioning a Postings List**.  A cada inserción de un nuevo documento en una colección de DocumentDB se le asigna una identificación de documento que aumenta de forma monótona. Para evitar la reserva estática de espacio de identificación para almacenar la lista de publicaciones para un rango dado de identificaciones de documentos, dividimos la lista de publicaciones en entradas de publicaciones. Además, la partición también ayuda a determinar el tamaño máximo de las páginas y la política de división de los tamaños de página del árbol B+ utilizados como método de acceso físico. Una entrada de contabilizaciones es un conjunto ordenado de una o más palabras de contabilización de documentos dentro de un rango de ID de documento específico. La lista de publicaciones para un término determinado consta de una colección de longitud variable de entradas de publicaciones divididas por el selector de entradas de publicaciones (PES).
- **Dynamic Encoding of Posting Entries**. Dentro de una sola partición (señalada por un PES), cada documento necesita solo 14 bits que se pueden capturar con una palabra corta. Esto marca el límite superior en la lista de publicaciones dentro de un depósito: nunca deberíamos necesitar más de 32 KB para capturar un depósito que está densamente lleno con todas las identificaciones posibles asignadas al depósito. Dependiendo de la distribución, las palabras de publicación dentro de una entrada de publicación se codifican dinámicamente utilizando un conjunto de esquemas de codificación que incluyen varios esquemas de codificación de mapa de bits inspirados principalmente en WAH (Word-Aligned Hybrid). La idea central es conservar la mejor codificación para distribuciones densas (como WAH) pero trabajar eficientemente para distribuciones dispersas.
