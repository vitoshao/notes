---
title: 預存程序的錯誤處理
layout: default
parent: StoredProc、Triggers、Views
nav_order: 3
description: "預存程序的錯誤處理"
date: 2013-05-17
tags: [SQL,StoredProc、Triggers、Views]
postid: "2453609116243954703"
---
# Error Messages

### 錯誤訊息的組成

SQL Server 中的每個錯誤訊息都包含以下幾個元素：  

- 錯誤代碼（Error number）　：每一則錯誤訊息都有唯一的整數值錯誤號碼，範圍由 1 到 49999
- 錯誤層級（Severity level）：嚴重性指出錯誤的嚴重程度。範圍由 0 到 25
- 錯誤訊息（Error message） ：錯誤訊息包含錯誤原因的資訊。訊息長度最大為 255 Unicode 字元.
- 狀態碼（State）           ：範圍由  0 到 127 ，供程式內部使用。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggWjQz-z2EPHGypIcaysHfokwybrou2tUW4CYemQSTo1T8mRvvq49XFPfp_BYpr39Nb9ndqpil5xQHN8UMjCrMX3Da5fYFilgVYNRXqpY5-o1R1AP486tA_MI2xPeG1DFVp45ARn7cOBY/s0/sql-ERROR-1.png)

### 建立自訂錯誤訊息

你也可以自訂錯誤訊息，錯誤代碼必須由 50001 開始，因為 50000 保留給沒有沒有代碼的自訂訊息使用，例如使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 引發的錯誤訊息。  若要加入自訂訊息，可以使用 [sp_addmessage](http://msdn.microsoft.com/zh-tw/library/ms178649.aspx) 預存程序。  
```sql
sp_addmessage 
[ @msgnum = ] msg_id , 
[ @severity = ] severity , 
[ @msgtext = ] 'msg'
[ , [ @lang = ] 'language' ]
[ , [ @with_log = ] 'with_log' ]
[ , [ @replace = ] 'replace' ]
```

- msg\_id：這是訊息的識別碼。使用者定義錯誤訊息的 msg\_id 必須是 50,001 ~ 2,147,483,647 之間的整數。
- severity：這是錯誤的嚴重性層級。有效的層級範圍是 1 ~ 25。
- msg：這是錯誤訊息的文字。  msg 是 nvarchar(255)，預設值是 NULL。
- language：訊息的語言。
- with\_log：訊息出現時，是否將訊息寫入 Windows 應用程式記錄檔中。
- replace：如果指定為 replace 字串，就會以新的訊息文字和嚴重性層級來覆寫現有的錯誤訊息。加入這個參數，等於是要修改錯誤訊息。

#### 備註

- 若要加入自訂錯誤訊息，必須先建立英文版本的訊息，才能建立別的語系版本，否則會出現以下錯誤。  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaDWng7C3ivAV7r9Mp2GBIVTxBwYm9qaE2VBFxwPi6ismUJBd5VE8kn6zIXye8PMX5FR65WsefwvDYYNy9gUMvh4hhL9R5o3u2778mYhak4QXKcAQoYNYpbI2_vSp5Zn1N1Cg8tzzyPWM/s850/sql-sp_addmessage-error.png)
- 若使用參數訊息，  在英語系中，是依順序對應到傳進來的參數。但是在其他語系版本中，使用 (%1!) (%2!) (%3!) 來表示各個參數，可不按順序出現。
```sql
EXEC sp_addmessage 	
@msgnum = 50001, 
@severity = 16,
@msgtext = N'The New Employee (%s) is Exist.',
@lang = 'us_english'

EXEC sp_addmessage 
@msgnum = 50001, 
@severity = 16,
@msgtext = N'新增員工(%1!)已存在',
@lang = '繁體中文'

RAISERROR(50001,1,1, 'vito')
```

#### 查詢所有錯誤訊息

