
PostgreSQL administra los permisos de acceso a la base de datos utilizando el concepto de roles. Un rol puede ser considerado como un usuario de la base de datos o como un grupo de usuarios de la base de datos, dependiendo de cómo se configure el rol.

Los roles pueden ser propietarios de objetos de la base de datos (como tablas y funciones) y pueden asignar privilegios sobre estos objetos a otros roles para controlar quién tiene acceso a qué objetos. Además, es posible otorgar la pertenencia a un rol a otro rol, lo que permite al rol miembro utilizar los privilegios asignados al rol maestro, Cualquier rol puede actuar como usuario, grupo o ambos.

>[!example] Docs Externos relacionados de interes
>[ Recurso externo para CREATE ROLE](https://www.postgresqltutorial.com/postgresql-administration/postgresql-roles/)
[ Recurso externo ALTER ROLE ](https://www.postgresqltutorial.com/postgresql-administration/postgresql-alter-role/)

[`Doc oficial - CREATE ROLE`](https://www.postgresql.org/docs/current/sql-createrole.html)

```sql
CREATE ROLE _`name`_ [ [ WITH ] _`option`_ [ ... ] ]
```

[`Doc oficial - ALTER ROLE`](https://www.postgresql.org/docs/current/sql-alterrole.html)

```sql
ALTER ROLE _`role_specification`_ [ WITH ] _`option`_ [ ... ]
```

[`Doc oficial - DROP ROLE`](https://www.postgresql.org/docs/current/sql-droprole.html)

```sql
DROP ROLE [ IF EXISTS ] _`name`_ [, ...]
```

Existen utilidades de línea de comandos proporcionadas con PostgreSQL
como `createuser` y `dropuser` para administrar usuarios de bases de datos,
se proporcionan como envoltorios alrededor de estas órdenes SQL que se pueden invocar desde la shell:

[Doc oficial a `CREATE USER`](https://www.postgresql.org/docs/8.0/sql-createuser.html)

`createuser nombre; `
`dropuser nombre;`

>[!note] Comando útil
> *( Dentro del Shell de postgres ) *
>para listar roles `\du`


Inicialmente, una instalación de PostgreSQL contiene roles predefinidos:

>[!importante] Importante
> *superuser*: Este rol suele llamarse `postgres` y para crear más roles, es necesario conectarse primero como este rol inicial.

Todas las conexiones al servidor de bases de datos se realizan utilizando el nombre de algún rol específico, 

Este rol determina los privilegios de acceso iniciales para las órdenes emitidas en esta conexión. 

Por esto es importante configurar cuidadosamente los privilegios al configurar un entorno multiusuario.

### Cambiar de ROLE en curso

#### **SET ROLE**

Cambiar temporalmente el rol actual dentro de la sesión de PostgreSQL. El usuario actual asume temporalmente los privilegios y permisos del rol especificado. 

Pero el usuario que se ha autenticado para acceder a la base de datos no cambia. El cambio de rol es temporal y no afecta a la autenticación de la sesión en sí misma.

- `SESSION`: Este modificador establece el rol para toda la sesión actual.
- `LOCAL`: Este modificador establece el rol solo para la transacción actual

```sql
SET [ SESSION | LOCAL ] ROLE nombre_del_rol;

SET ROLE 'nombre_del_rol';
```

 #### **SET SESSION AUTHORIZATION**:

Estamos cambiando tanto el rol actual como el identificador de usuario de sesión. No solo los privilegios y permisos del rol cambian para la sesión actual, sino que también la identidad del usuario autenticado en la sesión cambia permanentemente a otro usuario. 

Esto afecta a todas las operaciones realizadas durante la sesión, ya que ahora se realizan en nombre de un usuario diferente.

- - `SESSION`: Este modificador establece el usuario de sesión para toda la sesión actual.
- `LOCAL`: Este modificador establece el usuario de sesión solo para la transacción actual.

```sql
SET [ SESSION | LOCAL ] SESSION AUTHORIZATION nombre_de_usuario;
SET SESSION AUTHORIZATION 'nombre_de_usuario';

```