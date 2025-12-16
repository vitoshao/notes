---
title: Stored Procedures
layout: default
parent: StoredProc、Triggers、Views
nav_order: 2
description: "Stored Procedures"
date: 2013-05-17
tags: [SQL,StoredProc、Triggers、Views]
postid: "5586473009653088362"
---
The first time that a stored procedure is accessed, SQL Server generates compile and execution plans that are stored in the query cache and reused for subsequent executions.  Therefore, you can receive a slight performance benefit when using a stored procedure by avoiding the need to parse, compile, and generate a query plan on subsequent executions of a stored procedure.  

# 簡單範例

#### 建立預存程序
```sql
CREATE PROCEDURE uspEmp 
@EmpNo int, 
@DepNo int 
AS 
SELECT EmpNo,EmpName,E.DepNo,DepName
FROM Emp E
INNER JOIN Dep D On E.DepNo=D.DepNo
```

#### 執行預存程序
```sql
Exec uspEmp 1,1
```

#### 刪除預存程序
```sql
DROP PROCEDURE uspEmp
```

#### 修改預存程序
```sql
ALTER PROCEDURE uspEmp 
@EmpNo int, 
@DepNo int 
AS 
SELECT EmpNo,EmpName,E.DepNo,DepName
FROM Emp E
INNER JOIN Dep D On E.DepNo=D.DepNo
```

#### 查看預存程序內容
```sql
EXEC sp_helptext uspEmp;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9EXB31Q7Qm58cYKg8ZBhyI-Xnoo0uU0_pgBCeeDo1yJBSoFVcpHq21PCP4-CY3pxJhrPzBBibTYS9qpdrUmKMkegu4YarVn4uApVsxWj4ZCF3U2bUJxWrWjb6hQbvOiv7zBvOAS4xHTU/s0/sql-sp_helptext.png)

#### 檢測預存程序是否存在
```sql
IF OBJECT_ID('dbo.uspAddEmp', 'P') IS NOT NULL
--DROP PROC dbo.uspAddEmp;
PRINT 'EXIST'
```

#### BEGIN/END

BEGIN/END 這二個關鍵字用來包住程式碼的區塊，可用可不用，只是讓程式碼看起來更明確。

#### SET NOCOUNT ON/OFF

當執行預存程序結束時，預設都會回傳受影響的資料列數，你可以使用 SET NOCOUNT OFF 關閉這個功能。ON 則啟用這個功能。

# Variables, Parameters, and Return Codes

使用 [CREATE PROCEDURE](http://msdn.microsoft.com/zh-tw/library/ms187926.aspx)  陳述式建立預存程序，基本語法定義如下：
```sql
CREATE PROCEDURE <procedure name>
[參數列]
[WITH ENCRYPTION | RECOMPILE | EXECUTE AS Clause]
AS 
程式碼區塊
```

## Variables

SQL Server has two types of variables: local and global .   A local variable is designated by a single at sign (@) while a global variable is designated by a double at sign (@@).   In addition, you can create, read, and write local variables, but you can only read the values from global variables.  

### Local Variables

#### 宣告

要定義變數必須使用 [DECLARE](http://msdn.microsoft.com/zh-tw/library/ms188927.aspx) 子句，變數之間可以用逗號間隔。  但是如果為 TABLE 型別，必須獨立定義。  
```sql
DECLARE @intvariable INT,　@datevariable DATE,
@spatialvar GEOGRAPHY,
@levelvar HIERARCHYID

DECLARE @tablevar TABLE
(
ID INT NOT NULL,
Customer VARCHAR(50) NOT NULL
)
```

#### 配置

要 assign 值給變數，可以使用 SET 或 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 子句。  但是，若要由查詢指令中取得，就只能使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 子句。  
```sql
declare @EmpName nvarchar(50)

select @EmpName = 'abc'
set @EmpName = '123'
select @EmpName=EmpName FROM Emp WHERE EmpNo=1 --若回傳多列，變數值會是最後一筆的值
```

針對四則運算式，SQL2008 增加了以下方法：
```sql
declare @int int
set @int=0
set @int += 1 
set @int \*= 2 
set @int /= 2 

