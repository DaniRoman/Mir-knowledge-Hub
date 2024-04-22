
## Concepto

Proceso de copiar datos de un servidor de base de datos PostgreSQL a otro servidor. El servidor de la base de datos de origen también se conoce como servidor «primario», mientras que el servidor de la base de datos que recibe los datos copiados se conoce como servidor «réplica».

La base de datos PostgreSQL sigue un modelo de replicación sencillo, en el que todas las escrituras van a un nodo primario. El nodo primario puede entonces aplicar estos cambios y difundirlos a los nodos secundarios.

## Métodos de replicación 

1. **Replicación en streaming:** Replica los datos del nodo primario al secundario, y luego copia los datos a Amazon S3 o Azure Blob para el almacenamiento de las copias de seguridad. [[Replicación metodo Streaming]]

3. **Replicación a nivel de volumen:** Replica los datos en la capa de almacenamiento, empezando por el nodo primario hacia el secundario, y luego copia los datos en Amazon S3 o Azure Blob para el almacenamiento de las copias de seguridad.

4. **Copias de seguridad incrementales:** Replica los datos del nodo primario mientras construye un nuevo nodo secundario a partir del almacenamiento de Amazon S3 o Azure Blob, lo que permite el streaming directamente desde el nodo primario.