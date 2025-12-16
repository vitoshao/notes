---
title: 資料完整性
layout: default
parent: Database Design
nav_order: 1
description: "資料完整性"
date: 2013-05-03
tags: [SQL,Data Types]
postid: "3661235916778803834"
---

資料的完整性指的就是資料的正確性，通常我們都會在程式端撰寫程式驗證資料，較少在資料庫端驗證，這個文章就是要來介紹如何設計資料的驗證。

在 SQL Server 中，要確保資料的正確性，可分成二類方法：使用宣告(declarative)或者使用程序(procedural)。  

使用宣告方式指的是定義套用到資料上的驗證規則，這些規則又稱為條件約束(constraints)。

使用程序方式指的是透過 stored procedure 或者 trigger 來驗證資料。

#### 資料完整性

談到資料完整性，有以下幾個觀念：

- 實體完整性（Entity Integrity, EI）：透過最底層級的索引物件來限制資料的完整性，如使用 PK, Unique 條件約束來限制資料的完整性。
- 網域完整性（Domain Integrity, DI）：透過 CHECK 條件約束來限制資料的完整性。
- 參考完整性（Referential Integrity, RI）：透過 FOREIGN KEY 條件約束來限制資料的完整性。

# Implementing Declarative Data Integrity

使用宣告方式是最簡單用來確保資料完整性的方法，共有五種條件約束的方法：

- Primart Key
- Unique Key
- Foreign Key
- Check
- Default

## Primary Key and Unique Constraints

- PK, UC 都可以由單個欄位或數個欄位組合成的索引鍵
- PK, UC 都不允許欄位值重複
- PK　不許允有 Null 欄位值
- 索引鍵不可以超過 16 個欄位，或者超過 900 bytes 的資料
- 建立 PK 時，系統預設會使用叢集索引（clustered index）。
- 建立 UC 時，系統預設會非叢集索引（non-clustered index）。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUUvwHe_mVDE1-CmTNg-M3C552-NAUAar45vAoMfABlK3oOn8AP41Feazws-POvlLcZUSq1HyjsuCGcPwdK-xNFIJUf5zMkTHJOLH5S9pAgUG84lPV5PgBJRGb1eBw_uJhEx54f8JO4vo/s0/sql-pk-maxsize-900.png)

#### 在 [CREATE TABLE](http://msdn.microsoft.com/zh-tw/library/ms174979.aspx) 中，同時建立 PK
```sql
CREATE TABLE Employees2(
EmployeeID int IDENTITY(1,1) NOT NULL,
FirstName nvarchar(50) NOT NULL,
LastName nvarchar(50) NULL,
CONSTRAINT PK_Employees2 PRIMARY KEY CLUSTERED 
(
EmployeeID ASC
)
)
```

#### 使用 [ALTER TABLE](http://msdn.microsoft.com/zh-tw/library/ms190273.aspx) 建立 PK
```sql
--加入PK
ALTER TABLE Employees3
ADD PRIMARY KEY (FirstName, LastName);

--加入具名的PK
ALTER TABLE Employees3
ADD CONSTRAINT PK_Employees3 PRIMARY KEY (FirstName, LastName)

--刪除PK
ALTER TABLE customer 
DROP CONSTRAINT PK_Employees3;
```

#### 建立唯一索引 Unique Key
```sql
--預設會使用非叢集唯一索引建立
CREATE UNIQUE INDEX [IX_Emp_EmpName] ON Emp
(
EmpName ASC
)

--建立叢集唯一索引
CREATE UNIQUE CLUSTERED INDEX [IX_Emp_EmpName] ON Emp
(
EmpName ASC
)

--刪除唯一索引
DROP INDEX [IX_Emp_EmpName] ON Emp;
```

## Foreign Key Constraints

Foreign key constraints identify a column or combination of columns whose values must exist in another column or combination of columns in the same table or another table in the same database.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiY2PL5i__v3m6w_AUZUGR5wkScN6U-maBfVrEhOQBWYZXyeHZcR2knthafB6kgRmjtJWJFXRVJt_7zbisZo8LPq5XryL-11Hzce_BNwDsjZ_MtcXAlBPcgog0SKv3Xig29flhfP8Q1bWQ/s0/sql-pk-fk.png)

