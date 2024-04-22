 
 
1. Crea una tabla llamada TRABAJO con los campos nombre, edad y cargo, siendo
	los campos nombre y cargo con una longitud máxima de 20 y 50 respectivamente.
	La clave primaria será nombre.

```sql
CREATE TABLE trabajo ( 
	nombre VARCHAR (20) PRIMARY KEY, 
	edad SMALLINT, 
	cargo VARCHAR (20);
```

2. Crea una tabla llamada CIUDAD con los campos nombre, comarca, provincia y
	habitantes. Los campos textuales tendrán una longitud máxima de 25 caracteres.
	Ninguno podrá ser nulo excepto el número de habitantes y la clave primaria será el nombre
	de la ciudad.
	
```sql
CREATE TABLE ciudad ( 
	nombre VARCHAR (25) PRIMARY KEY, 
	comarca VARCHAR (25) NOT NULL, 
	provincia VARCHAR (25) NOT NULL,
	habitantes SMALLINT;
```

3. Crea una tabla llamada CLIENTE con los campos idclient, nombre, apellidos,
	fecha de nacimiento y tipos. El campo idclient será autonúmerico y la clave primaria
	 , los nombres apellidos serán únicos y longitud máxima de 25 caracteres, y el tipo tomará como valor por defecto ‘Habitual’.
	 
	 Haz otra versión de la tabla que cree la CONSTRAINT nombre y apellidos a nivel de tabla dandole un nombre a esta

```sql
CREATE TABLE cliente ( 
	idclient SERIAL PRIMARY KEY, 
	nombre VARCHAR(25) NOT NULL, 
	apellidos VARCHAR(25) NOT NULL, 
	fecha_nacimiento DATE NOT NULL, 
	tipo VARCHAR(25) DEFAULT 'Habitual', 


CREATE TABLE cliente ( 
	idclient SERIAL PRIMARY KEY, 
	nombre VARCHAR(25) NOT NULL, 
	apellidos VARCHAR(25) NOT NULL, 
	fecha_nacimiento DATE NOT NULL, 
	tipo VARCHAR(25) DEFAULT 'Habitual', 
	
	CONSTRAINT unq_nombre_apellidos UNIQUE (nombre, apellidos) );
```

4. Crear una base de datos para guardar los productos informáticos de una
	tienda y ciertos datos de sus fabricantes. 
	
	Crearemos una tabla producto:
	- código numérico único
	- nombre producto
	- descripción
	- categoría
	- precio
	
	Crearemos una tabla fabricante:
	- código numérico único
	- nombre no nulo
	- direccion_web
	- telefono
	
	Ambas tablas deben estar vinculadas.
	Los campos en forma de texto tendrán una longitud máxima de 20 caracteres
	(excepto que alguno vea que puede reducir el tamaño).

```sql
CREATE TABLE producto (
    codigo INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    codigo_fabricante INT,
    nombre VARCHAR(20) NOT NULL,
    descripcion TEXT,
    categoria VARCHAR(20),
    precio NUMERIC(10, 2),
    
    CONSTRAINT fabricante_pk 
	    FOREIGN KEY (codigo_fabricante) 
		    REFERENCES fabricante(codigo)
);

CREATE TABLE fabricante (
    codigo INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre VARCHAR(20) NOT NULL,
    direccion_web VARCHAR(20),
    telefono VARCHAR(9)
);

```

Un instituto quiere almacenar en una base de datos las asignaturas que se
cursan, los profesores que las imparten y los alumnos que realizan cada
asignatura.

tabla asignaturas:
. código de la asignatura (3 letras)
. nombre
. El ciclo a la que pertenece
. El curso al que pertenece.

Tabla profesor: 
. código numérico de profesor
. nombre
. apellidos
. fecha de nacimiento
. dirección
. teléfono.

Tabla alumno
. código numérico único
. nombre 
. apellidos
. fecha de nacimiento
. dirección
. teléfono
. cuando hizo su primera matrícula.

Los campos en forma de texto tendrán una longitud máxima de 20 caracteres, pueden haber mas tablas...

```sql
CREATE TABLE assignatura (
	id_assignatura VARCHAR(3) PRIMARY KEY, 
	nombre VARCHAR(20) NOT NULL, 
	ciclo VARCHAR(20) NOT NULL, 
	curso SMALLINT NOT NULL;
) 

CREATE TABLE profesor (
	id_profesor SERIAL PRIMARY KEY, 
	nombre VARCHAR(20) NOT NULL, 
	apellidos VARCHAR(20) NOT NULL, 
	fecha_nacimiento DATE, 
	direccion VARCHAR(20), 
	telefono VARCHAR(9) ); 
	
CREATE TABLE alumno (
	id_alumno SERIAL PRIMARY KEY, 
	nombre VARCHAR(20) NOT NULL, 
	apellidos VARCHAR(20) NOT NULL, 
	fecha_nacimiento DATE, 
	direccion VARCHAR(20), 
	telefono VARCHAR(9), 
	fecha_primera_matricula DATE NOT NULL) ; 

CREATE TABLE imparte (
	id_profesor INT, 
	id_assignatura VARCHAR(3), 
	
	PRIMARY KEY(id_professor,id_assignatura), 
	
	CONSTRAINT fk_professor
		FOREIGN KEY (id_professor) 
			REFERENCES professor (id_professor)
			ON DELETE CASCADE 
			
	CONSTRAINT fk_assignatura
		FOREIGN KEY (id_assignatura) 
			REFERENCES assignatura (id_assignatura) 
			ON DELETE CASCADE
	); 

CREATE TABLE matricula (
	id_alumno INT, 
	id_assignatura VARCHAR(3), 
	
	PRIMARY KEY(codigo_alumno,codigo_assignatura), 

	CONSTRAINT fk_alumno
		FOREIGN KEY (id_alumno) 
			REFERENCES alumno (id_alumno), 
			ON DELETE CASCADE 
	CONSTRAINT fk_assignatura
		FOREIGN KEY (id_assignatura) 
			REFERENCES assignatura (id_assignatura)
			ON DELETE CASCADE 
	);
```