[Recursos](https://www.postgresqltutorial.com/postgresql-administration/postgresql-copy-database/)
[[Recurso y comandos para gestionar Docker y Postgres]]

1. **Realizar una  copiar base de datos dentro del mismo servidor:**
	- consulta las base de datos que quieres copiar y utiliza las consulta para copiar la base de datos nueva usando el template de la antigua [Recurso CREATE DATABASE](https://www.postgresqltutorial.com/postgresql-administration/postgresql-create-database/)
	- Una vez echo comprueba que la base de datos se encuentra disponible y contiene las mismas tablas

>[!error] Error 
>Si la base de datos a copiar tiene conexiones activas, nos encontramos con el siguiente error:
>
>```plsql
>ERROR: source database "dvdrental" is being accessed by other users
>DETAIL:  There is 1 other session using the database.
>--Introduzco la siguiente query para eliminar las conexiones activas
>SELECT pg_terminate_backend (pid)
FROM pg_stat_activity
WHERE datname = 'dvdrental';
>```
		
2. **Realizar una  copiar base de datos dentro para un servidor diferente
	- Utiliza el comando `pg_dumb` para extraer la base de datos PostgreSQL en un archivo de script [Recurso para pg_dumb](https://www.postgresqltutorial.com/postgresql-administration/postgresql-backup-database/)
	- Levanta un nuevo servicio docker con postgres para crear una nueva base de datos
	- Crear la base de datos y vuelca el contenido del script
	
3. **Transferir el archivo de copia de seguridad:**
	- Utiliza métodos de transferencia de archivos como SCP, FTP o incluso una simple copia de archivos en red para transferir el archivo de copia de seguridad (`sourcedb_backup.sql`) desde la máquina virtual de origen a la máquina virtual de destino.
	
4. Utilizar Scripts de respaldo programado
    - Crea un script que ejecute el comando `pg_dump` para respaldar la base de datos en un archivo SQL y prográmalo para que se ejecute automáticamente cada cierto tiempo utilizando la utilidad [cron](https://www.hostinger.es/tutoriales/sintaxis-crontab)

