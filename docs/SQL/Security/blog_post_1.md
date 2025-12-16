---
title: Logins and Server Roles
layout: default
parent: Security
nav_order: 1
description: "Logins and Server Roles"
date: 2013-07-11
tags: [SQL,Security]
postid: "6464377928190516968"
---
# SQL Logins

SQL Server 2012 supports the following login types:

1. SQL Server–Authenticated Logins
2. Windows - Authenticated Logins
3. Certificate
4. Asymmetric key

## 建立 Logins

要建立 SQL Server 的 Logins ，你可以使用以下四種類別：

### 1. 使用 SQL Server–Authenticated

採用「SQL Server 認證」的登入是由 DB Engine 進行認證，登入密碼儲存在 master 資料庫中。  
```sql
CREATE LOGIN sql_user_a WITH PASSWORD = 'Pa$$w0rd';
```

### 2. 使用 Windows - Authenticated

採用「 Windows 帳號」的登入，你可以使用本機使用者或者網域中的使用者，同時也可以使用特定的群組來建立 SQL Server 登入。  
```sql
--使用特定使用者建立登入
CREATE LOGIN "SQL-A\Local_One" FROM WINDOWS;

--使用特定群組建立登入
CREATE LOGIN "SQL-A\Group_One" FROM WINDOWS;
```

### 3. 使用憑證認證（Certificate - Authentication）

你可以使用 [CREATE CERTIFICATE](http://msdn.microsoft.com/zh-tw/library/ms187798.aspx) 指令在 SQL 中建立憑證，再將該憑證授權給登入。  
```sql
--建立憑證
CREATE CERTIFICATE user2
ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'
WITH SUBJECT = 'user2 certificate in master database',
EXPIRY_DATE = '01/01/2014';

--建立登入
CREATE LOGIN user2 FROM CERTIFICATE user2;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7NFKXfk-jefySmuH-a40GPrXvEUTbqXKvVfq__ctNS9whWgYMea4JaJ-ynpM-03UiFij73gZ8y1WP0ArJSmdSWwQ6FiP6IwUdv_RSO6sBPIxwRnjj7AYfx2OeFchhnPis98_VWBeUASg/s0/sql-certificate-logins.png)

PS. 使用憑證認證常用於網路存取作業，如 Service Broker、Mirroring 。

### 4. 使用金鑰登入（Asymmetric Key - Authentication）

你可以使用 [CREATE ASYMMETRIC KEY](http://msdn.microsoft.com/zh-tw/library/ms174430.aspx) 指令在 SQL 中建立金鑰，再將該金鑰授權給登入。  
```sql
--建立金鑰
CREATE ASYMMETRIC KEY user3_key 
WITH ALGORITHM = RSA_2048
ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'; 

--建立登入
CREATE LOGIN user3 FROM ASYMMETRIC KEY user3_key;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEglktdAID76zTJQ_9UgZjpVHUFC1qfiwVlQdoWHIWDrCDCoZzYW8Q4uWN4TMeDEveW6iwU2Te_BcpvDDmj2e370fPSjn5quN6E8pCIUB_jRJs-OUEDtGyPtMmLydH0gop-0ZD7W1nl9A5o/s0/sql-asymmetric-key-logins.png)

## 驗證模式

在 SQL 安裝期間，您必須選取 Database Engine 的驗證模式。  有兩種可能的模式：「Windows 驗證模式」和「QL Server 及 Windows 驗證混合模式」。  

### Windows 驗證模式

這個模式 SQL Server 只允許合法的 Windows 網域使用者登入。

### SQL Server 及 Windows 驗證混合模式

這個模式 SQL Server 除了允許合法的 Windows 網域使用者外，也允許 SQL Server 帳戶來登入 SQL Server。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEin1GAqW1DiZ38JQSzF8h1HD7nOLNLaKZgJuHMho_h5xtBehsgiicsyTRx_I4mhL0IwYGFAGEbQUxrIN-Atba27-p_4VDcJ5XNsfEBe1YTGwsey4g4BIr5uBzMbTMsh1Hdk9hra92oksR8/s0/sql-authentication-modes.png)

## 驗證安全性

當使用 SQL Logins 時，你可以使用**憑證**或**非對稱金鑰**來增加驗證的安全性。

- Certificate Authentication
- Asymmetric Key Authentication

## 管理 Logins

