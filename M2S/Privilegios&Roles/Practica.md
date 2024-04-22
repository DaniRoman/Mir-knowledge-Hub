[Recurso Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps-2)
#### Creación de una Base de Datos:
    - Base de datos ya creada desde docker-compose.
#### Creación de Tablas:
	- Creo un volumen en docker-compose para cargar los scripts que contienen las tablas
#### Crear usuarios

 Crear usuarios admin, gerente, desarrollador, cliente
 
 [CREATE USER](https://www.postgresql.org/docs/8.0/sql-createuser.html)
 
>[!tip] comandos
> ver usuarios/roles `\du`

#### Creación de Roles:
    
Crea roles admin_rol, gerente_rol, desarrollador_rol, cliente_rol. 
[CREATE ROLE]()

#### Asignación y eliminación de Roles a los usuarios:

Asigna los roles al los usuarios creados

[GRANT REVOKE](https://www.postgresql.org/docs/current/role-membership.html)

>[!Tip] Ver los roles que tiene un usuario 
>
>```plsql 
>SELECT r.rolname AS rol_asignado 
>FROM pg_roles r JOIN pg_auth_members m ON r.oid = m.roleid 
>WHERE m.member = (SELECT oid FROM pg_roles WHERE rolname = 'nombre_rol');
>
>```


#### Asignar acceso a la base de datos a los usuarios:

Define el privilegio de conexión a la base de datos a los usuarios creados 

[GRANT CONNECT...](https://www.postgresql.org/docs/current/sql-grant.html)

>[!warning]
>Si alguno sigue sin poder login en algún usuario`ALTER ROLE nombre_usuario LOGIN;`


#### Concesión de Privilegios:

[[Ejemplo de caso de uso para definir roles]]
	
#### Asignar o quitar privilegios a las tablas y Permisos a Roles

Asigna a cada role los privilegios que tendrán sobre las tablas

[GRANT {...}](https://www.postgresql.org/docs/current/sql-grant.html) 
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
#### Verificación de Permisos:

[[Verificación de permisos para los roles]]

#### Revocación alteración de Permisos y Eliminación de Roles:**
    
- Revoca permisos de roles o usuarios según sea necesario y elimina roles que ya no son necesarios.


#### Para continuar...

podemos definir los permisos y restricciones con un mayor nivel de detalle. En lugar de asignar permisos generales a un usuario o rol, podemos especificar exactamente qué acciones puede realizar un usuario en tablas específicas, columnas, funciones y otros objetos de la base de datos.

En lugar de otorgar a un usuario el permiso de lectura (`SELECT`) en todas las tablas de la base de datos, otorgarle el permiso de lectura solo en ciertas tablas o incluso en ciertas columnas de esas tablas. Esto permite una gestión más precisa y segura de los privilegios de acceso a los datos.