declare @str varchar(100) =''
set @str += 'abc' 
set @str += '123' 
```

### Global Variables

- [@@ERROR](http://technet.microsoft.com/zh-tw/library/ms188790.aspx) ：Error code from the last statement executed
- [@@IDENTITY](http://msdn.microsoft.com/zh-tw/library/aa226159.aspx) ：Value of the last identity value inserted within the connection
- [@@ROWCOUNT](http://technet.microsoft.com/zh-tw/library/ms187316.aspx) ：The number of rows affected by the last statement
- [@@TRANCOUNT](http://msdn.microsoft.com/zh-tw/library/ms187967.aspx) ：The number of open transactions within the connection
- [@@VERSION](http://msdn.microsoft.com/zh-tw/library/ms177512.aspx) ：The version of SQL Server

#### @@IDENTITY

這個變數要小心取用，它是全域變數，所以任何 Insert 行為都有可能影響它的值。  例如，若某個 SP 會新增一筆 TableA 的資料，如果 TableA 的 trigger 還會去新增 TableB, TableC ，  而且這三個資料表都包含識別欄位，此時如果在 SP 使用 [@@IDENTITY](http://msdn.microsoft.com/zh-tw/library/aa226159.aspx) 取得的值，將會是最後一次 Insert 指令返回的識別碼。  若僅想要取得該 SP 範圍中的變數值，可以改用 SCOPE\_IDENTITY() 函式。  
```sql
CREATE PROCEDURE [dbo].[uspAddEmp]
@EmpName nvarchar(50)
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
--SELECT @@IDENTITY;
SELECT SCOPE_IDENTITY();
```

其實取得插入的識別值共有三個方法，每個函數定義的範圍(Scope)各有不同：  

- [IDENT_CURRENT](http://technet.microsoft.com/zh-tw/library/ms175098.aspx) ：會傳回在任何工作階段和任何範圍中，產生給特定資料表 (叫用函數時指定) 的最後一個識別值。
- [SCOPE_IDENTITY](http://technet.microsoft.com/zh-tw/library/ms190315.aspx) ：會傳回在目前工作階段以及目前範圍中，任何資料表產生的最後一個識別值。
- [@@IDENTITY](http://msdn.microsoft.com/zh-tw/library/aa226159.aspx) ：會傳回所有範圍的目前工作階段中，任何資料表所產生的最後一個識別值。

## Parameters

Stored Procedure 的參數都是 Local 變數，依性質可分成二類： INPUT 和 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 。 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 參數是用來回傳一個值給應用程式時使用。
```sql
CREATE PROCEDURE [dbo].[uspAddEmp2]
@EmpName nvarchar(50),
@EmpNo int OUTPUT
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
SELECT @EmpNo = @@IDENTITY;
```

你也可以使用帶預設值的參數，若叫用時該參數沒有傳入值就會使用預設值。
```sql
CREATE PROCEDURE [dbo].[uspAddEmp3]
@EmpName nvarchar(50) ,
@DepNo int = 1
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, @DepNo) 
SELECT @@IDENTITY;
```

## Return Codes

要回傳資料給叫用端，有幾下幾種方式：

1. 在最後一行使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 語法回傳表格資料
2. 使用輸出參數(Output Parameter)的方式回傳資料
3. 使用 [RETURN](http://technet.microsoft.com/zh-tw/library/ms174998.aspx) 語法回傳一個**整數型別**(Int32)的狀態碼
```sql
CREATE PROCEDURE [dbo].[uspEmp]
@EmpNo int   
AS 
SELECT \*
FROM Emp E INNER JOIN Dep D On E.DepNo=D.DepNo
WHERE E.EmpNo= @EmpNo
```
```sql
CREATE PROCEDURE [dbo].[uspAddEmp_O]
@EmpName nvarchar(50),
@EmpNo int OUTPUT
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
SET @EmpNo = @@IDENTITY;

--叫用時

declare @No int
exec uspAddEmp_O 'vito', @No out
print @No
```
```sql
CREATE PROCEDURE uspAddEmp4
@EmpName nvarchar(50)
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
RETURN @@IDENTITY;    --return 只能回傳整數型別，不能回傳其他型別。

