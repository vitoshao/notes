---
title: 彙總查詢
layout: default
parent: SQL
nav_order: 5
description: "彙總查詢"
date: 2013-05-08
tags: [SQL,Query Advance]
postid: "1199091924238638345"
---
**Aggregate functions** allow you to apply calculations on values in a column.   Adding the **GROUP BY** clause allows you to provide aggregate on subsets of the data.  

# Aggregate Functions

當使用彙總函式時要先知道的是：這些函式都會忽略 **NULL** 欄位值。  底下是 SQL 2008 支援的彙總函式：  

- [AVG](http://msdn.microsoft.com/zh-tw/library/ms177677.aspx) ：
- [COUNT](http://msdn.microsoft.com/zh-tw/library/ms175997.aspx) ：
- [COUNT_BIG](http://msdn.microsoft.com/zh-tw/library/ms190317.aspx) ：同 [COUNT](http://msdn.microsoft.com/zh-tw/library/ms175997.aspx) 函式，只是回傳型別為 bigint 。
- [MAX](http://msdn.microsoft.com/zh-tw/library/ms187751.aspx) ：
- [MIN](http://msdn.microsoft.com/zh-tw/library/ms179916.aspx) ：
- [SUM](http://msdn.microsoft.com/zh-tw/library/ms187810.aspx) ：
- [CHECKSUM_AGG](http://msdn.microsoft.com/zh-tw/library/ms188920.aspx) ：傳回群組中各個值的總和檢查碼。
- [STDEV](http://msdn.microsoft.com/zh-tw/library/ms190474.aspx) ：標準差 (standard deviation )
- [STDEVP](http://msdn.microsoft.com/zh-tw/library/ms176080.aspx) ：擴展標準差 (standard deviation for the population)
- [VAR](http://msdn.microsoft.com/zh-tw/library/ms186290.aspx) ：變異數 (variance)
- [VARP](http://msdn.microsoft.com/zh-tw/library/ms188735.aspx) ：擴展變異數 (variance for the population)
- [GROUPING](http://msdn.microsoft.com/zh-tw/library/ms178544.aspx) ：是否彙總 [GROUP BY](http://technet.microsoft.com/zh-tw/library/ms177673.aspx) 清單中指定的資料行運算式。

以下針對幾特定函式簡列說明：

## STDEV（標準差）

簡單來說，[標準差](http://zh.wikipedia.org/zh-tw/%E6%A8%99%E6%BA%96%E5%B7%AE)是一組數值自平均值分散開來的程度的一種測量觀念。  一個較大的標準差，代表大部分的數值和其平均值之間差異較大；一個較小的標準差，代表這些數值較接近平均值。  例如：{3, 5, 9, 11} 和 {5, 6, 8, 9} 這二組數的平均值都是 7 ，但是第一組的標準差為 3.65 ，第二組的標準差為 1.82 。  
```sql
DECLARE @AgeList1 table( age int NOT NULL);
DECLARE @AgeList2 table( age int NOT NULL);

insert @AgeList1 values (3),(5),(9),(11)
insert @AgeList2 values (5),(6),(8),(9)

SELECT AVG(age) From @AgeList1		--7
SELECT AVG(age) From @AgeList2		--7

SELECT STDEV(age) From @AgeList1	--3.65148371670111
SELECT STDEV(age) From @AgeList2	--1.82574185835055
```

## STDEVP（變異數）

變異數是由資料值與期望值的差異計算而來，也是用來描述一組數的密度(變化程度)。變異數的平方根＝標準差。  
```sql
SELECT VAR(age) From @AgeList1	--13.3333333333333
SELECT VAR(age) From @AgeList2	--3.33333333333333
```

## CHECKSUM_AGG

[CHECKSUM_AGG](http://msdn.microsoft.com/zh-tw/library/ms188920.aspx) 會傳回群組中各個值的總和檢查碼，常用在判斷集合中的資料是有被異動過。
```sql
select * from Emp
SELECT DepNo, CHECKSUM_agg(EmpNo) CheckValue FROM Emp GROUP BY DepNo
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQKbaj5Av32x574iiHyXM8W-MuSWcddaABjJKyXwYF8aOuguSuF-cYbmPTf4ZmWxZhg7ru2X8PNOmf3hrQyR-RTrCwdIeXBXY-4w0B20vlOgKpTRtW4XNCfHBuXa-KGtWiRfZa0_a0FvI/s0/sql-checksum_agg-1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivu-H-rMuC8LWotbq-VkvVSu1ZON3ieqYf6wJbK6QNwdowLefJoM8pzdC_U9qHEq-xOdi_UbF7gndJIUwX6Phyphenhyphenjk_L5WaGdMJmEunNsGiCVNcQn25R3B1DqX5QdKE4uhQKMEeIuXlmXSY/s0/sql-checksum_agg-2.png)
```sql
SELECT CHECKSUM_AGG(EmpNo) FROM Emp WHERE EmpNo in (1)          -- 1
SELECT CHECKSUM_AGG(EmpNo) FROM Emp WHERE EmpNo in (1,2)        -- 3
SELECT CHECKSUM_AGG(EmpNo) FROM Emp WHERE EmpNo in (1,2,3)      -- 0
SELECT CHECKSUM_AGG(EmpNo) FROM Emp WHERE EmpNo in (1,2,3,4)    -- 4
SELECT CHECKSUM_AGG(EmpNo) FROM Emp WHERE EmpNo in (1,2,3,4,5)  -- 1
```

[CHECKSUM_AGG](http://msdn.microsoft.com/zh-tw/library/ms188920.aspx) 的參數值必須是整數，也就是只適用於整數型別的欄位，它的檢查法就是將所有欄位值以二進位互斥運算。

## CHECKSUM_AGG VS CHECKSUM

- [CHECKSUM_AGG](http://msdn.microsoft.com/zh-tw/library/ms188920.aspx) 是彙總函式，它可以彙總多筆資料中的單一欄位做**總和檢查碼**計算。
- [CHECKSUM](http://msdn.microsoft.com/zh-tw/library/ms189788.aspx) 不是彙總函式，它可以針對一筆資料中的所有欄位做**總和檢查碼**計算。
```sql
--依據EmpNo,EmpName欄位，求每一筆資料的總和檢查碼
SELECT CHECKSUM(EmpNo,EmpName) FROM Emp

--依據所有欄位，求每一筆資料的總和檢查碼
SELECT CHECKSUM(*) FROM Emp 

--以下方法可用來比對二個 table 中的資料是否一樣   
SELECT CHECKSUM_agg(CHECKSUM(*)) FROM Emp 　
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOtUStu9hc6V_V58v1hf2XFL7CQQQ1ttf2Iod6Nt-HjZt7WAYS1Jn10BLQovmq43CimLbmhSCSHEzQn4VwHFzkpYNTHUtlYXM_57wiHoHQ0AlRKePeD4aJyVcHBe8T2TLWUGsoNbo8VOo/s0/sql-checksum.png)

# 使用 Group By 子句

當使用彙總函式時，用來分組統計。
```sql
SELECT OrderID, 
COUNT(1) as [COUNT], 
SUM(UnitPrice) as [SUM], 
AVG(UnitPrice) as [AVG], 
MAX(UnitPrice) as [MAX], 
MIN(UnitPrice) as [MIN]
FROM OrderDetails 
GROUP BY OrderID
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIdn18a127sjF9brgI8OxZtgzkVq-t6aBFmgYazisVqT7aci2MskzO-xBw79RrUphqthyAe1-OVqbC74k1MUry9Vs_KlkBSc1fToPly-fSDTb6ak8wVerzLmFdLRF7SeP6MNs6HJBT-ic/s0/sql-aggregate-groupby.png)

# COMPUTE 和 COMPUTE BY

- [COMPUTE](http://msdn.microsoft.com/zh-tw/library/ms181708.aspx) 和 [COMPUTE BY](http://msdn.microsoft.com/zh-tw/library/ms181708.aspx) 這二個子句必須與彙總函式搭配使用。
- COMPUTE 和 COMPUTE BY 子句可以針對特定欄位，再額外彙總一個結果。
- COMPUTE 和 COMPUTE BY 子句要放在整個查詢陳述式的後方。
- 可搭配的彙總函式包含 SUM、AVG、MIN、MAX 或 COUNT。
```sql
SELECT *
FROM tblGrade
COMPUTE SUM(Grade);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilG-KlrfZRKRtQS-JecONBsMUQO1EaTa8le5WggucpfVuedT7aSejFpPjj68QN9FSbM3MNltq0bIJLCwfLqbgo_eakWVeI_OV2KGcJ05jpgdkSxLF8Fwjuk0fndwYyl9Hi9NZ0dF-S7C8/s0/sql-compute.png)

[COMPUTE BY](http://msdn.microsoft.com/zh-tw/library/ms181708.aspx) 則可以根據特定欄位值，進行群組彙總。
```sql
SELECT StudentNo,ClassID,Grade
FROM tblGrade
ORDER BY StudentNo, ClassID 
COMPUTE SUM(Grade) BY StudentNo;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDtwkNjBcffcAOYTCpt51DOdm-vfUPpCENoOHOF-FGtlLTn5cnuN_sDbQ2KTzahN3dhWEF7xSdTxUdwWXTl6_lPiA7qZozJYRgqSgwdG2LBnY_2TYFmEx0au3j5rGR26h4VQRwl4Cc-0k/s0/sql-compute-by.png)

下一版的 Microsoft SQL Server 將不再提供此功能。請勿在新的開發工作中使用此功能，並且儘速修改使用此功能的應用程式。 改用 [ROLLUP](http://msdn.microsoft.com/zh-tw/library/bb522495.aspx) 。

# 使用 Having 子句

指定群組或彙總的搜尋條件。  
HAVING 只能搭配 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式使用。   
HAVING 通常用在 [GROUP BY](http://technet.microsoft.com/zh-tw/library/ms177673.aspx) 子句中。   
當未使用 [GROUP BY](http://technet.microsoft.com/zh-tw/library/ms177673.aspx) 時，HAVING 的行為會如同 WHERE 子句。   
```sql
SELECT 
CASE WHEN (GROUPING(TypeA)=1) THEN 'don''t care' ELSE TypeA END  as TYPEA,
CASE WHEN (GROUPING(TypeB)=1) THEN 'don''t care' ELSE TypeB END  as TYPEB,
CASE WHEN (GROUPING(TypeC)=1) THEN 'don''t care' ELSE TypeC END  as TYPEC,
SUM(Qty) AS QtySum
FROM @List
GROUP BY TypeA , GROUPING SETS (TypeB,TypeC)
HAVING SUM(Qty)>10
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmWEyVckqr0Foim6johzUtz8sdOq_C77qphfHEcUKmtQaaZ-tdkJurtJRDsNprUs69-jsQtfwffIFQelO42-xn8-rcXSOE_X6nWtgSjBlkOS42zOtvR-It_SvMOtgUGnYi_s1v_J2jsdY/s0/sql-having.png)
## 參考資料  

- [GROUPING SETS 對等項目](http://msdn.microsoft.com/zh-tw/library/bb510427.aspx)
- 
-