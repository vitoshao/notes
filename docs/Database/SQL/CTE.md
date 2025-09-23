---
title: 通用資料表運算式(CTE)
layout: default
parent: SQL
nav_order: 2
description: "通用資料表運算式(CTE)"
date: 2013-05-08
tags: [SQL,Query Advance]
postid: "7426099233810568098"
---
# 通用資料表運算式（CTEs）

[通用資料表運算式 (Common Table Expression)](http://msdn.microsoft.com/zh-tw/library/ms175972.aspx) 是 SQL 2005 才開始支援的查詢語法。  它有二個主要的用途：簡化子查詢與遞迴查詢。  

## 通用資料表(CTE)

CTE 就像是一個**暫存性資料表**或者**暫存檢視**，你可以在 CTE_query_definition 中使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 語法建立這個暫時性的資料表。  

語法：
```sql
WITH <CteName> AS [(ColName[,...n])]
(
-- CTE_query_definition
)
-- outer query
```

在通用資料表中的 CTE_query_definition ，不能使用下列項目： 

- ORDER BY (除非指定了 TOP 子句)
- INTO
- 含有查詢提示的 OPTION 子句
- FOR XML
- FOR BROWSE

### 例一：當做暫存表使用
```sql
WITH OrderItems AS
(
	SELECT OrderID, COUNT(*) as ItemCounts
	FROM   OrderDetails
	GROUP BY OrderID
)

--在 WITH 定義後，緊接者 SELECT 查詢 

SELECT O.OrderID, OI.ItemCounts FROM Orders O
INNER JOIN OrderItems OI ON O.OrderID=OI.OrderID
```

前面提過，CTE可以用來簡化子查詢，所以上面例子可以等於以下語法：
```sql
SELECT O.OrderID, OD.ItemCounts FROM Orders O
LEFT JOIN 
(
	SELECT OrderID, COUNT(*) as ItemCounts
	FROM   OrderDetails
	GROUP BY OrderID
) as OD 
ON O.OrderID=OD.OrderID
```

前面提過，CTE就像一個暫存資料表，所以在 CTE 後面，你可以使用任何 [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 來參考這個暫存資料表。
```sql
DELETE OrderDetails 
FROM OrderDetails, OrderItems
WHERE OrderDetails.OrderID=OrderItems.OrderID AND OrderItems.ItemCounts>10
```

### 例二：定義多個 CTE 

非遞迴的 CTE 允許同時定義多個 CTE 查詢
```sql
WITH OrderTable(OrderID, CustomerID) AS
(
	SELECT OrderID, CustomerID
	FROM   Orders
),
OrderDetail(OrderID, Total) AS
(
	SELECT OrderID, SUM(UnitPrice*Quantity) as Total
	FROM   OrderDetails
	GROUP BY OrderID
)
SELECT O.OrderID, O.CustomerID, OD.Total
FROM OrderTable O
INNER JOIN OrderDetail OD ON O.OrderID=OD.OrderID
```

## 遞迴通用資料表(Recursive CTE)

遞迴 CTE 的 CTE_query_definition 包含二個部份：錨點部分和遞迴部分。  

- 錨點部分：當作遞迴查詢的初始資料，使用 [UNION ALL](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 和遞迴部分的查詢結果結合。
- 遞迴部分：透過遞迴方式，與 CTE 反覆執行。

語法：
```sql
WITH <CteName> AS [(ColName[,...n])]
(
--CTE_query_definition

-- 1) anchor member 錨點部分

UNION ALL

-- 2) recursive member 遞迴部分
)
-- outer query
```

### 使用上注意事項

#### 遞迴 CTE 的查詢定義中包含二個部份：錨點部分和遞迴部分。且必須符合以下限制：

1. 使用下列運算子組合： [UNION ALL](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 、 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 、 [INTERSECT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) 或 [EXCEPT](http://msdn.microsoft.com/zh-tw/library/ms188055.aspx) 。
2. 但是最後一個錨點和第一個遞迴之間只能使用 [UNION ALL](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 運算子組合。
3. 錨點和遞迴的欄位數要相同。
4. 錨點和遞迴的欄位型別也要相同。
5. 遞迴成員的 FROM 子句只能參考 CTE expression_name 一次

#### 在遞迴通用資料表中的 CTE_query_definition ，不能使用下列項目：

- SELECT DISTINCT
- PIVOT
- HAVING
- Aggregation
- TOP
- LEFT、RIGHT、OUTER JOIN (允許 INNER JOIN)
- 子查詢
- 適用於 CTE_query_definition 內 CTE 之遞迴參考的提示。

### OPTION 子句

在 CTE 語法中，你可以透過 **OPTION** 子句來設定 **MAXRECURSION** 屬性，以定義遞迴的最高層次。  當遞迴層次超過指定的大小就會引發錯誤。例如：  
```sql
WITH cteOrg (OrgID, CName, level) AS
(
/*第一部分為 Anchor Member , 指不會被遞迴呼叫到的部分  */ 

SELECT OrgID, CName, 1
FROM tblOrganization
WHERE ParentID is Null

UNION ALL

/*會在遞迴過程中反覆執行, 直到無任何查詢結果為止   */ 

SELECT Org.OrgID, Org.CName, level+1
FROM tblOrganization Org
INNER JOIN cteOrg ON cteOrg.OrgID = Org.ParentID
)
SELECT * FROM cteOrg
OPTION (MAXRECURSION 5);
```

### ; WITH

因為 WITH 這個關鍵字用在許多表達式中，所以，若一個 CTE 表達式的第一行不是由 WITH 開始，就必須加上分號(semicolon)來斷行。例如：
```sql
;WITH cteOrg (OrgID, CName, OrgPath, level) AS
(
	SELECT OrgID, CName, cast(OrgID as varchar(200)) as OrgPath, 0
	FROM tblOrganization
	WHERE ParentID is Null

	UNION ALL

	SELECT Org.OrgID, Org.CName, cast(OrgPath+'/'+Org.OrgID as varchar(200)), level+1
	FROM tblOrganization Org
	INNER JOIN cteOrg ON cteOrg.OrgID = Org.ParentID
)
SELECT OrgID, Replicate('　　',level)+CName, OrgPath FROM cteOrg
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhABTSiCKluYXpBxLwh-Sw3WXHG_LiMswztLgYJPbRB3kCH8IFFLHogkYyPUhwvi31ylH3BcWdl_1CLJYo_3amBPycvgqc64CwldD3KYHV_whyf-FzurwNCJdSHKxNxeCVXP4Z6VEjTBYM/s0/sql-cte-result.png)
## 參考資料  

- 
- 
-