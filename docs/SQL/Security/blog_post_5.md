---
title: 稽核
layout: default
parent: Security
nav_order: 5
description: "稽核"
date: 2013-09-23
tags: [SQL,Security]
postid: "6409862555429673431"
---
稽核是來追蹤 SQL Server 上的活動(activity)，例如查看誰更新了資料，誰更改了權限等等。  

稽核系統是在SQL Server 2008版本開始導入一套全新的系統：SQL Server Audit，它讓管理人員可以精確地紀錄所需要之稽核資訊，也可以利用「[擴充事件(Extended Event)](http://msdn.microsoft.com/zh-tw/library/bb630282.aspx)」來監視系統。  

# 認識 SQL Server Audit

#### SQL Server Audit 元件

- SQL Server Audit 使用「擴充事件」(Extended Event) 來幫助建立稽核。（「擴充事件」是一種使用極少量效能資源的一種輕量型效能監視系統）
- SQL Server Audit 可以針對**「伺服器層級稽核(Server Level Audit)」**或**「資料庫層級稽核(Database Level Audit)」**進行稽核。
- SQL Server Audit 可針對伺服器或資料庫的特定動作(actions)或動作群組(actions group)，將數個元素組合成單一封裝。
- SQL Server Audit 物件會收集單一執行個體中的動作或動作群組。您可以針對每個 SQL Server 執行個體設有多個稽核。
- SQL Server Audit 可以將追蹤結果儲存在目標（Target），這個目標可以是**稽核檔案**或**事件記錄檔**。
- 所有SQL Server版本都支援伺服器層級稽核，但是資料庫層級稽核則限於Enterprise、Developer以及Evaluation版本。

# 使用 SQL Server Audit

## 1. 設定「儲存目標」（Target）

要使用稽核，必須建立目標，也就是指定稽核結果輸出的位置。  共有三個選項可使用：Application log、Security log、File。  若使用 Security log 或 File ，要考量存取權限的問題。  

### 使用「Windows Security Event Log」為目標

若要使用 Windows Security Event Log 事件記錄檔來記錄稽核結果，則必須做以下設定：

1. 授予 SQL Server 服務執行帳戶稽核權限：  
將 SQL Server 的服務執行帳戶加入到「產生安全性稽核（Generate Security Audits）」原則。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCMnI0RKbcjRUUJz3h4-pMtpSgSAsap2nnFc5f6bcopon7QXHNjXx7aRZ3QljMOIsyz6JF9rJO9psBE_SpuIw4zzREjKmuHnwDZNAx5OT-n0YUgAbj20fywRzmkEICJC_5sQ8tbvaL_ok/s0/sql-generate-security-audit.png)
2. 設定稽核存取原則：  
將「稽核物件存取（Audit Object Access）」原則設定成不管成功和失敗都稽核。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMx_XqzxWqleqXiowDoF09TVbDTiDmJU0xkv1rXIftK3_fr8hNi4jG6AOPS2KclsJXzeHoY2onO6blsjAAAegaCoJgBITbINRlif68pXUgxi9EnAksM08WIFprC-BAmUYFOhJMRsTRZWY/s0/sql-audit-object-access.png)

### 使用「檔案」為目標

若要使用檔案來記錄稽核結果，則必須做以下設定：

- 授予 SQL Server 服務執行帳戶對這個記錄檔案擁有讀取和寫入的權限。
- 任何屬於「稽核管理員（Audit Administrators）」角色的成員，必須對這個記錄檔擁有讀取和寫入的權限。
- 任何屬於「稽核讀取員（Audit Reader）」角色的成員，必須對這個記錄檔擁有讀取的權限。

## 2. 建立「稽核物件」（CREATE a SERVER AUDIT ）

不管是建立伺服器層級或資料庫層級的稽核，都必須先建立一個「**伺服器稽核物件**」（SERVER AUDIT ）。  這個伺服器稽核物件是用來收集要監視對象的操作行為。  

### 使用 SSMS 建立「伺服器稽核物件」

#### 新增稽核

