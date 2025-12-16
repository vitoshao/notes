---
title: 如何使用逗號合併查詢結果
layout: default
parent: HowTo
nav_order: 10
description: "如何使用逗號合併查詢結果"
date: 2015-04-13
tags: [SQL,HowTo]
postid: "2576223073544589233"
---
## 如何使用逗號合併多筆資料到同一欄位

有時候我們會想要將多筆資料合併為一筆顯示，如下面資料中，若想要將同一個 TicketID 的 Person 用逗號間隔，可以這麼做：  
```sql
CREATE  TABLE Tickets (
[TicketID] char(5) NOT NULL,
[Person] nvarchar(15) NOT NULL
)

INSERT INTO Tickets VALUES
('T0001', 'Alice'),
('T0001', 'Bob'),
('T0002', 'Catherine'),
('T0002', 'Doug'),
('T0003', 'Elaine')
```

### 使用 Cursor

使用 Cursor 就是跑迴圈。
```sql
ALTER PROCEDURE usp_GetPersonList
(@TicketId varchar(max))
AS
DECLARE @Person varchar(Max);
DECLARE @PersonList varchar(Max);

DECLARE _cursor CURSOR FOR
SELECT Person FROM Tickets WHERE TicketId=@TicketId

OPEN _cursor
FETCH NEXT FROM _cursor INTO @Person

SET @PersonList='';
WHILE @@FETCH_STATUS = 0
BEGIN
Set @PersonList = @PersonList + ',' + @Person
FETCH NEXT FROM _cursor INTO @Person
END

IF LEN(@PersonList)>0
SET @PersonList = Substring(@PersonList,2,LEN(@PersonList));

SELECT @PersonList;

CLOSE _cursor
DEALLOCATE _cursor
GO

EXEC usp_GetPersonList 'T0001'
GO
```

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated.png)

### 使用變數合併結果

上面的迴圈方法，也可以直接使用一個變數來累加結果，可以更簡便。  
```sql
DECLARE @PersonList varchar(MAX)

SELECT @PersonList = COALESCE(@PersonList + ',' , '') + Person
FROM Tickets WHERE TicketID='T0001'

SELECT @PersonList
```

- 上面用到 [COALESCE](http://msdn.microsoft.com/zh-tw/library/ms190349.aspx)，只是為了方便處理第一個逗號而已。

![](http://127.0.0.1:8080/_images/cs/sql-COALESCE.png)

### 使用 FOR XML Path

- [FOR XML](https://msdn.microsoft.com/zh-tw/library/ms178107.aspx) ：以資料列集（rowset）的形式取得查詢結果。

1. 首先透過 [FOR XML](https://msdn.microsoft.com/zh-tw/library/ms178107.aspx) 子句，可以將結果回傳成單一的 XML 格式。
```sql
SELECT (',' + Person) 
FROM Tickets 
WHERE TicketID='T0001'
FOR XML AUTO('')
```

![](http://127.0.0.1:8080/_images/cs/sql-for-xml-1.png)

2. 若是要取得所有群組。
```sql
SELECT B.TicketID, 
(
SELECT (',' + Person) 
FROM Tickets A
WHERE A.TicketID=B.TicketID
FOR XML PATH('') 
)
FROM Tickets B	
GROUP BY TicketID
```

![](http://127.0.0.1:8080/_images/cs/sql-for-xml-2.png)

3. 修飾第一個逗號。

使用 [STUFF](http://msdn.microsoft.com/zh-tw/library/ms188043.aspx) 將第一個字取代掉。
```sql
SELECT B.TicketID, 
STUFF((
SELECT (',' + Person) 
FROM Tickets A
WHERE A.TicketID=B.TicketID
FOR XML PATH('') 
) , 1, 1, '')
FROM Tickets B	
GROUP BY TicketID
```

![](http://127.0.0.1:8080/_images/cs/sql-for-xml-3.png)

## 如何將逗號間隔資料分成不同資料列

如果有一資料如下：  
```sql
CREATE  TABLE TicketList (
[TicketID] char(5) NOT NULL,
[PersonList] nvarchar(max) NOT NULL
)

INSERT INTO TicketList VALUES
('T0001', 'Alice,Bob'),
('T0002', 'Catherine,Doug,Elaine')
```

想拆成以下結果：  

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated-2.png)

查詢語法：  
```sql
;WITH tmp(TicketID, Person, PersonList) as (

select TicketID, LEFT(PersonList, CHARINDEX(',',PersonList+',')-1),
STUFF(PersonList, 1, CHARINDEX(',',PersonList+','), '')
from TicketList

union all

select TicketID, LEFT(PersonList, CHARINDEX(',',PersonList+',')-1),
STUFF(PersonList, 1, CHARINDEX(',',PersonList+','), '')
from tmp
where PersonList > ''

)

select TicketID, Person
from tmp
order by TicketID
```

結果：  

![](http://127.0.0.1:8080/_images/cs/sql-comma-separated-2.png)