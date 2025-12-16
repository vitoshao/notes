---
title: 資料型別
layout: default
parent: Database Design
nav_order: 2
description: "資料型別"
date: 2013-05-03
tags: [SQL,Data Types]
postid: "4250185785901521032"
---

# 資料型別（Data Types）

SQL Server 的資料型別，可大至分成二大類：

- 系統資料型別
- 使用者自訂型別(UDTs)或

## 文字型別（Character Type）

<table>
	<tr><th>類型</th><th>型別</th><th>範圍</th><th>位元數</th><th>2008</th></tr>  
	<tr><td rowspan="3">非Unicode</td><td>char(n)</td><td>固定長度1~8000字，區分全半型</td><td>1字 1Byte</td><td></td></tr>  
	<tr><td>varchar(n)</td><td>變動長度1~8000字，區分全半型</td><td>1字 1Byte</td><td></td></tr>  
	<tr><td>varchar(max)</td><td>變動大型儲存體</td><td>0 ～ 2GB</td><td></td></tr>  
	<tr><td rowspan="3">Unicode</td><td>nchar(n)</td><td>固定長度1~4000字，不區分全半型，為Unicode編碼</td><td>1字 2Byte</td><td></td></tr>  
	<tr><td>nvarchar(n)</td><td>變動長度1~4000字，不區分全半型，為Unicode編碼</td><td>1字 2Byte</td><td></td></tr>  
	<tr><td>nvarchar(max)</td><td>變動大型儲存體</td><td>0 ～ 2GB</td><td></td></tr>
</table>

### text and ntext

這二個型別在未來的 SQL 版本將不再支援，所以應避免再使用。  
請使用 varchar(max) 或 nvarchar(max) 替換。

### char vs varchar

- char is fixed-length and varchar is variable-length
- char always allocates enough storage space to store its entire declared length
- varchar stores only the actual data entered.

### 定序（collation）

當使用 char 或 varchar 型別時，你必須同時設定該欄位的定序（collation），用來指明文字編碼所使用的字元集（ Code Page or Character Set）和文字的排序方式。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtf1PZD0LyGIQfwlUDH5y0VABtd05ZXLL-RV5ClUY3fZtv8RAc4dd02UMT1r_fG8uW2aN3t65E5jYqVR27FT18TIA2tBBr5yDyn5Pm8zfe3gmcp8bGGEH2sSTKBvkxgtau0ZlHAKkZ8l8/s0/char-setting-collation.png)

針對文字的排序與比對方式，每種定序又被區分以下幾個類別：  

- **Case Sensitivity(CS) ： 區分大小寫**  
若是 Case Sensitivity 則 A 與 a 是不同的；  若是 Case Insesitivity 則 A 與 a 是相同的，也就是查詢A，連同a也會被查詢到。
- **Accent Sensitivity(AS)  ： 區分腔調**  
AS/AI 是用來設定是否區分腔調字。  
例如：a 跟 á 、 o 跟 ö 在腔調上是相同的，若要區分就設成 AS ，若不區分就設成 AI (Accent Insesitivity)。
- **Kana Sensitivity(KS) ： 區分平假名(Katakana)片假名(Hiragana)**  
KS/KI 是用來設定是否將日文中的平假名和片假名視為相同。
- **Width Sensitivity(WS) ： 區分半形與全型字**  
WS/WI 是用來設定是否將半形字與全型字視為相同。

所以，每種定序分別代表著不同的代碼頁，不同的排序與比對的規則。  在 SQL 2008 中，共提供了 2397 個定序，你可以使用 fn_helpcollations() 取得這些定序資訊。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYZPWA6Nfb_m4UZMNHmmwrnWhmKt8RJUAQZcyrS7C2w07ECXNbzlb7DyiOKsCjNj2te3NfwE6ynA1Er5UOif1iB7HSv6CmcieDnC9ZOoBs5ksZ_-_pcraZk6DwxDgdZzoji8ygpW1hS3E/s0/sql-fn_helpcollations.png)

### Chinese_Taiwan_Stroke_CI_AI_WS

像上面這個定序，就表示：

