[[PostgreSQL Commands]]
## Creación de una base de datos
[Recurso externo para Crear una base de datos](https://www.postgresqltutorial.com/postgresql-administration/postgresql-create-database/)
## Comando CREATE TABLE:

### Creación de tablas
[Recurso externo para crear una tabla](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-create-table/)

```sql
CREATE TABLE [IF NOT EXISTS] table_name (
   column1 datatype(length) column_constraint,
   column2 datatype(length) column_constraint,
   ...
   table_constraints
);
```

En esta sintaxis:

- El nombre de la tabla a crear después de `CREATE TABLE`.
- El nombre de la tabla debe ser único en un esquema. 

>[!tip]
Un *esquema* es una colección con nombre de objetos de base de datos que incluye tablas. Si crea una tabla sin un esquema, por defecto será pública.

- **IF NOT EXISTS** para crear una nueva tabla sólo si no existe evitando un error en caso de esta existir. 
- Especificar las columnas de la tabla separadas por comas. 
- Cada definición de columna consiste en el nombre de la columna, el tipo de datos, el tamaño y la restricción.
- **column_constraint** ( **restricciones de columna** ) de una columna especifica una regla que se aplica a los datos dentro de una columna, clave primaria, clave foránea, no nulo, único, comprobación y predeterminado.

### Tipos de Datos

PostgreSQL soporta los siguientes tipos de datos:

- [Booleano](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-boolean/)
- Tipos de [Caracteres](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-char-varchar-text/) como `char`, `varchar` y `text`.

>[!tip]
> Diferencia entre `VARCHAR(2)`, `CHAR(2)`, radica en cómo manejan el almacenamiento en la base de datos. `VARCHAR(2)` solo utiliza el espacio necesario para almacenar la cadena, mientras que `CHAR(2)` utiliza un espacio fijo, lo que puede llevar a un desperdicio de espacio si la mayoría de las cadenas son más cortas que la longitud máxima especificada.

- Tipos numéricos como integer y floating-point number.

>[!Tip]
>[[Diferencia entre Numeric y float]] - [Recurso externo a Integer](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-integer/)

- [Serial](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-serial/) Create Auto-increment Column
- Tipos temporales como [date](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-date/), time, timestamp e interval.
- [UUID](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-uuid/) para almacenar identificadores únicos universales.

>[!Tip]
>[[Casos necesarios para utilizar un UUID]]

- [Array](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-array/) para almacenar cadenas de array, números, etc.
- [JSON](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-json/) almacena datos JSON
- [hstore](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-hstore/) almacena pares clave-valor
- Tipos especiales como dirección de red y datos geométricos.

>[!tip]
>[Recurso Externo a tipos de datos](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-data-types/)

### Restricciones

PostgreSQL incluye las siguientes restricciones de columna:

[*PRIMARY KEY* ](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-primary-key/)- una columna de clave primaria identifica de forma única las filas de una tabla. Una tabla sólo puede tener una clave primaria. La restricción de clave primaria permite definir la clave primaria de una tabla.

>[!Tip] 
>[Identity column](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-identity-column/) [[Diferencia entre Identity y serial]]

[*NOT NULL*](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-not-null-constraint/) - asegura que los valores de una columna no pueden ser *NULL*.
[*UNIQUE* ](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-unique-constraint/)- asegura que los valores de una columna son únicos entre las filas de la misma tabla.
[*CHECK*](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-check-constraint/) - garantiza que los datos deben satisfacer una expresión booleana. Por ejemplo, el valor de la columna precio debe ser cero o positivo.
[FOREIGN KEY](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-foreign-key/)- garantiza que los valores de una columna o grupo de columnas de una tabla existen en una columna o grupo de columnas de otra tabla. A diferencia de la clave primaria, una tabla puede tener muchas claves externas.
[[Notas a Foreign Key]]
[DEFAULT](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-default-value/) asignar un valor por defecto a una columna

Por último, especificar las restricciones para la tabla, incluidas las restricciones de clave primaria, clave externa y comprobación.
### Restricciones de tabla

Las restricciones de tabla son similares a las restricciones de columna, salvo que se puede incluir más de una columna en la restricción de tabla.

```sql
CREATE TABLE example_table (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) ,
    age INT CHECK (age >= 18) UNIQUE,
    foreign_key_column INT REFERENCES other_table(id),
    
    CONSTRAINT custom_constraint_name UNIQUE (id, age)
);

```
sintaxis para table constrain

1. **constraint_name:** Es el nombre opcional que se le da a la restricción. Si se proporciona, este nombre debe ser único dentro del ámbito de la tabla.
    
2. **constraint_definition:** Define la restricción específica. Puede ser una PRIMARY KEY, FOREIGN KEY, UNIQUE o CHECK constraint aplicada a una columna específica.
    
3. **table_constraint:** Define restricciones que se aplican a nivel de tabla, es decir, restricciones que involucran múltiples columnas o no están asociadas a una columna específica. Por ejemplo, CONSTRAINT constraint_name constraint_definition.
    

Aquí otro ejemplo usando CHECK

podrías querer asegurarte de que ninguna persona en la tabla tenga una edad menor de 18 años y, al mismo tiempo, que su país de residencia sea "Estados Unidos". Esto implica verificar dos columnas diferentes al mismo tiempo: "edad" y "país". Esta sería una restricción de tabla.

```sql
CREATE TABLE Personas (
    Nombre VARCHAR(50),
    Edad INTEGER,
    País VARCHAR(50),
    CONSTRAINT chk_Edad_Pais CHECK (Edad >= 18 AND País = 'Estados Unidos')
);
```


## Comando DROP TABLE

Para eliminar una tabla de la base de datos

**sintaxis**

```sql
DROP TABLE [IF EXISTS] table_name 
[CASCADE | RESTRICT];
```

- Especificar el nombre de la tabla que desea eliminar después de las palabras clave DROP TABLE.
- Utilizar la opción IF EXISTS para eliminar la tabla sólo si existe.
Si elimina una tabla que no existe, PostgreSQL emitirá un error. 

- Si la tabla se utiliza en otros objetos de base de datos como vistas, disparadores, funciones y procedimientos almacenados, no se podrá eliminar . 
- En este caso, tiene dos opciones:

	- Utilizar la opción CASCADE para eliminar la tabla y sus objetos dependientes.
	- Utilizar la opción RESTRICT rechaza la eliminación si existe algún objeto dependiente de la tabla. La opción RESTRICT es la predeterminada si no la especifica explícitamente en la sentencia DROP TABLE.

**sintaxis** para varias tablas

```sql
DROP TABLE [IF EXISTS] 
   table_name_1,
   table_name_2,
   ...
[CASCADE | RESTRICT];
```


## Comando ALTER 

[Documentación a Fuente externa](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-alter-table/)

Para modificar la estructura de una tabla existente

*Sintaxis*

```sql
ALTER TABLE table_name action;
```

### *Acciones*

#### Add a column

añadir una nueva columna a una tabla

```sql
ALTER TABLE table_name 
ADD COLUMN column_name datatype column_constraint;
```

#### Drop a column
Borrar una columna de una tabla

```sql
ALTER TABLE table_name 
DROP COLUMN column_name;
```

#### Rename a column

Renombrar una columna

```sql
ALTER TABLE table_name 
RENAME COLUMN column_name 
TO new_column_name;f
```

#### Alter column set default

Para cambiar el valor por defecto de una columna

```sql
ALTER TABLE table_name 
ALTER COLUMN column_name 
[SET DEFAULT 'value' | DROP DEFAULT];
```


#### Change column constraint

```sql
ALTER TABLE table_name 
ALTER COLUMN column_name 
[SET NOT NULL| DROP NOT NULL];
```

>[!IMPORTANT] Importante!
>No funciona con PRIMARY KEY O FOREIGN tenemos que usar ADD CONSTRAINT


#### Add check constraint

```sql
ALTER TABLE table_name 
ADD CHECK expression;
```

#### Add | RemoveConstraint to a table

```sql
ALTER TABLE table_name 
[ADD CONSTRAINT | DROP CONSTRAINT] constraint_name constraint_TYPE_ (column);
```

>[!important] Importante!
>Si tenemos objetos que dependen de la restriccion, como eliminar una PRIMARY KEY que tiene una fk en otra tabla 
>```sql 
>DROP CONSTRAINT pk_table CASCADE;
>``` 


#### Change the data type o a column


```sql
ALTER TABLE table_name
ALTER COLUMN column_name 
TYPE new_data_type;
```

>[!Error] Error!
>Si intentas cambiar un tipo de dato a SERIAL no sera posible ya que este es un pseudo tipo, y se debe de eliminar la columna y crear una de nuevo. con `ADD | DROP column_name TYPE`



#### Rename table

```sql
ALTER TABLE table_name 
RENAME TO new_table_name;
```
## Comando TRUNCATE TABLE

Para eliminar todos los datos de una tabla, se utiliza la sentencia DELETE sin cláusula WHERE. Sin embargo, cuando la tabla tiene muchos datos, la sentencia DELETE no es eficaz. En este caso, puede utilizar la sentencia TRUNCATE TABLE.

```sql
DELETE FROM table_name;
```

La sentencia TRUNCATE TABLE elimina todos los datos de una tabla muy rápidamente

```sql
TRUNCATE TABLE table_name;

TRUNCATE TABLE 
    table_name1, 
    table_name2,
    ...;
```

Para eliminar los datos de una tabla y de otras tablas que tengan referencias de clave externa a la tabla, se utiliza la opción CASCADE en la TABLA TRUNCATE

```sql
TRUNCATE TABLE table_name 
CASCADE;
```
## Crear Indices

Se utilizan para acceder más rápidamente a los datos.
determinar y evaluar los accesos que se efectúan en los datos (SELECT) y determinar si se necesitan nuevos índices.

Los índices también presentan algunos inconvenientes.

- Los índices son pequeñas tablas resumen que se crean por cada columna que tiene las restricciones de clave primaria o unicidad. Por tanto, ocupan un espacio físico en el disco.
- En tablas pequeñas no mejora el tiempo de acceso a los datos.
- En tablas donde hay muchas operaciones de actualización, por cada
actualización (INSERT, UPDATE o DELETE) deben actualizarse también
los índices.

*Sintaxis*

 ```sql
 CREATE INDEX [IF NOT EXISTS] index_name
ON table_name(column1, column2, ...);
 ```

- Especificar el nombre del índice después de la cláusula CREATE INDEX.
- Utilizar la opción IF NOT EXISTS para evitar un error si el índice ya existe.
 - Indicar el nombre de la tabla a la que pertenece el índice.
- Enumerar una o más columnas indexadas dentro del () después del nombre de la tabla.

Podemos mostrar el plan de consulta como ejemplo (Utilizando la base de datos dvdrental)

```sql
EXPLAIN SELECT 
  address_id, 
  address, 
  district, 
  phone 
FROM 
  address 
WHERE 
  phone = '223664661973';
```

```sh
                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on address  (cost=0.00..15.54 rows=1 width=45)
   Filter: ((phone)::text = '223664661973'::text)
(2 rows)
```

Para encontrar la fila cuyo valor en la columna teléfono es 223664661973, PostgreSQL debe escanear toda la tabla de direcciones.

La salida indica que el optimizador de consultas tiene que realizar una exploración secuencial en la tabla de direcciones.

*Creamos un indice*

```sql
CREATE INDEX idx_address_phone 
ON address(phone);
```

Cuando ejecuta la sentencia CREATE INDEX, PostgreSQL escanea la tabla address, extrae los datos de la columna phone y los inserta en el índice idx_address_phone.

Este proceso se denomina creación de índice. Por defecto, PostgreSQL permite lecturas de la tabla de direcciones y bloquea las operaciones de escritura mientras construye el índice.

Volvemos a ejecutar la consulta con EXPLAIN

```sh
                                    QUERY PLAN
----------------------------------------------------------------------------------
 Index Scan using idx_address_phone on address  (cost=0.28..8.29 rows=1 width=45)
   Index Cond: ((phone)::text = '223664661973'::text)
(2 rows)
```

La salida indica que PostgreSQL utiliza el índice idx_address_phone para la búsqueda.

Eliminar el indice

```sql
DROP INDEX nombreIndice;
```

## Crear vistas

>[!warning] Nota
>[Enlace externo a documentación](https://www.postgresqltutorial.com/postgresql-views/)

Es una consulta con nombre almacenada en el servidor. Una vista se define en base a una o más tablas que se conocen como tablas base, y la consulta que define la vista se denomina consulta definidora.

( **tablas virtuales que se generan a partir de un Select de una o varias tablas**. Las vistas no almacenan datos propios, sino que muestran información que se encuentra en otras tablas.)

1) Simplificación de consultas complejas
Las vistas ayudan a simplificar las consultas complejas. En lugar de tratar con uniones, agregaciones o condiciones de filtrado, puede realizar consultas desde vistas como si fueran tablas normales.

Normalmente, primero se crean vistas basadas en consultas complejas y se almacenan en la base de datos. A continuación, se pueden utilizar consultas sencillas basadas en vistas en lugar de consultas complejas.

2) Seguridad y control de acceso
Las vistas permiten un control detallado del acceso a los datos. Se pueden crear vistas que expongan subconjuntos de datos en las tablas base, ocultando información sensible.

