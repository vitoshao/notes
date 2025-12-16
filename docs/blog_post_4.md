---
title: 資料庫備份
layout: default
parent: 
nav_order: 4
description: "資料庫備份"
date: 2013-07-08
tags: []
postid: "4868455704745804957"
---
# 認識備份

## 資料庫的復原模式

這篇文章明明是要介紹**備份**的，為什麼一開始確先討論**復原**模式？  因為資料庫的復原模式會與交易記錄的保存方式有關。  而交易記錄的保存方式會與資料庫執行復原時的[復原點目標（Recovery Point Objective, RPO）](http://en.wikipedia.org/wiki/Recovery_point_objective)相關。  

**復原點目標**簡單講就是當災難發生時，最大可容忍資料遺失的程度。例如，二小時，一小時，或者都不能接受任何資料遺失，則 RPO 為零。  所以要操作資料庫的備份前，必須先認識資料庫的復原模式，才能確實作好備份工作的安排。  

資料庫的復原模式有三種，[簡單](http://msdn.microsoft.com/zh-tw/library/ms186216.aspx)、[完整](http://msdn.microsoft.com/zh-tw/library/ms187495.aspx)、[大量記錄](http://msdn.microsoft.com/zh-tw/library/ms186289.aspx)，如下圖所示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhB4_dRa4K4ntPrSLQFLi5nNc0YsiChaSMFkwSIzhNKmG6iBjhUohgI7XHPbC9OfYF8W-1uX3j-XzDJiQNzDvsD8zHZ44E639EYsw05D_G7ckQ8uXuFKo7eilQhsY9w9NBQYK9KPUcnJwY/s0/sql-restore-mode.png)

### 簡單（Simple）

「簡單模式」不會執行 Transaction Log 的復原，  
所以備份時也僅支援「完整備份」和「差異備份」，不支援「交易記錄備份」。  
所以在簡單復原模式下，如果發生資料庫損毀，可能就得承受遺失最後一次備份以後的記錄。  

也就是說，若你將資料庫設定為「簡單復原模式」，那麼你只可以執行「完整備份」和「差異備份」。  
若發生災難，你必須承受遺失最近一次備份後的所有交易記錄。  
```sql
ALTER DATABASE [TestSB] SET Recovery Simple
```

### 完整（Full）

完整復原模式可以執行 Transaction Log 的復原，所以除了支援「完整備份」和「差異備份」，也支援「交易記錄備份」。  

使用完整復原模式的資料庫，除了可以設定「完整備份」和「差異備份」外，也必須設定「交易記錄備份」，才能確保還原作業可以不遺失任何記錄，並將資料庫還原到過去任何時間點。  
```sql
ALTER DATABASE [TestSB] SET Recovery Full
```

### 大量記錄（BULK\_LOGGED）

**大量記錄**復原模式，用法類似**完整**復原模式。  不過，大量記錄模式有一點比較特殊，  它只會保留絕大多數的交易記錄，但是不保存 BULK 行為產生的交易記錄(如建立索引或大量異動資料)。  雖然這個復原模式支援三種備份方式，然而無法保證能將資料庫還原到過去任何時間點。  
```sql
ALTER DATABASE [TestSB] SET Recovery BULK_LOGGED
```

另外，通常在重建索引時，也會產生許多的交易記錄，如果復原模式設定為大量記錄或簡單，就產生不會交易記錄。

## 備份類型

### 完整備份（Full database backups）

「完整備份」會備份以下內容

- 資料庫中的所有物件和資料。
- 備份期間發生的交易。

### 差異備份（Differential backups）

「差異備份」會備份以下內容

- 上一次**完整備份**到**目前之間**的異動資料。
- 只會備份異動的**最後結果**，而不會記錄異動歷程。

### 交易記錄備份（ransaction log backups）

「交易記錄備份」會備份以下內容

- 上一次**交易記錄備份**到**目前之間**新產生的交易記錄。
- 在預設的情況下會自動**截斷交易記錄**(限已committed或cancelled)

#### 交易記錄備份的特性如下：

- 資料的「**復原模式**」必須設定為「**完整**」或「**大量記錄**」。
- 保存資料的完整交易記錄。
- 可以將資料庫復原至過去某一時間點。

#### 差異備份 vs 交易記錄備

差異備份與交易記錄備份容易混淆：  

- 差異備份：僅備份最終結果。還原時也只能還原最後一次差異備份。
- 交易記錄備份：備份每一次的異動記錄。還原時可以指定還原到任意一次的交易記錄備份。

## 其他備份選項

### 記錄特定選項（Log-specific Options）

「記錄特定選項」僅能搭配 [BACKUP](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) 使用，不適用於 [BACKUP DATABASE](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) 。

會使用記錄特定選項，通常都是使用在容錯移轉的作業中。  當主系統發生異常，但資料庫尚可存取的狀況下，通常我們會執行結尾交易記錄備份以保留最完整的資料。  這時候就可以搭配 NORECOVERY 或 STANDBY 選項來備份交易記錄結尾。  若使用 NORECOVERY ，則備份完後主系統資料庫會處於 RESTORING 狀態，將無法再提供給使用者使用。  之後，您再將這結尾交易記錄備份套用到要取代的主要資料庫，以便向前復原這個資料庫。  

若使用 STANDBY 選項，則主系統資料庫會處於 STANDBY  狀態，使用者僅可以唯讀使用資料庫。
```sql
--資料庫在執行完交易記錄備份後，狀態將處於 RESTORING ，無法再提供給使用者使用。
BACKUP LOG [TestDB] To [testdb_bk_dev1] 
WITH NORECOVERY
```

### 檔案群組備份（File and filegroup Backups）

File and filegroup backups back up individual database files and filegroups rather than performing a full database backup.   This method backs up very large databases. You must back up the transaction log when performing a file and filegroup backup.   You cannot use this method if the Truncate Log On Checkpoint option is enabled.

### 只複制備份（Copy-only Backups）

備份時，若使用「[僅複製備份](http://msdn.microsoft.com/zh-tw/library/ms191495.aspx)」（(Copy-Only Backup)）選項，這不會影響正常的備份順序。  僅複製備份的建立與定期排程的傳統備份無關，它並不會影響資料庫的整體備份和還原程序。   

SQL Server 2005 導入的僅複製備份適用於需要執行備份來達成特定用途的情況 (例如，在線上檔案還原之前備份記錄檔)。  通常，僅複製記錄備份用過一次後便會刪除。

## 備份裝置（backup device）

為了方便管理資料庫的備份資料，SQL Server 使用「[備份裝置](http://msdn.microsoft.com/zh-tw/library/ms179313.aspx)」物件，用來建立與實際儲存備份資料的磁碟或磁帶等設備的對應。  

### 建立備份裝置

#### 使用 SSMS

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7W019nLwKSBDkpDPxwXZzKDjyNxXxwJtcYPGh7WZHIjfNJbdpUS4D0BKa8t_zyDUB0xxMzA0bev84TSul90zez_eGTF7EoY_ft4j6uSmEryb7R6y1aIsbJi0cUBkSAmfY6Ky9_5fZqZA/s0/sql-backup-device-1.png)

#### 使用 TSQL

使用 [sp_addumpdevice](http://msdn.microsoft.com/zh-tw/library/ms188409.aspx) 將備份裝置加入至 SQL Server 的執行個體。
```sql
EXEC sp_addumpdevice 'disk', 'TestDB_BackupDevice', 'D:\Database\bak\TestDB.bak'
```

### 備份裝置優點

#### 簡化備份程序

直接選個裝置，不用再指定檔案的存放位置。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXv10XZdzUVzsrZh3T3qBKL_WWnfudmbF0vqIQY-1LcmMVbkJxGj7Ti6WXbTWQYTwXFvZVGAbzIdeeZVdvSOYltZAaifhSyJxXoZRyuxfGTwP7E5i1nz5-tU_6VPc5twN7Jjnrkrk-j54/s0/sql-backup-device-2.png)

#### 容易查看備份內容

直接雙擊備份裝置，可以查詢此裝置中存放哪些資料，包括：備份類型、資料名稱，備份時間等。

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinxeK2Yh7nB83ppwMSCko5ywc1LGJ2YPZxHm562hHdUddWRZWdvGPo8rGKwP6hHzuR0wzy5C7R0WXVRtRvHhtXmqYw3eAk1L5SzI13wfAob9NiC48IGVct1jCntqd6wQDmf-p8Ur48UQE/s800/sql-backup-device-3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinxeK2Yh7nB83ppwMSCko5ywc1LGJ2YPZxHm562hHdUddWRZWdvGPo8rGKwP6hHzuR0wzy5C7R0WXVRtRvHhtXmqYw3eAk1L5SzI13wfAob9NiC48IGVct1jCntqd6wQDmf-p8Ur48UQE/s0/sql-backup-device-3.png)

## 備份壓縮（Backup Compression）

- [MSDN：備份壓縮 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/bb964719.aspx)

備份壓縮會大幅增加 CPU 使用量，但對 I/O 次數或備份時間則相對減少許多。  

備份壓縮使用時有以下幾點限制：

- 壓縮和未壓縮的備份無法在媒體集中並存。
- 舊版 SQL Server 無法讀取壓縮的備份。
- NTbackup 無法與壓縮的 SQL Server 備份共用磁帶。

備份壓縮的選項預設值是不啟用的，你可以在下圖中的地方變更。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilsch6GYEIA5fdmPZrJ3JochyJypKXeBd7RrWmTg0yKFI6T15IPk9kJGs90dhDjautoEdSgAkjyKYXMEdGkL91jDnPfw-oHYfR7gvspliTZt2F-NRo48s-vmRlvbbPEB03BsTMn1NkROw/s0/sql-backup-compress.png)

也可以透過指令來變更壓縮選項的預設值。
```sql
EXEC sys.sp_configure [backup compression default], 1
GO
RECONFIGURE WITH OVERRIDE
GO
```

注意，它只是壓縮選項的預設值，當真正執行壓縮時，你可以明確的使用 WITH NO\_COMPRESSION 或 WITH COMPRESSION 來指定是否使用壓縮。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjS9vXY5tpZuXxlhzd_2YWcpA9nWfjGREIcPdW13YZPnPG1qAh680MRJCiVHXxvIoXPkiL113bOzvpsM5qcNLHSVNKQ0aCjDlC43pSLwBcma8c86-ygP91_9NWBzoGkwYNUhgEotNUB7Bc/s0/sql-backup-compress-2.png)

