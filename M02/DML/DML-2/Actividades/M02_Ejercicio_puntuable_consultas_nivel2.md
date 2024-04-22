```sql
--NIVEL 2

--- **Obtener el total de productos en la tabla de Productos:**
SELECT COUNT(*) AS TotalProductos FROM Products;

---*Calcular el precio promedio de los productos:**
SELECT AVG(Price) AS PrecioPromedio FROM Products;

---**Obtener el total de productos por categoría, solo incluyendo categorías con más de 5 productos:**
SELECT CategoryID, COUNT(*) AS TotalProductos
FROM Products
GROUP BY CategoryID
HAVING COUNT(*) > 5;

---*calcula el número total de productos para cada categoría  y luego filtra los resultados para incluir solo aquellas categorías que tengan más de 10 productos.
SELECT CategoryID, COUNT(*) AS TotalProductos
FROM Products
GROUP BY CategoryID
HAVING COUNT(*) > 10;


```