
0. Crea una tabla llamada Empleado con las siguientes características:
 
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


1. Crea una tabla departamento con campos numero_dep (numérico),
nombre_dep (texto de ancho 9) y localizacion (texto de ancho 10) 

2. Añade  Que la primary key sea  numero_dep.

3. Crea la tabla empleado2 con los mismos campos que la tabla empleado, de
forma que la columna departamento tenga como clave externa la clave primaria
de la mesa DEPARTAMENTO y que elimine automáticamente todas las filas referenciadas en la tabla hija cuando se eliminan las filas referenciadas en la tabla padre..

4. Añadir a la tabla empleado2 la clave única compuesta por las dos columnas
salario y comisión, nombrando esta clave 

5. En la misma tabla pon un índice simple de nombre index1 sobre la columna código
mediante el orden ADD.

6. Borre el índice de nombre índice1 de la tabla EMPLEAT2 utilizando DROP INDEX.

7. Borre la clave primaria de la tabla departamento.

