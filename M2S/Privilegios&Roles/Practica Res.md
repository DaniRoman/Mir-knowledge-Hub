
#### Creación de una Base de Datos:
    - Base de datos ya creada desde docker-compose.
#### Creación de Tablas:
	- Creo un volumen en docker-compose para cargar los scripts que contienen las tablas
#### Crear usuarios

```plsql
-- Crear el usuario "admin" con contraseña
CREATE USER admin WITH PASSWORD 'admin_pass';
-- Crear el usuario "gerente" con contraseña
CREATE USER gerente WITH PASSWORD 'gerente_pass';
-- Crear el usuario "desarrollador" con contraseña
CREATE USER desarrollador WITH PASSWORD 'desarrollador_pass';
-- Crear el usuario "cliente" con contraseña
CREATE USER cliente WITH PASSWORD 'cliente_pass';
```

>[!tip] comandos
> ver usuarios/roles `\du`

#### Creación de Roles:
    
Crea roles en PostgreSQL, como "administrador_sistema", gerente_proyecto, desarrollador, cliente. 
```plsql
-- Crear el rol "administrador_sistema"
CREATE ROLE admin_rol;
-- Crear el rol "gerente_proyecto"
CREATE ROLE gerente_rol;
-- Crear el rol "desarrollador"
CREATE ROLE desarrollador_rol;
-- Crear el rol "cliente"
CREATE ROLE cliente_rol;		```

#### Asignación y eliminación de Roles a los usuarios:**


 ```plsql
 -- Asignar el rol administrador_sistema al usuario "admin"
GRANT admin_rol TO admin;
-- Asignar el rol gerente_proyecto al usuario "gerente"
GRANT gerente_rol TO gerente;
-- Asignar el rol desarrollador al usuario "desarrollador"
GRANT desarrollador_rol TO desarrollador;
-- Asignar el rol cliente al usuario "cliente"
GRANT cliente_rol TO cliente;

REVOKE nombre_de_rol FROM nombre_de_usuario;
 ```
 Ver el rol asignado
>[!note]
>```SELECT r.rolname AS rol_asignado 
>FROM pg_roles r JOIN pg_auth_members m ON r.oid = m.roleid 
>WHERE m.member = (SELECT oid FROM pg_roles WHERE rolname = 'nombre_rol');```

#### Asignar acceso a la base de datos a los usuarios:

```plsql
-- Otorgar acceso a la base de datos a todos los usuarios
GRANT CONNECT ON DATABASE tu_base_de_datos TO admin, gerente, desarrollador, cliente;
	```
	
>[!Error]
>Si alguno sigue sin poder login `ALTER ROLE desarrollador LOGIN;`


#### Concesión de Privilegios:

[[Ejemplo de caso de uso para definir roles]]
	
#### Asignar o quitar privilegios a las tablas y Permisos a Roles

```plsql
-- Asignar privilegios a la tabla usuarios
	GRANT SELECT, INSERT, UPDATE, DELETE ON usuarios TO admin_rol;
	-- Asignar privilegios a la tabla proyectos
	GRANT SELECT, INSERT, UPDATE, DELETE ON proyectos TO admin_rol, gerente_rol;
	-- Asignar privilegios a la tabla tareas
	GRANT SELECT, INSERT, UPDATE, DELETE ON tareas TO admin_rol, gerente_rol, desarrollador_rol;
	GRANT SELECT ON proyectos TO cliente; GRANT SELECT ON tareas TO cliente_rol;

	REVOKE privilegio1, privilegio2 ON nombre_de_tabla FROM nombre_de_rol;
```
 
##### Ver privilegios
###### Ver los privilegios de tabla para un rol específico: 

```plsql
	SELECT *
	FROM information_schema.table_privileges
	WHERE grantee = 'nombre_del_rol';
```


###### Verificar si un rol tiene privilegios especifico sobre una tabla

```plsql
	SELECT has_table_privilege('nombre_del_rol', 'nombre_de_la_tabla', 'privilegio');

```
#### Verificación de Permisos:*

[[Verificación de permisos para los roles]]

#### Revocación alteración de Permisos y Eliminación de Roles:**
    
- Revoca permisos de roles o usuarios según sea necesario y elimina roles que ya no son necesarios.