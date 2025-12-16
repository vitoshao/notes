---
title: 資料庫鏡像
layout: default
parent: Redundant
nav_order: 2
description: "資料庫鏡像"
date: 2013-07-11
tags: [SQL,Redundant]
postid: "2351105876256524693"
---
# 什麼是資料庫鏡像（Database mirroring）

「資料庫鏡像」也是一種提高可用性的機制，它可以用來維護單一資料庫的兩份副本，而這兩份副本必須位於不同的執行個體上。  在任何時間內，目前的用戶端都只能使用其中一份資料庫副本， 此份資料庫稱為「**主體資料庫**」(principal server)。  用戶端對主體資料庫所做的更新會套用到其他副本資料庫，也稱為「**鏡像資料庫**」(mirrored server)。  它與「容錯移轉叢集」或「記錄傳送」相較，有易於管理的優點。  

如果主體伺服器變成無法使用時，鏡像伺服器將接替主體伺服器的角色，並將原本的的鏡像資料庫變成為主體資料庫。   

附註  
未來的 Microsoft SQL Server 版本將移除這項功能。請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。請改用 AlwaysOn 可用性群組。   

## 資料庫鏡像概觀

#### 主體伺服器 & 鏡像伺服器

- 「**主體伺服器**」（principal server）：提供資料庫給用戶端的伺服器。
- 「**鏡像伺服器**」（mirroring server）：當做熱或暖待命伺服器。
- 熱待命伺服器（hot standby ）：若資料庫的鏡像工作階段是採用同步（synchronized）方法處理，鏡像資料庫可以扮演熱待命伺服器，支援快速容錯移轉，不會遺失任何已認可的交易資料。
- 暖待命伺服器（warm standby）：當工作階段無法同步處理時，鏡像伺服器只能當做暖待命伺服器使用 (可能發生資料遺失)。

#### 主體角色 & 鏡像角色

- 「**主體角色**」（Principal Role）：擁有主體角色的夥伴稱為「**主體伺服器**」，其資料庫副本就是目前的「**主體資料庫**」。
- 「**鏡像角色**」（Mirror Role）：擁有鏡像角色的夥伴則稱為「**鏡像伺服器**」，其資料庫副本就是目前的「**鏡像資料庫**」。

在「資料庫鏡像工作階段」，一定是由二個夥伴彼此合作，其中一個夥伴扮演「**主體角色**」，而另一個夥伴就扮演「**鏡像角色**」。  擁有主體角色的夥伴稱為「**主體伺服器**」，其資料庫副本就是目前的**主體資料庫**。  擁有鏡像角色的夥伴則稱為「**鏡像伺服器**」，其資料庫副本就是目前的「**鏡像資料庫**」。  

用戶端進行連線時，永遠只會連線到主體伺服器的資料庫，若它對主體資料庫進行任何的新增、修改、刪除指令，這些指令都會透過特定機制在鏡像資料庫中「重做」。  而完成重做的方式就是將交易記錄以資料流的方式傳送到鏡像伺服器，再由鏡像伺服器盡快依序將記錄套用到鏡像資料庫。   這方式與「複寫」作業不同，複寫是在邏輯層級運作的，而資料庫鏡像是在實體記錄層級運作。

## 作業模式

資料庫鏡像工作階段可分成**同步**或**非同步作業**。

- 在非同步作業下，交易不會等待鏡像伺服器將記錄寫入磁碟，即逕行認可，藉以達到最大效能。
- 在同步作業下，交易將同時在兩個夥伴上進行認可，也就是必須等二台伺服器都正常Commit之後才算完成，其代價當然是會增加交易延遲性。

依據作業模式的不同，鏡像工作也就有兩種模式：

### 1. 高安全性模式(High-safety mode)

第一種作業模式為**高安全性**作業模式，支援**同步**作業。  在高安全性模式下，當工作階段開始時，鏡像伺服器會儘快將鏡像資料庫與主體資料庫進行同步處理。  一旦資料庫同步處理完成之後，交易將同時在兩個夥伴上進行認可，代價是會增加交易延遲性。

