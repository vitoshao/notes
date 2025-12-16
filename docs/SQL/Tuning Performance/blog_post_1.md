---
title: 效能調校概說
layout: default
parent: Tuning Performance
nav_order: 1
description: "效能調校概說"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "1065487609267845149"
---
# 效能評估

## 效能評估指標

在學習效能調校之前，首先要知道如何評估效能，該使用什麼數據來判斷效能。  在 SQL Server 中，可以利用以下三種衡量標準(metric)來評估效能的好壞。  

### 查詢成本（Query Cost）

【查詢成本】是 SQL Server 內部的一種衡量標準，  它同時考量了查詢時所用到的 CPU 和 I/O 資源的使用狀況，所以它得到的數據是一種估計值。  也因此，用它的數據來評估一個 TSQL 的效能是比較客觀的，因為它比較不會受測試環境引響。  不過，要知道的是，有些執行項目它是不會列入查詢成本統計的，例如自訂函式(UDFs)或CLR程序等等。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEglTLWg6-fhGf9KK5X7w-O9s7cJlC_8kgc2TZ7KIrmRWQ0-utLJqy03t1WlF3QOgnNbdqLa9NW3kh-34vXV-kl9GS6C1NH54Lk2p9hF3QzuoVxWA5IjRHZx6P0lgk3NOD5ktNvqa38mlGY/s0/sql-query-cost.png)

### 磁碟分頁讀取次數（Page Reads）

