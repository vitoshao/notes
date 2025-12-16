---
title: LINQ 表示式(1) - Select、Where、Distince
layout: default
parent: LINQ
nav_order: 8
description: "LINQ 表示式(1) - Select、Where、Distince"
date: 2013-04-10
tags: [DotNetFramework,LINQ]
postid: "1058398143672197877"
---
關鍵字： Select 、 Where 、 Distinct

# Select

**Select** 關鍵字用來定義要回傳的資料內容

底下根據 Select 回傳的類型，分成７種操作方式：

## 1.基本類型

### 1-1.回傳物件
```sql
List<Employee> employees = TestData.GetEmployeeList();

var query1 =
from emp in employees
select emp;

foreach (var emp in query1)
myDebug.WriteLine("{0} {1} {2}", emp.FirstName, emp.LastName, emp.Age);
```
```c#
var query2 = employees
.Select(emp => emp);

foreach (var emp in query2)
myDebug.WriteLine("{0} {1} {2}", emp.FirstName, emp.LastName, emp.Age);
```

### 1-2.回傳單一欄位

若只選取單一欄位，其回傳值的型別即為該欄位的型別
```sql
var query1c =
from emp in employees
select emp.FirstName;

foreach (var name in query1c)
myDebug.WriteLine(name);  //回傳型別與欄位型別相同
```
```c#
var query1d = employees
.Select(emp => emp.FirstName);

foreach (var name in query1d)
myDebug.WriteLine(name);
```

## 2.匿名類型

若想取回特定的欄位，可以使用匿名型別(Anonymous types)。  
匿名類型是C#3.0中新增的功能。它會在編譯器時自動產生一個匿名類別來儲存回傳的物件。  
你可以自訂匿名類別的 Property 或者由系統自動產生。  

底下例子，編譯器會創建一個匿名類別，並具有 FirstName、LastName、Name 三個屬性。  
```sql
var query2a =
from emp in employees
select new { 
emp.FirstName, 
emp.LastName,
Name = emp.FirstName + " " + emp.LastName
};

foreach (var emp in query2a)
myDebug.WriteLine(emp.Name);
```
```c#
var query2b = employees
.Select(emp => new { 
emp.FirstName, 
emp.LastName, 
Name = emp.FirstName + " " + emp.LastName 
});

foreach (var emp in query2b)
myDebug.WriteLine(emp.Name);
```

## 3.指定類型

你也可以使用自訂類別取代匿名類型，則返回結果就會是該自訂類別。  
```sql
class Emp
{
public string FirstName { get; set; }
public string LastName { get; set; }
public string Name { get; set; }
}

var query3a =
from emp in employees
select new Emp
{
FirstName = emp.FirstName,
LastName = emp.LastName,
Name = emp.FirstName + " " + emp.LastName
};

foreach (Emp emp in query3a)
myDebug.WriteLine(emp.Name);
```
```c#
var query3b = employees
.Select(emp => new Emp
{
FirstName = emp.FirstName,
LastName = emp.LastName,
Name = emp.FirstName + " " + emp.LastName
});
```

## 4.條件類型

條件類型指的是類似 SQL 中的 case when then else 的用法。
```sql
var query4a =
from emp in employees
select new
{
Name = emp.FirstName + emp.LastName,
Age = emp.Age > 30 ? "Old" : "Young"
};
```
```c#
var query4b = employees
.Select(emp => new
{
Name = (emp.FirstName + emp.LastName),
Age = (emp.Age > 30) ? "Old" : "Young"
});
```

## 5.整型類型 (shaped)

整型類型指的是不僅僅返回值使用匿名類別，而且返回值中的屬性，也使用到匿名類別。  

下面這個例子，返回值中，欄位 Name 本身也是一個匿名類別。  
```sql
var query5a = from emp in employees
select new
{
EmployeeId = emp.EmployeeID,
Name = new { First = emp.FirstName, Last = emp.LastName },
};
foreach (var emp in query5a)
myDebug.WriteLine(emp.Name.First + " " + emp.Name.Last);
```

## 6.巢狀類型

巢狀類型指的是返回物件中的屬性，包含另外一個集合。  

下面例子，我們取回訂單資料，並且每筆訂單資料中同時也包含了訂單明細。  
```sql
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnString"]; 
NorthwindContext NWContent = new NorthwindContext(connString.ConnectionString);
var Orders = NWContent.orders;
var OrderDetails = NWContent.order_details;

var query6a = from o in Orders
select new
{
o.OrderID,
o.CustomerID,
OrderDetails = from d in OrderDetails where o.OrderID == d.OrderID select d
};
foreach (var order in query6a)
{
myDebug.WriteLine(order.OrderID + order.CustomerID);
var orderDetails = order.OrderDetails;
foreach (var detail in orderDetails)
{
myDebug.WriteLine(detail.ProductID);
myDebug.WriteLine(detail.UnitPrice \* detail.Quantity);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCg4BnQTKm5ZCoyBK_xkQEIrGaIOffGblv2KI2z01w5kSUHQXkZ6ZB4LLluhBUDC3Pu2tN7nUNBvFJufVjLPBXf_uRwVUCA8CW_F5Ok14GOI0fLn2NSUZOrvve1Qot2nVwe0ZCbuWV_CI/s611/linq-nested.png)

不過，要知道的是，其內部叫用 SQL 的部份，使用的是 Left Join 。  
```sql
SELECT [t0].[OrderID], [t0].[CustomerID], [t1].[OrderID] AS [OrderID2], [t1].[ProductID], [t1].[UnitPrice], [t1].[Quantity], [t1].[Discount], (
SELECT COUNT(\*)
FROM [Order Details] AS [t2]
WHERE [t0].[OrderID] = [t2].[OrderID]
) AS [value]
FROM [Orders] AS [t0]
LEFT OUTER JOIN [Order Details] AS [t1] ON [t0].[OrderID] = [t1].[OrderID]
ORDER BY [t0].[OrderID], [t1].[ProductID]
```

## 7.叫用方法類型

你可以在 Select 中，叫用自訂方法，以進行特定的處理。  
```sql
var query7a =
from emp in employees
select new
{
ID = emp.EmployeeID,
Name = MergeName(emp.FirstName,emp.LastName)
};
foreach (var emp in query7a)
myDebug.WriteLine(emp.ID + emp.Name);
```
```c#
var query7b = employees
.Select(emp => new
{
ID = emp.EmployeeID,
Name = MergeName(emp.FirstName, emp.LastName)
});
```

# Where

**Where**：To filter the result sets.
```sql
var query1 = from emp in employees
where emp.Age > 20 && emp.FirstName.Length > 5
select emp;
```
```c#
var query2 = employees
.Where(emp => (emp.Age > 20 && emp.FirstName.Length > 5));

//也可以用二個 Where 
var query3 = employees
.Where(emp => emp.Age > 20) 
.Where (emp => emp.FirstName.Length > 5);
```

Enumerable.**First**也有同 Where 的功能，但只會返回第一筆，相當於 SQL 中的 Top(1)。
```c#
var query4 = employees
.First(emp => (emp.Age > 20 && emp.FirstName.Length > 5));
```

# Distinct

Distinct 用來查詢不重複的結果集。
```sql
var query1 = (
from emp in employees
select new { emp.FirstName, emp.LastName }
).Distinct()
```
```c#
var query2 = employees
.Select(emp => new { emp.FirstName, emp.LastName })
.Distinct()
```
## 參考資料  

- [(轉貼) 學習 LINQ to SQL](http://jimmy0222.pixnet.net/blog/post/36912973)
- 
-