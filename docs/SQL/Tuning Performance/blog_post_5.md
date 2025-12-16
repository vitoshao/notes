---
title: 效能調校(2)-分析執行計畫
layout: default
parent: Tuning Performance
nav_order: 5
description: "效能調校(2)-分析執行計畫"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "2908646515459742297"
---
當一道TSQL，由用戶端送出，一直到伺服器端執行完畢，這中間可能包含了很多過程。  不過簡單來看大至包含以下三個步驟：  

1. Parse：先檢查語法，再建立 processor tree（定義logical steps）。
2. Optimize：使用「Query Optimizer」取得資料的統計資訊（如多少筆資料，有多少唯一的資料，需要多少resources, CPU & I/O等等）。  「Query Optimizer」會依這些資訊建立很多的 plan，然後選擇最好的plan。
3. Execute：最後就是依「Query Optimizer」送過來的 plan 去執行。

而圖形化的執行計畫是 SQL Server 提供給開發人員或 DBA 用來分析查詢執行成本的工具，以做為 T-SQL 指令碼效能調校的參考。

# 啟動圖形化的執行計畫

要啟動執行計畫，可以由以下三個地方開啟

### 由選單中開啟

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWNevC1E4NpwL-hWnWQwxZYhr8_T-XyiFWdj2ludXzHH7NwScbv4Tz4J-WQR7OjoZkNsmJMFZgRmNKLUvB8hUAGqyl0HOPAHc8DAZ6JQqfSZxVhbg0pNjpiIYWdocBy2EkvSwS6uzrUdc/s0/sql-include-workplan.png)

### 由工具列中開啟

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikL3pGQrDOVk-i6jZhCSPoU_reJ4vIoR73Ftw08YN3cS6pT5sGkA8CXeeTEbJ72eTEgBBLtu3D0BMaZUAuSEhAgSfTf_G2jKYxhyphenhyphenJeGa71Iln9Vmrn8SnTOkwNRZcr9l70ZixEvvprHZU/s0/sql-toolbar-workplan.png)

### 使用快捷鍵

或者直接執行 Ctrl+L 。

# 移除執行計畫快取

由於執行計畫可能被快取，所以有時候後來執行的TSQL會使用到前面的執行計畫，若要確保使用新的執行計畫，可以透過以下語法，從計畫快取移除所有元素
```sql
DBCC FREEPROCCACHE;  
```

這個問題，可參數StoredProcedure文章中的：[查詢計畫的參數探測行為](http://vito-note.blogspot.com/2013/05/stored-procedures.html)

# 搜尋 vs 掃描（Seek vs Scan）

## 搜尋與掃描

在圖形化執行計畫中，最被關注的項目就是 XXX Scan 或者 XXX Seek。那麼 Seek 和 Scan 有什麼不同。

- **Scan**  
Scan 是指查詢執行時，系統會以整個資料表或整個索引的方式讀取資料，不像 Seek 是由經排序過的實體資料中讀取資料。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEie4WCPguhmtfUXURffssGszGW0DOuO-1ToBKq_MvtqwbvluFFGFpzclU7xgN3SeArEwfJ_NJvz4xCNTXMHDTlqrMTfitcIhF78MFklXJXjlckG-WmuuQrDLZPXlP_CT1KaXmkWbNJv3as/s0/sql-scan.png)
- **Index Seek**  
Seek 是指查詢執行時不是一筆筆去找資料，而是在一個使用 B-Tree 排序過的實體資料中尋找資料。  所以會使用 Seek 必定該資料建有 Index 的情況下才有可能，但是建了 Index 之後，系統卻不一定就會用 Seek 的運作方式尋找資料，這還得看查詢條件是否有正確使用索引。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipVnRVMqavIfurmKW_2theThlnBTinuO2fqqvZJ8pW-JpUCGMvJyxVV0t5rrFiWrKyxhSyJEvB9smCTAdf9zlR7V2t5ZLif3aixBMQRWNOvmB6I30C2iFU9vAFdZbmmFn8OIYBkwtmnUw/s0/sql-seek.png)

## 資料搜尋的方式

#### 查詢參數（Seek Argument, Search Arguments, SARG）

