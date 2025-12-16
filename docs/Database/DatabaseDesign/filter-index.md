---
title: 如何建立篩選索引
layout: default
parent: Database Design
nav_order: 2
description: "篩選索引是一種索引，它只包含表中的部分資料列。"
date: 2025-02-24
tags:
  - filtered indexes
---

## 篩選索引 (filtered indexes)

- 篩選索引是一種索引，它只包含表中的部分資料列。
- 篩選索引可以定義索引的範圍，以便只包含表中的部分資料列。
- 篩選索引可以提高查詢效能，減少索引的大小，並改善索引的維護效率。

以上是 copilot 自已寫的，白話一點就是建立索引時，這個索引不是含蓋全部的資料，只針對符合條件的資料才建立索引。 

例如下面範例，依 OrderDate 欄位建立一個索引，但這個索引只對 IsValid=1 的資料做索引，這樣可以減少索引的大小，提高查詢效能。

### 建立篩選索引
```sql
 CREATE NONCLUSTERED INDEX [IX_Orders_OrderDate_Isvalid] 
  ON [dbo].[Orders] ([OrderDate] ASC)
 WHERE (IsValid = 1)
 ```

## 唯一篩選索引 (unique filtered indexes)

有時候我們需要限定某個欄位，只有在特定條件時，才需是唯一的，這時候就可以使用`唯一篩選索引`。
例如 Order 資料表的 OrderId 欄位，若我們希望，當這個欄位有值的時候，它必須是唯一的，但若是 NULL 時則不限定，則可以這麼做：

```sql
 CREATE UNIQUE NONCLUSTERED INDEX [IX_Orders_OrderId] 
  ON [dbo].[Orders] ([OrderId] ASC)
 WHERE [OrderId] IS NOT NULL ;
 ```
 另外，這類問題，也可以透過 Trigger 來處理，在資料新增前，判斷唯一的條件值是否存在，若已經存在，再產生 Exception 即可。

 ```sql
 CREATE TRIGGER Check_OrderId ON [dbo].[Orders] 
  AFTER INSERT,UPDATE 
AS
BEGIN

 Declare @OrderId varchar(10) =null;
 Select @OrderId=OrderId FROM inserted

 IF (@OrderId is not null)
 BEGIN
  IF EXISTS(Select OrderId FROM Orders Where OrderId=@OrderId)
  BEGIN
   Throw  50001, 'Duplicate OrderId', 1;
  END
 END 
END
 ```

## 參考資料
- <a target="_blank" href="https://learn.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes?view=sql-server-ver16&redirectedfrom=MSDN">建立篩選索引</a>
