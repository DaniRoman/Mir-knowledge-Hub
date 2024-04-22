## Insert

La sentencia INSERT de PostgreSQL permite insertar una nueva fila en una tabla.

```sql
INSERT INTO table1(column1, column2, …)
VALUES 
	(valor1_1, valor2_1, ...), 
	(valor1_2, valor2_2, ...), 
	(valor1_3, valor2_3, ...);
```

### sintaxis:

Después de las palabras clave INSERT INTO 

Especificar el nombre de la tabla (table1) en la que deseas insertar datos
y una lista de columnas separadas por comas (colum1, column2, ....).

En segundo lugar, proporcione una lista de valores separados por comas entre paréntesis (valor1, valor2, ...) después de la palabra clave VALUES. 

Las listas de columnas y valores deben estar en el mismo orden.
### Ejemplo

```sql
CREATE TABLE links (
  id SERIAL PRIMARY KEY, 
  url VARCHAR(255) NOT NULL, 
  name VARCHAR(255) NOT NULL, 
  description VARCHAR (255), 
  last_update DATE
);

INSERT INTO links (url, name)
VALUES('https://www.postgresqltutorial.com','PostgreSQL Tutorial');
```

>[!Warning] Atención 
>Para insertar datos de carácter, debe encerrarlos entre comillas simples ('), por ejemplo 'Tutorial PostgreSQL'.

>[!warning] Atención
Omitir las columnas no nulas en la sentencia INSERT, PostgreSQL emitirá un error. Pero si omite la columna nula, PostgreSQL utilizará el valor por defecto de la columna para la inserción.


En este ejemplo, la descripción es una columna anulable porque no tiene una restricción NOT NULL. Por lo tanto, PostgreSQL utiliza NULL para insertar en la columna description.

PostgreSQL genera automáticamente un número secuencial para la columna serial, por lo que no tiene que proporcionar un valor para la columna serial en la sentencia INSERT.

```
 id |                url                 |        name         | description | last_update
----+------------------------------------+---------------------+-------------+-------------
  1 | https://www.postgresqltutorial.com | PostgreSQL Tutorial | null        | null
(1 row)
```

Insertar un valor de fecha
Para insertar una fecha en una columna FECHA, utilice la fecha en el formato 'AAAA-MM-DD'.

```sql
INSERT INTO links (url, name, last_update)
VALUES('https://www.google.com','Google','2013-06-01');
```


También podemos devolver el campo de alguna columna que acabemos de insertar

```sql
INSERT INTO table1(column1, column2, …)
VALUES (value1, value2, …)
RETURNING id;
```

`RETURNING id` para obtener el valor de `id` generado para la fila que acabas de insertar.

Esto es útil si necesitas utilizar ese valor `id` posteriormente en tu aplicación, por ejemplo, para realizar operaciones adicionales o para mostrar información al usuario.

Caso para devolver todos los valores

```sql
`RETURNING *;`
```

## Delete

Permite borrar uno o varios registros de una tabla, la condición es una expresión booleana

```sql
DELETE FROM table_name
WHERE condition;

DELETE FROM pelicula
WHERE id_peli = 3; 
```

Como borrar todos los registros de una tabla

```sql
DELETE FROM pelicula;
```

Con DDL: [[Página de DDL (Data Definition Lenguaje)#Comando TRUNCATE TABLE]]

Como saber los registros afectados que hemos borrado

```sql
DELETE FROM pelicula
RETURNING *;
```

## Update

La sentencia UPDATE de PostgreSQL permite actualizar los datos de una o varias columnas de una o varias filas de una tabla.

### Sintaxis

```sql
UPDATE table_name
SET column1 = value1,
    column2 = value2,
    ...
[WHERE condition];
```

#### Sintaxis

Especificar el nombre de la tabla cuyos datos queremos actualizar después de la palabra clave UPDATE.

Especificar las columnas y sus nuevos valores después de la palabra clave SET. 

Las columnas que no aparecen en la cláusula SET conservan sus valores originales.

Determinar las filas a actualizar en la condición de la cláusula WHERE.
La cláusula WHERE es opcional. Sin ella, la sentencia UPDATE actualizará todas las filas de la tabla.


### Ejemplo 

```sql
CREATE TABLE courses(
  course_id serial PRIMARY KEY, 
  course_name VARCHAR(255) NOT NULL, 
  price DECIMAL(10,2) NOT NULL,
  description VARCHAR(500), 
  published_date date
);


INSERT INTO courses( course_name, price, description, published_date) 
VALUES 
('PostgreSQL for Developers', 299.99, 'A complete PostgreSQL for Developers', '2020-07-13'), 
('PostgreSQL Admininstration', 349.99, 'A PostgreSQL Guide for DBA', NULL), 
('PostgreSQL High Performance', 549.99, NULL, NULL), 
('PostgreSQL Bootcamp', 777.99, 'Learn PostgreSQL via Bootcamp', '2013-07-11'), 
('Mastering PostgreSQL', 999.98, 'Mastering PostgreSQL in 21 Days', '2012-06-30');

SELECT * FROM courses;
```

Update con la condición WHERE

```sql
UPDATE courses
SET published_date = '2020-08-01' 
WHERE course_id = 3;
```

Update con expresiones
La siguiente sentencia utiliza una sentencia UPDATE para incrementar el precio de todos los cursos un 5%:

```sql
UPDATE courses
SET price = price * 1.05;
```

Update y Devolver el valor Actualizado

```sql
UPDATE courses
SET published_date = '2020-08-01' 
WHERE course_id = 3;
RETURNING *;
```


