---
title: LINQ 表示式(3) - Join
layout: default
parent: LINQ
nav_order: 11
description: "LINQ 表示式(3) - Join"
date: 2013-04-11
tags: [DotNetFramework,LINQ]
postid: "8648113006601671191"
---
關鍵字： Join

當二個序列資料(sequences)存在可以用來比較的屬性欄位時，我們就可以用 **Join** 來建立二個序列資料間的關連。  你可以使用 **equals** 關鍵字來判斷二個資料的 Key 值是否相等。  

#### 在 Linq 中操作 Join 包含三種型態：

- **Inner Join** ：Join　查詢。
- **Group Join** ：分組查詢。
- **Left Join** ：一對多選擇。

# Inner Join

要建立 INNER JOIN 關連的 LINQ 語法，有以下幾種寫法：

## 類型一
```sql
from p in Products 
from od in OrderDetails
where od.ProductID == p.ProductID 
select new {
od.OrderID, od.ProductID, p.ProductName
}
```

若是使用具有關連性的 Entity 模型中的物件，可以直接這樣使用：
```sql
from p in Products
from od in p.OrderDetails
select new {
od.OrderID, od.ProductID, p.ProductName
}
```

以上所產生的 T-SQL 語法如下：
```sql
SELECT [t0].[OrderID], [t0].[ProductID], [t1].[ProductName]
FROM [Order Details] AS [t0], [Products] AS [t1]
WHERE [t0].[ProductID] = [t1].[ProductID]
```

## 類型二
```sql
from p in Products
join od in OrderDetails on p.ProductID equals od.ProductID
select new { od.OrderID, od.ProductID, p.ProductName }  //produces flat sequence
```
```c#
Products
.Join (
OrderDetails, 
p => p.ProductID, 
od => od.ProductID, 
(p, od) => 
new  
{
OrderID = od.OrderID, 
ProductID = od.ProductID, 
ProductName = p.ProductName
}
)
```
```sql
SELECT [t1].[OrderID], [t1].[ProductID], [t0].[ProductName]
FROM [Products] AS [t0]
INNER JOIN [Order Details] AS [t1] ON [t0].[ProductID] = [t1].[ProductID]
```

# Group Join

將 **join** 語法加入 **into** 表示，就是 Group Join 。  

## 類型一

Group Join 會回應一個階層式的序列結果。若比對不到的元素，就回應一個空陣列。  
```sql
from o in Orders
join od in OrderDetails on o.OrderID equals od.OrderID into tmpDetail
select new { o.OrderID, tmpDetail }
```
```c#
Orders
.GroupJoin (
OrderDetails, 
o => o.OrderID, 
od => od.OrderID, 
(o, tmpDetail) => 
new  
{
OrderID = o.OrderID, 
tmpDetail = tmpDetail
}
)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgim222Jyc7LpmA9XQBmqhIxZ2lykFF8obggtg7hss2kUkWoOjNxr7PGlBdf007Zj3egL85WuUhLcveuqK8f8hfMonuPmldndUvGpgDO8q8IdgNdc08qYk_hMQOqdCEeSXbRhstYkpmmA4/s443/linq-group-join-1.png)

## 類型二

你也可以針對 Join 後的結果再進行篩選
```sql
from o in Orders
join od in OrderDetails on o.OrderID equals od.OrderID into tmpDetail
from detail in tmpDetail
where detail.UnitPrice > 80
select new { o.OrderID, tmpDetail }
```
```sql
SELECT [t0].[OrderID], [t2].[OrderID] AS [OrderID2], [t2].[ProductID], [t2].[UnitPrice], [t2].[Quantity], [t2].[Discount]
FROM [Orders] AS [t0]
CROSS JOIN [Order Details] AS [t1]
LEFT OUTER JOIN [Order Details] AS [t2] ON [t0].[OrderID] = [t2].[OrderID]
WHERE ([t1].[UnitPrice] > @p0) AND ([t0].[OrderID] = [t1].[OrderID])
ORDER BY [t0].[OrderID], [t1].[OrderID], [t1].[ProductID], [t2].[ProductID]
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjuezOtUmsD38dAB2SNiESXnIL6xOzF8KDKQNc78NBytCuWHHLqxkRy0nSXwnF1o-slYnWTQfGewa63wz37Z6TrHdFY2ZDiwLeWe-hYsEdxXSHsr1lkyorVVlK2MxSq8rqNRlukSQhN8E/s442/linq-group-join-2.png)

上面這個例子，我們的條件是商品單價大於80，但是 Select 中，我們取的是整個 detail ，所以回傳的是反是訂單中有含80元以上的品項就會全部回傳。  

