---
title: Output 與 Merge
layout: default
parent: SQL
nav_order: 2
description: "Output 與 Merge"
date: 2013-05-02
tags: [SQL,Modify]
postid: "3028487986125734638"
---
The [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) clause allows you to return information from rows affected by an INSERT, UPDATE, or [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) statement.   With this functionality, you can perform additional tasks more cleanly based on the information provided.   These tasks can include confirmation e-mails, data auditing, and similar duties.  

The [MERGE](http://msdn.microsoft.com/zh-tw/library/bb510625.aspx) statement provides you [WITH](http://msdn.microsoft.com/zh-tw/library/ms175972.aspx) the ability to perform an INSERT, UPDATE, or [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) operation on a target table   based on a set of rules that are determined by a row comparison between the target table and a source table.  

# OUTPUT 子句

學習本章節，必須先瞭解什麼是 **deleted** 和 **inserted** 系統資料表：

- deleted 資料表中會儲存被 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 及 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 陳述式所影響的資料副本。
- inserted 資料表中會儲存被 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 及 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 陳述式所影響的資料副本。

Update 交易就像是先做刪除後再做插入的動作；所以，舊的資料列會先複製到 deleted 資料表，接著將新資料列再複製到觸發程序資料表以及 inserted 資料表。  在先前的 SQL 2005 版本中，要讀取這個二個特殊資料表中的資料只能在 triggers 中，現在透過 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 子句就可以直接由 SP 或 [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 來讀取。  

所以 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 子句就是用來取得 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 、 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 、 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 或 [MERGE](http://msdn.microsoft.com/zh-tw/library/bb510625.aspx) 陳述式對資料列影響後的相關訊息。  因為被這些陳述式影響的資料列副本會儲存在 **inserted** 和 **deleted** 這二個特殊資料表，  而 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 子句正是是用來讀取 **inserted** 和 **deleted** 這二個特殊資料表用的。    

## 搭配 INSERT 陳述式來使用 OUTPUT INTO

下面這個例子中，Products 包含一個自動編號欄位和一個預設值為 getdate() 時間欄位。  我們在 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 子句中透過 inserted 資料取得新增 Products 資料時的 ProductID 和 CreatedTime。
```sql
CREATE TABLE [dbo].[Products](
[ProductID] [int] IDENTITY(1,1) NOT NULL,
[ProductName] [nvarchar](255) NOT NULL,
[Price] [money] NULL,
[CreatedTime] [datetime] NOT NULL
)

CREATE TABLE [dbo].[Audit](
[AuditID] [int] IDENTITY(1,1) NOT NULL,
[TableName] [nvarchar](50) NOT NULL,
[CRUD] [char](1) NOT NULL,
[RunTime] [datetime] NOT NULL,
[Key1] [nvarchar](50) NULL,
[Key2] [nvarchar](50) NULL,
[Key3] [nvarchar](50) NULL,
)
```

下面這個範例，示範如何使用 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 來記錄新增異動記錄。
```sql
--原始 INSERT 語法
INSERT Products (ProductName, Price) 
Values ('apple', 50)

--加入了 OUTPUT 以便讀取 id 這個自動編號欄位
INSERT Products (ProductName, Price) 
OUTPUT 'Products', 'C', inserted.createdtime, inserted.ProductID
INTO Audit(TableName, CRUD, RunTime, Key1)
Values ('apple', 50)

--一次多筆也可以
INSERT Products (ProductName, Price) 
OUTPUT 'Products', 'C', inserted.createdtime, inserted.ProductID
INTO Audit(TableName, CRUD, RunTime, Key1)
Values ('book', '30'),  ('cherry', '100')

select \* from Products
select \* from Audit
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_Ao8ZSGFD7XJ26jL3veDQ40KwmzztQWiponk1X9NcyfOL23dCop4YhID12EmRxO_iZxvxHWlPUS3RW8w8hfppvgJyiiDdxcZ3fqrlxmLzhNICyO_NV02hpPN7HbgXurEveYVQTB39C3A/s0/sql-output-1.png)

## 搭配 UPDATE 陳述式使用 OUTPUT INTO

若使用 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) ，你可以由 deleted 資料表取得舊資料列，由 inserted 資料表取得新資料列。  

下面這個範例，使用 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 來調價，並且將更新前與更新後的價格記錄下來。
```sql
UPDATE Products SET Price = Price \* 1.05
OUTPUT 'Products', 'U', inserted.createdtime, inserted.ProductID, deleted.Price, inserted.Price
INTO Audit(TableName, CRUD, RunTime, Key1, key2, key3)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEIudXQv7GsTtumlfIOEH74TNRsRzy7vTXW_0XdPBN04cO8SYX0d4_UIWjuG7WJEHcOwFId9yu5tjC_p6zEJVOBGd2vAjIkypTR55fvwNHTXEWiyY37p9kDYktULHqN6NgYirIo0oUd7E/s0/sql-output-2.png)

## 搭配 DELETE 陳述式使用 OUTPUT INTO
```sql
DELETE Products 
OUTPUT 'Products', 'D', deleted.createdtime, deleted.ProductID, deleted.Price
INTO Audit(TableName, CRUD, RunTime, Key1, key2)
WHERE Price >50
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVCiu1OCRdQeyHFDmTjxMeHEfTR9Onu1oTBOli0B0pU_IZM9dvcioQBQlIJbpi6C5Ni_I-FrrFpMjEY_RagxJS3f2U7pn9dlkVYC3JBFr1255_O9UEJ6ftnW-E02C3nq2e6wfdIysn66s/s0/sql-output-3.png)

假設你有一個資料表用來記載每日交易，若你想將去年的資料搬移至另一個資料表備份，  通常我們會先執行複製指令再執行刪除指令。  現在，你也可以利用 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) ，以一個指令就可以完成以下操作。  
```sql
DELETE TableB
Output deleted.\* INTO TableC
WHERE id>2
```

# MERGE 陳述式

這個 [MERGE](http://msdn.microsoft.com/zh-tw/library/bb510625.aspx) 陳述式是 SQL 2008 的新功能。  它的主要功用是，依據來源資料中的資料，在目標資料表上執行插入、更新或刪除作業。  

## 使用單一的 MERGE 子句執行 INSERT 和 UPDATE 作業

### 案例一：取代 exists ... update ... insert ...

底下這個例子是常見的案例，判斷某個欄位值是否存在，再決定使用 Insert 或 Update 作業。  
```sql
IF (exists(SELECT ProductID FROM Products WHERE ProductName='apple'))
BEGIN
Update Products Set Price=40 WHERE ProductName='apple'
END
ELSE
BEGIN
Insert Products(ProductName,Price) values ('apple', 50)
END
```

現在可改使用 [MERGE](http://msdn.microsoft.com/zh-tw/library/bb510625.aspx) 子句，一個 SQL 就可以解決，如下：  
```sql
MERGE INTO Products AS target
USING(
Select 'apple',40
) As source (ProductName, Price)
ON (target.ProductName = source.ProductName)

WHEN MATCHED THEN 
UPDATE SET Price = source.Price

WHEN NOT MATCHED [BY Target] THEN
INSERT (ProductName, Price)
VALUES (source.ProductName, source.Price)
OUTPUT deleted.\*, $action, inserted.\*;

--注意，此時 INSERT 後面不用加 Table Name 了
```

若有多筆資料，也可如下使用：  
```sql
MERGE INTO Products AS target
USING(
values ('apple','40'), ('book','40'), ('cherry','40')   
) As source (ProductName, Price)
ON (target.ProductName = source.ProductName)
WHEN MATCHED THEN 
UPDATE SET Price = source.Price
WHEN NOT MATCHED THEN    
INSERT (ProductName, Price)
VALUES (source.ProductName, source.Price)
OUTPUT deleted.\*, $action, inserted.\*;

--$action 會回傳該筆資料是何種操作：如 Insert 或 Update
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUBlWnTDQna0lJtDbC1AvkwI9xRlwk_bDjMgKdG9_kMO6ZsI9nirb5q4RaxhB9tZYRcxrj_Xh39zT6jK278U20KyPNCOQ7g8MDed0LIVXYEf0toLJZBJ19lW4kxI7YR7VqQcmgccpWcgs/s0/sql-merge-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBgxVDfZpEmLblTsXlHWIS1tH2uk7Yh4taAKO7X5rre9zAAwZJke3bKR_6k5S-UDQI8eclLmh39H38tPj3Q-myaO5pLNd6QZAcNFqs1AaQi8eyW_1KiaOGlBqp_4tFQth2u_F_u84Aw8A/s0/sql-merge-2.png)

### 案例二：同步備份資料

假設有個員工備份檔存有較舊的員工檔資料，如何將員工備份檔和現有的員工檔同步呢？  
```sql
MERGE INTO EmployeeBackup AS target

USING(
Select \* From Employee
) As source

ON (target.id = source.id)

WHEN MATCHED THEN 
UPDATE SET field1 = source.field1
,field2 = source.field2
,createdtime = source.createdtime

WHEN NOT MATCHED THEN    
INSERT (id, field1, field2, createdtime)
VALUES (source.id, source.field1, source.field2, source.createdtime)
OUTPUT $action, Inserted.\*, Deleted.\* ;
```

## 使用單一的 MERGE 子句執行 UPDATE 和 DELETE 作業

ProductInventory 是產品庫存量的資料表，假設有個批次作業會依據每日的交易資訊更新這個資料表的庫存量值；如果庫存量小於零，並同時刪除該筆資料。  
```sql
MERGE ProductInventory AS target
USING (
SELECT ProductID, SUM(OrderQty) 
FROM OrderDetail AS od
JOIN Orders AS o
ON o.OrderID = od.OrderID AND o.OrderDate = @OrderDate
GROUP BY ProductID
) AS source (ProductID, OrderQty)
ON (target.ProductID = source.ProductID)
WHEN MATCHED AND target.Quantity - source.OrderQty <= 0 THEN 
DELETE
WHEN MATCHED THEN 
UPDATE SET target.Quantity = target.Quantity - source.OrderQty, 
target.ModifiedDate = GETDATE()

OUTPUT $action, Inserted.ProductID, Inserted.Quantity, Inserted.ModifiedDate, 
Deleted.ProductID, Deleted.Quantity, Deleted.ModifiedDate;
```