- Code Page = 950
- Case Insensitive
- Accent Insensitive
- Kana Insensitive
- Width Sensitivive

### nchar vs nvarchar

這二種都是 Unicode 型別，使用 Unicode Universal Code Page (UCS-2) 編碼，所以每個字都使用 2 個 byte 。  所以你可以不用考量使用哪一種代碼頁的定序，不過，你仍然必須指定一個定序，因為定序包含文字的排序與比對規則的定義。  

必要時，你也可以依特定的定序，以進行資料的比對。  下面例子，預設的定序會區分全型與半型字，你也可以在表式中指定不區分半型與全型的定序以進行資料比對。  

區分全型半型  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjuFVVH1AM7h_kV1HVqb0NzNZaeFdzLxmKT5RrCJ_aVcQpwG7ScaEnW7Al7jLBUlKVXMEQjNwwuIobGLxsyTniX5gXFkygHXKCKJyPQIVHWRXVhMifU0SJAX611U3AXohperkV2n55opkM/s0/collation-ws.png)

不區分全型半型  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNdup6nkuypVmzFzihk5lZdJEctpEubgMJc7yMO3Rjs8OtaxmHcKRdytOr0Jb4iRo3H22B4A5ttIL_2TavDfN-Jb6eDPCskhQ-XL5H5mWalbDm15iy0EMmVNfz7cuf6PxxipfwcYEJR9Y/s0/collation-wi.png)

## 數字型別（Numeric Types）

<table>  <tbody><tr><th>類型</th><th>型別</th><th>範圍</th><th>位元數</th><th>2008</th></tr>  <tr><td rowspan="5">精確數值<br>（整數）</td><td>bit</td><td>0、1、null</td><td>1 bit&nbsp;</td><td></td></tr>  <tr><td>tinyint</td><td>0 ～ 2<sup>8</sup>-1 (0 ～ 255)</td><td>1</td><td></td></tr>  <tr><td>smallint</td><td>-2<sup>15</sup> ～ 2<sup>15</sup>-1 (-32,768 ～ 32,767)</td><td>2</td><td></td></tr>  <tr><td>int</td><td>-2<sup>31</sup> ～ 2<sup>31</sup>-1 (-2,147,483,648 ～ 2,147,483,647)</td><td>4</td><td></td></tr>  <tr><td>bigint</td><td>-2<sup>63</sup> ～ 2<sup>63</sup>-1</td><td>8</td><td></td></tr>  <tr><td rowspan="2">精確數值<br>（小數）</td><td>numeric</td><td>-10<sup>38</sup>+1 ～ 10<sup>38</sup>-1</td><td>5 ～ 17</td><td></td></tr>  <tr><td>decimal</td><td>-10<sup>38</sup>+1 ～ 10<sup>38</sup>-1</td><td>5 ～ 17</td><td></td></tr>  <tr><td rowspan="2">精確數值<br>（貨幣）</td><td>money</td><td>-922,337,203,685,477.5808 到 922,337,203,685,477.5807</td><td>8</td><td></td></tr>  <tr><td>smallmomey</td><td>- 214、748.3648 到 214、748.3647</td><td>4</td><td></td></tr>  <tr><td rowspan="2">近似數值</td><td>float</td><td>–3.40E38 ～ –1.18E-38、0、1.18E-38 ～ 3.40E38</td><td>n:1~24 4<br>n:25~53 8</td><td></td></tr>  <tr><td>real</td><td>–1.79E308 ～–2.23E-308、0、2.23E-308 ～ 1.79E308</td><td>4</td><td></td></tr>  </tbody></table>

### numeric and decimal

這二個型別在 SQL 中基本上是一樣的。它們最多可以使用 38 個數字來表達，其語法如下：  
```sql
numeric(p, s)
```

- P : precision, 表示有效位數（小數點左右二邊的位數和）
- S : scale, 表示小數位數

因為只能使用 38 個數字來表達，所以 P 最大就是 38 。而 S 必須小於或等於 P 。例如：  

