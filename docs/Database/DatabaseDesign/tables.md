---
title: 資料表
layout: default
parent: Database Design
nav_order: 3
description: "資料表"
date: 2013-06-07
tags: [SQL,Data Types]
postid: "4358322532961729076"
---

Table 是關連式資料庫最核心的物件，它有以下幾種不同變化：

- 永久性表格（permanent tables）
- 區域型暫時性表格（local temporary tables）
- 全域型暫時性表格（global temporary tables）
- 表格變數（table variables）

# 基本概念

底下是幾個與表格相關的觀念：

## 結構描述（Schema）

從 SQL 2005 開始，要建立表格，必須要有一個結構描述(Schema)，每個表格都必須歸屬於一個結構描述，類似程式語言中的命名空間。但是與命名空間不同的是，結構描述只會有一層，所以不會有結構描述歸屬於結構描述的狀況，預設值為 dbo 。  

在 SQL 2005 以前，並沒有結構描述，而是使用資料庫中的使用者當做表格的擁有者。  其樣式如：&lt;owner&gt;.&lt;object&gt;，而 dbo 是所有物件的擁有者。  但是從 SQL2005 關始，不再採用這個概念，而是將所有物件都設定成必須被建立在自訂的結構描述底下。  底下是建立結構描述與表格的方法  
```sql
--建立結構描述 (Schema)
CREATE SCHEMA Sales;
Go;

--建立表格
CREATE TABLE Sales.Customers (
	CustomerId INT NOT NULL,
	Name NVARCHAR(50) NOT NULL
);

--查詢資料（含結構描述）
SELECT * FROM Sales.Customers

--變更結構描述
ALTER SCHEMA dbo TRANSFER Sales.Customers　　

--Sales.Customers 已被轉移成 dbo.Customers
SELECT * FROM Customers
```

#### one-part name vs two-part name

- **one-part name**：**Product**  
只含有物件名稱，預設使用 schema=dbo, database=current, server=connection .

- **two-part name**：**Production.Product**  
預設使用 database=current, server=connection

- **three-part name**：**AdventureWorks2012.Production.Product**  
預設使用 server=connection

- **four-part name**：**[LONDONPRODSERVER].AdventureWorks2012.Production.Product**  
[LONDONPRODSERVER] 是連結伺服器（linked server）

```sql
SELECT * FROM [YOUR_SS_SERVER].AdventureWorks2012.Production.Product
```

## 識別碼（Identifiers）

