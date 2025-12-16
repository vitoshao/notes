---
title: User Defined Functions
layout: default
parent: StoredProc、Triggers、Views
nav_order: 7
description: "User Defined Functions"
date: 2013-05-22
tags: [SQL,StoredProc、Triggers、Views]
postid: "5657130069690784278"
---
Functions are programmable objects that are used to perform calculations that can be returned to a calling application or integrated into a result set.   Functions can access data and return results, but they cannot make any modifications.   

You can create your own functions, referred to as user-defined functions,   and store the functions in any database for which you have [CREATE FUNCTION](http://msdn.microsoft.com/zh-tw/library/ms186755.aspx) authority.  

# 簡介使用者自訂函式（User-Defined Functions）

要建立自訂函式，首先要具有 [CREATE FUNCTION](http://msdn.microsoft.com/zh-tw/library/ms186755.aspx) 的權限。  自訂函式是用來執行運算用的，不允許變更資料庫的狀態。下表是在自訂函式中不允許操作的項目：  

- Perform an action that **changes the state** of an instance or database
- **Modify** data in a table
- Call a function that has an external effect, such as the **RAND** function
- Create or access **temporary tables**
- Execute code **dynamically**

自訂函式可以回傳一個**純量值**或是一個**資料表**，而資料表又分成**嵌入資料表**和**多重陳述式資料表**  

- 純量值函式（scalar function ）
- 嵌入資料表值函式（inline table-valued function）
- 多重陳述式資料表值函式（multi-statement table-valued function）

當新增自訂函式時，可以在選單中看到以下選擇：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBWwkKmi91sogAegMgQScpgNF9fHNN1Ec1OL4Qlu7S5vYNQZrYjA7Q49chd3n2fqE8_8Dk9-PwCK4YRpOzHieT3c73ggv0gVxBZJdQAOf27UMxGmY4XEWhrPEnSmPPs-i-kYdb6HLv9fs/s0/sql-add-function.png)

# 純量值函式（scalar function ）

#### 使用純量值函式注意事項:

- RETURN 子句後接一個 single value
- 要用 BEGIN/END 包住程式碼
- 回傳一個純量值，使用時，把函式當常數值用即可。

#### 定義
```sql
CREATE FUNCTION [ schema_name. ] function_name 
( 
[ 
{ @parameter_name [ AS ][ type_schema_name. ] parameter_data_type [ = default ] [ READONLY ] } 
[ ,...n ] 
]
)
RETURNS return_data_type
[ WITH <function_option> [ ,...n ] ]
[ AS ]
BEGIN
<function_body>
RETURN scalar_expression
END
[ ; ]
```

**範例**
```sql
CREATE FUNCTION fu_GetQuater ( @date as datetime )
RETURNS nvarchar(3)
BEGIN
DECLARE @return nvarchar(3);
SELECT @return = '第' + cast(DatePart(QQ,@date) as nvarchar(1)) + '季' 
RETURN @return
END

SELECT dbo.fu_GetQuater(OrderDate),\* FROM ORDERS
SELECT dbo.fu_GetQuater(GETDATE())
```

# 嵌入資料表值函式（inline table-valued function）

#### 使用嵌入資料表值函式注意事項:

- 使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式回傳查詢的結果集。
- 無法使用 [DECLARE](http://msdn.microsoft.com/zh-tw/library/ms188927.aspx) 宣告變數。
- RETURN 子句後接一個 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) statement
- 回傳一個結果集，可以自成一個檢視(VIEW)，使用時把函式當成 view 或 table 用。

#### 定義
```sql
CREATE FUNCTION [ schema_name. ] function_name 
( 
[ 
{ @parameter_name [ AS ][ type_schema_name. ] parameter_data_type [ = default ] [ READONLY ] } 
[ ,...n ] 
]
)
RETURNS TABLE
[ WITH <function_option> [ ,...n ] ]
[ AS ]
RETURN 
[ ( ] 
select_stmt 
[ ) ]
[ ; ]
```

**範例**
```sql
CREATE FUNCTION fu_GetEmps_ILTV( @DepNo as int )
RETURNS TABLE
RETURN 
(
SELECT EmpNo, EmpName, DepNo FROM Emp WHERE DepNo=@DepNo
)

SELECT \* FROM fu_GetEmps_ILTV(1)

SELECT D.DepName, E.EmpName 
FROM fu_GetEmps_ILTV(1) E
INNER JOIN Dep D ON E.DepNo=D.DepNo
```

# 多重陳述式資料表值函式（multi-statement table-valued function）

#### 使用多重陳述式資料表值函式注意事項

- 定義一個 TABLE 型別的變數，將所有陳述式的結果都放進這個變數，最後回傳。
- RETURN 子句放在函式結束的地方，後面不接東西。
- 要用 BEGIN/END 包住程式碼

#### 定義
```sql
CREATE FUNCTION [ schema_name. ] function_name 
( 
[ 
{ @parameter_name [ AS ][ type_schema_name. ] parameter_data_type [ = default ] [ READONLY ] } 
[ ,...n ] 
]
)
RETURNS @return_variable TABLE <table_type_definition>
[ WITH <function_option> [ ,...n ] ]
[ AS ]
BEGIN 
function_body 
RETURN
END
[ ; ]
```

**範例**
```sql
CREATE FUNCTION fu_GetEmps_MSTV( @DepNo as int )
RETURNS @mytable TABLE(
[EmpNo] [int],
[EmpName] [nvarchar](50),
[DepNo] [int]
)
As
BEGIN
INSERT	@MYTable
SELECT EmpNo, EmpName, DepNo FROM Emp WHERE DepNo=@DepNo

RETURN
END

SELECT \* FROM fu_GetEmps_MSTV(1)

SELECT D.DepName, E.EmpName 
FROM fu_GetEmps_MSTV(1) E
INNER JOIN Dep D ON E.DepNo=D.DepNo
```

# 自訂函式的叫用方法

自訂函式除了可以使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式叫用外，下列狀況也都可以使用：  

- A [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) list
- A WHERE clause
- An expression
- A CHECK or DEFAULT constraint
- A FROM clause with the CROSS/OUTER [APPLY](http://msdn.microsoft.com/en-us/library/ms175156.aspx) function
```sql
--A SELECT list
SELECT dbo.fu_GetQuater(OrderDate), COUNT(1)
FROM ORDERS
WHERE YEAR(OrderDate)=1997
GROUP BY dbo.fu_GetQuater(OrderDate)

--A WHERE clause
SELECT \* FROM ORDERS WHERE dbo.fu_GetQuater(OrderDate)=N'第1季'

--CROSS/OUTER APPLY function
SELECT D.DepName, E.EmpName 
FROM fu_GetEmps(1) E
INNER JOIN Dep D ON E.DepNo=D.DepNo
```

# 總結

彙整一下三種函式的使用方法，下圖為範例資料：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2q0rwk9P2xjJt2MjT3wYC8uAUUnEQO180SOu2E3WwCrgfvoQ7S7MZvLnzIWAhuwqPCxlt3jx-CAJjnqyZUHObUV4jcFJnMjhXcrvr75ctYrddB1CWgASsUJxRPqjgn8bJZ9PdR-L_9cg/s0/sql-grade-schema.png)
```sql
Create Function fu_GetGrade(@StudentNo int,@ClassID int)
Returns int
Begin

DECLARE @Grade int; 

SELECT @Grade = Grade FROM tblGrade 
WHERE StudentNo=@StudentNo AND ClassID=@ClassID

Return @Grade;
End
```
```sql
Create Function fu_GetGradeTable(@ClassID int)
Returns TABLE
As
Return
(
SELECT \* FROM tblGrade 
WHERE ClassID=@ClassID
)
```
```sql
Create Function fu_GetStudentGrade(@StudentNo int)
Returns @r 
TABLE(
StudentNo int,
ClassID int,
Grade int
)
AS
BEGIN
INSERT @r
SELECT StudentNo,ClassID,Grade FROM tblGrade 
WHERE StudentNo=@StudentNo

RETURN
END
```

叫用方法
```sql
--scalar 函式			
SELECT dbo.fu_tblGrade(1,1)

--inline 函式	
SELECT \* from dbo.fu_GetGradeTable(1)

--multi-statement 函式		
SELECT \* from dbo.fu_GetStudentGrade(1)
```

# Using APPLY

簡單講 [APPLY](http://msdn.microsoft.com/en-us/library/ms175156.aspx) 就是用來 JOIN 一般資料表和 table-valued function 回傳的資料表。

#### 使用 INNER JOIN 

既然 table-valued function 回傳的是一個資料表，那麼你可以這麼使用
```sql
SELECT S.StudentName, G.ClassID, G.Grade
FROM tblStudent S
INNER JOIN dbo.fu_GetStudentGrade(1) G ON S.StudentNO=G.StudentNo
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdZP0Ur6ygMLpS58F31yx0VCoFmma8eI2U_DibDO5u_zlhLXMKQEqUWgJG1l8RmiL5uftce2Rx5ZZLo9oNaysCW30rx6J0w5o_YA2BaLDjCExTB1Rb2K32Ke-uMYTrKlBl6qsEFQzhyphenhyphenek/s0/sql-apply.png)

#### 使用 [CROSS APPLY](http://msdn.microsoft.com/zh-tw/library/ms175156.aspx) 

不過 SQL 提供 [APPLY](http://msdn.microsoft.com/en-us/library/ms175156.aspx) 運算子，可以讓這件事做起來更方便。
```sql
SELECT S.StudentName, G.ClassID, G.Grade
FROM tblStudent S
CROSS APPLY fu_GetStudentGrade(S.StudentNO) G
WHERE S.StudentNo=1
```
## 參考資料  

- 
- 
-