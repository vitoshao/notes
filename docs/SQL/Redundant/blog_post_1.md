---
title: 資料庫還原
layout: default
parent: Redundant
nav_order: 1
description: "資料庫還原"
date: 2013-07-08
tags: [SQL,Redundant]
postid: "2423035339096132514"
---
當資料庫發生失敗，此時就可以使用現有的備分資料做還原操作。  SQL Server 支援以下三種還原機制：  

- [資料庫還原](http://msdn.microsoft.com/zh-tw/library/ms187495.aspx)：還原整個資料庫，包含還原完整備份或差異備份。
- [檔案和檔案群組還原](http://msdn.microsoft.com/zh-tw/library/ms190710.aspx)：還原一個資料檔或一組檔案，而不還原整個資料庫
- [資料頁還原](http://msdn.microsoft.com/zh-tw/library/ms175168.aspx)：執行分頁還原。（僅適用於使用完整或大量記錄復原模式）

# 資料庫復原

從[備份](http://vito-note.blogspot.com/2013/07/blog-post_275.html)章節中的末段，可以知道，不管中間經歷了幾次完整備份或差異備份，交易記錄備份的LSN都是連續的。  所以基本上 SQL Server 有能力將資料庫的資料還原到任何一個資料庫備份的時間點。  但是，為了加快資料還原的速度，我們通常不會從第一次完整備份開始逐一將交易記錄備份檔還原，而會透過最近一次的完整備份檔來還原資料庫，再透過差異備份和交易記錄備份來縮短還原的時間。  

根據現有的備份資料，可以組合出下列幾個還原策略：

- 只還原完整備份
- 還原完整備份 + 差異備份
- 還原完整備份 + 交易記錄備份
- 還原完整備份 + 差異備份 + 交易記錄備份

## 設定復原狀態

在進行資料庫還原時，你可以設定目前的資料庫狀態，用以告知系統目前資料庫是否還原完全。  底下是 SQL Server 所提供的三種復原選項：

- **Recovery**：這個選項指示，當還原程序執行完之後，資料庫回復正常狀態，可供存取。（此為預設行為）
- **NoRecovery**：這個選項指示，還原程序執行後進入離線模式，資料庫無法使用。
- **Standby**：這個選項指示，還原程序執行後進入資料庫唯讀模式。

### NoRecovery

一個還原過程，可能包含了數個步驟。例如：一次還原完整備份 + 一次差異備份 + 二次交易記錄備份。  當一個步驟執行之後，若不是最後一個還原步驟，則必須使用 NORECOVERY 選項，讓資料庫處於「無法被異動」的狀態。  簡單講，NoRecovery選項讓資料庫保持當在還原中的狀態，此時資料庫無法被存取。  

下面指令是前一個範例中的復原陳述式，前三個還原作業，因為之後還要繼續執行其他的還原作業，所以必須採用 NoRecovery 選項，告訴 SQL 尚不可以開放這個資料庫供人存取。（也可設成 StandBy 選項，表示開放唯讀）
```sql
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' WITH  FILE = 1,  NORECOVERY, REPLACE
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' WITH  FILE = 9,  NORECOVERY, REPLACE
RESTORE LOG [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak'      WITH  FILE = 10, NORECOVERY
RESTORE LOG [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak'      WITH  FILE = 11
```

#### 在 SSMS 中設定該選項

若在 SQL Server 2012 的 SSMS 中操作，則使用以下設定：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhO1UJSop-jxkERpsdWVn1RmPEQyWDeKItRuJXdpMrnHckznJCOiZKMxKgeFaqryrwXJJ0o6uldUB2bup8q0_Cd2ak8wQ7wnvNx3rc8mjVqwR1SD0QGY3zSuEf6MeKumVkWN4wPO4zmRao/s0/sql-restore-option-norecovery.png)

### Recovery

使用 RECOVERY 選項表示，當此還原步驟執行之後，會Commit所有交易，此時資料庫就會處於「備妥可用」狀態。  通常在整個還原作業中的最後一個步驟，要使用 RECOVERY 復原選項。  

如果 NORECOVERY、RECOVERY 和 STANDBY 三者都沒有指定，預設值就是 RECOVERY。

#### 在 SSMS 中設定該選項

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0FZbgv8PEgBpQEih-eS8yx7KhHudoABbwtK4MxUUoKMrsErYhX78v2SPuqX1GrVS1PHxXPffHGgyuVIN6b5a7hVsD3AJmrPjhnm6LbaiPNq2SjLHxAt6I9vpnMfDDKmw67Qhs5nBzUa4/s0/sql-restore-option-recovery.png)

### StandBy

使用 STANDBY 選項表示復原步驟執行後，讓資料庫處於「唯讀」狀態。此選項讓尚在還原中的資料庫，可保持在唯讀狀態。

#### 在 SSMS 中設定該選項

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUaf6TMhAiNlTWfQFzdjteB3IDKMkqOH_bBjwLEm1-oPtEwmjNxbSLKILslG_UlcCYmpDYEpuaFxt6RE-SsdiRwLfDxd0JPRINOYeuo1sH9NiGjUGqLqcc0RPTvkS66JJqVJ2r3Guy8J4/s0/sql-restore-option-standby.png)

## 問題排除：「無法獲得獨佔存取權，因為資料庫正在使用中」

因為資料庫還原時必須獨佔整個資料庫的存取權，所以若進行還原時，若該資料庫尚有其他連線，就會發生還原失敗的問題。  所以，若要讓還原作業順利進行，就必須中斷現有的連線，作法有二種，如下說明：  

### 方法一：使用 who + kill

使用sp\_who查詢目前存取要還原資料庫的連線有哪些，再搭配。

下圖為使用sp\_who敘述的查詢結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgLDYbAp_5VYB-gdfElDoXc65KQlP_oDMvjY5-T9ZsCGxgRw9K8udIg61C8Wv-_0IwfMWJBeCxoUZHcSSEofjxSC00vOtpqoUZuzNMA0XqUpILUgr3zCrJoerwyBFxKaZdw3r-5sWSX_U0/s0/sql-sp_who.png)

