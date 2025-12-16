---
title: Select
layout: default
parent: Query
nav_order: 3
description: "Select"
date: 2013-05-02
tags: [SQL,Query]
postid: "8293429777400068886"
---
```sql
select empid, empname, gender from (
values ('001','abc',1)
) tblEmployee( empid, empname, gender )
```