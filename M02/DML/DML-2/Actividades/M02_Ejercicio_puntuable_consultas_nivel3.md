```sql
---**Filtrar Pedidos con una Cantidad Total de Productos Mayor a un Valor Específico:**
SELECT OrderID
FROM Orders
WHERE (SELECT SUM(Quantity) FROM OrderDetails WHERE OrderID = Orders.OrderID) > 20;

--selecciona los nombres de clientes y de empleados para cada pedido:
SELECT Orders.OrderID, Customers.CustomerName, Employees.LastName, Employees.FirstName
FROM (Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
INNER JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID;

--selecciona todos los clientes y los pedidos que puedan tener, ordenados por el nombre del cliente:
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID
ORDER BY Customers.CustomerName;
```