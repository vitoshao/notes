---
title: 使用統計資料來改善查詢效能
layout: default
parent: Tuning Performance
nav_order: 11
description: "使用統計資料來改善查詢效能"
date: 2013-06-25
tags: [SQL,Tuning Performance]
postid: "8599536611754583744"
---
針對資料表或索引檢視表更新查詢最佳化統計資料。  根據預設，查詢最佳化工具已經視需要更新統計資料，以便改善查詢計畫。不過，在某些情況下，您可以使用 UPDATE STATISTICS 或 sp\_updatestats 預存程序，讓統計資料的更新頻率高於預設更新頻率，藉以改善查詢效能。  

更新統計資料可確保查詢使用最新的統計資料進行編譯。   不過，更新統計資料會導致查詢重新編譯。 我們建議您不要太頻繁地更新統計資料，因為改善查詢計畫與重新編譯查詢所花費的時間之間具有效能權衡取捨。 特定的權衡取捨完全取決於您的應用程式。 UPDATE STATISTICS 可以使用 tempdb 來排序資料列的範例，以便建立統計資料。   
```sql
EXEC sp_updatestats;
```
## 參考資料  

- [UPDATE STATISTICS (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms187348.aspx)
- 
-