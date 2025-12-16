---
title: 效能調校(1)-善用各種索引
layout: default
parent: Tuning Performance
nav_order: 2
description: "效能調校(1)-善用各種索引"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "4010330544355040574"
---
SQL Server 2008 supports two basic types of indexes: **clustered** and **nonclustered**.   Both indexes are implemented as a **balanced tree**, where the leaf level is the bottom level of the structure.   The difference between these index types is that the clustered index is the actual table;   that is, the bottom level of a clustered index contains the actual rows, including all columns, of the table.   A nonclustered index, on the other hand, contains only the columns included in the index's key, plus a pointer pointing to the actual data row.   If a table does not have a clustered index defined on it, it is called a **heap**, or an unsorted table.   You could also say that a table can have one of two forms: It is either a heap (unsorted) or a clustered index (sorted).  

# Improving Performance with Covered Indexes

## 覆蓋索引（Covered Index）

The notion of a covered index (覆蓋索引) is that SQL Server doesn't need to use lookups between the nonclustered index and the table to return the query results.   Because a clustered index is the actual table, clustered indexes always cover queries.  To consider the index covered, it must contain all columns referenced in the query (in any clause, SELECT, JOIN, WHERE, GROUP BY, HAVING, and so on)  

**覆蓋索引**指的是查詢陳述式中的所有用到的欄位（包含出現在 SELECT, JOIN, WHERE, GROUP BY, [HAVING](http://technet.microsoft.com/zh-tw/library/ms180199.aspx) 子句中的欄位）都在索引裡（包含 INCLUDE ）。  
```sql
Select Col1,Col2 From TableA WHERE Col1=1
```

考量上面這個範例，如果要索引覆蓋這個查詢，則索引至少要包含 Col1 和 Col2。以下幾個方式的索引都有覆蓋這個查詢。  
```sql
CREATE Index IX_TableA on TableA(Col1, Col2)
CREATE Index IX_TableA on TableA(Col1) INCLUDE (Col2)
CREATE Index IX_TableA on TableA(Col1, Col2, Col3)

CREATE Index IX_TableA on TableA(Col2, Col1)
CREATE Index IX_TableA on TableA(Col2) INCLUDE (Col1)
```

所以，只要查詢陳述中的欄位都有在索引之中，不管在索引中的位置順序，或者是在 INCLUDE 欄位中，都可以算是覆蓋索引。  雖然，上面這些不同的索引設定，在執行計畫中，是有不同的執行效率，但都算覆蓋索引。  

### 覆蓋索引範例說明

底下範例，比較覆蓋索引與非覆蓋索引的執行效率。

#### 建立測試範例資料
```sql
Select Count(\*)  From MsgTable -- 98629
--Q1～Q4
Select MsgId, MsgText From MsgTable WHERE MsgId = 1001                  --return 11 rows
Select MsgId, MsgText From MsgTable WHERE MsgId > 1000 AND MsgId < 1100 --return 1056 rows
Select MsgId, MsgText From MsgTable WHERE MsgId Between 1001 AND 1200   --return 1210 rows
Select MsgId, MsgText From MsgTable WHERE MsgId Between 1001 AND 1500   --return 2409 rows

--Noncovered index
CREATE INDEX IX_MsgTable_Noncovered ON MsgTable(MsgId)

--Covered index
CREATE INDEX IX_MsgTable_Covered ON MsgTable(MsgId) INCLUDE(MsgText)
```

#### Logical Reads

|  | Query1 | Query2 | Query 3 | Query 4 |
| --- | --- | --- | --- | --- |
| No Index | 2748 | 2748 | 2748 | 2748 |
| Noncovered index | 13 | 2748 | 2748 | 2748 |
| Covered index | 4 | 24 | 29 | 69 |

#### Execution Plan

|  | Query1 | Query2 | Query 3 | Query 4 |
| --- | --- | --- | --- | --- |
| No Index | Table Scan | Table Scan | Table Scan | Table Scan |
| Noncovered index | Index Seek + RID Lookup | Table Scan | Table Scan | Table Scan |
| Covered index | Index Seek | Index Seek | Index Seek | Index Seek |

#### RID查閱(RID Lookup)

[RID查閱(RID Lookup)](http://technet.microsoft.com/zh-tw/library/ms190696.aspx)：透過RID（資料列識別碼，RowID)，查閱實體資料表中的資料列。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMPMWzP2KumnSWNbhEHu90hNsmnHcZnUyGdoGIrNqP4rT4zDMELr_tnRCLwLS0O6Tagj3gD042013oN7Z-n9Q2M91am7jAwb_g_Wexi9tSElmcI2er0gS-4ac76I1pn8_c6YuMqR4wLn0/s0/sql-rid-lookup.png)

#### 總結

由上表可以看出：

- A covered index always performs better than a noncovered index.
- For queries that return a very limited number of rows, a noncovered index also performs very well.
-

## 包含欄位（Included Column）

在探討包含欄位之前，整理一下底下這個表

| 物件名稱 | 數目上限 |
| --- | --- |
| 每個主索引鍵的資料行數 | 16 |
| 索引欄位大小的總和 | 900 Bytes |
| 每個資料表的資料行數(非寬型) | 1024 |
| 每個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式的資料行數 | 4096 |
| 每個 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 陳述式的資料行數 | 4096 |
| 每個 GROUP BY、ORDER BY 的資料行 | 16 |
| 每份資料表的叢集索引數 | 1 |
| 每份資料表的非叢集索引數 | 249 |
| 每個索引鍵的資料行數 | 16 |
| 每個索引鍵的包含資料行數 | 1023 |
| 每個外部索引鍵的資料行數 | 16 |

由表中得知每個索引鍵最多只能有16個資料行組成，如果只靠索引的欄位要達到覆蓋索引，16個資料行數可能有所不足。  為了這個目的，SQL2005加入了包含欄位(Included Column)，可以用來避免資料表查閱(table lookup)的時間。  

SQL2005 允許每個索引鍵可以有 1023 個包含欄位，而一個資料表最大也只不過 1024 個欄位，所以要達到覆蓋索引就變的可能了。  

#### 使用包含欄位注意事項：

- 包含欄位沒有限定LBO型態。
- 包含欄位不適用於 filtering, grouping 或 sorting。

用來執行 filtering, grouping 或 sorting 的欄位，應該要設成是索引鍵的一部份，其他欄位才設成索引的包含欄位。  

### 包含欄位範例說明

#### 建立測試範例資料
```sql
CREATE TABLE TestTable(
PKCol UNIQUEIDENTIFIER NOT NULL DEFAULT NEWSEQUENTIALID() PRIMARY KEY CLUSTERED
,Col1 INT IDENTITY NOT NULL
,Col2 CHAR(20) NOT NULL
,Col3 CHAR(20) NOT NULL
,Col4 CHAR(20) NOT NULL
,Col5 CHAR(20) NOT NULL
,Col6 CHAR(20) NOT NULL
,Col7 CHAR(20) NOT NULL
,Col8 CHAR(20) NOT NULL
,Col9 CHAR(20) NOT NULL
,Col10 CHAR(20) NOT NULL
,Col11 CHAR(20) NOT NULL
,Col12 CHAR(20) NOT NULL
,Col13 CHAR(20) NOT NULL
,Col14 CHAR(20) NOT NULL
,Col15 CHAR(20) NOT NULL
,Col16 CHAR(20) NOT NULL
);

INSERT TestTable (Col2, Col3, Col4, Col5, Col6, Col7, Col8,
Col9, Col10, Col11, Col12, Col13, Col14, Col15, Col16)
SELECT TOP(1000000)
CAST(message_id AS CHAR(20)) AS Col2
,CAST(message_id AS CHAR(20)) AS Col3
,CAST(message_id AS CHAR(20)) AS Col4
,CAST(message_id AS CHAR(20)) AS Col5
,CAST(message_id AS CHAR(20)) AS Col6
,CAST(message_id AS CHAR(20)) AS Col7
,CAST(message_id AS CHAR(20)) AS Col8
,CAST(message_id AS CHAR(20)) AS Col9
,CAST(message_id AS CHAR(20)) AS Col10
,CAST(message_id AS CHAR(20)) AS Col11
,CAST(message_id AS CHAR(20)) AS Col12
,CAST(message_id AS CHAR(20)) AS Col13
,CAST(message_id AS CHAR(20)) AS Col14
,CAST(message_id AS CHAR(20)) AS Col15
,CAST(message_id AS CHAR(20)) AS Col16
FROM sys.messages AS sm
CROSS JOIN (
SELECT TOP(15) 1 AS Col FROM sys.messages
) AS x;

CREATE NONCLUSTERED INDEX IncludedColumns ON TestTable (Col1)
INCLUDE (Col2, Col3, Col4, Col5, Col6, Col7, Col8, Col9, Col10, Col11, Col12,
Col13, Col14, Col15, Col16);

CREATE NONCLUSTERED INDEX NoIncludedColumns ON TestTable
(Col1, Col2, Col3, Col4, Col5, Col6, Col7, Col8, Col9, Col10, Col11,
Col12, Col13, Col14, Col15, Col16);
```

PS.若要使用 GUI 設定包含欄位，可在索引屬性中設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrLUr4nW5O3QGq5nfzTp1HqZmf_CM4hZD_9hyphenhyphensbCTWuqIIr78q7F9CGV4QcJb3p6zvuiRKP0JzpDjv8BptqHeQ_xSdD94MdvqGuzrrxX0LTmtTUt5689x3EL5msVs_1pPQofgmfmx9wYM/s0/sql-create-included-columns.png)

#### 檢視索引相關資訊

使用 [sys.dm_db_index_physical_stats](http://technet.microsoft.com/zh-tw/library/ms188917.aspx) 這個系統內建的動態管理函式可以用來檢視索引的相關資訊
```sql
SELECT index_id,index_depth,index_level,page_count,record_count,avg_record_size_in_bytes
FROM sys.dm_db_index_physical_stats(DB_ID(),OBJECT_ID('TestTable'),NULL,NULL,'DETAILED') AS a
WHERE index_id>1
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAFsEGzywYHdgDqAt1oUZESYdiuT7si05DGtHVqlHZ4KyqW4mDhhl8peyQvgqbHfuukSjp4p4EH5wHLJAMkfqo2rrgnxEXayIva_qSzdori6ArK2zsf71ATVI8U-AQ2OhDHV82EYWrNaE/s0/sql-index-size.png)

#### 索引大小

|  | IncludedColumns | NoIncludedColumns |
| --- | --- | --- |
| 總使用空間 | 40148 pages | 41745 pages |
| 非葉級索引使用空間 | 146 pages | 1745 pages |
| 葉級索引使用空間 | 40002 pages | 40000 pages |
| 索引深度 | 3 | 5 |
| 非葉級索引每列平均的大小 | 27 bytes | 327 bytes |
| 葉級索引每列平均的大小 | 321 bytes | 321 bytes |

- 二個索引所使用總空間大小差不多(約4%)，這是因為它們都含有相同的欄位資料
- 但是二個非葉級的索引，IncludedColumns這個索引因為只有一個索引鍵，所以大小就小很多了。
- NoIncludedColumns 這個 Index 的葉級和非葉級索引大小差不多。因為都放了相同欄位的資訊。

## 覆蓋索引 vs 非覆蓋索引

由前面介紹知道，要設計覆蓋索引，第一種方法是將所有欄位都放在索引鍵，另一種方法是將只會用在 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 的欄位放到 INCLUDE 欄位。  底下來比較看看這二種覆蓋索引和非覆蓋索引的執行效能。  

### 陳述式

首先建立一個非覆蓋索引，和另一個要關連的資料表。
```sql
-- Create the NotCovered index.
CREATE NONCLUSTERED INDEX NotCovered ON TestTable (Col1);

-- Create and populate the OtherTable table.
CREATE TABLE OtherTable (
PKCol INT IDENTITY NOT NULL PRIMARY KEY
,Col1 INT NOT NULL
);

-- Insert test data
INSERT OtherTable (Col1) SELECT Col1 FROM TestTable;
```

下面陳述式中 **[WITH(INDEX)]** 是用來強迫 SQL Server 只使用指定的索引。
```sql
SELECT O.PKCol, T.Col2
FROM OtherTable AS O
INNER JOIN TestTable AS T WITH(INDEX(IncludedColumns)) ON O.Col1 = T.Col1
WHERE O.PKCol BETWEEN 1 AND 1000;

SELECT O.PKCol, T.Col2
FROM OtherTable AS O
INNER JOIN TestTable AS T WITH(INDEX(NoIncludedColumns)) ON O.Col1 = T.Col1
WHERE O.PKCol BETWEEN 1 AND 1000;

SELECT O.PKCol, T.Col2
FROM OtherTable AS O
INNER JOIN TestTable AS T WITH(INDEX(NotCovered)) ON O.Col1 = T.Col1
WHERE O.PKCol BETWEEN 1 AND 1000;
```

### 執行計畫

覆蓋索引(使用包含欄位)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGG6ArZAGPI2xSnI9cVXrlrZBxIZp1LxhbVcTsHmM5BS6DtnuPf-Q7wHqU_doHSckyVA2nWQUiW3uDuqUfRWvjYrsxGfERDB6Y59_RB0yaUr4BjJJUQ8nTfOCHw73ccTlRPG5hrq5QB3k/s0/sql-covered-index-1.png)

覆蓋索引(不使用包含欄位)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhsz5R39sxOp1IBB4nbu5itgj4r1DIyqhF5mWFPRMK8kSSZ36TUUYr1xcPED8SccCOzcykXW8SRXb9IeFGLk_9P-n-fboC3vKa9X6304FfUeqI4IIFpJKSoG4P9nfJ5exHmxNabFKXpqr0/s0/sql-covered-index-2.png)

非覆蓋索引

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTP1y0S53At2FuklG252ukoUu33EJyCfT2ntpgwc7vsEpP4AOeGJf5G22NELWekGNbXurd8pt1UZhYEDFBP8ORBxX5sMxaclL1P-sybJyClh_YoWGOtZuO_blIH02kjwrgUez5BEykfvU/s0/sql-covered-index-3.png)

### Page Reads

#### 三個陳述式的 Page Reads：

- Query 1：3292
- Query 2：5434
- Query 3：6288

#### 數據說明：

- Query3 效率最差是因為必須額外再使用索引值去lookup資料。
- Query2 效率比 Query1 效率差約 40% ，這主要是因為該索引的深度比較大造成的。這個 40% 的差距，也大約就是它們的深度比 3:5。

# Using Clustered Indexes

關於叢集索引的介紹，請看[這一篇](http://vito-note.blogspot.com/2013/05/blog-post_6020.html)。

# Using Computed Columns

**計算資料行 (Computed Columns)**是一種特殊的欄位，該欄位值是使用同一資料表中其他欄位的值運算而成的。詳情可參考MSDN:[計算資料行（Computed Columns）](http://msdn.microsoft.com/zh-tw/library/ms191250.aspx)。  

利用計算欄位，也可以用來將 scan 改善成 seek 。  

## Computed Columns 範例說明

### 使用 Computed Columns 調校 WHERE 子句中使用函式的問題

如果我們想統計整個資料表中六月份的訂單數量，我們通常會使用底下這個範例。  然而這並不是一個合法的 SARG ，因為 WHERE 子句中使用了函式，所以會採用 scan 運算以查詢資料。  
```sql
Select COUNT(1) From Orders　WHERE MONTH(OrderDate)=6
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg93vJsg9QjGk8JmakxN2QUco17hC4_nPAJzUA55oRCS3pLKCTNORZwOTXGmDyFYN95hHXDcKoWcD5EMu1svS8C3WLiw9jYfEtArBHSihZeNZ1JvGz5oZl8J0sVa-YxSkSHBFGOWQ15VXk/s0/sql-tuning-computed-column-1.png)

要避免這個問題，就可以採用[計算資料行（Computed Columns）](http://msdn.microsoft.com/zh-tw/library/ms191250.aspx)來調整效能。  
```sql
--建立計算欄位
ALTER TABLE Orders
ADD OrderMonth AS MONTH(OrderDate);

--使用計算欄位建立索引
CREATE NONCLUSTERED INDEX OrderMonthIndex
ON Orders (OrderMonth);
```

這時候你就可以利用計算資料行來進行查詢，最佳化工具就會採用 seek 運算。  
```sql
Select COUNT(1) From Orders WHERE OrderMonth=6
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnu9sYRLGlged5q7IWG-xkk10cZvBgMcyCakfnV-7vA8Mmoe_GdzDeA_jIMsxEQJAd0KbGUveV6rZ-rlNfUzYykdMtcXcexCSOJBk7KFnVHHZUrhOecgbApDDSZKxrxVTAtsrMHTPqoyA/s0/sql-tuning-computed-column-2.png)

其實，此時即使你沒有使用計算資料行，最佳化工具也會使用計算資料行的索引。  

As you can see, SQL Server used the index of the computed column without having a reference to it in the query.   This is a great feature because it makes it possible to add computed columns and index them without having to change the queries in applications  or stored procedures to use the new index.  
```sql
Select COUNT(1) From Orders WHERE MONTH(OrderDate)=6
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzvzAFxTPZ5GWWSTRb824KyzrQJi-k417xrJXapYILqpumiazzdeTX80bweCfoi4Qm_T1mcRCgb5WfBmTstt9xL-9LNrjSvbVNRmAb9Ym-al6NUvqU4AMgiM4wvccphobuVM9RXE6gEYw/s0/sql-tuning-computed-column-3.png)

### 使用 Computed Columns 調校 WHERE 子句中使用 collations 轉換的問題

#### 建立測試資料
```sql
DROP TABLE ProductNames
CREATE TABLE ProductNames (
Name NVARCHAR(50) COLLATE Latin1_General_CI_AI
);

INSERT ProductNames (Name) VALUES (N'Öl');
INSERT ProductNames (Name) VALUES (N'Olja');
INSERT ProductNames (Name) VALUES (N'Beer');
INSERT ProductNames (Name) VALUES (N'Oil');

CREATE CLUSTERED INDEX NameIndex ON ProductNames (Name);
```

#### Query 1

使用不分腔調字的查詢
```sql
SELECT Name FROM ProductNames WHERE Name LIKE N'Ö%';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvA4aOx7jPssfmOV6OB2uYYTKdhkTykz9LcQHoNrahwGs8QI1wvM0_ovmW2Ep15mWpFUD7kEpfF9cAPxX1WggoOovJ_7-Q1y1oHpQ2LsX3MaspI2zoexgToFWo50qovghitpHn4ripHks/s0/sql-tuning-collate-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcCzv_V6CEPeP66VavRO8D586jQbJu-otCfDIp6BDvhyphenhyphennlwQa6O_nKMjSN7sUKVuBMcTVBa4T5pSRv-0cyooM6BO05HEqv15jXyCNJupzeJwGV26djG3B5mSRcqcwsh7x3byGwEVselFY/s0/sql-tuning-collate-2.png)

#### Query 2

使用 [COLLATE](http://msdn.microsoft.com/zh-tw/library/ms184391.aspx) 讓查詢區分腔調字
```sql
SELECT Name FROM ProductNames WHERE Name LIKE N'Ö%' COLLATE Latin1_General_CS_AS
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNOMHigZofs-qRCc1Vq9m66n5i6gVxM9hdMhtUn47v20H3bFuIlmDQeXoZOs3K40Jjzy8pp_CWkXwMDJR8eaIr-BY0zoI62FiawPfPMr7rp2tTrgH0gl9D9DzHNFUwu3uU1lGE8m4wnd0/s0/sql-tuning-collate-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5yGn3V0a302lSRTYeApIkBDGbPaZ-kPgj5kGgTW1CfAPDRGrBsTc5BSqdxvLNyYQs0PRFu66DL5iMlQT73AEIKT_Zk9xquPqQcxrXVyp3_VhstbsDEk2KJuuDJDI5m2YaV1BO4DwcmVs/s0/sql-tuning-collate-4.png)