簡單講，**查詢參數**指的就是 WHERE 子句中的查詢條件。

對於沒有索引的資料，資料庫引擎自然只能使用「資料表掃描」。  但是，既使建了索引，資料庫也不見得會使用索引，這與**查詢參數**有相當大的關連。  因為只有在查詢參數足夠**選擇性**（Selectivity）時，才會正確使用到索引。  選擇性是指合格資料列與總資料列的比率。  如果比率低，表示索引具有高度選擇性。  這項索引就可以排除大部分的資料列，並大幅減少結果集的大小。  因此，這是一項值得建立的有用索引。反之，非選擇性的索引則沒有用處。  

簡單講，如果一個WHERE查詢的結果筆數，佔整體資料量的比例太大的話，那個這個查詢就不會用到索引搜尋。  所以查詢參數的使用將導至資料庫使用何種方式尋找資料，下面是各種可能的搜尋方式：  

### 資料表掃描（table scan）

這種搜尋方式，是當一個資料表沒有建立任何 Index 時所採取的資料搜尋方式，也就是實體資料以 Heap 架構存放時的處理方式。

### 叢集索引掃描（clustered index scan）

當一個資料表雖有建立叢集索引，但是查詢的 WHERE 條件中卻沒有使用到實體資料排序，   這時的搜尋方式，就會掃過整個叢集索引，以找出符合的資料。  
```sql
SELECT UserID FROM tblUser WHERE UserID=@UserID AND Password=@Password
SELECT UserID FROM tblUser WHERE UserID=@UserID
SELECT UserID FROM tblUser WHERE UserID=@UserID AND Password=RIGHT(sys.fn_VarBinToHexStr(hashbytes('MD5','000002')),32)

SELECT UserID FROM tblUser WHERE Password=@Password
SELECT UserID FROM tblUser WHERE Password=RIGHT(sys.fn_VarBinToHexStr(hashbytes('MD5','000002')),32)
```

上面例子是一個執行登入時常會使用到的 TSQL ，在資料庫規畫時，大家通常也都會將 UserID 設為 PK 欄位。  
前三行的指令都會採用**叢集索引搜尋**，  雖然 Password 不是索引欄位，但是查詢條件中已包含了 UserID 這個索引欄位，所以整體會使用**叢集索引搜尋**。  

後二行指令都採用**叢集索引掃描**  
只要沒有透過索引搜尋，當遇到資料量大的資料表時，效率就會降的非常快，因為它必須掃過所有的資料才能找到想要的資料。  
而其中第四行指令會更慢，因為其 SQL 語法中還包含了計算函式。

### 叢集索引搜尋（clustered index seek）

SQL Server 使用查詢參數中的第一個值，在**叢集索引**進行搜尋。  這種方式是最有效率的搜尋方式，資料完全利用叢集索引內所建立的實體資料排序。

### 索引掃描（index scan）

掃描整個**非叢集索引**。

### 索引搜尋（index seek）

SQL Server 使用查詢參數中的第一個值，在**非叢集索引**進行搜尋。  這種方式是最有效率的搜尋方式，資料完全利用叢集索引內所建立的實體資料排序。

### RID查閱（RID lookup）

Lookup for a single row in a table stored as a heap by using its row identifier (RID).

### 索引鍵查閱（key lookup）

Lookup for a single row in a table stored as a clustered index by using the key of the index.

## 範例說明

以下為測試資料的結構
```sql
--Table
CREATE TABLE [dbo].[Products](
[ProductID]			[int] IDENTITY(1,1) NOT NULL,
[ProductName]		[nvarchar](40) NOT NULL,
[SupplierID]		[int] NOT NULL,
[CategoryID]		[int] NULL,
[QuantityPerUnit]	[nvarchar](20) NULL,
[UnitPrice]			[money] NULL,
[UnitsInStock]		[smallint] NULL,
[UnitsOnOrder]		[smallint] NULL,
[ReorderLevel]		[smallint] NULL,
[Discontinued]		[bit] NOT NULL,
)
--PK
ALTER TABLE [dbo].[Products] ADD CONSTRAINT [PK_Products] PRIMARY KEY CLUSTERED 
(
ProductID ASC,
SupplierID ASC
)
--NonClustered Index
CREATE NONCLUSTERED INDEX [IX_Products_CategoryID] ON [dbo].[Products] 
(
CategoryID ASC
)
```

