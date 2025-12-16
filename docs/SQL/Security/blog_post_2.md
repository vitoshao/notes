---
title: Permissions
layout: default
parent: Security
nav_order: 2
description: "Permissions"
date: 2013-07-11
tags: [SQL,Security]
postid: "5568027315713954816"
---
SQL Server 是採用**角色為基礎**的授權模型（Role-base security, RBS）。  這個模型是依據**使用者名稱**或**所屬群組**來控制使用者存取資料的權限。  它主要包含兩個機制：  

- **驗證 (Authentication)** ：取得識別認證的程序。
- **授權 (Authurization)** ：授予特定資源的存取權限給通過驗證的識別。

例如，一個**主體**(Principal)使用特定的使用者名稱和密碼，交由驗證程序進行驗證。  如果認證結果為有效，則回傳給該實體一個已通過驗證的識別。  每個主體都有一個專屬的「識別碼（principal\_id）」和「安全性識別碼（SID）」。  這些識別碼資訊，可以利用 [sys.server_principals](http://msdn.microsoft.com/zh-tw/library/ms188786.aspx) 查閱。  
```sql
select \* from sys.server_principals
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEieCxlgy0dUnOfGPmNZA67VTbJCpOLFf4oEJMMwu1H_QV18i9SPOKz-hzqWpleymYbdCTNvmAPxYh6i2nGTnzT0KRd9rfWnrzGeH0nEMu7sKI6XB49XFVFIdItkPw17U6eXJYEr7D3R_pU/s0/sql-server_principals.png)

# 主體（Principal）

## 什麼是「**主體**」

簡單講，主體就是帳號，例如：sa。但是主體不單單就只有 SQL 中的使用者，更完整的定義如下：  

- 「主體」是可要求 SQL Server 資源的實體。
- 「主體」可以是單一個體，例如：登入帳戶 sa ，也可以是由主體所組合而成的集合，例如：Windows 群組。
- 「主體」的安排方式具有階層特性。
- 「主體」的影響範圍視主體的定義範圍 (Windows、伺服器、資料庫)。
- 每個「主體」都有一個安全性識別碼 (SID)。

## 主體的影響範圍

主體依其影響的範圍，可分成三類  

| 層級 | 主體 |
| --- | --- |
| Windows 層級主體 | Windows 網域登入帳戶  &lt;br&gt;  Windows 本機登入帳戶  &lt;br&gt;  Windows 群組 |
| SQL Server 層級主體 | SQL Server 登入帳戶 (Logins)  &lt;br&gt;  SQL Server 伺服器角色 (Server Roles) |
| Database 層級主體 | 資料庫使用者 (Users)  &lt;br&gt;  資料庫角色 (Database Roles)  &lt;br&gt;  應用程式角色 (Application Roles) |

# 安全性實體（Securables）

## 什麼是「**安全性實體**」

「安全性實體」指的是能由 SQL Server 管控存取授權的資源。例如：資料表、檢視...等。  

「安全性實體」採用巢狀階層式架構，也就是安全性實體可以包含在其他安全性實體內。

## 安全性實體包含範圍

安全性實體所包含的範圍，可以分成以下幾個層級：伺服器、資料庫以及結構描述。   

| 安全性實體「範圍」 | 包含的安全性實體 |
| --- | --- |
| 伺服器 | 端點   &lt;br&gt;  資料庫  &lt;br&gt;  登入  &lt;br&gt;  伺服器角色 |
| 資料庫 | 資料庫角色   &lt;br&gt;  應用程式角色   &lt;br&gt;  使用者   &lt;br&gt;  憑證  &lt;br&gt;  合約  &lt;br&gt;  結構描述  &lt;br&gt;  ... |
| 結構描述 | 型別  &lt;br&gt;  XML 結構描述集合   &lt;br&gt;  物件(彙總、函數、程序、資料表、檢視...) |

有些「主體」也可能會是「安全性實體」，例如：登入帳戶。  登入帳戶基本上是「主體」，但是，我們也可以在此登入帳戶上設定權限，所以登入帳戶也可以當作「安全性實體」。

# 權限（Permission）

「權限(Permission)」是 SQL Server 用來管控「主體(Principal)」對「安全性實體(Securables)」的存取限制。  
你可以透過授予或拒絕權限來管控主體對安全性實體的存取權。  
也可以將**登入**或**使用者**加入具有存取權的**角色**。  

## 如何指派權限

SQL 可以透過以下三個 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 陳述式來授予**主體**對**安全性實體**的權限。  

- [GRANT(授予)](http://msdn.microsoft.com/zh-tw/library/ms187965.aspx) ：將安全性實體的權限授予某個主體。
- [REVOKE(撤銷)](http://msdn.microsoft.com/zh-tw/library/ms186308.aspx) ：移除先前授予或拒絕的權限。
- [DENY(拒絕)](http://msdn.microsoft.com/zh-tw/library/ms188338.aspx) ：對主體拒絕權限。
```sql
GRANT <permission> [ ,...n ] ON 
[ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
TO <database_principal> [ ,...n ] 
[ WITH GRANT OPTION ]
[ AS <database_principal> ]
```

#### 例一
```sql
GRANT  SELECT ON [Orders] TO [vito]   -- 授予 vito 對 Order 資料表的 SLEECT 權限
DENY   DELETE ON [Orders] TO [vito]   -- 拒絕 vito 對 Order 資料表的 DELETE 權限
REVOKE INSERT ON [Orders] TO [vito]   -- 撤銷 vito 對 Order 資料表的 INSERT 權限
```

#### 例二
```sql
--授予 vito 對 Log 資料表的 INSERT,UPDATE 權限
GRANT INSERT,UPDATE ON [Log] TO vito

--授予 vito 對特定 sp 的 EXECUTE 權限
GRANT EXECUTE ON uspGetTop10Documents_Newest TO vito
GRANT EXECUTE ON uspGetTop10Documents_Random TO vito

--授予 vito 對所有物件的 EXECUTE 權限
GRANT EXECUTE TO vito;

--授予 vito 對 dbo 的 EXECUTE 權限
GRANT EXECUTE ON SCHEMA::[dbo] TO vito;

--將使用者加入特定角色，也可以授予其權限
ALTER ROLE [db_datareader] ADD MEMBER [vito]
```

## 使用資料庫角色來簡化權限管理

由上一章節知道角色分成「固定資料庫角色」和「彈性資料庫角色」。  每個固定資料庫角色都已包含特定權限，你可以利用它來簡單權限管理。  

### 使用固定資料庫角色

SQL Server 針對固定資料庫角色已預先設定好一些權限控管，例如 db\_datareader 和 db\_datawriter 等等。  我們可以直接利用這些資料庫角色來簡化授與使用者權限的管理。  

- 將**自定角色**加入固定資料庫角色 [sp_addrolemember](http://msdn.microsoft.com/zh-tw/library/ms187750.aspx)  ```sql
EXEC sp_addrolemember [db_datawriter], [mgr_user];
```
- 將**使用者**加入固定資料庫角色 [ALTER ROLE](http://technet.microsoft.com/en-us/library/ms189775.aspx)   ```sql
ALTER ROLE [db_datawriter] ADD MEMBER [mgr_user];
```

### 使用彈性資料庫角色

若要使用彈性資料庫角色，則必須先替該角色設定權限，再指派成員即可。  例如，若我們要授與使用者可以執行 Stored Procedure 的權限，就可以先建立一個 db\_executor 角色，方便管理這個功能。  
```sql
Use Northwind

-- 建立 db_executor 角色
CREATE ROLE db_executor

-- 授與 db_executor 角色具有 execute 權限
GRANT EXECUTE TO db_executor

-- 將 vito 加入 db_executor 角色中
EXEC sp_addrolemember 'db_executor','vito' 
```

### DENY 資料庫權限

#### db\_denydatawriter role

若你想限制使用者的修改權限，你可以使用 db\_denydatawriter 這個角色，但是它是針對整個資料庫。  若是只想限制特定的物件，就只能使用 [DENY](http://msdn.microsoft.com/zh-tw/library/ms188338.aspx) 指令搭配 ALTER, CONTROL, DELETE, INSERT, TAKE OWNERSHIP, [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 等權限設定。  
```sql
use [AdventureWorks2012]
GO

DENY ALTER ON [Person].[Address] TO [GAMMA-ROLE]
DENY INSERT ON [Person].[Address] TO [GAMMA-ROLE]
DENY UPDATE ON [Person].[Address] TO [GAMMA-ROLE]
GO
```

## 使用 Schema 來簡化權限管理

當設定權限也可以善用 Schema 來簡化管理。  例如，若要設定某個角色可以對 TableA, TableB, TableC 進行 SELECT, INSERT, [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 權限。  你可以將這個三個資料表都設成同一個 Schema ，再設定這個角色可以針對這個 Schema 進行 SELECT, INSERT, [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 權限即可。  而且若有新增的資料表要使用相同權限，只要設成同一個 Schema 即可，無須再針對這個資料表設定權限。  

1. 建立一個角色 RoleA ，這個角色是你想用來設定權限用的角色。
2. 建立一個 SchemaA
3. 將你要控管的資料表搬移到這個 SchemaA
4. 授予 RoleA 可以針對 SchemaA 進行 Select, Update, Inseer 權限
```sql
--step1
CREATE ROLE RoleA; 

--step2
CREATE SCHEMA schemaA;

--step3
ALTER SCHEMA schemaA TRANSFER dbo.Table1;
ALTER SCHEMA schemaA TRANSFER dbo.Table2;
ALTER SCHEMA schemaA TRANSFER dbo.Table3;

--step4
GRANT SELECT ON SCHEMA::schemaA TO [RoleA] ;
GRANT INSERT ON SCHEMA::schemaA TO [RoleA] ;
GRANT UPDATE ON SCHEMA::schemaA TO [RoleA] ;
```

## 權限檢視

### 使用 sys.fn\_builtin\_permissions 檢視所有可授權的權限清單

使用 [sys.fn_builtin_permissions](http://technet.microsoft.com/zh-tw/library/ms186234.aspx) 可檢視所有可授權的權限清單。
```sql
select \* from sys.fn_builtin_permissions(default) 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEht14QLxlvFbkAvBxbiBbk_53LlQ6A4aMcB9Kg37QLmzA7w4N-WP9iKmTJ07XppVfKNxQ_h-tEzAO63YwsxRGFle01Cq9DCOHsB7kP1sfbpZ09Bp7euSBHFB4didjcCABJGpkyWDBwY_u0/s0/fn_builtin_permissions.png)

### 使用 HAS\_PERMS\_BY\_NAME 檢視目前使用者的有效權限

使用 [HAS_PERMS_BY_NAME](http://technet.microsoft.com/zh-tw/library/ms189802.aspx) 函式可檢視目前使用者的有效權限。
```sql
--我有伺服器層級 VIEW SERVER STATE 權限嗎？
SELECT HAS_PERMS_BY_NAME(null, null, 'VIEW SERVER STATE');

--我可以模擬 (IMPERSONATE) 伺服器主體 Ps 嗎？
SELECT HAS_PERMS_BY_NAME('Ps', 'LOGIN', 'IMPERSONATE');
```
## 參考資料  

- [主體 (Principal)](http://msdn.microsoft.com/zh-tw/library/ms181127.aspx)
- [安全性實體 (Securables)](http://msdn.microsoft.com/zh-tw/library/ms190401.aspx)
- [權限 (Database Engine)](http://technet.microsoft.com/zh-tw/library/ms191291.aspx)
- [為主體授予權限](http://msdn.microsoft.com/zh-tw/library/ff878066.aspx)