這個結果與書上例子不太一樣，書上的例子是使用 clustered index scan

#### 建立 Computed Columns

由上面結果得知，因為轉換定序的原故，使用最佳化工具會使用 scan 取代 seek 。  這時候也可以利用 Computed Columns 來改善這個問題。  
```sql
-- 加入一個區分腔調字的「計算欄位」  ALTER TABLE ProductNames  ADD Name_Latin1_General_CS_AS  AS Name COLLATE Latin1_General_CS_AS;    -- 在「計算欄位」上建立索引  CREATE NONCLUSTERED INDEX NameIndex2 ON ProductNames(Name_Latin1_General_CS_AS);    -- 執行同樣查詢SELECT Name FROM ProductNames WHERE Name LIKE N'Ö%' COLLATE Latin1_General_CS_AS
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYfCPy2MYC89uXjxwktcBe5l33MjcZI25Ws8n1SvuLArEjZw9-iCgxPV11wtUWeTS619UAA_KrQ8sMdl_E3FMAmeu06schWGpiSJMyaVzRpFobaN3J5zCTRC_7cy7cGpSBKzZcUU81pQc/s0/sql-tuning-collate-5.png)

# Using Indexed Views

一般使用VIEW，只是簡化查詢語法，對執行效能是沒有任何幫助的。  然而，由 SQL 2000 開始，VIEW 加入了索引支援，這可以大大的提升 VIEW 的查詢效能。  

#### 建立測試 VIEW

這個VIEW建立了客戶與購買商品的關連
```sql
CREATE VIEW dbo.vw_OrderDetail
WITH SCHEMABINDING
AS
SELECT O.CustomerID, O.OrderID, P.ProductName, OD.Quantity, OD.UnitPrice, O.OrderDate, P.SupplierID
FROM dbo.Products P
INNER JOIN dbo.OrderDetails OD ON P.ProductID = OD.ProductID
INNER JOIN dbo.Orders O ON O.OrderID = OD.OrderID
```

底下是查詢VIEW的執行計畫：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDevZQl6DeF3-u1gQ0muWXoaQ5-6CLnEN0zF1DcHDDcc1ikya1tamYI2lfo01lMjjO4PJFBDO9xzjpUirmWWb5S866BpgbpdGiY76sqof5SQmMNTTPwBoQoeh8GPMgmZ5sX_UGdJfQBEA/s0/sql-tuning-view-index-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlheR55xf5Mgl28p_ZhVu5xR9DUsL3rhyphenhyphen7YWe9SaSsKXZiVBxrHTJbYzMD65r642z-fubRH2ln7r43uWZp0CBw4mDVlzaug3kcnGPt8FfNUyCXJ2BpU15yo-Ha9J9ZBlYpS-2sr_tET7I/s850/sql-tuning-view-index-3.png)

#### 建立 VIEW 的索引

現在我們在 VIEW 上建立索引，但是要注意，只有使用 [WITH SCHEMABINDING](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) 建立的 VIEW ，才可以建立索引。  而且第一個建立的索引，必須是**唯一叢集索引**，之後才可以建立非叢集索引。  
```sql
CREATE UNIQUE CLUSTERED INDEX [PK_vw_OrderDetail] ON [dbo].[vw_OrderDetail] 
(
CustomerID, OrderID ,ProductName
)
```

底下是新的查詢陳述式與產生的執行計畫：  
```sql
SELECT SupplierID,OrderID,ProductName FROM vw_OrderDetail WITH(NOEXPAND)
WHERE CustomerID='SAVEA'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCwXSoezuGyPM5MDvWHV3NyD59LlycYL7cK7aerkf-n2vZHmeZaR5yYysEXagf9ynf93_g6f2w6Gpy_YwXm9ndTh06sPR3TH9m8EBiPAxjIkkYLwU3MWDN0RLLT6aK4MrFi9rAAxAHNyo/s850/sql-tuning-view-index-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWuvJmwQ39TPLreOHcZg5Go0MXHL9XXvp1VWVm1rSX139PL6WEOVsDiBJ18ia2FkN8y6Mw84YbFS0jA0Ld6YDOn2Ge1_HLf19DCUSMQhvkIgXX6YrnhNdr5n3YkSRR8Rgw8GjNnAzEdKo/s850/sql-tuning-view-index-4.png)