若是你只想取得商品編號，改成這樣就可以了
```sql
from o in Orders
join od in OrderDetails on o.OrderID equals od.OrderID into tmpDetail
from detail in tmpDetail
where detail.UnitPrice > 80
select new { o.OrderID, o.CustomerID, detail.ProductID }
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgLwkiRfUN4tPvFKZ7RkrfuA186TB7vvUuXL9uvJqCUbx-gefrw0XE-YAYD9wlbJVI5aXhPfJo8qq9eOWt0A6BpYTkoUaYto_ujjgPddiCclsORKF_Mlja3UwJZDAUEjDtn25oAO1jRAk/s280/linq-group-join-3.png)

# Left Join

使用 Inner Join 時，若二者之間沒有 matching 時，就比對不出來。  使用 Left Join 時，不管有沒有 matching 到，左半部的序列資料都會被 return 。　  

## 類型一：透過關連性

在 Linq to SQL 資料模型，由於 Order 和 Customer 透過 CustomerID 存在關連，你可以簡單的使用以下語法得到 Left Join 結果。  

下面這個例子中，因為 Order 表格的 CustomerID 是一個 Nullable 欄位，所以會使用 Left Join 轉譯成 T-SQL.  如果 Order 表格的 CustomerID 是非 Null 欄位，則會使用 Inner Join 轉譯成 T-SQL  
```sql
from o in Orders
select new {  o.OrderID, o.Customer.ContactName }
```
```sql
SELECT [t0].[CustomerID], 
(CASE 
WHEN ([t1].[OrderID]) IS NULL THEN @p0
ELSE [t1].[OrderID]
END) AS [OrderID2]
FROM [Customers] AS [t0]
LEFT OUTER JOIN [Orders] AS [t1] ON [t0].[CustomerID] = [t1].[CustomerID]
ORDER BY [t1].[OrderID]
```

## 類型二：使用 DefaultIfEmpty 屬性

在 SQL 中，通常使用 Left Join 時，比較多的情況是用在一對多，在不確定關連性下，  若要使用 LINQ 語法來建立 Left Join 關連，只要在 Group Join 加上 [DefaultIfEmpty](http://msdn.microsoft.com/zh-tw/library/bb360530%28v=vs.100%29.aspx) 以處理比對不到時的回應資料。  

下面例子，我們可以透過 Left Join ，找出所有客戶的所有訂單編號。
```sql
from C in Customers
join O in Orders on C.CustomerID equals O.CustomerID into tmp
from T in tmp.DefaultIfEmpty()
select new { 
C.CustomerID, 
T.OrderID
}
```

上面這個例子，執行時你會發現以下錯誤：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDUYedh4wWBLZd50QakrO_Kj3BXUtGEx5IwwEQNHcIOljutb8JYHie3wiiykqXSYagOVf77G_D9EMLwO3BGUyFofSNWD3z1A76woG-0-yoC6IrRjNsTDqD1qWq21e5cOP3NRrrRRuOhS8/s443/linq-null-error.png)

這是在 select 的匿名型別中，每個欄位其實都是個物件，若為 null 時，你必須給定一個對等的輸出。  所以這個例子，若為 null ，就必須 new 一個 Nullable&lt;int&gt;() 給它，程式碼調整如下：。  
```sql
from C in Customers
join O in Orders on C.CustomerID equals O.CustomerID into tmp
from T in tmp.DefaultIfEmpty()
orderby T.OrderID
select new { 
C.CustomerID, 
OrderID = T.OrderID == null ? new Nullable<int>() : T. OrderID
}
```
```sql
SELECT [t0].[CustomerID], 
(CASE 
WHEN ([t1].[OrderID]) IS NULL THEN @p0
ELSE [t1].[OrderID]
END) AS [OrderID2]
FROM [Customers] AS [t0]
LEFT OUTER JOIN [Orders] AS [t1] ON [t0].[CustomerID] = [t1].[CustomerID]
ORDER BY [t1].[OrderID]
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPqhcAuo0jAhwWx0F4YN1c8FY8fomZWzbXMZdav2XpuKpaUC-mHOWYtEKxwTP8MIfO6GMjAPPn2U2bfzFqLTWcyH1iQjs35i288PA4nXNjzO0rPMR6ErX88nwdd7waLN5ir4vX906B7kM/s279/linq-left-join-1.png)

## 類型三：使用 SelectMany

### 例一

