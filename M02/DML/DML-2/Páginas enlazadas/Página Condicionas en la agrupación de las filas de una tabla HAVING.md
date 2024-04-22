
###### Condicionas en la agrupación de las filas de una tabla HAVING

`HAVING` Filtra resultados basados en condiciones de grupos en consultas que incluyen la cláusula `GROUP BY`. 

Aplica condiciones de búsqueda a los grupos resultantes de la cláusula `GROUP BY`.

**Diferencia entre WHERE y HAVING**:
La cláusula `WHERE` filtra las filas antes de que se realice el agrupamiento, mientras que la cláusula `HAVING` filtra los grupos después de que se hayan agrupado. Por lo tanto, las condiciones en `HAVING` se aplican a las filas agrupadas.

```plsql
SELECT column1, column2, ...
FROM table_name
GROUP BY column1
HAVING condition;
```

Podemos utilizar funciones de agregación dentro de la cláusula `HAVING` para filtrar grupos basados en el resultado de esas funciones. 

```plsql
SELECT category, AVG(sales) AS avg_sales 
FROM sales_data 
GROUP BY category 
HAVING AVG(sales) > 1000;