所有的錯誤訊息都存在 [sys.messages](http://msdn.microsoft.com/zh-tw/library/ms187382.aspx) 裡。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfGvqQlQ6rt17jR8coeR4jZF8he_IuoPevnZoiFPENr5-F11R5eug6a5Y3VL3-qfFR4EG1PXkSg4RUJ7dnrUxVpfgV2ZiG6BXdDxPBOYVodAviDVu5OrvLjBkXg3c8hrqC4AnfewsA0HQ/s0/sql-sys-messages.png)

#### 刪除自訂錯誤訊息

- sp\_dropmessage ：刪除自訂錯誤訊息
```sql
sp_dropmessage [ @msgnum = ] message_number 
[ , [ @lang = ] 'language'｜all ]   --all:刪除所有語言版本
```

#### 變更自訂錯誤訊息狀態

- sp\_altermessage ：變更使用者定義狀態或在 SQL Server Database Engine 執行個體中之系統訊息。
```sql
sp_altermessage [ @message_id = ] message_number   ,[ @parameter = ]'write_to_log'
,[ @parameter_value = ]'value' 
```

# Error Handling

## Detecting Errors

### 使用 @@ERROR

當陳述式發生錯誤時，系統會將錯誤代碼存放在 [@@ERROR](http://technet.microsoft.com/zh-tw/library/ms188790.aspx) 這個全域變數。
```sql
DECLARE @EmpName nvarchar(50) = 'vito'
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 

IF @@ERROR <> 0
PRINT 'ERROR(' + cast(@@ERROR as varchar(10)) + '): EmpName is not allowed NULL'
ELSE
PRINT 'INSERT is successful'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfvcO1yBxlr5Hq8hnLvlIGbU0fV_nDl_xHkjWsJnxmlJdXbUOIYFKEbS3Lfa1AFaHG21BHxyPzApw56GGoTDDBfngYqT-9nZjkzOS1KR0DYoT6Lp68xfuKVHHYobSjWutUNXZCpiSLNds/s0/sql-ERROR-2.png)

### 使用 TRY...CATCH

在較早的 SQL 版本，要檢測 TSQL 是否有發生執行錯誤，都只能透過判斷 [@@ERROR](http://technet.microsoft.com/zh-tw/library/ms188790.aspx) 全域變數，直到 SQL2005 才新增了 [TRY...CATCH](http://msdn.microsoft.com/zh-tw/library/ms175976.aspx) 這個結構化的例外處理功能。  它採用和程式語言中的 [TRY...CATCH](http://msdn.microsoft.com/zh-tw/library/ms175976.aspx) 類似的語法，在 TRY 區塊內放的是一般陳述式，CATCH 區塊內放的是錯誤處理的陳述式。  

使用 [TRY...CATCH](http://msdn.microsoft.com/zh-tw/library/ms175976.aspx) 結構，若執行的 TSQL 發生了錯誤，錯誤訊息將不會傳到呼叫端，除非透過 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 再送出錯誤訊息。  

#### 取得錯誤訊息的函式：

當使用 TRY...CATCH 時，你可以**在 CATCH 區塊中**使用以下函式以取得與錯誤訊息相關的資訊：  

- [ERROR_NUMBER](http://msdn.microsoft.com/zh-tw/library/ms175069.aspx) ：發生錯誤的錯誤代碼
- [ERROR_MESSAGE](http://msdn.microsoft.com/zh-tw/library/ms190358.aspx) ：發生錯誤的錯誤訊息
- [ERROR_SEVERITY](http://msdn.microsoft.com/zh-tw/library/ms178567.aspx) ：發生錯誤的錯誤層級
- [ERROR_STATE](http://msdn.microsoft.com/zh-tw/library/ms180031.aspx) ：發生錯誤的錯誤狀態
- [ERROR_PROCEDURE](http://msdn.microsoft.com/zh-tw/library/ms188398.aspx) ：發生錯誤的程序名稱
- [ERROR_LINE](http://msdn.microsoft.com/zh-tw/library/ms178600.aspx) ：發生錯誤的行數
```sql
BEGIN TRY
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
END TRY
BEGIN CATCH
PRINT '錯誤代碼：' + cast(ERROR_NUMBER() as varchar(5)) + char(13) +
'錯誤訊息：' + ERROR_MESSAGE()
RAISERROR('資料寫入錯誤',16,10)
END CATCH; 
```

以上函式，若不是在 CATCH 區塊中使用，都將回傳 NULL 。  

#### 使用 Transation 做到 ACID

上面例子，第一個 [INSERT](http://technet.microsoft.com/zh-tw/library/ms174335.aspx) 陳述式執行成功後，由於 EmpName 為 Uinque 欄位，所以第二行陳述會執行失敗。  雖然攔截了錯誤訊息，但是資料庫卻新增了一筆資料。若我們希望可以做到 ACID 的要求，只要搭配 Transation 來使用即可，如下範例：。  
```sql
BEGIN TRY
BEGIN TRAN
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
COMMIT TRAN
END TRY
BEGIN CATCH
ROLLBACK TRAN
PRINT '錯誤代碼：' + cast(ERROR_NUMBER() as varchar(5)) + char(13) +
'錯誤訊息：' + ERROR_MESSAGE()
RAISERROR('資料寫入錯誤',16,10)
END CATCH; 
```

#### 使用 [TRY...CATCH](http://msdn.microsoft.com/zh-tw/library/ms175976.aspx) 的注意事項

- 當 TRY 區塊內發生錯誤時，程式碼將跳到 CATCH 區塊。
- 若在 TRY 區塊內執行 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 指令，程式碼也會跳到 CATCH 區塊。
- 在 TRY...CATCH　內發生的任何錯誤，包括系統錯誤，都不會回傳給呼叫端。  若要傳出錯誤給呼叫端，必須在 CATCH 區塊內執行 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 指令，以引發一個新的自訂錯誤，才能將錯誤訊息回應給呼叫端。  而且 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 無法自訂錯誤代碼，這個代碼固定為 50000 。

下面例子，若原始的錯誤代碼為 2601 (key值重複)，則我們在 CATCH 中使用上面建立的自訂訊息回傳，否則就以原始的訊息內容回傳。
```sql
CREATE PROCEDURE [dbo].[uspAddEmp_TRY_CATCH]
@EmpName nvarchar(50)
AS 

BEGIN TRY
BEGIN TRAN
INSERT Emp(EmpName, DepNo) Values (@EmpName, 1) 
COMMIT TRAN
END TRY
BEGIN CATCH
ROLLBACK TRAN

declare @err_num integer = ERROR_NUMBER();
declare @err_msg nvarchar(2048) = ERROR_MESSAGE()

if (@err_num=2601)  -- (2601)=>重複的索引鍵
RAISERROR(50001,16,10, @EmpName)        --50001為上頭範例中自訂的錯誤訊息代碼。
else
RAISERROR(@err_msg,16,10)
END CATCH; 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0cvHGJbRWFS40N4nSPx4qNp1qyjpJRLG7nvMsti8gz4WBcNDnfxCyYqP6jUhkH6U3hXVAmZhM4k7pz8NwyN02gB5AJoSdOQjmKx2_txRXGFkhyphenhyphenOkziUeFjmDOUOSJJLEKOLUFTrzGAok/s0/sql-catch-rais-error.png)

## Raising Errors

若要引發自訂的錯誤，可以使用以下方法：  

- [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) ：這是 SQL2005 新增功能，用來引發例外狀況。
- [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) ：這是 SQL2012 新增功能，用來引發例外狀況。

使用自訂的錯誤要注意的事項：  

- 當在 TRY 區塊中使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 或 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 引發錯誤，程式會立即跳到 CATCH 區塊。
- 使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) ，錯誤等級必須介於 11~19 。
- 使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) ，預設的錯誤代碼為 50000 。

