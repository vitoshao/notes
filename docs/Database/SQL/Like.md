---
title: Like
layout: default
parent: SQL
nav_order: 4
description: "Like"
date: 2013-05-02
tags: [SQL,Query]
postid: "829772816322690340"
---
## Like 子句的萬用字元

- Percent (%) ：表示任意字數的字元
- Underscore (_) ：表示任意的一個字元
- Square Brackets ([]) ：表示必須在集合中的任意一個字元
- Caret (^) ：表示不在集合中的任意一個字元
```sql
--B開頭S結尾,中間為任何長度的字元
select * from customers where CustomerID like 'B%S'

--B開頭S結尾,但中間只有三個字
select * from customers where CustomerID like 'B___S'

--第三個字必須是 L 或 T
select * from customers where CustomerID like 'BO[L,T]__'

--第三個字不能是 L 或 T
select * from customers where CustomerID like 'BO[^L,^T]__'
```