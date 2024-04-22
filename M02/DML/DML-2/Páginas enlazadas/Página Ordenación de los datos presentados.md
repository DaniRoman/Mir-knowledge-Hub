`ORDER BY` se utiliza para ordenar el conjunto de resultados en orden ascendente o descendente.

```plsql
SELECT * FROM Products  
ORDER BY Price;
```

>[!tip]
>podemos ordenar los resultados de una consulta SQL utilizando más de una columna en la cláusula `ORDER BY`. Esto te permite ordenar los resultados primero por una columna y luego por otra en caso de empate


```plsql
SELECT _column1_, _column2, ..._  
FROM _table_name_  
ORDER BY _column1, column2, ..._ ASC|DESC;
```

Teniendo en cuenta la consulta siguiente, Esto ordenará los resultados primero por el salario en orden ascendente y luego, en caso de que dos empleados tengan el mismo salario, los ordenará por el nombre en orden descendente

```plsql
SELECT id, nombre, salario 
FROM empleados 
ORDER BY salario ASC, nombre DESC;
```

![[Captura de pantalla 2024-02-21 a las 10.11.45.png]]

