---
title: 自主資料庫
layout: default
parent: Database Manage
nav_order: 5
description: "自主資料庫"
date: 2013-09-24
tags: [SQL,Database Manage]
postid: "2451617401286812440"
---
#### 自主資料庫 (Contained Database)

一般將資料庫透過備份與還原方法複製到另外一台Server時，往往還需要完成其他的資料庫設定才算完成，例如登入、角色與設定等等。  「自主資料庫(Contained Database)」是 SQL Server 2012 新推出的功能，它可以讓整個「自主資料庫」與「執行個體」層級之間是獨立隔離的，將來在搬移「自主資料庫」到另外一個「執行個體」時，無需額外的管理組態作業，並且提供了資料庫層級的使用者驗證機制。   

#### 自主使用者 (Contained Users)

自主資料庫不使用原有的 SQL logins 進行登入，它使用一種稱為「自主使用者(Contained Users)」或者 Windows 主體(Principle)進行連線。  

# 「自主資料庫」概念

## 什麼是「自主資料庫」

簡單講，「自主資料庫」就是「資料庫」和「資料庫執行個體」彼此不相依存的一種資料庫。

#### SQL Server 2012 透過以下方式協助將資料庫與執行個體隔離：

- 將原先存放在 master db 中用來定義資料庫的 metadata ，改成直接存放在資料庫中。
- 所有中繼資料都使用相同「定序」來定義。
- 可以直接透過資料庫層級的「有密碼使用者」進行授權登入，減少與執行個體登入帳戶的相依性。
- 相關的動態管理檢視、XEvent等，也會加入了對「自主資料庫」相關資訊的回應與處理。

## 什麼是「內含項目（Containment）」

在「自主資料庫」內會使用到的功能，稱為：「內含項目（Containment）」。  依據所使用到的功能，可將自主資料庫分為：

- 完全自主資料庫（fully contained database）。
- 部分自主資料庫（Partially contained databases）。

若用到的功能全部的功能都能夠在資料庫內部處理的，稱為「完全自主資料庫」。  若有部分功能，仍位於資料庫外部或是仰賴與資料庫外部的功能來互動執行，稱為「部分自主資料庫」。

## 使用自主資料庫的限制

部分自主資料庫不允許下列功能。

- 無法使用複寫、異動資料擷取，或變更追蹤。
- 編號程序。(Numbered procedure)
- 不能使用可以用來變更定序的結構描述繫結物件

# 啟用「自主資料庫」

執行個體的「自主資料庫」功能預設是不啟用的，若要啟用，可以使用以下方法：

#### 使用 T-SQL ：
```sql
-- 啟用自主資料庫
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
-- 將資料庫轉換成部分自主資料庫
ALTER DATABASE [TestDB] SET CONTAINMENT = PARTIAL
GO
```

#### 使用 SSMS ：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhhBI3Cbd_0CyPFzmVU9gPbfweLRxp46k56wV9j-Hb4Tct9FWDoThPimpzGI54oAFdYfAQtagI5zwAPj0r3PFZDuk7JtkSnWbKOI8a9j_Dn0-Fcnz_wrykGkiUIanj7qE47zP3HgHrpzys/s0/sql-enable-contained-db.png)

# 建立「自主資料庫」

在啟用自主資料庫選項後，就可以在新增資料庫時，將「內含項目類型（Containment type ）」設定成「部分（Partial）」。

T-SQL 
```sql
-建立部分自主資料庫
CREATE DATABASE [TestDB] 
CONTAINMENT = PARTIAL 
ON PRIMARY ( NAME = N'TestDB', FILENAME = N'C:\TestDB.mdf' )
LOG ON ( NAME = N'TestDB', FILENAME = N'C:\TestDB_log.ldf')
```

SSMS GUI 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjx7FiBKIM3iWOdXYhdrkvEHRGC_Dk5NjDxTaLYCyGfVEPsGFpkr9Xneh8C38arajw4h0eq_hUqNyYtQGHAuRPRW7O6DIJNt92LJCPPAKkXdlWm0lqYjKMyVKAfeXixa97Xs3GJTSqj8gw/s0/sql-create-contained-db.png)

# 轉換「自主資料庫」與「非自主資料庫」

在啟用自主資料庫選項後，你也可以將原本的資料庫轉換成「自主資料庫」。

## 轉換資料庫
```sql
--將現有資料庫轉換成「自主資料庫」
ALTER DATABASE [TestDB] SET CONTAINMENT = PARTIAL
GO

--將「部分自主資料庫」轉換成「非自主資料庫」
ALTER DATABASE [TestDB] SET CONTAINMENT = NONE
GO
```

若要將「自主資料庫」轉換成「非自主資料庫」，必須注意，在「自主資料庫」中，不能含「有密碼的使用者」。  因為有密碼的使用者，只能在自主資料庫中使用

## 轉換使用者

