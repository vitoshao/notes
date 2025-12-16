---
title: How To ...
layout: default
parent: SQL
nav_order: 1
date: 2012-03-08
tags: [SQL,HowTo]
postid: "4365623267535356535"
---

- [如何快速取得 Table 的總筆數](#如何快速取得-table-的總筆數)
- [如何變更 SQL Server 預設的資料檔路徑與備份檔路徑](#如何變更-sql-server-預設的資料檔路徑與備份檔路徑)
- [如何取得 SQL Server 資料庫執行中的 T-SQL 指令與詳細資訊](#如何取得-sql-server-資料庫執行中的-t-sql-指令與詳細資訊)
- [如何使用逗號合併查詢結果](#如何使用逗號合併查詢結果)

## 如何快速取得 Table 的總筆數

一般來說， 要取得某一個Table的總筆數，可以簡單由 Select Count(*) From TableName 取得。 但是， 這個方法， SQL Server必需執行一次Index Scan之後， 才可以統計出來， 如果該Table的資料筆數很大時， 效率就會降低。

在 SQL Server 裡，也可以透過查詢 sys.partitions 系統資料表來取得資料表的總筆數，如下語法：
```sql
select rows from sys.partitions where object_id=object_id('dbo.Users') and index_id = 1
```

> 透過 sysindexes、partitions和sp_spaceused 取得資料筆數數值可能會因為更新統計值時間誤差而不準確， 最保險作法請先執行 DBCC UpdateUsage ('db_name', 'table_name') WITH COUNT_ROWS，然後再取得筆數的資訊，不過，這樣子整體執行時間將拉長。
> 如果有人對於「筆數」的精準度很要求的話，建議還是用 SELECT COUN(*) FROM TableName 的方式來取得總筆數比較安全， 如果以「統計網站累積使用人次功能」來說，數字差一點點其實沒關係的。


## 如何變更 Sql Server 預設的資料檔路徑與備份檔路徑

在操作 SSMS 時，常會碰到二個預設目錄，一個是在新增資料庫時，資料庫存放的目錄，一個是在備份資料庫時，備份檔存放的目錄。  由於系統使用的預設的目錄為 C:\Program Files\Microsoft SQL Server\MSSQL10\_50.W7\_SQLSVR\_2008\MSSQL\Backup\ ，  這通常不是大家慣於使用的位置，每次新增或備份資料庫的時候，就得手動再切換到自已要使用的目錄。  如果你跟我一樣覺得這樣子很麻煩的話，底下的示範就是說明如何變更這個預設目錄的值，讓使用 SSMS 時可以方便一點點。  

### 變更預設的「資料檔路徑」

首先，在 SSMS 裡點選ServerName，在右鍵選單中選取「屬性」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjM458sZUgFKG8Nx7_WePSWphzla7N_tBck7wymYL73veciNEBMOhEQH0Z2KSHs5N2Uw1Z_BsIFxJdPSyOWS02XrLzBGilFeKHkL-CTv5Qq4m3cbFtz2wc9UruD3AxGOX4fbmTHUccufPw/s314/sql-default-folder-02.png)

選擇「資料庫設定」頁籤，在下方就會出現資料庫預設路徑的設定欄位，將他改掉即可：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsagE-Pys3ZI_21_zFDngQRPmlTYnQSD_s8b7sjPLRVZP7v9m1xwbd-akW9Frzhyphenhyphen7cVKqe-T1hDcfEksIv62RdKnofPz9N3wWxvSrOIZfDOmx1HtLjJ1XWkY0ui0DSC-jHWhq_yr3eiAs/s715/sql-default-folder-01.png)

### 變更預設的「備份檔路徑」

要修改預設的備份路徑就必須手動修改機碼

首先，先開啟登錄編輯程式(regedit.exe)，  並找到 HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL10\_50.W7\_SQLSVR\_2008\MSSQLServer 機碼，  該機碼下有個 BackupDirectory 字串值，直接修改成你要的目錄路徑即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLkixro5swaCMg_UnXLSe0wutg3jtS64f_FCYcOp-zMOr_YXAhphdr-aMIAaSGmds2EgfgdBITsRY1qAgaswrUErjfeLx96RXdx3a4m25bTAecZIFXP1jtYRbb3deVlmDyShTCh3nUuW4/s744/sql-default-folder-03.png)

上面 MSSQL10\_50.W7\_SQLSVR\_2008 這個值要看實際的 SQL 執行個體的版本名稱，每個人的環境可能有所不同。

**備註：**

SQL 2012 已經在「資料庫設定」中，加入預設備份路徑的設定。(2013/7/4)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJ60rnPLL8tLT8CnZijy7PZCwxDGhaY5LRsP-QOW9nWI5DLahx3yjwxuiRaQJi1W-fGjoYOeEfPsQUYdQp9VdcqgaErPDgxKeK5K3QYcWAlfbrw6cZ9uhjnkgViWmZO4D8BhrsRdo7R_M/s0/sql-default-folder-04.png)

