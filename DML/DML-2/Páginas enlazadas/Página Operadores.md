
**LIKE**:

- Búsquedas por patrones dentro de una columna de texto.
- Hay dos comodines  ( %, _) que se utilizan a menudo junto con el operador LIKE:
- % representa cero, uno o varios caracteres
 - _ representa un único carácter.

>[!example]
> Para ver el patron de los comodines, https://www.w3schools.com/sql/sql_like.asp

```plsql
	SELECT * FROM Customers  
WHERE CustomerName LIKE 'a%';
```

**BETWEEN**:

- Filtrar valores dentro de un rango específico.
- Seleccionar filas donde el valor de una columna esté entre dos valores dados, inclusive.
- Por ejemplo, `BETWEEN 10 AND 20` seleccionará filas donde el valor de la columna esté entre 10 y 20, incluyendo los extremos.

>[!example]
>Ejemplos y mas casos de uso con  [`Between`](https://www.w3schools.com/sql/sql_between.asp)

**IN**:

- Comparar un valor con una lista de valores posibles.
- Seleccionar filas donde el valor de una columna coincida con cualquiera de los valores especificados en la lista ...`IN ('valor1', 'valor2', 'valor3')` seleccionará filas donde el valor de la columna coincida con 'valor1', 'valor2' o 'valor3'.

>[!example]
>Ejemplos y mas casos de uso con  [`IN`](https://www.w3schools.com/sql/sql_in.asp)
