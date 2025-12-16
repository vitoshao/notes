---
title: 轉換函式
layout: default
parent: SQL
nav_order: 10
description: "轉換函式"
date: 2013-06-21
tags: [SQL,Query Advance]
postid: "2362894716524329149"
---
# CAST 和 CONVERT
```sql
CAST ( expression as data_type )
CONVERT ( data_type , expression [ , style ] )
```

[CAST](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 和 [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 都是用來執行將指定的 expression 轉換成特定的資料型別。  當轉換過成發生失敗，則會回應錯誤訊息。  

## 日期和時間樣式

當 expression 是日期或時間資料類型時， style 就可以是下表所列的其中一個值。其他值則當做 0 處理。

| style | style | 輸出格式 | 說明 |
| --- | --- | --- | --- |
| ２碼 | ４碼 |
| --- | --- |
|  | 0或100 | mon dd yyyy hh:miAM(PM) | 預設 |
| 1 | 101 | mm/dd/yyyy | 美式 |
| 2 | 102 | yyyy.mm.dd | ANSI |
| 3 | 103 | dd/mm/yyyy | 英式 |
| 8 | 108 | hh:mi:ss |  |
| 11 | 111 | yyyy/mm/dd | 日式 |
| 12 | 112 | yyyymmdd | ISO |
```sql
DECLARE @Time1 DateTime = '2012-05-10 06:05:003';

SELECT 
CAST( @Time1 as varchar(10) ),	--05 10 2012
CAST( @Time1 as varchar(15) ),	--05 10 2012  6:0
CAST( @Time1 as varchar(18) )	--05 10 2012  6:05AM

SELECT 
CONVERT( varchar(18), @Time1, 0 ),		--05 10 2012  6:05AM
CONVERT( varchar(18), @Time1, 101 ),	--05/10/2012
CONVERT( varchar(18), @Time1, 102 ),	--2012.05.10
CONVERT( varchar(18), @Time1, 103 ),	--10/05/2012
CONVERT( varchar(18), @Time1, 108 ),	--06:05:03
CONVERT( varchar(18), @Time1, 111 ),	--2012/05/10
CONVERT( varchar(18), @Time1, 112 )		--20120510
```

上表僅列出幾個較常用的 style ，其它樣式請參考 MSDN ：[CAST 和 CONVERT (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms187928.aspx)

## money 和 smallmoney 樣式

當 expression 是 money 或 smallmoney 時，style 就可以是下表所列的其中一個值。其他值則當做 0 處理。

| style | 輸出格式 |
| --- | --- |
| 0 | #######.00 |
| 1 | #,###,###.00 |
| 2 | #######.0000 |
| 126 | 轉換成 char(n) 或 varchar(n) 時，相當於樣式 2 |
```sql
declare @num as money = 12345678;
select 
convert(char(20), @num, 0)		--  12345678.00
,convert(char(20), @num, 1)		--12,345,678.00
,convert(char(20), @num, 2)		--  12345678.0000
```

# PARSE
```sql
PARSE ( string_value AS data_type [ USING culture ] )
```

[PARSE](http://msdn.microsoft.com/zh-tw/library/hh213316.aspx) 函式算是 [CAST](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 的特例，只能用來將字串型別的資料轉換成日期/時間或數字型別。若轉換過程中發生失敗，則會產生錯誤訊息。

# TRY\_CAST
```sql
TRY_CAST  ( expression AS data_type [ ( length ) ] )
```

[TRY_CAST](http://msdn.microsoft.com/zh-tw/library/hh974669.aspx) 功能同 [CAST](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 函式，都是用來做資料型別的轉換，只是 [TRY_CAST](http://msdn.microsoft.com/zh-tw/library/hh974669.aspx) 若發生轉換失敗，則會傳回 Null，而不會回應錯誤訊息。  但是，如果要求的轉換是**不被允許**的，則 [TRY_CAST](http://msdn.microsoft.com/zh-tw/library/hh974669.aspx) 會失敗，並產生錯誤，例如：要求將整數轉換為 xml 資料類型，這類轉換是不被允許的。

# TRY\_CONVERT and TRY\_PARSE

這是 SQL 2012 新增的功能。

在程式中常會使用 [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 進行轉型，如果使用不當可能就會引發錯誤。  這時候你就可以使用這二個函式偵側潛在的錯誤。  

- [TRY_CONVERT](http://msdn.microsoft.com/zh-tw/library/hh230993.aspx) ：若轉型成功回傳新值，若失敗回傳 NULL 。
- [TRY_PARSE](http://msdn.microsoft.com/zh-tw/library/hh213126.aspx) ：若轉型成功回傳新值，若失敗回傳 NULL 。僅適用於從字串轉換到日期/時間及數字類型。

## TRY\_CONVERT
```sql
TRY_CONVERT ( data_type , expression [, style ] )
```

如果轉換成功，會傳回轉換為指定之資料類型的值；否則會傳回 Null。 
```sql
SELECT TRY_CONVERT(DATETIME, '1753-01-01');     -- return 1753-01-01
SELECT TRY_CONVERT(DATETIME, '1752-12-31');     -- return NULL : datetime does not accept dates earlier than 1753-01-01
```

## TRY\_PARSE
```sql
TRY_PARSE ( string_value AS data_type [ USING culture ] )
```

[TRY_PARSE](http://msdn.microsoft.com/zh-tw/library/hh213126.aspx) 僅適用將字串轉換到日期/時間或數字類型。  如果轉換成功， [TRY_PARSE](http://msdn.microsoft.com/zh-tw/library/hh213126.aspx) 會傳回轉換為指定之資料類型的值；否則會傳回 Null。   [TRY_PARSE](http://msdn.microsoft.com/zh-tw/library/hh213126.aspx) 接受 .NET Framework 所支援的任何文化特性。  
```sql
SELECT TRY_PARSE('1' AS INTEGER);       -- return 1
SELECT TRY_PARSE('B' AS INTEGER);       -- return NULL
```
## 參考資料  

- [轉換函數 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/hh231076.aspx)