資料庫物件名稱又稱為[識別碼（Identifiers）](http://msdn.microsoft.com/zh-tw/library/ms175874.aspx)，  資料庫中每個物件都具有識別碼。  所以 DB, Table, Column, View … 都有識別碼。  識別碼的命名可分成二類：  
```sql
-- 檢視所有物件
SELECT * FROM sys.objects

-- object_id 與 object_name 轉換函式
SELECT OBJECT_ID('Employees'), OBJECT_NAME(240719910) 
```

### 標準識別碼（Standard Identifiers）

當識別碼符合以下規則時，稱為一般識別碼。

1. 第一個字為：底線(\_)、符號(@)、符號(#)。
2. 第一個字為：文字字元（如字母a-z，Unicode中的標準字元也可以)
3. 識別碼不可以是數字，也不可以是補充字元（Supplementary characters）。
4. 識別碼不可以是 TSQL 保留字。
5. 識別碼不可以有空格或特殊字元。

標準識別碼在TSQL中，可以直接使用，不須要以符號分隔。
```sql
SELECT *
FROM TableX
WHERE KeyCol = 124
```

### 分隔識別碼（Delimited Identifiers）

不符合標準識別碼即稱為分隔識別碼。在TSQL中使用時，一定要以符號分隔。
```sql
SELECT *
FROM [My Table]      --Identifier contains a space and uses a reserved keyword.
WHERE [order] = 10   --Identifier is a reserved keyword.
```

## 識別欄位（Identity）

若要將欄位設定成識別欄位，其資料型態必須是整數值。  在識別規格中，可以指定一個起始值(seed)，以及遞增值(increment)。  
```sql
<col_name> <type> IDENTITY(<seed>,<increment>) NOT NULL
```
```sql
CREATE TABLE [dbo].[aaa](
[id] [int] IDENTITY(10000,1) NOT NULL,  --由 10000 開始，每次加 1
[name] [nchar](10) NOT NULL,
[col1] [int] NOT NULL,
[col2] [int] NULL
)
```

## 使用 NULL 或 NOT NULL

一般來說，能夠不用 NULL 就不要設定允許 NULL 。

如果允許 NULL 的欄位，就不要給定預設值。其意義是不同的。如下面範例所示：NULL欄位是不會列入總計函式中計算的。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqTRezv_6HRtB3S1LhKeTvWSXPP6Xold9oC_qt_KxN3ByAJHAGDVSLcafMXvI0-3u4Zi0iC56cJJFB5NWrb7Azbpx4MSuU5nEqPJf-UOAPQnuN7chVt6SUzZQRBIXhuaZQiT1fQifMmIU/s0/sql-null-avg-1.png)
```sql
select AVG(col1),AVG(col2) from aaa
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgI_QxQ7TESllRyo8dEdSs8EVGek9_b32Znz1NTP_Br5gc1sP9pbjJVDuPoTUIIWa1cMLGrhiuj0phsWrBTUZ731TaDc_Wb2CCOHISKQgZgUfFovI7XdfYRzBNEBqPPrPos3N8dZ_2TaxY/s0/sql-null-avg-2.png)

## 欄位或表格的命名規則

A few typical naming guidelines are provided here:

- Use PascalCasing
- Avoid abbreviations.
- A long name that users understand is preferred over a short name that users might not understand.

## 慎選欄位的資料型別

當設計資料表時，要如果決定欄位的資料型別，可參考以下幾點原則：

- 選用最小空間的型別。
- 選用適用性較大的型別，避免因日後程式需要又得變更型別。
- 一般狀況下，選用可變長度型別(eg. nvarchar)比選用固定長度型別(eg.char)好。
- 特殊狀況才選用固定長度型別。如：很常更新的欄位。
- 少用 datetime, smalldatetime ，因為它們使用空間比 date, time, datetime2 還要大，但精準度反而比較小。
- 使用 varchar(max), nvarchar(max), varbinary(max) 取代 text, ntext, image，因為未來版本不支援。
- 使用 rowversion 取代 timestamp ，因為未來版本不支援。
- 真正無法決定大小時才使用 varchar(max), nvarchar(max), varbinary(max), xml 型別，因為它們無法建立索引。

# 如何建立 Table

## 語法
```sql
CREATE TABLE  [ schema_name ] .table_name 
( 
{ <column_definition> }[ <table_constraint> ] [ ,...n ] 
) 
[ ON { partition_scheme_name | filegroup | "default" } ] 
[ WITH ( <table_option> [ ,...n ] ) ]
```

## 參數說明

### 使用 ON 陳述式指定儲存位置

當建立資料表的時候，可以使用 ON 陳述式來指定資料是要儲存在哪個**檔案群組**，或者那個**分割配置**。  要使用一個或多個檔案群組來儲存。  若都沒有指定，就會使用預設的檔案群組(PRIMARY)。  
```sql
--沒有指定 ON ，資料會儲存在 PRIMARY 這個預設檔案群組
CREATE TABLE table1()

--指定資料儲存在 filegroup1 這個檔案群組
CREATE TABLE table2()
ON filegroup1

--若要儲存在多個檔案群組，ON 後面接的是一個 Partition Scheme
CREATE TABLE table2()
ON MyPartitionScheme
```

如何建立 Partition Scheme 請參考 [分割資料表（Partition Table）](http://vito-note.blogspot.com/2013/05/blog-post_30.html)

## 範例

若要建立表格，必須使用 [CREATE TABLE](http://msdn.microsoft.com/zh-tw/library/ms174979.aspx) 陳述式。欄位定義至少包含三個屬性：欄位名稱, 資料型別, 是否允許Null.
```sql
CREATE TABLE Employees(
EmployeeID int IDENTITY(1,1) NOT NULL,
FirstName nvarchar(50) NOT NULL,
Merried bit NULL,
Age tinyint NULL,
Birthdate datetime NULL,
CreatedTime datetime NOT NULL
);
```

# 如何修改 Table 和 Column

[ALTER TABLE](http://msdn.microsoft.com/zh-tw/library/ms190273.aspx) 可以用來改變、加入或卸除資料行與條件約束、重新指派資料分割，或是停用或啟用條件約束與觸發程序等方式修改資料表定義。   

[sp_rename](http://msdn.microsoft.com/zh-tw/library/ms188351.aspx) 可以用來變更目前資料庫中之使用者建立物件的名稱。  這個物件可以是資料表、索引、資料行、別名資料類型或 CLR 使用者自訂類型。  

####  [ALTER TABLE](http://msdn.microsoft.com/zh-tw/library/ms190273.aspx) 語法

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiD2ATh474RcudrwiUCer_AqFmbPLtDvgPf42eWcTNXgiTT3ZjU_yjnrnnela1Aex81p7RD38plMWIzsu-mS_2zG2KWiwH7P-O0WawdeusMgTq9T823r_oPCwJFb5UeDV_Yq-bNJjEd1uo/s667/sql-alter-table.png)

### 範例一
```sql
-- 修改 Table 名稱
sp_rename 'old_table_name', 'new_table_name'