使用kill來結束處理程序
```sql
kill 52
kill 53
kill 54
```

### 方法二：將資料庫設定為「單一使用者模式」

將資料庫變更為單一使用者模式，也可以獨佔資料庫，順利進行還原作業。
```sql
USE [master]
GO

--切換成單人獨占模式
ALTER DATABASE [TestDB] SET SINGLE_USER WITH ROLLBACK IMMEDIATE
GO

RESTORE DATABASE ......

--回復成多人模式
ALTER DATABASE [TestDB] SET MULTI_USER  WITH NO_WAIT
GO
```

## 分頁還原（Page Restore）

當受損的資料庫只有一個或多個頁面時，可以使用分頁還原，而不用還原整個資料庫。  

至於如何知道受損的頁面有哪些，你可以由 [msdb..suspect_pages](http://technet.microsoft.com/zh-tw/library/ms174425.aspx) 取得。  當執行庫執行存取資料頁面時，若發生存取問題，這些頁面會被標示為「有疑問」。  有疑問的頁面就會被註記在 msdb 資料庫的 [suspect_pages](http://technet.microsoft.com/zh-tw/library/ms174425.aspx) 資料表中。  
```sql
RESTORE DATABASE [testdb]
PAGE='1:57, 1:202, 1:916, 1:1016'
FROM 'testdb_bk_dev1'
```

## 檔案還原（File Restore）

If a database contains multiple files or filegroups and uses the **full** or **bulk-logged** recovery model, you can perform a [file restore](http://msdn.microsoft.com/zh-tw/library/ms190710.aspx) rather than a full restore.   You might perform a file restore if the volume that hosts a particular file fails or a file becomes corrupt, but the other files used by the database do not have any problems.  

You can perform a file restore by taking the following steps:

- If possible, take a tail-log backup of the database on which you want to perform the file restore. If it is not possible to take a tail-log backup, it will be necessary to perform a complete database restore.
- Restore the corrupt file from the most recent backup that includes the file.
- Restore any differential backups that were taken of the file.
- Restore all transaction logs in sequence taken since the most recent differential, full, or file backup of the file.

# 範例一：還原「完整備份」

### 1. 原始檔案大小

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVxOsu_GzfEk6dQQVvoTPhDBX96DqXQuQil-gJx2oUpvkbrHgWd9atb9PXVY1PSwwJGszDPTiuHWUlpBaTRh1DPad0m9-KpamQ1o7KFEWhOUBHGV5oo1vaBQaAvhowc6f2xuWfy3eJ7Sc/s0/sql-restore-sample1-1.png)

### 2. 建立完整備份
```sql
--建立備份裝置
EXEC sp_addumpdevice 'disk', 'testdb_bk_dev1', 'D:\Database\testdb2008\testdb_bk_dev1.bak'

--執行完整備份 (覆寫)
Backup Database [TestDB] 
To [testdb_bk_dev1] WITH FORMAT
```

下圖顯示備份資料已經寫入備份裝置。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWY4HGzsWWh3_Mnp6vUvGHJtMTk1BGow8O-uUQ34jEdAg0uFhyphenhyphenLBUVbpBZbKxgADAHsLH2f46JW6ZQ-e5rjgHmw7iGvczsuhZgkB42stRDbcRv9QmT_DKzHORxkPVO1l37QQ8ixT-lsaE/s0/sql-restore-sample1-2.png)

