---
title: 效能調校-案例探討
layout: default
parent: Tuning Performance
nav_order: 4
description: "效能調校-案例探討"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "5974754146108218831"
---
以下探討幾種不同類型的陳述式與查詢效能的關連。

## 使用 串接查詢條件

寫程式常會提供使用者查詢資料的介面，以取得使用者輸入的查詢條件，再將查詢條件串接成 TSQL。可是針對使用者沒輸入的欄位，我們總希望可以回傳所有資料，所以底下是我們常用的串接方法：
```sql
SELECT UserID
FROM tblUser
WHERE 1=1
AND ( UserName=@UserName OR @UserName='' )
...
```

底下是這個陳述式的執行計畫，可以發覺，即使我們針對 UserName 欄位設定了索引，執行計畫卻沒有使用 Seek .

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilFZjPMWeNtp0r4kH85J3P7OOWgZESYJuWIYvsNxaKEj4jvuRP9XpVaahrQOj5oJjv0-9yGIZvHMV6HgUA4JOzfvUluOV_uPF7c4X9R6P8nBJLv0MBUX6oCzawgBZwQaAMXCKe3Fj6Tk4/s0/sql-tuning-case-emptyquery-1.png)

這是因為 @UserName='' 根本就沒有運用到索引，所以只能進行 Scan ，如果改成以下的 TSQL ，效能就可以立即提升。所以若要串接查詢條件還是得勤勞一點，如果使用者沒有輸入的欄位，就不要出現在 WHERE 條件中比較好。
```sql
SELECT UserID
FROM tblUser
WHERE 1=1
AND (UserName=@UserName)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibZkzQvi_Bsj9vi0a6eKklhn7IQo46urVSfuxTYj1gHZnbvdN-Er26Se4SLhKXmrWDJcHN5PHFlM60jRvbU_71YQ1SXaTOAVDOHAb3vIh7Q7ShAWUIuB_oWQZ1USq-BIVudTtmoeW3g9w/s0/sql-tuning-case-emptyquery-2.png)

注意

如果在 SP 中使用 WHERE UserName=@UserName ，若 @UserName 是 SP 的參數，因為此時這個變收是個不確定值，所以還是會使用 scan 運算。這個就是[參數探測(Parameter Sniffing )](http://blogs.technet.com/b/mdegre/archive/2012/03/19/what-is-parameter-sniffing.aspx)問題造成的。如果要避免這個問題，可以使用區域變數取代，這樣才會使用 seek 運算。如：
```sql
DECLARE @LocalUserName nvarchar(20);
SET @LocalUserName = @UserName