-- 新增欄位 
ALTER TABLE TableName ADD ColName DataType   

-- 刪除欄位 
ALTER TABLE TableName DROP COLUMN ColName    

-- 修改欄位資料型態 
ALTER TABLE TableName ALTER COLUMN ColName DataType   

-- 修改欄位名稱 
EXEC sp_rename 'TableName.OldColName','NewColName','COLUMN'
```

若要同時更改欄位名稱與型態，就需要分成兩道指令來進行
```sql
-- 修改欄位資料型態 
ALTER TABLE T2 ALTER COLUMN C5 smallint   

-- 修改欄位名稱 
EXEC sp_rename 'T2.C5','C6','COLUMN'
```

注意：

- 當您所欲更改欄位長度或資料型別的資料表有條件約束時，SQL Server 並不會自動幫您修改這些條件約束，此時，請先移除條件約束，待更改欄位長度或資料型別之後，再建立這些條件約束，以免於執行操作時，發生錯誤訊息。
- 重新命名欄位的物件，不會自動重新命名與這個物件相關的參考物件名稱。您必須手動修改任何參考重新命名之物件的物件。比方說，您重新命名某一個欄位，且有觸發程序參考這個欄位，您必須修改觸發程序來反映新的欄位名稱。在重新命名欄位之前，您可以使用 sys.sql\_dependencies 來列出其相依性。

### 範例二

下面這個例子，要將一個 GUID 欄位設定成不允許 NULL ，且設定預設值：

1) 如果是在Table中新增一個欄位，我們可以直接同時設定不允許NULL和預設值。
```sql
ALTER TABLE Products
ADD ProductGuid uniqueidentifier NOT NULL
CONSTRAINT [DF_ProductGuid]  DEFAULT (newid())
```

2) 如果是在Table中更改欄位成不允許NULL，則無法同時設定預設值，必須分成三個步驟。
```sql
ALTER TABLE Products 
ADD CONSTRAINT [DF_ProductGuid] 
DEFAULT (newid()) FOR ProductGuid

UPDATE Products SET ProductGuid=newid() WHERE ProductGuid is NULL

