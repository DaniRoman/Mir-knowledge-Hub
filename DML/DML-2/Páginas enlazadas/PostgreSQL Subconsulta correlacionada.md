En PostgreSQL, una subconsulta correlacionada es una subconsulta que hace referencia a las columnas de la consulta externa.

A diferencia de una subconsulta normal, PostgreSQL evalúa la subconsulta correlacionada una vez por cada fila procesada por la consulta externa.

Dado que PostgreSQL reevalúa la subconsulta correlacionada para cada fila de la consulta externa, esto puede dar lugar a problemas de rendimiento, especialmente cuando se trata de grandes conjuntos de datos.

Una subconsulta correlacionada puede ser útil cuando se necesita realizar una consulta que depende de los valores de la corriente que se está procesando.

Ejemplo de subconsulta correlacionada PostgreSQL
Utilizaremos la tabla de películas de la base de datos de ejemplo para la demostración:

[Subconsulta correlacionada](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-correlated-subquery/)
