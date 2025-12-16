---
title: 建立索引(2)-資料存放區索引
layout: default
parent: Tuning Performance
nav_order: 14
description: "建立索引(2)-資料存放區索引"
date: 2014-03-06
tags: [SQL,Tuning Performance]
postid: "7253223198019527356"
---
#### 資料行存放區索引（Columnstore indexs）

- supported from SQL2012
- just another nonclustered index on a table
- it can speed up data warehousing queries by a large factor, from 10 to even 100 times.
- A columnstore index is stored compressed.
- Columnstore indexes use their own compression algorithm; you cannot use Row or Page compression on a columnstore index.

# 什麼是「Columnstore Indexs」

在資料倉儲作業中，為了提升效率，常會使用 pre-computed summary table, index views, OLAP Cube 等技術。這些技術雖可顯著地加快查詢處理速度，卻往往缺乏彈性、不易維護，且必須個別針對各個查詢問題進行設計。  

使用「資料行存放區索引」，可以減輕使用者因為其他方案所造成的麻煩。「資料行存放區索引」也可以讓查詢在極短的時間內即求出計算結果，完全無需預先計算。 

傳統的資料是使用資料列（Row）架構，稱為「資料列存放區」(Rowstore) 的方式。  而「資料行存放區」(Columnstore)則是以資料行分組進行儲存。如下圖結構：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7SlZPR6zZxOPMbPQrjdu5tFVI_c9SKJnUTwBSfMS8It6qx58ucSgcDASPzyzRt6qw-8lowOlD1199gMIY-HU17GgKyfdrgMWMn5-9C414CYhFI7WziUzsVizLZSTqTy6lirCBZZGrM8w/s0/sql-columnstore-index.png)

所以這個架構針對資料倉儲中，以維度進行總計或者查詢時，就會有很好的效能提升。主要原因有以下幾點：

- 因為只需要讀取所需的資料行，  所以從磁碟讀取到記憶體，再從記憶體移至處理器快取的資料量也相對變少了。
- 資料行會高度地壓縮，  因此減少了必須讀取及移動的位元組數。
- 大多數的查詢不太需要觸及資料表的所有資料行，  也因此有許多資料行從不會被讀取到記憶體。 此特性加上先進的壓縮法，可以有效改善緩衝集區的使用率，從而降低整體的 I/O。
- 進階查詢執行技術，可以有效率地處理資料行區塊，從而降低 CPU 使用率。

# 如何建立「Columnstore Indexs」

### 建立語法

「Columnstore Indexs」的建立語法：
```sql
CREATE NONCLUSTERED COLUMNSTORE INDEX [index_columnstore] ON [dbo].[SalesOrderDetail]
(
SalesOrderID, LineTotal
)
```

比較一下一般非叢集索的建立語法：
```sql
CREATE NONCLUSTERED INDEX [index_rowstore] ON [dbo].[SalesOrderDetail]
(
SalesOrderID, LineTotal
)
```

### 資料行存放區索引的限制事項

#### 資料表無法更新

具有資料行存放區索引的資料表是無法更新的。此問題有三種因應方式。

1. 先卸除該索引，再執 INSERT、DELETE、UPDATE 或 MERGE 作業，然後重建資料行存放區索引。
2. 使用分割資料表並切換資料分割。進行大量插入時，請將資料插入暫存資料表，並建立暫存資料表的資料行存放區索引，然後將暫存資料表切換至空的資料分割。 如要進行其他更新，請將分割從主要資料表切換至暫存資料表，並停用或卸除暫存資料表的資料行存放區索引，然後執行更新作業，接著再重新建立暫存資料表的資料行存放區索引，最後切換回主要資料表。
3. 將靜態資料置入具有資料行存放區索引的主要資料表，然後新資料及最近可能會有所變更的資料置入另一張具有相同結構描述，但不具資料行存放區索引的資料表中。  將更新套用到包含最新資料的資料表。

#### 支援的資料類型

- int、bigint、smallint、tinyint
- bit
- float、real
- money、smallmoney
- decimal、numeric (但不支援有效位數大於 18）
- char、varchar (但不支援 varchar(max)）
- nchar、nvarchar (但不支援 nvarchar(max)）
- 所有日期和時間資料類型 (有效位數大於 2 的 datetimeoffset 除外)

### 與「Columnstore Indexs」相關的 Catalog View

- [sys.column_store_segments](http://technet.microsoft.com/zh-tw/library/gg509105.aspx) ：
- [sys.column_store_dictionaries](http://technet.microsoft.com/zh-tw/library/gg492082.aspx) ：

# 「Columnstore Indexs」效能比較

## 參考資料  

- [\[MSDN\] 資料行存放區索引（Columnstore Indexes）](http://technet.microsoft.com/en-us/library/gg492088.aspx)
- [\[MSDN\] CREATE COLUMNSTORE INDEX (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/gg492153.aspx)
-