--叫用時

DECLARE @No int
EXEC @No = uspAddEmp4 'vito'
PRINT @No
```

若由 ADO.NET 叫用預存程序，相關的配套方法可參考[保哥的文章](http://blog.miniasp.com/post/2008/12/09/How-to-get-Stored-Procedure-return-value-using-ADONET.aspx)。

## 預存程序群組
```sql
CREATE PROCEDURE sp_GetUser;1
AS
SELECT UserId,UserName,AliasName FROM tblUser WHERE Sex='0001'

CREATE PROCEDURE sp_GetUser;2
AS
SELECT UserId,UserName,AliasName FROM tblUser WHERE Sex='0002'
GO

EXEC sp_GetUser;1
EXEC sp_GetUser;2
```

# Control Flow Constructs

在預存程序中，有以下的流程語法可以使用：

- IF ... ELSE
- BEGIN. . .END
- WHILE
- BREAK / CONTINUE
- WAITFOR
- GOTO
- RETURN

### IF ... ELSE
```sql
IF (@EmpName='VITO')
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
ELSE
INSERT Emp(EmpName, DepNo) Values (@EmpName, 2) 


IF (@EmpName='VITO')
BEGIN
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
END
ELSE
BEGIN
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
END
```

### WHILE
```sql
DECLARE @var1 int = 1
WHILE( @var1<5 )
BEGIN
PRINT @var1
SET @var1 += 1
END
```

#### BREAK 與 CONTINUE

- BREAK ：用來停止該層迴圈。
- CONTINUE ：用來重新啟動 [WHILE](http://technet.microsoft.com/zh-tw/library/ms178642.aspx) 迴圈，它會忽略 [CONTINUE](http://technet.microsoft.com/zh-tw/library/ms174366.aspx) 關鍵字之後的任何陳述式。
```sql
DECLARE @var1 int = 0

WHILE( @var1<4 )
BEGIN
SET @var1 += 1
PRINT 'loop:top ' + cast(@var1 as char(1))

if @var1=3 
CONTINUE;

PRINT 'loop:bottum ' + cast(@var1 as char(1))
END

--loop:top 1
--loop:bottum 1
--loop:top 2
--loop:bottum 2
--loop:top 3
--loop:top 4
--loop:bottum 4
```

### WAITFOR

[WAITFOR](http://technet.microsoft.com/zh-tw/library/ms187331.aspx) 用來暫停執行，有以下三種使用方法：

#### WAITFOR DELAY

WAITFOR DELAY 用來延遲一段時間再繼續執行
```sql
DECLARE @var1 int = 0

WHILE( @var1<3 )
BEGIN
SET @var1 += 1
PRINT @var1
WAITFOR DELAY '00:00:05';  --暫停5秒
PRINT SYSDATETIME()
END

--1
--2013-05-15 11:39:41.4185893
--2
--2013-05-15 11:39:46.4261981
--3
--2013-05-15 11:39:51.43380
```

#### WAITFOR TIME

WAITFOR DELAY 用來暫停執行，直到指定的時間到了才繼續執行
```sql
PRINT SYSDATETIME()
WAITFOR TIME '11:48:30';  --等到 11:48:30 才執行
PRINT SYSDATETIME()

--2013-05-15 11:48:19.2525309
--2013-05-15 11:48:30.0321498
```

#### WAITFOR RECEIVE

含 receive\_statement 的 [WAITFOR](http://technet.microsoft.com/zh-tw/library/ms187331.aspx) 只適用於 Service Broker 訊息。如需詳細資訊，請參閱＜[RECEIVE (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms187331.aspx)＞

### RETURN

[RETURN](http://technet.microsoft.com/zh-tw/library/ms174998.aspx) 可用來停止預存程式，並返回一個整數型別的狀態值給叫用端的程式。  
```sql
CREATE PROCEDURE [dbo].[uspCheckEmp]
AS 
DECLARE @Num INT;
SELECT @Num = COUNT(\*) FROM Emp