這裡要注意一點，若不是使用 SQL Server 2008 企業版或開發版，必須加上 WITH(NOEXPAND)

# Using Columnstore Index

關於 Columnstore Index 的介紹，請看[這一篇](http://vito-note.blogspot.com/2014/03/blog-post.html)。

# Read Performance vs. Write Performance

待補

# Analyzing Index Usage

要分析索引的使用狀況，可以使用 [sys.dm_db_index_usage_stats](http://msdn.microsoft.com/zh-tw/library/ms188755.aspx) 這個 DMV 。或者由 SSMS 的報表功能查看。  

### 使用 sys.dm\_db\_index\_usage\_stats
```sql
select \* from sys.dm_db_index_usage_stats
```

- user\_seeks：使用 index seek 次數
- user\_scans：使用 index scan 次數
- user\_lookups：使用 index lookup 次數
- user\_updates：索引更新次數
- last\_user\_seek：
- last\_user\_scan：
- last\_user\_lookup：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8sC3b_wDEFQBudFn6kPqkoxt3xb6PL7glQfXY_De6bCOVqpkihm40MrrvbJx50LxQW0Gstiw70SdQS3JanOP-_IA_E-MvA6en9Bt8yXNzPZQRfQk7Y3KZ8N_EIHiRX8QH7OGgSc5cB5M/s0/sql-index-statistics-4.png)

在查詢結果中，你應該關注的是：

- 若 user\_seeks 或 user\_scans 值都很低，表示這個索引很少在使用。
- 若 user\_seeks 很低， user\_scans 很高，表示這個索引不是一個好的索引，應該可以進一步重新規劃。
- 若 user\_updates 很高，可以檢查看看，是否有不必要加入索引的欄位。

### 使用 SSMS 的索引統計報表

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixlYJOzLpx2LX9uRiwHaHZeohe-fh3evt51jSNLD_Hfqh-_9mYeKQIlnBMuSLazEhVU1kmsSq0bTNy-ajOqqiF8dRdRPHAE_2a22cgXv1mR0lpbgHLztkM7RbJGKVJy2v6rSkUmbtz1-k/s0/sql-index-statistics.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjvVKyKe9dC8XhRpaAg_5uLdeeQSanxFvqKMgbtC3KMxl-we8keL2nSBVKEcYsjP2VpEZhsgu5_scnkn1LJPblEtV5kCZ7fud-U0XXKeXliTlghk3l9D4xfkpFhJeuopF7cucjiRomB4sM/s0/sql-index-statistics-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCwUzH0ZsnHwIA4MUTjotwsCN6d50dm5C8R1g4UCH42crLwR4XHneUujkRcj6Q1H4lUkB8Y72491qDB5sElkk8Yu_eDHlDVAAiS-BaYCmGWSYvwcHkGUWOu4QR-8O7snl3pQpRY0fsJIQ/s850/sql-index-statistics-3.png)

