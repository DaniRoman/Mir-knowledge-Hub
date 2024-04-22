### SELECT WHERE + operadores comparación

```plsql
-- 1. Seleccionar todos los clientes de México:
SELECT * FROM tabla WHERE Country = 'Mexico';
--2. Seleccionar todos los clientes de Francia cuyo código postal sea '75012':
SELECT * FROM tabla WHERE Country = 'France' AND PostalCode = '75012';
--3. Seleccionar todos los clientes cuyo nombre de contacto comience con 'A':
SELECT * FROM tabla WHERE ContactName LIKE 'A%';
--4. Seleccionar todos los clientes que no sean de México:
SELECT * FROM tabla WHERE NOT Country = 'Mexico';
--5. Seleccionar todos los clientes que tengan 'Delikatessen' en el nombre de la empresa y estén en Alemania o Suiza:
SELECT * FROM tabla WHERE CustomerName LIKE '%Delikatessen%' AND (Country = 'Germany' OR Country = 'Switzerland');
--6. Seleccionar todos los clientes que estén en países que no sean ni Alemania ni Francia:
SELECT * FROM tabla WHERE Country NOT IN ('Germany', 'France');
--7. Seleccionar todos los clientes cuyo código postal sea numéricamente mayor que 50000:
SELECT * FROM tabla WHERE CAST(PostalCode AS INT) > 50000;
-- 1. clientes cuyo nombre comience con la letra 'A':
SELECT * FROM clientes WHERE CustomerName LIKE 'A%';
--2. clientes con códigos postales entre '05000' y '05999':
SELECT * FROM clientes WHERE PostalCode BETWEEN '05000' AND '05999';
--3. clientes de varios países específicos:
SELECT * FROM clientes WHERE Country IN ('Mexico', 'USA', 'Canada');
--4. clientes cuya dirección contenga la palabra 'rue':
SELECT * FROM clientes WHERE Address LIKE '%rue%';
--5. clientes cuyo ID esté entre 20 y 30:
SELECT * FROM clientes WHERE CustomerID BETWEEN 20 AND 30;
--6. clientes en ciudades específicas:
SELECT * FROM clientes WHERE City IN ('London', 'Paris', 'Berlin');


```

``` plsql
	--Contar el número total de registros en la tabla `Customers`.
SELECT COUNT(*) AS TotalCustomers FROM Customers;
--Calcular la suma total de la cantidad de productos en la tabla `OrderDetails`.
SELECT SUM(Quantity) AS TotalQuantity FROM OrderDetails;
---Calcular el precio promedio de los productos en la tabla `Products`.
SELECT AVG(Price) AS AveragePrice FROM Products;
--Encontrar el precio máximo y mínimo de los productos en la tabla `Products`.
SELECT MAX(Price) AS MaxPrice, MIN(Price) AS MinPrice FROM Products;
--Contar el número de clientes en cada país.
SELECT Country, COUNT(*) AS TotalCustomers 
FROM Customers 
GROUP BY Country;
--Calcular la cantidad total de productos vendidos por cada empleado.
SELECT EmployeeID, SUM(Quantity) AS TotalQuantitySold 
FROM OrderDetails 
GROUP BY EmployeeID;
--Calcular el precio promedio de los productos en cada categoría.
SELECT CategoryID, AVG(Price) AS AveragePrice 
FROM Products 
GROUP BY CategoryID;
--Encontrar el precio máximo y mínimo de los productos en cada categoría.
SELECT CategoryID, MAX(Price) AS MaxPrice, MIN(Price) AS MinPrice 
FROM Products 
GROUP BY CategoryID;
--Contar el número de pedidos realizados por cada cliente en cada país.
SELECT CustomerID, Country, COUNT(*) AS TotalOrders 
FROM Orders 
GROUP BY CustomerID, Country;

 ```

