
1. Crea una tabla llamada TRABAJO con los campos nombre, edad y cargo, siendo
los campos nombre y cargo con una longitud máxima de 20 y 50 respectivamente.
La clave primaria será nombre.

2. Crea una tabla llamada CIUDAD con los campos nombre, comarca, provincia y
habitantes. Los campos textuales tendrán una longitud máxima de 25 caracteres.
Ninguno podrá ser nulo excepto el número de habitantes y la clave primaria será el nombre
de la ciudad.

3. Crea una tabla llamada CLIENTE con los campos idclient, nombre, apellidos,
fecha de nacimiento y tipos. El campo idclient será autonúmerico y la clave primaria, los nombres apellidos serán únicos y longitud máxima de 25 caracteres, y el tipo tomará como valor por defecto ‘Habitual’.
 
 Haz otra versión de la tabla que cree la CONSTRAINT nombre y apellidos a nivel de tabla dandole un nombre a esta
 
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

5. Un instituto quiere almacenar en una base de datos las asignaturas que se
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