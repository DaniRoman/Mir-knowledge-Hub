Crea una tabla llamada Empleado con las siguientes características:
 código identificador auto incremental
 
 nombre: de tipo carácter con longitud 15
 apellidos: de tipo carácter con longitud 30
 trabajo: de tipo carácter con longitud 10
 código_cargo: entero numérico de 4 dígitos
 fecha_contrato: fecha
 salario: número con 2 decimales
 comision:número con 2 decimales
 departamento: entero numérico de 2 

codigo empleado sera primary key

```sql
CREATE TABLE empleado( 

	codigo SERIAL , 
	nombre VARCHAR(15), 
	apellidos VARCHAR(30), 
	trabajo VARCHAR(10), 
	codigo_cargo SMALLINT, 
	fecha_contrato DATE, 
	salario INT, 
	comision INT, 
	departament SMALLINT);

	CONSTRAINT pk_empleado PRIMARY KEY codigo
	
```

Modifica la tabla empleado definiendo y habilitando en la columna SALARIO un formato numérico de 5 cifras enteras y 2 decimales, y la restricción NOT NULL.

```sql
ALTER TABLE empleado
ALTER COLUMN salario TYPE NUMERIC(7,2);
ALTER COLUMN salario SET NOT NULL
```

Cambia la clave primaria para que sea el nombre y el apellido

```sql
ALTER TABLE empleado 
DROP CONSTRAINT pk_empleado;

ALTER TABLE empleado 
ADD PRIMARY KEY (nombre, apellidos);
```

Crea una tabla departamento con campos numero_dep (numérico),
nombre_dep (texto de ancho 9) y localizacion (texto de ancho 10) 

```sql
CREATE TABLE departamento (
	numero_dep SMALLINT,
	nombre_dep VARCHAR(9),
	localizacion VARCHAR(10)
);
```

Añade  Que la primary key sea  numero_dep.

```sql
ALTER TABLE departamento 
ADD CONSTRAINT pk_departamento PRIMARY KEY (numero_dep);
```

Crea la tabla empleado2 con los mismos campos que la tabla empleado, de
forma que la columna departamento tenga como clave externa la clave primaria
de la tabla departamento y que elimine automáticamente todas las filas referenciadas en la tabla hija cuando se elimine la tabla padre..

```sql
CREATE TABLE empleado2( 

	codigo SMALLINT PRIMARY KEY, 
	nombre VARCHAR(15), 
	apellidos VARCHAR(30), 
	trabajo VARCHAR(10), 
	codigo_cargo SMALLINT, 
	fecha_contrato DATE, 
	salario INT, 
	comision INT, 
	departamento SMALLINT,

	CONSTRAINT fk_departamento 
		FOREIGN KEY(departamento) 
		REFERENCES departamento(numero_dep)
		ON DELETE CASCADE
);
```


Elimina la PRIMARY KEY Y Añade una nueva a la tabla empleado2 la compuesta por las dos columnas salario y comisión, nombrando esta clave,

```sql
ALTER TABLE empleado2
DROP CONSTRAINT empleado2_pkey;

ALTER TABLE empleado2
ADD CONSTRAINT comision_salario_pkey PRIMARY KEY (salario, comision);
```

En la misma tabla pon un índice simple de nombre index1 sobre la columna código.

```sql
CREATE INDEX index1
ON empleado2 (codigo);
```

Borre el índice de nombre índice1 de la tabla empleado2 utilizando DROP INDEX.

```sql

DROP INDEX index1;
```

Borre la clave primaria de la tabla departamento.

```sql
ALTER TABLE departamento 
DROP CONSTRAINT pk_departamento CASCADE; 
```