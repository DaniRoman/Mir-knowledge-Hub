Filtra las filas de una tabla o vista según un criterio específico. mostrando solo las que cumplan ciertas condiciones.

Podemos  utilizar operadores de comparación (como "=", "<", ">", "<=", ">=") para comparar valores en columnas con un valor específico 

```plsql
	SELECT column1, column2 
	FROM table_name 
	WHERE condition;
```

```plsql
	FROM empleados WHERE salario >= 50000;
	SELECT * FROM clientes WHERE nombre = 'EL gran Jasbula';
```
o con otro valor de columna. 

``` plsql
	SELECT * FROM empleados WHERE salario > (SELECT AVG(salario) FROM empleados WHERE departamento = 'Ventas');
```

También se pueden utilizar operadores lógicos (como "AND", "OR", "NOT") para combinar múltiples condiciones. 

```plsql
	SELECT * FROM empleados WHERE salario >= 50000 AND departamento = 'Ventas';
```

>[!tip] Recurso
>[Recurso w3 schools WHERE](https://www.w3schools.com/sql/sql_where.asp)

### Operadores AND y OR y paréntesis en las consultas
[Recurso externo](https://help.goacoustic.com/hc/en-us/articles/360042858374-AND-and-OR-operators-and-parenthesis-in-queries)

Utilice Y si intenta cumplir dos criterios. 
Los paréntesis no son necesarios para sólo dos criterios. Sin embargo, si tiene más de dos criterios, coloque los criterios entre paréntesis.

Utilice O si intenta cumplir uno u otro criterio. Por ejemplo, quiere A O B pero no ambos.

Ejemplo:

Quiere encontrar todos los contactos que están en GA o en el Reino Unido y no pertenecen a la lista de contactos VIP.

WHERE (país = GA

OR país = UK)

AND contact NOT IN la Lista de contactos VIP.

En este caso, si no hay paréntesis, la consulta busca contactos que tengan como país GA o UK independientemente de si están o no en la lista de contactos porque está buscando contactos que cumplan uno u otro criterio. 

Sin embargo, cuando se utilizan paréntesis, éstos definen qué criterios deben realizarse primero, luego el segundo, el tercero y así sucesivamente en función del número de criterios.

Utilice paréntesis para agrupar las sentencias OR en las consultas
Si tiene una sentencia AND y varias sentencias OR relacionadas lógicamente (donde las sentencias OR están en el mismo campo), siempre debe utilizar paréntesis para agrupar las sentencias OR.

Criterios editables:

[SEGMENTO] =  2
OR [SEGMENTO] = 3
OR [SEGMENTO] = 4
AND [NYC] = Y

Debería ser:

([SEGMENTO] = 2
OR [SEGMENTO] = 3
OR [SEGMENTO] = 4)
AND [NYC] = Y

La colocación y el uso de los paréntesis importan en cuanto a los datos que se extraen.

Ejemplo

Se enviaron dos correos electrónicos a los contactos. Uno de ellos contenía una oferta de "compre uno, llévese otro gratis" (BOGO), y el otro, una tarjeta regalo de 25 $ al completar una encuesta. Los destinatarios que cumplían los criterios de la oferta de BOGO recibían el correo electrónico de BOGO y los destinatarios de la oferta de la tarjeta regalo recibían el correo electrónico de la tarjeta regalo.

El siguiente ejemplo es la consulta CORRECTA que se debe utilizar para que cada destinatario reciba su oferta específica. Observe la colocación de los paréntesis:

( [Nº de tienda] está en la lista (10001,10002,10031,10118,10171,10199,10451,10582,10587,10600,
10616,10619,10741,10820,10830,10902,10935,10951,10971,11125,
13360,13408,13408,13493,13511,13517,13599,13698,13712,17674))
El siguiente ejemplo es INCORRECTO. Resultaría en que los emails BOGO serían enviados a aquellos con criterios de tarjeta regalo y viceversa. Observe la ausencia de los paréntesis de apertura y cierre:

[[Nº de tienda] está en la lista (10001,10002,10031,10118,10171,10199,10451,10582,10587,10600,
10616,10619,10741,10820,10830,10902,10935,10951,10971,11125,
13360,13408,13408,13493,13511,13517,13599,13698,13712,17674)
[[Ejemplos SQL]]