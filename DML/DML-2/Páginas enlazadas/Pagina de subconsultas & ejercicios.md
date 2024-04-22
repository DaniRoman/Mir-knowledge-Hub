
>[!tip] Recurso
[Postgresql subqueries](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-subquery/)

Que son?

 Una subconsulta es una consulta anidada dentro de otra consulta. Una subconsulta también se conoce como consulta interna o consulta anidada.

Una subconsulta puede ser útil para recuperar datos que serán utilizados por la consulta principal como condición para una posterior selección de datos.

Sintaxis Básica
En esta sintaxis, la subconsulta se encierra entre paréntesis y se ejecuta en primer lugar:

```plsql
SELECT 
  select_list 
FROM 
  table1 
WHERE 
  columnA operator (
    SELECT 
      columnB 
    from 
      table2 
    WHERE 
      condition
  );
```


En lugar de ejecutar dos consultas, puede combinarlas en una sola, haciendo que la primera consulta sea una subconsulta y la segunda la consulta principal, como se indica a continuación:

```sql
SELECT 
  city 
FROM 
  city 
WHERE 
  country_id = (
    SELECT 
      country_id 
    FROM 
      country 
    WHERE 
      country = 'United States'
  ) 
ORDER BY 
  city;
```
##### Ejemplos con subconsultas 

```plsql
--- **Filtrar Pedidos de una ciudad Específica:**
SELECT OrderID FROM Orders WHERE (SELECT City FROM Customers WHERE CustomerID = Orders.CustomerID) = 'Mexico D.F.';
--- **Filtrar Clientes con una Cantidad Específica de Pedidos:**
SELECT CustomerID
FROM Customers
WHERE (SELECT COUNT(OrderID) FROM Orders WHERE CustomerID = Customers.CustomerID) > 20;
--filtrar los registros de la tabla Orders donde el CustomerID coincide con el CustomerID de la tabla Customers. Esto es esencial para relacionar los datos de ambas tablas y obtener resultados significativos en la consulta.

---Encontrar Pedidos de un Empleado Específico:**
SELECT OrderID
FROM Orders
WHERE (SELECT FirstName + ' ' + LastName FROM Employees WHERE EmployeeID = Orders.EmployeeID) = 'Nancy Davolio';

---**Filtrar Pedidos con una Cantidad Total de Productos Mayor a un Valor Específico:**
SELECT OrderID
FROM Orders
WHERE (SELECT SUM(Quantity) FROM OrderDetails WHERE OrderID = Orders.OrderID) > 20;
```

[[PostgreSQL Subconsulta correlacionada]]
[Subqueries con IN](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-subquery/)