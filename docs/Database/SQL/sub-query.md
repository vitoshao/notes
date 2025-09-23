---
title: 子查詢
layout: default
parent: SQL
nav_order: 3
description: "子查詢"
date: 2013-05-08
tags: [SQL,Query Advance]
postid: "8797171412260272832"
---
子查詢讓關發人員仍夠以較直覺的方式撰寫 TSQL ，也能夠讓較複雜的查詢在單一次的查詢中完成。  子查詢最多能夠使用至 32 層，但仍取決於伺服器的可用記憶體及查詢的複雜度。  

# 使用於 WHERE 條件的子查詢

子查詢可分成二大類型：關聯子查詢與獨立子查詢。  

## 獨立子查詢 (Noncorrelated Subqueries)

獨立子查詢就是子查詢可以單獨執行，和主查詢沒有相依性。
```sql
-- 清單型的 WHERE 子查詢
SELECT * FROM Customers
WHERE Region IN ( SELECT RegionID FROM Region WHERE RegionID<3 )

-- 單一值的 WHERE 子查詢
SELECT * FROM Products
WHERE UnitPrice > (SELECT AVG(UnitPrice) FROM Products)
```

## 關聯子查詢 (Correlated Subqueries)

關聯子查詢就是子查詢中的查詢條件必須參考到主查詢中的資料。
```sql
-- 相依型的 WHERE 子查詢
SELECT * FROM Products P
WHERE Exists ( 
	SELECT 1 FROM OrderDetails OD 
	WHERE OD.ProductID = P.ProductID
)
```

# 使用於 From 的子查詢
```sql
SELECT O.OrderID, O.ShippedDate, OD.Total
FROM Orders O INNER JOIN (
	SELECT OrderID, SUM(UnitPrice*Quantity) as Total
	FROM OrderDetails 
	Group By OrderID
) OD ON O.OrderID=OD.OrderID
```

# 使用於 SELECT 的子查詢
```sql
SELECT O.OrderID, O.ShippedDate, 
( SELECT COUNT(*) FROM OrderDetails OD WHERE O.OrderID=OD.OrderID ) as ItemCount
FROM Orders O
```
## 參考資料  

- 
- 
-