參考下圖，新增「伺服器稽核物件」：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1zS_Sr7seYWECIdjox_5CwSoNlp3RqJ9sOPbyQVnDb6RUSe4qMlGh2T7PJ-BpX3W-k09i8MkJRQ0g8WKQ5_PUZ06Sd6l0eCeDdmBlFq5hSBiynF8UB0Ba7aTodhAJPvqrVrkEVaWBKiA/s0/sql-create-audit-1.png)

#### 設定稽核物件內容

「伺服器稽核物件」包含下列項目：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgfvnnq2TGuoUJUP1ff-e_TKFNto_E2JgBn8TZ79K5yZiiCBX_geWqdB_D2MdMF7tWHKVtpDTTAe4Rs0ClZGlJJkbCIpCvmJ-ZvgOraW4Ky0pnCZdmAJ-wjBRFohjF1buUCOdPdpsOOXx4/s0/sql-create-audit-2.png)

1. 稽核名稱：自行輸入
2. 佇列延遲：判斷在強制處理稽核動作之前經過的時間長度 (以毫秒為單位)。 值為 0 表示同步傳遞。 可設定的最小查詢延遲值為 1000 (1 秒)，這是預設值。
3. 稽核失敗時關閉伺服器：當稽核失敗時是否要關閉伺服器。
4. 稽核目的地：可設定：Application log、Security log、File。

#### 設定稽核目的地：

在建立伺服器稽核物件時，必須建立目標（目的地），也就是指定結果輸出的位置。

你可以選擇以下三種目的地：File、Security Log、Application Log

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjC2QFASA-mYjV372FBrxqWvoYogBMfDQXOEd2SlObl6twaMMaXJlI4PjnDnyD_PxzIt3jfrLClW_a4xIGuw79frnU9pAMVKM2fOb4Op-DMCGNA9y_fYMHl-qjEEcrmBAJ20JRtTrg3Sn4/s0/sql-audit-distination.png)

### 使用 TSQL 建立「伺服器稽核物件」

你也可以使用 TSQL： [CREATE SERVER AUDIT](http://technet.microsoft.com/zh-tw/library/cc280448.aspx) 建立伺服器稽核物件。
```sql
CREATE SERVER AUDIT [myAUDIT]
TO APPLICATION_LOG
WITH
( 
QUEUE_DELAY = 1000, ON_FAILURE = SHUTDOWN
)
```
```sql
CREATE SERVER AUDIT [myAUDIT]
TO FILE ( FILEPATH ='\\SQLPROD_1\Audit\' );
```

#### 啟用稽核

你可以使用 ALTER SERVER AUDIT 啟用稽核
```sql
ALTER SERVER AUDIT [myAUDIT] 
WITH (STATE = ON);
```

## 3. 建立「稽核規格」

建立好稽核物件之後，就可以來設定「稽核規格」。這個步驟主要就是要設定稽核的項目。  

「稽核規格」可分成伺服器層級和資料庫層級：  

### 建立「伺服器稽核規格」（Server Audit Specification）

伺服器稽核規格是針對整個伺服器的行為進行稽核，以下是建立「伺服器稽核規格」步驟：

1. Verify that a server audit is present.
2. Create a server audit specification that maps to the audit.
3. Enable the audit specification.

#### 使用 SSMS 建立伺服器稽核規格

[執行個體]-&gt;[安全性]-&gt;[伺服器稽核規格]：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTUh3gJixE0DE_pg-E3DSB8MMUKVdF0Uj2CsAqHQBn3IJw3k8DywVOeZTkALZPxXyFZvChQ0XFlCJqJjKwLxJgJtYOC8BL44WwFvakiboIZDTVGg1U52hu798KBeD7sap1U9n3uwaUfq0/s0/sql-create-server-audit-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBCQ-PVxxSUYvv0DXW4lI3YawQpLXeTCYxUt05kmtGoU2P0SKB4OXE5AGV1BTZAx8iuOEQAcumf65rApGhKlsG1XCniuPx09dadyQHhrDUPXPuCJ_F0cG4zaAueMq6gKt2E2FS7fSantc/s0/sql-create-server-audit-2.png)

