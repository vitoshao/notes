---
title: Trigger
layout: default
parent: StoredProc、Triggers、Views
nav_order: 5
description: "Trigger"
date: 2013-05-17
tags: [SQL,StoredProc、Triggers、Views]
postid: "4051464632706251731"
---
TRIGGER 是一種特殊的預存程序，雖然也是由使用者自訂的可程式化物件，但是它不可以直接被使用者執行。  它必須建構在 table 或 view 的特定事件中，如：INSERT, UPDATE, DELETE。  當這些事件發生時，才會自動引發 TRIGGER 執行。  

# DML 觸發程序（DML Triggers）

[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)是必須透過 **table** 或 **view** 來建立，並且定義要引發 Trigger 的事件：INSERT, UPDATE, DELETE。  引發 Triggers 的陳述式和 Triggers 本身會被視為單一交易處理，而這樣的交易可以從觸發程序內部Rollback。例如偵測到伺服器錯誤時(例如，磁碟空間不足)，整個交易就會自動Rollback。  所以[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)可執行類似條件約束的功能，讓資料保持完整性。  

## 建立 DML 觸發程序

#### 建立觸發程序

要建立觸發程序，要使 [CREATE TRIGGER](http://technet.microsoft.com/zh-tw/library/ms189799.aspx) 陳述式。
```sql
CREATE TRIGGER trigger_name　ON { table | view }
[ WITH <dml_trigger_option> [ ,...n ] ]
{ FOR | AFTER | INSTEAD OF }
{ [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] }
[ WITH APPEND ]
[ NOT FOR REPLICATION ]
AS { sql_statement [ ; ] [ ,...n ] | EXTERNAL NAME <method specifier [ ; ] > }
```

#### 參數說明：

- **table | view**  
這是執行[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)的資料表或檢視，有時稱為觸發程序資料表或觸發程序檢視。
- **FOR | AFTER | INSTEAD OF**  

- FOR ：同等 AFTER 。
- AFTER ：只在觸發的 SQL 陳述式指定的所有作業都執行成功時，才引發[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)。檢視不能定義 AFTER 觸發程序。
- INSTEAD OF ：指定以[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)來取代觸發的 SQL 陳述式，因此，會覆寫觸發陳述式的動作。
- **[ [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) ] [ , ] [ [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) ] [ , ] [ [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) ]**  
設定要激發[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)的事件。
- **NOT FOR REPLICATION**  
複寫代理程式修改觸發程序所涉及的資料表時，不應執行觸發程序。

## 刪除、停用、啟用 DML 觸發程序
```sql
--刪除 DML 觸發
DROP TRIGGER [trg_EncryptPwd];

--停用 DML 觸發
DISABLE TRIGGER [trg_EncryptPwd] ON [tblUser];

--啟用 DML 觸發
ENABLE TRIGGER [trg_EncryptPwd] ON [tblUser];
```

## DML 觸發程序的種類

SQL Server 支援二種觸發程序：AFTER TRIGGER 及 INSTEAD OF TRIGGER 。

### AFTER 觸發程序

AFTER 觸發程序會在 INSERT, UPDATE, DELETE, [MERGE](http://msdn.microsoft.com/zh-tw/library/bb510625.aspx) 等指令正確執行完成後才會發引發。  若上述指令執行過程中有任何違反條件約束的行為， AFTER triggers 就不會被引發。  

所以，VIEW 就不能定義 AFTER 觸發程序。  

底下範例是一個作用在 tblGrade 資料表上的 AFTER Trigger 。  當完成 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 或 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 或 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 指令後，就會引發 Trigger 。  

- 你可以在 Trigger 中籍由 deleted 虛擬資料表取得異動前(或刪除)的資料 。
- 也可以在 Trigger 中籍由 inserted 虛擬資料表取得異動後(或新增)的資料 。
```sql
CREATE TRIGGER trg_tblGrade_after ON tblGrade
AFTER INSERT, UPDATE, DELETE
AS
DECLARE @OriginalData XML; 
DECLARE @NewData XML;
DECLARE @Operation char(1);
SET @OriginalData=''; 
SET @NewData='';

--Update ( deleted + inserted )
IF EXISTS(SELECT 1 FROM deleted) AND EXISTS(SELECT 1 FROM inserted)
BEGIN	 
SET @OriginalData = ( SELECT \* FROM deleted FOR XML RAW('GradeTable') ) 
SET @NewData	  = ( SELECT \* FROM inserted FOR XML RAW('GradeTable') ) 
END

--Insert ( inserted )
IF EXISTS(SELECT 1 FROM inserted) AND NOT EXISTS(SELECT 1 FROM deleted)   
BEGIN	 
SET @NewData	  = ( SELECT \* FROM inserted FOR XML RAW('GradeTable') ) 
END

--Delete ( deleted )
IF EXISTS(SELECT 1 FROM deleted) AND NOT EXISTS(SELECT 1 FROM inserted)
BEGIN	 
SET @OriginalData = ( SELECT \* FROM deleted FOR XML RAW('GradeTable') ) 
END

INSERT INTO Log(TableName, OriginalData, NewData) VALUES ('GradeTable', @OriginalData, @NewData)
```

### INSTEAD OF 觸發程序

INSTEAD OF 觸發程序會以觸發程序中的 TSQL 來取代原觸發的 TSQL ，也就是會覆寫觸發陳述式的動作。  

底下範例是一個作用在 tblGrade 資料表上的 INSTEAD OF [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) Trigger 。  當執行 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) tblGrade 指令完成時，就會引發 Trigger ，但是實際上，並不會新增資料到 tblGrade 資料表。  然而，你仍然可以在 Trigger 中，籍由 inserted 或 deleted 虛擬資料表取得異動的資料，再將新的資料做其他的處理。  
```sql
CREATE TRIGGER trg_tblGrade ON tblGrade
INSTEAD OF INSERT
AS
INSERT INTO tblGrade2( StudentNo, ClassID, Grade ) 
SELECT StudentNo, ClassID, Grade FROM inserted
```
```sql
INSERT tblGrade(StudentNo, ClassID, Grade) Values (2, 2,60);

select \* from tblGrade
select \* from tblGrade2
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhy5mL7tEgpZzk-Ewb5doeYTKtXIxpib37RWQgDHibHFt3kFi2Z_xsp6jYpRcKpPGYuiI9RuOB98XbyJYgGhE8LmqQByy5n_hqs9XRXg4H4ChbMzFlxtRqKO-qISdDyRjSN9W18Ar4M_k8/s0/sql-instead-of-insert-trigger.png)

#### 運用

INSTEAD OF 觸發程序有不少運用的時機，例如：

- 如果要設計一個資料表，其中某個欄位要符合多個 FK ，或者欄位值要用 TSQL 再加以判斷是否合法，這時候就可以使用 INSTEAD OF 觸發程序。
- 或者，若使用**不可更新檢視表**，因為無法直接更新資料，這時候也可以在檢視表表建立 INSTEAD OF 觸發程序，來達到必要的功能。
- 或者，針對某個 TABLE ，你僅允許特定欄位可以被更新。

#### 限制

- **可更新檢視表**不允許 INSTEAD OF 觸發程序
- **DDL Trigger** 或 **Logon Trigger** 不能指定 INSTEAD OF。

## inserted and deleted tables

- deleted 資料表中會儲存被 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 及 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 陳述式所影響的資料副本。
- inserted 資料表中會儲存被 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 及 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 陳述式所影響的資料副本。

當觸發 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 事件時，會將新增的資料列內容複製到 inserted 虛擬資料表；  
當觸發 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 事件時，會將新的資料內容複製到 inserted 虛擬資料表，同時將舊的資料列內容複製到 deleted 虛擬資料表；  
當觸發 [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 事件時，會將刪除前的資料列內容複製到 deleted 虛擬資料表。

## 巢狀 AFTER 觸發程序（Nested AFTER Triggers）

若 TableA 有個 AFTER Trigger 會去更改 TableB 中的資料，而 TableB 也有個 Trigger 會去更改 TableA 中的資料，這種情況稱為巢狀 AFTER 觸發程序。  巢狀 AFTER 觸發程序最多只會執行 32 次，之後就會停止。  

巢狀觸發程序的執行次數，是一個可設定值，只要不超過32即可。  
```sql
EXEC sp_configure 'nested triggers';        --查詢設定

EXEC sp_configure 'nested triggers', 0;     --設定
RECONFIGURE;
```

## 測試特定資料行的 UPDATE 或 INSERT 動作

您可以設計一個 Transact-SQL 觸發程序來執行以特定資料行之 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 或 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 修改為基礎的特定動作。請在觸發程序的主體中，利用 [UPDATE()](http://technet.microsoft.com/zh-tw/library/ms187326.aspx) 或 [COLUMNS_UPDATED](http://technet.microsoft.com/zh-tw/library/ms186329.aspx) 來完成這個目的。 [UPDATE()](http://technet.microsoft.com/zh-tw/library/ms187326.aspx) 會測試單一資料行所嘗試的 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 或 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 。 [COLUMNS_UPDATED](http://technet.microsoft.com/zh-tw/library/ms186329.aspx) 會測試多個資料行所執行的 [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) 或 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 動作，且會傳回一個位元模式來指出插入或更新了哪些資料行。 
```sql
--判斷 RetailPrice 或 WholeSalePrice 欄位值是否有被異動
CREATE TRIGGER TrgPriceChange ON Products FOR UPDATE AS
IF UPDATE(RetailPrice) OR UPDATE(WholeSalePrice)
-- Create Audit Records
```

## 範例

### 範例１：

下面範例示範由[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)來同步另外一個 table 中的資料
```sql
IF OBJECT_ID ('dbo.trig_AddEmp', 'TR') IS NOT NULL
DROP TRIGGER dbo.trig_AddEmp;
GO

CREATE TRIGGER trig_AddEmp ON Emp
AFTER UPDATE, INSERT, DELETE   
AS 
BEGIN
DECLARE @Operation char(1) = '';

IF EXISTS(SELECT 1 FROM inserted) AND NOT EXISTS(SELECT 1 FROM deleted)   
SET @Operation = 'I'		--Insert   

IF EXISTS(SELECT 1 FROM inserted) AND EXISTS(SELECT 1 FROM deleted)  
SET @Operation = 'U'		--Update  

IF NOT EXISTS(SELECT 1 FROM inserted) AND EXISTS(SELECT 1 FROM deleted)  
SET @Operation = 'D'		--Delete 

IF (@Operation = 'I') 
BEGIN
INSERT Emp2 SELECT \* FROM inserted
END
IF (@Operation = 'U') 
BEGIN
DELETE Emp2 WHERE EmpNo = (SELECT EmpNo FROM deleted)
INSERT Emp2 SELECT \* FROM inserted
END
IF (@Operation = 'D') 
BEGIN
DELETE Emp2 WHERE EmpNo = (SELECT EmpNo FROM deleted)
END		
END
```

### 範例２：

上面範例，每次僅能更新一筆資料列，若要支援大量更新刪除的狀況，必須調整如下：
```sql
IF (@Operation = 'I') 
BEGIN
INSERT Emp2 SELECT \* FROM inserted
END
IF (@Operation = 'U') 
BEGIN
DELETE Emp2 WHERE EmpNo IN (SELECT EmpNo FROM deleted)
INSERT Emp2 SELECT \* FROM inserted
END
IF (@Operation = 'D') 
BEGIN
DELETE Emp2 WHERE EmpNo IN (SELECT EmpNo FROM deleted)
END	
```

# DDL 觸發程序（DDL Triggers）

前面提及的[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)用來回應作用在 **table** 或 **view** 上的 [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 命令。  而[DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)是一種特殊類型的觸發程序，它會在 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 陳述式執行時引發，大多用來執行資料庫中的管理工作，例如稽核或管理資料庫作業等。  [DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)可回應各種 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 陳述式所引發的事件，這些事件包括 CREATE 、 ALTER 、 DROP 、 [GRANT](http://msdn.microsoft.com/zh-tw/library/ms187965.aspx) 、 [DENY](http://msdn.microsoft.com/zh-tw/library/ms188338.aspx) 、 [REVOKE](http://msdn.microsoft.com/zh-tw/library/ms186308.aspx) 或 [UPDATE STATISTICS](http://msdn.microsoft.com/zh-tw/library/ms187348.aspx) 關鍵字開頭的 TSQL 陳述式。   

在操作[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)時，我們會透過 inserted 和 deleted 這二個特殊資料表以取得需要的資料。  但是[DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)並不會產生這二個特殊資料表，如要取得異動的相關資訊可以透過 [EVENTDATA](http://technet.microsoft.com/zh-tw/library/ms173781.aspx) 這個函式取得。   

#### 定義
```sql
CREATE TRIGGER trigger_name ON { ALL SERVER | DATABASE } 
[ WITH <ddl_trigger_option> [ ,...n ] ]
{ FOR | AFTER } { event_type | event_group } [ ,...n ]
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier >  [ ; ] }
```

#### 參數說明

1. ALL SERVER | DATABASE  

- ALL SERVER：將 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 觸發程序的範圍套用在目前伺服器上，也就是套用到所有資料庫上。
- DATABASE：將 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 觸發程序的範圍套用在目前資料庫上。
2. FOR | AFTER  
[DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)或[Logon 觸發程序](http://technet.microsoft.com/zh-tw/library/bb326598.aspx)不能指定 INSTEAD OF。
3. event\_type | event\_group  

- event\_type   
[DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)只有 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) , [UPDATE](http://technet.microsoft.com/zh-tw/library/ms177523.aspx) , [DELETE](http://technet.microsoft.com/zh-tw/library/ms189835.aspx) 三種 event\_type ，但是[DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)的觸發事件就多了。              如：CREATE\_TABLE、DROP\_TABLE、ALTER\_TABLE、CREATE\_INDEX、CREATE\_VIEW、DROP\_USER 等，              這些觸發程序有些僅適用在資料庫範圍，有些僅適用在伺服器範圍，更多詳情可參考＜[DDL 事件](http://technet.microsoft.com/zh-tw/library/bb522542.aspx)＞。
- event\_group  
Event types roll up within a command hierarchy called event groups.

## 範例一：Log 誰執行了 DROP\_TABLE, ALTER\_TABLE, CREATE\_TABLE

下面範例示範由 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) Trigger 來記錄資料表的 CREATE, ALTER, DROP 事件。
```sql
CREATE TRIGGER trig_MoniterTables ON DATABASE　　--套用在目前資料庫
FOR DROP_TABLE, ALTER_TABLE, CREATE_TABLE
AS
DECLARE @user nvarchar(50);
SELECT @user = SYSTEM_USER;
INSERT DbEventTrack(UserName,EventMsg) Values(@user,cast(EVENTDATA() as nvarchar(max)))

