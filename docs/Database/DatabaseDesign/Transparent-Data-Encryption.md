---
title: 透明資料加密
layout: default
parent: Database Manage
nav_order: 8
description: "透明資料加密"
date: 2013-10-24
tags: [SQL,Database Manage]
postid: "4041019119247606563"
---
透明資料加密（Transparent Data Encryption, TDE），  這個功能是 SQL2008 才開始有的，它透過存在資料庫 boot record 中的資料庫密碼金鑰(DEK)，對整個資料庫的資料和記錄檔進行加密。  這個功能可以預防當實體資料庫不填被竊取後，遭附加或還原而導至資料被瀏覽。  

同時要注意的是，若執行個體中的任何一個資料庫啟用 TDE ，則系統資料庫 tempdb 也會同時被加密。

# 使用透明資料加密（Transparent Data Encryption, TDE）

若要使用 TDE，必須遵循下列步驟：

1. 建立主要金鑰
2. 建立憑證（使用主要金鑰保護）
3. 建立資料庫加密金鑰（使用憑證保護）
4. 設定資料庫使用 TDE
```sql
--建立主要金鑰
CREATE MASTER KEY ...

--建立憑證
CREATE CERTIFICATE ...

--建立資料庫加密金鑰
CREATE DATABASE ENCRYPTION KEY ...

--設定資料庫使用加密
ALTER DATABASE ... SET ENCRYPTION OFF  
```

## 1. 建立主要金鑰

**資料庫主要金鑰**是一個對稱金鑰，主要用於保護下一個步驟要建立的憑證。  當建立資料庫主要金鑰時，系統會利用 AES_256 演算法和使用者提供的密碼來加密。  並將它儲存在資料庫和 master 中。  
```sql
USE master;
GO
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'P@ssw0rd';
GO
```

PS. 在建立資料庫主要金鑰前，你可以使用 [sys.symmetric_keys](http://msdn.microsoft.com/zh-tw/library/ms189446.aspx) 檢視表查看資料庫中是否已經有建立金鑰。

## 2. 建立憑證

A certificate is a database-level securable that follows the X.509 standard and supports X.509 V1 fields.  

下面這個範例會建立憑證，同時使用資料庫主要金鑰來加密此私密金鑰。
```sql
CREATE CERTIFICATE MyCertificate 
WITH SUBJECT = 'My TDE Certificate';
```

## 3. 建立資料庫加密金鑰

在使用「資料庫透明加密」功能之前，必須要擁有「資料庫加密金鑰（DEK）」，而這個步驟就是建立資料庫加密金鑰的主要步驟。  

下列範例利用 AES_256 演算法建立一個資料庫加密金鑰
```sql
USE TestDB1;
GO 

CREATE DATABASE ENCRYPTION KEY 
WITH ALGORITHM = AES_256
ENCRYPTION BY SERVER CERTIFICATE MyCertificate
GO 
```

## 4. 設定資料庫使用 TDE

最後透過 [ALTER DATABASE](http://technet.microsoft.com/zh-tw/library/ms174269.aspx) 的 ENCRYPTION 選項來設定啟用(ON)或停用(OFF)資料庫加密。  
```sql
ALTER DATABASE TestDB1
SET ENCRYPTION ON;
GO
```

若要檢視資料庫的加密狀態，可以使用 [sys.dm_database_encryption_keys](http://technet.microsoft.com/zh-tw/library/bb677274.aspx) 動態管理。
```sql
select D.name, DEK.* 
from sys.dm_database_encryption_keys DEK
inner join sys.sysdatabases D on DEK.database_id=D.dbid
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNz6St7GLmTkSSvV_YrMqv4u0V_b7-OhoXLW50jx2OFCILyYvU-OyBwdR_SquEuu13yxhTEdWem1Gznxjlv6H0f1RAZOaCgExywdlzCBZs767JRR21Di2mkoyl-15qWJRLIjRcck6Bgk8/s0/sql-dm_database_encryption_keys.png)

### encryption_state 

- 0 = 沒有資料庫加密金鑰存在
- 1 = 未加密
- 2 = 加密進行中
- 3 = 已加密
- 4 = 金鑰變更進行中
- 5 = 解密進行中
- 6 = 保護變更進行中

## 備份主要金鑰與憑證

當啟用 TDE 時，您應該立即備份憑證以及與此憑證有關的私密金鑰。  如果此憑證無法使用或是您必須在另一部伺服器上還原或附加資料庫，您必須同時擁有此憑證和私密金鑰的備份，否則將無法開啟資料庫。  

### 備份資料庫主要金鑰
```sql
--備份Master Key 
USE master;

OPEN MASTER KEY DECRYPTION BY PASSWORD = 'P@ssw0rd';
BACKUP MASTER KEY TO FILE = N'd:\DbMasterKey' 
ENCRYPTION BY PASSWORD = 'P@ssw0rd';
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjbZgqYeoLirOQsEECO5TqSftXze_Ru1xilFTtMtylxUiAStQqPpwo-sOKjqlvTX55Vr8tBQ3M7svULEr7xiqYyryHMX8GOkhAdWzi-geUoraJhQ87NISOlDKVipnrX9dQN5FJsDvO-BQ/s0/sql-back-master-key.png)

### 備份憑證和私密金鑰
```sql
USE master;

BACKUP CERTIFICATE MyCertificate 
TO FILE = N'D:\DbCertificate.cer'
WITH PRIVATE KEY 
(
	FILE = N'D:\DbPrivateKey.pvk',
	ENCRYPTION BY PASSWORD = 'P@ssw0rd'
);
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiU4r1LKJYL_YWBJptnpgx3XwcIPHOL7__NG7kFXHwKD_sZywT7J8dAqQp3rnbj_mJVt1ipOj6kb9LtlpQ2EFztpapEIyX5B79mOzzwwImG_xoMW2Sp85YAcDmSEnVu0SKcvgTVC4AKnEM/s0/sql-back-certificate.png)

# 還原 TDE 資料庫

如果，你在一部沒有憑證的SQL執行個體中，進行 TDE 資料庫的還原，這時候你會遇到以下錯誤。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgG3s_2JVn0LsEp1yfj_kjwQ0ftXOrmBWfB3q4SvftgDAIqNqKR03e4Jz1zdtC74YYsK2Y5sxjUeuiZlI8waR7al-9THwE85kV-RT5OMR9ic7u4bg5UyPFOG4aHOz-Kyo2jbJ0mIjoXQ-I/s0/sql-restore-tde-error.png)

這時你必須先匯入先前備份的主要金鑰和憑證才行，可參考以下步驟：  
```sql
--1) 載入主要金鑰
RESTORE MASTER KEY 
FROM FILE = 'D:\DbMasterKey' 
DECRYPTION BY PASSWORD = 'P@ssw0rd'
ENCRYPTION BY PASSWORD = 'P@ssw0rd'

--2) 載入憑證
--再載入憑證前,必須先開啟主要金鑰
use [master]
OPEN MASTER KEY DECRYPTION BY PASSWORD = 'P@ssw0rd'
ALTER MASTER KEY ADD ENCRYPTION BY SERVICE MASTER KEY