- XXXX.yyy : 這可以用 numeric(7, 3) 表示。
- numeric(38, 0) : 這是可表達範圍中的最大值，也就是有效位數全部都是整數。
- numeric(38, 38): 這是可表達範圍中的最小值，也就是整數為０，小數有38個。
- numeric(18, 0) : 這是預設值。

### float and real

- float 和 real 資料類型也稱為近似資料類型。
- real 相當於 float(24)
- 近似數值資料類型不會儲存對許多數字指定的精確數值，而會儲存十分接近的近似值。
- 在需要精確數值行為時請勿使用這些資料類型，例如財務應用程式、牽涉到四捨五入的作業或者進行相等檢查作業時。
- 避免在 WHERE 子句的搜尋條件中使用 float 或 real 資料行，尤其是 = 與 &lt;&gt; 運算子。最好將 float 和 real 資料行限制為 &gt; 或 &lt; 比較。

## 日期和時間型別（Date and Time）

<table>  <tbody><tr><th>類型</th><th>型別</th><th>範圍</th><th>位元數</th><th>2008</th></tr>  <tr><td rowspan="6">日期類型</td>      <td>datetime</td><td>1753/01/01 00:00:00.000 ~ 9999/12/31 23:59:59.999 （精確度：0.00333秒）</td><td>8</td><td></td></tr>  <tr><td>SmallDatetime</td><td>1900/01/01 00:00 ~ 2079/06/06 23:59 （精確度：１分）</td><td>4</td><td></td></tr>  <tr><td>datetime2(n)</td><td>0001/01/01 00:00:00.0000000 ~ 9999/12/31 23:59:59.9999999 （精確度：n:0~7，10<sup>-n</sup>秒）</td><td>6~8</td><td>＊</td></tr>  <tr><td>date</td><td>0001/01/01 ~ 9999/12/31 （精確度：１天）</td><td>3</td><td>＊</td></tr>  <tr><td>time(n)</td><td>00:00:00.0000000 ~ 23:59:59.9999999 （精確度：n:0~7，10<sup>-n</sup>秒）</td><td>3~5</td><td>＊</td></tr>  <tr><td>DatetimeOffset(n)</td><td>0001/01/01 00:00:00.0000000 ~ 9999/12/31 23:59:59.9999999 （以UTC為單位）</td><td>8~10</td><td>＊</td></tr>  </tbody></table>

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkR2im7OhP4_TRCWIPoPyGoEBAlNnHsBkqytmvVWP7XgP5jNEh9bGBnelld7cqIxT5s08C8BWYCW14jPjLS4Jv14MWrtrJRin06_JB__aXwPx3JS2VF9t27Dj8rmKJac5ycIAqch-d9JE/s850/sql-type-datetime.png)

### 不得不知：

- datetime 的精準度是 3/1000 秒，所以 datetime 欄位值的最後一碼，一定是 0, 3, or 7 。
- smalldatetime 的精準度是 1 分
- date 的格式為: YYYY-MM-DD
- time 的格式為: HH:MI:SS.NNNNNNN
- datetime2 結合 date 和 time 型別，格式為: YYYY-MM-DD HH:MI:SS.NNNNNNN
- DatetimeOffset 的格式為: YYYY-MM-DD HH:MI:SS.NNNNNNN+|-HH:MI

### 用法

```sql
select * from tableb where t_datetime='2013/05/03 19:18:26.463'
select * from tableb where t_date='2013/05/03'
select * from tableb where t_time='19:18:30'

update tableb set t_datetime='2013/05/03 19:18:26.462'  -- 系統會儲存成尾碼3 -> 2013/05/03 19:18:26.463 
```

## 二進位資料

| 類型 | 型別 | 範圍 | 位元數 | 2008 |
| --- | --- | --- | --- | --- |
| 二進位資料 | binary(n) | 固定長度 1~8000 Byte |  |  |
| varbinary(n) | 變動長度 1~8000 Byte |  |  |
| varbinary(max) | 變動大型儲存體 | 0 ～ 2GB |  |
| 空間資料 | Geometry | 利用 CLR 實作資料類型，適用平面座標系統的資料 |  | ＊ |
| Geography | 利用 CLR 實作資料類型，適用經緯度座標系統的資料 |  | ＊ |

