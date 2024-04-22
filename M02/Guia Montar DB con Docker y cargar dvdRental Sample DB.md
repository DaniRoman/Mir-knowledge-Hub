
Descomprimimos carpeta postgres-sampleDB Dentro de esta tenemos el pdf con las tablas y relaciones de la base de datos una carpeta docker con el archivo docker-compose.yml que contiene la base de datos y el programa gracifico de este pg_admin y la carpeta dvdrental con el contenido de la base de datos

Una vez descomprimidos abriremos un terminal en la carpeta docker 
comprobaremos que tenemos instalado docker con `docker -v`
si todo esta correcto ejecutaremos `docker ps` para ver si tenemos alguna instancia antigua del servició base de datos Postgres.

![[Captura de pantalla 2024-03-13 a las 13.38.15.png]]

Los paramos y los eliminamos con el comando `docker stop containerId` `docker rm containerId` 

Ahora con el comando `docker-compose up` levantamos el servicio definido el archivo docker-compose.yml comprobamos que esten los contenedores corriendo con el comando `docker ps`

Esto a creado una especie de maquina virtual con nuestra base de datos y ortra con un programa que nos permitira acceder a ella de manera grafica

Entraremos en la maquina (Contenedor ) que contiene la base de datos  con el comando `docker exec -it containerId bash` para ver el numero del contenedor recordar el comando `docker ps` crearemos nuestra base de datos 

una vez dentro ejecutamos el comando `psql -U postgres` para ejecutar la base de datos, ahí crearemos nuestra base de datos dvdrental con `CREATE DATABASE dvdrental;` para comprobar que se creo correctamente usamos el comando `\l` si vemos nuestra base de datos esta todo correcto.

Salimos de la base de datos con el comando `exit`

Una vez fuera vamos a cargar los datos de la base de datos dentro de esta con el comando 
`pg_restore -U postgres -d dvdrental scripts/dvdrental`

Entramos dentro de la base de datos para comprobar que se cargaron correctamente los datos en esta `psql -U postgres -d dvdrental` y listamos las tablas `\dt` y hacemos un `SELECT * tabla` de cualquier tabla si esta todo correcto podemos salir

Ahora vamos a enlazar el programa gráfico con la base de datos creada entramos en localhost:5050 (Puerto donde hemos definido nuestro servicio pg_admin) 

En email introducimos las credenciales que tenemos definidas en el archivo docker-compose.yml en el servicio pg_admin 

```yml
pgadmin:
	container_name: pgadmin4_container
	PGADMIN_DEFAULT_EMAIL: admin@admin.com
	PGADMIN_DEFAULT_PASSWORD: root
```


Creamos un servidor y introducimos los valores de la imagen 

>[!warning] Importante!
>En User envès de root tenemos que poner postgres


![[Captura de pantalla 2024-03-13 a las 14.01.30.png]]