---
title: 資料倉儲效能維護
layout: default
parent: Data Warehouse
nav_order: 4
description: "資料倉儲效能維護"
date: 2013-12-16
tags: [SQL,Data Warehouse]
postid: "343945840283535287"
---
Data warehouses are often very large, so you have to deal with performance problems.   You can use index, data compression, columnstored indexed, T-SQL query skill ... to enhance performance.  

# Indexing Dimensions and Fact Tables

## Indexing

### clustered indexes

store every table with a clustered index, and surrogate keys are ideal for clustered indexes.

### Nonclustered indexes

Nonclustered indexes generally don't help DW queries much.   However, this does not mean that you shouldn't create any nonclustered indexes in your DW.  An attribute of a dimension is not a good candidate for a nonclustered index key.   You should create indexes only if these columns are used in report queries  

### filtered index

You can create a filtered nonclustered index which are useful when some values in a column occur rarely, whereas other values occur frequently .

## Join

### Hash Join

**SQL Server Query Optimizer** recognizes star join patterns and uses bitmap filtered hash joins.   Query Optimizer uses **hash joins** when you join on non-sorted columns from both tables involved in a join.  Hash joins can work in parallel threads.   With bitmap filtering, they can work on a subset of rows from a dimension and from a fact table in each thread.  Such queries are typical for data warehousing environments.   For hash joins, you do not index the foreign keys of a fact table.  

### Nested Loops Joins

If one join input is small (fewer than 10 rows) and the other join input is fairly large and indexed on its join columns, an index nested loops join is the fastest join operation because they require the least I/O and the fewest comparisons. 

### Merge Joins

If the two join inputs are not small but are sorted on their join column (for example, if they were obtained by scanning sorted indexes), a merge join is the fastest join operation.

## Indexed Views

Indexed views are especially useful for following case:  

- queries that aggregate data.
- queries that perform multiple joins.

However, to speed up data loads, you can drop or disable the index before load and then recreate or rebuild it after the load.  
```sql
SET STATISTICS IO ON

USE AdventureWorksDW2012;
GO

SELECT ProductKey, 
SUM(SalesAmount) AS Sales, 
COUNT_BIG(\*) AS NumberOfRows
FROM dbo.FactInternetSales
GROUP BY ProductKey;
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixB0WITOvRgtC-uSdBW3jm1gzMtB1zV3Nd2Lf-S1uMtGTPy_qRed2CND5jRiEPmP9gTMELqIMzBINSe14sq52MPFpdyw8s-JkAFY7NJJfHBKyzM8m-rv_X2-fZ1lU609YNRYjnONRFRB0/s0/sql-indexd-view-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKMdqPa1dVY0jNV59j8AfD0cw8752-aX6w_uKnT1879tYIKBeetdms7-IY-COMDk44qu48-IuAAoxo4lrwwsbwSaFGI_tUl_TYQNoOaxcYGPcfLk62As7uSkWXt6bHPYgFQClaI0CT1wI/s0/sql-indexd-view-2.png)
```sql
CREATE VIEW SalesByProduct WITH SCHEMABINDING AS
SELECT ProductKey,
SUM(SalesAmount) AS Sales,
COUNT_BIG(\*) AS NumberOfRows
FROM dbo.FactInternetSales
GROUP BY ProductKey;
GO
CREATE UNIQUE CLUSTERED INDEX CLU_SalesByProduct
ON dbo.SalesByProduct (ProductKey);
GO

SELECT ProductKey,
SUM(SalesAmount) AS Sales,
COUNT_BIG(\*) AS NumberOfRows
FROM dbo.FactInternetSales
GROUP BY ProductKey;
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMnlCEjhhMStDLK7UBIOHCAlLFx6HQ29eys6ENlRgzoCeBeofDDHAjZyr1n48UPKYWql_9HTqhZofhT4e9K39Y_mQXbMgTLuP0sfoRNNNBWqK1oAnC8UXsJXx39sheqpVVdcDMrDmHJWw/s0/sql-indexd-view-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIU5GFBSVS7q6PIimvKkMJ4G_p3CbTIFjcwjX7nxHoZsGY-xcCF1iTV_m7mVeRQeXcdAPQL-KMccB5JFvRxpPiwJ1yqGNrFg3OIIpsu3jEi-untIg2MxBPNvah-tgLY9QsMF1Ax1jT6rU/s850/sql-indexd-view-4.png)