--DROP
DROP TRIGGER trig_MoniterTables ON DATABASE
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhhhxe8fyBRH-WvnR2Pl6TpK60o4kNAYa4jvTHxaX3gU5MCj-hKlOC_AL7GiPSR0FcWm60HGqqGmFyUfmU25BsiQ9FNf5ZLppj1TsZiayxPc3o8Exeg53j1gTpaAClRFqmLBsaGmGb0V8E/s0/sql-db-trigger.png)

## 範例二：針對所有資料庫做 Log

如果你想套用 DLL 觸發程序到整個 SQL Server 上，例如上一例中的範例，若你想監測所有資料庫的資料表異動狀況，  除了在建立觸發程序時必須使用 **ALL SERVER** 選項外，那麼該 DLL 觸發程序建立時，就會被歸屬在伺服器物件之中。  同時，你還必須將用來存放資訊的資料表 DbEventTrack 改建在 master 資料庫，以便該觸發程序存取使用。  
```sql
CREATE TRIGGER [trig_MoniterTables] ON All SERVER  --套用到所有資料庫
FOR DROP_TABLE, ALTER_TABLE, CREATE_TABLE
AS 
DECLARE @DatabaseName sysname = 
EVENTDATA().value('(/EVENT_INSTANCE/DatabaseName)[1]','nvarchar(50)');  

DECLARE @PostTime datetime = 
EVENTDATA().value('(/EVENT_INSTANCE/PostTime)[1]','datetime');  

DECLARE @LoginName sysname = 
EVENTDATA().value('(/EVENT_INSTANCE/LoginName)[1]','sysname');  

DECLARE @TSQLCommand nvarchar(max) = 
EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nvarchar(max)');  

INSERT DbEventTrack(UserName,DBName,TSQLCommand,PostTime) 
Values(@LoginName,@DatabaseName,@TSQLCommand,@PostTime)
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVz2oa_92mM27luREOo7OzRY3OFZJLR-fnU8KWDZquxUzlP2lXkRMHoOe1Qq3Zmyb2J1uIMy55IJ3FTIAoGjwLZLt5g1w-C_YxpI1SGBadSBOKW579pdPZ02EW8ifkx1AkR1_rqUYfRhI/s0/sql-db-trigger-all-server.png)

## EVENTDATA function

當使用 [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 觸發程序時，我們可以在 deleted 或 inserted 虛擬資料表中取得必要的相關資訊。  如果使用 [DDL](http://msdn.microsoft.com/zh-tw/library/ff848799.aspx) 觸發程序，你應該會想知道那些內容被什麼人異動了，這些相關資訊就可以透過 [EVENTDATA](http://technet.microsoft.com/zh-tw/library/ms173781.aspx) 這個函式取得。  

上面範例中，就有使用 [EVENTDATA](http://technet.microsoft.com/zh-tw/library/ms173781.aspx) 函式取得一個 XML 型態的資訊，這個 XML 內容如下：  
```xml
<EVENT_INSTANCE>
<EventType>ALTER_TABLE</EventType>
<PostTime>2013-05-23T11:51:21.020</PostTime>
<SPID>56</SPID>
<ServerName>VITO-2011W7\W7_SQLSVR_2008</ServerName>
<LoginName>sa</LoginName>
<UserName>dbo</UserName>
<DatabaseName>test</DatabaseName>
<SchemaName>dbo</SchemaName>
<ObjectName>TableB</ObjectName>
<ObjectType>TABLE</ObjectType>
<TSQLCommand>
<SetOptions ANSI_NULLS="ON" ANSI_NULL_DEFAULT="ON" ANSI_PADDING="ON" QUOTED_IDENTIFIER="ON" ENCRYPTED="FALSE"/>
<CommandText>ALTER TABLE dbo.TableB SET (LOCK_ESCALATION = TABLE);</CommandText>
</TSQLCommand>
</EVENT_INSTANCE>
```

如果要取得其中項目的資料，可以透過 [XQuery](http://technet.microsoft.com/zh-tw/library/ms190262.aspx) 的 [value()](http://msdn.microsoft.com/en-us/library/ms178030.aspx) 方法取得，如下範例：  
```sql
CREATE TRIGGER trig_MoniterTables ON DATABASE 
FOR DROP_TABLE, ALTER_TABLE, CREATE_TABLE
AS 
DECLARE @DatabaseName sysname = 
EVENTDATA().value('(/EVENT_INSTANCE/DatabaseName)[1]','nvarchar(50)');  