``` plsql
--Obtener el número de pedidos para cada empleado que haya realizado más de 5 pedidos.        
        `SELECT EmployeeID, COUNT(*) AS NumOrders FROM Orders GROUP BY EmployeeID HAVING COUNT(*) > 5;`
        
--Obtener el nombre y el salario promedio de los empleados cuyo salario promedio sea superior a $3000.
        `SELECT LastName, AVG(Salary) AS AvgSalary FROM Employees GROUP BY LastName HAVING AVG(Salary) > 3000;`
        
--Obtener el nombre de los productos cuyo precio promedio sea superior a $50.     
        `SELECT ProductName, AVG(Price) AS AvgPrice FROM Products GROUP BY ProductName HAVING AVG(Price) > 50;`
        
--Obtener el nombre de los clientes que hayan realizado más pedidos que el promedio de pedidos por cliente.
`SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) > (SELECT AVG(NumOrders) FROM (SELECT COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID) AS AvgOrders);`
        
--Obtener el nombre de los productos cuya cantidad total pedida sea mayor que 100 y cuyo precio promedio sea superior a $20.
        `SELECT ProductID, SUM(Quantity) AS TotalQuantity, AVG(Price) AS AvgPrice FROM OrderDetails GROUP BY ProductID HAVING SUM(Quantity) > 100 AND AVG(Price) > 20;`
        
--Obtener el nombre de los clientes que hayan realizado al menos 3 pedidos.
        `SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) >= 3;`
        
--Obtener el nombre de los empleados y el número de pedidos que han realizado para cada país, mostrando solo aquellos empleados que hayan realizado más de 5 pedidos en un país determinado.
        `SELECT EmployeeID, Country, COUNT(*) AS NumOrders FROM Orders o JOIN Employees e ON o.EmployeeID = e.EmployeeID GROUP BY EmployeeID, Country HAVING COUNT(*) > 5;`
        
--Obtener el nombre de los clientes y el número de pedidos para cada cliente, mostrando solo aquellos clientes que hayan realizado al menos 2 pedidos, ordenados por el número de pedidos en orden descendente.

        `SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) >= 2 ORDER BY COUNT(*) DESC;`
        
--btener el nombre de los empleados y el número total de pedidos que hayan realizado, mostrando solo aquellos empleados cuyo apellido comience con 'D' y que hayan realizado al menos 3 pedidos.

        `SELECT LastName, COUNT(*) AS NumOrders FROM Orders o JOIN Employees e ON o.EmployeeID = e.EmployeeID WHERE LastName LIKE 'D%' GROUP BY LastName HAVING COUNT(*) >= 3;`
        
--Obtener el nombre de los clientes y el número de pedidos para cada cliente, mostrando solo aquellos clientes que hayan realizado más de 2 pedidos y menos de 6 pedidos.

        `SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) > 2 AND COUNT(*) < 6;`

```

##### Ejercicios clase

```plsql 

	--Contar el número total de registros en la tabla `Customers`.
SELECT COUNT(*) AS TotalCustomers FROM Customers;

--Encontrar el precio máximo y mínimo de los productos en la tabla `Products`.
SELECT MAX(Price) AS MaxPrice, MIN(Price) AS MinPrice FROM Products;

--Calcular la cantidad total de productos vendidos por tipo de producto.
SELECT CategoriID, SUM(Quantity) AS TotalQuantitySold 
FROM OrderDetails 
GROUP BY CategoriID;

--Calcular el precio promedio de los productos en cada categoría.
SELECT CategoryID, AVG(Price) AS AveragePrice 
FROM Products 
GROUP BY CategoryID;

---Calcular el precio promedio de los productos en la tabla `Products`.
SELECT AVG(Price) AS AveragePrice FROM Products;
```

```plsql
--Obtener el número de pedidos para cada empleado que haya realizado más de 5 pedidos.        
        `SELECT EmployeeID, COUNT(*) AS NumOrders FROM Orders GROUP BY EmployeeID HAVING COUNT(*) > 5;`

--Obtener el nombre de los productos cuyo precio promedio sea superior a $50.     
        `SELECT ProductName, AVG(Price) AS AvgPrice FROM Products GROUP BY ProductName HAVING AVG(Price) > 50;`

--Obtener el nombre de los clientes que hayan realizado al menos 3 pedidos.
        `SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) >= 3;`

--Obtener el nombre de los clientes y el número de pedidos para cada cliente, mostrando solo aquellos clientes que hayan realizado más de 2 pedidos y menos de 6 pedidos.
        `SELECT CustomerID, COUNT(*) AS NumOrders FROM Orders GROUP BY CustomerID HAVING COUNT(*) > 2 AND COUNT(*) < 6;`
```

>[!warning] Recordar
>Recordar agupar para poder mostrar resultado con funciones agregadas


[recurso externo](https://www.google.com/search?q=sql+sum+function+need+to+be+agroup&oq=sql+sum+function+need+to+be+agroup&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIJCAEQIRgKGKAB0gEINzU1NWowajeoAgCwAgA&sourceid=chrome&ie=UTF-8&safe=active&ssui=on)
[[Recurso]]