## Cambios Actividad anterior

Elimina la tabla empleado2



Borra la columna num_dep y añadela de nuevo pero que el TYPE sea SERIAL 

>[!Error] Error!
>Si intentas cambiar un tipo de dato a SERIAL o al revés no sera posible ya que este es un pseudo tipo, y se debe de eliminar la columna y crear una de nuevo.



Elimina la columna trabajo



Añade mas caracteres a las columnas (nombre_dep (40) trabajo(40), localizacion (100))



Añade la PRIMARY KEY numero_dep a la tabla departamento


Modifica la tabla empleado y añade la FOREIGN KEY que referencia a la tabla departamento 



Modifica la tabla departamento y añade que la columna localización tenga un valor por default que sea 'Dagestan'


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
);

CREATE TABLE departamento (
	numero_dep SERIAL,
	nombre_dep VARCHAR(9),
	localizacion VARCHAR(10) DEFAULT 'dagestan',

	CONSTRAINT pk_departamento PRIMARY KEY (numero_dep)
);

```

## Actividad

Inserta los datos para todas las columnas de la tabla departamento, ( los datos de tu elección ) y devuelve los valores de la PRIMARY KEY.



Inserta otros datos pero ahora que utilicen los valores por DEFAULT y retorna ese valor.



Inserta en la tabla empleado un nuevo registro con todos los campos ( a tu elección, menos en departamento que tiene que ser el numero 5), Algún error?  por que?solucionado.



Inserta en la tabla empleado un nuevo registro con todos los campos ( menos en salario ), Algún error?  por que?solucionado.



Elimina el empleado con el código numero 1



Borrar todos los registros de la tabla alumno



Borra  de la tabla departamento el registro con el numero que asignaste al empleado anterior, Da algún error? solucionalo sin eliminar a mano el registro del empleado.



Haz un Update del salario del empleado con el codigo 1 a 100000 Da algún error? porque?



Actualiza la comisión del empleado 1 de su salario * 0.5 y devuelve todos los campos

