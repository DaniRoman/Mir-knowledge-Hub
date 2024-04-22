```sql
--NIVEL 1

--**Seleccionar todos los productos cuyo precio esté entre 10 y 50 dólares,también sean de una categoría específica ordenados por precio de manera descendente**:
SELECT * FROM Products
WHERE Price BETWEEN 10 AND 50
AND CategoryID = tu_categoria
ORDER BY Price DESC;
--que selecciona todos los productos cuyo precio está entre 10 y 50 dólares y que son suministrados por un proveedor específico:
SELECT * FROM Products
WHERE Price BETWEEN 10 AND 50
AND SupplierID = 2;
--- Se seleccionan todos los clientes que sean de Estados Unidos o Canadá, filtra aquellos clientes cuyo código postal empieza con '9' en el caso de Estados Unidos o 'M' en el caso de Canadá. Ordena alfabéticamente por el nombre del cliente en orden ascendente.
SELECT * FROM Customers
WHERE (Country = 'USA' OR Country = 'Canada')
AND (PostalCode LIKE '9%' OR PostalCode LIKE 'M%')
ORDER BY CustomerName ASC;


```