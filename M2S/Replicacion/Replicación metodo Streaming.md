[Fuente del recurso ](https://www.youtube.com/watch?v=FC2JMBYDcJE)

Conocida como replicación WAL , Este enfoque de la replicación se basa en el traslado de los archivos WAL de la base de datos primaria a la base de datos de destino.

Implementamos este método utilizando una configuración primaria-secundaria. El servidor primario es la instancia principal que maneja la base de datos primaria y todas sus operaciones. 

El servidor secundario actúa como instancia complementaria y ejecuta en sí mismo todos los cambios realizados en la base de datos primaria, generando una copia idéntica en el proceso. El primario es el servidor de lectura/escritura, mientras que el secundario es de mera lectura.

Explicación 
Primero lanzaremos una instancia de postgres en un contenedor, persistiremos los datos en un volumen, archivaremos el WAL para incrementar la integridad de los datos (
En términos PostgreSQL, copiar los archivos WAL generados se llama archivar, y hacer que el servidor lea un archivo WAL y lo aplique se llama restaurar.), lanzaremos otro contenedor que hare un backup de la instancia primaria, creando automaticamente un directorio de datos, que esta configurado para trasmitir desde el directorio principal utilizando PgBase-Backup dandonos un volumen desde el cual podremos lanzar otra instancia de postgres en standby mode

Lanzamos la primera instancia de PostgreSQL
- Establecer volúmenes de datos únicos para los datos entre instancias
- Establecer archivos de configuración únicos para cada instancia, para poder configurarlo por separado
- Crear una red Postgres en docker para la comunicación entre contenedores

### Docker-compose de la instancia DB-master 

```yml
version: '3.8'
services:
	db-master:
		image: postgres:15.0
		container_name: db-master
		networks:
			- postgres
		environment:
			# POSTGRES_USER: postgresadmin
			POSTGRES_PASSWORD: root
			# POSTGRES_DB: postgresdb
			PGDATA: /data
		volumes:
			- ${PWD}/db-master/pgdata:/data
			- ${PWD}/db-master/config:/config
			- ${PWD}/db-master/archive:/mnt/server/archive
		ports:
			- "5432:5432"
		command: ["-c", "config_file=/config/postgresql.conf"]
networks:
	#importante para que los servicio dentro del archivo se puedan comunicar
	postgres:
		name: postgres
```

Notas sobre el archivo
#### ${PWD}
Dirección desde donde ejecutamos  el archivo docker-compose 
#### `PGDATA` 

es una variable de entorno que se utiliza para especificar el directorio de datos de PostgreSQL. 

Este directorio contiene todos los archivos necesarios para el funcionamiento de la base de datos, incluidos los archivos de configuración, los archivos de registro WAL (Write-Ahead Logging), los archivos de tabla y los archivos de índice.

Cuando instalas PostgreSQL, por defecto, `PGDATA` se establece en un directorio específico dependiendo del sistema operativo y de cómo se haya instalado PostgreSQL. En entornos de producción, es común que `PGDATA` se establezca en un directorio como `/var/lib/postgresql/data` en sistemas basados en Unix.

Sin embargo, en entornos de contenedores Docker, es común sobrescribir `PGDATA` para que apunte a un directorio dentro del contenedor que esté mapeado a un directorio en el sistema host. Esto se hace para permitir la persistencia de los datos de PostgreSQL incluso cuando el contenedor se detiene o se elimina.

`PGDATA: /data`

Significa que se establecio `PGDATA` en el directorio `/data` dentro del contenedor. Luego, mediante la opción de volumen `-v`, estás mapeando este directorio a un directorio en tu sistema host para persistir los datos de PostgreSQL

`volumes:   - ${PWD}/db-master/pgdata:/data`

Entonces, todos los datos de PostgreSQL, incluidos los archivos de registro, las bases de datos y los archivos de configuración, se almacenarán en el directorio `${PWD}/db-master/pgdata` en el sistema host, lo que proporciona persistencia a través de reinicios del contenedor y actualizaciones de la imagen del contenedor.
#### Archive

Archivaremos los logs del WAL en un volumen persistente en nuestra maquina, por si el contenedor muere no perdamos nuestro ARCHIVE

>[!tip] Archive
>Archive es el proceso de archivar los logs de transacciones, pudiendo recuperar postgres desde cualquier punto del tiempo. 


### Archivos de configuración 

En la carpeta donde tengo el docker-compose.yml tengo que tener las carpetas que indico en volumes del docoker-compose db_master (Esta sera la que contenga mi base de datos) > pgdata, config, archive y dentro de config los archivos descritos a continuación.
#### pg_hba.conf

```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD

host     replication     replicationUser         0.0.0.0/0        md5

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust

host all all all scram-sha-256
```
#### pg_ident.conf

```bash

# PostgreSQL User Name Maps
# =========================
#
# Refer to the PostgreSQL documentation, chapter "Client
# Authentication" for a complete description.  A short synopsis
# follows.
#
# This file controls PostgreSQL user name mapping.  It maps external
# user names to their corresponding PostgreSQL user names.  Records
# are of the form:
#
# MAPNAME  SYSTEM-USERNAME  PG-USERNAME
#
# (The uppercase quantities must be replaced by actual values.)
#
# MAPNAME is the (otherwise freely chosen) map name that was used in
# pg_hba.conf.  SYSTEM-USERNAME is the detected user name of the
# client.  PG-USERNAME is the requested PostgreSQL user name.  The
# existence of a record specifies that SYSTEM-USERNAME may connect as
# PG-USERNAME.
#
# If SYSTEM-USERNAME starts with a slash (/), it will be treated as a
# regular expression.  Optionally this can contain a capture (a
# parenthesized subexpression).  The substring matching the capture
# will be substituted for \1 (backslash-one) if present in
# PG-USERNAME.
#
# Multiple maps may be specified in this file and used by pg_hba.conf.
#
# No map names are defined in the default configuration.  If all
# system user names and PostgreSQL user names are the same, you don't
# need anything in this file.
#
# This file is read on server startup and when the postmaster receives
# a SIGHUP signal.  If you edit the file on a running system, you have
# to SIGHUP the postmaster for the changes to take effect.  You can
# use "pg_ctl reload" to do that.

# Put your actual configuration here
# ----------------------------------

# MAPNAME       SYSTEM-USERNAME         PG-USERNAME
```
#### postgresql.conf

```bash
# -----------------------------
# PostgreSQL configuration file
# -----------------------------
#

data_directory = '/data'
hba_file = '/config/pg_hba.conf'
ident_file = '/config/pg_ident.conf'

port = 5432
listen_addresses = '*'
max_connections = 100
shared_buffers = 128MB
dynamic_shared_memory_type = posix
max_wal_size = 1GB
min_wal_size = 80MB
log_timezone = 'Etc/UTC'
datestyle = 'iso, mdy'
timezone = 'Etc/UTC'

#locale settings
lc_messages = 'en_US.utf8'			# locale for system error message
lc_monetary = 'en_US.utf8'			# locale for monetary formatting
lc_numeric = 'en_US.utf8'			# locale for number formatting
lc_time = 'en_US.utf8'				# locale for time formatting

default_text_search_config = 'pg_catalog.english'

#replication
wal_level = replica
archive_mode = on
archive_command = 'test ! -f /mnt/server/archive/%f && cp %p /mnt/server/archive/%f'
max_wal_senders = 3
```

### Crear usuario de replicación

Para realizar la copia de seguridad utilizaremos una nueva cuenta de usuario PostgreSQL que tenga permisos para realizar la replicación.

```bash
docker exec -it containerName bash

# create a new user, -U postgres por defecto si no se especifico un user en el dockercompose.yml, -P  para que ingrese el password una vez creado 
createuser -U postgres -P --replication replicationUser

exit
```

Con este usuario creado editamos el archivo de autenticación `pg_hba.conf`permitiendole acceso a la instancia primaria 
### Activar Write-Ahead Log

[Wal oficial doc](https://www.postgresql.org/docs/current/wal-intro.html)
[[Archivo WAL]]

Configuraciones 

*wal_level 
Determina cuánta información se escribe en la WAL. El valor por defecto es replica, que escribe datos suficientes para soportar el archivado y replicación de la WAL, incluyendo la ejecución de consultas de sólo lectura en un servidor en espera

*archive_mode 

>[!tip] Archive
>Archive es el proceso de archivar los logs de transacciones, pudiendo recuperar postgres desde cualquier punto del tiempo. 

Cuando archive_mode está activado, los segmentos WAL completados se envían al almacenamiento de archivo configurando archive_command o archive_library.

archive_command = 'test ! -f /mnt/server/archive/%f && cp %p /mnt/server/archive/%f'
Este comando se ejecutara cada vez que un segmento se escriba en el log.

%f esto se remplaza por nombre del archivo y %p por el path, entonces el comando testea el path y copia el archivo al  archive directorio que especificamos en el docker-compose `- ${PWD}/db-master/archive:/mnt/server/archive`

>[!Error] Atención
>*Error* 
>Archiver process (PID 392860) exited with exit code 1 sh: 1: pg_wal/000000010000000000000002: Permission denied
>La solución es cambiar los permisos dentro del contenedor docker `/mnt/server/archive` de **root** a **postgres**.

con todo esto en marcha arrancamos el contenedor y ya podriamos ver que se a registrado el primer log en el volumen arcchive

![[Captura de pantalla 2024-03-27 a las 12.26.45.png]]

### Configurar y ejecutar la instancia replica/en Espera

 Como herramienta para la alta disponibilidad tenemos la herramienta nativa de postgres *pgbase_backup*, esta herramienta crea instancia standby (replicas/en espera) y backup para las bases de datos postgres.
 
Este conecta a la instancia primaria como un *replication user*, hace un backup y lo restaura en el carpeta propia de data de la instancia replica/standby, viéndose esta carpeta casi igual en el nodo replica que en el primario. 

Esta carpeta de datos estará lista y configurada para recibir la solicitud de replicación de la primaria, así que lo que vamos a hacer es utilizar esta utilidad para tomar un backup de nuestra carpeta de archivos de datos en el nodo primario y crear un nuevo directorio de datos en la replica, luego tomar este directorio de datos y hacer un nuevo volumen Docker 
  
Una vez que esta instancia secundaria se inicia automaticamente va a  empezar a leer el registro de transacciones desde el servidor primario y comenzar el proceso de replicación que técnicamente se ejecutará como una lectura en la instancia

### Hacer una copia de seguridad de la DB

Para realizar una copia de seguridad de la base de datos, utilizaremos la utilidad pg_basebackup. 

La utilidad está en la imagen docker de PostgreSQL, así que vamos a ejecutarla sin ejecutar una base de datos, ya que todo lo que necesitamos es la utilidad pg_basebackup.

También montamos nuestro directorio de datos en blanco, ya que haremos una nueva copia de seguridad allí:

```bash
docker run -it --rm \
--net postgres \
-v ${PWD}/db-replica/pgdata:/data \
--entrypoint /bin/bash postgres:15.0
```

Realice la copia de seguridad iniciando sesión en db-master con el replicationUser y escribiendo la copia de seguridad en /data.

`pg_basebackup -h db-master -p 5432 -U replicationUser -D /data/ -Fp -Xs -R`

Una vez realizado este comando podremos ver en el Archive volumen del db-master como se escribió el backup

![[Captura de pantalla 2024-03-27 a las 14.40.49.png]]

Y en el volumen pgdata que configuramos en la replica su sido rellenado con más o menos el volumen de datos de la carpeta de datos del nodo principal postgres.

También tiene la capacidad de replicación y streaming, ahora el volumen de datos para nuestra instancia secundaria, está listo para ejecutarse.

### Iniciamos la instancia replica de la base de datos

Una vez tenemos los datos del backup solo nos faltara añadir otro servicio a nuestro docker-compose con otra imagen postgreSQL y añadir a este contenedor los volumenes que creamos anteriormente.


En la carpeta donde tengo el docker-compose.yml tengo que tener las carpetas que indico en volumes del docoker-compose db_replica (Esta sera la que contenga mi base de datos) > pgdata, config, archive y dentro de config los archivos descritos a continuación.

```yml
db-replica:
	image: postgres:15.0
	container_name: db-master
	networks:
		- postgres
	environment:
		# POSTGRES_USER: postgresadmin
		POSTGRES_PASSWORD: root
		# POSTGRES_DB: postgresdb
		PGDATA: /data
	volumes:
		- ${PWD}/db-replica/pgdata:/data
		- ${PWD}/db-replica/config:/config
		- ${PWD}/db-replica/archive:/mnt/server/archive
	ports:
		- "5001:5432"
	command: ["-c", "config_file=/config/postgresql.conf"]
```

Ya con este contenedor replica en ejecución escuchando cualquier transaccion del nodo primario podremos ver en el contenedor replica en tiempo real cualquier cambio que hagamos en el primario.

### Conmutación por error

Ahora digamos que Nodo primario falla.

PostgreSQL no tiene incorporado failver automatizado y recuperación y requiere herramientas para realizar esto.

Cuando Nodo primario falla, usaríamos una utilidad llamada `pg_ctl` para promover nuestro servidor replica/stand-by a un nuevo servidor primario.

Luego tenemos que construir un nuevo servidor stand-by tal como lo hicimos anteriormente.
También tendríamos que configurar la replicación en el nuevo primario.

Paremos el servidor primario para simular un fallo

```
docker rm -f db-master
```

A continuación, inicie sesión en db-replica  promuévelo a primario:

```bash
docker exec -it db-replica bash

# confirmamos que no podemos crear una tabla ya que se trata de un servidor de reserva (Dentro del postgres)
CREATE TABLE customers (firstname text, customer_id serial, date_created timestamp);

# ejecute pg_ctl como usuario postgres (¡no puede ejecutarse como root!)(executa fuera de postgres)
runuser -u postgres -- pg_ctl promote

# confirmar que podemos crear una tabla como su un servidor primario (Ejecuta dentro de postgres)

CREATE TABLE customers (firstname text, customer_id serial, date_created timestamp);
```