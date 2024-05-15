### *Sublenguaje DCL (Data Control Language)*

Control de acceso a los datos, Este sublenguaje nos permite otorgar permisos individualmente o en grupos (roles) para realizar operaciones específicas en los objetos de la base de datos.

Comandos disponibles en este sublenguaje:
- `GRANT`: para otorgar permisos.
- `REVOKE`: para eliminar permisos otorgados.

Los permisos pueden ser otorgados individualmente o de manera colectiva usando roles. 

Preguntas importantes antes para definir un Rol:

- ¿El usuario participa en la administración del sistema?
- ¿El usuario necesita editar o crear datos?
- ¿El usuario solo necesita consultar datos?

Los *privilegios* se pueden otorgar sobre diferentes tipos de recursos, como tablas, secuencias, bases de datos, esquemas, espacios de tablas y funciones.

El comando `GRANT` tiene múltiples sintaxis dependiendo de los tipos de recursos a los que se aplica.

>[!Nota] Nota
>[Enlace a documentación oficial del comando `GRANT`](https://www.postgresql.org/docs/current/sql-grant.html)

Ejemplo para GRANT en tablas

```sql
GRANT { { SELECT | INSERT | UPDATE | DELETE | TRUNCATE | REFERENCES | TRIGGER } [, ...] | ALL [ PRIVILEGES ] } ON { [ TABLE ] nombre_de_tabla [, ...] | TODAS LAS TABLAS EN EL ESQUEMA nombre_de_esquema [, ...] } TO especificación_de_rol [, ...] [ WITH GRANT OPTION ]
```
##### Explicación de la sintaxis

La parte `GRANT { { SELECT | INSERT | UPDATE | DELETE | TRUNCATE | REFERENCES | TRIGGER } [, ...] | ALL [ PRIVILEGES ] }` especifica los privilegios que se están otorgando. Estos pueden ser uno o varios privilegios específicos, como `SELECT`, `INSERT`, `UPDATE`, etc., o bien `ALL PRIVILEGES`, que otorga todos los privilegios disponibles.

La parte `ON { [ TABLE ] nombre_de_tabla [, ...] | TODAS LAS TABLAS EN EL ESQUEMA nombre_de_esquema [, ...] }` especifica sobre qué objetos se están otorgando los privilegios. Puede ser una o varias tablas específicas, o bien todas las tablas en un esquema específico.

La parte `TO especificación_de_rol [, ...]` especifica a qué roles se están otorgando los privilegios. Puede ser un rol específico o varios roles.

Por último, `[ WITH GRANT OPTION ]` es un modificador opcional que indica si los roles a los que se otorgan los privilegios también pueden otorgar esos mismos privilegios a otros roles.

#### Mas ejemplos

```sql
-- Otorgar el privilegio de inserción de datos en la tabla `juegos` a todos:
GRANT INSERT ON juegos TO PUBLIC;
---- Otorgar todos los privilegios sobre la tabla `tareas` a `antonio` con opción de administración:
GRANT ALL PRIVILEGES ON tareas TO antonio WITH GRANT OPTION;
---- Añadir al usuario `joan` al grupo de administradores:
GRANT admin_role TO joan;

```





### **Eliminando privilegios de acceso**

- Al igual que SQL ofrece mecanismos para otorgar derechos, también los ofrece para revocarlos.
- Para eliminar derechos otorgados previamente, usamos el comando `REVOKE`, que tiene la misma complejidad que el comando `GRANT`.

**Sintaxis de REVOKE para tablas**:

```sql
REVOKE [ GRANT OPTION FOR ]
{ { SELECT | INSERT | UPDATE | DELETE | TRUNCATE | REFERENCES | TRIGGER } [, ...] | ALL [ PRIVILEGES ] }
ON { [ TABLE ] nombre_de_tabla [, ...] | TODAS LAS TABLAS EN EL ESQUEMA nombre_de_esquema [, ...] }
FROM especificación_de_rol [, ...] [ CASCADE | RESTRICT ]

----------------
--- EJEMPLOS ---
----------------

---- Revocar el privilegio de inserción en la tabla `juegos` a todos:
REVOKE INSERT ON juegos FROM PUBLIC;
--- - Revocar todos los privilegios sobre la tabla `tareas` a `antonio`:
REVOKE ALL PRIVILEGES ON tareas TO antonio;
---- Revocar el grupo de administradores a `joan`:
REVOKE administradores TO joan;
```

>[!important] Comando útil
> *( Dentro del Shell de postgres ) *
>para listar roles `\dp nombreTabla`

Significado de la salida de `\dp`:
r -- SELECT ("read") w -- UPDATE ("write")
a -- INSERT ("append") d -- DELETE
D – TRUNCATE x -- REFERENCES
t – TRIGGER X -- EXECUTE
U – USAGE C -- CREATE
c – CONNECT T -- TEMPORARY

arwdDxt -- ALL PRIVILEGES (for tables, varies for other objects)
* -- grant option for preceding privilege