---
title: 封裝安全性
layout: default
parent: SSIS
nav_order: 14
description: "封裝安全性"
date: 2014-01-10
tags: [SQL,SSIS]
postid: "4573311936116198476"
---
#### SSISDB Security

The **SSISDB security model** is implemented as relationships between the following: 

- SSISDB principals：SSISDB catalog users
- SSISDB securables：SSISDB folders, projects, and/or operations).
- SSISDB permissions：define which SSISDB principals can perform which SSISDB operations on which SSISDB securables.
- 

By default, after the SSISDB catalog has been created, no users are granted access to it.   An administrator has to grant access permissions to the users explicitly before the SSISDB catalog can actually be used.  

必須是 SSISDB 中的使用者才可以存取 Integration Services 目錄

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsFtwqcKFiu4ekBtn5eev0MkCX0NKQzO4Hzg2AvZxLQd8Au-XHiLSTkgRBCA15lXOYfDHgsv3YijOWm5QfVcqHMIMIi1ZQKmln5gekQDWyoiTLrJwFUoDljBVgpC71XnXD8kRPyE5mN7o/s0/ssis-security-01.png)

# Principals（主體）

- SSISDB Principals 的設計是採用和 SQL Server 資料庫中的主體設計類似。
- 必須是 SSISDB 資料庫中的使用者才可以存取 Integration Services 目錄。
- 只要是 SSISDB 資料庫中的使用者，就可以部署專案到 SSIS Server。
- 只要可以存取 Integration Services 目錄的使用者，預設就可以使用所有公用的 catalog view 和 stored procedure 。
- 下面幾個預存程序為管理用的，則必須要有管理員權限才可以執行：
- catalog.configure\_server
- catalog.grant\_permission
- catalog.revoke\_permission
- 系統中，有個預設的 **ssis\_admin** 角色，這個角色具有執行管理員工作的權限。所以該角色中的成員就具有管理員權限。
- 建立 SSISDB 的使用者就是 SSISDB 的 owner ，而 db\_owner 角色預設就是 ssis\_admin 成員，所以建立 SSISDB 者，預設就有管理員權限。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKi4JtDyeYoFY8yxw8ekPQmiImaIl5jM5CuOR-aIe1Y0NpLQT7T-4k1FkOfqhEZV8lq7AhGsukz23zyNAoQ6cZP6Wm-wxx6UO5z91jIxn9GS58dkq68XKR_bVVo-76rWTsuD20500DM9I/s0/ssis-security-02.png)

# Securables（安全性實體）

SSISDB 的安全性實體，也是採用和一般資料庫的安全性實體（如：table, views, function 等）相同的方式。

下列四個物件是 SSISDB 中的安全性實體：（第一碼數字表示該物件代碼(object\_type)）

- （1）資料夾（Folders）：**資料夾**是最上層的安全性實體，其內層即為**專案**。
- （2）專案（Projects）：**專案**包含封裝內容及參考到的**環境**。
- （3）環境（Environments）：**環境**則是用來設定環境變數。
- （4）作業（Operations）：。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmemGvXC6D04fxy5ab4-TVhR4LfC0k7vS5dKAjH4NL975xVi9dWny0UIgnqLE5waG4tXCVhDZYp58rf3Q4D7xK_9zEXpHULUAH0yxxgRXHv5NJR6MiETcClmkf_aH3XpALux3fYIBpl1o/s0/ssis-security-03.png)

# Permissions（權限）

