---
title: Backup All Databases
layout: default
parent: SQL
nav_order: 1
description: "有時，在 SQL Server 中需要備份所有資料庫，這時使用 T-SQL 腳本來做，會是比較方便的辦法。"
date: 2025-03-21
tags:
  - SQL
  - Backup
---

有時，在 SQL Server 中需要備份所有資料庫，這時使用 T-SQL 腳本來做，會是比較方便的辦法。

## 備份所有資料庫的 T-SQL 腳本

```sql
DECLARE @BackupFolder VARCHAR(100)
DECLARE @FileName VARCHAR(300)
DECLARE @DbName VARCHAR(100)
DECLARE @YYYYMMDD VARCHAR(8)

Set @BackupFolder = 'D:\DbBackup\'
SELECT @YYYYMMDD = Convert(varchar(8),getdate(),112)

--取得所有資料庫的名稱，並排除系統資料庫
DECLARE db_cursor CURSOR READ_ONLY FOR  
SELECT name 
FROM master.sys.databases 
WHERE name NOT IN ('master','model','msdb','tempdb')  -- 排除不備份的資料庫
AND state = 0			-- 啟用中的資料庫
AND is_in_standby = 0	-- 非 standby 狀態 
 
OPEN db_cursor   
FETCH NEXT FROM db_cursor INTO @DbName   
 
WHILE @@FETCH_STATUS = 0   
BEGIN   
   SET @FileName = @BackupFolder + @DbName + '_' + @YYYYMMDD + '.BAK'

   --print @FileName
   BACKUP DATABASE @DbName TO DISK = @FileName WITH COMPRESSION
 
   FETCH NEXT FROM db_cursor INTO @DbName   
END   
 
CLOSE db_cursor   
DEALLOCATE db_cursor
```

## 完整備份 差異備份 交易記錄備份

同場加映，下面備份腳本會執行完整備份、差異備份、交易記錄備份，並以週數為檔名備份。
完整備份及差異備份會在同一個備份檔，每次交易記錄備份也會集中在同一個檔案中。

所以，若備份排程設定為每週執行一次完整備份，每天執行一次差異備份，每小時執行一次交易記錄備份。
那麼，每週只會產生二個檔案：BAK檔案，含有完整備份及差異備份，TRN檔案則含有每次交易記錄備份。

### 完整備份
```sql
DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'D:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '_' + @YEAR + 'W' + @Week + '.BAK'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP DATABASE [YFEP] TO  DISK = @BackupFile 
	   WITH NOFORMAT, 
	   INIT,			--覆蓋
	   NAME = @DbName, 
	   SKIP, REWIND, NOUNLOAD, 
	   COMPRESSION,     --壓縮
	   STATS = 10
```
### 差異備份
```sql
DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'D:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '_' + @YEAR + 'W' + @Week + '.bak'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP DATABASE [YFEP] TO DISK = @BackupFile 
	   WITH DIFFERENTIAL, 
	   NOFORMAT, 
	   NOINIT,			--附加
	   NAME = @DbName, 
	   SKIP, REWIND, NOUNLOAD, 
	   COMPRESSION,		--壓縮
	   STATS = 10
```
### 交易記錄備份
```sql
DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'D:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '_' + @YEAR + 'W' + @Week + '.trn'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP LOG [YFEP] TO DISK = @BackupFile 
	   WITH NOFORMAT, 
	   NOINIT,		 --附加
	   NAME = @DbName, 
	   SKIP, REWIND, NOUNLOAD, 
	   COMPRESSION,  --壓縮
	   STATS = 10
```