## 如何取得 Sql Server 資料庫執行中的 T-SQL 指令與詳細資訊

使用 sp_who 或 sp_who2 這些系統預儲程序，雖然可以查出所有連線的狀況，也可以看到該連線被卡住 (Blocked) 的狀況，不過 Command 這個欄位卻只有查詢的摘要，看不出完整的查詢命令為何。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAuWtoGqW4Lybq5i8q3EFutH47VFbCkuG2VrkSPoZhWXmNYIymKS1Zix71fsJCB26W4ZsjmP6WeKPy3cxkfU0yiwYVJjXb4jCoME9wm91wIgKtedgjvUjhO1reVeY07UvvkXDmDiiRrNc/s0/sql-sp_who2.png)

這時可以利用幾個 DMVs (Dynamic Management Views) 來查詢 SQL Server 資料庫中即時的運作資訊，包括正在執行的完整 T-SQL 命令句：  

```sql
SELECT 
	r.scheduler_id as 排程器識別碼, 
	status   as 要求的狀態, 
	r.session_id   as SPID, 
	r.blocking_session_id as BlkBy, 
	substring( 
	ltrim(q.text), 
	r.statement_start_offset/2+1, 
	(CASE 
	WHEN r.statement_end_offset = -1 
	THEN LEN(CONVERT(nvarchar(MAX), q.text)) * 2 
	ELSE r.statement_end_offset 
	END - r.statement_start_offset)/2) 
	AS [正在執行的 T-SQL 命令], 
	r.cpu_time   as [CPU Time(ms)], 
	r.start_time as [開始時間], 
	r.total_elapsed_time as [執行總時間], 
	r.reads     as [讀取數], 
	r.writes    as [寫入數], 
	r.logical_reads   as [邏輯讀取數], 
	-- q.text, /\* 完整的 T-SQL 指令碼 \*/ 
	d.name      as [資料庫名稱], 
	c.client_net_address as [用戶端IP地址], 
	c.client_tcp_port as [用戶端Port] 
FROM 
	sys.dm_exec_requests r 
	CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS q 
	LEFT JOIN sys.databases d ON (r.database_id=d.database_id) 
	LEFT JOIN sys.dm_exec_connections c ON ( r.connection_id = c.connection_id ) 
ORDER BY 
r.total_elapsed_time desc
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgje6XSCzuF4NZWeozBHFLuWNJjwSNsXRW1F3RiwuNXJt_j6OiCSHan-q4M7d-a0KEssXL7iz4h8w4mbgiN-zocyqBWHxkcq-05YV18zmJRCuGqksBeAszhIQXygQLwvLJE_WAICxBOgHc/s0/sql-dm_exec_requests.png)

## 如何使用逗號合併查詢結果

有時候我們會想要將多筆資料合併為一筆顯示，如下面資料中，若想要將同一個 TicketID 的 Person 用逗號間隔，可以這麼做：  

```sql
CREATE  TABLE Tickets (
[TicketID] char(5) NOT NULL,
[Person] nvarchar(15) NOT NULL
)

