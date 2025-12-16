---
title: Views
layout: default
parent: StoredProc、Triggers、Views
nav_order: 6
description: "Views"
date: 2013-05-17
tags: [SQL,StoredProc、Triggers、Views]
postid: "7103217094779999144"
---
**檢視（VIEW）** 同時也稱為虛擬資料表，它將一連串複雜的 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式組合成一個虛擬資料表，可以用來簡化日後的查詢操作。  除了這個優點，你也可以透過 VIEW 來更新資料。此外，若搭配使用索引檢視（Indexed View）還可以用來提升查詢效能。  

# Creating a View

####  [CREATE VIEW](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) 
```sql
CREATE VIEW [ schema_name . ] view_name [ (column [ ,...n ] ) ]
[ WITH <view_attribute> [ ,...n ] ]
AS 
select_statement

[ WITH CHECK OPTION ] [ ; ]
```

#### View 的禁止事項

在 VIEW 中的 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式可以參考 Table, View, Function ，但是**不允許**以下項目：

- 無法使用 [COMPUTE](http://msdn.microsoft.com/zh-tw/library/ms181708.aspx) 或 [COMPUTE BY](http://msdn.microsoft.com/zh-tw/library/ms181708.aspx) 子句
- 無法使用 SELECT/INTO 關鍵字
- 無法使用 OPTION 子句
- 無法參考 temporary table 或 table variable
- 使用 ORDER BY 子句，除非 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式的選取清單中也有 TOP 子句
- 無法叫用 Stored Procedure
- 無法接受參數

## 參數說明

### SCHEMABINDING

在 VIEW 中參考到的實際資料表稱之為**基底資料表**，當指定 [SCHEMABINDING](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) 選項時，若異動基底資料表的 shcema 會引響到這個 VIEW 的話，則這個 VIEW 將會限制基底資料表的 shcema 修改。  

例如以下這個範例，因為使用 [SCHEMABINDING](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) ，所以你無法任意的變更 tblStudent 的結構，例如將 StudentNo 變更成 StudentID。  若要變更，必須先修改或卸除資料表的相依性。  
```sql
CREATE VIEW vw_GradeTable
WITH SCHEMABINDING 
AS
SELECT S.StudentNo, S.StudentName, C.ClassName, G.Grade
FROM dbo.tblStudent S
INNER JOIN dbo.tblGrade G ON S.StudentNo = G.StudentNo
INNER JOIN dbo.tblClass C ON C.ClassID=G.ClassID
```

**注意**

當使用 [SCHEMABINDING](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) 選項時，一定要使用 two-part name 來指定資料表名稱。也就是 ( schemaName.tableName) eg: dbo.tblStudent

### CHECK OPTION

強制執行所有針對檢視來執行的資料修改陳述式遵照 select\_statement 內所設定的準則。 當利用檢視來修改資料列時，WITH CHECK OPTION 可確保在認可修改之後，仍可以透過檢視見到資料。  
```sql
CREATE VIEW dbo.vw_Product
WITH SCHEMABINDING 
AS
SELECT ProductID,ProductName,Price
FROM dbo.Products
WHERE Price<7
WITH CHECK OPTION
```

上面這個 VIEW 使用 WITH CHECK OPTION ，用來限制新增或修改資料後，該資料都還必須在 VIEW 之中；刪除當然只能刪除 VIEW 之中的資料。

目前view中的資料
```sql
select \* from vw_Product
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCSuOkmxVA8EEcoh1e6emD34MQ7m05-ae1U4nlUFQIfWDDM9mSPEGmeh9WT6AhsaQmcn-WSwZMt6ob4unL2VaHX5npaf50FqdNHMn5Bc1rOx2hug9fduhlYUSGgXlNJcQRrvj2FJp4qN8/s257/sql-with-check-option-1.png)

執行更新，價格不能超出 WHERE 條件
```sql
update vw_Product set Price=6 where ProductID=20
update vw_Product set Price=60 where ProductID=20
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-kQqIn8KVCm7_GcHzoUUG2g7kBtDjtWsOEDEGYriokn6_RwjzlXGRc2SHBLjUL7LXF7Gv1Pq43OL5JEj6mL51JdGNYGp2S8WNpVCt3KygpUqlitQoknXtDaxmzBqCqNvVnr_-4vZvCIc/s651/sql-with-check-option-2.png)

執行新增，價格也不能超出 WHERE 條件
```sql
insert vw_Product(ProductName, Price) Values ('aaa',5)
insert vw_Product(ProductName, Price) Values ('bbb',20)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWJDgMKtcyrZx8vYyO9XTnSJwsFxhO23XCzFswLHEH3aATUpCr5LebsCGgnISj-VBlze06vDBnqsrwQgn2u7KuJwago5Kd4R9ha8miYfULYrf2XE4mbWLMj06eGfkUmB-7_8V2qhDo2mk/s675/sql-with-check-option-3.png)

## 如何避免修改基底資料表的結構後，造成檢視（View）無法使用

在 **VIEW** 中所使用到的實際資料表稱之為基底資料表（base table），SQL Server 並不會真正儲存檢視中的資料，而是儲存這些基底資料表的 metadata ，當您修改基底資料表的結構時，檢視的 metadata 並不會跟著更新，因而造成例外發生。  該如何避免這個問題，可參考以下文章。  
[http://www.dotblogs.com.tw/terrychuang/archive/2012/04/17/71581.aspx](http://www.dotblogs.com.tw/terrychuang/archive/2012/04/17/71581.aspx)

# Modifying Data Through a View

#### 刪除

如果檢視參考多個基底資料表，就不能直接透過檢視表刪除資料列。您只可以更新屬於單一基底資料表的資料行。

#### 新增

如果檢視參考多個基底資料表，就不能直接透過檢視表插入資料列。

#### 修改

支援更新的檢視表稱為"**可更新檢視表**"，要透過檢視直接修改資料，該檢視必須符合以下限制：

- 要更新位於 VIEW 中的資料行，可以明確對應到單一基底資料表中的資料行
- 不能更新 aggregation 資料行
- 不能含 UNION / UNION ALL / CROSSJOIN / EXCEPT / INTERSECT 子句。
- 不能含 DISTINCT, GROUP BY, HAVING  子句。
- 不能含 TOP 子句。

# 分割檢視（Partitioned Views）

參考 MSDN：[使用資料分割檢視](http://msdn.microsoft.com/zh-tw/library/ms190019%28v=sql.105%29.aspx)

# Creating an Indexed View

## 參考資料  

- [MSDN：設計索引檢視](http://msdn.microsoft.com/zh-tw/library/ms187864.aspx)
- [MSDN：建立索引檢視表](http://msdn.microsoft.com/zh-tw/library/ms191432.aspx)

# 決定性（Determinism）

#### 什麼是決定性（Determinism）

一個函式若是決定性函式，表示這個函式，無論何時叫用，只要給定相同輸入值，就會回傳相同的結果。  例如 SUBSTRING 、 YEAR 、 ROUND ，這些都是決定性函式。  而像 RAND 、 GETDATE 、 NEWID ，這些都是非決定性函式。  

在設計資料庫時，這個特性有時候是必須的。  例如：若我們要使用一個計算欄位(computed column)來建立索引，則這個欄位就不能由非決定性函式構成。  

參考 MSDN：[決定性與非決定性函數](http://msdn.microsoft.com/zh-tw/library/ms178091.aspx)

# Query Substitution