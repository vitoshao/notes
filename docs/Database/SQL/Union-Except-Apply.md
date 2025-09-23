---
title: 合併查詢結果
layout: default
parent: SQL
nav_order: 7
description: "合併查詢結果"
date: 2013-05-08
tags: [SQL,Query]
postid: "7276634671551804879"
---
在早期的 SQL Server 版本中就有提供 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 運算子，用以結合多個查詢結果。  直到 SQL2005 才又加入了 [EXCEPT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) 和 [INTERSECT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) 運算子，以取得多個查詢結果的差集和交集。  

# UNION

[UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 運算子：

- 用來結合多個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 取得的集合集。
- 要結合的結果集，必須欄位數相同，且對應欄位的資料型別必須相容。
- 結合後的結果集，欄位名稱是由第一個結果集的欄位名稱決定。
```sql
SELECT CONVERT(Char(8),Employees.EmployeeID) FROM Employees
UNION
SELECT Customers.CustomerID FROM Customers
```

其它注意事項：

- 結合後的結果集，其欄位名稱是由第一個結果集的欄位名稱決定。
- ORDER BY 必須置於最後一個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式之後。
- 要撰選資料，要在每一個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式之中加入 WHERE 子句。
- 使用 ALL 關鍵子可以回傳包含重複的列。
```sql
SELECT CONVERT(Char(8),Employees.EmployeeID) as AA FROM Employees WHERE EmployeeID>9
UNION ALL
SELECT Customers.CustomerID as BB FROM Customers WHERE CustomerID like 'A%'
ORDER BY AA desc
```

### 例
```sql
SELECT OrderID, ProductID FROM OrderDetails WHERE OrderID=10285 OR OrderID=10294
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-JjwezYhDQOiT5YSN3x142CkfLdb4vDxJrWAE3ii27ce3Q9ARhrTxikedo4GVF6V4bm5ImEvEEPebVwGvKkEtKDvejS81eO3uMjgS97bczENoYCcOS8ssHpEhoGFfAmtEazXoGMZVTKU/s0/sql-union-testdata.png)
```sql
SELECT ProductID FROM OrderDetails WHERE OrderID=10285
UNION
SELECT ProductID FROM OrderDetails WHERE OrderID=10294

SELECT ProductID FROM OrderDetails WHERE OrderID=10285
UNION ALL
SELECT ProductID FROM OrderDetails WHERE OrderID=10294
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiVkzBQevtNzrgv1Jbd0l0Jaks4w2u01hg8W2GuWeivG4vaQVlRjv8sIcVavWXEFcMuWhNzH-RMzYh55ybb8JoqAihfbYc0bOL1XgBRdKvlNOCvy6h1gisY3LLV3pZtN-cWlBiJvCIguk/s0/sql-union.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQ2VmubMzN8ynIBSU4tK0pXb0PKPY6QDPoctxFaYt69bb-2KvJ7x8pzg5_fvvpQHmoYH6SzhqLg0hF-3xQFkg7w_X_H97NalaiFPXRhHd8EbByH38WpCs1SveSu3YD8rRP5VxAmHglpdw/s0/sql-union-all.png)

# EXCEPT and INTERSECT

You use the [EXCEPT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) and [INTERSECT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) operators to perform semi-joins and anti-joins. 

- semi-joins：（反聯結，交集）returns rows from the **first input** if there is as least one matching row in the second input.
- anti-joins：（半聯結，差集）returns rows from the **first input** if there are no matching rows in the second input.

### 例
```sql
--Except
SELECT ProductID FROM OrderDetails WHERE OrderID=10285
Except
SELECT ProductID FROM OrderDetails WHERE OrderID=10294

--Intersect
SELECT ProductID FROM OrderDetails WHERE OrderID=10285
Intersect
SELECT ProductID FROM OrderDetails WHERE OrderID=10294
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhw5TRGai3Q_UmWDNMwrNLcqaAF136P5zLOHlW1yR_s8m4Fo0O3VrYiZHbP89nFcuRmFb6blAto6L9Gbh174Nr9X8dSXHa5m2UaMlYG4_2yWH-8mhfMz8TX3deDp7HT68VWFAmmnGlHwnc/s0/sql-union-Except.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWQhmEKFrVCGwHcqaKyprCVS3qz3ClcXxgsLNbFN9LJcnC2F21Sg5z8eTAXfm2sx0q5COxE7Efx6jVxAEHNL2eMt4SohyphenhyphenuGWYp49xuaj0Ojt9u0rT5f6gZwSut77M7AXBiJFMyyVRd2xw/s0/sql-Intersect.png)

# APPLY

[APPLY](http://msdn.microsoft.com/en-us/library/ms175156.aspx) 運算子主要用來將一個子查詢結果和一個資料表值函式(table-valued funtion)做 JOIN。  運算式會將每一筆查詢結果帶入資料表值函式，然後將子查詢結果會當成 left input ，資料表值函式則當成 right input ，再執行結合。  

[APPLY](http://msdn.microsoft.com/en-us/library/ms175156.aspx) 運算子有二種用法： [CROSS APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx)  和 [OUTER APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) 。這二個都是從 SQL2005 才提供的語法。若要使用 APPLY，資料庫相容性層級至少必須是 90。  

- [CROSS APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) ：相當於使用 Inner Join 與**資料表值函式**（table-value function）執行 JOIN 查詢。
- [OUTER APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) ：相當於使用 Left Outter Join 與**資料表值函式**（table-value function）執行 JOIN 查詢。

建立 table-value 函式
```sql
CREATE FUNCTION funOrderAmount(@OrderID int)
RETURNS TABLE
RETURN
(
	SELECT O.OrderID, SUM(UnitPrice\*Quantity) as Amount
	FROM Orders O
	INNER JOIN OrderDetails OD ON O.OrderID=OD.OrderID
	WHERE O.OrderID=@OrderID
	GROUP BY O.OrderID
)
```

使用 [CROSS APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) 取得所有訂單的總金額
```sql
SELECT O.OrderID, O.CustomerID, OA.Amount
FROM Orders O
CROSS APPLY funOrderAmount(O.OrderID) OA
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrmTQVwJVdUH029telFX6AOyK1RdfaWNhr3Qu7jrmFEaCP5YWXK0DGtruAinOpVspoWo0aXnOrbH9mt8rmqp3FZ5jL-s2WIEYs5TItLGJowFAScoi1IfR6JR_WmxuX_rC0ScnABoUjJNk/s250/sql-cross-apply.png)

如果使用 [OUTER APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) ，若 right table 沒有結果，就會得到 NULL 
```sql
SELECT O.OrderID, O.CustomerID, OA.Amount
FROM Orders O
CROSS APPLY funOrderAmount(O.OrderID) OA
```
## 參考資料  

- 使用 UNION 合併結果集
- 
-