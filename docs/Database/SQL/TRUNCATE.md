---
title: 資料新增、修改、刪除
layout: default
parent: SQL
nav_order: 1
description: "資料新增、修改、刪除"
date: 2013-05-02
tags: [SQL,Modify]
postid: "8878678197895911355"
---
# INSERT

針對所有欄位做新增時，就不須指定欄位

## INSERT INTO
```sql
INSERT TableA values ( 1, Default, Null, getdate())
```

- into 關鍵字可省略
- 使用 Default 關鍵字表示使用預設值
- 使用 Null 關鍵字表示 Null 值
- 使用 getdate() 函式可以取得系統時間

若針對特定的欄位做新增，就必須指定欄位
```sql
Insert TableA(id, field1) values ( 2, 'BB')
```

## INSERT ... SELECT

Using the [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) . . . [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) statement allows you to append rows to an existing table  based on data selected from a different table  
```sql
Insert into TableB
Select id, field1, field2, createdtime from TableA
```

## SELECT ... INTO

以查詢結果建立新的資料表。  
```sql
SELECT IDENTITY(int, 100,1) as id, field1, field2, createdtime 
INTO TableC 
FROM TableA
```

# UPDATE

更新所有資料
```sql
UPDATE TableB 
SET field2='CC'
```

加上 Where 條件
```sql
UPDATE TableB 
SET field1 += field2 
Where id=101
```

以另外一個資料表中的欄位值來更新現有資料
```sql
UPDATE TT2
SET TT2.Name = TT1.Name
From TT2, TT1
WHERE TT2.id = TT1.id

-- 下面這樣也可以

UPDATE TT2
SET TT2.Name = TT1.Name
From TT1
where TT2.id=TT1.id
```
```sql
UPDATE TT2
SET TT2.Name = TT1.Name
From TT2 
JOIN TT1 ON TT2.id = TT1.id
```
```sql
UPDATE TT2
SET TT2.Name = ( Select Name From TT1 WHERE TT1.id = TT2.id)

--這方法不算對，隱含陷井，因為沒比對到的資料列會被更新為 NULL ，要小心。
```

## Update之Write()子句

Write() 函數是 UPDATE 陳述式中用來執行區塊更新的子句，而且限制只能用在大數值的資料類型，如：varchar(max), nvarchar(max) and varbinary(max)。

**語法**
```sql
.WRITE ( expression, @Offset , @Length )
```

由 @Offset 開始，取 @Length 長度的資料，更新成 expression

#### 零頭NULL尾

- 若 @Offset = 0 ：由字串頭開始
- 若 @Offset = NULL ：由字串尾開始
```sql
--測試資料
create table #tmp( myname varchar(max) )
insert #tmp values ('123')

select myname from #tmp							--123

--插入在最後頭
update #tmp set myname.write('xyz', null, 0)	--123xyz

--插入在最前頭
update #tmp set myname.write('abc', 0, 0) 		--abc123xyz

--插入在位置3
update #tmp set myname.write('def', 3, 0) 		--abcdef123xyz

--以 'ABC' 置換位置3開始取長度2個,即 'de')
update #tmp set myname.write('ABC', 3, 2) 		--abcABCf123xyz
```
```sql
--測試資料
create table #tmp( myname varchar(max) )
insert #tmp values ('111')

--由字尾開始取長度5個,置換成'abc'	(也就是插入在字尾)
update #tmp set myname.write('abc', null, 5) 	--111abc
select len(myname) from #tmp					--6

--由位置2開始取長度0個,置換成'def'
update #tmp set myname.write('def', 2, 0) 		--11def1abc

--由位置0開始取長度0個,置換成'xyz'	(也就是插入在字首)
update #tmp set myname.write('xyz', 0, 0) 		--xyz11def1abc

--由位置3開始取長度2個,置換成'ABC'
update #tmp set myname.write('ABC', 3, 2) 		--xyzABCdef1abc
```

# DELETE

**簡單的範例**

### 簡單刪除的範例
```sql
DELETE TableB WHERE id>105
DELETE Top (2) From TableB WHERE id>105
```

### 參考另一個資料表進行刪除

**範例：以另外一個資料表中的欄位值來刪除現有資料**

就像 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 的用法， [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 資料時，也可以參考另一個資料表中的欄位值。  
```sql
DELETE From TableC
From TableB 
JOIN TableC ON TableB.id = TableC.id
WHERE TableB.id > 100
```

以下這個範例，透過 Join table 來刪除相關訂單的明細資料  
```sql
DELETE FROM [Order Details]
FROM ORDERS O JOIN [Order Details] OD ON O.OrderID = OD.OrderID
WHERE O.OrderDate < '07-10-1996'
```

### 刪除大量資料

若要刪除整個資料表中的資料，使用 Truncate Table 指令會比較快。  因為 DELETE 使用會使用 RowLock 機制，並且每刪除一筆就寫進交易紀錄檔；  而 TRUNCATE 是使用資料表和資料頁鎖定，它也會產生交易紀錄檔，只是內容少很多，當然也可以用來做復原用。  

但是若要刪除特定條件的大量資料，就無法使用 TRUNCATE 指令，因為它不接任條件語法。  但是若使用 DELETE 進行大量資料刪除的話，又可能導致使用者存取效能降低，因此可以透過分批的方式，降低對SQL Server的影響。  
```sql
WHILE  1 = 1            -- 一直進入迴圈   
BEGIN    
DELETE TOP (5000)   -- 利用 TOP 限制刪除筆數（1000為使用者自訂筆數、括號一定要存在）     
FROM tblUser     
WHERE Sex Is Null

IF @@ROWCOUNT = 0   -- 假如沒有刪除任何資料，則跳出迴圈      
BREAK   
END
```

# TRUNCATE

[TRUNCATE](http://msdn.microsoft.com/en-us/library/ms177570.aspx) 是用來刪除整個 Table 中的資料，相當於使用 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 指令，但不加任何 WHERE 條件。那麼二者的差異為何？  

#### 使用 DELETE

使用 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 語法會耗用較大資源，因為 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 語法：  
1) [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 為 [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 命令，資料刪除時是一筆筆移除的，且每筆刪除的資料都會有 log 記錄在交易記錄中。  
2) 若該 Table 有設定 Trigger ，將會引發觸發程序。  
3) 若該 Table 有自動編號的識別欄位，在 Delete 所有資料後，若再執行新增資料時，識別編號會從之前最後一個編號之後繼續編號下去。  

#### 使用 TRUNCATE

使用 [TRUNCATE TABLE](http://msdn.microsoft.com/en-us/library/ms177570.aspx) 會移除資料表中的所有資料列，但會保留資料表結構及其欄位、條件約束、索引等。  
1) [TRUNCATE TABLE](http://msdn.microsoft.com/en-us/library/ms177570.aspx) 為 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 命令，資料刪除時是以整個資料頁(Data Page)為對象，所以交易記錄只會記錄頁面的取消配置。  
2) [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 會鎖定每一筆資料，以便執行刪除。 [TRUNCATE](http://msdn.microsoft.com/en-us/library/ms177570.aspx) 只鎖定資料表和資料頁，不會鎖定每個資料列。  
3) 若該 Table 有自動編號的識別欄位，在 Truncate 所有資料後，識別編號會重設為原本定義的初始值。  

使用語法：
```sql
TRUNCATE TABLE MyTestTable
```