## 資料庫檢查點（Database Checkpoints）

- [MSDN：資料庫檢查點 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms189573.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKkaqlrrFsJbb7f8asaXMJCLYe1dNxwStX701224qHFUAEMkpox_KunfyBRh8haBO-ioHOxO8he2DkwYj5kygZ-CiG-JG67m-CMEIalNL1SG_eS_RAbqhyHtxQHj-c1c_BIinoy3YBxc0/s0/sql-checkpoint.png)

Database Engine 會定期在每一個資料庫上發出檢查點 (Checkpoint)，用以將目前記憶體中**已修改的頁面(Dirty Page)**和**交易記錄**資訊從記憶體寫入磁碟上，同時也會記錄有關交易記錄的資訊。  Database Engine 會自動管理檢查點，通常每分鐘發生一次檢查點。  

你也可以變更檢查點發生的頻率，如圖中的復原間隔(recovery interval)設定。  或者使用 [ALTER DATABASE](http://technet.microsoft.com/zh-tw/library/ms174269.aspx) … TARGET\_RECOVERY\_TIME 指令設定。

## 媒體集、媒體家族與備份組

- [MSDN：媒體集、媒體家族與備份組 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms189573.aspx)

### 媒體集（Media Sets）

- 所有用來進行備份的裝置統稱「媒體集」。
- 「媒體集」內的裝置都必須同一類型，例如可能是三個磁帶，或二個磁碟，不會同時包含磁帶與磁碟。
- 「媒體集」內的裝置數量是固定的，第一次備份使用幾個，往後就是幾個。除非使用 WITH [FORMAT](http://msdn.microsoft.com/zh-tw/library/hh213505.aspx) 重新定義。
- 壓縮和未壓縮的備份**無法**在「媒體集」中一起出現。(SQL Server 2008 Enterprise 和更新的版本才支援壓縮備份。)

**範例**：

1.使用二個備份裝置，進行備份作業，這二個備份裝置即為一個「媒體集」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzXd1Y4CYKRX0_EKLo6l0bSmV56ZbxUtQTdDcRe0wAo2Goiw9xD_EhyphenhyphenlQ9R1elKscfCk_0sdwkNXs896rcKqHIczhMYTcaewb62_JybOL2yFBEEoQXa1ZRu1favcjGHAPurZkbdx-Mguw/s0/sql-mediaset-1.png)

2.若再加入一個備份裝置，會產生錯誤

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSt8LBTsbdyvcC0-SUvZuz_tMicUp1nIu8uflQ6BZOqX2MfOKDi-1SdywpF80zp_NBOD7sUdcpyeomn6orUi7CfJ_Jwg5yWdcOAnXsznxnjYi4hN9mqGMh_aQtcUY0bgPi_2D7WOhYdfA/s0/sql-mediaset-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0N6_R9ixBTxU3uQjYo3POl0OiA0P27vytPxm2Rto9XrDIdEWfWid0LLY9swxkYy4F5B1O2pOxgwmbzKXwmZVdD4VeK8Sv3NUfmlzhUuHoI3NMVnShv0NUs5UkcacK5yRgTKc_CiFFyeU/s0/sql-mediaset-3.png)