[CREATE LOGIN](http://msdn.microsoft.com/zh-tw/library/ms189751.aspx) 是用來建立登入的指令，包含帳戶的使用期限或者密碼原則都可以方過它來設定。

### 建立 Windows 登入帳戶

透過 SQL 圖形介面建立

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwudaScsDwMInNM2CwkSoHmtWl1VQT7CQHYGYpyhvHkAMIkiGGElM760zKcKpbiEii0gnzMD5yxm7I-8UBYrS-W_PNzHaXxMefW5paKvh2kY87DLUjbEoxVZ9W5FjHcul5z01Zw115alA/s0/sql-login-windows.png)

### 建立 SQL 登入帳戶

透過 SQL 圖形介面建立

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNV68zEGWM3aO2cOZ0-IcDuy3DuIyEio3GjUeR6IN1OK13vRnQVCaX3nePu7FHRE0lwt25TEzw6aDtFDK3GZXshgyIYEFUXJxxbyc-zdPW8oCYJN8_XfZF6iJK5INbwbr0t2cC6JArkbI/s0/sql-login-sqls.png)

透過 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 建立

### 使用 DDL 來管理登入帳戶

- [CREATE LOGIN](http://msdn.microsoft.com/zh-tw/library/ms189751.aspx) ：建立 SQL Server、Windows Azure SQL 資料庫 及 SQL Server PDW 的 Database Engine 登入。
- [ALTER LOGIN](http://msdn.microsoft.com/zh-tw/library/ms189828.aspx) ：變更 SQL Server 登入帳戶的屬性。
- [DROP LOGIN](http://msdn.microsoft.com/zh-tw/library/ms188012.aspx) ：刪除 SQL Server 登入帳戶。
```sql
//設定本機使用者
CREATE LOGIN "SQL-A\Local_One" FROM WINDOWS;

//設定網域使用者
CREATE LOGIN "CONTOSO\Account_Two" FROM WINDOWS;

//設定網域群組
CREATE LOGIN "CONTOSO\Group_Two" FROM WINDOWS;
```
```sql
CREATE LOGIN sql_user_a WITH PASSWORD = 'Pa$$w0rd';
```
```sql
ALTER LOGIN vito DISABLE;
```
```sql
DROP LOGIN vito;
```

### 與登入相關的檢視

[sys.server_principals](http://msdn.microsoft.com/zh-tw/library/ms188786.aspx) 

查看登入的相關資訊，如：登入的建立時間，或目前狀態等屬性。  

[sys.sql_logins](http://msdn.microsoft.com/zh-tw/library/ms174355.aspx) 

查看更進階的登入相關資訊，如：密碼原則、密碼逾期等屬性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUy9UCMAaopXUc-sUw8fy7FJa8sHYpT1liYc5KsT5EC8p8y3N0_ubNMgeNIZEw7ByxJcZNI9KWnFyQWiXyvju60RoRSKa31hsydAwloh9JF4cYUHRx3snj-6aSl74WAdFWgKwEXLV0vXI/s850/sql-sql_logins.png)

### Denying Server Access

You can configure the Deny permission to block specific account from making connections to the Database Engine.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0i_ZTmdCKNrT0CVql8CmW6cUFwrat4d5v5FupFMO7DlDXIPZbatrhYeP2jVsuVJQFjF655PNesVaqY2PQm_Z8DM9536rhqaUQX4CSoIT9k-E7i0eET0QCJC6zQOnePnT-JKREyg6o3Gs/s0/sql-deny-login.png)
```sql
USE [master]
GO
DENY CONNECT SQL "contoso\domain_user_b";
GO
```

# Server Roles

前面提過 Permission 可以套用到個別的 Login ，如果要套用到一群人，則可以使用 [Server Roles](http://msdn.microsoft.com/zh-tw/library/ms188659.aspx)。  

[Server Roles](http://msdn.microsoft.com/zh-tw/library/ms188659.aspx) 是 Sever-level ，也就是權限範圍為整個伺服器。  另外 SQL Server 還有個 [Database Roles](http://msdn.microsoft.com/zh-tw/library/ms189121.aspx) ，該角色的權限範圍為整個資料庫。  

## Server Roles

SERVER ROLE 分成系統預設的角色，和使用者自訂的角色。  

以下９個是系統固定的server-level角色：  

- sysadmin
- serveradmin
- securityadmin
- processadmin
- setupadmin
- bulkadmin
- diskadmin
- dbcreator
- public

### 設定與修改 Server Role 的成員

透過 [ALTER SERVER ROLE](http://msdn.microsoft.com/en-us/library/ee677634.aspx) 指定，可以用來新增或刪除角色的成員。
```sql
ALTER SERVER ROLE serveradmin ADD MEMBER "vito";
ALTER SERVER ROLE serveradmin DROP MEMBER "vito";
```

### 查詢 Server Role 的資訊

You can use the following commands, views, and functions to learn more about the properties of specific server roles:

- [sp_helpsrvrole](http://msdn.microsoft.com/zh-tw/library/ms179864.aspx) ：Provides a list of fixed server roles
- [sp_helpsrvrolemember](http://msdn.microsoft.com/zh-tw/library/ms188772%28v=sql.105%29.aspx) ：Provides fixed server role membership
- sp\_srvrolepermission ：Provides fixed server role permissions
- IS\_SRVROLEMEMBER ：Enables you to check whether a SQL Server login is a member of a specific fixed or user-defined server role
- [sys.server_role_members](http://msdn.microsoft.com/zh-tw/library/ms190331.aspx) Provides information about role members, displayed as role and member id

## User-Defined Server Roles

**[User-Defined Server Roles](http://technet.microsoft.com/zh-tw/library/ee677610.aspx)** 是 SQL 2012 的新功能，  當固定的伺服器角色都不符合需求時，你就可以利用這個功能來建立 custom server roles ，建立步驟如下：  

- Creating the user-defined server role
- Granting server-level permissions to the role
- Adding SQL Server logins to the role

你可以在 SSMS 的 UI 中操作，也可以使用以下命令來操作：
```sql
CREATE SERVER ROLE Modify_Databases;

GRANT ALTER ANY DATABASE TO Modify_Databases;

ALTER SERVER ROLE Modify_Databases ADD MEMBER "vito"; 
```

# Credentials (認證)

[Credential](http://msdn.microsoft.com/zh-tw/library/ms161950.aspx) 是包含驗證資訊的記錄，該項資訊是連接到 SQL Server 外部資源時所需的資訊。  如果提供者沒有任何登入對應認證，系統就會使用對應至 SQL Server 服務帳戶的認證。  

一個 Credential 可以對應到多個 login 身份，但是一個 login 只可以對應到一個 Credential.  

大部分 Credential 都包含 Windows 使用者和密碼。 你何以在安全性節點上建立 Credential.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgS3HbYM6g8ftsn-ibXLUJX0d3Jwz5BAugvbFdgwEoIqRyJFYMUoXnUgoyDEuZg7bI-N38SDQ9qtQev32nv4wcIRCqQNke2o79TRo5Azbd8hDzdxiV6FgnV1t0NscMp3QSw1zseIo6hLBE/s0/sql-credential-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivwxbBmSEceujtexRpWh8ARTl599x4MI9vYqtYPe022GmWHd6MsOxFOQ1VxXA2n6_bfWpSfTpVxpNZ-cLgIlZdFBM307HnFSGpqZR61tS2hzP-Ysvj6loqeq0GZbX1KK9ArjZFl82lVyI/s0/sql-credential-2.png)

或者使用 [Create Credential](http://msdn.microsoft.com/zh-tw/library/ms189522.aspx) 建立認證，再使用 [ALTER LOGIN](http://msdn.microsoft.com/zh-tw/library/ms189828.aspx) 將認證對應到 SQL Server 登入。
--建立憑證  Create Credential credentialForVito  With identity='vito', secret='p@ssWord'    --將憑證配置給 User1 Login  ALTER LOGIN User1  ADD Credential credentialForVito;    --將憑證配置給 User2 Login  ALTER LOGIN User2  ADD Credential credentialForVito;
## 參考資料  

- [建立登入](http://msdn.microsoft.com/zh-tw/library/aa337562.aspx)
- [伺服器層級角色](http://msdn.microsoft.com/zh-tw/library/ms188659.aspx)
- [認證 (Database Engine)](http://msdn.microsoft.com/zh-tw/library/ms161950.aspx)
- [主體 (Principal)](http://msdn.microsoft.com/zh-tw/library/ms181127.aspx)
- [安全性實體 (Securables)](http://msdn.microsoft.com/zh-tw/library/ms190401.aspx)