---
title: Cursor
layout: default
parent: StoredProc、Triggers、Views
nav_order: 4
description: "Cursor"
date: 2013-05-17
tags: [SQL,StoredProc、Triggers、Views]
postid: "5294406988551739426"
---
SQL Server is built to process sets of data. However,   there are times when you need to process data one row at a time.   The result of a SELECT statement is returned to a server-side object called a **cursor**,   which allows you to access one row at a time within the result set and even allows scrolling forward as well as backward through the result set.  

# 認識游標

DECLARE CURSOR 子句
```sql
DECLARE cursor_name CURSOR 
[ LOCAL | GLOBAL ]
[ FORWARD_ONLY | SCROLL ]
[ STATIC | KEYSET | DYNAMIC | FAST_FORWARD ]
[ READ_ONLY | SCROLL_LOCKS | OPTIMISTIC ]
[ TYPE_WARNING ]
FOR 
select_statement
[ FOR UPDATE [ OF column_name [ ,...n ] ] ]
```

1. 游標有效範圍：**LOCAL**| GLOBAL
- LOCAL：區域游標表示該游標只在這個範圍內有效。
- GLOBAL：全域游標表示該游標可以在同一個 connection 下的所有 sp 或 batch 中共用。
2. 游標讀取方式：**FORWARD\_ONLY**| SCROLL
- FORWARD\_ONLY：游標只可以順向讀取。
- SCROLL：游標可以任意捲動讀取。
3. 游標型態：STATIC | KEYSET | DYNAMIC | **FAST\_FORWARD**
- STATIC：靜態資料指標，唯讀，且無法得知其他使用者的變更。
- KEYSET：索引鍵集資料指標，使用索引值關連到來源資料表中的實際資料。
- DYNAMIC：動態資料指標。
- FAST\_FORWARD：
4. 游標更新方式：**READ\_ONLY**| SCROLL\_LOCKS | OPTIMISTIC
- READ\_ONLY：唯讀游標，不需鎖定。
- SCROLL\_LOCKS：使用鎖定資料到，確保資料異動的正確性。
- OPTIMISTIC：樂觀游標，不需鎖定，利用 timestamp 來判斷資料列是否有被修改過。

## 游標讀取方式

### FORWARD\_ONLY

指定 cursor 只能從第一筆捲到最後一筆資料列。也就是只支援 FETCH NEXT 選項。

### SCROLL

這個項選表示可以使用所有 FETCH 功能(FIRST、LAST、PRIOR、NEXT、RELATIVE、ABSOLUTE)。

- FETCH FIRST：
- FETCH LAST：
- FETCH NEXT：
- FETCH PRIOR：
- FETCH ABSOLUTE n：Fetches to nth row from the beginning of the result set.
- FETCH RELATIVE n：Fetches to nth row from the current cursor pointer.

## 游標型態

### STATIC

游標會將符合查詢的結果放到暫存資料表中，如同一個靜態的快照。  你可以在這些資料中任意的移動游標以讀取資料，但因為讀取的是暫存的資料表，  所以其他使用者若對來源資料表做任何的更動，都不會影響到目前游標中的資料。  除非把游標關閉再開啟，以重讀一遍資料來源。

### KEYSET

這個機制會在暫存資料表中建立一個查詢結果的索引資料，  索引資料則關連到來源資料表中的實際資料，所以當讀取資料時，可以立即反應別的使用者對這個資料集中的資料進行 UPDATE 時。  但也因為它是以索引鍵識別，所以只要是索引鍵無法反應的操作 (INSERT) 就無法反應到游標上。

### DYNAMIC

這個機制不另外使用到暫存資料表，所以開啟游標的成本最低。  但是，查詢的成本卻是最高的，因為開啟游標後，其他使用者對來源資料的新增刪除與修改，都會反應到查詢的結果中。

### FAST\_FORWARD

Fast\_Forward 機制其實是使用上述三種方式，再搭配設定游標移動方向。  使用 Fast\_Forward 等同使用 FORWARD\_ONLY + READ\_ONLY。

## Concurrency Options

- READ\_ONLY：唯讀游標，不需鎖定。
- SCROLL\_LOCKS：當資料列讀入資料游標時，SQL Server 會立即鎖定這些資料列，以確保之後可對它們進行修改。
- OPTIMISTIC：樂觀游標，不需鎖定。當資料列讀入資料游標時，SQL Server 不會鎖定資料列。      當它要更新資料時，會利用 timestamp 來判斷資料列在讀入資料游標之後是否有被修改過。      若判斷結果為有被修改過，則會更新失敗。

# 開啟游標與讀取資料
```sql
--宣告游標需要用到的參數
DECLARE @ProductID int, @ProductName nvarchar(40), @UnitPrice money

--定義游標的資料內容
DECLARE cur_Products CURSOR 	
FOR SELECT ProductID, ProductName, UnitPrice FROM Products

--開啟游標
OPEN cur_Products;

--擷取游標內容
FETCH NEXT FROM cur_Products INTO @ProductID, @ProductName, @UnitPrice

WHILE @@FETCH_STATUS = 0
BEGIN
PRINT @ProductName
FETCH NEXT FROM cur_Products INTO @ProductID, @ProductName, @UnitPrice
END

--關閉游標
CLOSE cur_Products;

--結束游標
DEALLOCATE cur_Products;
```

# 修改與刪除
```sql
--宣告游標需要用到的參數
DECLARE @EmpNo int, @EmpName nvarchar(50)

--定義游標的資料內容
DECLARE cur_Emp CURSOR 	
FOR SELECT EmpNo,EmpName FROM Emp
FOR UPDATE of EmpName

--開啟游標
OPEN cur_Emp;

--擷取游標內容
FETCH NEXT FROM cur_Emp INTO @EmpNo, @EmpName

WHILE @@FETCH_STATUS = 0
BEGIN
--更新目前游標所指的資料列
UPDATE Emp set EmpName = @EmpName + CAST(@EmpNo as varchar(3))
WHERE CURRENT OF cur_Emp

--下一筆
FETCH NEXT FROM cur_Emp INTO @EmpNo, @EmpName
END

--關閉游標
CLOSE cur_Emp;

--結束游標
DEALLOCATE cur_Emp;
```
## 參考資料  

- [資料游標](http://msdn.microsoft.com/zh-tw/library/ms191179.aspx)
- [DECLARE CURSOR](http://msdn.microsoft.com/zh-tw/library/ms180169.aspx)
-