---
title: 如何清除交易記錄
layout: default
parent: HowTo
nav_order: 12
description: "如何清除交易記錄"
date: 2013-07-09
tags: [SQL,HowTo]
postid: "8129967347004978376"
---
SQL 資料庫系統的 Log記錄檔，會隨著系統的使用，不停的成長。若是長大到影響到其他系統，我們可以讓它瘦身一下。

### 使用 SQL 2005

在 SQL 2005 前(含)，可以使用 Backup log 來清除(Truncate)交易記錄。
```sql

--（1）truncate log in log file  
BACKUP LOG [DBName] WITH TRUNCATE_ONLY  

--（2）compress log file  
DBCC SHRINKFILE([DBName_log],2)  
```

例
```sql

BACKUP LOG TestDB WITH TRUNCATE_ONLY  
DBCC SHRINKFILE ('TestDB_Log', 2)          
```

BACKUP [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) 指令原本是用來備份 log 用的，因為加上了 WITH TRUNCATE\_ONLY 選項，所以不會真正備份記錄檔，只會移除非使用的交易記錄。

**後記**

[DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 是把實體的Log檔案縮小，可是只要資料繼續異動，Log檔就又會繼續長大。  
比較正確的作法，是執行 [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) 備份時，加上 WITH NO\_LOG 選項以便同時清除Log內容。  
這樣子，在清除Log後，雖然Log檔案大小不變，但內容是空的，可提供資料異動時的Log使用。  
```sql

BACKUP LOG TestDB_Log WITH NO_LOG;  
```

### 使用 SQL 2008

在 SQL 2008 環境下，上述用法已被停用，必須切換「復原模式」為「簡單」來清空交易記錄。
```sql

--1) 將資料庫 Northwind 切換為「簡單復原模式」，便會自動截斷交易記錄。   
ALTER DATABASE TestDB SET RECOVERY SIMPLE     

--2) 壓縮交易記錄檔  
DBCC SHRINKFILE(TestDB_Log, 2)  

--3) 若要使用備份交易記錄檔(\*.ldf)，再將資料庫 Northwind 切換回「完整復原模式」   
ALTER DATABASE TestDB SET RECOVERY FULL            
```

清除前

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfi0JhvKmiVbspdc7w2N8WVh5AkoeGoW7ZS-d2ZecYIL9JeAcb21so08m0UnlazeoQrJGrnrf7HzYBl88dEeMYdVuyKF5t0K5OpSTEb4Kt0m82leRKXA9fN-3TrY2cS91ZLNPVR9f3VXk/s0/sql-truncate-log-1.png)

清除後

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWdFVs8vt0iv3COtQolE7vC10vgl68LkE5GnbhVZ2zCwUHgzWRPudpNdutdLhqDZbHWYq6CIFp3jbL89wo4nmmJLOjQ6TfveULnyU45k15sWg8CKG1EV2JQ1_uryrbmAiDeblh8o9PXSM/s0/sql-truncate-log-2.png)