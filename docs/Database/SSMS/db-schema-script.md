---
title: 資料庫物件
layout: default
parent: Database Manage
nav_order: 3
description: "資料庫物件"
date: 2013-05-28
tags: [SQL,Database Manage]
postid: "89239082147293564"
---
# 資料庫相關的語法
```sql
--CREATE DATABASE
CREATE DATABASE [ABC] ON PRIMARY 
(	/*資料檔*/
	NAME = N'AAA',							/*邏輯名稱*/
	FILENAME = N'D:\Database\AAA.mdf' ,		/*檔案路徑*/
	SIZE = 3072KB ,							/*初始大小*/
	MAXSIZE = UNLIMITED,					/*檔案限制*/
	FILEGROWTH = 1024KB						/*檔案成長大小*/
)
LOG ON 
(	/*記錄檔*/
	NAME = N'AAA_log',						
	FILENAME = N'D:\Database\AAA_log.ldf' , 
	SIZE = 1024KB , 
	MAXSIZE = 2048GB , 
	FILEGROWTH = 10%
)

--修改資料庫名稱
EXEC sp_rename 'ABC', 'ABCDE', 'DATABASE'

--查詢資料庫邏輯名稱, 檔案路徑, 初始大小, 檔案限制, 檔案成長大小
EXEC sp_helpdb 'ABCDE'

--修改資料庫邏輯名稱
Alter DataBASE [ABCDE]
MODIFY FILE(NAME= 'AAA' ,NEWNAME= 'ABCDE' )
Alter DataBASE [ABCDE]
MODIFY FILE(NAME= 'AAA_log', NEWNAME= 'ABCDE_log' )
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwLm9ekV9Gx4xjVuOPX8iPcYPIdYV0xShpay9L9k5famiRVGC8LTnBgJlxu6RayRFADx48BQ2NogLhPDCdOQ9_2RTbnrIZuIOk0BvAPhdhidPXvbjTmG6IC2Tc0iRaLng86XMpoAQs8ls/s0/sql-db-create-database.png)

# 與資料庫物件相關的指令

- [sp_spaceused](http://msdn.microsoft.com/zh-tw/library/ms188776.aspx) ：查詢資料庫或資料表的使用情形
- [sp_helpdb](http://msdn.microsoft.com/en-us/library/ms178568.aspx) ：查詢資料庫的相關資訊
- [sp_helpfile](http://msdn.microsoft.com/zh-tw/library/ms187335.aspx) ：查詢資料庫檔案的相關資訊
- [sys.dm_db_index_physical_stats](http://technet.microsoft.com/zh-tw/library/ms188917.aspx) ：

## sp_spaceused : 查詢資料庫或資料表的使用情形

[sp_spaceused](http://msdn.microsoft.com/zh-tw/library/ms188776.aspx) 預存程序

- 若後面加TableName，可整個檢視表格的**資料列的數目**、**已使用磁碟空間大小**、**保留的磁碟空間大小**。
- 若後面沒加參數，可檢視資料庫的保留和使用的磁碟空間大小。
```sql
--更新統計資料 (耗資源指令)
EXEC sp_updatestats;

--報告和更正目錄檢視中不準確的頁面和資料列計數 (耗資源指令)
DBCC updateusage(0);

--顯示資料庫大小
EXEC sp_spaceused @updateusage=N'true';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8CUjoAUAZ1r7LnF5k7UM0rHlOoALgoX9vq3_d1jsb75pV7ttuuTY_SI9nPlh0qpP-p40EbEyCKWiTLJoCf1CMaMPmQ23WyGWyJdXWLfFAyPMQN5zTNiNrKPEwAcIeTRO_3JKoJIMB2sQ/s0/sql-sp_spaceused.png)

{: .note}
> reserved = data + index_size + unused
> reserved 和 unallocated space 都是指資料頁的空間大小。
> database_size 是整個資料庫大小，包含 log 記錄檔的大小。所以 database_size 必定會大於 reserved + unallocated space 的總和。

```sql
--顯示資料表大小
EXEC sp_spaceused 'TestTable';

--取得所有資料表大小
CREATE TABLE #t 
( 
	[name] NVARCHAR(128),
	[rows] CHAR(11),
	[reserved] VARCHAR(18), 
	[data] VARCHAR(18), 
	[index_size] VARCHAR(18),
	[unused] VARCHAR(18)
) 
INSERT #t EXEC sys.sp_MSforeachtable 'EXEC sp_spaceused ''?''' 
SELECT name, rows, data as data_size, index_size , unused FROM #t
drop table #t
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9Sbmf8y1_znyiFtLtuieB12WmnQO-deNy9BQ3YSuk_U1_vpXfSA7pDFIaPKx1DKLLxRpAtWYS81hBOZL6VkSDWMwntFZ2ZtIS7LNzGsrwVLNMp4iEXVpFEsTRps4YDBl3YF7AdCPxwW0/s0/sql-sp_spaceused-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwwTvpDyprlssCQgv9JF0iI0P7J0nUIKCKMuexlfZmYcQdH1wcj6pcq6y27FZ8gKLj4L3cmxu5YgHCnQkL1g7XLIKpafXrQt06T6YTbEGSoV9s5_c3D3FpktJOyLlTFdZE3ZRG4rDtOMc/s0/sql-sp_spaceused-3.png)

## sp_helpdb : 查詢資料庫的相關資訊

[sp_helpdb](http://msdn.microsoft.com/en-us/library/ms178568.aspx) 可用來傳回特定資料庫或所有資料庫的相關資訊。
```sql
EXEC sp_helpdb 'TestDB'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjm_1hwcFU2QXFgoNVzrLdFUclMR-NcCM2TYNik5gMRq6yoyZM1BCQ9XMpdS8C_sC9qoz4rTSlCIQjxut3pgPlu13bwY-e4hdVfSO-R0GIcxQms-P_U4u7TMwRyE7PMibUuHrahfOTrmgU/s0/sql-sp_helpdb.png)

## sp_helpfile : 查詢資料庫檔案的相關資訊

若僅想得知資料庫檔的實體名稱和屬性，可以使用 [sp_helpfile](http://msdn.microsoft.com/zh-tw/library/ms187335.aspx) 取得。
```sql
EXEC sp_helpfile;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjltIalSnqkUAgN623MR-v6pgxrVYH0XiRIWW0pyuPoB-K5TolEtZL2KTshyphenhyphen9VJYobymHv1MwvhUfZAl8smvzK0uaKRm-IqlTudsM94HhKLrgnQix0Fy51ePMzHssfvLiU6fDNJRm21xws/s0/sql-sp_helpfile.png)

# 與 Schema 相關物件