Note that the view must be created with the [SCHEMABINDING](http://msdn.microsoft.com/zh-tw/library/ms187956.aspx) option if you want to index it.   In addition, you must use the [COUNT_BIG](http://msdn.microsoft.com/zh-tw/library/ms190317.aspx) aggregate function.  Nevertheless, after creating the view and the index, execute the query again.

## Using Appropriate Query Techniques

若你寫出沒有效率的 TSQL ，資料庫引擎也幫不了你了。例如下面這個總計的查詢：  

需求：依訂單號碼順序，列出訂單明細中，各個品項的金額，並且後面加入一個累計金額欄位，如下面結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmL3fQrki3Q1kR0QYjEa0LpjT85dd-Mb_vx4eu-UvEltQ6v2by2W-0UcrUTUdhPKffDNs-cVh5KW7iqu4cog3UvBWiySp61_b4yZ3w3YUzyS9HaBIYov3pz1kryGofGIn3EwLtJN-NySY/s0/sql-appropriate-query-skill-1.png)

你可以使用 non-equi join 的查詢方式，例如：
```sql
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

WITH InternetSalesGender AS
(
SELECT ISA.CustomerKey, C.Gender,
ISA.SalesOrderNumber + CAST(ISA.SalesOrderLineNumber AS CHAR(1)) AS OrderLineNumber,
ISA.SalesAmount
FROM dbo.FactInternetSales AS ISA
INNER JOIN dbo.DimCustomer AS C ON ISA.CustomerKey = C.CustomerKey
WHERE ISA.CustomerKey <= 12000
)
SELECT ISG1.OrderLineNumber, ISG1.Gender,
Min(ISG1.SalesAmount) AS SalesAmount, 
SUM(ISG2.SalesAmount) AS RunningTotal
FROM InternetSalesGender AS ISG1
INNER JOIN InternetSalesGender AS ISG2 ON ISG1.Gender = ISG2.Gender AND ISG1.OrderLineNumber >= ISG2.OrderLineNumber
GROUP BY ISG1.Gender, ISG1.OrderLineNumber
ORDER BY ISG1.Gender, ISG1.OrderLineNumber
```

下圖是執行結果，  回傳 6434 列，  FactInternetSales 邏輯讀取 4534 ，  DimCustomer 邏輯讀取 196 ，  Worktable 邏輯讀取 21314。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmL3fQrki3Q1kR0QYjEa0LpjT85dd-Mb_vx4eu-UvEltQ6v2by2W-0UcrUTUdhPKffDNs-cVh5KW7iqu4cog3UvBWiySp61_b4yZ3w3YUzyS9HaBIYov3pz1kryGofGIn3EwLtJN-NySY/s0/sql-appropriate-query-skill-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjE-FfHEo-vg9h_ouj7N2UxgMkpjC7-1BDDaTgQbDVyQ7D-ZkVQlJSzDoVZug91OPHoiCq0V72Cb59d6elkbFcgyHahyjEgu4KM5_wmqIAyg3iW4u1c0LCP0kuqm07qNtxwDQwA8VlBBcM/s0/sql-appropriate-query-skill-2.png)

