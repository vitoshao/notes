---
title: 資料庫主控台命令
layout: default
parent: Database Manage
nav_order: 14
description: "資料庫主控台命令"
date: 2013-11-22
tags: [SQL,Database Manage]
postid: "731241783395878798"
---
SQL Server 提供了很多圖形化介面的管理功能，但是同時它也提供相對的指令來執行管理工作，這些指令式的陳述式就稱為「資料庫主控台命令」(Database Console Commands, DBCC)。  

DBCC陳述式可分成以下類別目錄：

- 維護：Maintenance tasks on a database, index, or filegroup.
- 驗證：Validation operations on a database, table, index, catalog, filegroup, or allocation of database pages.
- 資訊：Tasks that gather and display various types of information
- 其他：Miscellaneous tasks such as enabling trace flags or removing a DLL from memory.

# 內部唯讀資料庫快照集

下列 DBCC 命令執行時，Database Engine 會建立一個資料庫快照集，且會使它進入交易一致狀態。  之後，DBCC 命令會針對這個內部唯讀資料庫快照集來進行檢查。這可以防止在執行這些命令時，發生封鎖和並行問題。

- [DBCC CHECKDB](http://technet.microsoft.com/zh-tw/library/ms176064.aspx) ：檢查指定資料庫中所有物件的邏輯完整性和實體完整性（physical and logical integrity）。
- [DBCC CHECKTABLE](http://technet.microsoft.com/zh-tw/library/ms174338.aspx) ：檢查資料表或索引檢視表的所有頁面和結構的完整性。
- [DBCC CHECKFILEGROUP](http://technet.microsoft.com/zh-tw/library/ms187332.aspx) ：檢查檔案群組中所有資料表和索引檢視的配置和結構完整性。
- [DBCC CHECKCATALOG](http://technet.microsoft.com/zh-tw/library/ms186720.aspx) ：檢查指定資料庫內的目錄一致性。
- [DBCC CHECKALLOC](http://technet.microsoft.com/zh-tw/library/ms188422.aspx) ：檢查指定之資料庫的磁碟空間配置結構是否一致。
- [DBCC TRACEON](http://technet.microsoft.com/zh-tw/library/ms187329.aspx) ：啟用指定的追蹤旗標。

當在下列情況下執行 DBCC 命令時，不會建立內部資料庫快照集： 

- 針對 master，且 SQL Server 執行個體在執行單一使用者模式。
- 針對 master 以外的資料庫，但已利用 [ALTER DATABASE](http://technet.microsoft.com/zh-tw/library/ms174269.aspx) 陳述式，使資料庫進入單一使用者模式。
- 針對唯讀資料庫。
- 針對已利用 [ALTER DATABASE](http://technet.microsoft.com/zh-tw/library/ms174269.aspx) 陳述式，設定為緊急模式的資料庫。
- 使用 WITH TABLOCK 選項。  在這個情況下，DBCC 會接受要求，不會建立資料庫快照集。
- 針對 tempdb。 在這個情況下，會因為內部限制而無法建立資料庫快照集。

## 使用 DBCC CHECKDB 與 DBCC CHECKTABLE

### 搭配 REPAIR_REBUILD 修復參數

DBCC CHECKDB 可以用來檢查錯誤。它可搭配下列參數來使用：

1. REPAIR_ALLOW_DATA_LOSS  
嘗試修復所有報告的錯誤。  這些修復可能會造成某些資料的遺失。

2. REPAIR_FAST  
維護這個語法的目的，只是為了與舊版相容。  不會執行任何修復動作。

3. REPAIR_REBUILD  
執行不可能造成資料遺失的修復，  這可包括快速修復 (例如，修復非叢集索引中遺失的資料列) 以及更耗時的修復 (例如，重建索引)。

```sql
--SINGLE_USER 
ALTER DATABASE [Northwind] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;   
GO

--執行不會造成資料遺失的檢測和修復
DBCC CHECKDB (Northwind, REPAIR_REBUILD);
GO

--MULTI_USER
ALTER DATABASE [Northwind] SET MULTI_USER; 
GO
```

# 使用 DBCC TraceON 擷取死結資訊

[追蹤旗標](http://technet.microsoft.com/zh-tw/library/ms188396.aspx)  可以用來設定伺服器的特定行為，例如：設定執行個體啟動時，停用磁帶機的硬體壓縮；設定資料庫發生死結時，回傳鎖定的資源和類型。  

下表列出幾個 SQL Server 中可用的追蹤旗標。詳情請參考 MSDN：[追蹤旗標](http://technet.microsoft.com/zh-tw/library/ms188396.aspx) 

- 1204：傳回參與死結之鎖定的資源和類型，以及目前受影響的命令。
- 1222：傳回參與死結之鎖定的資源和類型，以及目前受影響的命令。（XML格式）
- 2528：利用 DBCC CHECKDB、DBCC CHECKFILEGROUP 和 DBCC CHECKTABLE 來停用物件的平行檢查。
- 7806：在 SQL Server Express 上啟用專用管理員連接 (DAC)。