DECLARE @PostTime datetime = 
EVENTDATA().value('(/EVENT_INSTANCE/PostTime)[1]','datetime');  

DECLARE @LoginName sysname = 
EVENTDATA().value('(/EVENT_INSTANCE/LoginName)[1]','sysname');  

DECLARE @TSQLCommand nvarchar(max) = 
EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nvarchar(max)');  

INSERT DbEventTrack(UserName, DBName, TSQLCommand, PostTime) 
Values(@LoginName, @DatabaseName, @TSQLCommand, @PostTime)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfzjpuswQeHW07zCI-FDhZC4BuBwgc0eUMdZ-P59vx2tekz6ktarTVu94jxZ7_6dDFIDt_uGYmvN6PX6jugy6ZndhlVdogpjE77Fh81VF6Qvz5sZWBvuaBlB_Fhwx2E6mJhVN11BUNeE8/s0/sql-ddl-trigger-result.png)

# Logon 觸發程序（Logon Triggers）

- [Logon 觸發程序](http://technet.microsoft.com/zh-tw/library/bb326598.aspx)會用來回應使用者工作階段建立時所引發的 LOGON 事件。
- Logon triggers are fired after authentication succeeds but before the user session is actually established.
- You can execute a ROLLBACK statement within a logon trigger, the connection to the instance terminates.
- You **cannot** return any messages to a user from within a logon trigger.
```sql
CREATE TRIGGER trigger_name ON ALL SERVER 
[ WITH <logon_trigger_option> [ ,...n ] ]
{ FOR| AFTER } LOGON  
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier >  [ ; ] }
```
## 參考資料  

- [CREATE TRIGGER (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms189799.aspx)
- [DML 觸發程序](http://technet.microsoft.com/zh-tw/library/ms178110.aspx)
- [DDL 觸發程序](http://technet.microsoft.com/zh-tw/library/ms190989.aspx)
- [DDL 事件](http://technet.microsoft.com/zh-tw/library/bb522542.aspx)
- [Logon 觸發程序](http://technet.microsoft.com/zh-tw/library/bb326598.aspx)