# Partitioning

資料分割資料表是 SQL Server 2005 針對 Enterprise 和 Developer 版所提供的一種用來將大型資料表利用分割的功能，以水平方式拆分成數個分割（Partition），藉此提升資料載入的效能並保有資料集合的完整性。  每個分割都是獨立的，假設您是依照年度來進行資料表分割，您就可以規劃針對今年的分割進行 INSERT、UPDATE、DELETE 等資料維護作業，今年以前的分割用來做查詢用途，配合適當的硬體組態來盡可能的提升查詢效能。  

詳細內容[請看這篇](http://vito-note.blogspot.com/2013/05/blog-post_30.html)

# Tuning Indexes Automatically

SQL Server 也有提供一些自動化調校的功能。  

例如：在使用圖型執行計畫時，有時候你會見到索引遺漏（Missing Index.）的提示訊息，你可以直接在提示訊息上使用右鍵功能取得建議的索引建立腳本。  

或者使用「Database Engine Tuning Advisor」這個工具來分析系統所缺少的索引。這個工具的使用，可以參考[這篇部落格](http://www.dotblogs.com.tw/jimmyyu/archive/2009/10/13/11039.aspx)。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkfL3SbWJ_RLobHcABYbhGk7NPEF5mjiiNJQPXnCsDv8cSKacGRtX6MEL7i3bo5wykltK4C0S0lh1wzwmv0FIp6I6IjLaIFxe5ICddZw59OdAGeDRDG3AbBBXmz3vpF7346O-uGXc_D4I/s0/sql-DETA.png)
## 參考資料  

- [CREATE INDEX (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms188783.aspx)
- [Indexes on Computed Columns](http://msdn.microsoft.com/en-us/library/ms189292.aspx)
- [Create Indexed Views](http://msdn.microsoft.com/en-us/library/ms191432.aspx)
- [\[WILL\]如何找出 SQL Server 2008 資料庫裡是否建立了多餘的索引](http://blog.miniasp.com/post/2011/08/12/How-to-find-out-unnecessary-SQL-Server-Index-from-Index-Usage-Statistics-Report.aspx)