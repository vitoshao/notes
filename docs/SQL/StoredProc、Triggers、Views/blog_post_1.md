---
title: 如何取得 Store Procedure 的回傳值
layout: default
parent: StoredProc、Triggers、Views
nav_order: 1
description: "如何取得 Store Procedure 的回傳值"
date: 2012-09-16
tags: [SQL,StoredProc、Triggers、Views]
postid: "1169118288792299545"
---
# 如何撰寫可回傳值的 Store Procedure 

### 使用 Select 回傳一個查詢結果
```sql
ALTER PROCEDURE [dbo].[usp_QueryEmployee]
@EmpName nvarchar(150)
AS
BEGIN
SELECT \* From Employee WHERE Name = @EmpName
END
```
```sql
exec usp_QueryEmployeeD 'vito'
```

### 使用 return 回傳一個值
```sql
ALTER PROCEDURE [dbo].[usp_UpdateSalary]
@EmpName nvarchar(150)
AS
BEGIN
Declare @AffectedRows int = 0 ;

Update Employee SET Salary = Salary + 50 WHERE Name = @EmpName
SET @AffectedRows = @@ROWCOUNT;

print N'共更新 ' + cast(@AffectedRows as varchar(10)) + N' 筆';
return @AffectedRows;
END
```
```sql
declare @AffectedRows int
exec @AffectedRows = usp_UpdateSalary 'vito'
print @AffectedRows
```

### 使用 OUTPUT 參數回傳
```sql
ALTER PROCEDURE [dbo].[usp_InsertEmployee]
@EmpName nvarchar(150),
@IdentityID int = 0 OUTPUT
AS
BEGIN
Insert Employee(Name) Values (@EmpName)

SET @IdentityID = @@IDENTITY;
IF (@IdentityID > 0) 
PRINT N'新增成功';
ELSE 
PRINT N'新增失敗';

SELECT @IdentityID;
END
```
```sql
declare @id int
exec usp_InsertEmployee 'vito', @id OUTPUT
print 'id=' + cast(@id as varchar(10))
```

# 使用 ADO.NET 取得 Strore Procedure 回傳值

## 不回傳值
```c#
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_InsertEmployee";

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));
cmd.ExecuteNonQuery();
}
```

## 取得 Select 查詢的結果
```c#
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_QueryEmployee";

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));

SqlDataAdapter da = new SqlDataAdapter(cmd);
DataSet dsEmployees = new DataSet("Employees");
da.Fill(dsEmployees, "Employees");
}
```

## 取得 Select 的回傳值

使用 ExecuteScalar 方法可取得回傳資料中的第一個資料列的第一個資料行的值。
```c#
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_InsertEmployee";

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));
int id = Convert.ToInt32(cmd.ExecuteScalar());
}
```

## 取得 Output 參數的回傳值
```c#
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_InsertEmployee";

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));

// 加入一個 Output 參數 , 設定 Direction = ParameterDirection.Output
SqlParameter outPara = new SqlParameter("IdentityID", SqlDbType.Int);
outPara.Direction = ParameterDirection.Output;
cmd.Parameters.Add(outPara);

cmd.ExecuteNonQuery();

Console.WriteLine(outPara.Value);
}
```

## 取得 return 指令的回傳值
```c#
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_UpdateSalary";

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));

// 加入一個 return 參數 , 設定 Direction = ParameterDirection.ReturnValue
SqlParameter returnValue = new SqlParameter("XXX", SqlDbType.Int);
returnValue.Direction = ParameterDirection.ReturnValue;
cmd.Parameters.Add(returnValue);

cmd.ExecuteNonQuery();

Console.WriteLine(returnValue.Value);
}
```

## 取得 PRINT 陳述式的輸出

有時候我們會在 Store Procedure 中使用 Print ，顯示某些資訊，這些資訊也可以由程式中取得。
```c#
protected void ConnectionInfoMessage(object sender, SqlInfoMessageEventArgs e)
{
foreach (SqlError info in e.Errors)
Console.WriteLine(info.Message);
}

private void btnPrint_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateConnection())
{
SqlCommand cmd = connection.CreateCommand();
cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_InsertEmployee";

// 訂閱 InfoMessage 事件
connection.InfoMessage += ConnectionInfoMessage;

connection.Open();
cmd.Parameters.Add(new SqlParameter("EmpName", "vito"));

cmd.ExecuteNonQuery();
}
}
```