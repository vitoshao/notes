---
title: 分析函式
layout: default
parent: SQL
nav_order: 6
description: "分析函式"
date: 2013-06-20
tags: [SQL,Query Advance]
postid: "5574609517641268788"
---
以下幾個[分析函數（Analytic Functions）](https://msdn.microsoft.com/zh-tw/library/hh213234%28v=sql.110%29.aspx)都是 SQL 2012 新增功能。  分析函數會根據資料列群組計算出彙總值。  但不同於[彙總函式](https://msdn.microsoft.com/zh-tw/library/ms173454.aspx)，其可以傳回每個群組的多個資料列。 您可以使用分析函數計算群組中的移動平均、最新總數、百分比或前 N 個結果。   

## LAG 和 LEAD

在一個有序的資料集中，有時候我們會需要在一筆資料中，同時顯示前一筆或下一筆資料中相同欄位的資料，如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3QacExUTK5222OkojenFzyZTnZ4MQmuMpks6odMQx2VDd6l-pAJzEXyr6qI0k57d7yebdOOOWjIoNhyphenhyphen7dDTeMOBtkX4kMq5Lql3kTngoifx4yASDh8o1mfkmEWmaaVYy9G16PWESXcXc/s0/sql-lag-lead-1.png)

過去總是要利用子查詢或其他複雜的技巧才能達到這個效果，現在 SQL 2012 提供了二個分析函式 [LAG](http://msdn.microsoft.com/zh-tw/library/hh231256.aspx) 、 [LEAD](https://msdn.microsoft.com/zh-tw/library/hh213125.aspx) 來讓您更容易進行 ROW LEVEL 資料比較。  
```sql
create table #sales(
YY int, MM int, AMOUNT money
)
INSERT #sales values(2009,1,150000)
INSERT #sales values(2009,2,260000)
INSERT #sales values(2009,3,185500)
INSERT #sales values(2009,4,170000)
INSERT #sales values(2009,5,365000)
INSERT #sales values(2009,6,300000)
```

### LAG

取得有序資料集中前一筆相同欄位的資料
```sql
SELECT YY, MM, AMOUNT, LAG(AMOUNT) OVER (ORDER BY MM) as LastMonthAmount
FROM #sales
ORDER BY YY,MM
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3QacExUTK5222OkojenFzyZTnZ4MQmuMpks6odMQx2VDd6l-pAJzEXyr6qI0k57d7yebdOOOWjIoNhyphenhyphen7dDTeMOBtkX4kMq5Lql3kTngoifx4yASDh8o1mfkmEWmaaVYy9G16PWESXcXc/s0/sql-lag-lead-1.png)

### LEAD

取得有序資料集中後一筆相同欄位的資料
```sql
SELECT YY,MM, AMOUNT, LEAD(AMOUNT) OVER (ORDER BY MM) as NextMonthAmount
FROM #sales
ORDER BY YY,MM
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgi_eYokb0KeAV_HBng5fmHW55YzAev_YwEoUTKT4nw4mwiwDC15oiSaL_CmMUwqNWgZhg21mMWlBxDh-80UuQavTnqLIpaCqf7OIuyzFDJx89WsO1zrwIXN-9wYezpYSw_nSR0Pm9P0kM/s0/sql-lead.png)

## FIRST_VALUE 和 LAST_VALUE

### FIRST_VALUE

如果你將一個有序的結果集進行分割，你可以使用 [FIRST_VALUE](http://msdn.microsoft.com/en-us/library/hh213018.aspx) 取得每個分割的第一筆資料。  
```sql
SELECT EmpNum, SwipeTime
,FIRST_VALUE(SwipeTime) over (PARTITION BY EmpNum,CONVERT(date, SwipeTime) 
ORDER BY EmpNum,SwipeTime) as '上班時間'
FROM tblCARD
WHERE CONVERT(date, SwipeTime)='2008-04-01'
ORDER BY EmpNum, SwipeTime
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjczbeNxIpzDC6QNURE3eMQUDpl1OCEmV5RVqx-5a3e2UfwMO-rp2G6hpCVgF3qV8BVx5YnfrcxVhimL5kGe_nV_ibB9OZsEWh6HVdpIH3oh3bG8TeLZKTV3pWdJHY2ppSsGeAzthB9Dak/s0/sql-first-value.png)

### LAST_VALUE

如果你將一個有序的結果集進行分割，你可以使用 [LAST_VALUE](http://msdn.microsoft.com/en-us/library/hh231517.aspx) 取得每個分割的最後一筆資料。  
```sql
SELECT EmpNum, SwipeTime
,LAST_VALUE(SwipeTime) over (PARTITION BY EmpNum,CONVERT(date, SwipeTime) 
ORDER BY EmpNum,SwipeTime
ROWS BETWEEN UNBOUNDED PRECEDING and UNBOUNDED FOLLOWING) as '下班時間'
FROM tblCARD
WHERE CONVERT(date, SwipeTime)='2008-04-01'
ORDER BY EmpNum, SwipeTime
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOaFYFMz4qjGbrTzU_rJhMIWgnDo3Pp6hVbC92Qo-yn1c_oGkSqGEIzqnanIJReljagg_sDttlJvGVGziMn1v9Ig5-wpy2zjDQX0dDHjeugUkx1IjRY3OtN-0LFUUdhKOl9c4MEbH3gew/s0/sql-last-value.png)

## 百分比排名函式

