### Concepto

### PL/pgSQL

PL/pgSQL (Procedural Language/Structured Query Language)** es una extensión del lenguaje SQL.

Combina las capacidades de SQL con las características de los lenguajes de programación procedimentales.
  
El lenguaje de consulta procedimental es un tipo de lenguaje de programación diseñado para interactuar con bases de datos de manera más controlada y estructurada, permitiendo la ejecución de operaciones complejas que no pueden ser realizadas fácilmente con lenguajes de consulta declarativos como SQL estándar.

>[!Note] Nota interesante
>El termino procedimental se refiere un enfoque en la programación que se centra en la secuencia de acciones o pasos que deben seguirse para lograr una tarea específica. Es un paradigma de programación en el cual un programa se estructura como una serie de procedimientos o subrutinas, cada uno de los cuales realiza una serie de operaciones específicas.

#### Su uso: 

Crear funciones definidas por el usuario, procedimientos almacenados y disparadores.  
Ampliar SQL estándar añadiendo estructuras de control como if-else, case y sentencias de bucle.  
Heredar todas las funciones, operadores y tipos definidos por el usuario.

### Sección 1 Empezando

#### Constantes Dollar-Quoted String 

Para hacer el código más legible, PostgreSQL ofrece una sintaxis mejor llamada constante de cadena entrecomillada o dollar quoting:

Las constantes de cadena entrecomilladas  permiten construir cadenas que contengan comillas simples sin necesidad de escaparlas.


```SQL
SELECT 'I''m a string constant';

SELECT $$I' a string constant$$;

```

sintaxis

```SQL
$tag$<string_constant>$tag$
```

sin tag:

```SQL
SELECT $$I'm a string constant$$ as message;

OUTPUT

        message
-----------------------
 I'm a string constant
```

con tag

```SQL
SELECT $message$I'm a string constant$message$ s;

OUTPUT

           s
-----------------------
 I'm a string constant

```

Mas ejemplos para bloques de código

```SQL
DO 
'declare
   film_count integer;
BEGIN 
   SELECT COUNT(*) into film_count
   from film;

   RAISE notice ''The number of films: %'', film_count;
END;'
;
```

```SQL

DO 
$$
declare
   film_count integer;
begin 
   select count(*) into film_count
   from film;
   raise notice 'The number of films: %', film_count;
end;
$$;
```
#### Estructura de bloques PL/pgSQL

PL/pgSQL es un lenguaje estructurado en bloques. Aquí está la sintaxis de un bloque (Anonimo) en PL/pgSQL:

```SQL
DO
[ <<label>> ]
[ declare
    declarations ]
begin
    statements;
	...
end [ label ];
```

Cada bloque tiene dos secciones:

Declaración ( opcional), donde se declaran todas las variables utilizadas dentro de la sección del cuerpo
Cuerpo (Obligatoria)

`label` (etiqueta) es un identificador opcional que puedes asociar con un bloque de código para referenciarlo de manera más sencilla y clara.

Declarar una variable  

```SQL
--Inicializada
counter int = 0;

counter int := 0;
-- Sin inicializar
max int;
```

Ejemplo 

```SQL
---La sentencia DO no pertenece al bloque. Se utiliza para ejecutar un bloque anónimo
DO
$$
<<first_block>>
DECLARE
  film_count integer := 0;
BEGIN
   -- get the number of films and pass it to the variable
   select count(*) 
   into film_count
   from film;

   -- display a message (% es un marcador de posición que se sustituye por el contenido de la variable film_count.)
   RAISE notice 'The number of films is %', film_count;
END first_block 
$$;
```

#### Subbloques

Dividir un bloque grande en subbloques más pequeños y lógicos es una práctica común en la programación procedimental.

- **Organización**: Los subbloques ayudan a organizar el código en secciones más manejables y lógicas.
- **Modularidad**: Facilita la comprensión y el mantenimiento del código, ya que cada subbloque puede enfocarse en una tarea específica.
- **Encapsulamiento**: Las variables declaradas en un subbloque no afectan a las variables con el mismo nombre en otros subbloques o en el bloque principal, reduciendo el riesgo de errores.

```SQL
DO
$$
DECLARE
    total_sum INTEGER := 0;
    final_result INTEGER := 0;
BEGIN
    -- Bloque principal
    RAISE NOTICE 'Inicio del bloque principal';
    
    -- Subbloque 1: Suma los IDs de dos desarrolladores
    DECLARE
        dev1_id INTEGER := 0;
        dev2_id INTEGER := 0;
    BEGIN
        SELECT id INTO dev1_id FROM developer WHERE name = 'developer1';
        SELECT id INTO dev2_id FROM developer WHERE name = 'developer2';
        total_sum := dev1_id + dev2_id;
        RAISE NOTICE 'Suma de los IDs de developer1 y developer2 es %', total_sum;
    END;
    
    -- Subbloque 2: Resta el ID de un proyecto del resultado anterior
    DECLARE
        project_id INTEGER := 0;
    BEGIN
        SELECT id INTO project_id FROM project WHERE name = 'project-1';
        final_result := total_sum - project_id;
        RAISE NOTICE 'Resta del ID de project-1 al total es %', final_result;
    END;
    -- Final del bloque principal
    RAISE NOTICE 'Resultado final: %', final_result;
END
$$;

```