Esto resulta especialmente útil cuando hay aplicaciones que necesitan acceder a distintas partes de los datos.

3) Independencia lógica de los datos
Podemos modificar libremente la estructura de las tablas base. En otras palabras, las vistas permiten crear una capa de abstracción sobre las tablas subyacentes.

*Sintaxis* 

```sql
CREATE VIEW view_name 
AS 
  query;
```

Ejemplo

```sql
CREATE VIEW contact AS 
SELECT 
  first_name, 
  last_name, 
  email 
FROM 
  customer;


SELECT * FROM contact;
```

*Output*

```sh
 first_name  |  last_name   |                  email
-------------+--------------+------------------------------------------
 Jared       | Ely          | jared.ely@sakilacustomer.org
 Mary        | Smith        | mary.smith@sakilacustomer.org
 Patricia    | Johnson      | patricia.johnson@sakilacustomer.org
...
```

### Remplazar una vista

Si la vista ya existe, la sentencia reemplaza la vista existente; en caso contrario, crea una nueva vista.

```sql
CREATE OR REPLACE VIEW view_name 
AS 
  query;
```

Usa el comando `\d+` para mostrar las vistas de la base de datos

## Añadir Datos desde un Script y enlace a DML - 1

>[!Important] Importante
> Y ahora, a Insertar datos!!! Enlace para Insertar, borrar y actualizar los datos [[DML-1]]

>[!Important] Importante
> Normalmente cuando tenemos una gran cantidad de datos ya sea de creación de tablas modificación de estas o insertar una gran cantidad de datos , crearía estos en un fichero (script) y luego mediante un comando de terminal de PostgreSQL añadiría todos esos datos de golpe a la base de datos.
> [[Commands related to Backup]]
## Actividades
### Actividad 01
[[Actividad 01 (DDL)]]
[[Actividad 01 (DDL) solución]]

### Actividad 02
[[Actividad 02 ( DDL )]]
[[Actividad 02 ( DDL ) Solución]]

## Actividad 03
[[Actividad 03 ( DDL + DML -1 ) Solución]]
[[Actividad 03 ( DDL + DML -1)]]

