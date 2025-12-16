---
title: 如何清除交易記錄
layout: default
parent: HowTo
nav_order: 11
description: "如何清除交易記錄"
date: 2013-06-25
tags: [SQL,HowTo]
postid: "551289301905754459"
---
#### 交易記錄檔

交易記錄檔是記錄交易過程中資料表的異動狀況。  記錄範圍從 BEGIN TRANSACTION 開始，到 COMMIT TRANSACTION 結束。  之間所有的資料表異動狀況都會被記錄下來，所以交易記錄檔膨脹速度很快，這很正常。  若遇到資料庫損毀，就可以透過交易記錄檔來進行資料還原。  

####   為什麼清除交易記錄檔前要先做完整備份

因為交易記錄清除了，就無法籍由交易記錄來還原資料庫。  而完整備份是最完整的備份資料，它可以直接透過備份檔案將資料庫還原，而不必再由交易記錄檔把資料復原。  

## 清除交易記錄檔

### SQL 2008

Server 2008的話可以嘗試以下的指令來做清除：

- [ALTER DATABASE](http://technet.microsoft.com/zh-tw/library/ms174269.aspx) ：修改資料庫或與資料庫相關聯的檔案和檔案群組。
- [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) ：壓縮目前資料庫之指定資料或記錄檔的大小
```sql

USE [資料庫名稱]  
GO  
ALTER DATABASE [資料庫名稱] SET RECOVERY SIMPLE WITH NO_WAIT  
DBCC SHRINKFILE(記錄檔邏輯名稱, 10)  
ALTER DATABASE [資料庫名稱] SET RECOVERY FULL WITH NO_WAIT  
GO  
```

例
```sql

USE [TestDB]  
GO  
ALTER DATABASE [Vito2012] SET RECOVERY SIMPLE WITH NO_WAIT  
DBCC SHRINKFILE(VitoBlog_log, 20)  
ALTER DATABASE [Vito2012] SET RECOVERY FULL WITH NO_WAIT  
GO  
```

### SQL SERVER 2000, 2005

之前的版本的話可以使用以下指令：

- [BACKUP](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) ：備份完整 SQL Server 資料庫，以建立資料庫備份。
- [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) ：壓縮目前資料庫之指定資料或記錄檔的大小。
```sql

--清除記錄檔的資料  
BACKUP LOG [資料庫名稱] WITH TRUNCATE_ONLY  

--壓縮記錄檔的大小  
DBCC SHRINKFILE(記錄檔名稱, 重設檔案大小下限)  
```

例
```sql

BACKUP LOG [TestDB] WITH TRUNCATE_ONLY  
DBCC SHRINKFILE(TestDB_log, 10)  
```