#### 要設計 Foreign Key 必須依循以下規則

- 參考與被參考的欄位必須相同型別（包含定序）。
- 被參考的欄位必須是唯一索引欄位。

#### 查詢 Foreign Key 的方法

[sys.foreign_keys](http://msdn.microsoft.com/zh-tw/library/ms189807.aspx) 系統資料表包含了每個 FOREIGN KEY 條件約束
```sql
select \* from sys.foreign_keys where object_id=544720993
select \* from sys.foreign_keys where name='FK_OrderDetails_Orders'
select \* from sys.foreign_key_columns where constraint_object_id=544720993
select \* from sys.foreign_keys where object_name(object_id)='FK_Orders_Customers'

SELECT name, type, 
is_disabled, is_not_trusted,
is_not_for_replication, delete_referential_action, update_referential_action
FROM SYS.FOREIGN_KEYS
WHERE name = 'FK_OrderDetails_Orders'
```

#### 當 Foreign Key 的參考欄位發生異動時

當 FK 的參考欄位發生異動時， FK 欄位有以下四種行為可以撰擇：  

- **NO ACTION**：引發錯誤，系統 Rollback。 (the default)
- **SET NULL**：將 FK 欄位值變成 Null。不會引發錯誤，系統也不會 Rollback。
- **SET DEFAULT**：將 FK 欄位值變成預設值。不會引發錯誤，系統也不會 Rollback。
- **CASCADE**：重疊顯示。也就是一同刪除或一同更新。

#### 建立 FK 的語法
```sql
CREATE TABLE Orders(
OrderID int IDENTITY(1,1) NOT NULL,
CONSTRAINT PK_Orders PRIMARY KEY CLUSTERED( OrderID ASC )
)

-- 使用 Create Table 建立 FK
CREATE TABLE OrderDetails(
OrderID int NULL,
PorductID int NULL
REFERENCES Orders (OrderID)
ON DELETE SET NULL
ON UPDATE CASCADE
)

-- 使用 Alter Table 建立 FK
CREATE TABLE OrderDetails(
OrderID int NULL,
PorductID int NULL
)
ALTER TABLE OrderDetails  
WITH CHECK ADD CONSTRAINT FK_OrderDetails_Orders 
FOREIGN KEY(OrderID)
REFERENCES Orders (OrderID)
ON UPDATE NO ACTION
ON DELETE CASCADE;
```

#### 停用與啟用 FK 的語法
```sql
--停用
ALTER TABLE OrderDetails NOCHECK CONSTRAINT FK_OrderDetails_Orders

--啟用
ALTER TABLE OrderDetails CHECK CONSTRAINT FK_OrderDetails_Orders
```

#### 啟用 FK 時，是否對現有資料進行檢查

當啟用 FK 時，可以針對現有資料設定是否要套用檢查規則。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCqRTc1j314YMz15YVLekpet_yLLpOZaYpDUeF2lyO_Omv3QISKOOs0reksibIgyMfNa-XL0Sr237ZRZ5x8miz2D6pTqWIARZv1zAcbszMU26jKUbMn553J9PbQcC_x0zh2kB7SWI6MX8/s850/sql-fk-settings.png)
```sql
--啟用FK條件約束，但在啟用時不對現有資料進行檢查
ALTER TABLE OrderDetails WITH NOCHECK CHECK CONSTRAINT FK_OrderDetails_Orders

--啟用FK條件約束，並且在啟用時針對現有資料進行檢查
ALTER TABLE OrderDetails WITH CHECK CHECK CONSTRAINT FK_OrderDetails_Orders       

--停用FK條件約束，也不對現有資料進行檢查
ALTER TABLE OrderDetails WITH NOCHECK NOCHECK CONSTRAINT FK_OrderDetails_Orders     
```

#### 啟用 Table 的所有條件約束
```sql
ALTER TABLE OrderDetails CHECK CONSTRAINT ALL
```

#### 啟用 Table 的所有條件約束
```sql
ALTER TABLE OrderDetails CHECK CONSTRAINT ALL
ALTER TABLE OrderDetails WITH CHECK CHECK CONSTRAINT ALL  --若要同時對現有資料進行檢查  ```