#### 使用 TSQL 建立伺服器稽核規格

同樣的，也可以使用 TSQL 指令建立伺服器稽核規格： [CREATE SERVER AUDIT SPECIFICATION](http://msdn.microsoft.com/zh-tw/library/cc280767.aspx) 
```sql
--建立 SERVER AUDIT SPECIFICATION
CREATE SERVER AUDIT SPECIFICATION [myServerAuditSpecification]
FOR SERVER AUDIT [myAUDIT]
ADD (DATABASE_ROLE_MEMBER_CHANGE_GROUP),
ADD (SCHEMA_OBJECT_CHANGE_GROUP)

--修改 SERVER AUDIT SPECIFICATION
ALTER SERVER AUDIT SPECIFICATION [myServerAuditSpecification]
ADD (DATABASE_LOGOUT_GROUP)
```

#### 啟用伺服器稽核規格

你可以在右鍵選單中啟用伺服器稽核規格。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9iZbj20ADfW8L9ituNsbMJFp052jUut0FZQ_w2Lz2myr9WARGXbqq1XDTml2ZdglCwT8lUss-DikdX2vTDMsNN-NwCSFiJF4X8t5urCbOfY1pS30GI7rKcYVaC562CWKTVMdEwUSps1M/s0/sql-enable-server-audit-sepcification.png)

也可以使用 TSQL 啟用伺服器稽核規格。

也可以使用 [ALTER SERVER AUDIT SPECIFICATION](http://msdn.microsoft.com/zh-tw/library/cc280767.aspx) 啟用「伺服器稽核規格」。
```sql
ALTER SERVER AUDIT SPECIFICATION [myServerAuditSpecification]
WITH (STATE = ON);
```

### 建立「資料庫稽核規格」（Database Audit Specification）

資料庫稽核規格是針對特定資料庫的行為進行稽核，以下是建立「資料庫稽核規格」步驟：

#### 1) 使用 SSMS 建立資料庫稽核物件

[執行個體]-&gt;[資料庫]-&gt;[安全性]-&gt;[資料庫稽核規格]：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzXBPbVKIFnYhtjS0XvKKc-ZUSl_dtYxnQjikb9FAIE2O5uh8KWHVVJp2wroJulSNk6iqa6FV1Z1nahrbx3tuRwAVU7ABcsHBs3QpZNQNBWeVV0ROG70pVBlCPLy_4Y_4OfDYa4uCF9fc/s0/sql-create-database-audit-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPJa700cjqIupf0TIV5a5E9NIL52K_57s7P7_Qey0R_NcNkJx4ODAMpisHAVqH2EKTalUi18Oy-YvyI6BaE9EXVEIRiCPLPuHdI8TKQ5pdfEhPX7uK_rAbKdwtnCHobgs1FFDPgVT6dbE/s850/sql-create-database-audit-2.png)

#### 2) 使用 TSQL 建立資料庫稽核物件

同樣的，也可以使用 TSQL 指令建立資料庫稽核規格： [CREATE DATABASE AUDIT SPECIFICATION](http://msdn.microsoft.com/zh-tw/library/cc280404.aspx) 
```sql
CREATE DATABASE AUDIT SPECIFICATION [myDatabaseAuditSpecification]
FOR SERVER AUDIT [myAUDIT]
ADD (UPDATE ON OBJECT::[dbo].[Products] BY [dbo])
```

#### 3) 啟用資料庫稽核規格

你可以在右鍵選單中啟用「資料庫稽核規格」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgF93s775-miWTs4tHsvK3Rk8JFC6Kg2ogeBTpJpEWKe6wWTIplIRcXFV5BrXV3jpFp617obmHB2JKGSsTUZWbkJWvfTW9aKobb4qj2sKaeNbkG_2BE4oFx-ewpvC7Qs-JKMBTdQ-t4aUA/s0/sql-enable-database-audit-sepcification.png)

