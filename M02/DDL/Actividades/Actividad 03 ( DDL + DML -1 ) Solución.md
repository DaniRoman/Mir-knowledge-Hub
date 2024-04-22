## Cambios Actividad anterior

Elimina la tabla empleado2

```sql
DROP TABLE empleado2;
```

Borra la columna num_dep y añadela de nuevo pero que el TYPE sea SERIAL 

>[!Error] Error!
>Si intentas cambiar un tipo de dato a SERIAL o al revés no sera posible ya que este es un pseudo tipo, y se debe de eliminar la columna y crear una de nuevo.

```sql
ALTER TABLE departamento 
DROP COLUMN numero_dep

ALTER TABLE departamento 
ADD COLUMN numero_dep SERIAL;
```

Elimina la columna trabajo

```sql
ALTER TABLE empleado
DROP COLUMN trabajo;
```

Añade mas caracteres a las columnas (nombre_dep (40) trabajo(40), localizacion (100))

```sql
ALTER TABLE empleado
ALTER COLUMN trabajo TYPE varchar(40),

ALTER TABLE departamento
ALTER COLUMN nombre_dep TYPE varchar(40),
ALTER COLUMN localizacion TYPE varchar(100);
```

Añade la PRIMARY KEY numero_dep a la tabla departamento

```sql
ALTER TABLE departamento 
ADD CONSTRAINT pk_departamento PRIMARY KEY (numero_dep)
```

Modifica la tabla empleado y añade la FOREIGN KEY que referencia a la tabla departamento con ON DELETE CASCADE.

```sql
ALTER TABLE empleado 
ADD CONSTRAINT fk_departamento 
	FOREIGN KEY (departamento)
	REFERENCES departamento(numero_dep)
	ON DELETE CASCADE;
```

Modifica la tabla departamento y añade que la columna localización tenga un valor por default que sea 'Dagestan'

```sql
ALTER TABLE departamento 
ALTER COLUMN localizacion
SET DEFAULT 'dagestan';
```
## Tablas actividad anterior

```sql
CREATE TABLE empleado( 
	codigo SERIAL, 
	nombre VARCHAR(15), 
	apellidos VARCHAR(30), 
	codigo_cargo SMALLINT, 
	fecha_contrato DATE, 
	salario NUMERIC(7,2) NOT NULL, 
	comision INT, 
	departament SMALLINT),

	CONSTRAINT pk_empleado PRIMARY KEY ( nombre, apellido)
	CONSTRAINT fk_departamento 
		FOREIGN KEY(departamento) 
		REFERENCES departamento(numero_dep)
		ON DELETE CASCADE
);

CREATE TABLE departamento (
	numero_dep SERIAL,
	nombre_dep VARCHAR(9),
	localizacion VARCHAR(10) DEFAULT 'dagestan',

	CONSTRAINT pk_departamento PRIMARY KEY (numero_dep)
);

```

Inserta los datos para todas las columnas de la tabla departamento, ( los datos de tu elección ) y devuelve los valores de la PRIMARY KEY.

```sql
INSERT INTO departamento (nombre_dep, localizacion)
VALUES 
	('Software Development', 'Azerbaiyan'),
	('Software architect', 'Georgia')
RETURNING numero_dep;

```

Inserta otros datos pero ahora que utilicen los valores por DEFAULT y retorna ese valor.

```sql
INSERT INTO departamento (nombre_dep)
VALUES 
	('DevOps')
RETURNING localizacion;
```

Inserta en la tabla empleado un nuevo registro con todos los campos ( a tu elección, menos en departamento que tiene que ser el numero 5), Algún error?  por que?solucionado.

```sql
INSERT INTO empleado (
	nombre, apellidos, trabajo
	codigo_cargo, fecha_contrato, 
	salario, comision, departamento)
VALUES ('Hasbula', 'Roman', 'Desarrollador', 1001, '2023-01-15', 2500.00, 300.00, 5);
```

Inserta en la tabla empleado un nuevo registro con todos los campos ( menos en salario ), Algún error?  por que?solucionado.

```sql
INSERT INTO empleado (
	nombre, apellidos, trabajo, 
	codigo_cargo, fecha_contrato, comision, departamento)
VALUES ('Hasbula2', 'Roman2', 
		'Desarrollador2', 
		1001, '2023-01-15', 300.00, 1);
```

Elimina el empleado con el código numero 1

```sql
DELETE FROM empleado 
WHERE codigo = 1;
```

Borrar todos los registros de la tabla alumno

```sql
DELETE FROM empleado;
```

Borra  de la tabla departamento el registro con el numero que asignaste al empleado anterior, Da algún error? solucionalo sin eliminar a mano el registro del empleado.

```sql
DELETE FROM departamento WHERE numero_dep = 1;

-- Eliminar la restricción existente
ALTER TABLE empleado 
DROP CONSTRAINT fk_departamento;

-- Agregar la nueva restricción con ON DELETE CASCADE
ALTER TABLE empleado 
ADD CONSTRAINT fk_departamento 
    FOREIGN KEY (departamento)
    REFERENCES departamento(numero_dep)
    ON DELETE CASCADE;

DELETE FROM departamento WHERE numero_dep = 1;
```

Haz un Update del salario del empleado con el codigo 1 a 100000 Da algún error? porque?

```sql
UPDATE empleado
SET salario = '100000' 
WHERE codigo = 1;

UPDATE empleado
SET salario = '10000' 
WHERE codigo = 1;
```

Actualiza la comisión del empleado 1 de su salario * 0.5 y devuelve todos los campos

```sql
UPDATE empleado
SET comision = salario * 1.05;
RETURNING*
```