---
title: 登入者、使用者、結構描述
layout: default
parent: Identity and Access Control
nav_order: 1
description: "登入者、使用者、結構描述"
date: 2013-05-22
tags: [SQL,Identity and Access Control]
postid: "2849025312315759937"
---
# 建立登入（Login）
```sql
--建立
CREATE LOGIN [vxxxxxxo] WITH PASSWORD = 'vxxxxxxo'

--刪除
DROP LOGIN [vxxxxxxo] 
```
```sql
--建立
USE AdventureWorks2012;
CREATE USER [vxxxxxxo] FOR LOGIN [vxxxxxxo] WITH DEFAULT_SCHEMA=[dbo]

--刪除
DROP USER vxxxxxxo

--授予連線權限
GRANT CONNECT TO [vxxxxxxo] AS [dbo]
```

# 建立資料庫使用者（Database User）

# 建立資料庫結構描述

在 SQL2000 時，指定物件的格式為：
```sql
伺服器名稱.資料庫名稱.[擁有者名稱].物件名稱```

在 SQL2005 時，指定物件的格式為：
```sql
伺服器名稱.資料庫名稱.[結構描述名稱].物件名稱```

但是在 SQL2005 中，這個結構描述並不等於擁有者；結構描述
## 參考資料

- [如何：建立 SQL Server 登入](http://msdn.microsoft.com/zh-tw/library/aa337562.aspx)
- [如何：建立資料庫使用者](http://msdn.microsoft.com/zh-tw/library/aa337545.aspx)
- [如何：建立資料庫結構描述](http://msdn.microsoft.com/zh-tw/library/dd207005.aspx)