SELECT UserID FROM tblUser WHERE UserName = @LocalUserName
```

## 使用「查詢參數」（Search Arguments, SARGs）

A **search argument (SARG)** is a filter expression that is used to limit the number of rows returned by a query and that can use an index **seek** operation that substantially improves the performance of the query.

並不是所有的 filter expression 都是 SARG ，例如：LEFT(Name, 1) = 'A' 或者 MONTH(OrderDate) = 6 ，這類經過額外運算處理的欄位都不算合法的 SARG。如果一個查詢中沒有合法的 SARG ，那麼查詢將採用 index scan 或 table scan 。
```sql
Select COUNT(1) From Orders　WHERE YEAR(OrderDate)=1997
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9EAqcDOP_W4Y3s0PWTvtr8DALX22vsJDfvM0_A4630yt8XvlC_1eQxyaCK8P7dFrIR0OFz7waTdiuBIHF4bqVlqA_oWICY_c5AvFXRBnVa6Omre1E0-XpQeX1H-4IPUE1Xjc_TAys8WI/s0/sql-tuning-sarg-invalid.png)
```sql
Select COUNT(1) From Orders WHERE OrderDate between '1997/1/1' and '1997/12/31 23:59:59'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1psSTxjkk-vIJPqqdTrQpUwDjbR5-fBTGIOZvVQN47ySo7EO_MrVGz2Tn3oqNN9CrLBmM0n7UQj-H3Sw4lRlp3JtBvBMWJSUSSw3rvA0fINuMgmRtO5Ta0RiSCwp3mG52elL18GzmWkE/s0/sql-tuning-sarg.png)

## 使用 Joins

- To optimize queries, one of the first basic strategies is to minimize the number of join clauses used.
- Another consideration is that outer joins incur more cost than inner joins because of the extra work needed to find the unmatched rows.

相較於「子查詢 (Subquery)」，若能用 JOIN 完成的查詢，一般會比較建議使用後者。原因除了 JOIN 的語法較容易理解外，在多數的情況下，JOIN 的效能也會比子查詢較佳；但這並非絕對，也有的情況可能剛好相反。

## 使用 Sub Query

子查詢有分獨立子查詢(Uncorrelated Subqueries)和關連子查詢(Correlated Subqueries)，不管哪一種子查詢都會引響效態。如果是獨立子查詢，其對效態引響還比較小，因為它只會執行一次。但是如果是關連子查詢，那就要非常小心使用，它隨著資料量的成長，效能會下降的非常快。
```sql
SELECT p.ProductID, p.ProductName, p.UnitPrice
FROM Products AS p
WHERE p.UnitPrice > ( SELECT AVG(P2.UnitPrice) FROM Products P2)
```
```sql
SELECT p.ProductID, p.ProductName, p.UnitPrice
FROM Products AS p
WHERE Exists ( SELECT ProductID FROM OrderDetails OD WHERE OD.ProductID = P.ProductID )
```

### 子查詢調校範例
```sql
SELECT 
OrderID,OrderDate,
(Select Top(1) UnitPrice FROM OrderDetails OD WHERE OD.OrderID=O.OrderID ORDER BY Quantity) as UnitPrice,
(Select Top(1) Quantity FROM OrderDetails OD WHERE OD.OrderID=O.OrderIDORDER BY Quantity) as Quantity
FROM Orders O
WHERE OrderID=10256
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiopMUoRqX2qmfiDDeh9cZH2uSTdMoCmdguaBSKxz7Go60kzUkGr2DSuduIYQUylyri4OPtqSuP0-CzJJfmKCaNBvFIQaD3pWDeP7lVCT-rbay94yLarouBayeAPMiddL-gvXARP_qKiD8/s850/sql-subquery-tuning-1.png)
```sql
SELECT 
OrderID,OrderDate,A.\*
FROM Orders O
OUTER APPLY (
Select Top(1) UnitPrice,Quantity FROM OrderDetails OD WHERE OD.OrderID=O.OrderID ORDER BY Quantity
) as A
WHERE OrderID=10256
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjISsInj-dK9RjFqhtVhnjuXary6Utmybvx0Vfnxu68nkQCaiHkr8zLbcfoPSUGnLm3jBFV6k4P1_KrGgAG219I8b98dnTPtvxwMAtc6FExejoyhfaoUhLPZZ0BQRlP3gvnwvQEOJiM3J4/s850/sql-subquery-tuning-2.png)
```sql
WITH A as (
SELECT O.OrderID, O.OrderDate, OD.UnitPrice, OD.Quantity, 
ROW_NUMBER() OVER (PARTITION BY O.OrderID ORDER BY Quantity DESC) as RowNum
FROM OrderDetails OD
INNER JOIN Orders O ON O.OrderID=OD.OrderID
WHERE O.OrderID=10256 
)
SELECT \* FROM A WHERE ROwNum=1
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcLY2tfUgt9MBzz2AdS4xrfPDSbF-6I49C3aFAGFGNE4Rnd9OeDKFHVbH1QPywU42BjgxznObG_uc-jxnscnqLMEyb3-HQQQHdcx2SuvxefLY8GLnAYsKMBqaLTfGv4GVnOtgR2yROK2A/s850/sql-subquery-tuning-3.png)

## 使用 Scalar UDFs

