---
title: IsNull and Coalesce
layout: default
parent: SQL
nav_order: 8
description: "使用內建函式查詢"
date: 2013-05-08
tags: [SQL,Query]
postid: "8695664206124201464"
---
# IsNull and Coalesce

- [ISNULL](http://msdn.microsoft.com/zh-tw/library/ms184325.aspx) ：以指定的取代值來取代 NULL。
- [COALESCE](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx) ：傳回其引數中第一個非 Null 的運算式。

[ISNULL](http://msdn.microsoft.com/zh-tw/library/ms184325.aspx) 是用來判斷欄位的值，若為 NULL 的時候，可以回傳自己所要的值。   [COALESCE](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx) 就像是 CASE WHEN 陳述式，會回傳引數中第一個非 Null 的運算式，如果所有引數都是 NULL， [COALESCE](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx) 便會傳回 NULL。   

## ISNULL
```sql
declare @T table (id char(2), age decimal(3,1));
insert @T values ('01', 10.0);
insert @T values ('02', null);
insert @T values ('03', 15.0);

--總計函式會忽略Null欄位
select AVG(age) from @T             -- return 12.5

--若為 NULL 回傳 11
select AVG(isnull(age,11)) from @T  -- return 12
```

## COALESCE

[Coalesce](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx) ：回傳引數中第一個非 Null 的運算式
```sql
declare @T table ( productid varchar(2), price1 int, price2 int, price3 int);

insert @T values ('01',10,11,12);
insert @T values ('02',null,11,12);
insert @T values ('03',null,null,12);
insert @T values ('04',10,null,12);
insert @T values ('05',null,null,null);

select productid , Coalesce(price1, price2, price3) from @T
select productid , Coalesce(price1, price2, price3, 15) from @T
```

![](http://127.0.0.1:8080/_images/cs/sql-coalesce-1.png)![](http://127.0.0.1:8080/_images/cs/sql-coalesce-2.png)

# 日期時間函式

## 取得系統和日期時間
```sql
SELECT GetDate()			--datetime 
SELECT SysDateTime()		--datetime2
SELECT SysUtcDateTime()		--datetime2
SELECT SysDateTimeOFFSET()	--datetimeoffset
SELECT ToDateTimeOffset(SysDateTimeOFFSET(), '+02:00') 
```

![](http://127.0.0.1:8080/_images/cs/sql-get-datetime.png)

### 較高精確度的日期時間

| 函式名稱 | 說明 | 備註 |
| --- | --- | --- |
| [SYSDATETIME](http://technet.microsoft.com/zh-tw/library/bb630353.aspx) | 取得主機上的時間 | 傳回 datetime2(7) 型別 |
| [SYSDATETIMEOFFSET](http://technet.microsoft.com/zh-tw/library/bb677334.aspx) | 取得主機上的時間 | 傳回 datetimeoffset(7) 型別 |
| [SYSUTCDATETIME](http://technet.microsoft.com/zh-tw/library/bb630387.aspx) | 取得即時的 UTC 時間 | 傳回 datetime2(7) 型別 |
```sql
SELECT 'SysDateTime()', SysDateTime()

SELECT 'SysDateTimeOFFSET()', SysDateTimeOFFSET()

SELECT 'SysUtcDateTime()', SysUtcDateTime()
```

![](http://127.0.0.1:8080/_images/cs/sql-datetime-1.png)

### 較低精確度的日期時間

| 函式名稱 | 說明 | 備註 |
| --- | --- | --- |
| [GETDATE](http://technet.microsoft.com/zh-tw/library/ms188383.aspx) | 取得主機上的時間 | 傳回 datetime 型別 |
| [GETUTCDATE](http://technet.microsoft.com/zh-tw/library/ms178635.aspx) | 取得即時的 UTC 時間 | 傳回 datetime 型別 |
| [CURRENT_TIMESTAMP](http://technet.microsoft.com/zh-tw/library/ms188751.aspx) | 取得主機上的時間 | 同 GetDate() ,但這個不是參數型函式 |
```sql
SELECT 'GetDate()', GetDate()

SELECT 'GetUTCDate()', GetUTCDate()

SELECT 'CURRENT_TimeSTAMP', CURRENT_TimeSTAMP  -- 同 GetDate() ,但這個不是參數型函式
```

![](http://127.0.0.1:8080/_images/cs/sql-datetime-2.png)

## 分解日期時間中的各個部份

- [DATEPART](http://technet.microsoft.com/zh-tw/library/ms174420.aspx) ：取得日期時間欄位指定部份的值，回傳整數型別
- [DATENAME](http://technet.microsoft.com/zh-tw/library/ms174395.aspx) ： 取得日期時間欄位指定部份的值，回傳字串型別
- [DAY](http://technet.microsoft.com/zh-tw/library/ms176052.aspx) ：
- [MONTH](http://technet.microsoft.com/zh-tw/library/ms187813.aspx) ：
- [YEAR](http://technet.microsoft.com/zh-tw/library/ms186313.aspx) ：

[DATEPART](http://technet.microsoft.com/zh-tw/library/ms174420.aspx) 和 DateName 用法相同，只是回傳型態不同。它們都帶有一個 datepart 參數，這個參數可使用的值如下例：
```sql
SELECT DateName(YY,@Now) YY,    -- year
DateName(MM,@Now) MM,    -- month
DateName(DD,@Now) DD,    -- day
DateName(HH,@Now) HH,    -- hour
DateName(MI,@Now) MI,    -- minute
DateName(SS,@Now) SS,    -- second
DateName(MS,@Now) MS     -- millisecond

SELECT DatePart(QQ,@Now) QQ,    -- quarter 本年度第幾季
DatePart(WK,@Now) WK,    -- week 本年度第幾週
DatePart(DY,@Now) DY,    -- dayofyear 本年度第幾天
DatePart(mcs,@Now) mcs,  -- microsecond
DatePart(ns,@Now) ns     -- nanosecond

SELECT Year(@Now) YEAR, 
Month(@Now) MONTH, 
Day(@Now) DAY
```

![](http://127.0.0.1:8080/_images/cs/sql-datetime-3.png)

## 日期時間的運算

常用的時間運算函式有：

- [DATEDIFF](http://technet.microsoft.com/zh-tw/library/ms189794.aspx) ：Returns the count (signed integer) of the specified datepart boundaries crossed between the specified startdate and enddate.
- [DATEADD](http://technet.microsoft.com/zh-tw/library/ms186819.aspx) ：Returns a specified date with the specified number interval (signed integer) added to a specified datepart of that date.
```sql
DATEADD (datepart , number , date )
DATEDIFF ( datepart , startdate , enddate )
```

PS. 雖然在 [DATENAME](http://technet.microsoft.com/zh-tw/library/ms174395.aspx) 函式中，若 datepart=dy ，是表示今年的天數，但是在 [DATEDIFF](http://technet.microsoft.com/zh-tw/library/ms189794.aspx) 函式中是表示二個日期相差的天數， quarter和 week 也是如此。
```sql
DECLARE @Time1 DateTime = '2012-05-10 11:35:41';
DECLARE @Time2 DateTime = GetDate();
SELECT DateDiff(YY, @Time1, @Time2) Diff_YY, 
DateDiff(MM, @Time1, @Time2) Diff_MM, 
DateDiff(DD, @Time1, @Time2) Diff_DD,
DateDiff(HH, @Time1, @Time2) Diff_DD,
DateDiff(MI, @Time1, @Time2) Diff_MI,
DateDiff(SS, @Time1, @Time2) Diff_SS,
DateDiff(MS, @Time1, @Time2) Diff_MS

SELECT DATEADD(YY, 1, @Time1),
DATEADD(QQ, 1, @Time1),
DATEADD(MM, 1, @Time1)
DATEADD(HH, 1, @Time1)
```

![](http://127.0.0.1:8080/_images/cs/sql-datetime-4.png)

新版的 SQL 還增加了底下幾個函式：

- [EOMONTH](http://technet.microsoft.com/zh-tw/library/hh213020.aspx) ：傳回指定日期的當月最後一天，回傳型別為 date。(SQL2012)
- [SWITCHOFFSET](http://technet.microsoft.com/zh-tw/library/bb677244.aspx) ：依指定時間變換時間。(SQL2008)
- [TODATETIMEOFFSET](http://technet.microsoft.com/zh-tw/library/bb630335.aspx) ：變更某個時間的時區值。(SQL2008)
```sql
--取得當月最後一天的日期
DECLARE @date DateTime = '2012-03-10 06:05:003'; 
SELECT EOMONTH ( @date, -1 )  AS 'Last Date in Previous Month';		-- 2012-02-29
SELECT EOMONTH ( @date )	  AS 'Last Date in This Month'			-- 2012-03-31
SELECT EOMONTH ( @date, 1 )   AS 'Last Date in Next Month';			-- 2012-04-30

DECLARE @Time2 datetimeoffset = '2012-05-10 06:05:03.00000 +8:00';

--將 Time2 由 +8:00 時區的時間轉換成 -04:00 時區的時間
SELECT SWITCHOFFSET(@Time2, '-04:00')	
-- Return: 2012-05-09 18:05:03.0000000 -04:00

--將 Time2 的時區值變更成 -04:00 時區值
SELECT ToDateTimeOffset(@Time2, '-04:00')	
-- Return: 2012-05-10 06:05:03.0000000 -04:00
```

## 建立 DateTime、Date、Time

以下６個函式都是 SQL2012 才有的支援：

| 函式名稱 | 說明 | 備註 |
| --- | --- | --- |
| [TIMEFROMPARTS](http://technet.microsoft.com/zh-tw/library/hh213398.aspx) | 建立時間 | 傳回 time 型別 |
| [DATEFROMPARTS](http://technet.microsoft.com/zh-tw/library/hh213228.aspx) | 建立日期 | 傳回 date 型別 |
| [SMALLDATETIMEFROMPARTS](http://technet.microsoft.com/zh-tw/library/hh213396.aspx) | 建立日期時間 | 傳回 smalldatetime 型別 |
| [DATETIMEFROMPARTS](http://technet.microsoft.com/zh-tw/library/hh213233.aspx) | 建立日期時間 | 傳回 datetime 型別 |
| [DATETIME2FROMPARTS](http://technet.microsoft.com/zh-tw/library/hh213312.aspx) | 建立日期時間 | 傳回 datetime2 型別 |
| [DATETIMEOFFSETFROMPARTS](http://technet.microsoft.com/zh-tw/library/hh231077.aspx) | 建立日期時間 | 傳回 datetimeoffset  型別 |
```sql
TIMEFROMPARTS           ( hour, minute, seconds, fractions, precision )
DATEFROMPARTS           ( year, month, day )
SMALLDATETIMEFROMPARTS  ( year, month, day, hour, minute )
DATETIMEFROMPARTS       ( year, month, day, hour, minute, seconds, milliseconds )
DATETIME2FROMPARTS      ( year, month, day, hour, minute, seconds, fractions, precision )
DATETIMEOFFSETFROMPARTS ( year, month, day, hour, minute, seconds, fractions, hour_offset, minute_offset, precision )
```

## 其他與日期時間相關的函式

## SET DATEFIRST

- [@@DATEFIRST](http://technet.microsoft.com/zh-tw/library/ms187766.aspx)
- [SET DATEFIRST](http://technet.microsoft.com/zh-tw/library/ms181598.aspx)
```sql
SELECT @@DATEFIRST

SET DATEFIRST 1
```

## SET DATEFORMAT

設定系統如何解讀日期字串之年月日的順序。 

格式有效的參數為： mdy、dmy、ymd、ydm、myd 和 dym 。
```sql
SET DATEFORMAT dmy;
DECLARE @date1 datetime = '31/12/2012 09:01:01';
SELECT @date1

SET DATEFORMAT ymd;
DECLARE @date2 datetime = '2012/12/31 09:01:01';
SELECT @date2
```

## SET LANGUAGE

設定工作階段和系統訊息的語言環境

- [@@LANGUAGE](http://technet.microsoft.com/zh-tw/library/ms177557.aspx)
- [SET LANGUAGE](http://technet.microsoft.com/zh-tw/library/ms174398.aspx)
- [sp_helplanguage](http://technet.microsoft.com/zh-tw/library/ms187357.aspx)
```sql
select @@LANGUAGE 

SET LANGUAGE 繁體中文
SET LANGUAGE us_english

sp_helplanguage 
```

# 字串函式

## SQL2012 新功能

- [FORMAT](http://msdn.microsoft.com/zh-tw/library/hh213505.aspx) ：將數值或日期資料，轉換成指定格式的字串。(SQL2012)
- [CONCAT](http://msdn.microsoft.com/zh-tw/library/hh231515.aspx) ：傳回串連兩個以上之字串值的結果字串。

### FORMAT

[FORMAT](http://msdn.microsoft.com/zh-tw/library/hh213505.aspx) 是用來將 date/time 或 number 型別的資料，轉換成具有文化特性格式的字串值。

#### 日期類型的Format

傳回不同文化特性的日期格式
```sql
DECLARE @d DATETIME = '2011/12/31';

SELECT FORMAT ( @d, 'D', 'en-US' ) AS '美式'
,FORMAT ( @d, 'D', 'en-gb' ) AS '英式'
,FORMAT ( @d, 'D', 'de-de' ) AS '德式'
,FORMAT ( @d, 'D', 'zh-tw' ) AS '台式'; 

SELECT FORMAT ( @d, 'd', 'en-US' ) AS '美式(簡)'
,FORMAT ( @d, 'd', 'en-gb' ) AS '英式(簡)'
,FORMAT ( @d, 'd', 'de-de' ) AS '德式(簡)'
,FORMAT ( @d, 'd', 'zh-tw' ) AS '台式(簡)'; 
```

![](http://127.0.0.1:8080/_images/cs/sql-format-1.png)

#### 數值類型的Format

傳回不同格式的數值
```sql
declare @i money = 1.5491

SELECT @i as 'Original Format'
,FORMAT(@i, 'N', 'en-us') AS 'Number Format'
,FORMAT(@i, 'G', 'en-us') AS 'General Format'
,FORMAT(@i, 'C', 'en-us') AS 'Currency Format'
```

![](http://127.0.0.1:8080/_images/cs/sql-format-2.png)

#### 傳回自訂格式
```sql
SELECT 
FORMAT(223939889,'(0#)-####-####') AS 'Phone Format',
FORMAT(952123456,'0###-######') AS 'Mobil Format';
```

![](http://127.0.0.1:8080/_images/cs/sql-format-3.png)

## 子字串和位置

- [SUBSTRING](http://msdn.microsoft.com/zh-tw/library/ms187748.aspx) ：
- [LEFT](http://msdn.microsoft.com/zh-tw/library/ms177601.aspx) ：
- [RIGHT](http://msdn.microsoft.com/zh-tw/library/ms177532.aspx) ：
- [LTRIM](http://msdn.microsoft.com/zh-tw/library/ms177827.aspx) ：
- [RTRIM](http://msdn.microsoft.com/zh-tw/library/ms178660.aspx) ：
- [REPLACE](http://msdn.microsoft.com/zh-tw/library/ms186862.aspx) ：取代子字串
- [STUFF](http://msdn.microsoft.com/zh-tw/library/ms188043.aspx) ：將指定字串插入另一個字串
- [CHARINDEX](http://msdn.microsoft.com/zh-tw/library/ms186323.aspx) ：指定字串，找出首次出現的位置
- [PATINDEX](http://msdn.microsoft.com/zh-tw/library/ms188395.aspx) ：依指定字串樣本，找出首次出現的位置（字串樣式支援萬用字）
- [REPLICATE](http://msdn.microsoft.com/zh-tw/library/ms174383.aspx) ：重複指定字串
- [SPACE](http://msdn.microsoft.com/zh-tw/library/ms187950.aspx) ：重複空白字串
```sql
declare @tmp varchar(50) = ' abcd bc d32ef ';

SELECT 
SUBSTRING(@tmp, 1,3),	--abc
LEFT(@tmp, 3),			--ab
RIGHT(@tmp, 3),			--ef
RTRIM(LTRIM(@tmp))		--abcd bc d32ef

SELECT 
CHARINDEX('bc', @tmp),		--2
CHARINDEX('g', @tmp),		--0
PATINDEX('%bc%', @tmp),		--2
PATINDEX('%[0-9]%', @tmp)	--10

SELECT 
REPLICATE('abc',3),					--abcabcabc
'a' + SPACE(2) + 'b',				--a  b
REPLACE('abc123abc','123','xyz')	--abcxyzabc
```
```sql
DECLARE @d DATETIME = GETDATE();
SELECT 
FORMAT( @d, 'dd/MM/yyyy', 'en-US' ) AS 'DateTime Result',   --27/09/2012       
FORMAT(123456789,'###-##-####') AS 'Custom Number Result';  --123-45-6789
```

## 字串變換

- [CONCAT](http://msdn.microsoft.com/zh-tw/library/hh231515.aspx) ：合併
- [LOWER](http://msdn.microsoft.com/zh-tw/library/ms174400.aspx) ：轉大寫
- [UPPER](http://msdn.microsoft.com/zh-tw/library/ms180055.aspx) ：轉小寫
- [REVERSE](http://msdn.microsoft.com/zh-tw/library/ms180040.aspx) ：反轉
- [STR](http://msdn.microsoft.com/zh-tw/library/ms189527.aspx) ：將數值轉字串
```sql
-- 將 123.45 轉成六個位數的字元字串
-- 數字的小數點後部份會捨入到一個小數位數。
SELECT STR(123.45, 6, 1);	-- 123.5
```

## 字串長度

- [LEN](http://msdn.microsoft.com/zh-tw/library/ms190329.aspx) ：回傳字數 (number of characters)
- [DATALENGTH](http://technet.microsoft.com/zh-tw/library/ms173486.aspx) ：回傳字元數 (number of bytes)
```sql
SELECT 
LEN('ABC'),LEN('ＡＢＣ'),				--3,3
DATALENGTH('ABC'),DATALENGTH('ＡＢＣ')	--3,6
```

## 如何在字串中反向找到特定字元的位置

使用 CharIndex 可以找到特定字元在字串中首次出現的位置，如果要由後面開始算呢？目前 SQL 內建函式好像沒有直接這樣的功能，不過可以利用以上的方法來取得。  假設有個字串「abc.def.xyz」，你要取得最後一個「.」的位置，你可以這麼做：  
```sql
declare @temp varchar(100) = 'abc.def.xyz'

select LEN(@temp) - CHARINDEX('.', REVERSE(@temp))
```

# 數學函式

## 算術函式

- [ROUND](http://msdn.microsoft.com/zh-tw/library/ms175003.aspx) ：捨入到指定的長度或有效位數。 (指定有效位數，以下四捨五入)
- [FLOOR](http://msdn.microsoft.com/zh-tw/library/ms178531.aspx) ：傳回小於或等於指定數值運算式的最大整數。 (取整數，無條件捨去小數)
- [CEILING](http://msdn.microsoft.com/zh-tw/library/ms189818.aspx) ：傳回大於或等於指定數值運算式的最小整數。 (取整數，小數無條件進位)
- [ABS](http://msdn.microsoft.com/zh-tw/library/ms189800.aspx) ：絕對值
- [SQRT](http://msdn.microsoft.com/zh-tw/library/ms176108.aspx) ：求平方根
- [SQUARE](http://msdn.microsoft.com/zh-tw/library/ms173569.aspx) ：求平方數
- [SIGN](http://msdn.microsoft.com/zh-tw/library/ms188420.aspx) ：判斷正或負，負數值回傳-1，零值回傳0，正數值回傳1。
- [POWER](http://msdn.microsoft.com/zh-tw/library/ms174276.aspx) ：傳回指定乘冪之指定運算式的值，例 Power(2,3) = 2^3^。
- [PI](http://msdn.microsoft.com/zh-tw/library/ms189512.aspx) ：傳回 [PI](http://msdn.microsoft.com/zh-tw/library/ms189512.aspx) 的常數值

若要求除法後的整數和餘數，沒有函式，可以直接使用 / 和 % 運算
```sql
select 11/3     -return 3
select 11 % 3   -return 2
```

####  [ROUND](http://msdn.microsoft.com/zh-tw/library/ms175003.aspx) 

對數值欄位指定有效位數，以進行四捨五入計算
```sql
ROUND ( numeric_expression , length [ ,function ] )   //length : 取到小數第幾位，若為負值，表示取到整數第幾位
```

範例１：
```sql
select ROUND(123.9994, 3)	--123.9990 
select ROUND(123.9995, 3)	--124.0000 
select ROUND(123.4545, 2)	--123.4500 
select ROUND(123.4545, -2)	--100.0000
select ROUND(748.58, -1)	--750.00 
select ROUND(748.58, -2)	--700.00 
select ROUND(748.58, -3)	--1000.00 執行批次時發生錯誤。錯誤訊息為: 算術溢位。
select ROUND(748.58, -4)	--0 
```

範例２：

假設資料庫中以下數字 {10.5, 12, 3.7, 1, 3, 9, 13.5}，型別為NUMBER(5,2)，要如何找出帶小數的值 {10.5, 3.7, 13.5}？  
1.轉字串後，再判斷字串含不含 dot   
2.執行 mod 1 後，判斷含不含餘數   
3.使用 [ROUND](http://msdn.microsoft.com/zh-tw/library/ms175003.aspx) ＝＞ (VALUE - ROUND(VALUE,0)) &lt;&gt; 0

## 三角或指數函式

- [SIN](http://msdn.microsoft.com/zh-tw/library/ms188377.aspx) ：三角正弦函式 (Sine)
- [COS](http://msdn.microsoft.com/zh-tw/library/ms188919.aspx) ：三角餘弦函式 (CoSine)
- [TAN](http://msdn.microsoft.com/zh-tw/library/ms190338.aspx) ：三角正切函式 (Tangent)
- [COT](http://msdn.microsoft.com/zh-tw/library/ms188921.aspx) ：三角餘切函式 (CoTangent)
- [ASIN](http://msdn.microsoft.com/zh-tw/library/ms181581.aspx) ：反正弦函式 (ArcSine)
- [ACOS](http://msdn.microsoft.com/zh-tw/library/ms178627.aspx) ：反餘弦函式 (ArcCosine)
- [ATAN](http://msdn.microsoft.com/zh-tw/library/ms181746.aspx) ：反正切函式 (ArcTangent)。
- [DEGREES](http://msdn.microsoft.com/zh-tw/library/ms178566.aspx) ：以弧度換算對應的角度(θ)
- [RADIANS](http://msdn.microsoft.com/zh-tw/library/ms189742.aspx) ：以角度換算對應的弧度(rad)
- [LOG10](http://msdn.microsoft.com/zh-tw/library/ms175121.aspx) ：以 10 為基底的對數。
- [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) ：自然對數，以 e 為基底的對數，其中 e 是大約等於 2.718281828 的無理常數。Log(10) ≒ 2.3
- [EXP](http://msdn.microsoft.com/zh-tw/library/ms179857.aspx) ：自然指數，以 e 為基底的指數。Exp(3) = e^3^ ≒ 20.0855

#### 角度與弧度

![](http://127.0.0.1:8080/_images/cs/radian-degree.png)

![](http://127.0.0.1:8080/_images/cs/redian-degree-formule.png)

![](http://127.0.0.1:8080/_images/cs/sql-radian-degree.png)

## 亂數函式

- [RAND](http://msdn.microsoft.com/zh-tw/library/ms177610.aspx) ：傳回 0 到 1 (不含) 的虛擬隨機(pseudo-random) float 值。

# 邏輯函數

## IsNull
```sql
ISNULL ( para1 , para2 )
```

若 para1 為 NULL，則回傳 para2
```sql
DECLARE @x int = 10, @y int = null
SELECT ISNULL ( @x , 0 )        --10
SELECT ISNULL ( @y , 0 )        --0
```

## NullIf
```sql
NULLIF ( expression1 , expression2 )
```

[NULLIF](http://msdn.microsoft.com/zh-tw/library/ms177562.aspx) 接受二個表示式，若 exp1=exp2 則回傳 NULL，否則回傳 exp1 。
```sql
declare @a int;
declare @b int;
set @a=2; 
set @b=2;
select NULLIF(@a, @b)	--NULL

set @a=2; 
set @b=3;
select NULLIF(@a, @b)	--2
```

## IIF

[IIF](http://msdn.microsoft.com/zh-tw/library/hh213574.aspx) 函式為 SQL2010 新功能，判斷運算式的真偽。
```sql
IIF ( boolean_expression, true_value, false_value )
```

- true\_value 及 false\_value 可以是任何類型。
- true\_value 及 false\_value 也可以是 null ，但是不能同時都為 null 。
- 若 boolean\_expression 錯誤，則函式會中斷。
```sql
DECLARE @a int = 45, @b int = 30;
SELECT IIF ( @a > @b, 'TRUE', 'FALSE' ) AS Result;              -- return TRUE
SELECT IIF ( @a > @b, 1, 0 ) AS Result;                         -- return 1

DECLARE @x int = 45, @y varchar(2) = '0';
SELECT IIF ( @x > @y, 'TRUE', 'FALSE' ) AS Result;              -- return TRUE
SELECT IIF ( @x / @y > 0 , 'TRUE', 'FALSE' ) AS Result;         -- error 
```

## CHOOSE

[CHOOSE](http://msdn.microsoft.com/zh-tw/library/hh213019.aspx) 函式為 SQL2010 新功能，從數值清單傳回指定之索引的項目。 
```sql
CHOOSE ( index, val_1, val_2 [, val_n ] )
```
```sql
SELECT CHOOSE ( 3, 'Manager', 'Director', 'Developer', 'Tester' ) AS Result;
--return: Developer

SELECT Gender, CHOOSE ( Gender, '男', '女') as Expression1 FROM Employees
Gender   Expression1
-------- ----
1      男
2      女
```

## CASE ... WHEN

CASE ... WHEN有二種使用方式

#### simple form

這個格式比較簡單，指定固定欄位當做比較式的來源。
```sql
SELECT ProductID, ProductName,
CASE Discontinued
WHEN 0 THEN 'No'
WHEN 1 THEN 'Yes'
ELSE 'Unknow'
END as Discontinued
FROM Products
```

#### searched form

這個格式比較彈性，因為比較式不限定同一個欄位。
```sql
SELECT ProductID, ProductName,
CASE 
WHEN Discontinued=0 THEN 'No'
WHEN Discontinued=1 THEN 'Yes'
ELSE 'Unknow'
END as Discontinued
FROM Products
```

# 系統函式

以下幾個也是常用的內建函式或是系統變數：

## 型別轉換函式

- [CAST](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) ：轉換運算式的資料類型。
- [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) ：轉換運算式的資料類型。
```sql
CAST ( expression as data_type )
CONVERT ( data_type , expression [ , style ] )
```

底下為幾個較常用的 style ：

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

## 執行結果回應函式

- [@@ERROR](http://technet.microsoft.com/zh-tw/library/ms188790.aspx) ：傳回最後執行之 Transact-SQL 陳述式的錯誤號碼。
- [@@TRANCOUNT](http://msdn.microsoft.com/zh-tw/library/ms187967.aspx) ：Returns the number of BEGIN TRANSACTION statements that have occurred on the current connection.
- [@@ROWCOUNT](http://technet.microsoft.com/zh-tw/library/ms187316.aspx) ：傳回受到前一個陳述式所影響的資料列數。

## 中繼資料函式（Metadata Functions）

- [OBJECT_ID](http://msdn.microsoft.com/zh-tw/library/ms190328.aspx) ：
- [OBJECT_NAME](http://msdn.microsoft.com/zh-tw/library/ms186301.aspx) ：
- [DB_ID](http://msdn.microsoft.com/zh-tw/library/ms186274.aspx) ：
- [DB_NAME](http://msdn.microsoft.com/zh-tw/library/ms189753.aspx) ：
- [COL_NAME](http://msdn.microsoft.com/zh-tw/library/ms174974.aspx) ：
```sql
SELECT DB_ID('test')		--19
SELECT DB_NAME(19)			--test

SELECT OBJECT_ID('tblUser')		--713821655
SELECT OBJECT_NAME(713821655)	--tblUser

--取得tblUser的所有欄位名稱
SELECT \* FROM sys.columns  where OBJECT_ID = 713821655

--由 tableid+columnid 取得欄位名稱
SELECT COL_NAME (713821655,1)	--UserID
```

## 目錄檢視（Catalog Views ）

下列內建的檢視（View）可以用來查詢各類別的物件資訊。

- [sys.objects](http://msdn.microsoft.com/zh-tw/library/ms190324.aspx) ：所有物件
- [sys.tables](http://msdn.microsoft.com/zh-tw/library/ms187406.aspx) ：取得資料表物件（sys.objects.type = U）
- [sys.columns](http://msdn.microsoft.com/zh-tw/library/ms176106) ：取得資料行物件
- [sys.triggers](http://msdn.microsoft.com/zh-tw/library/ms188746.aspx) ：取得觸發程序物件
- [sys.views](http://msdn.microsoft.com/zh-tw/library/ms190334.aspx) ：取得檢視物件（sys.objects.type = V）
- [sys.indexes](http://msdn.microsoft.com/zh-tw/library/ms173760.aspx) ：取得資料表、檢視或資料表值函式的索引
- [sys.foreign_keys](http://msdn.microsoft.com/zh-tw/library/ms189807.aspx) ：取得 FOREIGN KEY 條件約束（sys.object.type = F）
- [sys.procedures](http://msdn.microsoft.com/zh-tw/library/ms188737) ：取得程序物件（sys.object.type = P、X、RF、PC）
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [日期和時間資料類型與函式 (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms186724.aspx)
- [字串函式 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms181984.aspx)
- [Math.Round 方法](http://msdn.microsoft.com/zh-tw/library/system.math.round.aspx)