SCOPE ( Alcance de las variables )

```SQL
DO 
$$ 
DECLARE 
	father_var VARCHAR(20) := 'variable_padre'; 
BEGIN -- Bloque padre 
	RAISE NOTICE 'DESDE FATHER BLOCK MUESTRO LA %', father_var; 
	
	-- Bloque hijo 1 
	DECLARE child_var VARCHAR(20) := 'variable_child'; 
	BEGIN RAISE NOTICE 'DESDE CHILD BLOCK MUESTRO LA %', father_var; RAISE NOTICE 'DESDE CHILD BLOCK MUESTRO LA %', child_var; 
END; 

	-- Bloque hijo 2 
	DECLARE child2_var VARCHAR(20) := 'variable_child2'; 
	BEGIN RAISE NOTICE 'DESDE CHILD2 BLOCK MUESTRO LA %', father_var; RAISE NOTICE 'DESDE CHILD2 BLOCK MUESTRO LA %', child2_var; 
	END; 
	
-- Intento de acceder a variables de los bloques hijos desde el bloque padre (esto fallará) 
	BEGIN RAISE NOTICE 'DESDE FATHER BLOCK MUESTRO LA %', child_var; EXCEPTION WHEN others THEN RAISE NOTICE 'No se puede acceder a child_var desde el bloque padre'; 
	
	END; 
BEGIN RAISE NOTICE 'DESDE FATHER BLOCK MUESTRO LA %', child2_var; EXCEPTION WHEN others THEN RAISE NOTICE 'No se puede acceder a child2_var desde el bloque padre'; 
END; 
END; 
$$;
```
### Sección 2. Variables & Constantes
#### Variables

Son marcadores de posición para almacenar datos dentro de un bloque. Pueden contener valores de varios tipos, valores para cálculos, almacenar resultados de consultas, etc.

Antes de utilizar variables, debe declararlas en la sección de declaración de un bloque.

Las variables están limitadas al bloque en el que se declaran. Esto significa que las variables sólo son accesibles dentro del bloque y de los bloques anidados.

```SQL
$$ 
DECLARE
	movie_title VARCHAR(255);    
	movie_description TEXT; 
BEGIN     
-- Asignar el resultado de la consulta a las variables     
	SELECT title, description     
	INTO movie_title, movie_description     
	FROM film     
	WHERE film_id = 1;          
-- Mostrar el resultado     
	RAISE NOTICE 'Nombre de la Pelicula %, descripcion: %', movie_title, movie_description; 
END; 
$$
```
#### Constantes

Las constantes son identificadores cuyos valores no pueden modificarse durante la ejecución del código.

```SQL 
constant_name constant data_type = expression;
```

#### Row types

- **Definición**: Es un tipo de dato que refleja exactamente la estructura de una fila de una tabla específica.
- **Rigidez**: Siempre contiene todos los campos de la fila de la tabla para la que está definido.
- **Ejemplo de Uso**: Ideal cuando trabajas con datos de una tabla específica y necesitas asegurar que la variable tenga la misma estructura que la tabla.

```SQL
do 
$$
declare
   selected_actor actor%rowtype;
begin
   -- select actor with id 10   
   select * 
   from actor
   into selected_actor
   where actor_id = 10;

   -- show the number of actor
   raise notice 'The actor name is % %',
      selected_actor.first_name,
      selected_actor.last_name;
end; 
$$;
```
#### Record type

- **Definición**: Es un tipo de dato genérico que puede contener una estructura de campos que no está previamente definida.
- **Flexibilidad**: Permite almacenar el resultado de consultas con diferentes estructuras de columnas.
- **Ejemplo de Uso**: Ideal cuando necesitas seleccionar y manejar un subconjunto de columnas específicas o cuando la estructura de los datos no se conoce hasta el momento de la ejecución.
- 
```SQL
DO 
$$ 
DECLARE 
	rec record; 
BEGIN 
	SELECT film_id, title, length 
	INTO rec 
	FROM film 
	WHERE film_id = 200; 
	
	RAISE NOTICE '% % %', rec.film_id, rec.title, rec.length; 
END; 
$$
```



#### Copiando tipos de datos

A continuación se ilustra cómo declarar una variable con el tipo de datos de una columna de tabla:

`variable_name table_name.column_name%type;`


#### Select into

La sentencia select into permite seleccionar datos de la base de datos y asignarlos a una variable.

```SQL
select column1, column2, ...
into variable1, variable2, ...
from table_expression;
```

ejemplo1 

```SQL
do 
$$
declare
   v_first_name varchar;
   v_last_name varchar;
--- v_first_name actor.first_name%type;
---
begin
   -- select the first_name and last_name of the actor id 1
   select first_name, last_name
   into v_first_name, v_last_name
   from actor
   where actor_id = 1;

   -- show the full name
   raise notice '% %', v_first_name, v_last_name;
end; 
$$;
```


#### Variables en bloques y subbloques

Para acceder a una variable del bloque externo, se utiliza la etiqueta del bloque para calificar su nombre, como se muestra en el siguiente ejemplo:

```SQL
do 
$$ 
---Label de bloque
<<outer_block>>
declare
  counter integer := 0;
begin
   counter := counter + 1;
   raise notice 'The current value of the counter is %', counter;

   declare
       counter integer := 0;
   begin
       counter := counter + 10;
       raise notice 'Counter in the subblock is %', counter;
       raise notice 'Counter in the outer block is %', outer_block.counter;
   end;

   raise notice 'Counter in the outer block is %', counter;
   
end outer_block $$;
```




### Sección 3.  Notificación de mensajes y errores

se utiliza la sentencia raise para emitir un mensaje. Sintaxis de la sentencia raise:

```SQL
raise level format;
```

La opción de Level determina la gravedad del error con los siguientes valores:

-  `debug`
-  `log`
-  `notice`
-  `info`
-  `warning`
-  `exception`

*Format*

Cadena que especifica el mensaje.
Utiliza marcadores de porcentaje ( %) que serán sustituidos por los argumentos.

```SQL
do $$ 
begin 
  raise info 'information message %', now() ;
  raise log 'log message %', now();
  raise debug 'debug message %', now();
  raise warning 'warning message %', now();
  raise notice 'notice message %', now();
end $$;
```

### Sección 4. Estructuras de control

#### If 

##### `if then`

```SQL
---SINTAXIS---
if condition then
   statements;
end if;

---EJEMPLO_____
do $$
declare
  selected_film film%rowtype;
  input_film_id film.film_id%type = 0;
begin  

  select * from film
  into selected_film
  where film_id = input_film_id;
  
  if not found then
     raise notice'The film % could not be found', input_film_id;
  end if;
end $$;
```


##### `if then else`

```SQL
---SINTAXIS---
if condition then
  statements;
else
  alternative-statements;
end if;

---EJEMPLO_____
do $$
declare
  selected_film film%rowtype;
  input_film_id film.film_id%type := 100;
begin  

  select * from film
  into selected_film
  where film_id = input_film_id;
  
  if not found then
     raise notice 'The film % could not be found', 
	    input_film_id;
  else
     raise notice 'The film title is %', selected_film.title;
  end if;
end $$;
```

##### `if then elsif`

```SQL
---SINTAXIS---
if condition_1 then
  statement_1;
elsif condition_2 then
  statement_2
...
elsif condition_n then
  statement_n;
else
  else-statement;
end if;

---EJEMPLO_____
 do $$
declare
   v_film film%rowtype;
   len_description varchar(100);
begin  

  select * from film
  into v_film
  where film_id = 100;
  
  if not found then
     raise notice 'Film not found';
  else
      if v_film.length >0 and v_film.length <= 50 then
		 len_description := 'Short';
	  elsif v_film.length > 50 and v_film.length < 120 then
		 len_description := 'Medium';
	  elsif v_film.length > 120 then
		 len_description := 'Long';
	  else 
		 len_description := 'N/A';
	  end if;
    
	  raise notice 'The % film is %.',
	     v_film.title,  
	     len_description;
  end if;
end $$;
```

#### Loop 

Bucle incondicional que ejecuta un bloque de código repetidamente hasta que finaliza mediante una sentencia exit o return.

Normalmente, se utiliza una sentencia if para terminar el bucle basándose en una condición como ésta:

```SQL
<<label>>
loop
   statements;
   if condition then
      exit;
   end if;
end loop;

do $$

declare
    counter int := 0;
begin
  
  loop
  	counter = counter + 1;
	raise notice '%', counter;
	
	if counter = 5 then
		exit;
	end if;
	
  end loop;

end;

$$;
```

En la práctica, puede combinar las sentencias if y exit en una única sentencia como ésta:

```SQL
do $$

declare
    counter int := 0;
begin
  
  loop
  	counter = counter + 1;
	raise notice '%', counter;
	
	exit when counter = 5;
  end loop;

end;

$$;
```

