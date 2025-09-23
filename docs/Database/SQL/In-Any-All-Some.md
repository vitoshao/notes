---
title: IN、ANY、ALL、SOME
layout: default
parent: SQL
nav_order: 9
description: "ANY、ALL、SOME"
date: 2013-06-22
tags: [SQL,Query]
postid: "5381795169147261419"
---
[IN](http://msdn.microsoft.com/zh-tw/library/ms177682.aspx) 、   [ALL](http://msdn.microsoft.com/zh-tw/library/ms178543.aspx) 、   [SOME](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx) 、   [ANY](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx)   都算是[邏輯運算子](http://msdn.microsoft.com/zh-tw/library/ms189773.aspx)。  

- [IN](http://msdn.microsoft.com/zh-tw/library/ms177682.aspx) 後面可接一個**清單**，或是一個**單一欄位結果集的子查詢**。
- [ALL](http://msdn.microsoft.com/zh-tw/library/ms178543.aspx) 、   [SOME](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx) 、   [ANY](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx) 後面只可以接**單一欄位結果集的子查詢**。

**語法**
```sql
scalar_expression { = | < > | ! = | > | > = | ! > | < | < = | ! < } 
{ ALL | SOME | ANY } ( subquery ) 
```

- [SOME](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx) 和 [ANY](http://msdn.microsoft.com/zh-tw/library/ms175064.aspx) 是相同意義的。使用這二個運算子時，只要 scalar\_exp 符合 subquery 中任何一個值，判斷式就會成立。
- [ALL](http://msdn.microsoft.com/zh-tw/library/ms178543.aspx) 運算子則必須 scalar\_exp 符合 subquery 中所有的值，判斷式才成立。
- [ALL](http://msdn.microsoft.com/zh-tw/library/ms178543.aspx) 是一個受限制的 SELECT 陳述式，其中不允許使用 ORDER BY 子句和 INTO 關鍵字。


#### 範例說明

建立測試資料
```sql
CREATE TABLE #T1(ID int) ;

INSERT #T1 VALUES (1) ;
INSERT #T1 VALUES (2) ;
INSERT #T1 VALUES (3) ;
INSERT #T1 VALUES (4) ;

CREATE TABLE #T2(ID int) ;

INSERT #T2 VALUES (2) ;
INSERT #T2 VALUES (3) ;
```

例１：
```sql
SELECT * FROM #T1 WHERE ID IN ( SELECT ID FROM #T2 )

SELECT * FROM #T1 WHERE ID < SOME ( SELECT ID FROM #T2)

SELECT * FROM #T1 WHERE ID < ANY ( SELECT ID FROM #T2)

SELECT * FROM #T1 WHERE ID < ALL ( SELECT ID FROM #T2)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsrRHPL1_MejQbBeYYI339i1k92JERSXvG8gVQNA7q4zOSwDf_4Ws24FiH8M3451dKWMx5pytRWrIpTEw4xBSdsQ8g7pvHU3Z6P11fEG5G_FIIPwymh6Tv1IAfIQeXEfvkSo2vvbpBP3U/s0/sql-in-some-any-all.png)

例２：
```sql
SELECT * FROM #T1 WHERE ID NOT IN ( SELECT ID FROM #T2 )

SELECT * FROM #T1 WHERE ID <> SOME ( SELECT ID FROM #T2)

SELECT * FROM #T1 WHERE ID <> ANY ( SELECT ID FROM #T2)

SELECT * FROM #T1 WHERE ID <> ALL ( SELECT ID FROM #T2)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgL885fcSQSKsNUQGSKT8f7U_OLZzUg46Pvf9-Py5jwHBOPqp-fEW911eoxoo6PWMuRzrkGCMbTT3wJV0ihXTpVrWQtkwyyhFQuKMw-d00QINoS99vE2s_wOUdzVLM0BRrPmgX1cJgLSSw/s0/sql-in-some-any-all-2.png)