上面那個範例，我們可以直接透過 [SelectMany](http://msdn.microsoft.com/zh-tw/library/bb548748%28v=vs.100%29.aspx) 這個擴充方法來做，可以簡便許多。
```c#
Customers.SelectMany(
O => O.Orders.DefaultIfEmpty(),
(C, O) => new
{
C.CustomerID,
OrderID = O.OrderID == null ? new Nullable<int>() : O.OrderID
}
)
.OrderBy(O => O.OrderID)	
```
```sql
SELECT [t2].[CustomerID], [t2].[value] AS [OrderID2]
FROM (
SELECT [t0].[CustomerID], 
(CASE 
WHEN ([t1].[OrderID]) IS NULL THEN @p0
ELSE [t1].[OrderID]
END) AS [value]
FROM [Customers] AS [t0]
LEFT OUTER JOIN [Orders] AS [t1] ON [t1].[CustomerID] = [t0].[CustomerID]
) AS [t2]
ORDER BY [t2].[value]
```

使用 SelectMany 方法時，如上面程式碼，若少了 [DefaultIfEmpty](http://msdn.microsoft.com/zh-tw/library/bb360530%28v=vs.100%29.aspx) 屬性設定，那麼轉譯的 T-SQL 將會使用 Inner Join ，結果就無法 Join 到空的資料。

### 例二：Select vs SelectMany

在說明前，先看看底下這二段 LINQ ，意義有什麼不同？
```sql
Customers
.Where(C => C.City == "Madrid")
.Select( C => C.Orders )
```
```sql
Customers
.Where(C => C.City == "Madrid")
.SelectMany( C => C.Orders )
```

上面二段 LINQ 程式碼，同樣是要找出住在馬德里的客戶的訂單。但其回傳結果分別得到 3筆 和 9筆 二個結果如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7tyVyCfzaJ2SZNIrJuwQtwZl6Tw5fmuDvqXjGpe0WWACbmNGtNwtFQYy_Pchg7w8FUOd_50Sa7L_LBg4pTTXjmi5c3lRSSGXYfeIaRw5fMy6pP82Skekr-_Ha-XyfvFwpYM-zOZdx2so/s596/linq-select-selectmany-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjI_ZGRd5jGMjz7u7wpysUKauxt_0R3wxp_4EDVPSR7RRyc0K2YC66dc4x6ud8yyo5TlJOz_9a_oGrRr5n9FdhOG4XJ5srUSHD3dMdcfwA_dMUKG64kOZSavfSRj9yAA0UxzCL97RdDjqc/s416/linq-select-selectmany-2.png)

所以從回傳的結果，可以看的出來其回傳型別分別是：  
使用 Select 取得訂單，則不會合併訂單集合，則回傳結果的型別是 IEnumerable&lt;List&lt;Order&gt;&gt;。   
改用 SelectMany 取得資料庫中每位客戶的訂單，則回傳結果的型別是 IEnumerable&lt;Order&gt; 。   

SelectMany 方法會先列舉 TSource 的每個項目，接著叫用 selector，並傳回一系列的值，以得到一個二維的結果集。最後再將這個二維的集合簡化為一維並回傳。  以下是它們的原型宣告：  
```c#
public static IEnumerable<TR> Select<TS, TR>(this, Func<TS, TR> selector);

public static IEnumerable<TR> SelectMany<TS, TR>　　(Expression<Func<TS, IEnumerable<TR>>> selector)
public static IEnumerable<TR> SelectMany<TS, TC, TR>(Expression<Func<TS, IEnumerable<TC>>> collectionSelector, Expression<Func<TS, TC, TR>> resultSelector)

//TR:TResult
//TS:TSource
//TC:TCollection
```

# TOP

[Enumerable.Take&lt;TSource&gt;](http://msdn.microsoft.com/zh-tw/library/bb503062.aspx) ：從序列開頭傳回指定的連續項目數目。
```c#
DataTable employees = TestData.GetEmployeeDataTable();

var result = ( from employee in employees.AsEnumerable()
select employee ).Take(3);
```
## 參考資料  

- [join clause (C# Reference)](http://msdn.microsoft.com/en-us/library/bb311040.aspx)
- [在LINQ中實踐多條件LEFT JOIN](http://blog.darkthread.net/post-2010-08-12-linq-left-join.aspx)
- [分享幾個 LINQ to SQL 執行各種 Join 查詢的技巧](http://blog.miniasp.com/post/2010/10/14/LINQ-to-SQL-Query-Tips-INNER-JOIN-and-LEFT-JOIN.aspx#id_c648c9c7-e014-4d75-9eb6-d508dbae9d0a)