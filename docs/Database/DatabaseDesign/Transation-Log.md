---
title: 交易記錄管理
layout: default
parent: Database Manage
nav_order: 10
description: "交易記錄管理"
date: 2013-10-24
tags: [SQL,Database Manage]
postid: "1318220224211650909"
---
交易記錄檔是記錄每筆交易對資料庫所做的修改狀況。  記錄範圍從 BEGIN TRANSACTION 開始，到 COMMIT TRANSACTION 結束，這之間所有的資料表異動狀況都會被記錄下來。  所以系統的更新很頻繁，其交易記錄檔膨脹速度就會很快。  
```sql
--查看交易記錄檔資訊
EXEC sp_helpfile;

--查看交易記錄檔使用狀況
DBCC SQLPERF('LOGSPACE')
```

下圖為交易記錄檔的設定畫面，你可以設定是否啟用自動成長，還有檔案每次成長的大小及檔案上限。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjUfMp0Z2OJHdfFeGSB_RY3tGlb8AZdnoqtWr-6qCz5Nt3DYCcOwAvxcn8_jgqQD5rQN0QY4XcVjX2yHTHigexEQz9JqdLuwU9KT49oBK4RwkQYI5UXn3QQcaPabkK3CGzeYL9n6xr7Mr8/s0/sql-log-setting.png)

# 交易記錄截斷（Log Truncation）

交易記錄檔隨著時間會不停的成長，你必須選擇在適當的時機點，截斷(清除)交易記錄。  交易記錄檔必須在進行**截斷**後，才會釋出記錄檔中的空間，截斷後雖然記錄檔的大小不會變，但是其釋放的空間，可供其他交易記錄繼續重複使用。  所以為了避免記錄檔被填滿或者不斷的擴大，就必須定時的截斷記錄，以避免空間不足或效能下降的問題。  

當下列狀況發生時，交易記錄會自動進行截斷：  

