`GROUP BY` agrupa las filas que tienen los mismos valores en filas resumen

>[!tip]
>[Recurso `GROUP BY` en ws schools](https://www.w3schools.com/sql/sql_groupby.asp)

`GROUP BY` suele utilizarse con funciones de agregación (`COUNT()`, `MAX()`, `MIN()`, `SUM()`, `AVG()`) para agrupar el conjunto de resultados por una o varias columnas.

Teniendo en cuenta la siguiente tabla

| producto | categoria | cantidad |
| ---- | ---- | ---- |
| ProductoA | Categoria1 | 10 |
| ProductoB | Categoria2 | 15 |
| ProductoA | Categoria1 | 20 |
| ProductoC | Categoria1 | 12 |
| ProductoB | Categoria2 | 18 |
|  |  |  |

**Calcular el total de ventas por categoría de producto**
```plsql
SELECT categoria, SUM(cantidad) AS total_ventas
FROM ventas
GROUP BY categoria;
```

| categoria | total_ventas |
| ---- | ---- |
| Categoria1 | 42 |
| Categoria2 | 33 |
|  |  |
|  |  |

**Calcular el promedio de ventas por producto:**
```plsql
	SELECT producto, AVG(cantidad) AS promedio_ventas 
	FROM ventas 
	GROUP BY producto;
```

| producto | promedio_ventas |
| ---- | ---- |
| ProductoA | 15.0 |
| ProductoB | 16.5 |
| ProductoC | 12.0 |
|  |  |

**Contar el número de ventas por producto en cada categoría**
```plsql
SELECT categoria, producto, COUNT(*) AS cantidad_ventas
FROM ventas
GROUP BY categoria, producto;
```

| categoria  | producto   | cantidad_ventas |
|------------|------------|-----------------|
| Categoria1 | ProductoA  | 2               |
| Categoria1 | ProductoC  | 1               |
| Categoria2 | ProductoB  | 2               |

**Encontrar el producto más vendido en cada categoría**
```plsql
SELECT categoria, producto, MAX(cantidad) AS max_ventas
FROM ventas
GROUP BY categoria;
```

| categoria  | producto   | max_ventas |
|------------|------------|------------|
| Categoria1 | ProductoA  | 20         |
| Categoria2 | ProductoB  | 18         |
### Condición en la agrupación de las filas de una tabla con HAVING
[[Página Condicionas en la agrupación de las filas de una tabla con HAVING]]