## 其他型別

| 型別 | 範圍 | 位元數 | 2008 |
| --- | --- | --- | --- |
| XML | XML 資料 | 0 ～ 2GB |  |
| UniqueIdentifier | 全域唯一識別碼 | 16 |  |
| Timestamp | 資料庫內自動產生唯一的二進位值 |  |  |
| HierarchyID | 利用 CLR 實作資料類型，用以管理具有階層式結構的資料及資料表 |  | ＊ |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTJsw-DtgmiPr1gDh9PbJGESUH7AKEm7oYZ57mOACRm3Ik5d2HIx1Qm5txcbCvYlRva_1zSQCCJxlqV34jj9C7HJBkaW8x01HSLr7nBj_Qs-elqCMudskuXz2tqpzrcoob7wYu96Juh7Q/s0/sql-type-special.png)

## 特殊型別

以下型別無法使用於實體資料表中的欄位型態。常用於建立資料表變數之中。

| 型別 | 適用時機 | 2008 |
| --- | --- | --- |
| table | 用以儲存結果集的資料表的資料類型 |  |
| cursor | 用以建立資料指標 |  |
| sql_variant | 可用於儲存各種 SQL Server 支援型別的資料。  <br>  例如，若欄位定義為 sql_variant ，則可以儲存 int、binary 和 char 等型別的值。 |  |
| sysname | 內建資料型別，限制 128 Unicode 大小，不允許 NULL。  <br>  用於儲存物件名稱。 |  |

```sql
DECLARE @tDBSize TABLE(
	DBName SYSNAME DEFAULT(DB_NAME()), 
	Fileid INT, 
	FileGroup INT, 
	TotalExtents INT, 
	UsedExtents INT, 
	Name SYSNAME, 
	FileName NVARCHAR(4000)
)    

INSERT @tDBSize(Fileid,FileGroup,TotalExtents,UsedExtents,Name,FileName) 
EXEC ('DBCC showfilestats')   

SELECT 
	DBName N'資料庫',Name N'資料檔案', 
	TotalExtents\*64.0/1024 N'資料檔案使用硬碟空間(MB)', 
	UsedExtents\*64.0/1024 N'資料實際使用空間(MB)', 
	FileName N'實體檔案路徑'
FROM @tDBSize
```

## 使用者定義資料表類型

暫略

## 計算的資料行（Computed Columns）

計算的資料行不能算是一種資料型別，而是 SQL Server 提供一種特殊的欄位格式，稱為[計算的資料行（Computed Columns）](http://msdn.microsoft.com/zh-tw/library/ms191250.aspx).  這種欄位的值，是使用同一資料表中其他欄位的值運算而成的。  

### 建立計算的資料行

1) 使用SSMS的UI進行設定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNu-cjYYFzFmj-21KkQ8CcwX8fS87LzemJZTxAXorEBMWG-_zWrnuQayOaY5yhfmNrWIhi6F_CwpmOf0a4vJ_GS1ElVnzWd0t1MiE-gLOLCnQhOyYNIXQF1viiKjgus5gF1uBhYJeNKgk/s0/sql-computed-column.png)

2) 使用 DDL 進行設定
```sql
--建立計算欄位
ALTER TABLE Orders
ADD OrderMonth AS MONTH(OrderDate);

ALTER TABLE OrderDetails
ADD SubTotal1 AS ( UnitPrice \* Quantity )
```

### 計算的資料行也可以使用自訂函式
```sql
ALTER TABLE Products
ADD Price2 AS dbo.GetProductPrice(ProductID);
```

### 計算資料行的使用限制

- 若要在計算資料行上使用 CHECK、FK、NOT NULL 條件約束，則該欄位必須標示為 PERSISTED。
- 若希望計算的資料行將值實際儲存，而不是等到要用時才計算，則該欄位必須標示為 PERSISTED。
- 若該計算資料行是可決定性的(deterministic expression)，則該欄位也可以成為索引鍵。
- 計算資料行無法成為 INSERT 或 UPDATE 陳述式的目標。

# 型別轉換

## Convert and Cast