### 3. 建立資料異動

步驟２中已經建立了完整備份，接著我們新增多筆資料到資料庫中，可以看見資料庫檔案成長不少。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvRJJL4Dk22hDLLPTcu5fBqeXoIdUoDMvvy0V-g7ipCpM1-DOJ4MxBdTVRVX9CpQ7z12QRetyAtpLVhPhmPOOzNbf18iidSprMc4VjpjxJhfUkoe3cA2Jja7kQHGEBWy30sZ62Hw0-lxI/s0/sql-restore-sample1-3.png)

查詢目前 TestTable 資料筆數
```sql
EXEC sp_spaceused 'TestTable'; 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNAExHg0bsU-AtCHppLht2Alk22vdUbBshzy3tQExLKj_7Q0P08YlmMRTy4mGz3xoofzo3aI6w1slvZvOdjpffZAutl5P2lC81GBaSc9IVP7kTHxABTwGvMsNmcRV8H74qfTlf0wD9Wvg/s0/sql-restore-sample1-4.png)

### 4. 進行還原

使用先前建立的完整備份，進行資料還原。

#### 使用 SSMS 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNkA-N_9p87VAaFPegjn2H4SuuOIoLm6K5zEhP-Be9oqqSqY0UbSfQjVwNvuMCMpX_jiVPZoww67MX1_vjT8HeGHUjsj6HCBKgK3Sg5yy_e9dHtBOQZB5UPd-tsAiX_oe1GMbdRn2AVe0/s0/sql-restore-sample1-7.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDCu96cFo1kebTmyj68nQ2wISyJK4kLkDa0tJOwI3ixLoEhdU8I7Tsfq73FbW-3Y-nsT-1pr4bhn5xCiPXqzUEE0viUuAcDMt2pbx3BWfKe65j4TwFty_x5rCsoRQObHPZS3gDLp8uVB0/s850/sql-restore-sample1-8.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgew9NDzl7V47eDCwSbrXSmJXyMyFLHYyQR6kQhn_d_8MsI6cVXX8HqKMjUwNFBMLCd9uOMzHZ8qwYQuj8Q2X9TjZG20mAN9LlCWySjA6HB8Y6qjchbcmRE8hUaGO3UvzhAWHC54ibmsa4/s850/sql-restore-sample1-9.png)

#### 使用 TSQL 

透過 [RESTORE](http://technet.microsoft.com/zh-tw/library/ms186858.aspx) 指令，可以用來復原資料庫。  其中 MOVE TO 選項是用來指定資料庫檔案的存放位置，若不指定，會使用和原先資料庫相同的路徑。  
```sql
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  
FILE = 1, 
MOVE 'TestDB' TO 'D:\Database\testdb2008\TestDB.mdf',               --指定資料庫檔(.mdf)的存放位置，若不指定，會使用和原先資料庫檔相同的路徑
MOVE 'TestDB_Log' TO 'D:\Database\testdb2008\TestDB_Log.ldf',       --指定記錄檔(.mdf)的存放位置，若不指定，會使用和原先記錄檔相同的路徑
RECOVERY,                                                           --讓資料庫保持備妥
REPLACE                                                             --強制還原
```

還原後，資料庫檔案大小回到原始狀況。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8QZh6O6SM_oV0_heL34snKbgMEEcCh57jvzw_neSgRHuEx3uqnn190B5rk4II4OffOu4bsQGZ7dzWuMA8ZL0g5-TFifxN63MLHQoTdtzhJ8mftja38RGqsQV0NwlGCjRu53rvFyULVLo/s0/sql-restore-sample1-5.png)

資料筆數也復原了。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhb9FJxQveVSMYAMl8f0N-FaYLXbuH37SfF6RYXH2XLUYAHjPXEWKKQUHfhwum4JdRVozOJ5mJlRMszENDrV2EqDlNQpd_ZbwrVbYfQeOWB3Y_wqPt4xNt3_tqx8e72Gyq6A_CiIhjv0vQ/s0/sql-restore-sample1-6.png)

# 範例二：還原「交易記錄備份」

## 1. 檢查備份策略

下面這個範例，假定備份機制是：每週一次完整備份，每天一次差異備份，每八小時一次記錄備份。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIvE4QnqZ0pnKtOfAAT_UiA9f5LXDT335RBjy7BrEh9cMog03Y6vd2ghe9qwpYOr7fKqJrQqpWRjOhkarwGNXcS1mr4Ksb5Kq_9SFK5QGopQeL9N2ie4AOe6kDpNn0R9Co5G9y7H_I-08/s850/sql-backup-set-2.png)

那麼其備份組內容會如下圖所示，共有１１個備份組：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaWOgndyUN8MsMHYKKajCY344Zwtclp9bnqLr8hyphenhyphentSGykU5zQbg0Scypb5VQo2Yr8Fz1FsHDVsEzl5xYZVSHWy0H2gfQGQ4-RbXLcH3WEsVbGWcUIowx8b26Yh7oNwfas4cgkCLdlJ8dc/s0/sql-backup-set.png)

## 2. 還原前準備

### 確認備份裝置

在還原資料庫之前，你必須先確認備份裝置是否正常。

#### 使用 SSMS 確認

若使用 SSMS ，當你由「來源裝置」開啟備份裝置時，若沒有問題，則會自動展開備份組：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQRreM-SNAzgmfiwLwdzre92L15DOu8gGFlVFFFsmOCtBOcvwFTPrJdm_34SVpZ5K_GKokEvD0eRs_kIvMu3mvlxLFikFLcde-okWlcIVZFKBf3PeGpj1TB4GeT3wLzu2DqCeO5PNSBfE/s0/sql-restore-sample2-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQkLwx-mHFnIclKLwcY-ElJNM3DvF_fE4UJ5pwSS9L3IZZDf6FsgBOSAOSnWMToYJgBM3uRu1NGFZEsie8mqp8EYuuTX2Fjkleb9UVg1i27TOLRoSSnjE3ddggybsHWLlq0qMCzAX_7p8/s850/sql-restore-sample2-4.png)

#### 使用 TSQL 確認

你也可以使用 TSQL 檢視備份裝置的內容：

- [RESTORE VERIFYONLY](http://msdn.microsoft.com/zh-tw/library/ms188902.aspx) ：驗證備份檔是否有效，以免因毀損或不完整，導致還原作業失敗。
- [RESTORE FILELISTONLY](http://msdn.microsoft.com/zh-tw/library/ms173778.aspx) ：傳回含有資料庫清單的結果集及備份組所包含的記錄檔。
- [RESTORE HEADERONLY](http://msdn.microsoft.com/zh-tw/library/ms178536.aspx) ：傳回含有特定備份裝置上的所有備份組之所有備份標頭資訊的結果集。
```sql
--檢查備份檔是否 OK
RESTORE VerifyOnly   FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'

