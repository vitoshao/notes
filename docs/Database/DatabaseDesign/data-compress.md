---
title: 資料壓縮
layout: default
parent: Database Manage
nav_order: 6
description: "資料壓縮"
date: 2013-10-24
tags: [SQL,Database Manage]
postid: "6336707783510827860"
---

在資料庫中使用「壓縮」，大至可分成幾種狀況。

1. **資料庫壓縮**  
資料庫壓縮並不是透過什麼壓縮技術來縮小資料庫的大小，它只是把沒有用到空間清理出來，即使不壓縮，這些空間也會被資料庫重新使用。  你可以直接對資料庫設定自動壓縮，或者手動使用 [DBCC SHRINKDATABASE](http://technet.microsoft.com/zh-tw/library/ms190488.aspx) 或  [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 陳述式來壓縮整個資料庫或各別的資料庫檔案。

2. **資料壓縮**  
資料壓縮可以用來對資料表和索引進行壓縮，壓縮方法又分成**資料列**和**頁面壓縮**。

3. **備份壓縮**  
[備份壓縮](http://technet.microsoft.com/zh-tw/library/bb964719.aspx)是針對備份媒體進行壓縮，因為備份資料壓縮後會使用較少的空間，I/O相對減少，所以通常可以有效提升備份速度。

本文僅討論資料壓縮。

# 資料壓縮

「資料壓縮」技術是SQL2008才加入的新特性(Enterprise或developer 版本)。它可分兩大類型，「資料列壓縮」和「頁面壓縮」。

使用資料壓縮可以節省空間，縮小資料庫的大小。  資料壓縮也有助於改善 I/O 密集型工作負載的效能，因為資料會儲存在更少的頁面中，所以查詢時的磁碟讀取的頁面也變少了。   但是在與應用程式交換資料時，資料庫伺服器上需要額外的 CPU 資源來壓縮和解壓縮資料。  

#### 可壓縮物件

可以設定資料壓縮的物件包含：

- [Clustered tables](http://msdn.microsoft.com/zh-tw/library/hh213609%28v=SQL.110%29.aspx) (a table with a clustered index)
- [Heap tables](http://msdn.microsoft.com/zh-tw/library/hh213609%28v=SQL.110%29.aspx) (a table without a clustered index)
- Non-clustered indexes
- Indexed views
- Individual partitions of a partitioned table or index

# 資料列壓縮（Row-Level Compression）

當啟用[資料列壓縮](http://msdn.microsoft.com/zh-tw/library/cc280576.aspx)時，SQL Server只會變更資料類型的實體儲存格式(physical storage format)，不會變更任何語法或語意，所以用戶端不需做任何變更。  

它會將所有資料型別轉換成可變長度的資料型別(variable-length data type)，而且不使用空間儲存 Null 值的資料。  所以資料列壓縮針對固定長度型別的資料會有較佳的壓縮效果，也就是對非var開頭的型別資料才有節省量。  它大概會針對下列三個狀況做空間壓縮：  

- 數字部份：使用符合數值的欄位型態。例如 bigint 原本空間大小為 4 bytes，但是若欄位值大小可以儲存在 1 個byte內，則只會使用 1 個byte的空間儲存。
- 字串部份：移除尾端填補字元。例如原先使用 char(50) 型別的欄位，但實際資料只有 10 個byte，壓縮後則會移除尾端填補字元以節省空間。
- 0 或 Null：若欄位值為 0 或 Null，則不使用空間儲存。

使用資料列壓縮的壓縮效果不如頁面壓縮，但是對CPU的要求也比較小。

- Non-clustered indexes do not inherit the compression property of the table,
- but clustered indexes created on a heap inherit the compression state of the heap.

下面語法是執行資料列壓縮的方法：
```sql
ALTER TABLE [tableName] REBUILD WITH (DATA_COMPRESSION = ROW)
```

例：
```sql
--對 Customers 資料表進行資料列壓縮
ALTER TABLE [Customers] REBUILD 
WITH (DATA_COMPRESSION = ROW)

--對 Customers 資料表的所有 Partition 進行資料列壓縮
ALTER TABLE [Customers] REBUILD PARTITION = ALL
WITH (DATA_COMPRESSION = ROW)
```

若使用 SSMS ，則可以執行資料壓縮精靈（Data Compression Wizard）來幫助建立壓縮的腳本。

在要執行壓縮的資料表上，點選「管理壓縮」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlQ9rjD43GijF7B8sc4bWhwacVSadTRAV8SWmhffHZDFe38oCWCCpEIfPvK2sarrr85TJUwKxP67KfSgUKbVOlCbLIqtefYDuN1gIIdXZL9c4Le7DK_0zvvYFcehld7Q5551izwzyrYiI/s0/sql-data-compress-wizard-1.png)

在資料壓縮精靈頁面設定壓縮選項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhruTgxv2vrM13g2lC8leBnIvIGVxOW1mLVhPSzzdcJiwXyQNqFWb0pSEW0eR90w_z9ROFC1xXCVdVwoVJoS7R0xVwxUwbvuQ0ABQl4IGc0UvL6_rdpR66CVwb4gYGH2PdCTEQArG_Dc-4/s0/sql-data-compress-wizard-2.png)

# 頁面壓縮（Page-Level Compression）

當執行[頁面壓縮](http://msdn.microsoft.com/zh-tw/library/cc280464.aspx)時，實際上是由下列三個作業組合而成：  

1. 資料列壓縮：減少不需要用到的空間。
2. 前置詞壓縮：用一個參考值來取代在資料區中相同的 pattern ，。
3. 字典壓縮：找出重複的值，然後在 CIS 區塊中，建立對應的字典資料。

此外要知道的是，系統若要執行頁面壓縮，必須等待這個頁面的資料填滿(filled)，否則只會先進行資料列壓縮，直到該頁面填滿，才會發生頁面壓縮，這時才能取得更大的壓縮比。

下面語法是使用頁面壓縮的方法：  
```sql
ALTER TABLE [tableName] REBUILD WITH (DATA_COMPRESSION = PAGE)
```

# Unicode 壓縮

SQL Server 使用 Standard Compression Scheme for Unicode (SCSU) 演算法的實作來壓縮儲存在資料列或頁面壓縮物件中的 Unicode 值。  對於這些壓縮的物件而言，系統會自動針對 nchar(n) 和 nvarchar(n) 資料行進行 Unicode 壓縮。 不論地區設定為何，Database Engine 都會將 Unicode 資料儲存成 2 位元組。 這稱為 UCS-2 編碼。 對於某些地區設定而言，SQL Server 中的 SCSU 壓縮實作最多可以節省 50% 的儲存空間。 

Unicode compression is applied automatically when you apply either row or page compression.

# 評估壓縮 (Estimating Compression)

若要評估是否該使用壓縮技術，您可以先執行內建的預存程序 [sp_estimate_data_compression_savings](http://msdn.microsoft.com/zh-tw/library/cc280574.aspx) 。它可以幫助您評估是否要使用這個壓縮技術。
```sql
--評估PAGE壓縮
EXEC sp_estimate_data_compression_savings 'dbo', 'myTable', NULL, NULL, 'PAGE';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDY7_5ysHvSWSTkslMWPuY7YY8oMbl9phW4bgu20F_oWGPz0ALSNMWjpfyuM-HNBj1qvao1dpgFCbl3dL6KLNDsDo7OcvlePWej1q9G_9sKhLm9xP5L9Ri2PEfu7li8aisNjPDeGVUGlM/s850/sql-sp_estimate_data_compression_savings-1.png)

下表是使用 [sp_spaceused](http://msdn.microsoft.com/zh-tw/library/ms188776.aspx) 查看經過資料壓縮後的 Table 大小。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEieSB-m7fK7wtYAXi9eoaIVquxPAwBFS3EncXTXP1O1Qy4pxkJzl-LgjMu0JWWFy9DCl5hmZYxMgUv600xxyG4KAoiv6wa2mHQ_J1ldaZaOWPSPpDV5UNzTpsKAOyFLbewkOv0LuVeehGI/s0/sql-compression-result.png)


## 參考資料  

- [堆積 (無叢集索引的資料表)](http://msdn.microsoft.com/zh-tw/library/hh213609%28v=SQL.110%29.aspx)
- [資料壓縮](http://msdn.microsoft.com/zh-tw/library/cc280449.aspx)
- [資料列壓縮實作](http://msdn.microsoft.com/zh-tw/library/cc280576.aspx)
- [頁面壓縮實作](http://msdn.microsoft.com/zh-tw/library/cc280464.aspx)
- 
-