### 測試１
```sql
select SupplierID from products where ProductID=999
select \* from products where ProductID=999
select \* from products where ProductID=999 AND SupplierID=12
select \* from products where SupplierID=12 AND ProductID=999
```

這四行指令都會使用【叢集索引搜尋】，因為WHERE條件皆有叢集索引鍵中的第一個欄位，所以會採取 Seek 搜尋方式。

### 測試２
```sql
select SupplierID from products where SupplierID=12
select ProductID,SupplierID from products where SupplierID=12
select ProductID,SupplierID,ProductName from products where SupplierID=12
```

- 行１：索引掃描
- 行２：索引掃描
- 行３：叢集索引掃描

若不是使用叢集索引鍵中的第一個欄位，則會使用【索引掃描】。  

行３因為SELECT欄位包含非索引欄位，所以僅能進行【叢集索引掃描】。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBTwasVuhy0yop_tNm5sjOAFG9Rcw53dj0Dlsj1Ac2cztSoba3A3C8e146cQETWB90lDt1gKlnOJUfyBTcoJCWP9lB0JMm8WZOxwS2U3Yk11Ou6j0SWBPKDOpstXDAnlrCn5N6f8y7w3U/s0/sql-index-test2.png)

### 測試３
```sql
select SupplierID,CategoryID from products where CategoryID=8
select SupplierID,CategoryID from products where CategoryID=8 AND SupplierID=12
select SupplierID,CategoryID from products where CategoryID=8 AND SupplierID=12 AND UnitPrice=10
select SupplierID,CategoryID,ProductName from products where CategoryID=8
```

- 行１：索引搜尋
- 行２：索引搜尋
- 行３：叢集索引掃描
- 行３：叢集索引掃描

行１行２因為WHERE條件皆為索引鍵中的欄位，所以會採取【索引搜尋】搜尋方式。  行３因為WHERE條件包含非索引欄位，所以僅能進行【叢集索引掃描】。  行４因為SELECT欄位包含非索引欄位，所以僅能進行【叢集索引掃描】。  

行３會引起下面的「遺漏索引」  
```sql
CREATE NONCLUSTERED INDEX [IndexName] ON [dbo].[Products] ([SupplierID],[CategoryID],[UnitPrice])
```

行４會引起下面的「遺漏索引」  
```sql
CREATE NONCLUSTERED INDEX [IndexName]　ON [dbo].[Products] ([CategoryID])
INCLUDE ([ProductName],[SupplierID])
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfTG3iDUTJ_pEhx94s_5aY5tp0JRX7HIqE3y0lecOkWUH_UUvlJzJsfiGTZ243kW3tTouHVJcmqJZPt3Rt7ur68hD2BR2tQ_fDzPlsgxWeTAW0AVxRUfZlfmqXJZ3ReoD-YOsdy-Agd1Y/s850/sql-index-test3.png)

### 測試４
```sql
CREATE NONCLUSTERED INDEX [IX_Products_ProductName] ON [dbo].[Products] (
[ProductName] ASC
)