#### 針對整個DB進行條約約束的檢查

不管條約約束是否有啟用，使用以下語法，可以找出所有不符合條件約束的資料
```sql
ALTER TABLE OrderDetails CHECK CONSTRAINT ALL
```

## Check Constraints

CHECK 條件約束，可以用來限制欄位可接受的值，藉此強制值域(domain)的完整性。

#### 建立 CHECK 條件約束
```sql
--不具名
ALTER TABLE OrderDetails
ADD CHECK( Quantity >0 );

--具名
ALTER TABLE OrderDetails
ADD CONSTRAINT CK_OrderDetail_Qty CHECK  ( Quantity>0 )
```

#### 使用 UI 建立 CHECK 條件約束

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMZZ43nxn3N9kekON2BeA0_asmp49nh_HBX09kJ-Bmzk8WxzxK2eZiNpzEcxp2tjLhoTBXyBEZ48LEGS_3sC3ZzJiBJk_tuNc43sPsa4sZPNwYPhrbogDoCzi-rxUmTQfKT6SUuEoFLpE/s407/sql-check-constraints-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYi9uB5IJZl9xr8CmT8bUu3ndoopike1TcIUZZMn6j2EMueZ8SB4hN1tThcz-Xz900bQAtX9R85dkhqh0PjbtiZp1iYf9mKsvRhnGXAzz8mWdNlz4ri4hqlXgEKTopMHlQNW199Q6kYPg/s676/sql-check-constraints-2.png)

## Default Constraints

Default 條件約束，可以用來設定欄位的預設值，底下示範如何建立 Default 條件約束：
```sql
--新增欄位，同時包含 Default 條件約束
ALTER TABLE Products
ADD ProductGuid uniqueidentifier NOT NULL
CONSTRAINT [DF_ProductGuid]  DEFAULT (newid())

--將現有的欄位，加入 Default 條件約束
ALTER TABLE Products 
ADD 
CONSTRAINT [DF_ProductGuid] DEFAULT (newid()) FOR ProductGuid
```

常見的預設值函式：

