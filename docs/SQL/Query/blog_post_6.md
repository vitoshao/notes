---
title: 使用 JOIN 查詢
layout: default
parent: Query
nav_order: 6
description: "使用 JOIN 查詢"
date: 2013-05-02
tags: [SQL,Query]
postid: "9202757171479878393"
---
SQL 中的 JOIN 運算，大至包含以下幾種分類

1. [INNER JOIN](http://msdn.microsoft.com/zh-tw/library/ms190014%28v=sql.105%29.aspx)
2. [OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx)
- [LEFT OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx)
- [RIGHT OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx)
- [FULL OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx)
3. [CROSS JOIN](http://msdn.microsoft.com/zh-tw/library/ms190690%28v=sql.105%29.aspx)
4. [Self-Joins](http://msdn.microsoft.com/zh-tw/library/ms177490%28v=sql.105%29.aspx)

INNER 與 OUTER 關鍵字皆可以省略。 

# INNER JOIN

[INNER JOIN](http://msdn.microsoft.com/zh-tw/library/ms190014%28v=sql.105%29.aspx) 只會回傳 join 條件中有匹配的資料列。
```sql
DECLARE @Emp1 table( id int, name nvarchar(10) )
DECLARE @Emp2 table( id int, age int )

insert @Emp1 values (1,'vito'),(2,'shao'),(3,'peter')
insert @Emp2 values (1,20),(3,30),(4,40)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjq-sEckA08KEPGgJpmroJNDWNDKYNnz4UgfT2uiW-Sh6l88btu3fHYaGXFg0SV1dpICeYUp75hBtdoFGs1AXAwU3k4zrEvf3rxrOwT-QcmZ1V_13NhyzhGUkQ_5PaW75eSzIrhlYT7FjA/s0/sql-join-testdata-1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiakkegE7rqPx5ddmnnyIIgIpXcNCUxcAOnemOOu5NLXxuHXNN902OxfZlU7xQM8FPMXnNDs08NAA9jNwSbgU9VWdb-L_zH0vWq2g2BeDBPP_e9lcrWBGijAMsQx7XkWPypR9eEQepeOps/s0/sql-join-testdata-2.png)  

**範例**：
```sql
Select E1.id, E1.name, E2.age
FROM @Emp1 E1
INNER JOIN @Emp2 E2 ON E1.id=E2.id
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbyfQW0VEkpVqLChCCZJwlTgRl-c8jfxGf_fzQ8i9s8pa8WhXKQG4bUBFYhg3VnOKcw7eZXAMvWfVaCIWHLmV4kezMq_CImMDzc2mbWSg3VtlUnl60Pe1BeNjNlkikOjEXj14IYJfEJEo/s0/sql-inner-join.png)

下面語法意義相同，效能相同，但較建議使用上面寫法
```sql
Select E1.id, E1.name, E2.age
FROM @Emp1 E1, @Emp2 E2
WHERE E1.id=E2.id
```

# OUTER JOIN

- OUTER JOIN 回傳的是單一邊資料表中的資料列，以及另一邊資料表中符合 join 條件中匹配的資料列。
- OUTER JOIN 包含：[LEFT OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 、 [RIGHT OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 、 [FULL OUTER JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 。
- OUTER 關鍵字可以省略。

## LEFT OUTER JOIN

**範例**：

以下範例以 E1 為主去 JOIN E2。
```sql
Select E1.id, E1.name, E2.age
FROM @Emp1 E1
LEFT JOIN @Emp2 E2 ON E1.id=E2.id
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjj-uuSDmQB5BP5j0uqRSXleCLJjxMr_4o-W5ps3lWMPd2_euIHy24jMy0PrPZZBrmXnyw8YxAP7aob9ZQLO6mju2QgRYrudsDe6O-aa8_PjrATbUmx4Rl5FnqrJPGKKs90YSPBth7hm_I/s0/sql-left-join.png)

## RIGHT OUTER JOIN

**範例**：

以下範例以 E2 為主去 JOIN E1。
```sql
Select E2.id, E1.name, E2.age
FROM @Emp1 E1
RIGHT JOIN @Emp2 E2 ON E1.id=E2.id
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQE9v0WcIF7PwXDR6WOmjox7gBEp-OHZfSAAfFJYO07Wfjze57jeCsMbKE4wvmfttvxfwDqIrWgX6s4vIjMx2IijiBNIj4gN4v6LY_wRc57saYUDEDEAusd5fRWCIPyozhFYFiS5DdtNw/s0/sql-right-join.png)

## FULL OUTER JOIN

[FULL JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 即為 [LEFT JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 與 [RIGHT JOIN](http://msdn.microsoft.com/zh-tw/library/ms187518%28v=sql.105%29.aspx) 的聯集，它會返回左右資料表中所有的紀錄，不論是否符合 Join 條件。   有時候也可以利用它來找尋不相匹配的資料。  
```sql
Select E1.id, E1.name, E2.id, E2.age
FROM @Emp1 E1
FULL JOIN @Emp2 E2 ON E1.id=E2.id
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwNfcO2uyyjkluaML60bS1pyrpR5L-HwvfHeaDd6vaK_CM4cUovme2OlhUHxqp6tIIPxO6BJuPiJm0tKyynnEsGS232uu-IQJqVGYfq6l_x-ZVjqQ7gHLU6r8HJjp0v4kA0Nq06RvmkW8/s0/sql-full-join.png)

PS. 若二個資料表 Join 的欄位有使用 freign key 當做條件約束，那麼 Full Join 結果將等同 Left Join 。

# CROSS JOIN

交叉連接為兩個資料表間的笛卡兒乘積 (Cartesian product)，兩個資料表在 Join 時，**不能指定任何條件**，  它會回傳兩個資料表中所有欄位可能的排列組合出來，資料很可觀，要小心使用。
```sql
Select E1.id, E1.name, E2.id, E2.age
FROM @Emp1 E1
CROSS JOIN @Emp2 E2
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAC9MbQufx75VUePAJDRcl59mTS4yVXAryFg3EpgkT1M__OkodtbVOey08y7EkWIYaR01XZxoqIw73kOnfyLptXw4aQg36h2ZlZAFPD3E9Oy62yQ27GjG7dJhlSsVFW8VamNFkKGjd-uo/s0/sql-cross-join.png)

# Self Join

A self-join is when a single table is referenced more than once in the JOIN clause because it uses a different alias each time it refers to the table.  
```sql
SELECT 
E.FirstName + ' ' + E.LastName AS 'Employee Name'
, DS.FirstName + ' ' + DS.LastName AS 'Direct Supervisor'
FROM DimEmployee E 
INNER JOIN DimEmployee DS ON E.ParentEmployeeKey = DS.EmployeeKey;
```
## 參考資料  

- [使用聯結](http://msdn.microsoft.com/zh-tw/library/ms191472%28v=sql.105%29.aspx)
-