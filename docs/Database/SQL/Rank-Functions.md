---
title: 次序函數
layout: default
parent: SQL
nav_order: 4
description: "次序函數"
date: 2013-05-08
tags: [SQL,Query Advance]
postid: "8432349368937361252"
---

排名函數是指可以傳回查詢結果的序號或排名的函式。  自 SQL 2005 關始，SQL Server 開始提供符合 ANSI SQL:2003 標準的排名函數，以方便使用者能夠將資料做相關的其他用途。  

- [ROW_NUMBER](http://msdn.microsoft.com/zh-tw/library/ms186734.aspx) ：為每筆結果加上一個序列號。
- [RANK](http://msdn.microsoft.com/zh-tw/library/ms176102.aspx) ：若遇到相同資料值，則同序列號；後續資料則跳號。
- [DENSE_RANK](http://msdn.microsoft.com/zh-tw/library/ms173825.aspx) ：若遇到相同資料值，則同序列號；後續資料不跳號，接續排名。
- [NTILE](http://msdn.microsoft.com/zh-tw/library/ms175126.aspx) ：根據帶入的參數，將資料分割成 N 群組。

```sql
ROW_NUMBER() OVER ( [ <partition_by> ] < order_by_clause > )
RANK()       OVER ( [ <partition_by> ] < order_by_clause > )
DENSE_RANK() OVER ( [ <partition_by> ] < order_by_clause > )
NTILE (n)    OVER ( [ <partition_by> ] < order_by_clause > )
```

## Row_Number

為每筆結果加上一個序列號
```sql
SELECT ProductName, UnitPrice, ROW_NUMBER() OVER ( Order by UnitPrice )
FROM Products WHERE UnitPrice>8
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjL0SlmdJvKlKRiWTqAmkihQ0nANui2mNBYCRpLgaAssIxM1DcEbD_Xy1F_2TJLYHJtQ9JkVURt_NPi5UrpV_2iikq-kVQ4vy7M0Ty3zKProtTvyxmQEVKGIa7kWgU6WLF_y_1C80j3ESU/s0/sql-row_number.png)

### 排名函數中的 [OVER](http://msdn.microsoft.com/zh-tw/library/ms189461.aspx) 子句

在排名函數中的 [OVER](http://msdn.microsoft.com/zh-tw/library/ms189461.aspx) 子句有二部份

- Partition By ：非必要屬性，用來決定重排的欄位
- Order By ：必要屬性，用來決定排列順序的欄位

當指定 Partition By 時，序列號會以 Partition By 設定的欄位做分隔。  例如，若使用 Partition By SupplierID ，則不同供應商的商品，其序列號會重新排名。  
```sql
SELECT SupplierID, ProductName, UnitPrice, 
ROW_NUMBER() OVER ( Partition by SupplierID Order by UnitPrice )
FROM Products WHERE UnitPrice>8
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfYMXagyqOf2RR4C6H7RQjTikfL21v69qkNVAaPAbtFOrHuzLFvvtOt7yRDbtDScoMiWq-opcWBkUknqY8zxjB-Rw9BtudXfuztYUYeiw1cA4FM7ofPnZqkYd57iyv3Oav6cirC5XRRBY/s0/sql-rank-partition-by.png)

### 範例一：使用 ROW_NUMBER 做分頁

網頁程式常會有個需求，就是要取得第Ｎ頁的資料。  你可以將所有查詢結果丟給程式，由程式去粹取出第Ｎ頁的部份，  也可以利用 [ROW_NUMBER](http://msdn.microsoft.com/zh-tw/library/ms186734.aspx) 函式，直接在資料庫中只回傳該區間的資料。  
```sql
declare @n1 int = 6;
declare @n2 int = 10;

SELECT \* From    
(   
SELECT ProductName, UnitPrice, ROW_NUMBER() OVER ( Order by UnitPrice ) as RankNum
FROM Products WHERE UnitPrice>0
) As RankResult
WHERE RankResult.RankNum between @n1 and @n2 
```

### 範例二：使用 ROW_NUMBER 取得各分類最大值的資料

需求：取出各個供應商最貴的商品資料。  
```sql
SELECT SupplierID, ProductName , UnitPrice FROM 
(
SELECT SupplierID, ProductName , UnitPrice,  ROW_NUMBER() OVER ( PARTITION BY SupplierID ORDER BY UnitPrice DESC ) AS [RANK] 
FROM Products
) A
WHERE [RANK] =1
```

## Rank

若遇到相同資料值，則使用相同的序列號；後續資料則跳號。
```sql
SELECT ProductName, UnitPrice, RANK() OVER ( Order by UnitPrice )
FROM Products WHERE UnitPrice>8
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjNJCmcGU4lN1yS8-jkOP50A24gnzhyphenhyphengQFceq1HXQ17Ewk1oecYRX9MiWxVWG8dRTq3WdD5KX9vYommdHkSMs07nyKcTExQoFXLwGmYeHYFPrgS5Xi0Xyig-h8-03e9zGLox61cA8dopp4/s0/sql-rank.png)

## Dense_Rank

若遇到相同資料值，則使用相同的序列號；後續資料不跳號，接續排名。

```sql
SELECT ProductName, UnitPrice, DENSE_RANK() OVER ( Order by UnitPrice )
FROM Products WHERE UnitPrice>8
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwk1ePnwW6Xz0TWp5DL6MBWlHl0M3115WZA9rxGemKblZCseLakf75oJvPtl9Q_ZH7bXQ8WK5ESFifwiQtvvPYGimGaAawfFZzNJTRNgXCqAMfE6xRSlwdmJF52oWk1iY8lxS3u_Fu6oY/s0/sql-dense_rank.png)

## NTile

NTile 會依照參數，將查詢結果分割成 N 個相同的群組。  
```sql
SELECT ProductName, UnitPrice, NTile(4) OVER ( Order by UnitPrice )
FROM Products WHERE UnitPrice>8 and UnitPrice<12
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgP2O1g9RMwU3FG90En-TP1OTxeVXoxqMVvAGqTdPR2okq7qiP7rjos54QHXW9dsPri94P3ASbregGRRXo-eRb7TJt8weouBJhwf2fPl61bXt6ssWQY3fH_sQ1QU5HtOGvhj3WXACi84NY/s0/sql-ntile.png)

這個函式還滿好用的，下面這個例子，假設我們需求是取出 Products 資料表中，售價排名在前 1/3 的商品，可以這麼做：  
```sql
SELECT ProductID, ProductName, UnitPrice FROM 
(
	SELECT NTILE(3) OVER ( ORDER BY UnitPrice ) AS [RANK] , ProductID, ProductName, UnitPrice
	FROM Products
) A
WHERE [RANK]=1
```

下面這個例子，將 Products 資料表中的供應商，依其商品的平均價格分成二類。  
```sql
SELECT NTILE(2) OVER ( ORDER BY AVG(UnitPrice) ) AS [RANK] , SupplierID
FROM Products
GROUP BY SupplierID
```
## 參考資料  

- [次序函數 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms189798.aspx)
- 
-