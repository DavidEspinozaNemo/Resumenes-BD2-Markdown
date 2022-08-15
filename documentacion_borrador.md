# Proyecto Opcional

    Tecnológico de Costa Rica
    Escuela de Ingeniería en Computación
    Bases de Datos II (IC 4302)
    Segundo Semestre 2022

## Integrantes

    David Jose Espinoza Soto - 2016012024
    Black
    Johnny Díaz
    Jurgenn

## Objetivo General

- Desarrollar habilidades en tecnologías y lenguajes de programación que serán utilizados durante el curso.

## Objetivos Específicos

- Implementar un RESTful API spider en Python.

- Desarrollar scripts de procesamiento de datos mediante el uso de Spark SQL.

- Implementar un sistema de búsqueda de artículos científicos de Covid19.

- Automatizar una solución mediante el uso de Docker, Docker Compose, Kubernetes y Helm Charts.

- Desarrollar arquitecturas de microservicios en Kubernetes.

- Implementar servicios básicos de extracción de entidades.

- Instalar y configurar servicios de bases de datos relacionales.

- Instalar y configurar servicios de bases de datos NoSQL.

- Comparar rendimiento de bases de datos NoSQL y SQL mediante la ejecución de benchmarks.

## Describción General del Proyecto

El proyecto *bioRxiv Search* pretende implementar dos motores de búsqueda de artículos científicos de Covid19, uno en una base de datos NoSQL llamada Elasticsearch y otro en una base de datos relacional llamada MySQL, para este propósito utilizaremos un repositorio de descripciones de artículos científicos llamada bioRxiv, el cual al día 25/07/2022 contaba con 24347 artículos.

Un usuario debe ingresar al Kibana Service, en el índice llamado jobs, creara un documento de la siguiente forma:

```
{
    "jobId": "{alfanumérico}",
    "pageSize": "{número}",
    "sleep": "{número en milisegundos para dormir entre cada request}"
}
```

El Controller, debe estar continuamente revisando el índice, en el momento que detecta un cambio en el documento, hace un llamado al API bioRxiv (el repositorio que solo tiene las descripciones de los articulos ) y captura el valor llamado **messages.total**, con este se generan varios *splits* (que son una porción de los articulos que deben ser descargados). La cantidad de *splits* se consigue con la división **messages.total**/**pageSize**, por cada *split* se publica un mensage en una cola de kafka con el siguiente formato:

```
{
    "jobId": "{alfanumérico}",
    "pageSize": "{número}",
    "sleep": "{número en milisegundos para dormir entre cada request}",
    "splitNumber": "{número de split}"
}
```

Estos mensages son leídos por el API Spider, descarga los archivos del API bioRxiv y los almacena en un disco, que se comparte entre varios componentes, y publica en otra cola de kafka un mesage con el siguiente formato:

```
{
    "jobId": "{alfanumérico}",
    "pageSize": "{número}",
    "sleep": "{número en milisegundos para dormir entre cada request}",
    "splitNumber": "{número de split}",
    "status": "DOWNLOADED"
}
```

Scala Spark Processor debe leer estos mensajes y, utilizando SparkSQL, aplica las siguientes transformaciones:

- *author_name* -> Apellido, Nombre
- *author_inst* -> deberá separarse en sus componentes
- *category* -> cada primera letra va en mayuscula y remueve espacios en blanco

- *rel_date* -> pasa al formato dd/mm/yyyy

La transformación se debe guardar en el disco compartido y se debe publicar el siguiente mensaje en una cola de Kafka:

```
{
    "jobId": "{alfanumérico}",
    "pageSize": "{número}",
    "sleep": "{número en milisegundos para dormir entre cada request}",
    "splitNumber": "{número de split}",
    "status": "PROCESSED"
}
```

Spacy Entity Extractor debe leer estos mensages y, utilizando la funcionalidad de Spacy para realizar **Named Entity Recognition**, las volvera entidades y las almacenarán en un nuevo campo de tipo array llamado *entities*. Los documentos se guardaran en el disco compartido y se dos colas de Kafka ( una para Elasticsearch y una para mysql ) con el siguiente formato:

```
{
    "jobId": "{alfanumérico}",
    "pageSize": "{número}",
    "sleep": "{número en milisegundos para dormir entre cada request}",
    "splitNumber": "{número de split}",
    "status": "AUGMENTED"
}
```

Elasticsearch Publisher y MySQL Publisher leerán estos mensages y publicará los docuentos en MySQL y Elasticsearch. El usuario podra consultarlos mediante Kibana o MySQL Worbench.

## Instrucciones para Ejecutarlo

## Resultados de Pruebas Unitarias

## Recomendaciones y Concluciones (almenos 10)

## Matriz de Responsabilidades

En esta matriz explica como los integrantes del equipo se relacionaron en el desarrollo de cada sección del proyecto.

- **N** ) significa que no estubo relacionado con la **sección**.
- **S** ) significa que brindo apoyo a la **sección**.
- **Y** ) significa que estubo implecado en la **sección**.

|  Implementación             | David | Black | Johnny | Jurgenn |
|-----------------------------|:-----:|------:|-------:|--------:|
|Controller (5%)              |   Y   |   n   |   n    |   n     |
|API Spider (20%)             |   n   |   n   |   n    |   n     |
|Scala Spark Processor (20%)  |   n   |   n   |   n    |   n     |
|Elasticsearch/Kibana (5%)    |   n   |   n   |   n    |   n     |
|MySQL (5%)                   |   n   |   n   |   n    |   n     |
|Kafka (5%)                   |   Y   |   n   |   n    |   n     |
|Servicios (5%)               |   n   |   n   |   n    |   n     |
|Spacy Entity Extractor (10%) |   n   |   n   |   n    |   n     |
|MySQL Publisher (5%)         |   n   |   n   |   n    |   n     |
|Elasticsearch Publisher (5%) |   n   |   n   |   n    |   n     |
|Gatling Scripts (5%)         |   n   |   n   |   n    |   n     |
