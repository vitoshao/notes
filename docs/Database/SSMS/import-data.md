---
title: 匯入匯出
layout: default
parent: Database Manage
nav_order: 4
description: "匯入匯出"
date: 2013-07-08
tags: [SQL,Database Manage]
postid: "7024217131520711749"
---
匯入及匯出主要是討論「大量資料」(Bulk Data)的傳送問題，SQL Server也提供了數種方法以處理不同情境下的匯入匯出作業。  

# (一) 使用匯入匯出精靈

SQL Server 的**匯入匯出精靈**（**SQL Server Import and Export Wizard** ,DTSWizard.exe）算是 Integration Service 中的一個元件。  它支援以下資料來源：  

- Microsoft Access
- Microsoft Excel
- Flat File Source
- SQL Server Native Client 11.0
- .NET Framework Data Provider for SQLServer
- Microsoft OLE DB Provider for Analysis Services 11.0
- Microsoft OLE DB Provider for Oracle
- Microsoft OLE DB Provider for SQL Server

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBPeRS9Luaw1KXlVUCAlfRdIyBalaxeW7KIYQOCwDNMnGeBKra9tb-4c_qf0xUq6_BQclOTIoFQPGWEIOC-AZ8UeunhlgrPXlElyCfAEmvQhYRLlhVf43Kw5hyP7cLtq21yRm7dQxcFVE/s0/sql-import-export.png)

## 設定一般檔案目的地

當使用 SQL Server 的匯入匯出精靈時，若目的地設定為「一般檔案目的地」，即可指定目的地一般檔案的格式化選項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTVT9uE7jpG693kB-6beU3atI01VP9lLGd7ERaDQJNrqB_90yMWmyR1yQ3oSR2TQfT9DUEJXupsu8yP4R7pJtQigEeu8YhsDSIrJ8SJFW_YTxn2r7laHvCzMtkw4_97pN-LNtg4dFPWdE/s0/sql-import-export-2.png)

### 分隔符號：

- `{CR}{LF}`：歸位字元和換行字元(carriage return and line feed)
- `{CR}`：歸位字元(carriage return)
- `{LF}`：換行字元(line feed)
- `{;}`：分號
- `{:}`：冒號
- `{,}`：逗號
- `{t}`：定位字元
- `{|}`：分隔號

## 儲存封裝

你也可以將匯入匯出的過程儲存成SSIS封裝，這樣子就可以在日後透過排程執行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMmn1W28om-eaQymGnSiNacpMmmrmIwiB5KkvVpvKq2EsJjV2aNGKRb7wkW0knOpbKpOFOi7XnAMjFrJE3uCYO5ZBYg7qEOsEEBD3b8kgD9-MvVf_YDMx0-JrkCB81EkNMVppxCCN-SyE/s0/sql-import-export-3.png)

儲存方式可選擇「檔案」或「SQL SERVER」，若選擇檔案，封裝將被儲存成 .dtsx 檔，若選擇「SQL SERVER」，封裝將儲存到 SQL Server 的封裝存放區。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiE5KhJVRkEMkct_3x73sdZgREas9G3fbAmbbge-Bue23fez73wfPIq7a6jr-4zOWRmT2EHuDSyszfIsMx67F2_2itEQ1CjXvU1UnuPX7qcPIEbViGZojEysW8Wsol7pcxaJFfh75-yzj4/s0/ssis-package-storage.png)

# (二) 使用 BCP 匯入匯出

