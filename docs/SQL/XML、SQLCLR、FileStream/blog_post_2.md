---
title: 使用 SQLCLR 和 FileStream
layout: default
parent: XML、SQLCLR、FileStream
nav_order: 2
description: "使用 SQLCLR 和 FileStream"
date: 2013-06-05
tags: [SQL,XML、SQLCLR、FileStream]
postid: "1127502108560186924"
---
自 SQL 2005 後，SQL Server 開始與 .NET 的 CLR 平台整合，讓 SQL Server 可以輕鬆的使用 .NET 程式語言的強大功能，以擴充 SQL Server 功能性及延展性。  

CLR 可以為 SQL Server 撰寫五種擴充功能，分別是：   

- 預存程序 (Stored Procedure)
- 自訂函式 (User Defined Function)
- 觸發程序 (Trigger)
- 彙總函式 (Aggregation Function)
- 自訂型別 (User Defined Type)

在SQL Server 2012中已經將CLR開發整合至新的開發平台(SQL Server Data Tool)，各位可以到以下網址下載，有同時提供整合Visual Studio 2010以及2012的版本。  [http://msdn.microsoft.com/zh-tw/data/tools.aspx](http://msdn.microsoft.com/zh-tw/data/tools.aspx)  

# 如何使用 SQLCLR 

通常會使用到 SQLCLR 通常是在處理資料的過程中，需用到複雜的邏輯計算，或者處理非 SQL Server 能夠處理的領域，如檔案IO、作業系統功能。  而以下項目則是使用 SQLCLR 的必要步驟：  

1. 設定 SQL Server 可以使用 CLR
2. 選寫 .NET 程式
3. 將 .NET 程式編輯成組件
4. 在 SQL Server 中載入程式組件
5. 建立資料庫物件以對應到組件中的功能。

#### 設定啟用 SQLCLR
```sql
EXEC sp_configure 'clr enabled', 1;
RECONFIGURE;
```

# 支援 CLR 的 SQL 物件

## 預存程序（Stored Procedure）

### 1. 撰寫組件
```c#
public partial class StoredProcedures
{
[Microsoft.SqlServer.Server.SqlProcedure]
public static void GetCustomer(string CustomerId)
{
using (SqlConnection conn = new SqlConnection("context connection=true"))
{
SqlCommand cmd = conn.CreateCommand();
cmd.CommandText = @"SELECT \* FROM Customers WHERE CustomerID = @CustomerID";
cmd.Parameters.AddWithValue("@CustomerID", CustomerId);
conn.Open();
SqlContext.Pipe.ExecuteAndSend(cmd);
}
}
};
```

#### Note the following points in this example:

- “context connection=true” tells the SqlConnection object to connect to the SQL Server instance within which you are already executing.
- SqlContext and SqlPipe classes are specific to the SQLCLR implementation and are used to communicate to the SQL Server session within which you are executing.

### 2. 載入組件

CREATE ASSEMBLY MySqlLibDemo FROM 'D:\dll\MySqlLib.dll';

### 3. 建立預存程序

要建立使用 CLR 的預存程序，同樣是使用 [CREATE PROCEDURE](http://msdn.microsoft.com/zh-tw/library/ms187926.aspx) 陳述式。  但是必須利用 EXTERNAL NAME 子句以指定 CLR 程序中要參考的組件方法。  
```sql
EXTERNAL NAME AssemblyName.ClassName.MethodName 
```
```sql
CREATE PROCEDURE usp_CLR_GetCustomer
@CustomerID nchar(5)
AS
EXTERNAL NAME MySqlLibDemo.StoredProcedures.GetCustomer;
```

### 4. 執行預存程序

執行 CLR 預存程序和 TSQL 預存程序是一樣的。  
```sql
EXEC usp_CLR_GetCustomer @CustomerID='ANATR'
```

#### 附註

- 原本建置的 dll 檔可以刪了，因為 [CREATE ASSEMBLY](http://msdn.microsoft.com/zh-tw/library/ms189524.aspx) 時，SQL 已經複製到系統目錄。
- 你也可以使用 ALTER ASSEMBLY ... ADD FILE 將原始檔案加入資料庫，讓它跟著資料漂流。

## 自訂函式（User Defined Function）

### 建立 Scalar UDF

撰寫 CLR Scalar UDF 就和 CLR SP 類似，只是 Scalar UDF 必須回傳一個值，而 SP 回傳 void .  

#### 1. 撰寫組件
```c#
[SqlFunction(IsDeterministic = true, IsPrecise = true)]
static public SqlBoolean IsRegExMatch(SqlString sSourceText, SqlString pattern)
{
// Return NULL if either parameter is NULL.
if (sSourceText.IsNull || pattern.IsNull)
return SqlBoolean.Null; 
return (SqlBoolean)Regex.IsMatch(sSourceText.Value, pattern.Value);
}
```

- (決定性) IsDeterministic = true ：doesn't return different values when called multiple times with the same input values.
- (精確計算) IsPrecise = true ：doesn't use floating point calculations

#### 2. 載入組件

同 CLR SP 。

#### 3. 建立自訂函式
```sql
CREATE FUNCTION fun_CLR_IsRegExMatch
(
@SourceText nvarchar(4000), 
@Pattern nvarchar(100)
)
RETURNS BIT
AS 
EXTERNAL NAME MySqlLibDemo.UserDefinedFunctions.IsRegExMatch;
```

#### 4. 執行自訂函式
```sql
declare @pattern nvarchar(100) = '[A-Z][12](\d{8})';
declare @source nvarchar(4000) = 'F123456789';

--使用自訂函式判斷資料
select dbo.fun_CLR_IsRegExMatch(@source, @pattern)

--找出是否有員工的 IDNO 資料不符合這個 RegEx 判斷式
SELECT EmployeeID, FirstName, IDNO
FROM   Employees
WHERE  dbo.fun_CLR_IsRegExMatch(IDNO, '[A-Z][12](\d{8})') = 0
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgn9FghtUFTH3T34m9pZw6ix9OPHy5-NYvwZxKH67EeAgs9p7icl6os6jSPtVdjiVDYJlkien4Q_j27rFt4VujVGGeWN4H3Uvw8VUYhKaif_BXujhAvGntjR36CtSeGUUj8SDLrSx639-4/s0/sql-clr-udf-1.png)

### 使用 Scalar UDF 當做條件約束

CLR Scalar UDF 也可以當做欄位的條件約束。

#### 建立條件約束

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyKqfQvTzPgMJxLMeGC71zd6KxjY7Pvg7ktuG9MudAx7nTEN2V_jHVC9dp_wvK1O0NKede8N1AzZoxdb4Wf7wuYhgsw_d7lEgl9WAzV1Zk-q1x6dyuR7c8BRAG2wyQdNshQmR9Tb0zeRE/s0/sql-clr-udf-3.png)

#### 資料異動

當設定條件約束的欄位發生資料異動時，便會執行判斷。
```sql
UPDATE Employees SET IDNO='F123' WHERE EmployeeID=1
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxGa3OHJjsyngj2UzSqw3Sldcj0o-GWCeAtPM1gG7g9wNQWtt-AHXpUHxDvWMi5fEY2OcsFtCLz0KcMZsHmzB11MlPbPoLLB3ngo46pxe5D7rcEq1r4sxxFkeslsPwGaWkAuly_kdHNYo/s850/sql-clr-udf-2.png)

### 使用 Scalar UDF 建立索引

當一道 TSQL 查詢中的 WHERE 條件必須經過計算，資料庫引擎可能就只能使用 scan 來查詢資料。
```sql
SELECT EmployeeID 
FROM Employees
WHERE dbo.fun_CLR_IsRegExMatch(IDNO, '[A-Z][12](\d{8})') = 1
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOoAw-fTtZYg9g4U46BQm_1Pfh4AzIVW6XM6xGx-WEFqJximqKAVLhd6md61LBnY5Ajqr31seFwq-ceDNhd2a0pYI75LfEJwO4fjLRL2dMJNh3WXXEQmnKfJIxiXORpxPDYmfa8METxMk/s0/sql-clr-udf-5.png)

#### 建立索引

要改善上面問題，我們可以利用這個 UDF 來建立一個索引。  

首先以使用這個 UDF 建立一個計算欄位(Computed Column)。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-uqZ42Vq86WDu9nIiup3MK6m3fjrbgYTd-x36ytQESbPzpXwiWqnbX62nD6TBSQQAlqK6ukTd6Uj6ERR7e-kvJAv5fkukKyJGkA-ioEfJuHQJnqb4tgMcNfcuZPDqfleFUIZiIlRGGXw/s0/sql-clr-udf-6.png)

使用計算欄位建立索引  
```sql
CREATE NONCLUSTERED INDEX [IX_Employees_IsValidIDNO] ON [dbo].[Employees] 
(
[sIsValidIDNO] ASC
)
```

#### 效率比較

再執行同樣查詢，執行計畫改用 seek 了。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiORKlYPJDnzOP3qLD2459AucFv22Lw2tsJ_aNmTG7wLEZMxkslfpmnKwboMHyiNEXfJtObr2umUl3q6uvW9pxG5emNPxDQba8sEJ6WRB9oGvb3mrRy5LgweOwWjGkFeD_LSo782VbPH7A/s0/sql-clr-udf-4.png)

### 建立 Table-Valued UDF

Because a table-valued UDF returns multiple values (or rather, multiple rows), it is a bit more complex than a scalar UDF.   While a scalar UDF consists of a single method, a table-valued UDF consists of two methods:  

1. 第一個 Method 扮演 iterator 角色，用來尋灠每一個要回傳的資料
2. 第二個 Method 用來產生真正要回傳給 SQL 的資料列。

#### 1. 撰寫組件
```c#
//使用 SqlFunction 屬性,用以告知 Visual Studio 這是一個 UDF
//TableDefinition : 回傳 Table 的結構
//FillRowMethodName : 用來填每個 row 的函式

[SqlFunction(
FillRowMethodName = "fnSplitString_FillRow",
TableDefinition = "SerNum INT, Value NVARCHAR(100)")]
public static IEnumerator fnSplitString(SqlString sSourceText, SqlString separator)
{
// Exit if the string to split is NULL.
if (sSourceText.IsNull)
yield break;

int valueIndex = 1;
foreach (string s in sSourceText.Value.Split(separator.Value.ToCharArray()))
{
yield return new KeyValuePair<int, string>(valueIndex++, s.Trim());
}
}

public static void fnSplitString_FillRow(object row, ref SqlInt32 resultInt, ref SqlString resultString)
{
if (row != null)
{
KeyValuePair<int, string> returnValue = (KeyValuePair<int, string>)row;  
resultInt = returnValue.Key;  
resultString = returnValue.Value;  
}
}
```

#### 2. 載入組件

同 CLR SP 。

#### 3. 建立自訂函式
```sql
CREATE FUNCTION fun_CLR_SplitString
(
@SourceText nvarchar(4000), 
@Separator nvarchar(100)
)
RETURNS TABLE ( num int, result nvarchar(4000) ) 
AS 
EXTERNAL NAME MySqlLibDemo.UserDefinedFunctions.fnSplitString;
```

#### 4. 執行自訂函式
```sql
select \* from dbo.fun_CLR_SplitString('abc,def,xyz',',');
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJ5agS1gIKiBPzZBvP2nnB8pMw3sjBuu_nky216Ei9OMQT4UGN8sytvg1OIAXwfogcsgv8WRWZdQgDZd6NLeHmZRRC6NoUDOV4Jk6-fCX6mml8WX2ybFM29VjRiV7IfHWrv1vGJDZyfB8/s0/sql-clr-udf-7.png)

## 觸發程序（Trigger）

暫略

## 彙總函式（User-Defined Aggregate）

暫略

## 自訂型別（User Defined Type, UDT）

暫略

# CLR 組件的權限設定

當 SQL Server 存取組件時，必須授予組件程式碼一組存取權限。  可使用的存取權限有以下三種：**SAFE**、**EXTERNAL\_ACCESS**、**UNSAFE**。  若未指定，便採用 **SAFE** 作為預設值。   

- **SAFE**  
SAFE 是最有限的權限集合。  具有 SAFE 權限的組件所執行的程式碼，不能存取外部系統資源，例如，檔案、網路、環境變數或登錄。
- **EXTERNAL\_ACCESS**  
EXTERNAL\_ACCESS 權限集合可讓組件存取特定的外部系統資源，例如，檔案、網路、環境變數和登錄。
- **UNSAFE**  
UNSAFE 可讓組件無限制地存取 SQL Server 執行個體內外的資源。  從 UNSAFE 組件內執行的程式碼，可以呼叫 Unmanaged 程式碼。

# 使用 Filestream

請參考[這篇](http://vito-note.blogspot.com/2013/06/filestream.html)
## 參考資料  

- [啟用 CLR 整合](http://msdn.microsoft.com/zh-tw/library/ms131048.aspx)
- [Common Language Runtime (CLR) 整合程式設計概念](http://msdn.microsoft.com/zh-tw/library/ms131102.aspx)
- [CLR 整合程式碼存取安全性](http://msdn.microsoft.com/zh-tw/library/ms345101.aspx)