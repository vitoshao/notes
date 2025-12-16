---
title: 判斷 Null 值
layout: default
parent: Query
nav_order: 2
description: "判斷 Null 值"
date: 2013-05-02
tags: [SQL,Query]
postid: "7196360384106211794"
---
#### 如何判斷欄位是否為 Null 值

因為 SQL Server 預設會使用 ISO 標準，所以 Null 欄位值不能使用 (=) 或 (&lt;&gt;) 進行比較，心須使用 **IS NULL** 和 **IS NOT NULL** 。  

除非將 ANSI\_NULLS 設為 OFF ，以使用非 ISO 標準。不過，將來的 SQL 版本將不再支援 OFF 。  
```sql
--ANSI_NULLS = On

SELECT \* FROM Production.Product WHERE Color is not NULL;   -- return non null records
SELECT \* FROM Production.Product WHERE Color <> NULL        -- return nothing

SET ANSI_NULLS OFF;
SELECT \* FROM Production.Product WHERE Color <> NULL        -- return non null records
```