[bcp 公用程式](http://technet.microsoft.com/zh-tw/library/ms162802.aspx)是一個命令列式的工具，可用來將資料庫中的資料匯出匯入到檔案，也可以匯出物件的結構。
```sql
bcp [database_name.] schema.{table_name | view_name} | "query" {in data_file | out data_file | queryout data_file | format nul}
```
{: .note}


{: .note}
> BCP共有四個動作可以選擇:
> - out ：將特定資料表的資料匯出至指定個檔案。
> - in : 將資料從檔案匯入到資料庫資料表或檢視表。
> - queryout : 使用查詢，將結果匯出至指定檔案。
> - format : 以指定的格式建立檔案。包含輸出特定格式的資料，或者輸出schema的資料。

## 範例一：使用 bcp 匯出特定資料表資料
```
bcp TestDB1.dbo.Customers out Customers.txt -c -T
bcp TestDB1.dbo.Customers out Customers.txt -c -U "sa" -P "password"
bcp TestDB1.dbo.Customers out Customers.txt -c -T -S "serverName\instantName"
bcp TestDB1.dbo.Customers out Customers.txt -c -T -t  
```
### 參數說明：

- out：將資料輸出
- -T ：使用信任連線。若沒指定這個，則必須指定 -U 與 -P，才能順利登入。
- -U ：login_id
- -P ：password。
- -S ：指定要連接的 SQL Server 執行個體。若沒指定，會連本機的**預設執行個體**。server_name[ \instance_name]
- -t ：這個參數可以用來設定分隔符號，若下了 -t 參數，卻沒有設定分隔符號，則會使用固定寬度的資料；若不使用 -t 參數，預設分隔符號為\t。

### 下面４個參數用來指定操作模式：

- -c ：利用字元資料類型來執行大量複製作業。並非每個欄位都有這個選項的提示。它使用 char 做為儲存類型 (沒有前置詞)、欄位分隔符號是 \t (定位字元)，且資料列結束字元是 \r\n (換行字元)。-c  與 -w 不相容。
- -w ：利用 Unicode 字元執行大量複製作業。並非每個欄位都有這個選項的提示。它會使用 nchar 做為儲存類型 (沒有前置詞)、欄位分隔字元是 \t (定位字元)，而且資料列結束字元是 \n (換行字元)。 -w  與 -c 不相容。
- -n ：Performs the bulk-copy operation using the native (database) data types of the data.
- -N ：Performs the bulk-copy operation using the native (database) data types of the data for noncharacter data, and Unicode characters for character data.

## 範例二：使用 bcp 匯出查詢結果
```
bcp "SELECT CustomerID, CompanyName, ContactName, ContactTitle FROM TestDB1.dbo.Customers Order By CustomerID" queryout Customers.txt -c -t, -T -S "serverName\instantName"  
```

### 參數說明：

- queryout：將查詢結果輸出
```
ALFKI,3,ALFKI,Sales Representative
ANATR,Ana Trujillo Emparedados y helados,Ana Trujillo,Owner
ANTON,Antonio Moreno Taqueria,Antonio Moreno,Owner
AROUT,Around the Horn,Thomas Hardy,Sales Representative
BERGS,Berglunds snabbkop,Christina Berglund,Order Administrator
```

## 範例三：使用 bcp 匯出資料表結構

使用 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 指令除了可以匯出資料，也可以用來將 table schema 匯出，操作方法請參考下文使用格式檔案

## 範例四：使用 bcp 匯入資料

在匯入資料時，首先要注意的是該資料檔是採用什麼格式資料。
```sql
--使用 \t 間隔的資料
bcp TestDB1.dbo.Customers out Customers1.txt -c -t -T -S "serverName\instantName"

--使用 , 間隔的資料
bcp TestDB2.dbo.Customers in Customers2.txt -c -t, -T -S "serverName\instantName"

--使用固定寬度間隔的資料
bcp TestDB2.dbo.Customers in Customers3.txt -c -T -S "serverName\instantName"
```

## 範例五：使用格式檔案以 bcp 進行大量匯入

如何制作格式檔案請參考本文下段。

格式檔案是用來描述資料的型別，以便匯入資料能正確判斷資料類型。  除此之外，如果資料的順序和資料表的欄位順序不同，也可以利用格式檔案來加以描述。  

下面範例包含：(1)匯出資料。 (2)建置XML格式的格式檔案。 (3)使用XML格式檔案以 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 進行大量匯入。
```sql
--export data
bcp TestDB1.dbo.Customers out Customers.txt -n -t -T -S "serverName\instantName"

--export schema
bcp TestDB1.dbo.Customers format nul -n -t -x -f Customers.xml -T -S "serverName\instantName"

--import data with schema file
bcp TestDB2.dbo.Customers3 in Customers.txt -f Customers.xml -T -S "serverName\instantName"
```

# (三) 使用 BULK INSERT 匯入

[BULK INSERT](http://msdn.microsoft.com/zh-tw/library/ms188365.aspx) 可以將檔案中的資料匯入資料表中，如同使用 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 命令的 in 選項。  差異僅在於 [BULK INSERT](http://msdn.microsoft.com/zh-tw/library/ms188365.aspx) 是 DB Engine 的指令，而 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 是 OS 的命令。  

在使用 [BULK INSERT](http://msdn.microsoft.com/zh-tw/library/ms188365.aspx) 匯入資料時，必須先確認資料表已存在，且欄位格式階符合資料。  
```sql
BULK INSERT TestDB2.dbo.Customers2 FROM 'C:\Customers.txt';
```

# (四) 使用 OPENROWSET 匯入

[OPENROWSET](http://msdn.microsoft.com/zh-tw/library/ms190312.aspx) 陳述式允許你連線到 OLE DB 資料來源去取得資料。  所以只要再搭 INSERT 陳述式就可以匯入資料。  

對於常用的 OLEDB 連線，你也可以使用 Linked Server 來簡化操作。  

## 使用 OPENROWSET 連線到另一個 SQL 執行個體
```sql
--使用信任連線  

--ps. 注意這個例子中的 Trusted_Connection 和"分號"之間不可以有空白，也就是
--Server=VITO-2011W7\DBSERVER2012 ;Trusted_Connection=yes;    ---(ok)
--Server=VITO-2011W7\DBSERVER2012 ; Trusted_Connection=yes;   ---(error)

SELECT * FROM OPENROWSET(
'SQLNCLI', 
'Server=VITO-2011W7\DBSERVER2012 ;Trusted_Connection=yes;',      
'SELECT * FROM Northwind.dbo.Customers') 

--使用帳號連線

SELECT * FROM OPENROWSET(
'SQLNCLI', 
'VITO-2011W7\DBSERVER2012' ;'sa' ;'password',      
'SELECT * FROM Northwind.dbo.Customers')
```

根據預設，SQL Server 不允許使用 [OPENROWSET](http://msdn.microsoft.com/zh-tw/library/ms190312.aspx) 和 [OPENDATASOURCE](http://technet.microsoft.com/zh-tw/library/ms179856.aspx) 進行特定分散式查詢。  也就是說，「特定分散式查詢(Ad Hoc Distributed Queries)」選項是停用的。  所以若要使用 [OPENROWSET](http://msdn.microsoft.com/zh-tw/library/ms190312.aspx) 和 [OPENDATASOURCE](http://technet.microsoft.com/zh-tw/library/ms179856.aspx) 進行特定分散式查詢，請先確認「特定分散式查詢」選項是否啟用。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggFSc0XZ1XSkUVJ1y_q9IthL7F5rrlw5tp4RJHRci1poIGs7zSdypPNLJEYWIkRd__fmaP9A2anHz8pNUlw4sooW9DEJJx_VwZwaxF3NLT6_k2HOWsIS_2zuuzA3h95CEmiuGxgB4KIsU/s0/sql-ad-hoc-distributed-query-1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXpFLObAW8TijCbg94hfPskuC28OL0VJumFE7yWQeF21sIZ5Zra0sz96a-M70406MTOMFQRIWK5aq57Wp3ITmfQUJEmsQap83nXC1Do6ylFO3OlBknw-lWv0C_jcxF6dMPds1yung8Vxc/s0/sql-ad-hoc-distributed-query-2.png)

## 使用 OPENROWSET 開啟 Excel 檔
```sql
select * from OpenRowSet(
'Microsoft.Jet.OLEDB.4.0', 
'Excel 8.0;HDR=Yes;IMEX=1;Database=C:\temp\Customers.xls', 
'select * from [Sheet1$]')   
```

PS.僅適用32位元版本的OS

## 使用 OPENROWSET 開啟 .csv 檔
```sql
SELECT * FROM OPENROWSET(
'MSDASQL', 
'Driver={Microsoft Text Driver (*.txt; *.csv)};DefaultDir=F:\data\export\csv\;',
'SELECT * FROM Top10.csv')
```

PS.僅適用32位元版本的OS

## 使用 OPENROWSET 將大量資料檔匯入 SQL

使用 [OPENROWSET](http://msdn.microsoft.com/zh-tw/library/ms190312.aspx) 搭配 BULK 選項，可以用來匯入大量資料。

使用時必須指定檔案的資料型別，共支援３種型別。

- SINGLE_BLOB：以 varbinary(max) 型別讀取檔案。
- SINGLE_CLOB：以 varchar(max) 型別讀取檔案。
- SINGLE_NCLOB：以 nvarchar(max) 型別讀取檔案。

## 使用 OPENROWSET 讀取文字檔
```sql
declare @log nvarchar(max);

set @log = (
	SELECT * FROM
	OPENROWSET (BULK 'C:\Temp\Log.txt', SINGLE_NCLOB ) as A
)

select @log
```

## 使用 OPENROWSET 讀取影像檔
```sql
declare @img varbinary(max);
	set @img = (
	SELECT * FROM
	OPENROWSET (BULK 'C:\Temp\images.jpg', SINGLE_BLOB ) as A
```

# (五) 使用 SELECT INTO

[SELECT INTO](http://msdn.microsoft.com/zh-tw/library/ms188029.aspx) 會在預設的檔案群組中建立新的資料表，然後將查詢的結果資料列插入其中。
```sql
SELECT * INTO customers4 FROM customers
SELECT CustomerID, CompanyName INTO customers5 FROM customers WHERE CustomerID Like 'A%'
```

如果資料存在遠端伺服器，也可以搭配 [OPENQUERY](http://technet.microsoft.com/zh-tw/library/ms188427.aspx) 或 [OPENDATASOURCE](http://technet.microsoft.com/zh-tw/library/ms179856.aspx) 函式來指定來源資料。
```sql
SELECT * INTO Customer6 FROM OPENQUERY (DBSERVER2, 'SELECT * FROM Northwind.dbo.Customers');

SELECT * INTO Customer7 FROM
OPENDATASOURCE('SQLNCLI', 'Data Source=serverName\instantName; User ID=sa; Password=password')
.Northwind.dbo.Customers
```

{: .note}
> - 這個指令不會複制索引和約束條件。
> - 這個指令無法建立partitioned table。

## 如何建立 Linked Server

上面範例使用 [OPENDATASOURCE](http://technet.microsoft.com/zh-tw/library/ms179856.aspx) 連結到遠端的伺服器，如果該連結很常使用，可以建立 Linked Server 簡化操作。

### 在伺服器物件節點中，新增連結伺服器

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6L3FH1o3mkB41oi76zJEK5IU5SePmw9juVHmSIc2d2Mh6JHC70Q5pXFR27jnIzhJtLWoSgT1pVk_jwLLc-ndjyjpYP1FY9WzEenzXLTzaeVXPVn_4DWPOzKDRQYklwI59O_ICvfORh-A/s0/sql-add-linked-server-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrgb62_U-a5LHC8mOT6gzlzSvKiuB5ErKO6BTx2XwNXqGVNFmZ9peRZpS9j31_gn1Gnf4xLHITMVKJQuAzFoC0ttMJZ6KrEAu45qvkCjO2o8UVItF_TCn0kfqaES_oSOYdlHTkERz5tjc/s0/sql-add-linked-server-2.png)

### 使用 TSQL 新增連結伺服器
```sql
EXEC master.dbo.sp_addlinkedserver 
@server		= N'DBSERVER2', 
@srvproduct	= N'SQL 2012', 
@provider	= N'SQLNCLI', 
@datasrc	= N'serverName\instantName'

EXEC master.dbo.sp_addlinkedsrvlogin 
@rmtsrvname=N'DBSERVER2',
@useself=N'False',
@locallogin=NULL,
@rmtuser=N'sa',
@rmtpassword='########'
```



# 使用格式檔案

格式化資料：指的使用特定分隔符號或者固定寛度來區隔檔案中的資料。

格式檔案：指的用來描述這些資料的格式，也就是 schema 資料。

例：
```
10,Field2,Field3,Field4
15,Field2,Field3,Field4
46,Field2,Field3,Field4
58,Field2,Field3,Field4  9.0
4
1       SQLCHAR       0       7       ","      1     Col1         ""
2       SQLCHAR       0       100     ","      2     Col2         SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR       0       100     ","      3     Col3         SQL_Latin1_General_CP1_CI_AS
4       SQLCHAR       0       100     "\r\n"   4     Col4         SQL_Latin1_General_CP1_CI_AS    
```
使用 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 指令，除了可以匯出資料，也可以匯出資料表的結構。  有了格式檔案，就可以用來說明資料的型別以及對應到資料表的欄位等相關訊息。  

格式檔案可分成：[非 XML](http://msdn.microsoft.com/zh-tw/library/ms191479.aspx) 或 [XML](http://msdn.microsoft.com/zh-tw/library/ms187833.aspx) 格式的檔案。  底下示範如何建立格式檔案：  

## 建立非 XML 格式檔案範例

這個範例會將 Customers 資料表的結構以非XML格式匯出。非 XML 格式的檔案結構說明，可參考[MSDN : 非 XML 格式檔案結構。](http://msdn.microsoft.com/zh-tw/library/ms191479.aspx)
```sql
--原生格式 (native)
bcp TestDB1.dbo.Customers format nul -N -f Customers.fmt -T -S "serverName\instantName"

--字元格式 (character)
bcp TestDB1.dbo.Customers format nul -w -f Customers.fmt -T -S "serverName\instantName"
```
```
10.0
4
1       SQLNCHAR            2       10      ""   1     CustomerID                   Chinese_Taiwan_Stroke_CI_AS
2       SQLNCHAR            2       80      ""   2     CompanyName                  Chinese_Taiwan_Stroke_CI_AS
3       SQLNCHAR            2       60      ""   3     ContactName                  Chinese_Taiwan_Stroke_CI_AS
4       SQLNCHAR            2       60      ""   4     ContactTitle                 Chinese_Taiwan_Stroke_CI_AS  10.0
4
1       SQLNCHAR            0       10      "\t\0"       1     CustomerID                   Chinese_Taiwan_Stroke_CI_AS
2       SQLNCHAR            0       80      "\t\0"       2     CompanyName                  Chinese_Taiwan_Stroke_CI_AS
3       SQLNCHAR            0       60      "\t\0"       3     ContactName                  Chinese_Taiwan_Stroke_CI_AS
4       SQLNCHAR            0       60      "\t\0"       4     ContactTitle                 Chinese_Taiwan_Stroke_CI_AS
```

## 建立XML 格式檔案範例

XML 格式檔案具有以下幾個優點：
- XML 格式檔案具有自我描述能力，使其易於讀取、建立和擴充。
- XML 格式檔案包含目標資料行的資料類型。
- XML 格式檔案允許從資料檔載入包含單一大型物件 (LOB) 資料類型的欄位。

```sql
--原生格式 (native)
bcp TestDB1.dbo.Customers format nul -N -t -x -f Customers.xml -T -S "serverName\instantName"

--字元格式 (character)
bcp TestDB1.dbo.Customers format nul -c -t -x -f Customers_n.xml -T -S "serverName\instantName"
```
```xml
<?xml version="1.0"?>
<BCPFORMAT xmlns="http://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<RECORD>
		<FIELD ID="1" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="10" COLLATION="Chinese_Taiwan_Stroke_CI_AS"/>
		<FIELD ID="2" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="80" COLLATION="Chinese_Taiwan_Stroke_CI_AS"/>
		<FIELD ID="3" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="60" COLLATION="Chinese_Taiwan_Stroke_CI_AS"/>
		<FIELD ID="4" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="60" COLLATION="Chinese_Taiwan_Stroke_CI_AS"/>
	</RECORD>
	<ROW>
		<COLUMN SOURCE="1" NAME="CustomerID" xsi:type="SQLNCHAR"/>
		<COLUMN SOURCE="2" NAME="CompanyName" xsi:type="SQLNVARCHAR"/>
		<COLUMN SOURCE="3" NAME="ContactName" xsi:type="SQLNVARCHAR"/>
		<COLUMN SOURCE="4" NAME="ContactTitle" xsi:type="SQLNVARCHAR"/>
	</ROW>
</BCPFORMAT>
```


## 參考資料  

- [關於大量匯入和大量匯出作業](http://msdn.microsoft.com/zh-tw/library/ms187042%28v=sql.105%29.aspx)
- [匯入和匯出封裝 (SSIS 服務)](http://technet.microsoft.com/zh-tw/library/ms141772.aspx)
- [準備大量匯出或匯入的資料 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms188609%28SQL.110%29.aspx)
- [bcp 公用程式](http://technet.microsoft.com/zh-tw/library/ms162802.aspx)
- [使用 BULK INSERT 或 OPENROWSET(BULK...) 匯入大量資料(SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms175915.aspx)
- [使用格式檔案大量匯入資料 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms178129%28SQL.110%29.aspx)
- [設定一般檔案目的地 (SQL Server 匯入和匯出精靈)](http://msdn.microsoft.com/zh-tw/library/ms178804.aspx)