- [CUME_DIST](http://technet.microsoft.com/zh-tw/library/hh231078.aspx) ：統計各筆資料在群組內的「累計分佈」（cumulative distribution ），也就是各筆資料列在群組內的「相對位置」。
- [PERCENT_RANK](http://technet.microsoft.com/zh-tw/library/hh213573.aspx) ：統計各筆資料在群組內的「相對排名」（relative rank）。
- [PERCENTILE_DISC](http://technet.microsoft.com/zh-tw/library/hh231327.aspx) ：依據資料行值的「離散分佈」（discrete distribution）特性來計算百分位數。
- [PERCENTILE_CONT](http://technet.microsoft.com/zh-tw/library/hh231473.aspx) ：依據資料行值的「連續分佈」（continuous distribution）特性來計算百分位數，因為考量的是連續分佈，所以回傳的是一個內插值。

### CUME_DIST 和 PERCENT_RANK

[RANK](http://msdn.microsoft.com/zh-tw/library/ms176102.aspx) 函式會傳回每一筆資料的排名，若有相同資料，則使用相同排名，下一個排名則跳號再編。  若想要以 % 的角度來觀看排名，則可以使用 [CUME_DIST](http://technet.microsoft.com/zh-tw/library/hh231078.aspx) 和 [PERCENT_RANK](http://technet.microsoft.com/zh-tw/library/hh213573.aspx) 函式。   

[CUME_DIST](http://technet.microsoft.com/zh-tw/library/hh231078.aspx) 可用來統計在一堆數值中某數值的累計分佈，其回傳值 = RANK() / 總資料列筆數。  

[PERCENT_RANK](http://technet.microsoft.com/zh-tw/library/hh213573.aspx) 可用來統計在一堆數值中某數值的累計排名，其回傳值 = (RANK()-1) / (總資料列筆數-1) 。  第一個資料列的 [PERCENT_RANK](http://technet.microsoft.com/zh-tw/library/hh213573.aspx) 皆為 0 ，其餘傳回值範圍大於 0 並小於或等於 1。  
```sql
Select categoryid, ProductID, ProductName,UnitPrice, 
RANK() OVER ( Order by UnitPrice ) as [Rank],
CUME_DIST () OVER (ORDER BY UnitPrice) AS CumeDist,
PERCENT_RANK () OVER (ORDER BY UnitPrice) AS PercentRank
From Products
where categoryid=5
Order By UnitPrice
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYvHwhOWCLTHbI0Q4EhGCY9EkA_EoHpzpKCKbTHLk9GpcPpFofQnLCEzRgCELksip86QjNF0CAxGtO93eLqXrmLM0eAlamoxho7DK-MgLmKo42G88RsZULrjQafrW2uIz6qThbaS-Vs5I/s0/sql-CUME_DIST.png)

### PERCENTILE_CONT 和 PERCENTILE_DISC

[RANK](http://msdn.microsoft.com/zh-tw/library/ms176102.aspx) 函式會傳回每一筆資料的排名，而 [PERCENT_RANK](http://technet.microsoft.com/zh-tw/library/hh213573.aspx) 則以百分比數字來描述這個排名。  若你想找特定的排名，例如排在 30% 的資料，你就可以透過 [PERCENTILE_CONT](http://technet.microsoft.com/zh-tw/library/hh231473.aspx) 或 [PERCENTILE_DISC](http://technet.microsoft.com/zh-tw/library/hh231327.aspx) 。  

[PERCENTILE_DISC](http://technet.microsoft.com/zh-tw/library/hh231327.aspx) 會依據指定的排名數值，去找到符合的資料，並回傳值。  

[PERCENTILE_CONT](http://technet.microsoft.com/zh-tw/library/hh231473.aspx) 會依據指定的排名數值，去找到符合的資料，並回傳近似的內插值。  
```sql
Select categoryid, ProductID, ProductName,UnitPrice, 
RANK() OVER ( Order by UnitPrice ) as [Rank],
CUME_DIST () OVER (ORDER BY UnitPrice) AS CumeDist,
PERCENT_RANK () OVER (ORDER BY UnitPrice) AS PercentRank,
PERCENTILE_DISC (0.58) WITHIN GROUP (ORDER BY UnitPrice) OVER (PARTITION BY categoryid) AS PercentDist,
PERCENTILE_CONT (0.58) WITHIN GROUP (ORDER BY UnitPrice) OVER (PARTITION BY categoryid) AS PercentCount
From Products
where categoryid=5
Order By UnitPrice
```

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKgtFNnX-jQi4X9V3z0LNAt8eg94AMGD5pYQSokLFqEZH5V1f5O0-fK6n0TQsh-nqJODPcvDHb0gVwg2rwmANhVkDlKAIEKY0KDZYx5Z76CxPaW1BIH68f4CuLkApEi_u6vxymEzIyzgI/s800/sql-PERCENTILE_CONT.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKgtFNnX-jQi4X9V3z0LNAt8eg94AMGD5pYQSokLFqEZH5V1f5O0-fK6n0TQsh-nqJODPcvDHb0gVwg2rwmANhVkDlKAIEKY0KDZYx5Z76CxPaW1BIH68f4CuLkApEi_u6vxymEzIyzgI/s0/sql-PERCENTILE_CONT.png)

- PERCENTILE_DISC (0.58) ：要傳回排名在 58% 的值，但因為最接近 0.58 的是 0.666 那筆，所以 [PERCENTILE_DISC](http://technet.microsoft.com/zh-tw/library/hh231327.aspx) 就是 21.00
- PERCENTILE_CONT (0.58) ：要傳回排名在 58% 的值，但沒有剛好排名在 0.58 的，所以求 19.5 和 21.00 這二筆的內插值， [PERCENTILE_CONT](http://technet.microsoft.com/zh-tw/library/hh231473.aspx) 得到 20.22
- 
-
## 參考資料  

- [分析函數 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/hh213234.aspx)
-