### RAISERROR

[RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 是舊的命令，  
```sql
RAISERROR ( { msg_id | msg_str | @local_variable }
{ ,severity ,state }
[ ,argument [ ,...n ] ] )
[ WITH option [ ,...n ] ]
```

- msg\_id  ：指定錯誤代碼以引發一個錯誤。代碼必須大於 50000 ，若沒指定就是 50000 。
- msg\_str ：指定訊息內容以引發一個錯誤。訊息長度最大為 2047 個字元，支援替代參數的用法。
- @local\_variable ：指定一個 char 或 varchar 型態的變數以引發一個錯誤。
- severity ：使用者自訂嚴重性層級，任何使用者皆可指定從 0 到 18 的嚴重性層級。從 19 到 25 的嚴重性層級只能由系統管理員 (sysadmin) 固定伺服器角色成員或具有 ALTER TRACE 權限的使用者指定。。
- state ：自訂錯誤的狀態碼，值必須介於 0 到 255 之間的整數。
- argument ：用於 msg\_str 訊息中所定義的替代變數的參數值。
- option ：錯誤的自訂選項。

底下範例，在 CATCH 區塊中使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 來重新擲出錯誤
```sql
BEGIN CATCH
Declare @ErrNum int = ERROR_NUMBER();
Declare @ErrMsg nvarchar(200) = ERROR_MESSAGE();
RAISERROR('錯誤代碼：%d \n\r錯誤內容：%s', -- error message
16, -- Severity
10, -- State
@ErrNum, @ErrMsg ) -- error message 的引數值
END CATCH; 
```

**注意：**若要在應用程式端引發錯誤，則嚴重性層級必須大於10(11~20)，例如下面例子，若將嚴重性層級由 15 改成 10 ，則應用程式端就不會產生 exception 。  詳細情形可參考：[HOW TO: Return Errors and Warnings from a SQL Server Stored Procedure in ADO.NET](http://support.microsoft.com/kb/321903)
```sql
CREATE PROCEDURE usp_Test
AS
BEGIN
declare @oldStockQty int = 0;
declare @TradeQty int =2;	
RAISERROR ('現有庫存數量：%d ，不足賣出數量：%d',15, 1, @oldStockQty, @TradeQty)
RETURN 1 
END
```
```c#
try
{
using (StockDBDataContext db = new StockDBDataContext())
{
var result = db.usp_Test();
}
}
catch(SqlException sqlex)
{
MessageBox.Show(sqlex.Message);
//Console.WriteLine(string.Format("State:{0}, severity:{1}, Msg:{2}", sqlex.State, sqlex.Class, sqlex.Message));
}
catch (Exception ex)
{
MessageBox.Show(ex.Message);
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5sftRU2D83i_MP_9QYO_nVOOS2txWJbz5rFgENmadH7I4bC2lxLOzRMfKMoc6cJ_1kaRKpIOB7AChm_CGaL42kE0zkmZtg9itm9mbEoGHIAjK41A3RQwbcGmgeUnUHWr-2-XsGbCLli8/s276/sql-rais-error-1.png)

### THROW
```sql
THROW [ { error_number | @local_variable },
{ message | @local_variable },
{ state | @local_variable } ] 
[ ; ]
```

#### 使用 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 來重新引發例外狀況

你可以直接在 CATCH 區塊使用 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) ，將原先的錯誤直接重新引發。  
```sql
BEGIN CATCH
THROW;
END CATCH;
```

#### 使用 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 引發一個新的例外狀況

你也可以利用 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 引發一個新的例外狀況。  
```sql
DECLARE @msg nvarchar(2048);
SET @msg = '錯誤....訊息';
THROW 60000, @msg, 1; 
```

因為 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 本身不支援格式化的訊息參數，你可以利用 [FORMATMESSAGE](http://msdn.microsoft.com/zh-tw/library/ms186788.aspx) 來協助。  
```sql
DECLARE @message AS NVARCHAR(1000) = FORMATMESSAGE (50001, N'vito'); 
THROW 50001, @message, 0;
```

#### 使用 [FORMATMESSAGE](http://msdn.microsoft.com/zh-tw/library/ms186788.aspx) 搭配 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 
```sql
--訊息 20009 為「發行項 '%s' 無法加入到發行集 '%s' 中」。

DECLARE @msg NVARCHAR(2048) = FORMATMESSAGE(20009, N'First', N'Second'); 

THROW 20009, @msg, 1;  --發行項 'First' 無法加入到發行集 'Second' 中
```

### RAISERROR 與 THROW 的差異

- 使用 [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) 不會中斷執行，而 [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) 會中斷執行。
- You cannot issue [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) with a NOWAIT command in order to cause immediate buffer output. (NOWAIT = SET LOCK\_TIMEOUT 0)
- You cannot issue [THROW](http://msdn.microsoft.com/zh-tw/library/ee677615.aspx) using a severity level higher than 16 by using the WITH [LOG](http://msdn.microsoft.com/zh-tw/library/ms190319.aspx) clause as you can with [RAISERROR](http://msdn.microsoft.com/zh-tw/library/ms178592.aspx) .
```sql
RAISERROR ('Hi there', 16, 0);
PRINT 'RAISERROR error';        -- Prints

THROW 50000, 'Hi there', 0;
PRINT 'THROW error';            -- Does not print
```

## TRY\_CONVERT and TRY\_PARSE

SQL 2012 新增功能。

在程式中常會使用 [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 進行轉型，如果使用不當可能就會引發錯誤。  這時候你就可以使用這二個函式偵側潛在的錯誤。  

- [TRY_CONVERT](http://msdn.microsoft.com/zh-tw/library/hh230993.aspx) ：若轉型成功回傳新值，若失敗回傳 NULL 。
- [TRY_PARSE](http://msdn.microsoft.com/zh-tw/library/hh213126.aspx) ：若轉型成功回傳新值，若失敗回傳 NULL 。僅適用於從字串轉換到日期/時間及數字類型。
```sql
CAST ( expression AS data_type )
CONVERT ( data_type , expression [, style ] )

TRY_PARSE ( string_value AS data_type [ USING culture ])
TRY_CONVERT ( data_type , expression [, style ] )
```

### TRY\_CONVERT
```sql
TRY_CONVERT ( data_type , expression [, style ] )
```

如果轉換成功，會傳回轉換為指定之資料類型的值；否則會傳回 Null。 
```sql
SELECT TRY_CONVERT(DATETIME, '1753-01-01');     -- return 1753-01-01
SELECT TRY_CONVERT(DATETIME, '1752-12-31');     -- return NULL : datetime does not accept dates earlier than 1753-01-01
```

### TRY\_PARSE
```sql
TRY_PARSE ( string_value AS data_type [ USING culture ] )
```

僅適用將字串轉換到日期/時間及數字類型。  如果轉換成功， TRY\_PARSE 會傳回轉換為指定之資料類型的值；否則會傳回 Null。接受 .NET Framework 所支援的任何文化特性。  
```sql
SELECT TRY_PARSE('1' AS INTEGER);       -- return 1
SELECT TRY_PARSE('B' AS INTEGER);       -- return NULL
```

### PARSE
```sql
PARSE ( string_value AS data_type [ USING culture ] )
```

PARSE 用法同 TRY\_PARSE，只是轉換失敗時，會產生錯誤。

### TRY\_CAST 
```sql
TRY_CAST  ( expression AS data_type [ ( length ) ] )
```

TRY\_CAST 意義和 TRY\_CONVERT 是相同的，只是用法不大一樣。

如果轉換成功， TRY\_CAST 會傳回轉換為指定之資料類型的值；如果轉換失敗會傳回 Null。  但是，若您要求的轉換是**不被允許**的，則 TRY\_CAST 會失敗，並產生錯誤，例如：要求將整數轉換為 xml 資料類型，這類轉換是不被允許的。