3.你可以使用新的媒體集備份

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjErxdc_AVu4cnoxFolUBCdcfVv70rNov9pbs66jPRiQ-5vX98q1EY1SpvHzWYNG3GPkcN9GKYn-a-OYq_SGnacZwwWiVPPesFp49ptG3UD-45upbx1quWAH46bX_1pNkKVU_msR7gdS4/s0/sql-mediaset-4.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiy6ZDu_ahr3OYR3uMKj4LhXA8kez4RBfMd5TCxpyTOkGyhvc-z6_RsLvQfJ3PRJTNV3S7w35yZTO38T2Ch6xIN6b2S6NGLqOpPQ-YXksvXljlGh13QtjAJd7CUBsLa3PaKf7zaQS3-QNc/s0/sql-mediaset-5.png)

### 媒體家族（Media Families）

在單一非鏡像裝置上或媒體集鏡像裝置組上所建立的備份，即構成一個「媒體家族」。  媒體集使用的備份裝置數量決定媒體集內的媒體家族數。  例如，如果媒體集使用兩個非鏡像的備份裝置，此媒體集即包含兩個媒體家族。  

如上面範例中的媒體集即包含兩個媒體家族。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjD3HPp6n9Z1xjReCEsh5cP-UcjbowxiCQVZlKWtgvl7CdbDDpngN8zDauNoL7HceSZUQGB2UXJmScqj4s7TndzXeltvbgZ0AKVxwbjVbKCBZ1aXjlLgGL6kkGZDvSJ5FCZxS-ANPmKUf8/s0/sql-media-family-1.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5I6bBTIcSt0phJNjdyZvffLWv4MwXgKtzI4hsHyjiHqqokiLN0gKllfKAIssmXn0c55sObCK7DYm7KcNweVXszlj63SVurCb5vYaEIQy9K2cy9M2SRHrxjStUtlQz83hUy_UT_8SuZ8Y/s0/sql-media-family-2.png)