IF (@Num % 2) = 0
RETURN '0'
ELSE
RETURN '1'

-- 叫用

DECLARE @result char(1)
EXEC @result = uspCheckEmp
PRINT @result
```

### GOTO

[GOTO](http://technet.microsoft.com/zh-tw/library/ms180188.aspx) allows you to pass the execution to a label embedded within the procedure.   Code　constructs such as [GOTO](http://technet.microsoft.com/zh-tw/library/ms180188.aspx) are discouraged in all programming languages.

# Executing Stored Procedures

## 使用 Input 參數

執行預儲程序有以下幾個方法：  
```sql
--無參數
EXEC <stored procedure>

--按位置順序
EXEC <stored procedure> @parm1=<value>, @parm2=<value>,...

--按參數名稱
EXEC <stored procedure> <value>, <value>,...
```

按位置順序較簡單；按參數名稱可避免預存程序的參數因變換順序產生的錯誤。

按位置順序的叫用，預存程序中含預設值的參數必須放在最後面；按參數名稱就沒這個限制。

沒有預設值的參數都必須要傳入。

## 使用 Output 參數

建立預存程序時，參數後面必須使用 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 關鍵字
```sql
CREATE PROCEDURE [dbo].[uspAddEmp_O]
@EmpName nvarchar(50),
@EmpNo int OUTPUT
AS 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
SET @EmpNo = @@IDENTITY;
```

叫用預存程序時，參數值後面也必須使用 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 關鍵字
```sql
declare @No int
exec uspAddEmp_O 'vito', @No OUTPUT
print @No
```

PS. 此處的 [OUTPUT](http://msdn.microsoft.com/zh-tw/library/ms177564.aspx) 可以使用縮寫 OUT 。

## Dynamic Execution

有時候陳述式必須到了執行階段才有辨法組成，這時候就必須採用動態執行的技巧。  

#### 執行語法：
```sql
EXEC(<command>) 
--或
sp_executesql <command>
```

**範例**：
```sql
EXEC('SELECT OrderID, CustomerID FROM Sales.SalesOrderHeader WHERE OrderID = 1')
GO

DECLARE @var VARCHAR(MAX)
SET @var = 'SELECT OrderID, CustomerID FROM Sales.SalesOrderHeader WHERE OrderID = 1'
EXEC(@var)
```

# 模組執行內容（Modules Execution Context）

「模組」（modules）是指 function, procedures, queues, trigger 這些功能。   「執行內容」則是指目前的工作階段（session），它是由一對安全性 Token 來表示：Login Token 和 User Token。  若一個 Login Token 有多個資料庫存取權，則這個 Login Token 會有多個 User Token 。  

## 自訂模組的執行內容

[EXECUTE AS](http://technet.microsoft.com/zh-tw/library/ms188354.aspx) 子句可用來設定下列模組的執行內容：函數、程序、佇列和觸發程序。  
```sql
CREATE PROCEDURE [ProcA]([參數列])
WITH EXECUTE AS { CALLER | SELF | OWNER | 'user_name' } 
AS
BEGIN
.....
END
```

- CALLER：指定模組內的陳述式，以模組的**呼叫者**為執行內容。
- SELF：指定模組內的陳述式，以模組的**建立者**為執行內容。
- OWNER：指定模組內的陳述式，以模組的**擁有者**為執行內容。
- user\_name：指定模組內的陳述式，以**指定的使用者**為執行內容。

在舊版的 SQL Server 中，在這些模組中一律只能在**模組的呼叫者**的內容中執行。  例如，若 User1 對預存程序 procedureA 有執行權，在過去，在這個預存程序中的指令就不能超越 User1 的權限，否則就會發生錯誤。  

從 SQL 2005 開始，透過 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 指定執行模組，執行內容可以從**模組的呼叫者**切換到**模組的擁有者**，或切換到**指定的使用者**。  例如，上例中，若在 procedureA 執行了某個動態檢視查詢(DMV)，雖然 User1 沒有這個權限，但是 User2 有，那麼就可以使用 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 來切換到 User2 。  

您可以指定執行模組的內容，來控制 Database Engine 是使用哪一個使用者帳戶，以驗證該模組參考之物件的權限。  此舉可以在使用者自訂模組以及其所參考物件之間的物件鏈結管理權限時，提供更大的彈性和控制權。  您只需要授予模組本身的權限給使用者即可，不必授予使用者參考物件的明確權限。  只有執行模組的使用者，必須具有模組所存取之物件的權限。

## 模擬其他執行內容

當使用者（user）或登入者（login）連接到**工作階段（session）**時，SQL Server 會依據這個資料建立**執行內容（Execution Context）**，直到使用者登出時結束。  工作內容期間的所有作業都是依據該使用者或登入者的權限檢查而定。  

你可以使用 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 來模擬不同工作階段的執行內容。（PS.這裡的[EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx)陳述式和上面的[EXECUTE AS](http://technet.microsoft.com/zh-tw/library/ms188354.aspx)子句是不同的。）
```sql
{ EXEC | EXECUTE } AS <context_specification>

