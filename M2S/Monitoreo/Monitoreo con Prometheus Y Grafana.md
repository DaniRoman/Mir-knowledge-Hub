## Explicación 

>[!example]
>[Recurso externo para tomar notas](https://sematext.com/blog/postgresql-monitoring/)

La captura de métricas es crucial para asegurarse de que la base de datos es fiable, está disponible y funciona de forma óptima. 

Esto ayuda a profundizar en los problemas de rendimiento de la base de datos, realizar ajustes de rendimiento, optimizar consultas e índices y tomar decisiones de particionamiento. 

Hay dos tipos principales de monitorización en PostgreSQL:

Monitoreo de recursos del sistema: Observa el uso de la infraestructura en la que se ejecuta su PostgreSQL, exponiendo métricas como el uso de CPU y memoria.

- Uso de la CPU
- Memoria  
- Almacenamiento  
- Red

Monitoreo de base de datos: Asegura que la base de datos esté saludable y funcionando óptimamente

- Rendimiento de las consultas
- Sesiones activas  
-  Replicación  
-  Registros

## Archivos configuración

### `docker-compose.yml`

Le añado al docker-compose de la practica anterior los servicios de Grafana y Prometheus.

```yml
version: '3.8'
services:
	 prometheus
		image: prom/prometheus
		ports:
			 - 9090:9090
		volumes:
			- ./prometheus.yml:etc/prometheus/prometheus.yml:ro
	grafana:
		image: grafana/grafana
		ports:
			- 3000:3000
```

>[!nota] Nota
>Cuando se usa `:ro` al final de la definición de un volumen, indica que el contenedor solo tiene permisos de lectura sobre los archivos en ese volumen y no puede modificarlos.
>Útil para garantizar que los archivos críticos o de configuración dentro del contenedor no sean modificados accidentalmente.

### `prometheus.yml`

```yml
global:
	scrape_interval: 15s
	evaluation_interval: 15s
	
scrape_configs:
	- job_name: 'postgres_exporter'
	scrape_interval: 5s
	static_configs:
		- targets: ['prometheus:9090']

	- job_name: 'prometheus'
	scrape_interval: 5s
	static_configs:
		- targets: ['postgres-exporter:9187']
```

>[!Error] Ojo al Error!!
>La identación en el `.yml` es importante de no ser correcta el contenedor que ejecuta el servicio Prometheus parara, Foto de la correcta identación.
>![[Captura de pantalla 2024-04-18 a las 13.11.06.png]]


#### Sintaxis del archivo

1. **Sección global:**

    - `scrape_interval`: Establece el intervalo entre las operaciones de raspado (scrape) de métricas. 
    - `evaluation_interval`: Define el intervalo entre las evaluaciones de las reglas de alerta y las grabaciones de reglas de grabación. 
    
1. **Sección scrape_config:**

    - Esta sección configura qué y cómo Prometheus recopila datos de los objetivos (targets).
    - Hay dos bloques de configuración de trabajo (job) definidos aquí.

>[!nota] Nota 
>En Prometheus, un *"job" *es una unidad lógica de trabajo que define qué y cómo se deben recopilar las métricas de un conjunto específico de objetivos (targets).
>
Determinan qué servicios, sistemas o componentes se monitorean y cómo se obtienen las métricas asociadas.


a. **Job "prometheus":**

- Destinado a escrapear las propias métricas de Prometheus.

- `static_config`: Se utiliza para especificar configuraciones estáticas, es decir, no dinámicas.

- Dentro de `static_config`, se define una lista de objetivos (targets) a los que se accederá para recopilar métricas. En este caso, solo hay un objetivo, que es el servicio de Prometheus mismo, escuchando en el puerto 9090 en el contenedor llamado "prometheus".

b. **Job "postgres-exporter":**

- Este job recopila métricas del servicio Postgres utilizando un exportador de Prometheus específico para PostgreSQL.

- `static_configs`: Aquí se configuran los objetivos estáticos para el raspado.
- Dentro de `static_configs`, también hay una lista de objetivos a los que se accederá para recopilar métricas. En este caso, el objetivo es el servicio del exportador de PostgreSQL, escuchando en el puerto 9187 en el contenedor llamado "postgres-exporter".

### `docker-compose.yml`

Añadimos el Prometheus Postgres Exporter

Es una herramienta utilizada para exportar métricas de bases de datos PostgreSQL a Prometheus para supervisar y medir el rendimiento de las instancias de bases de datos PostgreSQL.

Recopila métricas de PostgreSQL y proporciona un punto final web para que Prometheus las extraiga. 

>[!nota] Nota
>Prometheus web endpoints es una URL a la que se puede acceder para obtener métricas y datos sobre el estado del sistema monitoreado.

Estas métricas incluyen el tamaño de la base de datos, las tasas de transacción y los tiempos de ejecución de las consultas. 

Con esta información, podemos conocer la salud y el rendimiento de la base de datos, identificar cuellos de botella y optimizar el rendimiento de la base de datos.

```yml
version: '3.8'
services:
	 prometheus
		image: prom/prometheus
		ports:
			 - 9090:9090
		volumes:
			- ./prometheus.yml:etc/prometheus/prometheus.yml:ro
	grafana:
		image: grafana/grafana
		ports:
			- 3000:3000
	postgres-exporter:
		image: prometheuscommunity/postgres-exporter
		ports:
			- 9187:9187
		environment:
			DATA_SOURCE_NAME: "postgresql://postgres:postgres:postgrespassword@postgres:5432/?sslmode=disable"
		links:
			- postgres
			- prometheus
```


>[!important] Importante!
>Para monitorear todas las instancias de Postgres en la variable de entorno de postgres-exporter no pondremos nada despues del puerto en caso de querer monitorear una database en concreto añadiremos al final `/nombreBaseDatos`
>environment:
			DATA_SOURCE_NAME: "postgresql://postgres:postgres:postgrespassword@postgres:5432/shop?sslmode=disable"
		
>[!warning] Recordar!!
>Recordar de cambiar las credenciales de acceso al servidor postgres en DATA_SOURCE_NAME:

## Configuraciones en Grafana 

Una vez este en ejecución el docker-compose accedemos a localhost:3000 para ver la interfaz de Grafana, introducimos las credenciales usr:admin pass:admin y agregamos un nuevo Data source

![[Captura de pantalla 2024-04-18 a las 11.29.33.png]]

Seleccionamos prometheus y añadimos la dirección al contenedor de prometheus de nuestro localhost, al final de la página le damos a guardar.

![[Captura de pantalla 2024-04-18 a las 11.34.31.png]]

![[Captura de pantalla 2024-04-18 a las 11.38.44.png]]

En la barra de navegación lateral, seleccionamos dashboard y importamos, una vez en la siguiente pantalla buscamos el numero 12485. [link externo a dashboard](https://grafana.com/grafana/dashboards/12485-postgresql-exporter/)


![[Captura de pantalla 2024-04-18 a las 11.43.29.png]]

![[Captura de pantalla 2024-04-18 a las 11.43.55.png]]

Como DS_PROMETHEUS seleccionamos el prometheus dataSource que cargamos anteriormente

![[Captura de pantalla 2024-04-18 a las 11.45.53.png]]