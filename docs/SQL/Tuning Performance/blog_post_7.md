---
title: 效能調校(4)-善用覆蓋索引
layout: default
parent: Tuning Performance
nav_order: 7
description: "效能調校(4)-善用覆蓋索引"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "4021425176725452802"
---
通常大家都知道，在資料庫加入索引，可以增進查詢效率。可是若將查詢欄位加上了索引之後，為什麼使用到 RowNumber() 進行查詢時，效率還是很慢？  在說明之前，可先參考[這篇](http://vito-note.blogspot.com/2013/05/blog-post_6020.html)，瞭解索引的類型。  

下面這二個例子，CreatedTime 和 RevisedTime 這二個欄位都有建立 NonClustered-Index，但分別以該欄位進行排序搜尋後的結果，二者效能上卻差了10倍以上。  問題就在於，這二個索引之中，CreatedTime 欄位沒有使用內含資料行。  

使用圖行執行計畫時，最佳化工具會自動判斷是否有缺少內含資料行，若有則會出現「使用遺漏索引資訊(Missing Index Information)」。  當發生遺漏索引時，查詢引擎無法有效利用索引鍵值找到其他欄位資料的位置。  

## 例一：沒使用內含資料行的索引
```sql
CREATE NONCLUSTERED INDEX IX_tblUser_CreatedTime ON dbo.tblUser(CreatedTime desc) 

SELECT TOP 10 \* FROM 
(
SELECT ROW_NUMBER() OVER ( ORDER BY CreatedTime) AS RowNumber, \*
FROM (
SELECT U.UserID, U.UserName, CN1.CodeName as Sex, Birthday,
U.CreatedTime, U.RevisedTime FROM tblUser U WITH (NOLOCK)
LEFT JOIN tblCodeName CN1 WITH (NOLOCK) ON U.Sex=CN1.Code AND CN1.CodeType='0001'
WHERE 1=1  
) A
) B 
WHERE RowNumber > 3276820
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjn0SQ34DiEv_np_E5te8vghXkX0Xj6vKzhPQWvYAm_9uyvpkwf148NEseoIOGMIKI8nhsO0SfrRZzD35UFIp3p2SE0hahyn4CK1_wLQDG29j2KKZUdP6-Fgt_DaBDgDXx1XoshfnVE56M/s850/create_index.png)

## 例二：使用內含資料行的索引
```sql
CREATE NONCLUSTERED INDEX IX_tblUser_RevisedTime ON dbo.tblUser(RevisedTime desc) 
INCLUDE (UserID,UserName,Sex,Birthday,CreatedTime)

SELECT TOP 10 \* FROM 
(
SELECT ROW_NUMBER() OVER ( ORDER BY RevisedTime) AS RowNumber,\*
FROM (
SELECT U.UserID, U.UserName, CN1.CodeName as Sex, Birthday,
U.CreatedTime, U.RevisedTime FROM tblUser U WITH (NOLOCK)
LEFT JOIN tblCodeName CN1 WITH (NOLOCK) ON U.Sex=CN1.Code AND CN1.CodeType='0001'
WHERE 1=1  
) A
) B 
WHERE RowNumber > 3276820
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihaZo09bvPxTrseyNqUI0YtRGvdyPviCLOMpQQpggQqQajFYmOM_UQzU2qiKFEjJmuSJ2BVeHFNv52lUpo5PW9iUUMtNY1Q29cMeEuJBrSNksEpx311iUKBdTqGTg6HbqYULTBO43PvOc/s850/create_index_include.png)

1.無任何索引

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOr9_aGGlY-7RpEPj6sqyg8gPSDa-Rnr-mbpimPTB7KAS6DxaJF1hYdDdIgV6I8mJJl0C6u97RYU8OKIuPyD4y_DW16JEesaxJ3tsFLqQ5Rhsxa3j6LoBOc1eH7Zyoo5qnXLsvWJOpJCk/s555/no_index.png)

2.nonclustered-index(NP\_ID)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitZHEeTQcbamkWlo6A3_O4q-dnu1TMEOfWdXWmqRuqk0y4tdqtuTqPz1DXurk97qxrV-c1q6eEsm8ydwV0ljwQQC18TtHYgwErBWW6IzqaEN8-pDwMF15KRHRM0BhnoKRhCq3oB_ezbw4/s555/nonclustered_index.png)

3.clustered-index(NP\_ID)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsNVzeldENlYSTNoymSlO2_AkBwCTA2BLo1_SW1GlLUbWocH1b-nd8UUNnzzj-a9cEgo22LRUd8SIWB9LxosuDjklN0_gYr5IS6PsYc_Y67VKkE3sAdWHXtYIyyz5N6BJK0f4uw6X0G1Q/s555/clustered_index.png)

4.index(NP\_ID) include(CHT\_NAME) , PS.叢集索引無法指定Include

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg68ZaZXz1bqAVbsU6YhHlAHaORMrhEMIcp-onzshASSHI78IlA58LlYFWRXknF9cdJRETcRe4olXdmn03x27rnOMudMJ2LxqpXRfZFslSk4XXYAkcQMtms06kiEu8ugIRgpiUVS_YRpNE/s555/noncluster_index_include.png)


- [關於遺漏索引功能](http://msdn.microsoft.com/zh-tw/library/ms345524.aspx)
- [淺談遺漏索引(missing index)](http://sharedderrick.blogspot.com/2010/02/missing-index.html)
- [建立內含資料行的索引](http://msdn.microsoft.com/zh-tw/library/ms190806.aspx)