--列出備份檔案內包含哪些資料庫檔案
RESTORE FileListOnly FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSQhOa7_1Oe-EeaW-x02Nb7apAB65mOiQ5JyTwfvCGMX7jsKbWcyQLEJKSYZ-xTgtXoX8dshYuVh4FNoKasxbmYSKB17_1rQJ9WYVZ2gCqqBGdsNgCU26V1V29jSulr0YcjATSX8TbJZo/s0/sql-restore-sample2-5.png)
```sql
-- 列出備份檔的備份資訊
RESTORE HeaderOnly   FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhlFZBejNwy-ssIwgIP6fMe9vr5ZyqKCGWb8476uas14Sgb3ZlHxrkusM_LgSPDMHc96cn50gFGLlJqUda5-S9X9W2OmH1YAHLOf81qUyvMFK4SBlDi-Fv_9g7gVcevxKlw1NKp-l_8Cnc/s850/sql-restore-sample2-6.png)

RESTORE HeaderOnly　回傳的結果集中，幾個重要資訊如下：

- FirstLSN ：表示備份組中第一個記錄的記錄序號
- LastLSN ：表示備份組之後下一個記錄的記錄序號
- CheckpointLSN ：在建立備份時，最近的檢查點之記錄序號。
- DatabaseBackupLSN ：最近完整資料庫備份的記錄序號。

所以，從上圖中可以看出幾項資訊：

- 後四筆記錄都是以第一筆完整備份當做基礎
- 後四筆記錄最近的檢查點之記錄序號在第二筆。
- 所有的交易記錄備份，其記錄序號都是連貫的，不管中間是否有完整或差異備份。

### 確認還原時間點

#### 使用 SSMS 設定還原內容

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIvE4QnqZ0pnKtOfAAT_UiA9f5LXDT335RBjy7BrEh9cMog03Y6vd2ghe9qwpYOr7fKqJrQqpWRjOhkarwGNXcS1mr4Ksb5Kq_9SFK5QGopQeL9N2ie4AOe6kDpNn0R9Co5G9y7H_I-08/s850/sql-backup-set-2.png)

不同的還原時間點，所需要的備份組會有所不同。

如上面這個例子：  
如果要還原到星期一的16:00，則需要 F1 + L1 + L2  
如果要還原到星期二的16:00，則需要 F1 + D1 + L4 + L5  
如果要還原到6/11的01:00，只需要 D2 即可  

如上面例子，若要還原到最近點，則需要勾選備份組編號１、９、１０、１１即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhflh3_8QZUPOPo6qKBYE_0CBkWFHmjeNn5vl2lM4gz-yl6-8rrlLwm6ffEBj_0tY9LMFjs-d86v4eJHFTks2biGpMuc1rzZREH4fz53S6hTLjEoM4PvZjxPMTqqxgUbUFTkf6tGrOQaUU/s850/sql-restore-sample2-7.png)

#### 使用 TSQL 設定還原內容
```sql
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  FILE = 1, NORECOVERY, REPLACE
GO
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  FILE = 9, NORECOVERY, REPLACE
GO
RESTORE LOG [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  FILE = 10, NORECOVERY
GO
RESTORE LOG [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  FILE = 11
GO
```

#### 還原至特定時間

當使用「交易記錄還原」若沒有指定時間，則會還原該次備份中的所有交易，你也可以使用 StopAt 選項，指定只還原至特定時間。
```sql
RESTORE DATABASE [TestDB] FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
WITH FILE=1, NoRecovery , Replace
RESTORE DATABASE [TestDB] FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
WITH FILE=2, NoRecovery
RESTORE DATABASE [TestDB] FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
WITH FILE=3, NoRecovery
RESTORE DATABASE [TestDB] FROM DISK='D:\Database\testdb2008\testdb_bk_dev1.bak'
WITH FILE=4, Recovery, StopAt = '2013-07-11 14:45:35.000'	                --該時間點後面的記錄將不會還原
```

# 範例三：使用 RESTORE 指令還原

[RESTORE](http://technet.microsoft.com/zh-tw/library/ms186858.aspx) 的用法，請參考以上範例。  

下面範例示範，如果檔案的位置和原先資料庫檔案位置不同，則必須使用 MOVE TO 選項將檔案設定到新的位置。
```sql
RESTORE DATABASE [TestDB] FROM  DISK = N'D:\Database\testdb2008\testdb_bk_dev1.bak' 
WITH  
FILE = 1, 
MOVE 'TestDB' TO 'D:\Database\testdb2008\TestDB.mdf',
MOVE 'TestDB_Log' TO 'D:\Database\testdb2008\TestDB_Log.ldf',
RECOVERY, 
REPLACE
```

# 範例四： RESTORE 到指定時間點

使用 RESTORE LOG 還原交易記錄時，可以使用以下選項，將交易記錄還原到指定的時間：

- stopAT：將交易記錄還原到指定時間
- stopATmark：將交易記錄還原到標記
- stopBEFOREmark：將交易記錄還原到標記之前

下面例子，不同的資料異動，只是為了區分各個備份資料中有不同的資訊。
```sql
---------------------------------------------------
--完整備份 (覆寫)
---------------------------------------------------
insert customers(No,Name) values ('1','A')
Backup Database [DB1] To DISK = N'D:\Database\tmp\DB1.bak' WITH FORMAT	
---------------------------------------------------
--第一次交易備份 (覆寫) 
---------------------------------------------------
insert customers(No,Name) values ('2','B')
BACKUP Log [DB1]  To DISK = N'D:\Database\tmp\Test_Log.bak' WITH FORMAT
---------------------------------------------------
--第二次交易備份 (附加) 
---------------------------------------------------
insert customers(No,Name) values ('3','C')
BACKUP Log [DB1]  To DISK = N'D:\Database\tmp\Test_Log.bak'

---------------------------------------------------
--未備份交易
---------------------------------------------------
insert customers(No,Name) values ('4','D')
WAITFOR DELAY '00:00:05';
insert customers(No,Name) values ('5','E')
delete customers
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgtnnyj_24pttKm35JlgZDl5zZpno7H85yfkGRVkLmIRYDmYWQzJRJTuBmubIHqjbHPvLM2S5K1RYtwcIfLaE6YSX__F2kPMQj6rC1mh3xKqibUl9U5d7gYy_h6ooAnwZYIDScNPtCpcXQ/s0/sql-restore-sample.png)

上面例子，如果最後一行是個手殘，這時怎麼辦？下面分別針對不同的還原需求進行測試。

## 還原到第５筆

#### 1.結尾備份

若需求要還原到第５筆，該資料交易的時間點在最後一次交易記錄備份之後，所以此時要先做一次結尾備份。
```sql
--結尾備份
BACKUP LOG [DB1] To DISK = N'D:\Database\tmp\DB1_Log_Tail.bak'
```

#### 2.檢查備份檔

在執行還原前，最好先檢查一下備份檔中的記錄：
```sql
--列出備份檔內包含備份資訊
USE [DB1]
RESTORE HeaderOnly FROM DISK=N'D:\Database\tmp\DB1.bak' 
RESTORE HeaderOnly FROM DISK=N'D:\Database\tmp\DB1_Log.bak'  
RESTORE HeaderOnly FROM DISK=N'D:\Database\tmp\DB1_Log_Tail.bak'  
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinB5-l6VPsWm4sNePMOfW2Z_p0Y9MhWAAX9kAY8ptaiySdyiVo_Y3wVS4udjHq9O0fJrMElTtHoWt5JBMhAcsMjZZaU-TJrv6YcoRiUHHi_sKtGakwbPoU4wHhzvYcP1-BD6ArXz4s3ts/s850/sql-restore-HeaderOnly.png)

由上圖中可以看到，二筆交易記錄備份和結尾交易記錄備份都是以完整備份為基礎，且三筆交易記錄備份也都是連續的。

#### 3.執行還原
```sql
USE [master]

--切換成單人獨占模式
ALTER DATABASE DB1 SET SINGLE_USER WITH ROLLBACK IMMEDIATE

--還原完整備份 (加入NORECOVERY)
RESTORE DATABASE DB1 FROM DISK = N'D:\Database\tmp\DB1.bak' WITH NORECOVERY, REPLACE
GO

--還原交易備份
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log.bak'  WITH FILE = 1, NORECOVERY
GO
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log.bak'  WITH FILE = 2, NORECOVERY
GO
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log_Tail.bak'  WITH RECOVERY, STOPAT = N'2013-11-08T11:27:30.999'
GO

--回復成多人模式
ALTER DATABASE DB1 SET MULTI_USER WITH NO_WAIT
```

上面執行語法中，使用 StopAt 選項，指定只還原至該交易備份中的特定時間，才不會資料又都被 delete 交易記錄刪除了。

## 還原到第４筆

若要還原到第４筆，方法和上面一樣，只要使用 StopAt 選項時，將還原時間點設定在第４筆和第５筆交易中間即可。

只是要注意一點，除了最後一個還原指令是加上 「 WITH RECOVERY」，其他的都要加上「  WITH NORECOVERY」。

## 還原到第２筆

若只是要還原到第２筆，就無須做結尾備份。直接利用完整備份和第一次交易備份即可。
```sql
--還原完整備份 (加入NORECOVERY)
RESTORE DATABASE DB1 FROM DISK = N'D:\Database\tmp\DB1.bak' WITH NORECOVERY, REPLACE
GO

--還原交易備份
RESTORE LOG DB1 FROM DISK = N'D:\Database\tmp\DB1_Log.bak'  WITH FILE = 1, RECOVERY
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVwLMS94VXc6uamm5SMBDgTRAJdZu9Yvg1AllSuShqT3oFhJsPppQZBmXnT-zIJ3yv6G2RCjr4tEfxljZzUiKo7_pN3HIuLFX24p0bb8T_x6n5D_ehZeeugHzPkt3fvZaBE9gvvCs9w8c/s0/sql-restore-resutl.png)
## 參考資料  

- [BACKUP (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms186865.aspx)
- [RESTORE (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms186858.aspx)
- [SQL Server 資料庫的備份與還原](http://msdn.microsoft.com/zh-tw/library/ms187048.aspx)
- [完整的資料庫還原 (完整復原模式)](http://msdn.microsoft.com/zh-tw/library/ms187495.aspx)
- [還原和復原概觀 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms191253.aspx)
- [將資料庫還原到備份中的時間點](http://technet.microsoft.com/zh-tw/library/ms190244%28v=SQL.105%29.aspx)