- [NEWID](http://msdn.microsoft.com/zh-tw/library/ms190348.aspx) ：回傳 UniqueIdentifier 型別
- [GETDATE](http://technet.microsoft.com/zh-tw/library/ms188383.aspx) ：回傳 DateTime 型別
- [SYSDATETIME](http://technet.microsoft.com/zh-tw/library/bb630353.aspx) ：回傳 DateTime2 型別
- [SYSDATETIMEOFFSET](http://technet.microsoft.com/zh-tw/library/bb677334.aspx) ：回傳 DatetimeOffset 型別
- [NEXT VALUE FOR](http://msdn.microsoft.com/zh-tw/library/ff878370.aspx) [MySeqNo] ：使用順序物件以取得預設值。
- UDF ：使用自訂函式以取得預設值

#### 修改 DEFAULT 定義

若要修改 DEFAULT 定義，您必須先刪除現有的 DEFAULT 定義，然後使用新的定義重新建立它。

# Check Constraints with User-Defined Functions (UDFs)

## Using a UDF with a SubQuery

## Using a Foreign Key with a SubQuery

# Check 和 ForeignKey 對查詢效能的引響

當我們使用 Check 或 ForeignKey 當作欄位的條件約束，那麼這會引響執行效能嗎？  書上的回答是:“Yes, they can,” and “No, they don't.”  

底下這個例子，我們使用不同的條件約束，來看看查詢結果回傳執行計畫。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpfik9UGS-pBXTK4E-lZeMwuMlUreLN00wTEttyqgK2cqGn3AYGRg6BZTY4LJb_QgufLjuGqP7WARrxqZYIIQiE_2ju6N4iPGgZqcwvswG6k80FVZbmdloOV89MQ92HdOC_1S8IfG55rs/s0/sql-contraint-test.png)
```sql
SELECT O.\* FROM Orders O
WHERE EXISTS (SELECT C.\* FROM Customers C WHERE C.CustomerId=O.CustomerId)
```

| 名稱 | case1 | case2 | case3 | case4 |
| --- | --- | --- | --- | --- |
| Catalog |  | PK | PK |  |
| Document | PK | PK | PK+FK |  |
|  |  |

#### Case1

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjli9zC85eb4B_B9mJ1qLR8IqgeEXpLzV-zR0Dc7dePsnn06H5ukaDcKQ0vAHFpauqxgZ4U-6I0GHh9-06G9PrhUMrAKdYmtnPZbQLxjq10YhycHlnDBUgBYIvdfnUxAm59re9kZ8cmlU/s0/sql-index-case1.png)

#### Case2

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5Efg3zY_QW4e62MfqdZjBr7DRwPSWsfQdYGUeYxBzIZT0iBOuwHK2zarQVDtfzrUOEQPqKD2VcmS25VaigJ7odmBvCtYVw696yYwIYOviLNGdnZnzzy1VWbyEHc2_u6bSk-1At2zT3U4/s0/sql-index-case2.png)

#### Case3：使用 FK

在這個例子中的 Fk ，其屬性設定如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhR1xktOU-wD7t3jDqX7dJ2tTG_SXW828jjrr7pXiyxmx-7-kW-XZJ40Hh4pQUczTt2VNfN5CUZKJDoTR5QSa-CgISjHqo_bsQc6YcJlEAPDE7gn-NTppCroOirzQ-PMgBZzPSc9RilJ_8/s0/sql-contraint-test2.png)

從下圖的結果可以看的出來，資料庫引擎沒有對 Customer 執行讀取計畫。  這是因為該條件約束的設定讓資料庫引擎知道無需執行 Exists 這部份的查詢，就可以保證 Exists 的回傳值一定是 True。  因為 is\_not\_trusted=0 表示客戶編號一定存在 Customer 資料表中。  （注意此時的 Orders 的 CustomerID 欄位為非Null欄位，若為允許 Null ，結果又不一樣了）  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwZKrR6UYTaE-X68Bjc6ObttC0qI9-N_CNkjoLKsEgQJZe1UtQoT_7f9RR1zNP0lCm6sGykqQHZLVdeK_DxW1Jj5fziE3H_lAeC2WhmokbjRxJ7UgxlS8i-60H6vXLXi5XBfiqllkFlIY/s850/sql-index-case3.png)

#### Case4：使用 FK + WITH NoChecked

若 Fk 的屬性設定如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJXz1p1rJsUsVE87zYrQJpu5CYT85kt4syrpFykzzfqqj57JGibtHDkN-PMQwOaOmRUeHhUV6H0V-LsDNwFNJnQhXPgkwH8_dKZFc1mUZsSYYnNxSPy6TcIctt810yukiO7xM9tY1AAEI/s0/sql-contraint-test3.png)

此時因為資料庫引擎不確定客戶編號是否存在 Customer 資料表中，所以就必須執行 Exists 這部份的查詢。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxkBSQ3derDZCBvQkcPrFrazXMbZeB2PuG4QKuFj7EqeVFo2UGcMy3hyqcAiEWLhGpK-MmZak1UdCs2n8F5A7qKP1v4p97gZD5Ge71SI8SPZpeM2khsw6IKCQr3pddmnql3irFTImK_yg/s850/sql-index-case4.png)

#### 在SSMS中查詢時使用執行計畫

- Ctrl-M ： 回傳的查詢結果包含執行計畫。
- Ctrl-L ： 只回傳執行計畫。

## 參考資料  

- [資料完整性基本概念](http://msdn.microsoft.com/zh-tw/library/ms191447.aspx)
- [PRIMARY KEY 條件約束](http://msdn.microsoft.com/zh-tw/library/ms191236.aspx)
- [UNIQUE 條件約束](http://msdn.microsoft.com/zh-tw/library/ms191166.aspx)
- [FOREIGN KEY 條件約束](http://msdn.microsoft.com/zh-tw/library/ms175464.aspx)
- [CHECK 條件約束](http://msdn.microsoft.com/zh-tw/library/ms188258.aspx)
- [DEFAULT 定義](http://msdn.microsoft.com/zh-tw/library/ms189909%28v=sql.105%29.aspx)
- [停用條件約束和觸發程序](http://msdn.microsoft.com/zh-tw/magazine/cc163442.aspx)