若要將原資料庫中的登入移轉為具有密碼之自主資料庫使用者，你可以使用 [sp_migrate_user_to_contained](http://technet.microsoft.com/zh-tw/library/ff929275.aspx) 這個預存程序：

### 移轉單一使用者

下列範例會將名為 vito 的 SQL Server 登入移轉為具有密碼之自主資料庫使用者。  此範例會保留使用者名稱，而且將登入保留為已啟用。
```sql
--移轉單一使用者
sp_migrate_user_to_contained 
@username = N'vito',
@rename   = N'keep_name',
@disablelogin = N'do_not_disable_login' ;
```

### 將所有具有登入的資料庫使用者移轉為沒有登入的自主資料庫使用者

下列範例會將以 SQL Server 登入為基礎的所有使用者移轉至具有密碼之自主資料庫使用者。  此範例會排除未啟用的登入。 您必須在自主資料庫中執行此範例。 
```sql
DECLARE @username sysname ;
DECLARE user_cursor CURSOR
FOR 
SELECT dp.name 
FROM sys.database_principals AS dp
JOIN sys.server_principals AS sp 
ON dp.sid = sp.sid
WHERE dp.authentication_type = 1 AND sp.is_disabled = 0;
OPEN user_cursor
FETCH NEXT FROM user_cursor INTO @username
WHILE @@FETCH_STATUS = 0
BEGIN
EXECUTE sp_migrate_user_to_contained 
@username = @username,
@rename = N'keep_name',
@disablelogin = N'disable_login';
FETCH NEXT FROM user_cursor INTO @username
END
CLOSE user_cursor ;
DEALLOCATE user_cursor ;
```

# 登入「自主資料庫」

#### 使用者類型

在「自主資料庫」上，可以使用以下使用者類型來登入存取資料庫的資源：

- 具有密碼之自主資料庫使用者：此為資料庫層級的驗證機制。
- Windows主體：此為資料庫層級信任Windows驗證機制。

### 建立「具有密碼使用者」
```sql
USE [TestDB]
GO

CREATE USER [ContainedUser] WITH PASSWORD=N'1234'
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhpnVeBuOozo4tpm_2U57A18SfHoNZubcXZt6ZE50JkRX5i0zCNu6G9Rji-N8WlN3B-70ZkuSCgAlSdsCT9tYq-pjoePHLCOmlLCz5KTsDFEIehHRle3lfW7xFgUO6fERu-SqM8FpEkDiQ/s0/sql-create-contained-user.png)

### 使用「具有密碼使用者」登入「自主資料庫」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjw1rH9cL_ap322ddYh1vJxkYpdJnN-5hWro8IgsdnoL-BJwy9qbB65pwSki4usLU_QerwAIwwga3yMXCqDqEbs7PEpj84wLjCcSLhqKk23jHACCgfwxL792zrPV3yvKp0W7XnSVrlYk2E/s0/sql-login-contained-db-1.png)

若是輸入帳號和密碼，登入時會發生以下錯誤

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-Az9ULPAxBOn4joG2f79A1mRoKAKHQQ1EdVi83eslWfWOzxVrGDY51UxAeUUW38QlvoTt_5j8MrGaDSevphNGowwydE37-MtUhHlGG24H9zVmwS_hUtoHPkMZIQHQ8Zt8yn6Ib2Tkt6Y/s0/sql-login-contained-db-error.png)

為了正確登入，還必須自行輸入「連線資料資」：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3-We5Ubd5flu4etRpNyfDflkeoNslKOfDgSKjCHs2JoNF9lxXWFNxXT226kNc9EMTJiggm5vnOKzBUUeGqiWutbP5MaBrHlH7OtoV645qo1rDqtL0vD6gln_jNpVCxIbVWauUdYG-isM/s0/sql-login-contained-db-2.png)
&lt;!--相關文章--&gt;  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2Q1Ch0u6DdE7o3mJIqe9Q-tlW1K61LeLq7ZCdEAeAF3NC0R3zMj_CxYX_YlXVbOgBUmVX0U7tPO0ylNpH5PN3_JfocbUJ4-yQdOr_TB_dYYQ3UO5Fuf9CpKQVykMY9LOtKVOFiTiPWCA/s120/RelationPage.png)

- [自主資料庫](http://technet.microsoft.com/zh-tw/library/ff929071.aspx)
- [新手學SQL Server 2012「自主資料庫(Contained Database)」(1)](http://blogs.uuu.com.tw/Articles/post/2012/04/17/%E6%96%B0%E6%89%8B%E5%AD%B8SQL-Server-2012%E3%80%8C%E8%87%AA%E4%B8%BB%E8%B3%87%E6%96%99%E5%BA%AB%E3%80%8D%281%29.aspx)
- [Contained Databases in SQL Server Denali](http://www.sqldbadiaries.com/2011/02/01/contained-databases-in-sql-server-denali/)