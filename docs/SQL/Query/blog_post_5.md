---
title: 查詢基本語法
layout: default
parent: Query
nav_order: 5
description: "查詢基本語法"
date: 2013-05-02
tags: [SQL,Query]
postid: "2561639544909169150"
---
### TSQL 可分成三種類型

#### [資料定義語言（Data Definition Language, DDL）](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx)

包括 CREATE 、 ALTER 、 DROP 子句，用來用來定義資料庫的物件，例如：
```sql
Create Table Ord( OrdId int primary key, OrdDate datetime, EmpId int)
```

#### [資料操作語言（Data Manipulation Language, DML）](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx)

包括 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 、 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 、 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 、 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 子句，用來操作資料內容，例如：
```sql
Select * From Customers
```

#### [資料控制語言（Data Control Language, DCL）](http://en.wikipedia.org/wiki/Data_control_language)

包括 [GRANT](http://msdn.microsoft.com/zh-tw/library/ms187965.aspx) 、 [DENY](http://msdn.microsoft.com/zh-tw/library/ms188338.aspx) 、 [REVOKE](http://msdn.microsoft.com/zh-tw/library/ms186308.aspx) 子句，用來設定資料庫權限，例如：
```sql
Grant Select, Insert, Delete, Update On Customer To Jack
```
## 參考資料  

- [Transact-SQL 參考](http://msdn.microsoft.com/zh-tw/library/bb510741.aspx)
- 
-