SELECT ProductID,SupplierID,ProductName from products where ProductName='Schoggi Schokolade'
```

這行指令因WHERE條件含有非索引欄位，所以是【叢集索引掃描】。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgK86woFrVp_NEO1ShVtkqdw7iD-itJngFAvWoD2Qfld4mLYNdUDGhpuaLxY1T92_0PXMAYkKwjO9oI1JNKvnpF6zGVIHCjr_ZOPZo3uqRs10VrZ6er9MCp43qiJZ7sfHCEkpu1pfacBmM/s0/sql-index-test4-1.png)

如果將 ProductName 建立索引欄位，就會變成【索引搜尋】。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixatZVWVm2pAI9HemP8hKMT3LhggL45eWzY8DDEDSAtOqu2W7RLdeNHl0dzICa22zGhOx8zHJsZK3MXX2NfvtGx6xm4Z68ivBmMNNN-nhR-P3QmtdniLd9KYo2eE2HHLrFXfvD66wxCJA/s0/sql-index-test4-2.png)

### 測試５
```sql
select ProductID,SupplierID,ProductName,UnitPrice from products where ProductName='Schoggi Schokolade'
```

這個例子，因為SELECT欄位包含非索引欄位，所以自然是【叢集索引掃描】。  

我們常會 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 很多欄位，難道要所有欄位都要加入索引才會使用【索引搜尋】嗎？  

當然不必的，因為 NonClustered Index 裡頭除了有索引鍵外，也可以**外包**一些欄位，用來應付所有的查詢欄位。  這些外包欄位，不會建立索引，一般稱為 **Nonkey** ，必須使用 [INCLUDE](http://technet.microsoft.com/zh-tw/library/ms188783.aspx) 子句來建立。  通常我們會將常用來搜尋的欄位當成 Nonclutered Index 鍵值，而其他欄位都可以加成 Nonkey 欄位。  
```sql
CREATE NONCLUSTERED INDEX [IX_Products_ProductName] ON [dbo].[Products] (
[ProductName] ASC
)
INCLUDE (UnitPrice,UnitsInStock)
```

如此查詢最佳化工具才會改用【索引搜尋】  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdvJhOMApGj8VBOFLjoqr9YZRYuBF4iw-9Z2zTAXj9soeL8xLLJnH7vIoyGSg5SnK2Gks6Vox7iiTgTm3BOjdl5tgsxbp4zrL74GZS8eYP7R9Gs8unewktahOBU9GbEUcumQl8V55kSDo/s850/sql-index-test5.png)

### 測試６：複合索引欄位

假設 WHERE 條件為複合索引中的欄位，那麼結果又如何呢？
```sql
CREATE NONCLUSTERED INDEX [IX_Products] ON [dbo].[Products] 
(
[SupplierID] ASC,
[CategoryID] ASC
)
```
```sql
select ProductID from products where  SupplierID=12
select ProductID from products where  CategoryID=5
select ProductID from products where  SupplierID=12 and CategoryID=5
```

- 行１：索引搜尋
- 行２：索引掃描（還是必須以複合索引中的第一個欄位才會使用Seek）
- 行３：索引搜尋

複合索引的第一個欄位選擇非常重要，因為它是唯一一個會經過排序的欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyNfs3oOQ17fYTyuQgny2hM2ls8XcXePAB2rSu3t1eEmHgo0P6zmU8gt7YNMKrf34yKjbbYjagRZzE9UZsczfjOgkNPPnuLk2XYwhzWOc5Oo4jOhsnZaCdYd4toJXsAXiYSlf9DewUo_Q/s0/sql-index-test6-1.png)

若 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 條件還包含非 PK 的欄位，則結果將全部變成【叢集索引掃描】
```sql
select ProductID,ProductName from products where  SupplierID=12 and CategoryID=5
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAmRplolgu64O0QN81i-5oGr0gi_wWtSMS-yDxrlbaqgh0duV-UeHrpvmrHyf3xwzR4PyvFVM2CTdxruZ5YA5shnoK1Kkz3bc9UYgsuSfp4bRp9qpLPvd529t8x_4EreaIciEtaumb6xA/s0/sql-index-test6-2.png)

必須將 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 欄位設成索引的 Nonkey 欄位，才會使用【索引搜尋】
```sql
CREATE NONCLUSTERED INDEX [IX_Products] ON [dbo].[Products] 
(
[SupplierID] ASC,
[CategoryID] ASC
)
INCLUDE ( [ProductName])  ```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4aLaLrjyjBO2GN3huPL5ptpMu7uxSzhhQfuED0MViyrC08sD99VZIyGewkg8jjUt93h8oL50xDIAUr0u60hHcru2-VNpocRLFNISvY5q9UFOzyqDi7-Y5bnC96gKPVuYezpxCBexT764/s0/sql-index-test6-3.png)
## 參考資料  

- [顯示圖形執行計畫（SQL Server Management Studio）](http://technet.microsoft.com/zh-tw/library/ms178071.aspx)
- 
-