### 備份組（Backup Sets）

成功的備份作業會將一個「備份組」(備份內容)加入至媒體集。  
這個備份組是根據備份所屬的媒體集加以描述。  
如果備份媒體僅由一個媒體家族組成，則該家族就包含整個備份組。   
如果備份媒體由多個媒體家族組成，則備份組會分散於其中。   
在每個媒體上，備份組都會包含描述該備份組的標頭。

# 執行備份作業

備份作業共分成三種：完整備份、差異備份、交易記錄備份。  前二者是針對資料庫資料，後者是針對交易記錄。  

在 SSMS 中，備份作業可以單獨執行，也可以設定成排程來執行。

[BACKUP DATABASE](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) 指令可用來執行整個資料庫備份，也可用來備份一個或多個檔案群組。  從 SQL Server 2012 SP1 開始，也支援備份至 Windows Azure Blob 儲存體服務。  

[BACKUP Log](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) 指令則可以在「完整復原模式」或「大量記錄復原模式」下用來備份資料庫的交易記錄。  

## 完整備份

#### WITH  選項

WITH 是 [BACKUP DATABASE](http://technet.microsoft.com/zh-tw/library/ms186865.aspx) 重要的選項，可用來指定要搭配備份作業：

- INIT        ：將備份**覆寫**到現有的備份組。（若沒這個項目，則使用附加）
- FORMAT      ：將備份寫到新的媒體集，若該媒體集已存在則會被覆寫。（使用FORMAT，則不需要Init)
- COMPRESSION ：明確啟用備份壓縮。（若沒這個項目，則進行完整備份）
- DIFFERENTIAL：使用差異備份

