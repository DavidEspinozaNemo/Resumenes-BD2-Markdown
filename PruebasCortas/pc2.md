# Prueba Corta # 2

**Fecha de entrega**: *09/09/22 9:59 pm*

**Estudiante:** *David José Espinoza Soto - 2016012024*

## Responda

### Explique el concepto de shard, replica y partition

Una **replica** es básicamente la copia o el reflejo de una base de datos, la replica existe como seguro en caso de emergencia y si se llegara a perder la base de datos original, el respaldo toma inmediatamente su lugar y el servicio a los clientes no se vea afectado.

Una **partition** o partición, implica dividir la información de las tablas en partes más pequeñas para poder organizarlas en diferentes partes de un big warehouse. Toda la información pertenece a un solo grupo, pero esta dividido a nivel lógico.

La verdad no me acuerdo muy bien de que era **shard**, creo que tenia que ver con fragmentar la información y ponerla en diferentes servidores, que era para cuando se escalaba una aplicación.

### Explique la diferencia entre Strong Consistency y Eventual Consistency

La consistencia, en general, significa que los datos escritos en la base de datos son válidos según todas las reglas definida y sus limitaciones.

El **strong consistency** significa que no se presentan datos hasta tener la ultima versión de los mismos.

Por otro lado, **eventual consistency** si habilita las lecturas aun cuando no ha verificado que los datos sean los más actualizados, bajo la premisa de que eventualmente seran consistentes. Es más flexible que **strong consistency**.

### ¿En que consiste warm replicas y hot replicas?

Una replica **warm** obliga una consistencia fuerte, por lo que no acepta nuevas conexiones hasta asegurar la consistencia. Por otro lado una replica **hot** dispone de una consistencia eventual, por lo que la replica solo acepta conexiones de lectura, las cuales tienen una ventana de tiempo donde la información puede no ser consistente.

### ¿En que consiste consiste switch over y fail over?

Segun como lo entendi, un **fail over** ocurre cuando una app falla, y esto hace que salte una alarma. Por otro lado, un **switch over** seria una respuesta manual ante la falla.
