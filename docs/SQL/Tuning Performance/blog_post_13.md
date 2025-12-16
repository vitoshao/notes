---
title: 效能調校(7)-使用 DMV 查詢系統消耗資源
layout: default
parent: Tuning Performance
nav_order: 13
description: "效能調校(7)-使用 DMV 查詢系統消耗資源"
date: 2013-06-25
tags: [SQL,Tuning Performance]
postid: "4990906320391455433"
---
資料庫管理師常常面臨不知 SQL Server 如何使用資源的困擾，尤其是程式開發人員誤用 T-SQL 語法，索引設計不佳、大量來回存取…等，造成多人同時使用時耗盡系統資源，或是互相鎖定等狀況。  但要資料庫管理師抓出元兇，或是分析使用趨勢，利用既有工具程式如 SQL Server Management Studio 所內建的報表、活動監視器、SQL Trace/Profiler、Windows 效能監視器…等，仍力有未逮。  還要再進一步深入分析 SQL Server，則需使用[動態管理物件(Dynamic Management Object, DMOs)](http://technet.microsoft.com/zh-tw/magazine/hh441700.aspx)。   

# 與 OS 相關

## 記憶體相關

- [sys.dm_os_sys_memory](http://msdn.microsoft.com/zh-tw/library/bb510493.aspx) ：從作業系統傳回記憶體資訊。
- [sys.dm_os_memory_clerks](http://msdn.microsoft.com/zh-tw/library/ms175019.aspx) ：傳回 SQL Server 執行個體目前記憶體的使用狀況。

SQL Server 大量利用記憶體來存放暫存資料、中繼資料、執行計畫快取…等，當它逐步累積各種結構時，會越吃越大直到我們所設定的「最大伺服器記憶體」量。  以下的效能計數器可以用來分析記憶體是否夠用：   

- SQL Server: Buffer Manager\Buffer cache hit ratio  
越大越好，代表 SQL Server 使用的資料大多從記憶體取出，一般建議 &gt; 95%。
- SQL Server: Buffer Manager\Page Life Expectancy  
越大越好，代表記憶體夠，不須把暫存資料清掉，一般平均值要 &gt; 300 秒，代表快取的資料頁在記憶體中保有 5 分鐘以上。
- SQL Server: Memory Manager\Memory Grants Pending  
越小越好，代表需要記憶體時不必等待，其值最好為 0。

除了透過以上的效能計數器來分析記憶體使用狀況，一般，DBA 還會希望知道 SQL Server 把記憶體用到哪去了？

一開始若想要綜觀伺服器硬體的記憶體資源，可以查詢 [sys.dm_os_sys_memory](http://msdn.microsoft.com/zh-tw/library/bb510493.aspx) 動態管理檢視。它會告訴你該機器的實體與虛擬記憶體的大小、當下可用的量有多少，記憶體是否足夠等資訊。 
```sql
select 
total_physical_memory_kb/1024 as [總記憶體(MB)]
,available_physical_memory_kb/1024 as [可用記憶體(MB)]
,total_page_file_kb/1024 as [總分頁檔(MB)]
,available_page_file_kb /1024 as [可用分頁檔(MB)]
,system_cache_kb/1024 as [系統快取記憶體(MB)]
,system_high_memory_signal_state
,system_low_memory_signal_state
,system_memory_state_desc 
from sys.dm_os_sys_memory 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZB0yrDbq1MrHCxhpnYQR2ZWU-NA5LB4_chFGaZN5wvr84fNoD9i-LLb8vIjlktsdhUhQN0kV33EycqVNIexPTDBPWDCaiDDMqwktJD4555UULe7wzXu6cKxrbrn8QdfWiwsxq2VqbwFo/s850/sql-dm_os_sys_memory.png)

接下來，可以透過 [sys.dm_os_memory_clerks](http://msdn.microsoft.com/zh-tw/library/ms175019.aspx) 動態管理檢視觀察握在 SQL Server 執行個體手上的記憶體用在何處。  當上層的記憶體結構物件，如資料暫存區(Data Buffer)、執行計畫快取(Procedure Cache)、鎖定管理…等，需要記憶體時，要憑藉「記憶體 Clerk」存取記憶體節點以配置記憶體。   

前述需大量記憶體的每個物件須建立自己的「記憶體 Clerk」，並透過 Clerk 介面來配置它所有的記憶體。  由於一個物件可能建立多個「記憶體 Clerk」以配置不同的記憶體區塊，因此，需要依物件類型彙總記憶體的使用量，範例語法如下，其執行結果如下圖所示：   
```sql
select type 
,sum(pages_kb)/1024						[記憶體佔用大小(MB)]
,sum(virtual_memory_reserved_kb)/1024   [保留虛擬記憶體的數量(MB)]
,sum(virtual_memory_committed_kb)/1024	[使用虛擬記憶體的數量(MB)]
from sys.dm_os_memory_clerks
group by type
order by [記憶體佔用大小(MB)] DESC
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2ZHlxoP52SkPhhj6JkpueOeldA0TJ3q1Sf2mEcIGVAsQaE747zXdyUO7VMg0MW-Imh21iUXLpTbZmvhImNgtSJVHPXC-i3TTGFuEnhDxK7T4MIzKdxIlSskcMt7T_6RBflapYBPpHoUk/s0/sql-dm_os_memory_clerks.png)

以上是目前這台 SQL Server 執行個體主要的記憶體耗用情形，排名前面的幾項也正是一般常見耗用 SQL Server 記憶體的主要部分

- SQL Buffer Pool：資料的暫存區域。
- Cachestore\_Sqlcp：前端應用程式組織 T-SQL 語法要 SQL Server 執行，SQL Server 編譯成執行計畫並放在快取區所用的記憶體。
- Cachestore\_OBJCP：伺服器物件，如預存程序，所耗的執行計畫快取。

## SQL Buffer Pool

#### 查詢暫存區放置了哪些資料

當 SQL Server 從硬碟讀取資料進記憶體後，就放到資料暫存區。你可以利用以下語法，查詢暫存區放置了哪些資料：
```sql
select
ISNull(DB_NAME(database_id),N'資料資料庫') [資料庫],
Count(\*)/128 as [耗用暫存記憶體(MB)]
from sys.dm_os_buffer_descriptors
group by database_id
order by 2 desc
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAJlrTOyUnJqH-8EqcCdPb_EQHm-YG_uJyT2J0E3_9ulXbGPIXQHu5TfOeAoUFmBXBFUjk8nuzB-F2iKCxaiGQNlmGb_yuJY3X4OO0RzzHrogq8UQRhZx3XlkMaKOfhIt530IYVJo6Q6E/s0/sql-dm_os_buffer_descriptors-1.png)

此時就算利用語法清空資料暫存區：「DBCC DROPCLEANBUFFERS」，SQL Server也不會將記憶體還給作業系統，避免需要記憶體時又要請作業系統配置記憶體，久而久之就造成記憶體零碎。

接著執行語法故意放置一些資料到資料暫存區，然後再查詢一次暫存區放置了哪些資料
```sql
SELECT \* FROM 
Sales.SalesOrderHeader H
INNER JOIN Sales.SalesOrderDetail D ON H.SalesOrderID=D.SalesOrderID
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjvfunm5ULnRUuT7QHL2TgB-PJPYqCpex_NPotPDvYOSOL7tU2AOZ7bEgbX5lmqrh3sEdQCKaefavYeqAigJtvEsk2YGSxkRxHzxNG-yn7y7vieyQ0aimjoErtiKDE6bet6-oxErYpISWI/s0/sql-dm_os_buffer_descriptors-2.png)

由以上，你可以看到，AdvancetureWorks2012 用掉了 17 MB 的暫存記憶體。  若先切換到 AdvancetureWorks2012 資料庫，再執行以下語法：  
```sql
USE [AdvancetureWorks2012]

SELECT
OBJECT_NAME(p.object_id)  [物件名稱],
p.index_id,
Count(\*)/128 as [耗用暫存記憶體(MB)],
Count(\*) as [暫存區數量]
FROM sys.dm_os_buffer_descriptors dobd
join sys.allocation_units au on au.allocation_unit_id=dobd.allocation_unit_id
join sys.partitions p on au.container_id=p.hobt_id
WHERE dobd.database_id=DB_ID() AND
ObjectProperty(P.object_id,'ISSystemTable')=0
group by p.object_id, P.index_id
order by 4 desc
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghIx58ZiZrYtCoUgB7UhR4VQmhOzSQLDgCa31Xc-O9HglyrPiYW3SH-ykAIyfSrqpp0WwruWrSElAuZZrZXqQf5YeL1DrazuC0RkR2PaUH092E17wBnutid7Gu3zgGmLjYleOx8MT42P8/s0/sql-dm_os_buffer_descriptors-3.png)

PS.由於暫存區數量是以資料頁(page)為單位(大小為8 k bytes)，因此乘以8再除以 1024，也就是除以 128 即為 Mega Bytes。

## Cachestore\_Sqlcp

## Cachestore\_OBJCP

# 與 I/O 相關

### 顯示資料庫大小

要取得資料庫的空間大小和使用狀況，最簡單的方法就是透過 [sp_spaceused](http://msdn.microsoft.com/zh-tw/library/ms188776.aspx) 預存程序。
```sql
--顯示目前資料庫的空間大小和使用狀況
EXEC sp_spaceused 

--先更新即時資訊, 再顯示目前資料庫的空間大小和使用狀況
EXEC sp_spaceused @updateusage = N'TRUE';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8CUjoAUAZ1r7LnF5k7UM0rHlOoALgoX9vq3_d1jsb75pV7ttuuTY_SI9nPlh0qpP-p40EbEyCKWiTLJoCf1CMaMPmQ23WyGWyJdXWLfFAyPMQN5zTNiNrKPEwAcIeTRO_3JKoJIMB2sQ/s0/sql-sp_spaceused.png)

不過上面結果中的 database\_size 數值包含 mdf 和 ldf 的檔案大小。  若要區分資料檔和交易記錄的使用狀況，可以分別使用 DBCC showfilestats 和 [DBCC SQLPERF](http://technet.microsoft.com/zh-tw/library/ms189768.aspx) 指令取得相關資訊。  
```sql
SET NOCOUNT ON
DECLARE @tDBSize TABLE
(DBName SYSNAME DEFAULT(DB_NAME()),
Fileid INT, FileGroup INT, TotalExtents INT,
UsedExtents INT, Name SYSNAME, FileName NVARCHAR(4000)) 

DECLARE @tLogSize TABLE
(DBName sysname, logsize float, used float, status int)

INSERT @tDBSize(Fileid,FileGroup,TotalExtents,UsedExtents,Name,FileName)
EXEC ('DBCC showfilestats')

INSERT INTO @tLogSize
EXECUTE ('DBCC SQLPERF(LOGSPACE)')

SELECT D.DBName N'資料庫',D.Name N'資料檔案',
TotalExtents\*64.0/1024 N'資料檔案使用硬碟空間(MB)',
UsedExtents\*64.0/1024 N'資料實際使用空間(MB)',
logsize N'交易記錄檔整體使用的硬碟空間(MB)',
(logsize\*used/100) N'交易記錄檔整體的實際使用(MB)'
FROM @tLogSize L INNER JOIN @tDBSize D
ON L.DBName=D.DBName
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwkp5N9nrlYWbDCWN1KZDTREWFdTpzn7GQPi33ITK_HsCtEbuuwhLT89btIRYcyp_G2TnRWehv0vVtPSb7Ds678yVTI_V-7euSAHMMZFxcWZuzCCkBxbaqNXevAjNlG7D-5pISEzp8n5A/s850/sql-dbfile-usage.png)

另外使用 SSMS 的報表功能，也可以取得磁碟使用狀況。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7byhNycLYBfMztH-mTsz9UaXf5JI9jWZcGfVjotCmHrXYluZf0ZY9W_tHMKe7-3Jqhg2zm81wX8x79VDoKILK3wxA98IrFiK-dIrarrbPkChhLS6kKq0lDNYcJnq-1mSNvnGuJ2AaRPg/s0/sql-dbfile-usage-2.png)

關於 DBCC showfilestats 回傳內容的意義，可參考這篇介紹：[Understanding SQL Server's DBCC SHOWCONTIG](http://www.sql-server-performance.com/2002/dt-dbcc-showcontig/)

### 顯示 Table 大小
```sql
--顯示 Purchasing.Vendor 的 筆數,大小
EXEC sp_spaceused N'Purchasing.Vendor';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9Sbmf8y1_znyiFtLtuieB12WmnQO-deNy9BQ3YSuk_U1_vpXfSA7pDFIaPKx1DKLLxRpAtWYS81hBOZL6VkSDWMwntFZ2ZtIS7LNzGsrwVLNMp4iEXVpFEsTRps4YDBl3YF7AdCPxwW0/s0/sql-sp_spaceused-2.png)
```sql
----顯示所有 table 的 筆數,大小
EXEC sys.sp_MSforeachtable "EXEC sp_spaceused '?'"
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwwTvpDyprlssCQgv9JF0iI0P7J0nUIKCKMuexlfZmYcQdH1wcj6pcq6y27FZ8gKLj4L3cmxu5YgHCnQkL1g7XLIKpafXrQt06T6YTbEGSoV9s5_c3D3FpktJOyLlTFdZE3ZRG4rDtOMc/s0/sql-sp_spaceused-3.png)

# 與 process 相關

### sys.dm\_exec\_sessions

[sys.dm_exec_sessions](http://msdn.microsoft.com/zh-tw/library/ms176013.aspx) 檢視表會回傳目前所有的工作階段(session)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgc9jflqYSV_Qznn9uJEFYDYW4aaqo7V-f0tZdYxzTQgdAEeBbpAUQ0bCVQ4-0S0LqmbhWgRhcC3I38CB4ONMukxUUgD2hyAcYEBXVbv_YRIS3R3S6RW6pcIDNY4xsCUHTUP_Pmf7BdcTQ/s850/sql-dm_exec_session.png)

### sys.dm\_os\_waiting\_tasks

[sys.dm_os_waiting_tasks](http://technet.microsoft.com/zh-tw/library/ms188743.aspx) 檢視表會回傳正在等候某項資源的工作。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCzzbVKfSZZX6wNixzw5O4yRd1-ZKsn8vjcFf_6VBokDThuftLFZ2_c8AL19V8NAOhCIpcx4TYIBGISpbHbYaoTuQVBRTvNCfNjuiHxLGw61M4rMIyO77B9xW5SXT2eqGFBRLgI8E82xs/s0/sql-dm_os_waiting_task.png)

### sys.dm\_os\_wait\_stats

[sys.dm_os_wait_stats](http://technet.microsoft.com/zh-tw/library/ms188743.aspx) 檢視表會回傳等候的相關資訊。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjedjZnooDdltQP1yorWrN9mfDxhJokQB7krrUyZL2BO3EiFxyPRIQMaavGPX_7EnNi6-T1JZBQYhG1BK8ddQ8JFGw0q2Uo381cR12dCwygrfth_j_rlvt_Wn5WzzHCZICwQKapfM50Mk/s0/sql-dm_os_wait_stats.png)

# 其他校能調校常使用的 DMO

DMOs 物件依功能性大至可分成以下幾類：  

- SQL Server Operating System (SQLOS)–related DMOs
- Execution-related DMOs
- Index-related DMOs
## 參考資料  

- [以動態管理物件觀察SQL Server(1)](http://www.runpc.com.tw/content/content.aspx?id=107951)
- [以動態管理物件觀察 SQL Server(2)─找到最耗資源的執行計畫](http://www.runpc.com.tw/content/content.aspx?id=108039)
- [顯示資料庫所使用的磁碟空間、資料實際所使用的空間](http://sharedderrick.blogspot.tw/2008/01/displays-disk-space-reserved-and-used.html)