ALTER TABLE Products 
ALTER COLUMN ProductGuid uniqueidentifier NOT NULL
```

# 資料壓縮（Data Compression）

SQL Server 從 SQL 2008 的 Enterprise 版本起，開始提供表格(tables)或索引(indexes)的壓縮功能，  資料壓縮可分成二個層級：[資料列壓縮](http://technet.microsoft.com/zh-tw/library/cc280576.aspx)和[頁面壓縮](http://technet.microsoft.com/zh-tw/library/cc280464.aspx)。  

## 資料列壓縮（Row Compression）

當啟用[資料列壓縮](http://technet.microsoft.com/zh-tw/library/cc280576.aspx)時，SQL Server只會變更資料類型的實體儲存格式(physical storage format)。  啟用列壓縮會將所有資料型別轉換成可變長度的資料型別(variable-length data type)，而且不使用空間儲存 Null 值。  這些變更和語法或語意都不相關，所以在應用程式的使用上也無須要做任何改變。

## 頁面壓縮（Page Compression）

[頁面壓縮](http://technet.microsoft.com/zh-tw/library/cc280464.aspx)是由下列順序的三種作業組成：   

1. 資料列壓縮
2. 前置詞壓縮
3. 字典壓縮

詳細內容請看 [MSDN:資料壓縮](http://technet.microsoft.com/zh-tw/library/cc280449.aspx)

# 暫存資料表（Temporary Tables）與表格變數 (Table variables)

## 暫存資料表（Temporary Tables）

暫存資料表必須由開發者自行建立，待工作工作階段(session)結束，會由系統自動刪除。暫存資料表一律存放於系統資料庫 tempdb 中  

- 當 session 關閉時, 這個table 將會自動drop
- 好的寫作習慣, 應在暫存表使用完畢後, 下指令去 drop, 而不是讓系統自動回收。
- 暫存表是存在主機記憶體中, 因此存取速度較快
- 暫存表的名稱不能超出 116 個字元，因為系統要針對不同使用者套用後置詞以組成不同的表格名稱。

於 Microsoft® SQL Server™中，暫存資料表可區分為下列兩種類型：

### 區域性暫存資料表（Local temporary tables）

- TableName 前加１個"#"表示是區域暫存資料表，如： #T1 。
- 區域性暫存資料表僅限同一個工作階段(session)使用。(指的就是同一個連線階段)。
- 當使用者切斷與 SQL Server 的連線，區域性暫存資料表會自動被刪除。
- 區域性暫存資料表的適用範圍僅限於建立它的連線，因此無需擔心其名稱會與其他連線所採用的名稱相同。
- 若 stored procedure A 建立了一個區域性暫存表, 並呼叫 stored procedure B ，則在 B 中可以存取這個區域性暫存表
```sql
CREATE TABLE #City (
CityID int NOT NULL,
CityName varchar(100)
)
```

### 全域性暫存資料表（Global temporary tables）

- TableName 前加２個"#"表示是區域暫存資料表，如： ##T1
- 全域性暫存資料表一旦被建立，後續連線至此 SQL Server 的任何使用者都能夠存取它（包括讀取與寫入），而且不需要特定的權限。
- 當建立全域性暫存資料表的使用者切斷與 SQL Server 的連線，SQL Server 會檢查是否有其他使用者正在使用此全域性暫存資料表，如果沒有，便立即將全域性暫存資料表刪除。
- 如果有，SQL Server 會讓這些正在存取中的作業繼續進行，但是不允許任何使用者再去存取全域性暫存資料表，等到所有未完成的作業執行完畢後，全域性暫存資料表會自動被刪除。
- 由於全域性暫存資料表能夠被所有的連線使用，因此您必須注意其名稱不能與其他連線所採用的名稱相同。
```sql
CREATE TABLE ##City (
CityID int NOT NULL,
CityName varchar(100)
)
```

你可以使用以下語法查詢暫存資料表
```sql
SELECT * FROM #City
```

你無法在 [sys.objects](http://msdn.microsoft.com/zh-tw/library/ms190324.aspx) 中，以暫存資料表名稱找到該物件。
```sql
SELECT * FROM sys.objects where name='#City'
```

因為實際的暫存資料表名稱為：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwz6AWRg6N-wlnnVOGcjvuFkBcMmXAkDepcsc23GG0Pf91mC7y6vu1hBescmTXgsw52XNCq2_euLv83NdUEmxt6PfLPZ8ImbB_B1WaFmYLxvYy1lQ2hR-Xt752du9fHzLW1wACRCCQRAE/s0/sql-temp-table.png)

若要判斷暫存表是否存在可使用內建函式 [OBJECT_ID](http://msdn.microsoft.com/zh-tw/library/ms190328.aspx) 來判斷：
```sql
IF object_id('tempdb..#City') IS NOT NULL 
BEGIN
print '#City is exist'
END
```

## 表格變數 (Table variables)

#### 表格變數：

- TableName 前加一個"@"表示是表格變數，如： @T1 。
- 它和暫存表類似，但它更加彈性，且不會存在於 tempdb 中(完全存在於記憶體)。
- 使用完畢後，不須手動去 drop 它。
```sql
DECLARE @City TABLE 
( 
CityID int NOT NULL,
CityName varchar(100)
)
```

#### 表格變數與暫存資料表的使用時機比較：

- 當暫存的資料筆數小於100筆時，使用表格變數。因為 SQL Server 不會去解析最佳化表格變數的效能。
- 如果資料量較大，對好對表格建立索引(Index)，這時就必須使用暫存資料表。