SSISDB 的權限設定也是和資料庫的權限設定使用相同方式。  Principals 必須對 Securables 有 Permissions ，才可以存取。  你可以使用 [catalog.grant_permission](http://technet.microsoft.com/zh-tw/library/ff878150.aspx) 來設定權限。  
```sql
grant_permission 
[ @object_type = ] object_type
, [ @object_id = ] object_id
, [ @principal_id = ] principal_id
, [ @permission_type = ] permission_type
```

object\_type：資料夾 (1)、專案 (2)、環境 (3) 和作業 (4)。 

下表是針對 SSISDB 物件可設定的 Permissions ：  

| Permissions名稱 | 說明 | 可套用物件 |
| --- | --- | --- |
| （1）讀取 | READ | Folder、Project、Environment、Operation |
| （2）修改 | MODIFY | Folder、Project、Environment、Operation |
| （3）執行 | EXECUTE | Project |
| （4）管理權限 | MANAGE\_PERMISSIONS | Folder、Project、Environment、Operation |
| （100）建立物件 | CREATE\_OBJECTS | Folder |
| （101）讀取物件 | READ\_OBJECTS | Folder |
| （102）修改物件 | MODIFY\_OBJECTS | Folder |
| （103）執行物件 | EXECUTE\_OBJECTS | Folder |
| （104）管理物件權限 | MANAGE\_OBJECT\_PERMISSIONS | Folder |

## 權限設定

下面範例示範如何設定使用者 (vito) 對資料夾 (MyFolder2) 的存取權限：

### 使用 SSMS UI

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8tINmgP9iDw7WSDlUrP7zzAM-8VIlTQ4FKogRmEO18Z8SqfpboE5IYI1MHcnM0rqrMpCv4zAdhmu1Q439qMjtfNxZCPVni6fZ0Y7dIlWt8nOLSc0xP-NKpmrulgv8noB_WS8KAqEEuGo/s0/ssis-security-05.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVwAWUd0GCknHY0kh-CU7hABbih8iqJXTN9aZ-Xya-5zLrno45abOgPwplYU9UrU4Eds0W130xhEVXSXvNqFFUSS-t6b3A2AXkIQmAEJb8XnkMMYvLjCEw6sc9jXS_vRklPLO_EB4TVUY/s0/ssis-security-06.png)

### 使用 TSQL
```sql
declare @object_type smallint = 1;
declare @object_id bigint ;
declare @principal_id int ;
declare @permission_type smallint = 1;

-- get folder id
SET @object_id= (select folder_id from catalog.folders where name='MyFolder2');
-- get user's principal id 
SET @principal_id= (select DATABASE_PRINCIPAL_ID ( 'vito' ));

-- grand permission
exec catalog.grant_permission @object_type, @object_id, @principal_id, 1;
exec catalog.grant_permission @object_type, @object_id, @principal_id, 2;
exec catalog.grant_permission @object_type, @object_id, @principal_id, 101;
exec catalog.grant_permission @object_type, @object_id, @principal_id, 102;
```

## 關於 SSISDB 的權限設定

- SSISDB 中的 securables 是使用 SSISDB 資料庫來加以管理。
- 每個 securables 在 SSISDB 中都有一個對應 table 。
- 每個 securables 也都有一個相對應的權限設定表，其中每一筆資料列就表示一項權限設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsW0ye-Csa0b7SunYxdLqpEgLqQ6qCpNK4qOnYUWQF_V87M1aGuVZPkThNKlLPqexpch02LdBtamQOIlf351HTFRCAAS_4HM8O_a-U52X1GER3E0LoVr_zbMVXNS9QFPG6X0Bmj74SWnE/s0/ssis-security-04.png)

## Permission Inheritance

- SSISDB objects are hierarchical, which allow permissions of child object to be inherited from the ancestral object.
- The following objects cannot have permissions assigned directly, only through inheritance:
- **Packages** and **environment references** inherit permissions from the containing project.
- **Environment variables** inherit permissions from the containing environment.

## Default Permissions

預設上，只要是 SSISDB 資料庫中的使用者，就可以部署專案到 SSIS Server，其他的權限都必須被明確的授予才行。  但是，對建立 SSISDB 物件的使用者，預設會直接隱含的授予特定權限：  

- Any user who deploys a project is automatically granted permissions to read, modify, and execute that project.
- Any user who creates an environment is automatically granted permissions to read and modify that environment.
- Any user who creates an operation is automatically granted permissions to read and modify that operation.
-

#### 封裝保護等級

- 不要儲存機密資料 (DontSaveSensitive)
- 所有資料都以密碼加密 (EncryptAllWithPassword)
- 所有資料以使用者金鑰加密 (EncryptAllWithUserKey)
- 機密資料以密碼加密 (EncryptSensitiveWithPassword)
- 機密資料以使用者金鑰加密 (EncryptSensitiveWithUserKey)
- 依賴伺服器儲存體進行加密 (ServerStorage)

## 參考資料  

- [SSIS Catalog](http://technet.microsoft.com/en-us/library/hh479588.aspx)
- [封裝中的敏感性資料存取控制](http://technet.microsoft.com/zh-tw/library/ms141747.aspx)
-