【Page Reads】表示 SQL Server 執行查詢時，用在讀取磁碟分頁的次數。  要取得這個數據，你必須啟用 [SET STATISTICS IO](http://msdn.microsoft.com/en-us/library/ms184361.aspx) 設定：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgvfIR5kc3Xtplz4zx1vSL4fN-pwGm5qEDSp7D454sOM7WXic6Ctb0ftS37Is-sXTyQxNhzJn7wcFjRi3KYkDldYrpCh_e09QMMCmdPYvFvoDxFlULuYg9f2BidZxnQin-xEdZCju2XKM0/s850/sql-page-reads-counter.png)

上圖中的邏輯讀取數據就是 Page Reads ，這個查詢的總分頁讀取次數就是 693+59。  這個數據沒有考量 CPU 資源，所以它不像查詢成本是一種估計值。  但是，同樣的，它也不會將自訂函式(UDFs)或CLR程序列入統計。

### 查詢執行時間（Query Execution Time）

【執行時間】這個數據雖然很直接易懂，但是它卻是最不固定的衡量標準，因為不同的硬體配備或執行時的系統環境都會引響這個數據值。  要取得這個數據，你必須啟用 [SET STATISTICS TIME](http://msdn.microsoft.com/zh-tw/library/ms190287.aspx) 設定：

## 瞭解Query的執行順序

在調校效能時，首先要知道每個 SQL Query 陳述式都可細分成數個部分，瞭解各個部分的執行次序將有助於理解 SQL Server 是如何產生查詢結果的。  依據陳述式中是否含有 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 子句，其執行順序可分成二類：  

### 不含UNION子句的執行順序

1. FROM, JOIN, APPLY, and ON
2. WHERE
3. GROUP BY and aggregate functions (such as SUM, AVG, and so on) thatare included in the query
4. HAVING
5. SELECT
6. ORDER BY
7. TOP
8. FOR XML

### 含UNION子句的執行順序

1. FROM, JOIN, APPLY, and ON
2. WHERE
3. GROUP BY and aggregate functions (such as SUM, AVG, and so on) thatare included in the query
4. HAVING
5. TOP
6. UNION and SELECT
7. ORDER BY
8. FOR XML

## 範例說明
```sql
SELECT TOP(2) OrderID,ProductID,UnitPrice from OrderDetails where OrderID=10248
UNION ALL
SELECT TOP(2) OrderID,ProductID,UnitPrice from OrderDetails where OrderID=10249
ORDER BY ProductID
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDvdDNbYdUKdsQJ8pYaTZnzt9aQ4JNCs08lXmQw8IYKCDKP5CrfIKcLuDgH-02WMjXz4AtH2PEnIQTvTCwWq-_Rpvv0n5Dybu6CcHZhLVx0ENnqhY5JiELLaTSxzlaM0ftuxfA7X0aLlw/s0/sql-union-order-by-1.png)

上面這個例子，首先要知道的是，UNION 只允許使用一個 ORDER BY，而且 ORDER BY 要放在最後一個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式中，而且是針對整個 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 後的結果做排序。  所以如果你希望的是二個查詢結果先做完排序再 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 在一起，那麼就必須改成以下做法。  
```sql
WITH R1 AS(
SELECT TOP(2) OrderID,ProductID,UnitPrice from OrderDetails where OrderID=10248
ORDER BY ProductID
),
R2 AS(
SELECT TOP(2) OrderID,ProductID,UnitPrice from OrderDetails where OrderID=10249
ORDER BY ProductID
)
SELECT \* FROM R1
UNION ALL
SELECT \* FROM R2
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJgn93ZnI6yKoVGtPl111_rCHY3Pgp2WD5s4N6J4yiFGYna2PhVzuIesiwggGntTrDwFL_pvpPzA_Y9nE5WodVJCRL_qglJl2g_F-2wtmB0EWJZS5P52wCvt07dFixzZarDrdNvy3wZ2g/s0/sql-union-order-by-2.png)

# 效能調校

要調校查詢效能，可以從以下幾個方法著手：  

- rewriting query
- normalizing/de-normalizing tables
- add/remove index
- combination above item

## 使用圖形執行計畫評估效能（Graphical Execution Plan）

圖形執行計畫是 SQL Server 提供給開發人員或 DBA 用來分析查詢執行成本的工具，用來做為 T-SQL 指令碼效能調校的參考。

[更多的介紹請看這篇](http://vito-note.blogspot.com/2013/05/blog-post_2862.html)

## 使用查詢參數提升效能（Serarch Arguments, SARG）

查詢參數指的就是 WHERE 查詢條件，這裡是說，要效能好就要正確使用查詢參數。  正確使用 SARG 可以縮小回傳的資料量，而且使用 Seek 運算來提升執行效能。  就像底下二道陳述式，雖然結果是相同的，但是執行過程中，一個使用 seek ，一個使用 scan ，效能自然差別很大。  
```sql
SELECT COUNT(1) From Orders WHERE YEAR(OrderDate)=1997

SELECT COUNT(1) FROM Orders
WHERE OrderDate >= '1997/01/01' AND OrderDate < '1998/01/01';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizTpF-SX8kSUiJGfvnuUTX7BHgA6hwqEOhXX7o6AdjaDh5tYRHJue4q8yYAhdUmcFPEEywIR98Kc5f1BBoZrXENt-yMUPOzmbsu8wpeM1h2ZI3KeBYCIHMb7NpDEdkwJSIeYbmeKy-d-s/s0/sql-sarg.png)

#### 查詢參數規則

為了讓最佳化器（optimizer ）使用索引，可參考下列幾點查詢參數的使用規則：

1. 避免使用以下的運算子：NOT 、 != 、 &lt;&gt; 、 !&gt; 、 !&lt; 、 NOT EXISTS 、 NOT IN 、 NOT LIKE  
這類運算子會造成「負向查詢」，易讓「最佳化器」無法有效地使用索引
2. 減少使用區域變數 (@local) ，除非變數值在執行前就可以決定。不固定的變數值，最佳化器無法核對分配步驟。
3. 避免在欄位上執行運算 (columnA \* 100 = const)，這不會被視為有效的搜尋引數。可以用下面方法取代： (columnA = const / 100)
4. 避免使用以下類型的子查詢： "...where column operator (select column from table)" 。因為直到執行查詢時，才會知道常數運算式的值。
5. 

很多時候 IN 可以使用 EXISTS 代替：
select num from a where exists(select 1 from b where num=a.num)

## 提升效能的案列

[請參考這篇](http://vito-note.blogspot.com/2013/05/blog-post_1930.html)

# Finding Out Which Queries to Tune

## 參考資料  

- 
- 
- 
- 
- 
- 
- [了解參數探測行為(1)](http://www.dotblogs.com.tw/ricochen/archive/2012/01/03/64152.aspx)
- [調校 SQL 以徹底改善應用程式效能](http://blog.xuite.net/j2ee/code/15120677)
- [淺談執行計畫快取和重用](http://www.dotblogs.com.tw/ricochen/archive/2013/04/08/100987.aspx)