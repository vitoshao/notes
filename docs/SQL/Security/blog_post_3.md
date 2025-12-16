---
title: Users and DatabaseRoles
layout: default
parent: Security
nav_order: 3
description: "Users and DatabaseRoles"
date: 2013-07-11
tags: [SQL,Security]
postid: "5709532843390109156"
---
資料庫層級的安全性原則，包含：

- Database Users
- fixed database roles
- flexible database roles
- 

# Database Users

- Database users represent the identity of a SQL Server login when connected to a database.
- Each Database Users maps to a SQL Server login
- 一個 Users 雖然對應到單一個 Logins ，但是沒有限制二者要用相同的名稱。若一個 Login 允許連線到不同資料庫，你也可以分別在不同的資料庫設定不同的 User 名稱。
- You can assign database-level permissions directly to database users.
- It is bestpractice to assign database-level permissions to database-level roles and then add database users to those roles

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHU-ucQxBEMkiW7asRHoWmIW8TUlTbJolWBYKzeFAhhBA2gxJNNvRjVqvbneU85U3bKS0ZoJXwyIHNyRX9iMkGa_5hhK5yXAEiA2aDJMgSeamMzBMuMtfRmzPA-1B8avZNKlQ486jPElU/s0/sql-create-users.png)

- [CREATE USER](http://msdn.microsoft.com/zh-tw/library/ms173463.aspx) ：將使用者加入目前資料庫中。
- [DROP USER](http://msdn.microsoft.com/zh-tw/library/ms189438.aspx) ：將使用者自目前資料庫中刪除。
- [ALTER USER](http://msdn.microsoft.com/zh-tw/library/ms176060.aspx) ：重新命名資料庫使用者或變更其預設結構描述。
- [sp_change_users_login](http://msdn.microsoft.com/zh-tw/library/ms174378.aspx) ：現有的資料庫使用者對應至 SQL Server 登入。  未來的 Microsoft SQL Server 版本將移除這項功能。請避免在新的開發工作中使用這項功能。 請改用 [ALTER USER](http://msdn.microsoft.com/zh-tw/library/ms176060.aspx) 。
```sql
use [mydb]
GO

--新增一個資料庫使用者(user1)，對應到登入(login 1)
CREATE USER "user1" FOR LOGIN "login1";
GO

--修改資料庫使用者(user1)，對應到登入(login 1)
ALTER USER "user1" WITH LOGIN "login1";
GO
```

# Database Roles

**Database Role** 是屬於 **Database-level** 的物件，可用來方便權限的管理，只要設定好 Database Role 權限後再將使用者加入即可。  
SQL Server 中有兩種資料庫層級角色類型：一是由資料庫預先定義的「固定資料庫角色」(Fixed Database Role) 以及您可以建立的「彈性資料庫角色」(Flexible Database Role)。   

## Fixed Database-Level Roles

固定資料庫角色義於資料庫層級，並存在每個資料庫中。下表顯示固定資料庫層級角色和其功能。    

| 資料庫層級角色名稱 | 說明 |
| --- | --- |
| db\_owner | db\_owner 成員可以在資料庫上執行所有的組態和維護活動，也可以卸除資料庫。 |
| db\_securityadmin | db\_securityadmin 成員可以修改角色成員資格及管理權限。  將主體加入這個角色可能會產生不必要的權限擴大。 |
| db\_accessadmin | db\_accessadmin 成員可以針對 Windows 登入、Windows 群組及 SQL Server 登入加入或移除資料庫的存取權。 |
| db\_backupoperator | db\_backupoperator 成員可以備份資料庫。 |
| db\_ddladmin | db\_ddladmin 成員可在資料庫中執行任何「資料定義語言」(DDL) 的命令。 |
| db\_datawriter | db\_datawriter 成員可以加入、刪除或變更所有使用者資料表中的資料。 |
| db\_datareader | db\_datareader 成員可以從所有使用者資料表讀取所有資料。 |
| db\_denydatawriter | db\_denydatawriter 成員不能加入、修改或刪除資料庫中使用者資料表的任何資料。 |
| db\_denydatareader | db\_denydatareader 成員不能讀取資料庫中使用者資料表的任何資料。 |

### 將使用者加入固定資料庫角色

使用 [sp_addrolemember](http://msdn.microsoft.com/zh-tw/library/ms187750.aspx) 預存程式
```sql
USE [AdventureWorks2012];
GO
EXEC sp_addrolemember 'db_datawriter', "contoso\domain_user_b";
GO
```

## Flexible Database-Level Roles

當「固定資料庫角色」不能滿足需求時，也可以自行定義「彈性資料庫角色」。   你可以使用 [CREATE ROLE](http://msdn.microsoft.com/zh-tw/library/ms187936.aspx) 指令建立彈性資料庫角色。   
```sql
CREATE ROLE role_name [ AUTHORIZATION owner_name ]
```

**AUTHORIZATION** 子句用來指派這個角色的擁有者，若未指定任何使用者，該角色的擁有者為執行 [CREATE ROLE](http://msdn.microsoft.com/zh-tw/library/ms187936.aspx) 的使用者。  

建立角色之後，再使用 [GRANT](http://msdn.microsoft.com/zh-tw/library/ms187965.aspx) 指令授與各種權限，或者使用 [sp_addrolemember](http://msdn.microsoft.com/zh-tw/library/ms187750.aspx) 預存程序將使用者加入該角色中。  
```sql
--建立角色，並設定角色的owner
CREATE ROLE TableCreator AUTHORIZATION "vito";

--授與 CREATE TABLE 權限
GRANT CREATE TABLE TO TableCreator;

--加入使用者
EXEC sp_addrolemember 'TableCreator', "vito";
```

### 與使用者和角色相關的檢視(View)

以下幾個是與使用者或角色相關的系統檢視：

- [sys.database_principals](http://msdn.microsoft.com/zh-tw/library/ms187328.aspx) ：傳回安全性實體的相關資料。
- [sys.database_role_members](http://msdn.microsoft.com/zh-tw/library/ms189780.aspx) ：傳回資料庫的相關成員
- [sys.database_permissions](http://msdn.microsoft.com/zh-tw/library/ms188367.aspx) ：傳回資料庫的權限設定

#### 查詢 db role 的相關成員
```sql
SELECT 
PrincipalType = P1.type_desc,
DatabaseRole = P1.name,
PrincipalName = P2.name
FROM sys.database_role_members M
INNER JOIN sys.database_principals P1 ON M.member_principal_id = P1.principal_id
INNER JOIN sys.database_principals P2 ON M.role_principal_id = P2.principal_id 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2FlA7C0pm_ZjwbVIJat6GiSl0oSbGPJ3ZBguzXXuXmYXFYFyErqGHu1cLO_grw3CjJthbwjl9ors1WmBWougQHc17dXVJN_bwkwPeia_RgmeCF1UZtIhTRlZOpM56k1vg9JXeY4IypSw/s0/sql-db-role-members.png)

#### 查詢使用者對資料庫的相關權限
```sql
select Prin.principal_id, Prin.name, Prin.type_desc,Prin.authentication_type_desc,
Perm.state_desc, Perm.permission_name
FROM sys.database_principals Prin
INNER JOIN sys.database_permissions AS Perm on Prin.principal_id = Perm.grantee_principal_id
WHERE Prin.name='vito'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjtoZxl7vGjg-MNQ01VXCFEoAvqb-lYSUYcnQp-dIfxkAXIoHd0JCTLJoUYXlxqT31B4cWyM0B4o0AepSqbDP1FPYAA8m6pi5jZPAoC7z6lsUh1CLW5kTyiU5yE8fs8E2AZcRYGG8ym6pk/s0/sql-principal-permissions.png)

#### 查詢使用者對資料庫物件的相關權限
```sql
select Prin.principal_id, Prin.name, Prin.type_desc, Prin.authentication_type_desc,
Perm.state_desc, Perm.permission_name , s.name + '.' + o.name AS ObjectName
FROM sys.database_principals Prin
INNER JOIN sys.database_permissions AS Perm on Prin.principal_id = Perm.grantee_principal_id
INNER JOIN sys.objects AS o ON Perm.major_id = o.object_id
INNER JOIN sys.schemas AS s ON o.schema_id = s.schema_id
WHERE Prin.name=N'vito'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSGrbawVQ0i2kgIfKvOlnLOtK3fX3EV5JRF0lAYb53qT-hp3lXfqlK4tV5vF5GEGsQlE764ryJuCmcfntJhefFR0PBlT-aVoeSqkYiIXywvUH656PJdpUb8vF9ojHiaM7maDnZN22VcEA/s0/sql-object-permissions.png)

# Application Roles

「應用程式角色」可以用於將權限指派給應用程式，也就是只允許特定應用程式來連接存取資料，而不是透過資料庫角色或使用者。

- 「應用程式角色」屬資料庫層級物件。
- 「應用程式角色」的權限同 guest 使用者，所以應用程式角色將無法存取任何已停用 guest 的資料庫。
- 要啟用「應用程式角色」功能，可使用 [sp_setapprole](http://msdn.microsoft.com/zh-tw/library/ms188908.aspx) (需要有密碼)。

更多內容可參考：

- [應用程式角色](http://msdn.microsoft.com/zh-tw/library/ms190998.aspx)
- [在 SQL Server 中建立應用程式角色](http://msdn.microsoft.com/zh-tw/library/bb669062.aspx)

# Contained Users

Contained Users 是 Contained Database 中的使用者主體，更多內容可[參考這篇](http://vito-note.blogspot.com/2013/09/blog-post_24.html)
## 參考資料  

- [建立資料庫使用者](http://msdn.microsoft.com/zh-tw/library/aa337545.aspx)
- [資料庫層級角色](http://msdn.microsoft.com/zh-tw/library/ms189121.aspx)
- [應用程式角色](http://msdn.microsoft.com/zh-tw/library/ms190998.aspx)
- [在 SQL Server 中建立應用程式角色](http://msdn.microsoft.com/zh-tw/library/bb669062.aspx)