### Sección 5. funciones

```SQL
create [or replace] function function_name(param_list)
   returns return_type 
   language plpgsql
  as
$$
declare 
   -- variable declaration
begin
   -- logic
end;
$$;
```

#### En esta sintaxis:
- En primer lugar, El nombre de la función después de las palabras clave crear función. Para reemplazar la función existente, usar la opción o reemplazar.
- A continuación, enumerar los parámetros rodeados de paréntesis después del nombre de la función. 
- A continuación, defino el tipo de datos del valor devuelto tras la palabra clave returns.
- A continuación, utilizo el lenguaje plpgsql para definir el lenguaje de procedimiento de la función.
- Por último, coloco un bloque en la constante de cadena entre comillas para definir el cuerpo de la función.

```SQL
create or replace function get_film_count(len_from int, len_to int)

returns int
language plpgsql

as

$$
declare
   film_count integer;
begin
   select count(*) 
   into film_count
   from film
   where length between len_from and len_to;
   
   return film_count;
end;
$$;

---Asi la llamaria
SELECT get_film_count(40,90);
```

##### Returns SetOf

Definir una función que devuelva una o varias filas.

Esta es la sintaxis para crear una función que devuelva un conjunto de filas:

```SQL
create or replace function function_name(parameters)
returns setof row_structure (por ejemplo RECORDS)
as
$$
   -- logic
   -- ...
   -- devuelva filas dentro del cuerpo de la función utilizando la sentencia return query seguida de una sentencia select.
   return query select_query;
$$ language plpgsql;


create or replace function find_film_by_id(
	p_id int
)
returns setof film
as
$$
begin
   return query select * from film
   where film_id = p_id;
end;
$$
language plpgsql;
```

### Sección 6. Cursores

Un cursor es un objeto de base de datos que permite recorrer una a una las filas del resultado de una consulta.

Útiles cuando se trabaja con grandes conjuntos de resultado.

- Declare un cursor

```SQL
DECLARE 
	cursor_name CURSOR FOR 
	
	query;
```

- Abrira el cursor.

`OPEN cursor_name;`

- Buscar las filas del conjunto de resultados en un registro o en una lista de variables.

	- `FETCH NEXT`: fetches the next row from the cursor.
	- `FETCH PRIOR`: fetches the previous row from the cursor.
	- `FETCH FIRST`: fetches the first row from the cursor.
	- `FETCH LAST`: fetches the last row from the cursor.
	- `FETCH ALL`: fetches all rows from the cursor.

- Procesar la fila obtenida y salir del bucle si no hay más filas que obtener.

Después de obtener una fila, puedes procesarla. Utilizando una sentencia LOOP para procesar las filas obtenidas del cursor:

```SQL
LOOP    
    -- Fetch the next row
    FETCH NEXT FROM cursor_name INTO variable_list;

    -- exit if not found
    EXIT WHEN NOT FOUND;

    -- Process the fetched row
    ...

END LOOP;
```

- Cerrar el cursor.

`CLOSE cursor_name;`

Ejemplo

```SQL
CREATE OR REPLACE FUNCTION fetch_film_titles_and_years(

	OUT p_title VARCHAR(255), 
	OUT p_release_year INTEGER)

RETURNS SETOF RECORD AS

$$

DECLARE

    film_cursor CURSOR FOR
    
        SELECT title, release_year
        FROM film;
        
    film_record RECORD;
BEGIN
    -- Open cursor
    OPEN film_cursor;

    -- Fetch rows and return
    LOOP
        FETCH NEXT FROM film_cursor 
        INTO film_record;
        EXIT WHEN NOT FOUND;

        p_title = film_record.title;
        p_release_year = film_record.release_year;
        
        RETURN NEXT;
    END LOOP;

    -- Close cursor
    CLOSE film_cursor;
END;
$$
LANGUAGE PLPGSQL;

SELECT * FROM fetch_film_titles_and_years();
```

### Sección 7. Triggers

Función especial definida por el usuario asociada a una tabla que se invoca automáticamente cada vez que ocurre un evento asociado a una tabla. INSERTAR, ACTUALIZAR, ELIMINAR o TRUNCAR.

Defino primero una función Trigger , a continuación, vinculo esta función Trigger a una tabla.

Tipos de disparadores PostgreSQL
PostgreSQL proporciona dos tipos principales de disparadores:

Disparadores a nivel de fila
Disparadores a nivel de sentencia.
Las diferencias entre los dos tipos son cuántas veces se invoca el disparador y en qué momento.

Por ejemplo, si emite una sentencia UPDATE que modifica 20 filas, el disparador de nivel de fila se invocará 20 veces, mientras que el disparador de nivel de sentencia se invocará 1 vez.

Puede especificar si el disparador se invoca antes o después de un evento. 