--從檔案建立憑證
CREATE CERTIFICATE MyCertificate
FROM FILE = 'D:\DbCertificate.cer'
WITH PRIVATE KEY (FILE = 'D:\DbPrivateKey.pvk', DECRYPTION BY PASSWORD = 'P@ssw0rd')
;
```

這時候，再執行 Restore 就可以直接復原資料庫。  
```sql
Use [master]
RESTORE DATABASE [TestDB2] 
FROM DISK = N'D:\Database\bak\TestDB1.bak' WITH FILE = 1  
,RECOVERY 
,REPLACE
,MOVE 'TestDB' TO 'D:\Database\testdb2008\TestDB2.mdf'              
,MOVE 'TestDB_Log' TO 'D:\Database\testdb2008\TestDB2_Log.ldf'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIQ2OfTxpukEiwIzmBmW1e-pp67eULCKZPz440Gi5ivDOsMv0GhD00CrBO1RH50WED9qMIPBfHJxGL4ryB-auiATA5Layp7PXVmEvEyoNePeCOrkL7dZY6qJGuOc3fDmrbivG9JZploFc/s0/sql-restore-success.png)

PS. 不同版本的資料庫，其匯出的憑證格式不盡相同，所以也無法拿到不同版本的資料庫進行匯入。

# 移除 TDE

要移除透明資料庫加密，大至和建立時的步驟相反。
```sql
--停用資料庫加密
ALTER DATABASE testdb1
SET ENCRYPTION OFF;

--移除資料庫加密金鑰
use [testdb1]
DROP DATABASE ENCRYPTION KEY 

--卸除憑證
use [master]
DROP CERTIFICATE MyCertificate

--卸除主要金鑰
DROP MASTER KEY
```
## 參考資料  

- [透明資料加密 (TDE)](http://technet.microsoft.com/zh-tw/library/bb934049.aspx)
- [瞭解透明資料加密 (TDE)#簡介](http://www.dotblogs.com.tw/ricochen/archive/2010/02/09/13553.aspx)
-