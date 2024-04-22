### Tablas w3 schools
Customers
CustomerID	CustomerName	ContactName	Address	City	PostalCode	Country
Categoryes
CategoryID	CategoryName	Description
Employees
EmployeeID	LastName	FirstName	BirthDate	Photo	Notes
Order detailes
OrderDetailID	OrderID	ProductID	Quantity
Orders
OrderID	CustomerID	EmployeeID	OrderDate	ShipperID
Products
ProductID	ProductName	SupplierID	CategoryID	Unit	Price
Shippers
ShipperID	ShipperName	Phone
Suppliers
SupplierID	SupplierName	ContactName	Address	City	PostalCode	Country	Phone
### Nivel 1: Básico (0.5 puntos cada una)

1. **SELECT WHERE operadores lógicos y de comparación**: Consultas simples que involucran operadores lógicos como `AND`, `OR` y operadores de comparación como `=`, `<>`, `>`, `<`, `>=`, `<=`.
    
2. **Otros operadores como BETWEEN, LIKE, IN, NOT IN**: Consultas que incluyen el uso de operadores como `BETWEEN`, `LIKE`, `IN`, `NOT IN`.
    
3. **ORDER BY**: Consultas que requieren ordenar los resultados usando la cláusula `ORDER BY`.
4. [[M02_Ejercicio_puntuable_consultas_nivel1]]

### Nivel 2: Intermedio (1 punto cada una)

4. **Funciones de agregación simples**: Consultas que involucran funciones de agregación simples como `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`.
    
5. **Funciones de agregación agrupadas y filtradas con HAVING**: Consultas que usan funciones de agregación en combinación con la cláusula `GROUP BY` y `HAVING`.
6. [[M02_Ejercicio_puntuable_consultas_nivel2]]
    

### Nivel 3: Avanzado (1.5 puntos cada una)

6. **Subconsultas escalares básicas**: Consultas que incluyen subconsultas escalares simples, es decir, subconsultas que devuelven un solo valor.
    
7. **JOIN**: Consultas que utilizan la cláusula `JOIN` para combinar datos de múltiples tablas.
8. [[M02_Ejercicio_puntuable_consultas_nivel3]]