### 2. 高效能模式(High-performance mode)

第二種作業模式稱為**高效能**模式，會以**非同步**方式執行。  鏡像伺服器會盡量跟上主體伺服器所傳送的記錄。 鏡像資料庫可能會稍微落後主體資料庫。  然而，在資料庫之間的間距通常很小。  但是，若主體伺服器的工作負載很大，或鏡像伺服器的系統超載時，此差距就會變大。  

在高效能模式中，當主體伺服器傳送記錄到鏡像伺服器時，主體伺服器會立即傳送確認給用戶端。它不會等候鏡像伺服器的收條。  這表示交易不會等待鏡像伺服器將記錄寫入磁碟，即逕行認可。  這種非同步作業可以讓主體伺服器在執行時將交易延遲性降到最低，但必須承擔可能遺失資料的風險。

更多內容請參考 MSDN：[資料庫鏡像作業模式](http://msdn.microsoft.com/zh-tw/library/dd207006)

## 見證（witness）

使用資料庫鏡像，若要達到具有自動容錯移轉的高安全性模式，則需要第三個伺服器執行個體，稱為「**見證**（witness）」。  
見證角色本身不提供資料庫服務，它是用來驗證主體伺服器是否正常運作中，以便啟始自動容錯移轉。  
```sql
ALTER DATABASE TestDB
SET WITNESS = 'TCP://192.168.0.3:7024'
```

如需詳細資訊，請參閱[MSDN：資料庫鏡像見證](http://msdn.microsoft.com/zh-tw/library/ms175191.aspx)

## 角色切換與容錯移轉

在資料庫鏡像工作階段的內容中，主體與鏡像角色通常可以用一種稱為「角色切換」的程序交換。  在角色切換中，鏡像伺服器將充當主體伺服器的「容錯移轉夥伴」，鏡像伺服器將接替主體角色，並使其資料庫副本變成線上狀態以做為主體資料庫。  先前的主體伺服器將會擔任鏡像角色，而其資料庫則會變成新的鏡像資料庫。  原則上，這二個角色可以重複來回切換。  

角色切換類型共有三種，至於支援哪一種形式，視工作階段的作業模式而定：

### 1. 自動容錯移轉（Automatic Failover）

只有在**高安全性模式**下搭配**見證**執行的資料庫鏡像工作階段才支援自動容錯移轉。  
資料庫必須已同步處理，而見證也必須連接到鏡像伺服器。

### 2. 手動容錯移轉（Manual Failover）

手動容錯移轉會中斷用戶端與資料庫之間的連接，並將夥伴的角色反轉過來。  
只有**高安全性模式**支援手動容錯移轉。  
夥伴必須互相連接，而且資料庫也必須已完成同步處理。  
```sql
ALTER DATABASE database_name SET PARTNER FAILOVER
```

### 3. 強制服務（Forced service）

在高效能模式與不含自動容錯移轉的高安全性模式中，如果主體伺服器已經故障，但鏡像伺服器仍然可用，就可以使用強制服務。  
由於強制服務會面臨可能遺失資料的風險，所以應該小心並謹慎使用。   
```sql
ALTER DATABASE database_name SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS 
```

PS.  
在任何角色切換案例中，一旦新主體資料庫變成線上狀態之後，用戶端應用程式就可以重新連接到該資料庫來快速復原。

## 並行工作階段

**並行工作**是指同一個伺服器可以同時伴演著不同角色的工作，並沒有限制一台伺服器只能伴演一個角色。  例如：一台伺服器在不同的鏡像工作階段，分別伴演主體角色和鏡像角色。  

通常服器執行個體會固定伴演固定角色，如專門當做夥伴或見證服務。  不過，由於每個工作階段獨立於其他工作階段，所以伺服器執行個體也可以在某些工作階段中當做夥伴，而在其他工作階段中當做見證。  

例如，一個伺服器執行個體最初可能同時是兩個資料庫的鏡像伺服器。  如果其中一個資料庫發生容錯移轉，該伺服器執行個體會變成容錯移轉資料庫的主體伺服器，但同時維持另一個資料庫的鏡像伺服器身分。   

另舉一例，假設有一個伺服器執行個體做為兩個以上資料庫的主體伺服器，並在具有自動容錯移轉的高安全性模式下執行；  如果這個伺服器執行個體故障了，則所有資料庫都會自動容錯移轉到其各自的鏡像資料庫。

# 使用資料庫鏡像的必要條件

要建立鏡像資料庫，必須先確工作環境是否支援鏡像：  

- The primary, mirror, and witness instances must run the same version of SQL Server.
- The primary and the mirror instances must run the same edition
- The witness instance must run only an edition of SQL Server that supports witnessing.

## 必要條件

下表是 SQL Server 2012 各個版本對鏡像的支援：

- **Enterprise**：支援 high-performance, high-safety, 和 witness 模式。
- **Business Intelligence**：支援 high-safety 和 witness 模式。
- **Standard**：支援 high-safety 和 witness 模式。
- **Web**：僅支援 witness 模式。
- **Express**：僅支援 witness 模式。

## 限制

使用鏡像，下列限制也是必須知道的：

- 只可對使用者資料庫做鏡像，無法鏡像 master, msdb, tempdb, model 等系統資料庫。
- 鏡像資料庫在鏡像工作階段不可以重新命名。
- 鏡像不支援 FILESTREAM。
- 在 32 位元系統上，因為每個資料庫鏡像工作階段所耗用的工作者執行緒數目有限制，所以資料庫鏡像最多可以為每一個伺服器執行個體支援約 10 個資料庫。
- 跨資料庫交易或分散式交易不支援資料庫鏡像。
-

## 設定夥伴伺服器的建議

1. 夥伴伺服器的等級最好要相近  
如果您打算使用具有自動容錯移轉的高安全性模式，則每個容錯移轉夥伴的正常負載應該少於 50% 以下的 CPU 使用量。  因為如果負載太大的話，夥伴間容易發生連繫失敗而導至不必要的容錯移轉。
2. 容錯夥伴的資料庫路徑最好完全相同  
如果可行的話，鏡像資料庫的路徑 (包括磁碟機代號) 應該要和主體資料庫的路徑完全相同。  如果檔案配置不同，就必須在 [RESTORE](http://technet.microsoft.com/zh-tw/library/ms186858.aspx) 陳述式中使用 MOVE 選項。  
注意：在建立鏡像資料庫時，如果您移動資料庫檔案，則稍後必須暫停鏡像，否則可能無法將檔案加入資料庫。
3. 使用相同的主要字碼頁和定序  
鏡像工作階段中的所有伺服器執行個體都應該使用相同的主要字碼頁和定序。如果有差異，可能會在鏡像設定期間發生問題。
4. 為鏡像使用專用的網路介面卡  
為達最佳效能，請為鏡像使用專用的網路介面卡 (NIC)。

# 如何設定資料庫鏡像

要設定資料庫鏡像，必須先將資料庫由 principal 複製備份到 mirroring ，完成還境的建置。  當鏡像工作階段執行時，二台主機要彼此授權，所以必須要有認證機制。  若二者在同一個domain下，可以直接使用Windows認證即可。  若二者不在信認網域內，則必須透過伺服器憑證來協助認證。  

## 建立鏡像資料庫

要建立鏡像資料庫，你必須先在主伺服器製作備份，再由鏡像伺服器進行還原。其中有幾點要注意的：

#### 1. Full Recovery Model

首先主資料庫的復原模式僅支援**完整模式**，所以復原模式必須設定成完整模式。
```sql
ALTER DATABASE Exemplar SET RECOVERY FULL;
```

#### 2. Recover by Using NORECOVERY

使用 NORECOVERY 選項，在鏡像伺服器中進行還原
```sql
--from principal server
BACKUP  DATABASE [MirrorDB] TO   DISK = N'C:\backup\MirrorDB.bak' WITH FORMAT;
GO

--to mirrored server
RESTORE DATABASE [MirrorDB] FROM DISK = N'C:\backup\MirrorDB.bak' WITH NORECOVERY;
GO
```

PS. 如果有用到交易備份，就一併還原所有交易備份。

#### 3. Endpoint Firewall Rules

資料庫鏡像工作階段中的每個伺服器執行個體都需要一個[資料庫鏡像端點](http://msdn.microsoft.com/zh-tw/library/ms179511.aspx)。如果端點不存在，您就必須自行建立。   [資料庫鏡像端點](http://msdn.microsoft.com/zh-tw/library/ms179511.aspx)是一種特殊目的之端點，專門用來接聽來自於其他伺服器的連接。  資料庫鏡像端點使用 TCP 協定來傳送和接收訊息，且每個資料庫鏡像端點必須具有唯一的 TCP 通訊埠編號。

## 使用 Windows 認證

準備好鏡像資料庫之後，就可以建立資料庫**鏡像工作階段**。  主體、鏡像及見證伺服器執行個體必須是個別的伺服器執行個體，且應位於個別的主機系統上。   

如果 principal, mirror, witness 都在同一個 ad domain 或者 trusted domains 之中，使用 Windows 認證是最簡單，直接在 SSMS 中操作即可。  若不在同一個 domain ，就必須在各個 SQL 執行個體中建立相同的 login .  

SSMS 提供方便的介面設定鏡像，如果要使用 TSQL 可參考下面範例：   

### 建立鏡像資料庫

＠主體伺服器
```sql
--備份資料庫
use [TestDB1]
ALTER DATABASE [TestDB1] SET Recovery Full

Backup Database [TestDB1]      
To DISK = 'D:\Database\bak\TestDB1.bak' 
WITH Init
```

＠鏡像伺服器
```sql
--還原資料庫
ALTER DATABASE [TestDB1] SET SINGLE_USER WITH ROLLBACK IMMEDIATE	--切換成單人獨占模式 

RESTORE DATABASE [TestDB1]											--還原完整備份 (加入NORECOVERY) 
FROM DISK = N'D:\Database\bak\TestDB1.bak' 
WITH NORECOVERY, REPLACE

ALTER DATABASE [TestDB1] SET MULTI_USER WITH NO_WAIT				--回復成多人模式
```

還原完成後，鏡像伺服器的資料庫狀態會處於 [正在還原...]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9AGV0stdgZ3_8XJzSL9JNhnZNBBQOaFqMsuDKBJWogt_as-sMfojXa_h7bN7jorCwY6e-3yLEkPAkAuoGbeAhcBA8Y6U6Y_lr6OmmPeGsGXkkVEoNpLmzMl6pn9NdyO0gH8MocqV9Bws/s0/sql-mirror-restored.png)

### 建立通訊端點

二台主機都要建立
```sql
--建立端點
CREATE ENDPOINT [Mirroring] 
AS TCP (LISTENER_PORT = 5022)
FOR DATA_MIRRORING (ROLE = PARTNER, ENCRYPTION=DISABLED)
GO
--啟動端點
ALTER ENDPOINT [Mirroring]
STATE = STARTED 
AS TCP (LISTENER_PORT = 5022)
FOR database_mirroring (ROLE = PARTNER);
GO
```

若要查看端點狀態，可用以下語法
```sql
SELECT name, type_desc, port, state FROM sys.tcp_endpoints;
SELECT name,role,state_desc,certificate_id FROM sys.database_mirroring_endpoints;
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirFbL1Qxa2hnveSHIiEUD8PPhIkvdaOFNjL4bog7wT_BDPA8E8_Y-doMwftR7TFfOly7stBTPE7FiIN9k1VqkqiuHmyMcXk3cZWEjPzvbiSy35kBxikpQH2UCEjy_tOjxmhRO9F-64sfM/s0/sql-mirror-endpoint.png)

### 設定鏡像夥伴

＠主體伺服器
```sql
ALTER DATABASE [TestDB1] SET PARTNER ='TCP://SRV1:5022'
```

＠鏡像伺服器
```sql
ALTER DATABASE [TestDB1] SET PARTNER ='TCP://SRV2:5022'
```

設定完成，二台伺服器就會開始同步作業。

### 移除資料庫鏡像

若要停止資料庫鏡像，可以使用以下語法：
```sql
ALTER DATABASE [TestDB1] SET PARTNER OFF
```

若要刪除端點，可以使用以下語法：
```sql
DROP ENDPOINT [Mirroring]
```

停止資料庫鏡像後，若要使用鏡像資料庫，可使用以下語法將其還原成一般狀態：
```sql
RESTORE DATABASE [TestDB1] WITH RECOVERY;
```

## 使用憑證認證

若要執行**鏡像工作**的執行個體不在相同的 domain 之中，那麼就必須使用「伺服器憑證」來進行認證。  這個方法沒有 SSMS 的 UI 操作，只能透過 TSQL 來設定。  你必須先在主伺服器建立憑證，然後匯出，再匯入到鏡像伺服器。  

為了方便說明，假設 SQL-A, SQL-B 分別是 principal 和 mirror 身份，而且鏡像資料庫也已經建立完成，資料庫名稱為 TestDB。  下面示範如何使用憑證認證來進行鏡像工作階段：  

### 1. 建立主要金鑰

要使用憑證認證，首先你必須先在 SQL-A, SQL-B 都建立資料庫主要金鑰。  在建立主要金鑰前，你可以使用 [sys.symmetric_keys](http://msdn.microsoft.com/zh-tw/library/ms189446.aspx) 檢視表查看資料庫中是否已經有主要金鑰。  
```sql
--@ SQL-A
USE master;
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Pa$$w0rd';
GO

--@ SQL-B
USE master;
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Pa$$w0rd';
GO
```

### 2. 建立憑證

當每一個執行個體的「主要金鑰」建立完成之後，你就可以分別在各個執行個體上建立「[憑證](http://technet.microsoft.com/zh-tw/library/ms187798.aspx)」。

憑證是遵照 X.509 標準及支援 X.509 V1 欄位的資料庫層級安全性實體。

SQL Server 所產生的私密金鑰長度為 1024 個位元。  從外部來源匯入的私密金鑰，其最小長度為 384 個位元，其最大長度為 4,096 個位元。
```sql
--@ SQL-A
USE master;
CREATE CERTIFICATE SQL_A_cert WITH SUBJECT = 'SQL_A certificate';
GO

--@ SQL-B
USE master;
CREATE CERTIFICATE SQL_B_cert WITH SUBJECT = 'SQL_B certificate';
GO
```

### 3. 建立端點

After you have created a certificate for each instance on each instance,   you create endpoints on each instance that use the certificate you just created for authentication.  You can choose any available TCP port for mirroring.  

下面範例，使用 7024 port 建立一個名為 Endpoint\_Mirroring\_A 的端點，並使用 SQL\_A\_cert 憑證來驗證。  
```sql
--@ SQL-A
CREATE ENDPOINT Endpoint_Mirroring_A
STATE = STARTED AS TCP 
( 
LISTENER_PORT = 7024, 
LISTENER_IP = ALL
)
FOR DATABASE_MIRRORING (
AUTHENTICATION = CERTIFICATE SQL_A_cert, 
ENCRYPTION = REQUIRED ALGORITHM AES, 
ROLE = ALL
);
GO

--@ SQL-B
CREATE ENDPOINT Endpoint_Mirroring_B
STATE = STARTED AS TCP 
( 
LISTENER_PORT = 7024, 
LISTENER_IP = ALL
)
FOR DATABASE_MIRRORING (
AUTHENTICATION = CERTIFICATE SQL_B_cert, 
ENCRYPTION = REQUIRED ALGORITHM AES, 
ROLE = ALL
);
GO
```

### 4. 備份憑證

在 primary 和 mirror 執行個體的憑證都建立之後，還必須備份憑證，這樣才可以匯入到 partner 身上。  
```sql
--@ SQL-A
BACKUP CERTIFICATE SQL_A_cert TO FILE = 'C:\BACKUP\SQL_A_cert.cer';
GO

--@ SQL-B
BACKUP CERTIFICATE SQL_B_cert TO FILE = 'C:\BACKUP\SQL_B_cert.cer';
GO
```

### 5. 使用憑證建立Login

你必須在 SQL-A 中，使用 SQL\_B\_cert 憑證建立 Login ；也必須在 SQL-B 中，使用 SQL\_A\_cert 憑證建立 Login   
```sql
--@ SQL-A
USE master;

CREATE LOGIN SQL_B_login WITH PASSWORD = 'Pa$$w0rd';
GO
CREATE USER SQL_B_user FOR LOGIN SQL_B_login;
GO

CREATE CERTIFICATE SQL_B_cert AUTHORIZATION SQL_B_user
FROM FILE = 'C:\backup\SQL_B_cert.cer'
GO
```

### 6. 授與端點權限

建立好 Login, user, certificate 之後，最後還必須開放端點的權限給使用者。（[MSDN：GRANT 端點權限 (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms187811.aspx)）

您可以在 [sys.endpoints](http://technet.microsoft.com/zh-tw/library/ms189746.aspx) 目錄檢視中，看到有關端點的資訊。  您可以在 [sys.server_permissions](http://msdn.microsoft.com/zh-tw/library/ms186260.aspx) 目錄檢視中，看到有關伺服器權限的資訊，且可以在 [sys.server_principals](http://msdn.microsoft.com/zh-tw/library/ms188786.aspx) 目錄檢視中，看到有關伺服器主體的資訊。
```sql
--@ SQL-A
GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring_A TO [SQL_B_login];
GO

--@ SQL-B
GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring_B TO [SQL_A_login];
GO
```

### 7. 設定 mirror 伺服器

最後就是要在資料庫中，指定鏡像伺服器的對象。  
```sql
--@ SQL-A
ALTER DATABASE [TestDB]
SET PARTNER = 'TCP://192.168.0.2:7024';
GO

--@ SQL-B
ALTER DATABASE [TestDB]
SET PARTNER = 'TCP://192.168.0.1:7024';
GO
```

完成這個步驟，資料庫就會使用高安全模式（high-safety mode)開始鏡像。

# 監視鏡像資料庫

# 升級鏡像資料庫

## 參考資料  

- [資料庫鏡像 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms189852.aspx)
- [資料庫鏡像的必要條件、限制和建議事項](http://msdn.microsoft.com/zh-tw/library/ms366349.aspx)
- [設定資料庫鏡像 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms190941.aspx)
- [範例：使用憑證設定資料庫鏡像 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms191140.aspx)
- [資料庫鏡像工作階段期間的角色切換 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms189850.aspx)
- [資料庫鏡像監視器概觀](http://msdn.microsoft.com/zh-tw/library/ms365809.aspx)
- [在升級伺服器執行個體時將鏡像資料庫的停機時間減至最少](http://msdn.microsoft.com/zh-tw/library/bb677181.aspx)
- [資料庫鏡像見證](http://msdn.microsoft.com/zh-tw/library/ms175191.aspx)
- [加入或取代資料庫鏡像見證 (SQL Server Management Studio)](http://msdn.microsoft.com/zh-tw/library/ms365603.aspx)