<context_specification>::=
{ LOGIN | USER } = 'name' [ WITH { NO REVERT | COOKIE INTO @varbinary_variable } ] 
| CALLER
```

- LOGIN ：以特定的登入（Login）來模擬工作內容。
- USER ：以特定的資料庫使用者（USER）來模擬工作內容。
- CALLER ：用於模組內部時，指定模組內部的陳述式是在模組呼叫者的內容中執行。 。
- NO REVERT ：設定工作內容切換後不能還原回先前的工作內容。
- COOKIE INTO @var ：設定工作內容切換後，若要還原，只能以特定的工作內容還原

#### 備註

執行內容中的變更會一直持續有效，直到發生下列項目之一為止： 

- 執行另一個 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 陳述式。
- 執行 [REVERT](http://msdn.microsoft.com/zh-tw/library/ms178632.aspx) 陳述式。
- 卸除工作階段。(session is dropped)

#### 權限

若要執行 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 命令，則呼叫者本身要具有 IMPERSONATE 權限。

## 範例一：

下面這個簡單例子便是使用 [EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms181362.aspx) 命令來切換到不同的使用者內容。
```sql
--檢查目前的工作內容
SELECT SUSER_NAME(), USER_NAME();  --sa

DELETE Emp WHERE EmpNo=80;  --sa的權限可以刪除成功

--切換成 vxxxxxxo 工作內容
EXECUTE AS LOGIN = 'vxxxxxxo';

DELETE Emp WHERE EmpNo=81  --由於 vxxxxxxo 對物件 'Emp' 沒有 DELETE 權限，所以失敗。

--還原工作內容
REVERT;

DELETE Emp WHERE EmpNo=82  --刪除成功
```

## 範例二：

若有個預存程序 ProcA ，程式裡面包含了叫用 sys.sql\_logins 系統檢視表，而 sys.sql\_logins 檢視表必須具有 VIEW SERVER STATE 權限才可使用。  若 UserA 只有 ProcA 執行權，沒有 VIEW SERVER STATE 權限，那麼該如何在不變更 UserA 權限下，讓他可以成功執行 ProcA 。  

以下二種方法都可以用來解決這個狀況：  

方法一：

1. 將 ProcA 加入 EXECUTE AS OWNER 陳述式：
2. 變更 ProcA 的 OWNER 為 UserB （UserB 必須有 VIEW SERVER STATE 權限）
```sql
ALTER PROCEDURE ProcA
WITH EXECUTE AS Owner　  --以模組的Owner的工作內容執行
AS
BEGIN
......
END
```

方法二：

1. 找一個(或新增一個)有 VIEW SERVER STATE 權限的使用者 UserB
2. 在 ProcA 中加入 EXECUTE AS UserB
```sql
ALTER PROCEDURE ProcA
AS
BEGIN

EXECUTE AS UserB    --模擬 UserB 的工作內容

......

