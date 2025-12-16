---
title: 使用同義字
layout: default
parent: SQL
nav_order: 9
description: "使用同義字"
date: 2013-06-20
tags: [SQL,Query Advance]
postid: "2170601350020745586"
---
SQL Server 提供 Multipart Names 來表示物件，您可以利用下列任何一種方式來存取資料庫物件：

| one-part： | object\_name |
| --- | --- |
| two-part： | [schema\_name].object\_name |
| three-part： | [database\_name].[schema\_name].object\_name |
| fout-part： | server\_name .[database\_name].[schema\_name].object\_name |

若是不想使用這麼長的名字，就可以使用同義字(SYNONYM)來為資料庫物件建立別名。

- [CREATE SYNONYM](http://msdn.microsoft.com/zh-tw/library/ms177544.aspx) ：建立新的同義字。

#### 同義字是具有下列用途的資料庫物件：

- 對在本機或遠端伺服器上的另一個資料庫物件 (稱為基底物件) 提供別名。
- 提供抽象層來保護用戶端應用程式，避免變更基底物件的名稱或位置。

**範例**：
```sql
--使用 MultiPart 表示法來查詢TABLE
SELECT \* FROM AdventureWorks2012.Production.Product

--定義同義字
CREATE SYNONYM MyProduct
FOR AdventureWorks2012.Production.Product

--使用同義字來查詢TABLE
SELECT \* FROM MyProduct

--使用同義字來更新TABLE
UPDATE MyProduct SET Price=35 WHERE ProductID=2
```

已建立的同義字會列在下圖中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhILidtWrqltxUFu1Tj0zcbU-3JI4SfKMj92ukr9FaGFJHT9WTZGjRw-vQ80ZAiozb6Ac_G_tLL9aZxt4-4zepruf5wEsTalGm9qDiwof4HvnrBTNd8h9uzHR5FO1IzQOD4cj1VdRgL-Pc/s205/sql-synonym.png)

#### 查詢與刪除同義字：
```sql
--查詢同義字
SELECT \* FROM sys.synonyms 
--刪除同義字
DROP SYNONYM MyProduct
```

#### 變更實體物件名稱對同義字的影響

若將上例中的 Product 變更成 Products ，則會導至無效的連結。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdoZ26TAWGRKzX_-xMcjbV0b355gj5JPYBhz6wblaTOpcd2pUSQx2EtH3pcUPpyPwPEgZ7V8Mjyzgu-LafV3B-5tNL4wlL5L27De35ik4FdP7BSkAOE6FHyqXcOwjpSZyu2RNm6vH1trA/s264/sql-synonym-2.png)