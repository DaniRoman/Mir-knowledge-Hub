#### Configurar el entorno
##### Script para hacer la practica

- Entra dentro del contenedor `pg_container`
- Entra en la base de datos por defecto de Postgres y crea la base de datos `adminrolesdb`

```sh
docker exec -it pg_container bash`

psql -U postgres

CREATE DATABASE adminrolesdb;
```

Una vez creada esa base de datos crea un script en la carpeta `postgres-sampleDB/script/adminrolesDb.sql` para cargar el contenido de este en la base de datos.

```sql
DROP TABLE IF EXISTS project_manager,developer,project,task,client;

  
CREATE TABLE IF NOT EXISTS project_manager (
	id SERIAL,
	name VARCHAR(100),
	
	CONSTRAINT project_manager_pk PRIMARY KEY (id)
	);

  
CREATE TABLE IF NOT EXISTS developer (
	id SERIAL,
	name VARCHAR(100),
	
	CONSTRAINT developer_pk PRIMARY KEY (id)

);

CREATE TABLE IF NOT EXISTS project (
	id SERIAL,
	developer_id SMALLINT,
	name VARCHAR(100),
	creation_date DATE,
	limit_date DATE,
	
	CONSTRAINT project_pk PRIMARY KEY (id),
	
	CONSTRAINT developer_fk
		FOREIGN KEY (developer_id)
			REFERENCES developer (id)

);

  
CREATE TABLE IF NOT EXISTS task (
	id SERIAL,
	project_id SMALLINT,
	developer_id SMALLINT,
	name VARCHAR(100),
	state VARCHAR(10) DEFAULT 'To do',
	fecha_creacion DATE,
	fecha_limite DATE,
	
	CONSTRAINT task_pk PRIMARY KEY (id),
	
	CONSTRAINT project_fk
		FOREIGN KEY (project_id)
			REFERENCES project (id),
	
	  
	CONSTRAINT developer_fk
		FOREIGN KEY (developer_id)
			REFERENCES developer (id),

	CONSTRAINT state_values CHECK (state IN ('To do', 'Doing', 'Done'))

);

  
CREATE TABLE IF NOT EXISTS client (
	id SERIAL,
	project_id SMALLINT,
	name VARCHAR(100),
	
	CONSTRAINT project_fk
		FOREIGN KEY (project_id)
			REFERENCES project(id)
);

INSERT INTO project_manager (name)
VALUES ('project_manager-1');

INSERT INTO developer (name)
VALUES
('developer1'),
('developer2');

INSERT INTO project
	(developer_id,
	name,
	creation_date,
	limit_date)
VALUES
(1,'project-1', '2024-02-13', '2024-04-13'),
(2,'project-2', '2024-01-23', '2024-03-23');

INSERT INTO client
	(project_id,
	name)
VALUES 
	(1, 'Joquim Mir'),
	(2, 'Mir');

INSERT INTO task
	(project_id,
	developer_id,
	name,
	fecha_creacion,
	fecha_limite)
VALUES
(1, 1, 'Add-Git', '2024-02-13', '2024-02-13'),
(1, 1, 'Add Node Modules', '2024-02-13', '2024-02-13'),
(2, 2, 'Add Docker Image', '2024-02-13', '2024-02-13');
```

Carga el script

`psql -U postgres -d adminrolesdb -f scripts/adminrolesdb.sql`

Comprueba que existe la base de datos y el contenido de esta.

```sh
#Conectarme a la base de datos
psql -U postgres 

#Listar base de datos
\l
#Cambiar a la base de datos 
\c adminrolesdb
#Listar las tablas de la base de datos
\dt

SELECT * FROM projects;
```
#### Crea usuarios ( 0,5p )

Inicia sesión como superuser y Crear cinco usuario de la manera que quieras con `CREATE ROLE, CREATE USER ` o con  utilidades de línea de comandos `createuser` 

- `project_manager`
- 2`soft_developer`
- 2`client` 

```plsql
---- Inserte aqui su codigo joven

```

>[!tip] comandos
> ver usuarios/roles `\du`

#### Creación de Roles: ( 0,5p )

Crea los roles siguientes

- `project_manager_role`
- `developer_role`
- `client_role` 

```plsql
---- Inserte aqui su codigo joven

```

#### Asignación de Roles a los usuarios:( 0,5p )

Asigna los Roles que creaste anteriormente a los usuarios correspondientes

```plsql
---- Inserte aqui su codigo joven

```

#### Creación de una vista para el Developer y el Cliente ( 1p )

Crea una vista donde el Desarrollador pueda los proyectos y las tareas que tiene asignadas.
Crea una vista donde el Cliente pueda ver los proyectos en los que esta involucrado.

>[!important] Importante!
>En la ultima clausula utiliza`WHERE name = current_user` para que la vista solo muestre los datos para el developer que esta actualmente conectado .

```plsql
---- Inserte aqui su codigo joven

```

#### Concesión de Privilegios ( 3p ):

Asignar  privilegios a las tablas Para los roles definidos y haz las comprovaciónes de lo que si pueden hacer.

Project Manager_role

- `Project` - Control total.
- `project_manager` - Solo ver.
- `Task` - Solo ver.
- `Client` - Solo ver. 
- `developer` - Solo ver.

*Developer_role

- `vista_custom` - Solo ver.
- `Task` - Control total menos borrar.

*Client_role*

- `project` Solo puede ver los proyectos a los que esta asignado.

```plsql
---- Inserte aqui su codigo joven

```

*INSERTA AQUI LAS FOTOS QUE PIDE EL ENUNCIADO PARA LAS COMPROBACIÓNES CORRESPONDIENTES.*

>[!important] Nota!
>Si quieres puedes comprobar permisos de esta manera.

#### Ver Permisos

```sql
---Ver los privilegios de tabla para un rol específico
SELECT 
    table_schema,
    table_name,
    privilege_type
FROM 
    information_schema.table_privileges
WHERE 
    grantee = 'nombre_del_rol';

---Verificar si un rol tiene privilegios especifico sobre una tabla
	SELECT has_table_privilege('nombre_del_rol', 'nombre_de_la_tabla', 'privilegio');
```
#### Verificación de Permisos:* ( 3p )

Az comprobaciones con los roles (usuarios) para ver que no pueden hacer  sobre los objetos de la base de datos.

Project Manager_role

- `project_manager` - Borrar sus datos.
- `Task` - Actualizar una tarea.
- `Client` - Eliminar un cliente. 
- `developer` - Eliminar un desarrollador.

*Developer_role
- `Project` - Ver todos los proyectos.
- `project_manager` Ver los datos del project manager.
- `Task` - Borrar una tarea.

*Client_role*

- `project` Ver todos los projectos de la empresa.

*INSERTA AQUI LAS FOTOS QUE PIDE EL ENUNCIADO PARA LAS COMPROBACIÓNES CORRESPONDIENTES.*

#### Revocación alteración de Permisos y Eliminación de Roles:** ( 1.5p )

La empresa a echo cambios de política y tenemos que actualizar algunos permisos y eliminar otros.

Project Manager_role

- `Task` - Actualizar una tarea.
- `Client` - ya no puede eliminar un cliente. 

*Client_role*

- Elimina este rol

*Developer_role
- `Task` - Ya no puede ni añadir ni actualizar.

```sql
REVOKE DELETE ON client FROM project_manager;
GRANT INSERT ON task TO project_manager;

REVOKE INSERT UPDATE ON task FROM task;

DROP ROLE client;
```