REVERT;             --還原工作內容
END
```

在方法一中，因為 ProcA 的 OWNER 有 VIEW SERVER STATE 權限，所以在 ProcA 中透過 EXECUTE AS OWNER 指定模組執行時使用模組的 OWNER 執行內容。  

在方法二中，因為 UserB 有 VIEW SERVER STATE 權限，所以在 ProcA 中使用 EXECUTE AS UserB 模擬 UserB 身份執行。

# RECOMPILE

當 Stores Procedure 首次被執行時，SQL Server 會先進行編譯，並建立最佳化的執行計畫，同時將編譯過的命令存放在查詢快取(query cache)。  等下次執行時，就直接執行快取中的程式碼，以提升效率。  

可是如果與該查詢的相關資料結構有被調整過，之前編譯過的查詢就不一定是最適合的，所以必須重新編譯。  要命令 Stores Procedure 進行重新編譯有以下三種方式。  

#### 在預儲程序中使用 [WITH RECOMPILE](http://msdn.microsoft.com/zh-tw/library/ms190439.aspx)

這個方法，預儲程序在每次執行時都會重新編譯，與直接執行 TSQL 沒有差別，無法提升執行效率。
```sql
CREATE PROCEDURE uspAddEmp_RECOMPILE @Name varchar(30)
WITH RECOMPILE
AS
...
```

#### 執行預儲程序時使用 [WITH RECOMPILE](http://msdn.microsoft.com/zh-tw/library/ms190439.aspx)

這個方法只會在該次執行預存程序時進行重新編譯
```sql
exec uspAddEmp_RECOMPILE 'vito2' WITH RECOMPILE
```

#### 使用 [sp_recompile](http://msdn.microsoft.com/zh-tw/library/ms181647.aspx) 指令

這個方法使用指令直接對預存程序進行重新編譯
```sql
sp_recompile uspAddEmp_RECOMPILE
```

# Encryption

[WITH ENCRYPTION](http://msdn.microsoft.com/zh-tw/library/ms187926.aspx) 是用來加密物件用的選項，可以針對 Stored Procedure, Function, Trigger, View 等物件。  若建立預存程序時使用了 [WITH ENCRYPTION](http://msdn.microsoft.com/zh-tw/library/ms187926.aspx) 選項，則 SQL Server 會將預存程序的原始文字轉換為混亂格式(obfuscates)。  而加密後的 SP 雖然可以正常執行，但是無法被修改，也無法被檢視，既使以 SA 的權限也不行。  

要反解這個加密選項，若不想刪除重建，就必須使用[專用管理員連接（Dedicated admin connection，DAC）](http://msdn.microsoft.com/zh-tw/library/ms178068.aspx)，並執行特定的指令。  網路上可以找到 [sp__windbi$decrypt](http://social.msdn.microsoft.com/Forums/zh-TW/sqlserverzhchs/thread/aa71d098-e150-465a-b3ad-c1f881c6ca52) 這個前人寫好的 SP ，就是用來清除加密的功能。  另外也有現成的 SQL Decryptor 解密軟體可用，就請自訂 Google 一下。  

若使用 DAC 有問題產生，可參考以下幾篇文章

- [專用管理員連接(DAC)](http://fecbob.pixnet.net/blog/post/39055959-%E5%B0%88%E7%94%A8%E7%AE%A1%E7%90%86%E5%93%A1%E9%80%A3%E6%8E%A5%28dac%29)
- [SQL Server Express 與 「專用管理員連接(dedicated administrator connection，DAC)」](http://sharedderrick.blogspot.tw/2008/12/sql-server-express-dedicated.html)
- [\[SQL Server\] Decryptoin StoredProcedure(With Encryption)](http://itgroup.blueshop.com.tw/inmarkblue/markshu?n=convew&i=21337)

# 了解參數探測行為

前面提過，使用 SP 時，SQL Server會 reuse 查詢計畫，以提升執行效率。  但是，在某些情況下，reuse 查詢計畫，有時反而會來一些效能低落的問題。  底下說明就是要介紹這些狀況。  

## 什麼是參數探測行為

最佳化工具會在重新啟動 MicrosoftSQL Server 後、首次執行預存程序時自動發生，  這時會將有使用的參數值一同包含在查詢計畫的一部份中，  這樣的作法對大多情況下來說是比較好的，只要SP後續執行都是使用常值，  那基本上都能夠重用執行計畫，但有可能會誤用執行計畫(造成查詢效能成本過高)。  換句話說，假設第一次返回資料量相當龐大(已快取執行計畫)，  執行第二次返回資料量很小但查詢成本(效能)反而不如預期(因為使用了第一次執行計畫)，  這樣就是標準的參數探測行為。

## 範例說明

底下為測試的環境：  
```sql
--create Stored Procedure
CREATE procedure [dbo].[uspGetUser](@Sex varchar(10))
as
SELECT UserID,UserName,Sex,AliasName FROM tblUser WHERE Sex=@Sex	

