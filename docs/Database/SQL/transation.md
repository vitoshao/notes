---
title: Transactions
layout: default
parent: SQL
nav_order: 3
description: "Transactions"
date: 2013-05-02
tags: [SQL,Modify]
postid: "6123784607207513391"
---
# Understanding Transaction

## 交易行為

交易（Transaction）指的是一連串的工作指令，在執行時必須全部成功或全部失敗。  使用交易可以滿足以下四個屬性，一般簡稱 ACID 規範。  

- **不可部份完成性（Atomicity）**  
交易必須全部完成執行，要不就是全部不執行。
- **一致性（Consistency）**  
交易完成時，全部的資料必須維持一致性的狀態。
- **隔離性（Isolation）**  
交易中的資料會處於隔離狀態，無法被其它交易存取。
- **耐久性（Durability）**  
交易完成之後，其作用便永遠存在於系統之中。

## 交易模式

- **明確交易模式**  
每個交易都必須明確定義交易的啟動與結束。
- **隱含交易模式**  
若前一個交易結束或回復後，就會自動啟動一個新的交易。  也就是您不需要處理交易的啟動，只要認可或回復每一個交易即可。  這個執行個體會持續產生隱含交易鏈，直到隱含交易模式關閉為止。
- **自動認可模式**  
這是 SQL Server 的預設交易管理模式。  每一個 TSQL 陳述式都會在完成時認可或回復。
- **控制交易**  
透過指定交易來控制何時啟動及結束交易。
- **分散式交易**  
跨越二個或多個稱為[資源管理員(Resource Governor)](http://msdn.microsoft.com/zh-tw/library/bb933866.aspx)的伺服器的交易。

## Transaction Levels and States

You can detect the transaction level or state by using two system functions:

### Transaction Levels

[@@TRANCOUNT](http://msdn.microsoft.com/zh-tw/library/ms187967.aspx) can be queried to find the level of transaction.

- A level of 0 indicates that at this point, the code is not within a transaction.
- A level &gt; 0 indicates that there is an active transaction, and a number &gt; 1 indicatesthe nesting level of nested transactions.

### Transaction States

[XACT_STATE](http://msdn.microsoft.com/zh-tw/library/ms189797.aspx) can be queried to find the state of the transaction.

- A state of 0 indicates that there is no active transaction.
- A state of 1 indicates that there is an uncommitted transaction, and it can be committed.
- A state of -1 indicates that there is an uncommitted transaction, but it cannot be committed due to a prior fatal error.

# Defining Explicit Transaction

明確交易要自行描述交易的開始與認可，以下三個陳述式是用來定義明確交易各個步驟：  

- BEGIN TRANSACTION ：啟動交易。
- COMMIT TRANSACTION ：如果沒有發生錯誤，可用來順利結束交易。
- ROLLBACK TRANSACTION ：發生錯誤時，用來清除交易。交易所修改的一切資料都會回到交易啟動時的狀態。

**注意**：當 Rollback 發生時，雖然所有資料都會回復到先前的狀態，但是某些機制並不會重置，例如識別(identity)型態欄位的 seed 值。  

例如下面例子，第一次交易被 Rollback ，雖然資料列沒有新增成功，但是 depno 這個識別欄位，它的值就會跳了二號。
```sql
Begin Transaction
insert dep values ('MR')
insert dep values ('MIS')
Rollback TRANSACTION
Begin Transaction
insert dep values ('MR')
insert dep values ('MIS')
Commit Transaction
```

## 如何達到 ACID 交易

下面例子，如果 EmpName 為 Unique 欄位，那麼執行第二道指令時一定會產生錯誤。  例子中，雖然我們啟用了 TRAN 交易，但是最後是叫用 COMMIT TRAN ，所以最後資料庫不但 Insert 了第一筆，還會繼續完成第三行指令。  
```sql
BEGIN TRAN

INSERT Emp(EmpName,DepNo) Values ('vito1', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito1', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito3', 1) 

COMMIT TRAN  -- Emp 資料表將被 Lock ，直到 Commit 或 Rollback
```

若想避免上面例子中的問題，有二個做法：

### 1. 啟用 XACT\_ABORT 以進行 ACID 交易

1) 直接啟用 [SET XACT_ABORT](http://msdn.microsoft.com/zh-tw/library/ms188792.aspx) 屬性，此時如果有任一個 TSQL 發生問題，會立即終止交易並回復整個交易。  
```sql
SET XACT_ABORT ON 
BEGIN TRAN

INSERT Emp(EmpName,DepNo) Values ('vito7', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito7', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito8', 1) 

COMMIT TRAN
SET XACT_ABORT OFF 
```

### 2. 使用 TRY...CATCH 以進行 ACID 交易

雖然設定啟用 [XACT_ABORT](http://msdn.microsoft.com/zh-tw/library/ms188792.aspx) 可以達到 ACID 要求，但是每次連線時都要啟用設定，用完還得 reset 回去，除了麻煩，還沒辦法進行錯誤處理。  下面例子搭配 [TRY...CATCH](http://msdn.microsoft.com/zh-tw/library/ms175976.aspx) 使用結構化的錯誤處理方式，是執行 ACID 要求比較好的方法。  
```sql
BEGIN TRY
BEGIN TRAN

INSERT Emp(EmpName,DepNo) Values ('vito7', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito7', 1) 
INSERT Emp(EmpName,DepNo) Values ('vito8', 1) 

COMMIT TRAN
END TRY
BEGIN CATCH
ROLLBACK TRAN
END CATCH
```

#### 使用 [XACT_STATE](http://msdn.microsoft.com/zh-tw/library/ms189797.aspx) 函式判斷交易狀態

不過，上面例子是一個比較完美的案例，有時候程式碼進入了 CATCH 區塊，也許都還沒開始 TRANSATION ，也就是行１～行２之間就發生錯誤。  這時候，你根本不能直接在 CATCH 區塊中使用 ROLLBACK。  遇到這種狀況時，你必須使用 [XACT_STATE](http://msdn.microsoft.com/zh-tw/library/ms189797.aspx) 來判斷目前是否有交易正在執行以及該交易目前的狀態。  

- 1 ：An open transaction exists that can be either committed or rolled back.
- 0 ：There is no open transaction; it is equivalent to [@@TRANCOUNT](http://msdn.microsoft.com/zh-tw/library/ms187967.aspx) = 0.
- -1 ：An open transaction exists, but it is not in a committable state. The transaction can **only** be **rolled back**.
```sql
--設定 SET XACT_ABORT ON ，若違反任何資料庫合約，該交易才會變成 uncommittable
SET XACT_ABORT ON;
BEGIN TRY
SET NOCOUNT ON;
BEGIN TRAN
INSERT Emp(EmpName,DepNo) Values ('vito1', 1)  --EmpName為unique欄位
INSERT Emp(EmpName,DepNo) Values ('vito2', 1)  --若有重複就會發生錯誤
COMMIT TRAN		
END TRY
BEGIN CATCH
declare @xact_state int = XACT_STATE();
PRINT CAST(@xact_state as char(2));
IF (@xact_state) = 1    --transaction is committable.
BEGIN
PRINT 'NO ERRORS in TRANSATION'
COMMIT TRAN
END
IF (@xact_state) = -1   --transaction is uncommittable.
BEGIN
PRINT 'ERRORS in TRANSATION'
ROLLBACK TRAN
END
IF (@xact_state) = 0    --no transaction 
PRINT 'NO TRANSATIONS'
END CATCH
SET XACT_ABORT OFF;
```

# Understanding Special ROLLBACK Scenarios

## 巢狀交易

下面是一個沒有具名的巢狀交易(nested transations)例子：  
```sql
Begin Transaction
insert dep values ('AA')
Begin Transaction
insert dep values ('BB')
Commit Transaction
Rollback TRANSACTION

Begin Transaction
insert dep values ('CC')
Begin Transaction
insert dep values ('DD')
Rollback TRANSACTION
Commit Transaction	
```

當使用巢狀交易時有幾點要注意：

- 若 Commit Transaction 是在內層，它會Commit最接近的那個開始交易。（行５－＞行３）
- 若 Rollback Transaction 是在內層，不管它在哪一層，它會Rollback所有巢狀中交易。（行１２－＞行１０跟行８）

所以說，巢狀交易的 Commit 或 Rollback 是依照最外層的交易的結束動作而定。  若外層交易被 Commit ，則內部的巢狀交易也會被 Commit 。若是外部交易被 Rollback ，則不管內部交易是否個別被 Commit ，所有的內部交易都會被 Rollback 。

## 交易儲存點（Transaction Savepoints）

由以上說明知道，在巢狀交易中，任何一個內層交易只要執行 Rollback ，整個巢狀交易都會被 Rollback，那麼要如何做到局部的 Rollback 呢？那就必須使用[交易儲存點](http://msdn.microsoft.com/zh-tw/library/ms178157.aspx)（Transaction Savepoints）。  
```sql
Begin Transaction
insert dep values ('CC')
Save Transaction TrnA           -- 透過 Save Transaction 定義一個 Savepoints
insert dep values ('DD')
Rollback Transaction TrnA       -- Rollback 到上述 Savepoints
Commit Transaction	
```

# Gathering Information About Transaction

- [@@TranCount](http://msdn.microsoft.com/zh-tw/library/ms187967.aspx) ：This global variable return the number of open transactions in the current session.
- [sys.dm_tran_active_transactions](http://msdn.microsoft.com/zh-tw/library/ms174302.aspx) ：傳回有關 SQL Server 執行個體之交易的資訊。
- [sys.dm_tran_current_transaction](http://msdn.microsoft.com/zh-tw/library/ms186327.aspx) ：傳回一個顯示目前工作階段交易之狀態資訊的資料列。
- [sys.dm_tran_locks](http://msdn.microsoft.com/zh-tw/library/ms190345.aspx) ：傳回目前使用中之鎖定資源的資訊。
```sql
select @@trancount
select \* from sys.dm_tran_locks
select \* from sys.dm_tran_active_transactions
```

# Understanding Locking

「**鎖定（Locking）**」與「**隔離層次（Isolation Level）**」都是資料庫引擎用來操作**並行控制**所使用的方法。  

## 並行控制（Concurrency Control）

當有許多人會同時嘗試修改資料庫中的資料時，必須實作一個控制系統，這樣某一個人所做的修改才不會嚴重影響到另一個人所做的修改。這就叫做並行控制（Concurrency Control）。  

並行控制理論大至可分為兩大類：

- **悲觀鎖定（Pressimistic Lock）**：  
資料庫引擎認為更新資料期間，其他的使用者也會同時來存取相同資料，所以為了確保資料的正確性，在資料的操作一關始時，即立即鎖定資料，直到操作結束才釋放鎖定。  這個方法的優點是資料正確性較高，但是如果在鎖定過程中系統掛了，或者發生 exception 後沒有解除鎖定，就很容易發生死結問題。
- **樂觀鎖定（Optimistic Lock）**：  
資料庫引擎認為更新資料期間，不會有其他的使用者會來存取相同資料，因此不對資料進行鎖定，讓存取的速度可以加快。  它在資料變更時，會查看在讀取資料之後，是否有其他使用者變更了該資料。若有其他使用者更新了該資料，就會產生 Optimistic Locking Exception 錯誤，也就是 DbConcurrencyException 例外狀況。

如果沒有做好並行控制，可能會發生 Lost Update, Dirty Read, NonRepeatable Reads, Phantom Read 等問題：

- **Lost Update（更新遺失）**：  
這個問題是指某一交易對欄位進行更新的資訊，因其他交易的介入而遺失。  
例一：下面這個例子，資料將回覆到B更新前的狀態。  
1.交易A更新欄位1  
2.交易B更新欄位1  
3.交易A COMMIT  
4.交易B ROLLBACK  
例二：下面這個例子，B所做的更新將會遺失，又稱為二次更新遺失。  
1.交易A更新欄位1  
2.交易B更新欄位1  
3.交易B COMMIT  
4.交易A COMMIT
- **Dirty Read（髒讀）**：  
這個問題是指某一交易允許其他交易讀取 Uncommitted 的資料，其他交易就可能讀到Dirty的資料。  
例：下面這個例子，如果交易A做了Rollback，那麼交易B讀取到的資料就可能與實際資料庫中的資料不一致。  
1.交易A更新欄位1  
2.交易B讀取欄位1  
3.交易A ROLLBACK  
4.交易B COMMIT
- **UnRepeatable Reads（無法重複的讀取）**：  
這個問題是指某個交易兩次讀取同一欄位的資料發生不一致的狀況。  
例：下面這個例子，交易A在步驟１和４所讀取的兩次欄位1資料，可能得到不同的結果。  
1.交易A讀取欄位1  
2.交易B更新欄位1  
3.交易B COMMIT  
4.交易A讀取欄位1
- **Phantom Read：（幻讀）**  
這個問題是指某個交易A進行兩次查詢，但在二次查詢中，若有其他的交易B插入或刪除資料，則A第二次查詢時得到的資料多出或缺少資料。  
1.交易A進行查詢得到五筆資料  
2.交易B插入一筆資料  
3.交易B COMMIT  
4.交易A進行查詢得到六筆資料

## 鎖定資料粒度（Lock Granularity ）

當 SQL Server 在執行交易時，底層的的 DB Engine 會根據交易內容進行不同粒度（Granularity）的資源進行鎖定。  

下表顯示Database Engine可以鎖定的資源。

- RID：使用資料列識別碼（row identifier ）來鎖定單一資料列。
- KEY：索引中的資料列鎖定
- PAGE：資料庫中的 8 KB 頁面，例如資料或索引頁面。
- EXTENT：連續八個頁面的群組，例如資料頁或索引頁面。
- TABLE：一整個資料表，包含所有資料和索引。
- FILE：資料庫檔案
- DATABASE：一整個資料庫。
- ...

## 鎖定模式（Lock Mode）

[鎖定模式](http://msdn.microsoft.com/zh-tw/library/ms175519.aspx)是指並行交易時，鎖定的資源是否可被存取。  鎖定的目的就是要隔進交易，每一種鎖定模式對效能都有不同成度的影響，必須依據資料庫的讀寫更新頻繁性來設定。  SQL Server 2008 提供以下幾種資源鎖定模式：  

- **共用鎖定 (Shared Locks)(S)**：  
這個鎖定模式，允許並行交易使用共用鎖定，也就是交易尚未結束，其他交易也可以同時讀取相同資源。  當讀取資料時，SQL Server就是使用共用鎖定。  ```sql
select \* from Emp WITH (HOLDLOCK)
where empname='shao'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-CIh1Atw-o62ks-rDtKAxEjBUYvRoQp-ljRDpRN6GkPSuYfq4GumSczQVyKoHKznqmb2YieO9i5tPtLv1HqJHQYWXHckbRHMg6bJ0zOGuaF_JeDiuT3ijrP_RQ1-Kff8qMdjcXPeUFrU/s626/sql-lock-shared.png)

PS.在一般的情況下，你將看不到共享鎖定，因為在選取 sys.dm\_tran\_locks 的命令過程中 SELECT 已經釋放鎖定。  這也就是為什麼需要一個額外的 (HOLDLOCK) 提示來看到鎖定的類型。
- **獨佔鎖定 (Exclusive Locks)(X)**：  
當修改資料時，例如 INSERT、UPDATE 或 DELETE，SQL Server會使用獨佔鎖定。  在交易結束之前，其他交易的鎖定請求都會被拒絕，確保不能對相同資源同時進行多重更新。  ```sql
UPDATE Emp SET DepNo='5' WHERE EmpName='vito7'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4SU9G1Fr4uRy2DCU6f_u84mkbsgl0sxeYcMGTzEGMVdbymrAAeGhMrIYEKAQKQDsAdReQNk4kJPiaKh7xA7W1SJvSZTelAqRMtravCg-VnMGG1ob-6cGveejaQdqVNGwd76_lGyeIf1M/s626/sql-lock-exclusive.png)
- **更新鎖定 (Update Locks)(U)**：  
更新鎖定是一種分享和獨占的混合鎖定。  在DML執行修改前會使用共用鎖定，此時其他交易可以讀取被鎖定的資料，但不可以修改。一旦開始修改後，就變成了獨占鎖定，直到交易結束。  用於可更新的資源上。下面例子，我們使用 UPDLOCK 提示來轉換更新鎖，讓已讀取的資料由共用鎖定變成更新鎖定。  ```sql
select \* from Emp WITH (UPDLOCK)
where empname='shao'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhsSUQR4gmN_oG0TnWBG2mNX_ryi215bcemMAC9ooC6H-vkRZbdQtB4vw6t4RJCrxYcMjD5BokzwPXbwjDGTb4D2xIrG6lV3MoxbITVMXQcnZpRILmEbvjNfXc5vViu4aECFI9n0jupEMs/s601/sql-lock-update.png)
- **意圖鎖定 (Intent Locks)(IS, IX, SIX)**：  
意圖鎖定的意思是當一個交易通知其他的交易，他有 "意圖" 去鎖定資料，防止其他交易去取得一個更高層級的鎖定。  意圖鎖定的類型為：意圖共用 (IS)、意圖獨佔 (IX) 與共用意圖獨佔 (SIX)。
- **結構描述鎖定 (Schema)(Sch-M, Sch-S)**：  
當物件發生結構改變時，將封鎖存取物件資料。
- **大量更新鎖定 (Bulk Update)(BU)**：  
當使用 TABLOCK 提示進行大量資料更新時，Database Engine 會使用大量更新 (BU) 鎖定。  大量更新鎖定允許多個執行緒將資料同時大量載入到相同資料表，同時禁止未大量載入資料的其他處理序存取該資料表。
- **索引鍵範圍鎖定 (Key-Range Locks)**：  
當使用序列化交易隔離層級（serializable transaction isolation level）時，資料庫會使用索引鍵範圍鎖定。  也就是保護在該 TSQL 所讀取的資料集範圍內，無法進行幽靈插入或刪除。  ```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN TRAN

UPDATE Emp SET DepNo='5' where empname like 'vito%'

ROLLBACK
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgE74ij0XqjgDIHJCMlPKPwsEV0D1Y9JqW2xSyPeJnR7cHJU73RCvYbdlLVti0RPlGCujha7GDzwhrZjxT-CEHyuB4riXT7-NJUeJlvKNngJ2ZM6EBMc8UI4Uob5U1hm-cnMD3kMwNV97o/s735/sql-lock-key-range.png)

## Deadlock

## Reports on Lock Status

There are many options for viewing lock status within your computer running SQL Server.

- You can use **SQL Profiler** to capture lock and blocking information.
- You can use the **System Monitor** that is part of the performance console (perfmon) to capture statistics on lock wait times, locks per second, and so on.
- You can use the **sys.dm\_tran\_locks** dynamic management view (DMV) to gather information on locks being held by transactions.
- You can use the **Activity Monitor** in SSMS to see information on blocking processes.

### sys.dm\_tran\_locks

要查看鎖定的狀態，最簡單的方法就是使用 [sys.dm_tran_locks](http://msdn.microsoft.com/zh-tw/library/ms190345.aspx) 這個 DMV ，例如：  
```sql
SELECT 
str(request_session_id, 4, 0) AS spid,
convert (varchar(20), db_name(resource_database_id)) AS DB_Name,
case when resource_database_id = db_id() and resource_type = 'OBJECT'
then convert(char(20), object_name(resource_Associated_Entity_id))
else convert(Char(20), resource_Associated_Entity_id)
end as object,
resource_type, request_type, request_status, request_mode
from sys.dm_tran_locks
order by request_session_id, 3 desc
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjg4mCe0_81eyZryYSf15YBgi1zbn_vDrwoppVopFqnicpEw2wU59kNAgL1W7bDsEkhygA13diXXSiq4OxGgeUi1fl_TxaBZtCmk7X3N__ccFMSk0M00bzG1WfxHb_5xCs93dUMYPIZwDM/s618/sql-lockmode.png)

### Using SQL Server Extended Events

[SQL Server 擴充事件](http://technet.microsoft.com/zh-tw/library/bb630354.aspx)是一種用於伺服器系統上的事件處理系統。  例如：FindBlocker 和 lock\_count。  這些事件可以與 WindowsEventLogs, SQL Profiler, 或 System Monitor 結合。

### Using DBCC LOG

SQL Server 也包含資料庫主控台命令（Database Console Commands，DBCC），  透過 DBCC [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) 陳述式，也可以用來取得目前交易記錄(transation log)中的資訊。語法如下：  
```sql
DBCC LOG (<databasename>, <output identifier>)  
```

例：
```sql
DBCC LOG (test, 4)
```

# 隔離層次（Isolation Level）

隔離層次主要是要設定當交易正在進行時，其他的交易動作是否可以讀寫處理中資料，也就是針對已鎖定的資料設定是否允許被讀寫。  每個 connection 只能設定一個隔離層次，所以若沒明確指定變更，這個隔離層次設定對同一個連線就持續有效。  要設定隔離層次，可使用 [SET TRANSACTION ISOLATION LEVEL](http://technet.microsoft.com/zh-tw/library/ms173763.aspx) 陳述式來設定，其語法如下：（預設值為READ COMMITTED）  
```sql
SET TRANSACTION ISOLATION LEVEL 
{ READ UNCOMMITTED
| READ COMMITTED
| REPEATABLE READ
| SNAPSHOT
| SERIALIZABLE
}
```

以下是各種不同隔離層次的說明：  

- **Read Uncommitted**  

這個隔離層次**允許讀取尚未認可的資源**。這個隔離層次因為幾乎沒有隔離效果，容易發生 Dirty Reads 問題。  例如下圖中，右邊的交易還在進行中，左邊的交易設定成 Read Uncommitted，所以資料可以讀取的出來，但這資料就有可能有 dirty 問題。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj60J3HhURFRUoGUUcgmlqnFhZH9lvhj5VjtFEjqcV-HZC3I7C5jgcWrc-jr5nUlhCgxjlNBEwvoofXtLhPY465iEi6nAg2u4cww-FN35SrrwtWOBKNssdORVizs3BFSad61qyyMT9bCSw/s850/sql-read-uncommitted.png)
- **Read Committed**  

這個隔離層次**不允許讀取尚未認可的交易**，但是**允許讀取已認可的交易**。(此為預設隔離等級）。  
它的優點是鎖定範圍小，對效能影響不大，而且可以保證資料的修改是已認可的。  但是它卻有可能會發生[幽靈讀取](http://technet.microsoft.com/zh-tw/library/ms190805%28v=sql.105%29.aspx) (Phantom Read) 和[不可重複讀取 (Nonrepeatable read)](http://technet.microsoft.com/zh-tw/library/ms190805%28v=sql.105%29.aspx) 的問題。  例如下圖中，右邊的交易還在進行中，左邊的交易設定成 Read Committed，它就會一直等待到右邊交易 committed 之後才讀取的出來。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLyW5Kyfb_1Gu3Gcybngz27rWx58ahtUiWcEJicLaoe1ifKYfWBsOBP772m3mWM7CuMPv_oFp1pQpoAK509ToPf3cmLiuV8Hv3gWO7Mh9k0oo353CbWmLNDi3IjxejqVdlyANaQj3LY4A/s850/sql-read-committed.png)
- **Repeatable Read**  
不允許讀取尚未認可的交易，但是**允許讀取已認可的交易**。  
而且，**交易中的資料不管是否已認可，在交易完成之前，都不允許修改**，但是，允許新增。
- **Serializable**  
所有的交易都必須按照順序執行，執行期間，**不允許讀取交易內的所有資源**。
- **Snapshot**  
以上４種隔離層級都是 SQL-92 中的規範，Snapshot 是 SQL2005 中才開始加進去的。  此隔離層級是在交易進行前先複製交易內相關的資源到 tempdb 中，所有交易會在這個複本中進行，所以原本的資料讀取不會受到鎖定影響，而在多個交易同時進行時，Snapshot 會使用資料列版本（row version）的識別機制來判斷認可交易時的寫入順序，也因為它是在副本中進行，所以不會發生 Dirty Read, Nonrepeatable Read 與 Phantom Read 的狀態，Snapshot Isolation 的本身是樂觀鎖定，所以在資源鎖定的範圍也不會太大。

### 測試範例１：READ COMMITTED

首先開啟兩個查詢視窗，
```sql
BEGIN TRAN
SELECT \* FROM Products
UPDATE Products SET Price=30 WHERE ProductID=2
--COMMIT TRAN 
--ROLLBACK TRAN
```
```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED 

SELECT \* FROM Products
```

執行時，Query1因為未完成Commit，所以Query2執行時，就會一直等待。  只要Query1完成Commit動作，Query2就馬上可以完成。

### 測試範例２：SNAPSHOT

要使用 SNAPSHOT 隔離層次，必須先啟用 [READ_COMMITTED_SNAPSHOT](http://msdn.microsoft.com/zh-tw/library/tcbchxcb.aspx) 這個選項

底下查詢語法可以用來得知資料庫有無啟用 read committed snapshot isolation
```sql
SELECT name, snapshot_isolation_state, is_read_committed_snapshot_on FROM sys.databases
```

底下語法可以用來設定 read committed snapshot isolation
```sql
ALTER DATABASE [<databasename>]
SET READ_COMMITTED_SNAPSHOT ON
```
## 參考資料  

- [交易 (Database Engine)](http://msdn.microsoft.com/zh-tw/library/ms190612.aspx)
- [鎖定模式](http://msdn.microsoft.com/zh-tw/library/ms175519.aspx)
- [開啟活動監視器](http://msdn.microsoft.com/zh-tw/library/ms175518.aspx)
- [交易相關的動態管理檢視和函數 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms178621.aspx)
- [鎖定使用的藝術 (Part 2) - 隔離層次 (Isolation Level)](http://www.dotblogs.com.tw/regionbbs/archive/2011/04/17/sql.server.isolation.level.aspx)
- [談資料鎖定(Data Lock)](http://www.dotblogs.com.tw/jimmyyu/archive/2010/06/12/data-lock.aspx)
- [並行效果](http://technet.microsoft.com/zh-tw/library/ms190805%28v=sql.105%29.aspx)