INSERT INTO Tickets VALUES
('T0001', 'Alice'),
('T0001', 'Bob'),
('T0002', 'Catherine'),
('T0002', 'Doug'),
('T0003', 'Elaine')
```

### 使用 Cursor

使用 Cursor 就是跑迴圈。
```sql
ALTER PROCEDURE usp_GetPersonList (@TicketId varchar(max))
AS
    DECLARE @Person varchar(Max);
    DECLARE @PersonList varchar(Max);
    
    DECLARE _cursor CURSOR FOR
    SELECT Person FROM Tickets WHERE TicketId=@TicketId
    
    OPEN _cursor
    FETCH NEXT FROM _cursor INTO @Person
    
    SET @PersonList='';
    WHILE @@FETCH_STATUS = 0
    BEGIN
    Set @PersonList = @PersonList + ',' + @Person
    FETCH NEXT FROM _cursor INTO @Person
    END
    
    IF LEN(@PersonList)>0
    SET @PersonList = Substring(@PersonList,2,LEN(@PersonList));
    
    SELECT @PersonList;
    
    CLOSE _cursor
    DEALLOCATE _cursor
    GO
    
    EXEC usp_GetPersonList 'T0001'
    GO
```

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated.png)

### 使用變數合併結果

上面的迴圈方法，也可以直接使用一個變數來累加結果，可以更簡便。  
```sql
DECLARE @PersonList varchar(MAX)

SELECT @PersonList = COALESCE(@PersonList + ',' , '') + Person
FROM Tickets WHERE TicketID='T0001'

SELECT @PersonList
```

- 上面用到 [COALESCE](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx)，只是為了方便處理第一個逗號而已。

![](http://127.0.0.1:8080/_images/cs/sql-COALESCE.png)

### 使用 FOR XML Path

- [FOR XML](https://msdn.microsoft.com/zh-tw/library/ms178107.aspx) ：以資料列集（rowset）的形式取得查詢結果。

1. 首先透過 [FOR XML](https://msdn.microsoft.com/zh-tw/library/ms178107.aspx) 子句，可以將結果回傳成單一的 XML 格式。
```sql
SELECT (',' + Person) 
FROM Tickets 
WHERE TicketID='T0001'
FOR XML AUTO('')
```
![](http://127.0.0.1:8080/_images/cs/sql-for-xml-1.png)

2. 若是要取得所有群組。
```sql
SELECT B.TicketID, 
(
	SELECT (',' + Person) 
	FROM Tickets A
	WHERE A.TicketID=B.TicketID
	FOR XML PATH('') 
)
FROM Tickets B	
GROUP BY TicketID
```
![](http://127.0.0.1:8080/_images/cs/sql-for-xml-2.png)

3. 修飾第一個逗號。

使用 [STUFF](http://msdn.microsoft.com/zh-tw/library/ms188043.aspx) 將第一個字取代掉。
```sql
SELECT B.TicketID, 
STUFF((
	SELECT (',' + Person) 
	FROM Tickets A
	WHERE A.TicketID=B.TicketID
	FOR XML PATH('') 
) , 1, 1, '')
FROM Tickets B	
GROUP BY TicketID
```

![](http://127.0.0.1:8080/_images/cs/sql-for-xml-3.png)

## 如何將逗號間隔資料分成不同資料列

如果有一資料如下：  
```sql
CREATE  TABLE TicketList (
	[TicketID] char(5) NOT NULL,
	[PersonList] nvarchar(max) NOT NULL
)

INSERT INTO TicketList VALUES
('T0001', 'Alice,Bob'),
('T0002', 'Catherine,Doug,Elaine')
```

想拆成以下結果：  

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated-2.png)

查詢語法：  
```sql
;WITH tmp(TicketID, Person, PersonList) as (

select TicketID, LEFT(PersonList, CHARINDEX(',',PersonList+',')-1),
STUFF(PersonList, 1, CHARINDEX(',',PersonList+','), '')
from TicketList

union all

select TicketID, LEFT(PersonList, CHARINDEX(',',PersonList+',')-1),
STUFF(PersonList, 1, CHARINDEX(',',PersonList+','), '')
from tmp
where PersonList > ''

)

select TicketID, Person
from tmp
order by TicketID
```

結果：  

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated-2.png)
