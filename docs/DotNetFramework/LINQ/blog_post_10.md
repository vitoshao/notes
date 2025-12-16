---
title: LINQ 表示式(2) - OrderBy 、 GroupBy 、 Into 、 Max 、 Min 、 Average 、 Sum 、 Count 、 Aggregate
layout: default
parent: LINQ
nav_order: 10
description: "LINQ 表示式(2) - OrderBy 、 GroupBy 、 Into 、 Max 、 Min 、 Average 、 Sum 、 Count 、 Aggregate"
date: 2013-04-11
tags: [DotNetFramework,LINQ]
postid: "5466830926949781563"
---
關鍵字： OrderBy 、 GroupBy 、 Into 、 Count 、 Max 、 Min 、 Average 、 Sum 、 Aggregate

# OrderBy

**OrderBy** 是用來將結果集做昇冪排序，加上 **descending** 表示降冪排序。  
```sql
var query1 = from emp in Employees
orderby emp.FirstName, emp.LastName, emp.City descending
select new 
{
emp.EmployeeId, 
emp.Age, 
emp.FirstName
};
```

若是使用 Lambda 表示式，當有多個欄位做排序時，必須使用 **ThenBy** 或 **ThenByDescending**。  
```c#
var query2 = Employees
.OrderBy(emp => emp.FirstName)
.ThenBy(emp => emp.LastName)
.ThenByDescending(emp => emp.City)
.Select(emp => new
{
emp.EmployeeId,
emp.Age,
emp.FirstName
});
```

# GroupBy

**GroupBy** 是用來將結果集分組。  

## 簡單形式
```sql
var query1 =
from order in OrderDetails
where order.OrderID == 10248 || order.OrderID == 10249
group order by order.OrderID into gorder
select gorder;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQieEnmr1uEdPpGuDqssiYBlANOKQZbPk8L3PIILaeBem8zyh6sPtyIwaGuQ8j-Je36YbQ4nMaNr4WTQyorBhUX1EH8lgMM8_rWyD9u2q6q0kfAMAhNaTY3AmLtyxF_Bqp3HE_6_uhXV8/s455/linq-groupby.png)

由上面 LINQPad 所得到的回傳結果，  看的出來，**GroupBy** 只是將結果依 GroupBy 所指定的 Key 值區分成不同的組別而已。  若要列舉這些結果可以這麼用：
```c#
foreach (var gorder in query1)
{
myDebug.WriteLine("Key : {0}", gorder.Key);
foreach (var order in gorder)
{
myDebug.WriteLine("{0} {1} {2}", order.ProductID, order.Quantity, order.UnitPrice);
}
}
```

若使用 Lambda 可以這麼操作：
```c#
var query2 = OrderDetails
.Where(order => ((order.OrderID == 10248) || (order.OrderID == 10249)))
.GroupBy(order => order.OrderID)
.Select(gorder => gorder);
```

## 多重欄位分組(Multiple Columns)

若要分組多個欄位，必須使用匿名型別。  
```c#
group x by new { x.Column1, x.Column2 }
```

它的意義是說，每個 Key 值是由二個欄位組成，而這個 Key 值本身也是個匿名型別。  
例如：  
```sql
var query3 =
from order in OrderDetails
where order.OrderID == 10285 || order.OrderID == 10294
group order by new { order.OrderID, order.ProductID };

foreach (var gorder in query3)
{
myDebug.WriteLine("Key : OrderID:{0} ProductID:{1}", gorder.Key.OrderID, gorder.Key.ProductID); 
foreach (var order in gorder)
{
myDebug.WriteLine("{0} {1} {2}", order.ProductID, order.Quantity, order.UnitPrice);
}
}
```

## 使用運算式分組(Expression)

通常使用上大都依據欄位值來分組，但是這裡也可以使用運算式分組。

下面這個例子，會依據價格是否大於 20 來分組
```sql
from order in OrderDetails
where order.OrderID==10285 || order.OrderID==10294
group order by new { UnitPrice = order.UnitPrice > 20 } into g
select g
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNeLWPtmvnShgs2pgmKBWXmhyuJleobUC4XYF_EgSGwKDimwyGtBLkDppSaqa-8ST4lG-iF5qi4-tMyE47z6VDA-mDWkER09SaAsX8TBSQRxJT8bGGd29om5rSOq5_5nrjgV0URRYNxcI/s418/linq-groupby2.png)

## GroupBy + OrderBy

