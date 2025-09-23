---
title: Backup All Databases
layout: default
parent: BusinessContinuity
nav_order: 1
description: "有時，在 SQL Server 中需要備份所有資料庫，這時使用 T-SQL 腳本來做，會是比較方便的辦法。"
date: 2025-06-05
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
-------------------------------------------------------    
--完整備份 壓縮備份 以週數為檔名 覆蓋
-------------------------------------------------------

DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'C:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '\' + @DbName + '_' + @YEAR + 'W' + @Week + '.BAK'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP DATABASE @DbName TO DISK = @BackupFile 
	   WITH NOFORMAT, 
	   INIT,			--覆蓋
	   NAME = @DbName, 
	   SKIP, REWIND, NOUNLOAD, 
	   COMPRESSION,     --壓縮
	   STATS = 10
```
### 差異備份
```sql
-------------------------------------------------------    
--差異備份 壓縮備份 以週數為檔名 覆蓋
-------------------------------------------------------

DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'C:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '\' + @DbName + '_' + @YEAR + 'W' + @Week + '.BAK'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP DATABASE @DbName TO DISK = @BackupFile 
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
-------------------------------------------------------    
--交易記錄備份 壓縮備份 以週數為檔名 覆蓋
-------------------------------------------------------

DECLARE @DbName VARCHAR(50) = 'YFEP'
DECLARE @BackupFolder VARCHAR(500) = 'C:\DbBackup\'
DECLARE @BackupFile VARCHAR(500)
Declare @YEAR Varchar(4)
Declare @WEEK Varchar(2)

SELECT @Week = FORMAT(DATEPART(week, GETDATE()), '00')
SELECT @YEAR = DATEPART(year, GETDATE())

Set @BackupFile = @BackupFolder + @DbName + '\' + @DbName + '_' + @YEAR + 'W' + @Week + '.TRN'

EXECUTE master.dbo.xp_create_subdir @BackupFolder

BACKUP LOG [YFEP] TO DISK = @BackupFile 
	   WITH NOFORMAT, 
	   NOINIT,		 --附加
	   NAME = @DbName, 
	   SKIP, REWIND, NOUNLOAD, 
	   COMPRESSION,  --壓縮
	   STATS = 10
```


## 還原資料庫

有了備份，那就順便記錄一下還原指令。

從備份章節中的末段，可以知道，不管中間經歷了幾次完整備份或差異備份，交易記錄備份的LSN都是連續的。 所以基本上 SQL Server 有能力將資料庫的資料還原到任何一個資料庫備份的時間點。 但是，為了加快資料還原的速度，我們通常不會從第一次完整備份開始逐一將交易記錄備份檔還原，而會透過最近一次的完整備份檔來還原資料庫，再透過差異備份和交易記錄備份來縮短還原的時間。

根據現有的備份資料，可以組合出下列幾個還原策略：

- 只還原完整備份
- 還原完整備份 + 差異備份
- 還原完整備份 + 交易記錄備份
- 還原完整備份 + 差異備份 + 交易記錄備份

### 設定復原狀態

在執行資料庫還原(restore)作業時，每一道還原程序，會建立 uncommitted transations，我們必須透過復原選項(Recovery state)來告知系統，目前資料庫還原程序是否都完備了，以便啟動最終復原作業。 
也就是復原選項，決定了該道還原程序執行後，資料庫是否可供使用，以及是否允許後續的還原程序。底下是 SQL Server 所提供的三種復原選項：

**NoRecovery**

- 表示該還原程序後執行後，不會回復未認可的交易。
- 此時可以繼續進行其他還原程序，例如，可以依序還原完整備份、差異備份和交易記錄備份。
- 此時資料庫會保持在「還原中」狀態，無法被使用者存取。
- 通常用於需要分階段還原多個備份的情境，確保所有備份程序都已還原後才進行最終復原。
 
**Recovery**

- 表示該還原程序後執行後，要認可所有交易，執行資料庫復原。
- 一旦執行RECOVERY，就不能再對資料庫進行其他還原程序。 
- 此時資料庫會恢復到可供使用者存取的正常狀態。（此為預設行為）
- 通常用於還原作業的最後階段，確保資料庫可以正常使用。

**Standby**

以Standby復原選項執行還原程序後，它也會回復未認可的交易，並讓資料庫處於唯讀模式，讓使用者可以在唯讀狀態中進行查詢。
STANDBY會使用備用檔案記錄回復操作，以便在需要時還原回復的效果。

### 範例一：還原「完整備份」
若只要還原「完整備份」，只會有一道程序，所以會使用 Recovery 復原選項。
```sql
RESTORE DATABASE [YFEP] 
	FROM  DISK = N'C:\DBFile\bak\20250820.bak' 
	WITH 
		FILE = 1, 
		RECOVERY, 
		REPLACE
```

### 範例二：還原「完整備份」＋「交易記錄備份」
還原完整備份使用 NoRecovery 復原選項；還原交易記錄備份使用 Recovery 復原選項。
```sql
-- Restore full backup with NORECOVERY
RESTORE DATABASE [MyDatabase]
FROM DISK = 'C:\Backups\YourBackupFile.bak'
WITH NORECOVERY;

-- Restore transaction log backup with RECOVERY (final step)
RESTORE LOG [MyDatabase]
FROM DISK = 'C:\Backups\YourBackupLog.trn'
WITH RECOVERY;
```
### 範例三：還原時使用 Move 選項
若想將資料庫還原到特定的目錄，可以使用 MOVE TO 選項將檔案設定到新的位置。
```sql
RESTORE DATABASE [YFEP] 
	FROM  DISK = N'C:\DBFile\bak\20250820.bak' 
	WITH  
		FILE = 1, 
		MOVE 'YFEP' TO 'C:\DBFile\YFEP.mdf',
		MOVE 'YFEP_Log' TO 'C:\DBFile\YFEP_Log.ldf',
		RECOVERY, 
		REPLACE
```
### 範例四：還原到指定時間點
使用 RESTORE LOG 還原交易記錄時，可以使用以下選項，將交易記錄還原到指定的時間：

- stopAT：將交易記錄還原到指定時間
- stopATmark：將交易記錄還原到標記
- stopBEFOREmark：將交易記錄還原到標記之前

```sql
USE [master]

--切換成單人獨占模式
ALTER DATABASE DB1 SET SINGLE_USER WITH ROLLBACK IMMEDIATE

--還原完整備份 (加入NORECOVERY)
RESTORE DATABASE DB1 FROM DISK = N'D:\Database\tmp\DB1.bak' WITH NORECOVERY, REPLACE
GO

--還原交易備份
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log.trn'  WITH FILE = 1, NORECOVERY
GO
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log.trn'  WITH FILE = 2, NORECOVERY
GO
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log_Tail.trn'  WITH RECOVERY, STOPAT = N'2013-11-08T11:27:30.999'
GO

--回復成多人模式
ALTER DATABASE DB1 SET MULTI_USER WITH NO_WAIT
```