# ironhack-data-querys
Implementing and Optimizing Database Queries
 # 1. SQL Query Optimization

 ## Orders Query: Retrieve orders with many items and calculate the total price

 ``` sql
SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
    FROM Orders
JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;


 ```

## Analisis

- Se detecta que se hace un barrido completo sobre la tabla Order
- La columna OrderID deberia ser primary key, por lo que en automatico estaria indexada
- Asegurar que orderId este indexada en la tabla OrderDetail
- Asegurar que la columna quantity este indexada

## Mejoras
- Cambiar la tabla pivote a OrderDetail, para evitar un barrido completo a Orders

``` sql

SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
from OrderDetail
    join Orders ON OrderDetails.OrderID = orders.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;
```



 ## Customer Query: Find all customers from London and sort by CustomerName.


``` sql
SELECT CustomerName 
FROM Customers 
WHERE City = 'London' 
ORDER BY CustomerName;

```
## Analisis
- El query parece estar bien, solo asegurarnos que la columna city este indexada.



# 2. NoSQL Query Implementation

## - User Posts Query: Retrieve the most popular active posts and display their title and like count.

db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 });

## Analisis
- Asegurarse que este indexado el status
- Los campos title y likes son campos en proyeccion por lo que no estamos trayendo todo el contenido
y estamos reduciendo la cantidad de informacion
- Si los resultados son muchos se puede implementar un paginado de la consulta para reducir la latencia



## - User Data Aggregation: Summarize the number of active users by location.

db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
]);

## Analisis
 - Indexar el campo status
 - Analizar la funcionalidad para determinar si los campo status y id son usados con frecuencia en diferentes consultas
 podemos construir un indice compuesto