### 依群組 Key 值欄位排序
```sql
//eg1
from order in OrderDetails
where order.OrderID==10285 || order.OrderID==10294
group order by order.OrderID into g
orderby g.Key
select g

//eg2
from order in OrderDetails
where order.OrderID==10285 || order.OrderID==10294
group order by new { order.OrderID, order.ProductID } into g
orderby g.Key.OrderID, g.Key.ProductID
select g
```

### 依群組中的資料排序

下面範例，群組的排序是依據每一個組別中的第一筆的ProductID順序。
```sql
from order in OrderDetails
where order.OrderID==10285 || order.OrderID==10294
group order by order.OrderID into g
orderby g.First().ProductID
select g
```

# Into

**into** 關鍵字可以用於建立暫時的識別項，用來識別 group、 join 或 select 子句的結果。  

範例：
```sql
from order in OrderDetails
where order.OrderID==10285 || order.OrderID==10294
group order by order.OrderID into g
orderby g.Key
select g
```

# Count 、 Max 、 Min 、 Average 、 Sum 、 Aggregate

Aggregation 包含 Max 、 Min 、 Average 、 Sum 、 Count 、 Aggregate 等關鍵字。  
使用這一類功能時，都會使用立即查詢，而不是延遲查詢。  

## Count

**Count** 與 **LongCount** 都可以取得元素個數。返回型別不同而已。  

### 簡單形式
```sql
var result1 =
(
from order in OrderDetails
where order.OrderID == 10285 || order.OrderID == 10294
group order by order.OrderID into gorder
select gorder
).Count();

myDebug.WriteLine("Result : {0}", result1.ToString());
```
```c#
var result2 = OrderDetails
.Where(order => ((order.OrderID == 10285) || (order.OrderID == 10294)))
.GroupBy(order => order.OrderID)
.Count();
```

### 分組統計資料
```sql
from order in OrderDetails
group order by order.OrderID into g
select new 
{
g.Key,
RowCount = g.Count()
}
```
```c#
OrderDetails
.GroupBy (order => order.OrderID)
.Select ( g => new  
{
Key = g.Key, 
RowCount = g.Count ()
})
```

### 帶條件分組統計

相檔於 SQL 中的 Having
```sql
from order in OrderDetails
group order by order.OrderID into g
where g.Count() > 5
select new 
{
g.Key,
RowCount = g.Count()
}
```
```c#
OrderDetails
.GroupBy (order => order.OrderID)
.Where (g => g.Count () > 5)
.Select ( g => new  
{
Key = g.Key, 
RowCount = g.Count ()
})
```

## Max 、 Min

### 簡單形式

取得 Products 中最貴的價格
```sql
//方法１
(
from p in Products
select p.UnitPrice
).Max()

//方法２
(
from p in Products
select p
).Max(x=>x.UnitPrice)
```
```c#
//方法１
Products.Max(x=>x.UnitPrice)

//方法２
Products.Select(x=>x.UnitPrice).Max()
```

### 找整筆資料

取得 Products 中最貴的產品
```sql
(
from p in Products
where p.UnitPrice ==
(
from mp in Products
select mp.UnitPrice
).Max()
select p
).Take(1)
```
```c#
Products.First( 
p => p.UnitPrice == Products.Max(x=>x.UnitPrice)
)
```

### 分組統計資料

取得每一張 Order 中最貴的產品
```sql
from order in OrderDetails
group order by order.OrderID into g
select new 
{
g.Key,
Product = from details in g
where details.UnitPrice == g.Max(detail => detail.UnitPrice)
select details
}
```
```c#
OrderDetails
.GroupBy (order => order.OrderID)
.Select ( g => new  
{
Key = g.Key, 
Product = g.Where (details => details.UnitPrice == g.Max (detail => detail.UnitPrice))
}
)
```

## Average 、 Sum

### 簡單形式

取得 Products 中的平均價格
```sql
(
from p in Products
select p.UnitPrice
).Average()

(
from p in Products
select p
).Average(x=>x.UnitPrice)
```
```c#
Products.Average(x=>x.UnitPrice)

Products.Select(x=>x.UnitPrice).Average()
```

### 分組統計資料

取得每一張 Order 的平均價格
```sql
from order in OrderDetails
group order by order.OrderID into g
select new 
{
g.Key,
AveragePrice = g.Average (detail => detail.UnitPrice)
}
```
```c#
OrderDetails
.GroupBy (order => order.OrderID)
.Select ( g => new  
{
Key = g.Key, 
AveragePrice = g.Average (detail => detail.UnitPrice)
})
```

## Aggregate

累計