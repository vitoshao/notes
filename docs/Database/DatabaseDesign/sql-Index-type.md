---
title: 索引
layout: default
parent: Database Manage
nav_order: 12
description: "索引"
date: 2013-11-21
tags: [SQL,Database Manage]
postid: "2640223055431409576"
---
# 索引類別

## 叢集索引（Clustered Index）

## 非叢集索引（Non-Clustered Index）

## 唯一索引（Unique  Index）

## 覆蓋索引（Covering Index）

## 篩選的索引（Filtered Index）

篩選索引是最佳化的非叢集索引，特別適合某些特別狀況的查詢。   篩選索引會使用篩選述詞對資料表中的部分資料列進行索引。   與完整資料表索引相較，設計良好的篩選索引可以提升查詢效能、降低索引維護成本和儲存成本。  

篩選索引適合下列二種情境：

### 1.當查詢的資料行僅少數列有值

當查詢的資料行僅少數列有值時，可以在值的子集上建立篩選索引。  例如，若資料表中有個欄位，大部份的資料都是 NULL ，但是查詢都只會針對非 NULL 值進行篩選，這時就可以針對非 NULL 的資料列建立**篩選索引**。

### 2.當資料表具有異質資料列時

當資料表具有異質資料列時，您可以針對一或多個資料類別建立篩選索引。    例如，若資料表中有個代表年度的欄位，每次查詢都會使用不同年度，這時就可以針對這個欄位建立**篩選索引**。

下面例子是建立非叢集索引並套用篩選的範例：
```sql
--建立一個非叢集索引，並針對 Color 欄位設定篩選
CREATE NONCLUSTERED INDEX idx_SupplierID
ON Products (SupplierID, CategoryID)
WHERE Color IS NOT NULL ;

--查詢資料
SELECT ProductID FROM Products
WHERE SupplierID=15 
AND CategoryID=4 
AND Color IS Not Null
```

若這個非叢集索引沒有使用篩選，執行時會使用叢集索引掃描。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnmQLpVd1swr6Yu9ABf6POcZCeL6ubTTv0drBqbakk2h9PMf8XrGPUMYMWPx-tauqgMpvPp-Q6C0DOkEBWyI71OSTF4LRfaRHzQSEdHQoGYywZmZpQxEE26CT76GXeWmwTlXjwXQK1-hw/s0/sql-filter-index-1.png)

若非叢集索引有使用篩選，執行時就會乖乖的使用索引搜尋。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHoaCBxYqrUiJhWasI_h46MRxakrmslxNbIZpiLWX0YnSpUexsPDppAzLzJHsDosEMv7fUPB5VLAUSs5Ly0l2r3vrUr0O6t73Ff1yko177SXLlOyes5gExMIiwFEfW8KJDeJJ_Cj0PK0I/s0/sql-filter-index-2.png)

## 計算資料行的索引（Computed Column Index）

計算資料行的索引

## Primary XML

## Secondary XML

## Spatial

## Full-text

## Columnstore

## 參考資料  

- [建立叢集索引](http://technet.microsoft.com/zh-tw/library/ms186342.aspx)
- [建立非叢集索引](http://technet.microsoft.com/zh-tw/library/ms189280.aspx)
- [建立唯一索引](http://technet.microsoft.com/zh-tw/library/ms187019.aspx)
- [篩選的索引](http://technet.microsoft.com/zh-tw/library/cc280372.aspx)
- [內含資料行的索引](http://technet.microsoft.com/zh-tw/library/ms190806.aspx)
- [計算資料行的索引](http://technet.microsoft.com/zh-tw/library/ms189292.aspx)
- 
- 
- 
-

# 如何設計有效率的索引

# 認識索引統計

# 建立與修改索引

## 設定索引填滿因子（Index Fill Factors）

## 分析索引破碎程度（Index Fragmentation）

## 減少索引破碎程度

## 更新索引統計

# 追蹤遺漏索引（Missing Indexes）

# 檢查未使用到的索引

## 參考資料  

- [CREATE INDEX (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms188783.aspx)
- 
-