也可以使用 [ALTER DATABASE AUDIT SPECIFICATION](http://msdn.microsoft.com/zh-tw/library/cc280404.aspx) 啟用「資料庫稽核規格」。

或者在  加入 WITH (STATE = ON) 選項。
```sql
ALTER DATABASE AUDIT SPECIFICATION [myDatabaseAuditSpecification]
WITH (STATE=ON)
```

#### 4) 檢視稽核內容

當符合稽核的條件發生時，該事件就會被記錄在指定的目標中，你可以由以下位置，開啟檢視：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoTSCLsy_VWjXVNT1WLrukNrT_xDBYD_YPccCtYO608GBPw1LmNwjDh9_XLpxCGJ2ROT32yFDmdjZdkGDQYFzaepqQChlYRw79ArCh_soN2s_Dq-gGMmsqOeLtWhyphenhyphen_JrogrTozZvqH4ZI/s0/sql-create-database-audit-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMh3csj2o67OV-irC2qovNbRnhH9_3j9700BmUtJSSlmliPdhN_24twaDobRDfThXVE9aE5CVeh0jsXIkiqMAUeyv5huAAwoW59YdxM1KV3PgboVtFvGrldn-CSoyA8B6XspKcwqIkA30/s850/sql-create-database-audit-4.png)
**附加(attach)資料庫時注意事項：**  
當附加資料庫到新的執行個體時，稽核規格將變成孤兒狀態(orphaned state)，不再記錄任何資訊。  主要原因在於它不認得新執行個體的稽核物件，所以只要重新指定即可。

## 4. 設定「稽核動作類型」

當建立稽核規格時，必須指定「稽核動作類型（Audit Action Type）」，也就是設定要稽核的行為。  每一個稽核可以由數個動作項目所組成，  這些項目可能包含數個操作，稱為**動作群組** (如 Server\_Object\_Change\_Group 動作，即包含了 CREATE、ALTER 和 DROP 等動作) ；  或者是僅含個別的**動作** (如 SELECT 動作)。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBCQ-PVxxSUYvv0DXW4lI3YawQpLXeTCYxUt05kmtGoU2P0SKB4OXE5AGV1BTZAx8iuOEQAcumf65rApGhKlsG1XCniuPx09dadyQHhrDUPXPuCJ_F0cG4zaAueMq6gKt2E2FS7fSantc/s0/sql-create-server-audit-2.png)

底下隨意列出幾個稽核動作：

#### 伺服器層級的稽核動作：

- APPLICATION\_ROLE\_CHANGE\_PASSWORD\_GROUP ：每當變更應用程式角色的密碼時，就會引發這個事件。
- BACKUP\_RESTORE\_GROUP ：每當發出備份或還原命令時，就會引發這個事件。
- FAILED\_LOGIN\_GROUP：表示主體嘗試登入 SQL Server 但失敗。
- DATABASE\_OBJECT\_CHANGE\_GROUP ：資料庫物件發生 CREATE、ALTER 或 DROP 陳述式時，就會引發這個事件。
- DATABASE\_ROLE\_MEMBER\_CHANGE\_GROUP ：每當從資料庫角色中加入或移除登入時，就會引發這個事件。
- SERVER\_ROLE\_MEMBER\_CHANGE\_GROUP ：每當從固定伺服器角色中加入或移除登入時，就會引發這個事件。
- SUCCESSFUL\_LOGIN\_GROUP ：表示主體已成功登入 SQL Server。
- LOGIN\_CHANGE\_PASSWORD\_GROUP ：每當透過 ALTER LOGIN 陳述式或 sp\_password 預存程序變更登入的密碼時，就會引發這個事件。

#### 資料庫層級的稽核動作：