- [CAST](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) ：轉換運算式的資料類型。
- [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) ：轉換運算式的資料類型。
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

## Collate

- [COLLATE](http://msdn.microsoft.com/zh-tw/library/ms184391.aspx) ：轉換定序。可套用在資料庫，資料表，或者陳述式之中用來做定序轉換之用。

在 SQL 2008 中，共提供了 2397 個定序，你可以使用 [sys.fn_helpcollations](http://msdn.microsoft.com/zh-tw/library/ms187963.aspx) 函式取得這些定序資訊。  

當您建立資料表時，可以使用 [CREATE TABLE](http://msdn.microsoft.com/zh-tw/library/ms174979.aspx) 陳述式的 [COLLATE](http://msdn.microsoft.com/zh-tw/library/ms184391.aspx) 子句來指定字元字串型別資料行的定序。若未指定任何定序，就會使用資料庫的預設定序指派給此資料行。   
```sql
--取得 SERVER 的預設定序
SELECT CONVERT (varchar, SERVERPROPERTY('collation'))

--取得 DATABASE 的定序
SELECT CONVERT (varchar, DATABASEPROPERTYEX('test','collation'))  --test is a DatabaseName

--TABLE 本身沒有定序 , 但它的字串型別欄位才有，若建立欄位時沒有特別指明定序，就會使用資料庫的預設定序.

--取得 Products.ProductName 的定序
SELECT name as ColName, collation_name as Collation
FROM sys.columns
WHERE
OBJECT_NAME(object_id)='Products'
AND name='ProductName'

--變更 DATABASE 的定序
ALTER DATABASE test COLLATE Chinese_Taiwan_Stroke_CI_AS

--變更資料行的定序 
ALTER TABLE myTable ALTER COLUMN mycol NVARCHAR(10) COLLATE Greek_CS_AI

--轉換結詢結果的定序
SELECT EmpName COLLATE Chinese_Taiwan_Stroke_CS_AS 
FROM Emp ORDER BY EmpName 

--使用不同定序做排序
SELECT EmpName FROM Emp 
ORDER BY EmpName COLLATE Chinese_Taiwan_Stroke_CS_AS ASC;
```

# SQL 資料型別與.NET Framework 型別對應

<table>  <tbody><tr><th>&nbsp;</th><th>SQL 型別</th><th>.NET Framework 型別</th></tr>  <tr><td class="head" rowspan="11">數字</td><td>bigint</td><td>Int64</td></tr>  <tr><td>int</td><td>Int32</td></tr>  <tr><td>smallint</td><td>Int16</td></tr>  <tr><td>tinyint </td><td>Byte</td></tr>  <tr><td>real</td><td>Single</td></tr>  <tr><td>decimal</td><td>Decimal </td></tr>  <tr><td>float</td><td>Double</td></tr>  <tr><td>numeric</td><td>Decimal</td></tr>  <tr><td>money</td><td>Decimal</td></tr>  <tr><td>smallmoney</td><td>Decimal</td></tr>  <tr><td>money</td><td>Decimal</td></tr>    <tr><td class="head" rowspan="5">日期時間</td><td>date</td><td>DateTime </td></tr>  <tr><td>datetime</td><td>DateTime </td></tr>  <tr><td>datetime2</td><td>DateTime </td></tr>  <tr><td>smalldatetime</td><td>DateTime </td></tr>  <tr><td>time</td><td>TimeSpan</td></tr>      <tr><td class="head" rowspan="3">文字</td><td>(n)char</td><td>String</td></tr>  <tr><td>(n)text</td><td>String</td></tr>  <tr><td>(n)varchar</td><td>String</td></tr>    <tr><td class="head">識別</td><td>uniqueidentifier</td><td>Guid</td></tr>  <tr><td class="head">Xml</td><td>xml </td><td>Xml</td></tr>  </tbody></table>

## 參考資料  

- [資料類型 (Database Engine)](http://msdn.microsoft.com/zh-tw/library/ms187594.aspx)
- [SQL Server 資料型別對應](http://msdn.microsoft.com/zh-tw/library/cc716729%28v=vs.110%29.aspx)
-