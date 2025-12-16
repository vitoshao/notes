---
title: 使用優化器提示來改善查詢效能
layout: default
parent: Tuning Performance
nav_order: 12
description: "使用優化器提示來改善查詢效能"
date: 2013-06-25
tags: [SQL,Tuning Performance]
postid: "8794318087852863257"
---
#### 查詢優化器(Optimizer)

查詢優化器(Optimizer) 是一個 SQL Server 引擎中的元件，負責決定查詢陳述式該如何最佳化執行。  例如 indexes 如何使用、表格排序、合併(JOIN)表格時該如何執行等等。  簡單講，這個元件就是將查詢指令自動最佳化的工具。  

#### [提示(Hints)](http://msdn.microsoft.com/en-us/library/ms187713.aspx)

Hints 是 SQL Server 中的指令，用來告訴 SQL Server 查詢處理器在執行 SQL 陳述式時，應該要強制執行的選項或策略。  當使用 Hints 指令時， Hints 指令可能會覆寫查詢優化器原本預計選取的執行計畫。  

# Optimizer Hints

既然有了 Optimizer ，那為何還需要優化提示 (Optimizer Hint) 指令來優化查詢。  事實上，最佳化是一個相當複雜的工作，雖然在大部份的案例上，SQL Server 的查詢優化器將會正確的評估一個查詢和執行最佳化的可能。  但是在某些情況下可能 Optimizer 會產生一些不好的執行計畫，造成查詢效能的不佳。  這時你就可以透過優化提示 (Optimizer Hint) 改寫查詢計畫。  Hint 的種類可分成下列五種：  

- [Join Hints (聯結提示)](http://msdn.microsoft.com/zh-tw/library/ms173815.aspx)：指定兩個表格結合時的策略。
- [Table Hints (資料表提示)](http://msdn.microsoft.com/zh-tw/library/ms187373.aspx)：在查詢時指定特定的索引
- [Query Hints (查詢提示)](http://msdn.microsoft.com/zh-tw/library/ms181714.aspx)：提示的使用將會影響 GROUP BY 和 UNION 的運作。
- [Lock Hints (鎖定提示)](http://msdn.microsoft.com/zh-tw/library/ms189857.aspx)：使用上去避免不好的鎖定。
- [View Hints (檢視提示)](http://msdn.microsoft.com/zh-tw/library/ms190237.aspx)：指定 Views 的 Indexs 。

一般來說，hints 應該是可以避免少用。因為查詢優化器是非常聰明(大部份的時間)，而且產生的執行計畫是最好的。  所以我們必須確認所認知的部份，不要在不明確的情況下進行使用。  當查詢優化器錯誤而且是我們確認的情況下，就可以透過 hints 來改善，進行最好的測試去並找到更好的方法。

# Query Hints
```sql
<query_hint > ::= 
{ { HASH | ORDER } GROUP 
| { CONCAT | HASH | MERGE } UNION 
| { LOOP | MERGE | HASH } JOIN 
| EXPAND VIEWS 
| FAST number_rows 
| FORCE ORDER 
| IGNORE_NONCLUSTERED_COLUMNSTORE_INDEX
| KEEP PLAN 
| KEEPFIXED PLAN
| MAXDOP number_of_processors 
| MAXRECURSION number 
| OPTIMIZE FOR ( @variable_name { UNKNOWN | = literal_constant } [ , ...n ] )
| OPTIMIZE FOR UNKNOWN
| PARAMETERIZATION { SIMPLE | FORCED }
| RECOMPILE
| ROBUST PLAN 
| USE PLAN N'xml_plan'
| TABLE HINT ( exposed_object_name [ , h1table_hint> [ [, ]...n ] ] )
}
```

# Table Hints
```sql
WITH  ( <table_hint> [ [, ]...n ] )

<table_hint> ::= 
[ NOEXPAND ] { 
INDEX  ( index_value [ ,...n ] ) | INDEX =  ( index_value )    | FORCESEEK [( index_value ( index_column_name  [ ,... ] ) ) ]
| FORCESCAN
| FORCESEEK
| HOLDLOCK 
| NOLOCK 
| NOWAIT
| PAGLOCK 
| READCOMMITTED 
| READCOMMITTEDLOCK 
| READPAST 
| READUNCOMMITTED 
| REPEATABLEREAD 
| ROWLOCK 
| SERIALIZABLE 
| SPATIAL_WINDOW_MAX_CELLS = integer
| TABLOCK 
| TABLOCKX 
| UPDLOCK 
| XLOCK 
} 
```

# Join Hints
```sql
<join_hint> ::= 
{ LOOP | HASH | MERGE | REMOTE }
```
## 參考資料  

- [提示 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms187713.aspx)
- [查詢提示 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms181714.aspx)
- [資料表提示 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms187373.aspx)
- [聯結提示 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms173815.aspx)