下面範例使用二個備份裝置（稱為媒體集）來儲存，SQL Server 會運用平行處理來備份資料庫。  
```sql
--完整備份 (覆寫)
Backup Database [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2] WITH Init

--完整備份 (附加)
Backup Database [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2]

--完整備份+鏡射備份 (覆寫)
Backup Database [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2]
Mirror To [testdb_bk_dev3] ,[testdb_bk_dev4]
WITH FORMAT, Init
```

## 差異備份
```sql
--差異備份 (附加)
BACKUP DATABASE [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2] WITH DIFFERENTIAL;

--差異備份 (覆寫)
BACKUP DATABASE [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2] WITH DIFFERENTIAL, Init;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7VhYQVj74rGz0mjTyyOJ79awOKAdlM_72PV5RYkTXh9vGK9T9P8Nk4SMUjuSpIuG5bJ2r9LSHs1ecMW-Jq-An1HGv47bmgchVyuApXi9uVtksyaAzg8hegQvW9V1eBBYITs-blO6HSk4/s0/sql-full-diff-backup-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVRPa3j90-07XmNld1VEvJo-sKR6ntuFr0KV0jY1yZDAXOYBzWPNzDirxywt3QnNxI9lvBgUVaCbPgPg7K947-LS20r38daW_O41ZG-lg_Ag46q4EjTYH9YUk2y039bfBv58LW7R5bxpk/s0/sql-full-diff-backup-2.png)

## 交易記錄備份

「交易記錄備份」作業僅支「完整」或「大量記錄」復原模式。若資料庫的復原模式為「簡單」，則無法使用。
```sql
--交易記錄備份
BACKUP LOG [TestDB] 
To [testdb_bk_dev1],[testdb_bk_dev2]

--備份交易記錄，並且建立備份副本
BACKUP LOG [TestDB] 
To [testdb_bk_dev1]
Mirror To [testdb_bk_dev3],[testdb_bk_dev4]
WITH FORMAT
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWbAjX7cuXFq7ndF1vVhjhyFhs2LR7JQq_aKI6zhkV9yMJmljHLEwnPiI4CwBiPnhiBgr985hK1gRpDXtCA_QDqaC-6X1mAjyI6avU5zvJqQId0G2c2WGQPZjQWOB_B6IN-ge03VXO5QY/s0/sql-log-backup-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjkql2H-pgXR2KlJitVE4wjIs-CgLYM4l2pG4fE1K8kNd_9tT0sbMNOtBwl9297ElBQkTOR6GK1mHfW66Be5m72K7qExr2Hk1GyEXL6bIh_OdiFkb5XCXqoMHp93hHBnzrfZ40Diuh23k8/s0/sql-log-backup-2.png)

# 檢視備份記錄

可以使用 [記錄檔檢視器(Application Event Log)](http://msdn.microsoft.com/en-us/library/dd206998.aspx) 檢視備份的記錄。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMuSr9hapqvILys3BdCT07wpe1V4_lqVLOwkwCWMk4PoV4Gkgp-SYgcAiuGKBkHwC4w77q26DVvRURxHjx1hVHwl9BEyqIu70UUimR4zWZFbbTSL-MlweOc4AELRdEbNk6NgD_53XgDZM/s0/sql-view-backup-log-1.png)

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhWCr8GUytuWtIZ4HflTOzneLO6ft9U7luFNXjnuNhz4HtR2Em03FB9kCxb1h1oGgJXQye_KwgIUGvhsEY_o6J3HHpBKQ0Gw7Br4VxXmbA0pdsXeluwgZ3wMOsTU5vhksriF-fE1Iy_fQ/s800/sql-view-backup-log-2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhWCr8GUytuWtIZ4HflTOzneLO6ft9U7luFNXjnuNhz4HtR2Em03FB9kCxb1h1oGgJXQye_KwgIUGvhsEY_o6J3HHpBKQ0Gw7Br4VxXmbA0pdsXeluwgZ3wMOsTU5vhksriF-fE1Iy_fQ/s0/sql-view-backup-log-2.png)