- 在「簡單」復原模式下，DB Engine 會在每一個[檢查點（checkpoint）](http://msdn.microsoft.com/zh-tw/library/ms189573.aspx)之後，自動進行截斷交易記錄。
- 在「完整」或「大量記錄」復原模式下，只要執行「交易記錄」備份，DB Engine 就會自動進行截斷交易記錄。

# 縮小交易記錄檔

交易記錄檔雖然可設定啟用「自動成長」，以避免空間不足的問題。  但是如果沒有做好妥善管理，其檔案大小將隨時間不斷的成長，也是會將實體的磁碟空間用完的。  此時，若想要縮小交易記錄檔的大小，可參考以下幾個方法：  

## SQL SERVER 2000, 2005

若想要縮小交易記錄檔的檔案大小，可以使用指令 [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 來執行壓縮。  不過 [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 並不會將檔案壓縮到小於資料儲存在檔案中所需要的大小，所以為了得到較好的壓縮效果，就必須先清除/截斷(Truncate)交易記錄。  

要手動截斷交易記錄，可以使用 [BACKUP LOG](http://msdn.microsoft.com/zh-tw/library/ms186865.aspx) 指令。  這個指令原本是用來備份 log 用的，但是可以加上 **WITH TRUNCATE\_ONLY** 選項，就不會真正執行交易記錄備份，只會截斷交易記錄。  
```sql
--清除記錄檔的資料
BACKUP LOG [資料庫名稱] WITH TRUNCATE_ONLY

--壓縮記錄檔的大小
DBCC SHRINKFILE(記錄檔名稱, 重設檔案大小下限)
```

範例
```sql
--截斷交易記錄
BACKUP LOG [TestDB] WITH TRUNCATE_ONLY

--壓縮記錄檔
DBCC SHRINKFILE(TestDB_log, 10)
```

備註

若要查看交易記錄檔的使用狀況，可以使用 [DBCC SQLPERF('LOGSPACE')](http://technet.microsoft.com/zh-tw/library/ms189768.aspx) 指令查看。
```sql
DBCC SQLPERF('LOGSPACE')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgg4dfeSKY4jJ53reHcwijtzBc4QEIRT-j9tOgVpj0R3kwsFefCpbp3WOwjoN4na01MLk4wkcQHl02SKsJyowCDi2ojP_TMYPoDCxM9ZgduS8X2bfyU3yr6yb_zrbqgqog53ko2jdotE_0/s0/sql-sqlperf-log-space.png)

## SQL 2008

在 SQL2005 可以使用 Trancate\_only 選項，手動截斷交易記錄，但是到了 SQL Server 2008 之後，BACKUP 指令就取消了 Trancate\_only 選項功能。  因為 SQL2008 認為若系統的還原模式設定為「[完整模式](http://msdn.microsoft.com/zh-tw/library/ms189275.aspx)」，  而為了維護完整的 log chain ，在交易記錄沒有被擷取到備份檔之前，不應該被截斷。  既然交易記錄沒有被截斷，使用 [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 壓縮，也就得不到什麼效果。  

所以，你只要執行一次交易記錄備份，那麼未被截斷的交易記錄就會被截斷，  

另外，若你可以接受這些交易記錄的損失，你也可以使用下面技倆來縮小交易記錄檔：  

1. 先將還原模式變更為「[簡單](http://msdn.microsoft.com/zh-tw/library/ms189275.aspx)」：因為使用簡單的還原模式，交易記錄會自動被截斷。
2. 再使用 [DBCC SHRINKFILE](http://technet.microsoft.com/zh-tw/library/ms189493.aspx) 壓縮。
3. 再將還原模式變更為「完整」。
```sql
USE [TestDB]
GO
--先變更為簡單模式
ALTER DATABASE [TestDB] SET RECOVERY SIMPLE WITH NO_WAIT

--壓縮資料庫
DBCC SHRINKFILE(TestDB_log, 20)

--再變更回完整模式
ALTER DATABASE [TestDB] SET RECOVERY FULL WITH NO_WAIT
GO
```

## 正確清除交易記錄檔

上面二個例子，可能都因為交易記錄檔沒有受到管控，最後發生檔案過大，導至必須手動縮減交易記錄檔的大小狀況。  在本篇文章一開頭有提到，在以下二個狀況發生時，交易記錄會自動進行截斷：  

- 在「簡單」復原模式下，DB Engine 會在每一個[檢查點（checkpoint）](http://msdn.microsoft.com/zh-tw/library/ms189573.aspx)之後，自動進行截斷交易記錄。
- 在「完整」或「大量記錄」復原模式下，只要執行「交易記錄」備份，DB Engine 就會自動進行截斷交易記錄。

所以只要確保交易記錄能夠定時的被截斷，該空間就可以被重複使用，整個交易記錄檔也就不會發生不斷澎漲的問題。  

若系統採用簡單復原模式，由於檢查點預設每分鐘會執行一次，在檢查點之後，交易記錄都會自動進行截斷，所以其交易記錄檔中的空間也就可以不斷地重複使用，就比較不會發生交易記錄檔過於澎漲的問題。  如果在「完整」或「大量記錄」復原模式下，就必須在備份作業中，設定執行「交易記錄」的備份，那麼交易記錄就會在執行完交易記錄備份後被截斷，就可以必避交易記錄檔不斷的成長。

# 交易記錄截斷實驗

使用 [DBCC SQLPERF (LOGSPACE)](http://technet.microsoft.com/zh-tw/library/ms189768.aspx) 可以查看交易記錄檔的使用情形。

下面這個例子，我們使用交易記錄備份來截斷交易記錄，看看交易記錄檔的使用將況。
```sql
--1.先查看目前交易記錄檔的使用將況
DBCC SQLPERF (LOGSPACE)

--2.備份交易記錄
Backup Log [TestDB2] To [TestDB2_BackupDevice]

--3.先查看目前交易記錄檔的使用將況
DBCC SQLPERF (LOGSPACE)
```

下表就是交易記錄截斷前後，交易記錄檔的使用情形，由 55% 降為 2% .

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3i4_SHA93oCDjqsQuE41Rt7qWOYo8Jl4oskcU4X-_za6Wr8mJAYYB2IGDguGz_sY2PHucBy9IASyefpO9tCpBUDPbJ-0X1Cz8_vGXjoy-LMBUL06awIMv7zdCbUtnqPh3NWMQHdZWvLw/s0/sql-log-truncate-table.png)
## 參考資料  

- [交易記錄 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms190925%28SQL.110%29.aspx)
- [交易記錄截斷](http://technet.microsoft.com/zh-tw/library/ms189085%28v=sql.105%29.aspx)