Scalar UDFs是一種很常用的自訂函式類型，但不幸地，它也會降底執行效率。原因是這種函式無法被最佳化器擴展和最佳化到主要的查詢計畫中。執行計畫只會叫用這個函式，不會最佳化，也不會將它列入執行成本統計之中。
```sql
SELECT OrderID, OrderDate, dbo.fuGetEmpName(1)
FROM Orders
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHgC7xwFz2ELz4IdmpHuXQOsjLpWGbPL0aFFEnYcohbt9MVh0GdFtKYy77O_ZTR4HAoKcZKkYr7JnLYPcWGfD2cGx0Ww-nTgt7Y6uJ24VfS0JdtDBseHNGhkCbfzzD_75k-JVqLBbsA1Y/s0/sql-tuning-case-scalar-udf-1.png)

上面範例使用 UDF ，但由執行計畫中卻都看不到任何 Employees 資料表的參考。  
下面範例改採內嵌關連子查詢，可以發覺，雖然執行成本的數據看似較大，但是整體執行時間卻比較快。這是因為執行計畫可以連同內嵌關連子查詢一起做最佳化處理，所以執行效率會比較好。
```sql
SELECT OrderID, OrderDate, ISNULL(
(
SELECT FirstName + ' ' + LastName
FROM Employees
WHERE EmployeeID = O.OrderID
), 'NOT FOUND')
FROM Orders O
```
```sql
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQmGq_NSfCvVKrp8exu2sDraWFY24mGwZG_GkMZVTXxRaXGkptTGt47lh19nDUHn7PBCPP2PjDtaQ1JHeI20u2LsCMWDhK_o5Yz6F1cgpXbD3e9m380anAGaAfDDu2EAgXtHjkhjkNFvE/s0/sql-tuning-case-scalar-udf-2.png)

## 使用 Table-Valued UDFs

Table-Valued 的 UDFs 共有三種型態，其中二種是使用 TSQL 撰寫，另一種是 CLR 語言。

- T-SQL inline table-valued UDF
- T-SQL multistatement table-valued UDF
- CLR table-valued UDF

這三種 UDF 的行為各不相同：

### inline t-v UDF

使用內嵌資料表值函式，它就像使用一個可以接受參數的view，最佳化工具可處理這類型自訂函式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkIxxHhJzo0HRez045In6PHH-_IVeMC6D0fidQSuF_ZvLRl-ckq_MwTEF_umPrnqh2wRNis4PnUcXMAokxrQPmmMjyoZPt-CGnpsXCktwicho76iPZxxqb9i4-ZNcmt_KW3cB5IAphcdo/s0/sql-tuning-case-inline-udf.png)

### mulitstatement t-v UDF

使用多陳述式資料表值函式，它就像使用預存程序，然後產生暂存資料表給外部查詢使用。這種函式必須先被完全執行完畢，才能將結果提供給外部查詢使用。也就是說，如果這個函式必須回傳一百萬筆資料，整個查詢作業就要先等這一百萬筆資料處理完之後才會開始。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOSXarD-o_rXFzF8S4Se6PCN06IuJq9Flo-9C8wXNTWQ7kssM8WMOl8nUKePQG0bMFv8GqhSBeotl5pkn62t5b9kH8f4oA3-s6sIgs4kLGWmJQdME1rQGVMiWFrRQYlVensdr7XCOati0/s0/sql-tuning-case-ms-udf.png)

### CLR t-v UDF
待。。。

## 使用 Cursors

使用 Cursor 會大大的降低執行效率，因為在 Cursor 迴圈中的 FETCH 陳述式，每次執行時就像是執行一次 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式，而且還不能像 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式一樣可以被最佳化。

You should try to rewrite cursor logic into one or more set-based statements (SELECT, INSERT, UPDATE, DELETE, or MERGE). If you cannot convert the cursor logic to a set-based statement, consider implementing the logic using a CLR stored procedure ora table-valued UDF instead (depending on the functionality you need).
## 參考資料

- [查詢效能微調 (SQL Server Compact)](http://technet.microsoft.com/zh-tw/library/ms172984.aspx)
- 
-