也可以由 [msdb.dbo.backupset](http://msdn.microsoft.com/zh-tw/library/ms186299.aspx) 系統資料表查詢：
```sql
SELECT
DATABASE_NAME as '資料庫名稱',
CASE [type]   
WHEN 'D' THEN N'資料庫'  
WHEN 'I' THEN N'差異資料庫'  
WHEN 'L' THEN N'紀錄'  
WHEN 'F' THEN N'檔案或檔案群組'  
WHEN 'G' THEN N'差異檔案'  
WHEN 'P' THEN N'部分'  
WHEN 'Q' THEN N'差異部分'  
ELSE N'NULL'  
END as '備份類型', 
RECOVERY_MODEL as '還原模式',
DATABASE_BACKUP_LSN as '完整資料庫備份之LSN',
FIRST_LSN as '第一個LSN',
LAST_LSN as '下一個LSN',
DIFFERENTIAL_BASE_LSN as '差異備份的基底LSN',
backup_start_date as '備份開始時間', 
backup_finish_date as '備份完成時間',
backup_size 
FROM msdb.dbo.backupset 
WHERE DATABASE_NAME='TestDB2' 
```

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgeQEhUUSeIp0DoI2lp7GOcmGdsIfUcyUYqkogJyi6GIlrHTXmNVrnBLk1d0ZqXbeaV4I61UzIAJv1JfNn50xkPOmQ3R4k4zhweTYeXCg-f8oSculDmxI_k9_rM9FpIum4B5t2_DYA1J7k/s800/sql-view-backup-log-3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgeQEhUUSeIp0DoI2lp7GOcmGdsIfUcyUYqkogJyi6GIlrHTXmNVrnBLk1d0ZqXbeaV4I61UzIAJv1JfNn50xkPOmQ3R4k4zhweTYeXCg-f8oSculDmxI_k9_rM9FpIum4B5t2_DYA1J7k/s0/sql-view-backup-log-3.png)

在這個圖中，可以看到**完整資料庫備份之LSN**都是一樣的。而且每個交易記錄檔的LSN都是循序的。

# 其他備分作業

以上討論的內容都是針對整個資料庫或者交易記錄內容執行備份，另外還有以下幾項備份相關的作業。

## 備份「系統資料庫」（System Databases）

「系統資料庫」（System Database）是資料庫執行個體不可或缺的資料庫。  在每次重大更新之後，有幾個系統資料庫是一定要備份的，包括 msdb、master 和 model。   

所以系統資料庫　都是使用「簡單」的還原模式，所以無須進行交易記錄備備。

## 備份「複寫資料庫」（Replicated Databases）

若備份作業備份的對象是複寫資料庫時，要記得同時備份複寫作業會使用到的系統資料庫。  

- master, msdb, and publication databases at the publisher instance
- master, msdb, and distribution databases at the distributor instance
- master, msdb, and subscription databases at the subscriber instances

## 備份「鏡像資料庫」（Mirrored Databases）

若系統包含鏡像資料庫，它本身就像是一個備份資料庫了，無法對鏡像資料庫再設定備份。

至於如何設定「鏡像資料庫」，請看[這篇](http://vito-note.blogspot.com/2013/07/mirroringaspx.html)。

## 備份「次要複本」 (AlwaysOn 可用性群組)

備份作業可能會對 I/O 和 CPU 造成相當大的壓力。 如果將備份卸載至已同步處理或正在同步處理的次要複本，可讓裝載主要複本的伺服器執行個體上的資源用於第 1 層工作負載（ tier-1 ）。

## 部分備份（Partial Backups）

## 結尾記錄備份（Tail-Log Backups）

當系統發生失敗，我們即將進行復原作業前，必須先做結尾記錄備份。

結尾記錄備份其實就是交易記錄備份，用來備份最後一次交易記錄備份後所發生的交易，以避免還原過程中，遺失了最新的資料。

不過，若沒有打算要復原到最新狀態，也就不一定要做結尾記錄備份。
## 參考資料  

- [BACKUP (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms186865.aspx)
- [RESTORE (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms186858.aspx)
- [SQL Server 資料庫的備份與還原](http://msdn.microsoft.com/zh-tw/library/ms187048.aspx)
- [備份概觀 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms175477.aspx)
- [完整資料庫備份 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms186289.aspx)
- [建立完整資料庫備份 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms187510%28v=sql.110%29.aspx)
- [復原模式 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms189275.aspx)