- APPLICATION\_ROLE\_CHANGE\_PASSWORD\_GROUP：每當變更應用程式角色的密碼時，就會引發這個事件。
- BACKUP\_RESTORE\_GROUP：每當發出備份或還原命令時，就會引發這個事件。
- DATABASE\_ROLE\_MEMBER\_CHANGE\_GROUP ：每當從資料庫角色中加入或移除登入時，就會引發這個事件。
- FAILED\_DATABASE\_AUTHENTICATION\_GROUP：表示某個主體嘗試登入自主資料庫卻失敗。
- SCHEMA\_OBJECT\_CHANGE\_GROUP：在結構描述上執行 CREATE、ALTER 或 DROP 作業時，就會引發這個事件。
- SUCCESSFUL\_DATABASE\_AUTHENTICATION\_GROUP：表示主體已成功登入自主資料庫。  等於稽核成功資料庫驗證事件類別。

更多內容請參考 MSDN：[SQL Server Audit 動作群組和動作](http://msdn.microsoft.com/zh-tw/library/cc280663.aspx)

## 查看 SQL Server Audit

The following dynamic views, catalog views, and functions enable you to view audit configuration information:

- sys.dm\_audit\_actions ：
- [sys.server_audits](http://msdn.microsoft.com/zh-tw/library/cc280727.aspx) ： 傳回 SQL Server Audit 物件
- sys.dm\_server\_audit\_status ：
- [sys.server_audit_specifications](http://msdn.microsoft.com/zh-tw/library/cc280898.aspx) ：傳回伺服器稽核規格的資訊
- sys.server\_audit\_specifications\_details ：
- [sys.database_audit_specifications](http://msdn.microsoft.com/zh-tw/library/cc280726.aspx) ：傳回資料庫稽核規格的資訊
- sys.database\_audit\_specifications\_details ：
- [sys.fn_get_audit_file](http://msdn.microsoft.com/zh-tw/library/cc280765.aspx) ：傳回稽核檔案中的資訊

## 使用 TSQL 建立資料庫稽核

下面整理一下，如何以 TSQL 建立一個資料庫稽核
```sql
--建立稽核

USE [Master];

CREATE SERVER AUDIT [myAUDIT] TO APPLICATION_LOG        --建立稽核物件，並設定使用 application log 記錄稽核

ALTER SERVER AUDIT [myAUDIT]                            --啟用稽核
WITH (STATE = ON);

--建立(資料庫)稽核規格

USE [TestDB1];

CREATE DATABASE AUDIT SPECIFICATION [mySpecification]   --建立稽核規格
FOR SERVER AUDIT [myAUDIT]
ADD  (UPDATE ON dbo.Products BY dbo)
WITH (STATE = ON);                                  --啟用稽核規格
```

# 設定登入稽核

若要監視 SQL Server Database Engine 登入活動，可以在 Server 屬性視窗中的安全性頁簽中設定。如下圖。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQsUqek3Q1CpALo5YI0zRYN-nCHd6FWw5cycUOdgQ_CnDsj5leifdyjiaJnzRZ-ZJD1IUC2ZA4ww3Cn81__dWBbtNjHtOh-8zPoqltIHVmHTAcSGQE8Cg1sAeSmfVmpp_g8AS4zcuq39w/s0/sql-ausit-login.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMFJazI432ML-tNu1Zk7qcrtQEG_0JogtEvEhmbJtIWtpPS4ALOWd0ZmF4OW_39TY8ckU1mNLkzxpcC1RlwXKEBPgQhEgGDtAEZf8cxZF8h5M7VI5cbAjPV-OwJWbRCfoGbO_85R2SjwM/s850/sql-audit-login-2.png)

更多內容請參考 MSDN：[設定登入稽核 (SQL Server Management Studio)](http://msdn.microsoft.com/zh-tw/library/ms175850.aspx)

# C2 稽核模式

C2 稽核模式可以透過 SQL Server Management Studio 或 sp\_configure 中的 c2 audit mode 選項來設定。   

這個選項，伺服器會依據 c2 安全性標準 (c2 security standard)，同時將失敗和成功的存取陳述式都記錄下來。  這項資訊可協助您分析系統活動並追蹤可能違反安全性原則的違規。  

未來的 Microsoft SQL Server 版本將移除這項功能。

更多內容請參考 MSDN：[C2 稽核模式伺服器組態選項](http://msdn.microsoft.com/zh-tw/library/ms187634.aspx)
```sql
sp_configure "show advanced options", 1;
GO
RECONFIGURE;
GO
sp_configure "c2 audit mode", 1;
GO
RECONFIGURE;
GO
```

# 通用條件符合（Common Criteria Compliance）

通用條件符合（Common Criteria Compliance）是指伺服器組態中的選項設定。這個選項設定，會引響下列元素：  

- **剩餘資訊保護 (RIP)**：在記憶體重新配置到新的資源之前，RIP 需要使用已知的位元模式來覆寫記憶體配置。  符合 RIP 標準可促使安全性改善。不過，覆寫記憶體配置可能會降低效能。 啟用 common criteria compliance enabled 選項之後，就會進行覆寫。
- **檢視登入統計的功能**：啟用 common criteria compliance enabled 選項之後，就會啟用登入稽核。  每次使用者成功登入 SQL Server 時，就會提供一些資訊，包括上次登入成功的時間、上次登入不成功的時間，以及上次登入成功與目前登入時間之間的登入嘗試次數。
- **GRANT 不應覆寫資料表 DENY**：啟用 common criteria compliance enabled 選項之後，資料表層級 [DENY](http://msdn.microsoft.com/zh-tw/library/ms188338.aspx) 的優先順序會高於資料行層級 GRANT。如果沒有啟用此選項，資料行層級 [GRANT](http://msdn.microsoft.com/zh-tw/library/ms187965.aspx) 的優先順序會高於資料表層級 DENY。

更多內容請參考 MSDN：[通用條件符合已啟用伺服器組態選項](http://msdn.microsoft.com/zh-tw/library/bb326650.aspx)

# 原則式管理（Policy-Based Management）

原則式管理指的是：建立某個**原則**，並讓多台 SQL Server 執行個體都符合這個原則。例如以下狀況：  

- 若公司禁用 Database Mail 或 SQL Mail。因此就可以建立這個原則，管理員只要進行伺服器狀態比較，若與此原則不相符合，管理員就可以選擇設定模式，讓伺服器狀態符合。
- 若公司規定所有預存程序都要以字母 usp\_ 開頭命名，也可以建立原則來強制執行此規定。

更多內容請參考 MSDN：[使用原則式管理來管理伺服器](http://msdn.microsoft.com/zh-tw/library/bb510667.aspx)

## 練習：建立預存程序的命名規則，限制預存程序的名稱必須以\_usp開頭

這個練習，大至步驟如下：

- **Create a Contidion** named StoredProcNamingConvention	by using the 「Stored Procedure facet」.
- Setup a single expression with Field to @Name, Operator to Like, and Value to 'sp\_%'
- **Create a Policy** named StoredProcNamingPolicy.
- Set the Check condition to 「StoredProcNamingConvention 」 and Evaluation Mode to 「On Change:Prevetn.」
- **Enable StoredProcNamePolicy**.

下面範例示範，如何建立一個預存程序的命名規則：

### 1. 建立條件

原則管理的功能，位於[執行個體]下的[管理]節點內。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimXMDs5heUcJjZNfutWMT2_KoEgzewwzqxKGTkDejk5pr4ylH_8N-ZuLtY5094_9ZmPspped-Z5kG4EF3zkL4SMnLbaSeQUijTHqmAZeAJQdY9Y7Dc1vvsjm4pDrX1P-iG7HXLt-appS4/s0/sql-police-mgr-1.png)

首先我們要建立一個命名的條件，因為我們要設定的對象是預存程序，所以在 Facet 節點下，找到預存程序，然後在它的右鍵選單中點選［新增條件］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgU847W1OdmkHq6PLN-TfUnxJeGyErBw7WVDybk5C0NPh_5IPgGewpdgfnqiEP06lROrn8H2xEa5eNqfeKhEyja1-B68iWA6NsRmJTvAJDN8ThUz0ujp3S4hCbfiRcOjQyPmQ4iD0UjExQ/s0/sql-police-mgr-2.png)

設定條件內容：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgP2m-HRmhMB3D5S9aAtgF1bdYeJDz6xwo3VA8pZj5vrVBOSg4mZJq2PPmzxBKsuhuz3URDbke-yjLBdragjqGNnCDGdLrxamQVYljgK1pExr6eF0-IQwDmdCxoF8eYaXRrdt9Q38QYJE0/s0/sql-police-mgr-3.png)

### 2. 建立原則

接著我們要設定原則，也就是設定若條件發生時的處理原則：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZFtJ0DitzecTAcZ9GG7C6ST8hzJRD6GMpP8JnRJBI5FUJkiT0fbp_WVLP36B1ZWJ8-3uGNWaAX99soIMeV6KMnbmvYzQhbymawmgkdTOvVwT_f9dVNGfr_PDTmWKTJzya-KYT7026nhk/s0/sql-police-mgr-4.png)

設定原則內容

- 若要啟用原則，則勾選［已啟用］，有勾選，原則才會執行。
- 檢查條件：選擇上一步驟中建立的條件。
- 評估模式（evaluation modes）：選擇［變更時：避免］，這個選項才會禁止違反原則的行為。
- (On Demand)視需要：這模式為手動的意思，也就是使用者指定時才評估原則。
- (On Change: Prevent)變更時(避免)：若違反原則則禁止。
- (On Change: log only)變更時(僅限記錄)：若違反原則則記錄。
- (On Schedule)按排程時間：這種自動模式會使用 SQL Server Agent 作業來定期評估原則。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxnsp6x4APXcGNRjtQPwVLNUNAaGabOvYC_6EAzlJnmdEqP_Tqh2nppdfeIjakp1VI06tNc92RZADqUuMzhE1OWK34oZE0qTkjYdyWC2auVATZozCJCElCfv5yh2tiU6bWq3-v2y9CjIk/s0/sql-police-mgr-5.png)

你可以在描述中，替這個原則內容設定說明文字。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-sWJ7KS3rIEQGOsMkYBuLBqlU2gs0NF4qxnv1Ptdc9KvbCRGn5RTGXagL4FiyFa5vf3bYD4WV6NpZiJoMr7mGnXyUmpptv7V5MS_cDZEvB9iy7aO6sAOe5OTQnzdjh0QL6xCg4N7lciU/s0/sql-police-mgr-6.png)

### 3. 測試

底下我們執行建立 sp 語法，測試原則是否有依據以上設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJ5-HOkIVBjGjgoduVfmWBsowkF0DfEZJQLCZGn0V0cjO9hAN688M10ccp0gpJ2sx1qRK3dfxQCxSH-4f0u6axCBmlKz-cz7KzDGA86SIssuuot4kAHzCF2dW9ooicdlHfVDFFeRJ-lZo/s0/sql-police-mgr-7.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPXwZbElF_bMtoHawAUBgvg2DaQHCTcRrc435jOMPe9oRcyu7FOmjdzJwJSvB8gjaCaTRJfz00v6RB67LcaD4OPTRxQUjs88j076IMxF2ITEyUmnYbX718qC4jsxJ4YoKVBg7euTFE8RQ/s0/sql-police-mgr-8.png)
## 參考資料  

- [新手學SQL Server 2012稽核SQL Server Audit新增強的功能(1)](http://blogs.uuu.com.tw/Articles/post/2012/05/10/新手學SQL-Server-2012稽核SQL-Server-Audit新增強的功能%281%29.aspx)
- [SQL Server Audit (Database Engine)](http://msdn.microsoft.com/zh-tw/library/cc280386.aspx)
- [SQL Server Audit 動作群組和動作](http://msdn.microsoft.com/zh-tw/library/cc280663.aspx)
- [設定登入稽核 (SQL Server Management Studio)](http://msdn.microsoft.com/zh-tw/library/ms175850.aspx)
- [C2 稽核模式伺服器組態選項](http://msdn.microsoft.com/zh-tw/library/ms187634.aspx)
- [使用原則式管理來管理伺服器](http://msdn.microsoft.com/zh-tw/library/bb510667.aspx)