--create index
CREATE NONCLUSTERED INDEX [IX_tblUser_Sex] ON [dbo].[tblUser] 
(
[Sex] ASC
)
```

資料筆數  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDE-Rh48MdgcDExxnQ7Sdf58B_lN3TFUZ2VzLZ1eKeSpXWDfShz5SMH0c7A5Q9hCsLMVXuiVk2pRbl5mXXx9Tyc4Ji12zKXlgYhY5hwql9juoDhpZwPBYS-1YkEdk0yJtsmgC5VHZ8Aug/s0/sql-test-execute-plan-0.png)

#### 第一次執行SP

第一次執行SP時，SQL Server 會為SP建立執行計畫快取

實際資料列數目：2712  
估計資料列數目：2712  
查詢成本：8.83809  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjd_jC2tqi8vn6ewWV4rUWA_SGOgkRb_6nyf1GhUPyBjUj5KMhcySY_ZXTMqsRscK0hHI9-7bv2mNA59-eGywUDPX2I4CZ8YAXaBoiof9VO_HbfI8QagZA-X7JBgBTVuUDFOqDAQnVC7sA/s0/sql-test-execute-plan-1.png)

#### 第二次執行SP

第二次執行時，由於該SP執行計畫已經快取，所以參數探測行為會直接採用快取中的執行計畫。

實際資料列數目：102  
估計資料列數目：2712  
查詢成本：8.83809  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhC-s6-vJN-1jEol0AZEBW9GtyGjQ92mEOMfdW8dWW6ppHFEmwOZHZt22nhTtmiHfHSEJiH2pS9nm0_YOom-EDShB56f8A6F8zvDarLeo9zi9AcxPW6ACvvfb5u_CSzV527tfawf0cMy8E/s0/sql-test-execute-plan-2.png)

#### 第三次執行SP

第二次執行因為參數探測行為導致誤用了執行計畫(因為快取了第一次執行計畫)，所以本次執行前，先刪除執行計畫快取才再次執行SP。

實際資料列數目：102  
估計資料列數目：102  
查詢成本：0.335455  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMaiN4JD6rvVEHllZ8leak-AoYwGfQdFQLu25eiNtiKzGyDCL7FpWu31ULJOyUXdZi1bWZ10jA-OsZljI2KrInvepN_dXSVgcSWBoyYv2QHRmCGysfDZHkVEe4-QHw52HvOCSMyZcE0jU/s0/sql-test-execute-plan-3.png)

如果要避免參數探測行為，請參考[了解參數探測行為(2)](http://www.dotblogs.com.tw/ricochen/archive/2012/01/03/64153.aspx)
## 參考資料  

- [預存程序 (Database Engine)](http://msdn.microsoft.com/zh-tw/library/ms190782.aspx)
- [\[WILL\]ADO.NET 如何取得 Stored Procedure 的回傳值](http://blog.miniasp.com/post/2008/12/09/How-to-get-Stored-Procedure-return-value-using-ADONET.aspx)
- [流程控制語言](http://technet.microsoft.com/zh-tw/library/ms174290.aspx)
- [了解 Database Engine 錯誤](http://msdn.microsoft.com/zh-tw/library/ms165761.aspx)
- [了解執行內容](http://msdn.microsoft.com/zh-tw/library/ms187096.aspx)
- [了解參數探測行為(1)](http://www.dotblogs.com.tw/ricochen/archive/2012/01/03/64152.aspx)
- [在模組中使用 EXECUTE AS](http://msdn.microsoft.com/zh-tw/library/ms178106.aspx)