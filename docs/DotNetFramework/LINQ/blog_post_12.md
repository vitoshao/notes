---
title: LINQ 表示式(4) - Exist、In、Any、All、Contains
layout: default
parent: LINQ
nav_order: 12
description: "LINQ 表示式(4) - Exist、In、Any、All、Contains"
date: 2013-04-11
tags: [DotNetFramework,LINQ]
postid: "3781759138957108070"
---
關鍵字： Any、All、StartsWith、IndexOf、Contains

# Any、All

## Any

Any 與 All 都會使用到 T-SQL 中的 Exist ，用來判斷集合中是否有任何一個元素滿足指定的條件。

Any 有二種用法，帶條件式與不帶條件式。

### 1.簡單形式

查詢曾購買的客戶
```sql
from c in Customers
where c.Orders.Any()
select new {c.CustomerID, c.ContactName}
```
```c#
Customers
.Where ( c => c.Orders.Any ())
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE EXISTS(
SELECT NULL AS [EMPTY]
FROM [Orders] AS [t1]
WHERE [t1].[CustomerID] = [t0].[CustomerID]
)
```

### 2.帶條件形式

查詢1996曾購買的客戶
```sql
from c in Customers
where c.Orders.Any(O => O.OrderDate.Year==1996 )
select new {c.CustomerID, c.ContactName}
```
```c#
Customers
.Where (c => c.Orders.Any (O => (O.OrderDate.Year == 1996)))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE EXISTS(
SELECT NULL AS [EMPTY]
FROM [Orders] AS [t1]
WHERE (DATEPART(Year, [t1].[OrderDate]) = 1996) AND ([t1].[CustomerID] = [t0].[CustomerID])
)
```

## All

All 僅允許帶條件式的用法，其和 Any 不同處在於：

- All 要求所有元素都要滿足指定的條件
- Any 要求只要任何一個元素滿足條件即可

### 1.帶條件形式

這個例子也是要查詢 1996 曾購買的客戶，  
當使用 All 時，表示要查詢的客戶，其所有消費都必須在 1996 年裡。  
而使用 Any 表示只要有任一消費在 1996 年即可。  
```sql
from c in Customers
where c.Orders.All(O => O.OrderDate.Year==1996 )
select new {c.CustomerID, c.ContactName}
```
```c#
Customers
.Where (c => c.Orders.All (O => (O.OrderDate.Year == 1996)))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE NOT EXISTS(
SELECT Null AS [EMPTY]
FROM [Orders] AS [t1]
WHERE 
(
CASE 
WHEN DATEPART(Year, [t1].[OrderDate]) = 1996 THEN 1 ELSE 0 
END
) =0
AND ([t1].[CustomerID] = [t0].[CustomerID])
)
```

由 T-SQL 來看，這個 All 方法，實際上是先找出不符合條件的資料，再排除這些資料。  以上面例子來看，就是先找出在 1996 年以外消費的客戶，再排除這些客戶，剩下的就是只有在 1996 年裏消費的客戶。  

**特別注意：**
**All 運算子有一個特性，如果來源序列是空的（沒有任何項目），則不管設定的條件為何，回傳都是真（true）。**  
由 T-SQL 來看，其實這是很容易理解的。  上面例子中，回傳的客戶資料中，實際上也包含從未消費的客人。  因為從未消費的客人並不在排除名單中，所以自然在剩餘名單中了。

# StartsWith、IndexOf、Contains

## StartsWith
```sql
from e in Employees
where e.FirstName.StartsWith("A")
select new { e.EmployeeID, e.FirstName}
```
```c#
Employees
.Where (e => e.FirstName.StartsWith ("A"))
.Select ( e => new { e.EmployeeID, e.FirstName } )
```
```sql
SELECT [t0].[EmployeeID], [t0].[FirstName]
FROM [Employees] AS [t0]
WHERE [t0].[FirstName] LIKE 'A%'
```

## IndexOf

IndexOf 的回傳值以 0 表示第一個字元位置，在 SQL 中的 CharIndex 函式是以 1 表示第一個字元位置。  所以下面這個例子是要找出 "A" 出現在第二個位置的資料  
```sql
from e in Employees
where e.FirstName.IndexOf("A")==1
select new { e.EmployeeID, e.FirstName}
```
```c#
Employees
.Where (e => (e.FirstName.IndexOf ("A") == 1))
.Select ( e => new { e.EmployeeID, e.FirstName } )
```
```sql
SELECT [t0].[EmployeeID], [t0].[FirstName]
FROM [Employees] AS [t0]
WHERE (CHARINDEX('A', [t0].[FirstName]) - 1) = 1
```

## Contains

用於判斷集合中是否包含有某一元素；不延遲。它是對兩個序列進行連接操作的。  視比對的條件，使用 Exist 或 Like 轉譯成 T-SQL 。  

### 1.包含一個物件：

#### eg1
```sql
from c in Customers
where c.ContactName.Contains("A")
select new { c.CustomerID, c.ContactName}
```
```c#
Customers
.Where (c => c.ContactName.Contains ("A"))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE [t0].[ContactName] LIKE '%A%'
```

#### eg2
```sql
var order =(
from o in Orders
where o.ShipCity=="London"
select o ).First();

var query =
from c in Customers
where c.Orders.Contains(order)
select new { c.CustomerID, c.ContactName};

query.Dump();
```
```c#
Customers
.Where (c => c.Orders.Contains (order))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT TOP (1) [t0].[OrderID], [t0].[CustomerID], [t0].[EmployeeID], [t0].[OrderDate], [t0].[RequiredDate], [t0].[ShippedDate], [t0].[ShipVia], [t0].[Freight], [t0].[ShipName], [t0].[ShipAddress], [t0].[ShipCity], [t0].[ShipRegion], [t0].[ShipPostalCode], [t0].[ShipCountry]
FROM [Orders] AS [t0]
WHERE [t0].[ShipCity] = @p0
GO

SELECT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE EXISTS(
SELECT NULL AS [EMPTY]
FROM [Orders] AS [t1]
WHERE ([t1].[OrderID] = 10289) AND ([t1].[CustomerID] = [t0].[CustomerID])
)
```

### 2.包含多個值：

#### eg3
```sql
string[] cities = 
new string[] { "Berlin", "London","Paris" };

var query = 
from c in Customers
where cities.Contains(c.City)
select new {c.CustomerID, c.City};

query.Dump();
```
```c#
Customers
.Where (c => cities.Contains (c.City))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[City]
FROM [Customers] AS [t0]
WHERE [t0].[City] IN ("Berlin", "London","Paris")
```

#### eg4
```sql
var orders =
(
from C in Customers
where C.Orders.Any(O => O.ShipCity=="London" )
select C
);

var query =
from c in Customers
where orders.Contains(c)
select c;
```
```c#
Customers
.Where (c => orders.Contains (c))
.Select ( c => new { c.CustomerID, c.ContactName } )
```
```sql
SELECT [t0].[CustomerID], [t0].[City]
FROM [Customers] AS [t0]
WHERE EXISTS(
SELECT NULL AS [EMPTY]
FROM [Customers] AS [t1]
WHERE ([t1].[CustomerID] = [t0].[CustomerID]) AND (EXISTS(
SELECT NULL AS [EMPTY]
FROM [Orders] AS [t2]
WHERE ([t2].[ShipCity] = 'London') AND ([t2].[CustomerID] = [t1].[CustomerID])
))
)
```