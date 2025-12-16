---
title: 如何做：存在更新、不存在新增
layout: default
parent: HowTo
nav_order: 4
description: "如何做：存在更新、不存在新增"
date: 2013-03-12
tags: [SQL,HowTo]
postid: "5750617834412283068"
---
唉...又是防老語法...
```sql
IF EXISTS (SELECT \* FROM Table1 WHERE Column1='SomeValue')
UPDATE Table1 SET (...) WHERE Column1='SomeValue'
ELSE
INSERT INTO Table1 VALUES (...)
```
```sql
UPDATE Table1 SET (...) WHERE Column1='SomeValue'
IF @@ROWCOUNT=0
INSERT INTO Table1 VALUES (...)
```