下面範例，我們套用 [OVER](http://msdn.microsoft.com/zh-tw/library/ms189461.aspx) 子句，重新撰寫 TSQL ：
```sql
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

WITH InternetSalesGender AS
(
SELECT ISA.CustomerKey, C.Gender,
ISA.SalesOrderNumber + CAST(ISA.SalesOrderLineNumber AS CHAR(1)) AS OrderLineNumber,
ISA.SalesAmount
FROM dbo.FactInternetSales AS ISA
INNER JOIN dbo.DimCustomer AS C ON ISA.CustomerKey = C.CustomerKey
WHERE ISA.CustomerKey <= 12000
)
SELECT ISG1.OrderLineNumber, ISG1.Gender, 
ISG1.SalesAmount , 
SUM(ISG1.SalesAmount)
OVER(
PARTITION BY ISG1.Gender 
ORDER BY ISG1.OrderLineNumber 
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS RunningTotal
FROM InternetSalesGender AS ISG1
ORDER BY ISG1.Gender, ISG1.OrderLineNumber;
```

下圖是執行結果，  回傳 6434 列，  FactInternetSales 邏輯讀取 2062 ，  DimCustomer 邏輯讀取 87 ，  Worktable 邏輯讀取 0。  整個效能提升很多：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5gWapTxAxQeh9nb84_KVAH2oIuLKclVMBq4XiVxkc9kdhQ4D4H_grJUhQ_qz9m13FRhMlss1ucC7uKm57jYuSSOhYIgA6Y2j3_9QR4EmAeFjpzQIOL_5B-Isnu2_euvyDaRRnlaGXDRQ/s0/sql-appropriate-query-skill-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjE0j49D-Pnw61s3eJzoOAeHOtEgNg3T_1p8LzpH8G40TT-f0MkrG8X7PSbT9P0B0xKQygga97nGmy05TCXLNlfdQJf1Ho5LgzYVKBjNqZxlU5JVI6_oyuf69V9S4zL8UuCcUrdUBpESjo/s0/sql-appropriate-query-skill-4.png)

## Columnstore Indexes（資料行存放區索引）and Batch Mode

Columnstore Index 和 Batch Mode Processing 都是 SQL 2012 才加進來的新功能，可用來提升資料查詢的效率。

## Columnstore Indexes

**Columnstore indexes** is a new method of storing nonclustered indexes in SQL Server 2012.  which can store index data column by column .  Columnstore indexes can speed up data warehousing queries by a large factor, from 10 to even 100 times!  

- Columnstore indexes accelerate data warehouse queries but are not suitable for OLTP workloads
- If you want to update a table with a columnstore index, you must first drop the columnstore index.
- If you use table partitioning, you can switch a partition to a different table without a columnstore index, update the data there, create a columnstore index  on that table (which has a smaller subset of the data), and then switch the new table data back to a partition of the original table.
- In addition to fact tables, very large dimensions could benefit from columnstore indexes as well. Do not use columnstore indexes for small dimensions.

### Use columnstore indexes for

- Read-mostly workloads.
- Updates that append new data.
- Workflows that permit partitioning or index drop/rebuild.
- Queries that often scan and aggregate lots of data.

### Don't use columnstore indexes when

- You update the data frequently.
- Partition switching or rebuilding indexes doesn’t fit your workflow.
- Your workload includes mostly small lookup queries.

## Batch Mode

SQL Server 2012 includes another important improvement for query processing.   In batch mode processing, SQL Server processes data in batches rather than processing one row at a time.  

ref. [\[SQL SERVER\]\[Denali\] Columnstore Index 執行模式 (Row VS Batch)](http://www.dotblogs.com.tw/ricochen/archive/2012/02/10/68652.aspx)

## Data Compression

Data compression reduces the size of the database,  which helps improve query performance because queries on compressed data read fewer pages from disk and thus use less IO.  

SQL Server supports three compression implementations:

- Row compression ：將欄位的資料型別轉換成可變長度的資料型別
- Page compression：repeated prefixes of values, dictionay compression ..
- Unicode compression：substitutes single-byte storage for Unicode characters that don't truly require two bytes.

You can gain quite a lot from data compression in a data warehouse. Foreign keys are often  repeated many times in a fact table. Large dimensions that have Unicode strings in name  columns, member properties, and attributes can benefit from Unicode compression.

## 參考資料  

- 
- 
-