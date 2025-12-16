---
title: 使用 ADO.NET Disconnected 類別
layout: default
parent: ADO.NET and XML
nav_order: 1
description: "使用 ADO.NET Disconnected 類別"
date: 2012-09-16
tags: [Web,ASPNET,ADO.NET and XML]
postid: "5339967256683944984"
---
# ADO.NET

MSDN：[ADO.NET 概觀](http://msdn.microsoft.com/zh-tw/library/h43ks021%28v=vs.100%29.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTg0PIpqvHF62zPSD6wzr55vPkQNs-qvfzBPXXJ0Y_yiQhO8DzUK5-ySJ529FsTGhr_IEfAUQccFP0GCTbTbgy46wzs-TTlMaOdu9z4sOi_1kVFqM0W449AtySzJgvxFZq6QM_FV5AhyphenhyphenM/s578/IC30745.gif)

# 使用 DataTable 物件

## DataTable 物件

[DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 是一種表格式的資料表現概念，它由資料列(rows)、資料欄(columns)、條件約束(constraints)組成。

#### 常用方法：

- [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) ：認可自前一次呼叫 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 以來對這個資料表所做的所有變更。
- [GetChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.getchanges.aspx) ：取得自前一次呼叫 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 以來所做的所有變更。
- [Load](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.load.aspx) ：載入資料。（ 視 [LoadOption](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) 參數，執行不同的載入操作）
- [LoadDataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.loaddatarow.aspx) ：尋找並更新特定資料列。 如果找不到符合的資料列，則使用指定值來建立新的資料列。
- [Merge](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.merge.aspx) ：將指定的 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 與目前的 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 合併。
- [NewRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.newrow.aspx) ：使用與資料表相同的結構描述來建立新的 DataRow。
- [OnRowChanged](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.onrowchanged.aspx) ：引發 RowChanged 事件。
- [Select](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.select.aspx) ：取得符合篩選條件的所有 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 物件的陣列。
- [Copy](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.copy.aspx) ：複製這個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的結構和資料。

## 建立 DataTable 
```c#
private DataTable CreateDataTable()
{
//=====================================================
//Create the DataTable named "Employee"
//=====================================================

DataTable Employee = new DataTable("Employee");

//=====================================================
//Add the DataColumn
//=====================================================

DataColumn col1 = new DataColumn("EmpNo");
col1.DataType = typeof(string);
col1.MaxLength = 10;
col1.Unique = true;                 // Unique Key
col1.AllowDBNull = false;
col1.DefaultValue = "";             // DefaultValue
col1.Caption = "EmpNo";
col1.AutoIncrement = false;

DataColumn col2 = new DataColumn("FirstName");
col2.MaxLength = 20;
col2.AllowDBNull = false;

DataColumn col3 = new DataColumn("LastName");
col3.MaxLength = 20;
col3.AllowDBNull = false;

DataColumn col4 = new DataColumn("Age");
col3.DataType = typeof(Int16);
col1.DefaultValue = 0;

// Add [ Expression columns ]

DataColumn col5 = new DataColumn("LastName and FirstName");
col5.DataType = typeof(string);
col5.MaxLength = 40;
col5.Expression = "LastName + ',' + FirstName";
col1.AllowDBNull = false;

Employee.Columns.Add(col1);
Employee.Columns.Add(col2);
Employee.Columns.Add(col3);
Employee.Columns.Add(col4);
Employee.Columns.Add(col5);

//=====================================================
// Set Primary Key Columns
//=====================================================

Employee.PrimaryKey = new DataColumn[] { col1 };

return Employee;
}
```

##  Copy 、 Clone 、 Import 

- [Copy](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.copy.aspx) ：複製完整 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的結構和資料。
- [Clone](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.clone.aspx) ：僅複製 (Clone) [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的結構，包括所有 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 結構描述和條件約束。。
- [ImportRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.importrow.aspx) ：將 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 複製到 DataTable，並且保留該資料列的 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 版本。
```c#
DataTable employee = CreateDataTable();

// Copy DataTable
DataTable copy = employee.Copy();

// Clone DataTable
DataTable clone = employee.Clone();

// Importing datarows to datatable
clone.ImportRow(employee.Rows[0]);
```

使用 [ImportRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.importrow.aspx) 時須注意：

- 呼叫 [NewRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.newrow.aspx) 會使用現有資料表結構描述，將資料列加入至資料表，但具有資料列的預設值，並將 [DataRowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 設定為 Detached。
- 呼叫 [ImportRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.importrow.aspx) 會保留現有的 DataRowState，以及資料列中的值。
- 新資料行會加入至資料表的結尾。
- 如果匯入的資料行違反條件約束 (如：PK)，會引發 Exception，不會被新增到資料表中。

## DataColumn 物件

[DataColumn](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.aspx) 是用來表示 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 中資料行的結構描述。

#### 底下是 [DataColumn](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.aspx) 預設的屬性值：

- [DataType](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.datatype.aspx) ：預設值為 String
- [MaxLength](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.maxlength.aspx) ：預設值為 -1 (表示不執行最大長度的檢查)
- [Unique](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.unique.aspx) ：預設值為 False
- [AllowDBNull](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.allowdbnull.aspx) ：預設值為 True
- [Caption](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.caption.aspx) ：預設值為 ColumnName 的值。
- [AutoIncrement](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.autoincrement.aspx) ：資料行是否自動遞增資料行的值。預設值為 false。
- [Expression](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.expression.aspx) ：設定運算式的欄位。

#### 建立 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的 PK 欄位
```c#
Employee.PrimaryKey = new DataColumn[] { col1 };
```

## 篩選 DataTable 

### 使用 DataTable.Select()

[DataTable.Select](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.select.aspx) 可以用來取得符合篩選條件的所有 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 物件的陣列。
```c#
protected void btnDataTableSelect_Click(object sender, EventArgs e)
{
// load test data
DataTable dataTable = TestData.CreateEmployeeDataTable();
TestData.SetEmployeeData(dataTable);

// set filter expressiong
string expression = "FirstName = 'AAA'";
DataRow[] foundRows = dataTable.Select(expression);

// import filter result to new datatable
DataTable tableFiltered = dataTable.Clone();  // 複製datatable結構描述。
foreach (DataRow row in foundRows)
{
tableFiltered.ImportRow(row);
}

gv1.DataSource = tableFiltered;
gv1.DataBind();
}
```

### 使用 DataView.RowFilter()

[RowFilter](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.rowfilter.aspx) 是 [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 物件的屬性，用來設定該 [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 的篩選條件。  你可以透過 [RowFilter](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.rowfilter.aspx) 屬性的設定，取得符合篩選條件的 [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 物件。  
```c#
protected void btnDataViewRowFilter_Click(object sender, EventArgs e)
{
// load test data
DataTable dtEmpData = TestData.CreateEmployeeDataTable();
TestData.SetEmployeeData(dtEmpData);

// Use the RowFilter method of DataView object to find all rows matching the filter.
dtEmpData.DefaultView.RowFilter = "FirstName = 'AAA'";
DataTable dtFiltered = dtEmpData.DefaultView.ToTable();

// 使用篩選後的 datatable 當做資料來源
gv1.DataSource = dtFiltered;
gv1.DataBind();

// DataView 物件也可以直接當做資料來源
dtEmpData.DefaultView.RowFilter = "FirstName = 'AAA'";
gv1.DataSource = dtEmpData.DefaultView;
gv1.DataBind();
}
```

# 使用 DataRow 物件

[DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 是用來表示 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的欄位定義，也就因此它必須由 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 產生。

## DataRow 物件的常用方法

- [BeginEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.beginedit.aspx) ：在 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 物件上開始批次更改。
- [EndEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.endedit.aspx) ：結束批次更改。
- [CancelEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.canceledit.aspx) ：取消批次更改。
- [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) ：認可自前一次呼叫 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 以來，對這個資料列所做的所有變更。
- [RejectChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rejectchanges.aspx) ：拒絕自前一次呼叫 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 以來，對資料列所做的所有變更。
- [Delete](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.delete.aspx) ：刪除 DataRow。
- [HasVersion](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.hasversion.aspx) ：判斷是否有指定的版本。

當

## 將資料加入到 DataTable 中

要將資料加入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) ，可以使用 Add 、 [Load](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.load.aspx) 或 [LoadDataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.loaddatarow.aspx) 方法。

####  [Add](http://msdn.microsoft.com/zh-tw/library/system.data.datarowcollection.add.aspx) ：將資料逐筆加入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 中

- 透過 [DataTable.NewRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.newrow.aspx) 以建立符合該 [Table](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.table.aspx) 結構的 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 物件。
- 再將 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 加入到 [DataTable.Rows](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.rows.aspx) 集合之中。
```c#
public void Add(DataRow row);
public DataRow Add(params object[] values);  //若是自動編號欄位，該欄位必須傳入 null 以取得該資料行的預設值。 
```
```c#
// 1) 先建立 DataRow , 再加入 DataTable

DataRow newemployee = employee.NewRow();
newemployee["EmpNo"] = "123456789A"; 
newemployee["FirstName"] = "Nancy"; 
newemployee["LastName"] = "Davolio"; 
newemployee["Age"] = 30; 
employee.Rows.Add(newemployee);

// 2) 直接在 DataTable 加入一筆資料

employee.Rows.Add("987654321X", "Andrew", "Fuller", 40);
```

####  [Load](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.load.aspx) ：資料來源的值填滿 DataTable。

如果 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 已經包含資料列，則資料來源中的資料將會與現有的資料列合併。  合併的原則，則視 [LoadOption](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) 參數決定。  

使用 [Load](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.load.aspx) 或 [LoadDataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.loaddatarow.aspx) 方法時，可以指定是否更新現有 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 中的資料，所以必須指定 PK 欄位。  當載入的資料與現有的資料具有相同的 PK 時，它就會根據 [LoadOption](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) 參數值做不同的處理。  
```c#
public void Load(IDataReader reader);
public void Load(IDataReader reader, LoadOption loadOption);
public virtual void Load(IDataReader reader, LoadOption loadOption, FillErrorEventHandler errorHandler);
```
```c#
DataTable NewEmployees = CreateDataTable();
NewEmployees.Columns.Remove("LastName and FirstName");
NewEmployees.Rows.Add("1234567890", "AAA", "BBB", 10);
NewEmployees.Rows.Add("1234567891", "CCC", "EEE", 20);
DataTableReader reader = new DataTableReader(NewEmployees);

Employee.Load(reader);
```

####  [LoadDataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.loaddatarow.aspx) ：尋找並更新特定資料列

如果找不到符合的資料列，則會將該資料列加入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 。
```c#
public DataRow LoadDataRow(object[] values, bool fAcceptChanges);
public DataRow LoadDataRow(object[] values, LoadOption loadOption);
```
```c#
employee.LoadDataRow(
new object[] { "1234567891", "CCC", "DDD", 30 },
LoadOption.OverwriteChanges);       //更新 Current , 且更新 Original
```

#### [LoadOption](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) 

這個列舉值是指明若載入的資料與現有資料 PK 重複時，應如何套用到現有的資料列上。
```c#
public enum LoadOption
{
OverwriteChanges = 1,   // OverwriteRow, Update Original and Current Values 
PreserveChanges = 2,    // PreserveCurrentValues (default), Update Original Values 
Upsert = 3,             // UpdateCurrentValues 
}
```

| LoadOption | Version Value Changed | RowState |
| --- | --- | --- |
| [OverwriteChanges](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) | 以收到的資料列值，更新 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) and [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。 | 無論原狀態為何，只要有被更新的列，  &lt;br&gt;  其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 屬性會設定為 [Unchanged](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  新增的列， [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 屬性會設定為 [Unchanged](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。 |
| [PreserveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) | 以收到的資料列值，更新 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。 | 被更新的列，  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Added](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會設定為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會設定為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Deleted](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會保留為 [Deleted](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  新增的列， [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 屬性會設定為 [Unchanged](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。 |
| [Upsert](http://msdn.microsoft.com/zh-tw/library/system.data.loadoption.aspx) | 以收到的資料列值，更新 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。 | 被更新的列，  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Added](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會保留為 [Added](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會保留為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  若其 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 原本為 [Deleted](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，則會設定為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  &lt;br&gt;  新增的列， [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 屬性會設定為 [Added](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。 |

**範例**：
```c#
//載入測試資料：Modify , Unchanged 和 Added 各二筆
DataTable dTable = CreateDataTable();
dTable.Rows.Add("001", "AAA", "aaa", 10);   //Modified
dTable.Rows.Add("002", "BBB", "bbb", 20);   //Unchanged
dTable.AcceptChanges();
dTable.Rows.Add("003", "CCC", "ccc", 30);   //Added
dTable.Rows.Add("004", "DDD", "ddd", 40);   //Added
dTable.Rows[0][3] = 15;
dTable.Rows[2][3] = 35;

dTable.LoadDataRow(new object[] { "001", "AAA", "aaa", 11 }, LoadOption.PreserveChanges);
dTable.LoadDataRow(new object[] { "003", "CCC", "ccc", 31 }, LoadOption.PreserveChanges);
dTable.LoadDataRow(new object[] { "005", "EEE", "eee", 51 }, LoadOption.PreserveChanges);

dTable.LoadDataRow(new object[] { "001", "AAA", "aaa", 11 }, LoadOption.OverwriteChanges);
dTable.LoadDataRow(new object[] { "003", "CCC", "ccc", 31 }, LoadOption.OverwriteChanges);
dTable.LoadDataRow(new object[] { "005", "EEE", "eee", 51 }, LoadOption.OverwriteChanges);

dTable.LoadDataRow(new object[] { "001", "AAA", "aaa", 11 }, LoadOption.Upsert);
dTable.LoadDataRow(new object[] { "003", "CCC", "ccc", 31 }, LoadOption.Upsert);
dTable.LoadDataRow(new object[] { "005", "EEE", "eee", 51 }, LoadOption.Upsert);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiU_9nsdHw3lcv1ubvV-ByoPw5WjFJ9RqH4iItMl4NdCN4ABPCVC5_zDtdCQcy7gqT1p6tDF7y1gh0AMHkPz-tjtUPGBt8S2BSTCRFleOffAuCYlgs6IA8zu3-xwnkXx7Pe5IE1DE03H2I/s274/LoadOptionTest.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgx9r2zc0B8IqznywvsLSldJmfT96hOiVn89iWoJfsrOeAonEFojjlIB68Lx976c5s2dNbMgORKCu7_l_rPtd2syGuAjQZ22SBhMeZL-rmYzLlimoIRsQLmSfbJZCYDiDuvtZ5nUm5QthI/s360/LoadOptionTest-PreserveChanges.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXxkIRoLQ_RDvahTYBycr9E4dUNd45aUy_BoK4mQObluw1hYWlNOxJ6HvUBeg3uP1mV189gZ-CKEeh1COmg_hMf7FS758vRZI0g9EkpY2hJ1SiepCljjtYlaUOWnVeDaMZkzr3WhcLVVg/s384/LoadOptionTest-OverwriteChanges.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjH53Nz63Y6SIQftSkGVAeO4vkrbD0saMjvKJBTnY1RVfxmq1BJvVr-ym1BxHEx51G5nix0iEkzTVw7rpU8MTMN6lfoqrE6fsDZBJpfaQB3NydJFz_66vVKCB7drVv-8azRcPAnbIP4abc/s343/LoadOptionTest-Upsert.png)

## DataRowState

[DataRowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 這個列舉值是用來取得 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 物件的狀態。
```c#
public enum DataRowState
{
Detached = 1,   // DataRow is created but not added to a DataTable.
Unchanged = 2,  // DataRow has not changed since the last call to the AcceptChanges method.
Added = 4,      // DataRow is added to a DataTable.
Deleted = 8,    // DataRow is deleted using the Delete method of the DataRow.
Modified = 16,  // DataRow has been modified since the last time the AcceptChanges method was called.
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRY-Tgc5iBT3uXgP-Ewx8kMpPdLy70RGVy56iKQC4vkfaEZbVwgUfGoZb1y3a4WO0gLBGQEM-O9dFNmqepSM1_hetJDG7J9ap2GZWIs6aSIgerXhdg9k9_GPZC9ZNshx_D48D_oYwR7J8/s791/datarow-rowstate.png)

## DataRowVersion

[DataRowVersion](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 這個列舉值是用來描述 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 的版本。
```c#
public enum DataRowVersion
{
Original = 256,     // 資料列含有原始值(original value)。
Current = 512,      // 資料列含有目前的值(current value)。
Proposed = 1024,    // 資料列含有建議值(proposed value)。
Default = 1536,     // 當 RowState 為 Added、Modified 時，其預設的版本 Current ； 當 DataRowState 為 Detached 時，版本則為 Proposed。
}
```

#### [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 的版本會隨下列狀況下變更：

- 當 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 建立時，只會有單一版本，叫做 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。
- 當叫用 [BeginEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.beginedit.aspx) 時，變更的資料會放在第二個版本，叫做 [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。      所以會有 [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。
- 當叫用 [EndEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.endedit.aspx) 時， [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 變成 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) ， [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 變成 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。  所以會有 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。
- 當再次叫用 [BeginEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.beginedit.aspx) 時，會複製 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 到 [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。                   所以會有 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。
- 當叫用 [EndEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.endedit.aspx) 時， [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 變成 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。                           所以會有 [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 和 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 。
- 當叫用 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 時， [Original](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 值會變成和 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 值相同。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh448JkLXRAicEUioSBUSVA3Zj9BKantdA1Bc3IGwz0lo3HI0o9nBFu_4xLc5M3A3VaI3gJWXtc9Xw6S1yhylaDugvCKPTeOwQECGc0jkNRgUZE-GylYFjV1OSo_pUrNdBCHEVicq3VOlc/s850/datarow-version.png)

#### 取得 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 不同的版本。
```c#
if (dRow.HasVersion(DataRowVersion.Original))
myDebug.Write( dRow["Age", DataRowVersion.Original].ToString() ); 

if (dRow.HasVersion(DataRowVersion.Proposed))
myDebug.Write( dRow["Age", DataRowVersion.Proposed].ToString() ); 
```

#### [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 的預設版本：

- 當 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 為 [Added](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 、 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 時，若讀取資料值時，會取得 [Current](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 版本的值。
- 當 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 為 [Deleted](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 時，若讀取資料值時，會引發 Exception 。
- 當叫用 [BeginEdit](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.beginedit.aspx) 方法後，若讀取資料值時，會取得 [Proposed](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 版本的值。

**範例１**
```c#
DataTable dTable = CreateDataTable();

DataRow dRow = dTable.NewRow();
dRow["EmpNo"] = "12345";
dRow["FirstName"] = "vito";
dRow["LastName"] = "shao";
dRow["Age"] = 10;
ShowState("1)After DataTable.NewRow", dTable, dRow);

dTable.Rows.Add(dRow);
ShowState("2)After DataTable.Rows.Add", dTable, dRow);

dRow.AcceptChanges();
ShowState("3)After Add + AcceptChanges", dTable, dRow);

dRow.BeginEdit();
dRow["Age"] = 22;
ShowState("4)After BeginEdit", dTable, dRow);

dRow.CancelEdit();
ShowState("5)After CancelEdit", dTable, dRow);

dRow.BeginEdit();
dRow["Age"] = 33;
dRow.EndEdit();
dRow.CancelEdit();
ShowState("6)After BeginEdit+EndEdit+CancelEdit", dTable, dRow);

dRow["Age"] = 44;
dTable.RejectChanges();
ShowState("9)After modify + RejectChanges", dTable, dRow);

dRow["Age"] = 55;
dRow.AcceptChanges();
ShowState("7)After modify + AcceptChanges", dTable, dRow);

dRow.Delete();
ShowState("8)After Delete", dTable, dRow);

dTable.AcceptChanges();
ShowState("9)After Delete + AcceptChanges", dTable, dRow);

//                                        RowState   RowCounts   Age    Default Current Original Proposed
//                                        =========  ==========  ===    ======= ======= ======== ========
//1)After DataTable.NewRow                Detached    0                   10                       10       
//2)After DataTable.Rows.Add              Added       1           10      10      10                        
//3)After Add + AcceptChanges             Unchanged   1           10      10      10      10                
//4)After BeginEdit                       Unchanged   1           22      22      10      10       22 (Default=Proposed)
//5)After CancelEdit                      Unchanged   1           10      10      10      10                
//6)After BeginEdit+EndEdit+CancelEdit    Modified    1           33      33      33      10                
//9)After modify + RejectChanges          Unchanged   1           10      10      10      10                
//7)After modify + AcceptChanges          Unchanged   1           55      55      55      55                
//8)After Delete                          Deleted     1                                   55                
//9)After Delete + AcceptChanges          Detached    0              
```

**範例２**
```c#
DataTable Employees = CreateDataTable();
Employees.Rows.Add("001", "AAA", "BBB", 10);
Employees.Rows.Add("002", "CCC", "DDD", 20);
Employees.AcceptChanges();

DataRow emp = Employees.Rows[1];

DataRowState rowState;
String LastName = "";

rowState = emp.RowState;    //Unchanged

emp["LastName"] = "EEE";
rowState = emp.RowState;    //Modified

emp.AcceptChanges();
rowState = emp.RowState;    //Unchanged

emp.BeginEdit();
emp["LastName"] = "FFF";
rowState = emp.RowState;    //Unchanged

LastName = emp["LastName"].ToString();

if (emp.HasVersion(DataRowVersion.Current))
LastName = emp["LastName", DataRowVersion.Current].ToString();

if (emp.HasVersion(DataRowVersion.Original))
LastName = emp["LastName", DataRowVersion.Original].ToString(); 

if (emp.HasVersion(DataRowVersion.Proposed))
LastName = emp["LastName", DataRowVersion.Proposed].ToString(); 

emp.EndEdit();
rowState = emp.RowState;    //Modified

if (emp.HasVersion(DataRowVersion.Current))
LastName = emp["LastName", DataRowVersion.Current].ToString();

if (emp.HasVersion(DataRowVersion.Original))
LastName = emp["LastName", DataRowVersion.Original].ToString();

if (emp.HasVersion(DataRowVersion.Proposed))
LastName = emp["LastName", DataRowVersion.Proposed].ToString();

//                   RowState   LastName   Current Original Proposed
//                   =========  ========== ======= ======== ========
//1)After BeginEdit  Unchanged  EEE        EEE     EEE      FFF
//2)After EndEdit    Modified   FFF        FFF     EEE      無
```

# 轉換 DataTable 與 XML 資料

## DataTable 轉換 XML 相關的方法

- [ReadXml](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.readxml.aspx) ：將 XML 結構描述和資料讀入 DataTable。
- [WriteXml](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.writexml.aspx) ：將 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的目前內容寫成 XML。

## 如何將 DataTable 轉成 XML 文件
```c#
DataTable employee = CreateDataTable();
employee.Rows.Add("987654321X", "vito", "shao", 40);
employee.Rows.Add("A123456789", "allen", "wu", 30);

employee.WriteXml(Server.MapPath("employee1.xml"));
Response.Redirect("employee.xml");
```

下面為輸出結果，其中，由於項目名稱不允許空白，所以自動使用 \_x0020\_ 取代。
```xml
<?xml version="1.0" standalone="yes"?>
<DocumentElement>
<Employee>
<EmpNo>987654321X</EmpNo>
<FirstName>vito</FirstName>
<LastName>shao</LastName>
<Age>40</Age>
<LastName_x0020_and_x0020_FirstName>shao,vito</LastName_x0020_and_x0020_FirstName>
</Employee>
<Employee>
<EmpNo>A123456789</EmpNo>
<FirstName>allen</FirstName>
<LastName>wu</LastName>
<Age>30</Age>
<LastName_x0020_and_x0020_FirstName>wu,allen</LastName_x0020_and_x0020_FirstName>
</Employee>
</DocumentElement>
```

## 如何控制 XML 文件的樣式

變更 [DataColumn](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.aspx) 物件的 [ColumnMapping](http://msdn.microsoft.com/zh-tw/library/system.data.datacolumn.columnmapping.aspx) 屬性，可以控制 XML 文件輸出的樣式

####  [MappingType](http://msdn.microsoft.com/zh-tw/library/system.data.mappingtype.aspx) 列舉值：

- [Attribute](http://msdn.microsoft.com/zh-tw/library/system.data.mappingtype.aspx) ：將欄位對應至 XML 的屬性 (Attribute)。
- [Element](http://msdn.microsoft.com/zh-tw/library/system.data.mappingtype.aspx) ：將欄位對應至 XML 的項目。（預設）
- [Hidden](http://msdn.microsoft.com/zh-tw/library/system.data.mappingtype.aspx) ：不對應欄位。
- [SimpleContent](http://msdn.microsoft.com/zh-tw/library/system.data.mappingtype.aspx) ：將欄位對應至 XmlText 的節點。
```c#
DataTable employee = CreateDataTable();
employee.Rows.Add("987654321X", "vito", "shao", 40);
employee.Rows.Add("A123456789", "allen", "wu", 30);

DataColumnCollection dColumns = employee.Columns;
dColumns["EmpNo"].ColumnMapping = MappingType.Attribute;                //將 EmpNo 以屬性型式輸出
dColumns["FirstName"].ColumnMapping = MappingType.Hidden;               //忽略輸出 FirstName 欄位
dColumns["LastName"].ColumnMapping = MappingType.Hidden;                //忽略輸出 LastName 欄位
dColumns["Age"].ColumnMapping = MappingType.Hidden;                     //忽略輸出 Age 欄位
dColumns["LastName and FirstName"].ColumnName = "FullName";

employee.WriteXml(Server.MapPath("employee2.xml"));
Response.Redirect("employee2.xml");
```

下面為輸出結果：
```xml
<?xml version="1.0" standalone="yes"?>
<DocumentElement>
<Employee EmpNo="987654321X">
<FullName>shao,vito</FullName>
</Employee>
<Employee EmpNo="A123456789">
<FullName>wu,allen</FullName>
</Employee>
</DocumentElement>
```

## 如何將 DataTable 轉成含 Schema 的 XML 文件

上面的例子，我們輸出的 XML 文件並沒有包含資料的型別，所以所有的欄位資料都會被認定為字串。  若要在輸出的 XML 文件包含 Schema 的話，只要在輸出時，多加指定 [XmlWriteMode](http://msdn.microsoft.com/zh-tw/library/system.data.xmlwritemode.aspx) 屬性即可。  
```c#
DataTable employee = CreateDataTable();
employee.Rows.Add("987654321X", "vito", "shao", 40);
employee.Rows.Add("A123456789", "allen", "wu", 30);

DataColumnCollection dColumns = employee.Columns;
dColumns["EmpNo"].ColumnMapping = MappingType.Attribute;                //將 EmpNo 對應成屬性
dColumns["LastName and FirstName"].ColumnMapping = MappingType.Hidden;  //忽略 FullName 欄位

employee.WriteXml(Server.MapPath("employee3.xml"), XmlWriteMode.WriteSchema);
Response.Redirect("employee3.xml");
```

下面為輸出結果：
```xml
<?xml version="1.0" standalone="yes"?>
<NewDataSet>
<xs:schema id="NewDataSet" xmlns="" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata">
<xs:element name="NewDataSet" msdata:IsDataSet="true" msdata:MainDataTable="Employee" msdata:UseCurrentLocale="true">
<xs:complexType>
<xs:choice minOccurs="0" maxOccurs="unbounded">
<xs:element name="Employee">
<xs:complexType>
<xs:sequence>
<xs:element name="FirstName" msdata:Ordinal="1">
<xs:simpleType>
<xs:restriction base="xs:string">
<xs:maxLength value="20" />
</xs:restriction>
</xs:simpleType>
</xs:element>
<xs:element name="LastName" msdata:Ordinal="2">
<xs:simpleType>
<xs:restriction base="xs:string">
<xs:maxLength value="20" />
</xs:restriction>
</xs:simpleType>
</xs:element>
<xs:element name="Age" type="xs:short" default="0" minOccurs="0" msdata:Ordinal="3" />
</xs:sequence>
<xs:attribute name="EmpNo" msdata:DefaultValue="" use="required">
<xs:simpleType>
<xs:restriction base="xs:string">
<xs:maxLength value="10" />
</xs:restriction>
</xs:simpleType>
</xs:attribute>
<xs:attribute name="LastName_x0020_and_x0020_FirstName" msdata:ReadOnly="true" msdata:Expression="LastName + ',' + FirstName" use="prohibited">
<xs:simpleType>
<xs:restriction base="xs:string">
<xs:maxLength value="40" />
</xs:restriction>
</xs:simpleType>
</xs:attribute>
</xs:complexType>
</xs:element>
</xs:choice>
</xs:complexType>
<xs:unique name="Constraint1" msdata:PrimaryKey="true">
<xs:selector xpath=".//Employee" />
<xs:field xpath="@EmpNo" />
</xs:unique>
</xs:element>
</xs:schema>
<Employee EmpNo="987654321X">
<FirstName>vito</FirstName>
<LastName>shao</LastName>
<Age>40</Age>
</Employee>
<Employee EmpNo="A123456789">
<FirstName>allen</FirstName>
<LastName>wu</LastName>
<Age>30</Age>
</Employee>
</NewDataSet>
```

## 將 XML 文件轉 DataTable

透過 [DataTable.ReadXml](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.readxml.aspx) 方法，可以輕易的將 XML 文件轉入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 。  若這個 XML 文件不含 shcema ，則必須先建立 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的樣式，而且要注意，讀取資料時，若資料型別無法對應，也會引發例外狀況。  若使用含 shcema 的 XML 文件，則可以不用建立 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的樣式，就可以直接取得和原本一模一樣的 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 物件。
```c#
public XmlReadMode ReadXml(Stream stream);
public XmlReadMode ReadXml(string fileName);
public XmlReadMode ReadXml(TextReader reader);
public XmlReadMode ReadXml(XmlReader reader);
```
```c#
protected void btnReaXML1_Click(object sender, EventArgs e)
{
DataTable Employee = CreateDataTable();                //先建立 DataTable 樣式
Employee.ReadXml(Server.MapPath("employee1.xml"));  //讀取原始的 XML 文件
gv1.DataSource = Employee;
gv1.DataBind();
}
protected void btnReaXML2_Click(object sender, EventArgs e)
{
DataTable xmlTable = new DataTable();               //不須先建立 DataTable 樣式
xmlTable.ReadXml(Server.MapPath("employee3.xml"));  //可讀取樣式的 XML 文件
gv1.DataSource = xmlTable;
gv1.DataBind();
}
```

# 使用 DataView 物件

## DataView 物件

[DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 就像是 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的檢視表。主要功能可以用它來排序、篩選、搜尋資料。

####  [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 建構子
```c#
public DataView();
public DataView(DataTable table);
public DataView(DataTable table, string RowFilter, string Sort, DataViewRowState RowState);
```

####  [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 屬性

- [AllowDelete](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.allowdelete.aspx) ：是否允許刪除。
- [AllowEdit](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.allowedit.aspx) ：是否允許編輯。
- [AllowNew](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.allownew.aspx) ：是否可以使用 [AddNew](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.addnew.aspx) 方法加入新的資料列。
- [ApplyDefaultSort](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.applydefaultsort.aspx) ：是否要使用預設排序。若設為 true 則使用 PK 欄位做遞增排序。
- [RowFilter](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.rowfilter.aspx) ：指定欄位篩選條件。
- [RowStateFilter](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.rowstatefilter.aspx) ：指定 [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 做為篩選條件。
- [Sort](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.sort.aspx) ：指定排序的欄位。
- [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) ：取得或設定來源 DataTable。

####  [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 方法

- [AddNew](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.addnew.aspx) ：加入新的資料列至 DataView。
- [Delete](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.delete.aspx) ：刪除指定索引處的資料列。
- [Find](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.find.aspx) ：根據指定的排序鍵值，在 [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 中尋找資料列。
- [FindRows](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.findrows.aspx) ：傳回其資料行符合指定排序鍵值的 [DataRowView](http://msdn.microsoft.com/zh-tw/library/system.data.datarowview.aspx) 物件陣列。
- [ToTable](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.totable.aspx) ：根據現有 [DataView](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.aspx) 中的資料列，建立新的 DataTable。
- [CopyTo](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.copyto.aspx) ：將項目複製到陣列。 只針對 Web Form 介面。

## 排序 DataView 

- 若設定 [ApplyDefaultSort](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.applydefaultsort.aspx) = true ，會使用 PK 欄位做遞增排序。
- 若有指定 [Sort](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.sort.aspx) 屬性，則會依據該條件排序。
```c#
DataTable employee = CreateDataTable();
employee.Rows.Add("222", "vito", "shao", 40);
employee.Rows.Add("333", "allen", "wu", 30);
employee.Rows.Add("111", "shao", "vito", 35);

DataView myDataView = new DataView(employee);

myDataView.ApplyDefaultSort = true;

GridView1.DataSource = myDataView;
GridView1.DataBind();
```
```c#
DataView myDataView = new DataView(employee);

myDataView.Sort = "FirstName DESC, Age ASC";

GridView1.DataSource = myDataView;
GridView1.DataBind();
```

## 篩選 DataView 

[DataView.RowFilter](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.rowfilter.aspx) 屬性是用來過濾資料用，其使用語法類似 SQL 中的 Where 子句，但是不用 Where 這個關鍵字。
```c#
DataView myDataView = new DataView(employee);

myDataView.RowFilter = " Age > 30 and FirstName like '%A%' ";
myDataView.Sort = "FirstName DESC";

GridView1.DataSource = myDataView;
GridView1.DataBind();
```

## 搜尋 DataView 

使用 [Find](http://msdn.microsoft.com/zh-tw/library/system.data.dataview.find.aspx) 方法前，必須先指定排序的欄位，因為它是依照排序的欄位進行搜尋，並回傳第一筆符合條件資料的索引。
```c#
protected void btnFind_Click(object sender, EventArgs e)
{
DataTable employee = CreateDataTable();
employee.Rows.Add("222", "vito", "shao", 40);
employee.Rows.Add("333", "allen", "wu", 30);
employee.Rows.Add("111", "shao", "vito", 35);

DataView myDataView = new DataView(employee);

myDataView.Sort = "Age ASC";

int iRowIndex = myDataView.Find("30");

if (iRowIndex == -1)
{
myDebug.WriteLine("找不到符合條件的資料");
}
else
{
DataTable dTable = CreateDataTable();
DataRow dRow = dTable.NewRow();

foreach (DataColumn col in employee.Columns)
{
if (!col.ReadOnly)
dRow[col.ToString()] = myDataView[iRowIndex][col.ToString()];
}
dTable.Rows.Add(dRow);

GridView1.DataSource = dTable;
GridView1.DataBind();
}
}
```

使用多欄位條件搜尋
```c#
myDataView.Sort = "FirstName, LastName";

// Find FirstName + LastName
object[] vals = new object[2];
vals[0] = "vito";
vals[1] = "shao";

int iRowIndex = myDataView.Find(vals);
```

# 使用 DataSet 物件

## DataSet 物件

[DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 是一種使用記憶體來存放關聯性資料的物件。其中包含了 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 和 [DataRelation](http://msdn.microsoft.com/zh-tw/library/system.data.datarelation.aspx) 物件。  透過 [DataRelation](http://msdn.microsoft.com/zh-tw/library/system.data.datarelation.aspx) 物件可以用來建立兩個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 之間的父子關聯性。  

### 如何建立 DataSet 與 DataTable 與 DataRelation
```c#
private DataSet CreateDataSet()
{
//==========================================================
// 在 DataSet 中，加入 DataTable 
//==========================================================

DataTable dtCompany = new DataTable("Company");
dtCompany.Columns.Add("CompanyId", typeof(Guid));
dtCompany.Columns.Add("CompanyName", typeof(string));
dtCompany.PrimaryKey = new DataColumn[] { dtCompany.Columns["CompanyId"] };

DataTable dtEmployee = new DataTable("Employee");
dtEmployee.Columns.Add("EmployeeId", typeof(Guid));
dtEmployee.Columns.Add("CompanyId", typeof(Guid));
dtEmployee.Columns.Add("LastName", typeof(string));
dtEmployee.Columns.Add("FirstName", typeof(string));
dtEmployee.Columns.Add("Salary", typeof(decimal));
dtEmployee.PrimaryKey = new DataColumn[] { dtEmployee.Columns["EmployeeId"] };

DataSet dsCompanyData = new DataSet("CompanyList");
dsCompanyData.Tables.Add(dtCompany);
dsCompanyData.Tables.Add(dtEmployee);

//==========================================================
// 在 DataSet 中，加入 DataRelation 
//==========================================================

DataRelation rlCompanyEmployee = 
new DataRelation("Company_Employee", dtCompany.Columns["CompanyId"], dtEmployee.Columns["CompanyId"]);
dsCompanyData.Relations.Add(rlCompanyEmployee);

return dsCompanyData;
}
```

### 如何將 DataSet 繫結到控制項

1. 設定控制項的 DataSource 屬性。
2. 叫用 DataBind 方法。
3. 可以使用控制項的 DataMember 屬性，指定繫結到特定的 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) ，若沒有指定，會繫結到第一個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 。
```c#
gv1.DataSource = ds;
gv1.DataMember = "Company";
gv1.DataBind();

gv2.DataSource = ds;
gv2.DataMember = "Employee";
gv2.DataBind();
```

## DataRelation 物件

[DataRelation](http://msdn.microsoft.com/zh-tw/library/system.data.datarelation.aspx) 是用來表示 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 之間的關聯性。  透過 [DataRelation](http://msdn.microsoft.com/zh-tw/library/system.data.datarelation.aspx) 可以由 **Parent DataTable** 關聯到 **Child DataTable** ，反之亦可。  

- [DataRow.GetChildRows](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.getchildrows.aspx) ：取得關聯子資料表中相關聯的 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 資料列。
```c#
//建立測試資料
DataSet companyList = CreateDataSet();
PopulateDataSet(companyList);

//取得資料關聯性
DataRelation dRelation = companyList.Relations["Company_Employee"];

//取出父資料表中的一筆公司資料
DataRow companyParent = companyList.Tables["company"].Rows[1];

//透過關連取出與該公司相關聯的員工資料
string tmp = "";
foreach (DataRow employeeChild in companyParent.GetChildRows(dRelation))
{
tmp = employeeChild["LastName"] + " " +
employeeChild["FirstName"] + " " +
string.Format("{0:C}", employeeChild["Salary"]);
myDebug.WriteLine(tmp);
}
```

## 建立條件約束 (Key Constraint)

若父資料表有一個 Primary Key ，通常我們會在子資料表建立 Foreing Key ，以便約束索引欄位的正確性。  或者當父資料表有資料被刪除時，我們希望子資料表中相關聯的資料也自動被刪除，類似這樣的需求都可以透過索引鍵條件約束 (Key Constraint) 來達成。  

### UniqueConstraint

[UniqueConstraint](http://msdn.microsoft.com/zh-tw/library/system.data.uniqueconstraint.aspx) 這個物件可以用來表示欄位值必須唯一的限制。

### ForeignKeyConstraint

[ForeignKeyConstraint](http://msdn.microsoft.com/zh-tw/library/system.data.foreignkeyconstraint.aspx) 這個物件就是用來建立 Fk 的約束行為。以下是它的幾個方法：

- [DeleteRule](http://msdn.microsoft.com/zh-tw/library/system.data.foreignkeyconstraint.deleterule.aspx) ：設定資料列刪除時會跨越這個條件約束發生的動作。
- [UpdateRule](http://msdn.microsoft.com/zh-tw/library/system.data.foreignkeyconstraint.updaterule.aspx) ：設定資料列更新時會跨越這個條件約束發生的動作。
- [AcceptRejectRule](http://msdn.microsoft.com/zh-tw/library/system.data.foreignkeyconstraint.acceptrejectrule.aspx) ：指示在叫用 [AcceptChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.acceptchanges.aspx) 時應該跨越這個條件約束來發生的動作。
```c#
protected void btnCascadingUpdateDelete_Click(object sender, EventArgs e)
{
//父層資料表：建立 Unique Key
//子層資料表：建立 Foreign Key
DataSet companyList = CreateConstraintDataSet();
PopulateDataSet(companyList);

//刪除父層資料表中的資料,則子層資料表中的相關資料也會被刪除
companyList.Tables["Company"].Rows[0].Delete();
BindingDataSet(companyList);
}

private DataSet CreateConstraintDataSet()
{
DataTable dtCompany = new DataTable("Company");
dtCompany.Columns.Add("CompanyId", typeof(Guid));
dtCompany.Columns.Add("CompanyName", typeof(string));
dtCompany.PrimaryKey = new DataColumn[] { dtCompany.Columns["CompanyId"] };

DataTable dtEmployee = new DataTable("Employee");
dtEmployee.Columns.Add("EmployeeId", typeof(Guid));
dtEmployee.Columns.Add("CompanyId", typeof(Guid));
dtEmployee.Columns.Add("LastName", typeof(string));
dtEmployee.Columns.Add("FirstName", typeof(string));
dtEmployee.Columns.Add("Salary", typeof(decimal));
dtEmployee.PrimaryKey = new DataColumn[] { dtEmployee.Columns["EmployeeId"] };

//======================================================
// 建立 DataSet 
//======================================================

DataSet dsCompanyData = new DataSet("CompanyList");
dsCompanyData.Tables.Add(dtCompany);
dsCompanyData.Tables.Add(dtEmployee);

//======================================================
// 建立 UniqueConstraint 
//======================================================

//UniqueConstraint UKey = new UniqueConstraint("UK_CompanyId", dtCompany.Columns["CompanyId"]);
//dtCompany.Constraints.Add(UKey);      // (注意:該欄位若為 PK , 則無法再建立 unique )

//======================================================
// 建立 ForeignKeyConstraint 
//======================================================

ForeignKeyConstraint FKey = new ForeignKeyConstraint("Fk_CompanyId",
dtCompany.Columns["CompanyId"], dtEmployee.Columns["CompanyId"]);

FKey.DeleteRule = Rule.Cascade;         //設定若父層刪除,子層也跟著刪除 
FKey.UpdateRule = Rule.Cascade;         //設定若父層變更,子層也跟著變更
FKey.AcceptRejectRule = AcceptRejectRule.Cascade;

dtEmployee.Constraints.Add(FKey);

return dsCompanyData;
}
```

## 序列化 DataSet 物件

- [WriteXml](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.writexml.aspx) ：將目前 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中的資料寫入指定的 XML 檔案。若參數 [XmlWriteMode](http://msdn.microsoft.com/zh-tw/library/system.data.xmlwritemode.aspx) 設成 WriteSchema ，可連同 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 的結構描述一起輸出。
- WriteXmlSchema ：將目前 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 的結構描述寫入指定的 XML 檔案。
- [ReadXml](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.readxml.aspx) ：讀取指定檔案中的 XML 結構描述和資料。
- ReadXmlSchema ：讀取指定檔案中的 XML 結構描述。

### 將 DataSet 輸出成 XML 文件
```c#
//建立測試資料
DataSet companyList = CreateDataSet();
PopulateDataSet(companyList);

//輸出 DataSet 中的資料到 XML 文件
companyList.WriteXml(MapPath("CompanyList.xml"));

//輸出 DataSet 中的資料和結構描述到 XML 文件
companyList.WriteXml(MapPath("CompanyListSchema.xml"), XmlWriteMode.WriteSchema);

//輸出 DataSet 中的結構描述到 XML 文件
companyList.WriteXmlSchema(MapPath("CompanyListSchema.xsd"));
```

### 將 DataSet 輸出成巢狀的 XML 文件

上面的例子，其輸出的 XML 文件會把 Company 資料會全部集中在一起， Employee 資料也會集中在一起。  如果我們希望在某一筆公司資料底下顯示該公司的員工資料，就可以用設定成巢狀 (Nested) 的 XML 文件。  當然啦，這個功能是由 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中的 [DataRelation](http://msdn.microsoft.com/zh-tw/library/system.data.datarelation.aspx) 所控制的。  
```c#
//建立測試資料
DataSet companyList = CreateDataSet();
PopulateDataSet(companyList);

//設定巢狀的 DataRelation
companyList.Relations["Company_Employee"].Nested = true;

//輸出
companyList.WriteXml(MapPath("CompanyList.xml"));
```

### 將異動過的 DataSe 輸出成 XML 文件

由前面的說明，我們知道， [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中包含一個稱為 [DataRowVersion](http://msdn.microsoft.com/zh-tw/library/system.data.datarowversion.aspx) 的資訊，可用來表示更改前後的不同資訊。  若希望輸出的 XML 文件包含所有的資料，則可以將參數 [XmlWriteMode](http://msdn.microsoft.com/zh-tw/library/system.data.xmlwritemode.aspx) 設成 [DiffGram](http://msdn.microsoft.com/zh-tw/library/ms172088.aspx) 。  

####  [XmlWriteMode](http://msdn.microsoft.com/zh-tw/library/system.data.xmlwritemode.aspx) 列舉：

- WriteSchema ：輸出內容包含資料和結構描述。
- IgnoreSchema ：輸出內容僅含資料。
- DiffGram ：輸出內容包括原始和目前的值。

**範例**：
```c#
//建立測試資料
DataSet companyList = CreateDataSet2();
PopulateDataSet2(companyList);

companyList.Tables["Employee"].AcceptChanges();
companyList.Tables["Employee"].Rows[1]["Salary"] = 0;

// XML 文件
companyList.WriteXml(MapPath("Employee.xml"));

// DiffGram XML 文件
companyList.WriteXml(MapPath("EmployeeDiffGram.xml"), XmlWriteMode.DiffGram);
```

下面為輸出結果：  
第10行顯示該筆資料的狀態為 [Modified](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) 。  
而文件的下半部，則顯示未更改的原始資料。  
```xml
<?xml version="1.0" standalone="yes"?>
<diffgr:diffgram xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" xmlns:diffgr="urn:schemas-microsoft-com:xml-diffgram-v1">
<CompanyList>
<Employee diffgr:id="Employee1" msdata:rowOrder="0">
<EmployeeId>001</EmployeeId>
<LastName>AAA</LastName>
<FirstName>aaa</FirstName>
<Salary>40</Salary>
</Employee>
<Employee diffgr:id="Employee2" msdata:rowOrder="1" diffgr:hasChanges="modified">
<EmployeeId>002</EmployeeId>
<LastName>BBB</LastName>
<FirstName>bbb</FirstName>
<Salary>0</Salary>
</Employee>
<Employee diffgr:id="Employee3" msdata:rowOrder="2">
<EmployeeId>003</EmployeeId>
<LastName>CCC</LastName>
<FirstName>ccc</FirstName>
<Salary>20</Salary>
</Employee>
</CompanyList>
<diffgr:before>
<Employee diffgr:id="Employee2" msdata:rowOrder="1">
<EmployeeId>002</EmployeeId>
<LastName>BBB</LastName>
<FirstName>bbb</FirstName>
<Salary>70</Salary>
</Employee>
</diffgr:before>
</diffgr:diffgram>
```

由上面例子可以知道 DiffGram 會輸出整個 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中的資訊，包含變更過和沒變更過的資料。  若只希望輸出的 XML 文件僅含變更過的資訊，可以使用 [GetChanges](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.getchanges.aspx) 方法，取得變更資料集的複本，再輸出成 XML 文件。

## 還原序列化 DataSet 物件
```c#
//讀取結構描述
DataSet companyList1 = new DataSet();
companyList1.ReadXmlSchema(MapPath("CompanyListSchema.xml"));

//讀取資料檔
companyList1.ReadXml(MapPath("CompanyListData.xml"));

//同時讀取結構描述和資料檔 
DataSet companyList2 = new DataSet();
companyList2.ReadXml(MapPath("CompanyListDataSchema.xml"));
```

## 二進位序列化與還原序列化 DataSet 物件

- 二進位序列化約有 20K 的初始化額外資訊，所以序列化小型的 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 時，檔案不一定會比較小。
- 二進位序列化會自動包含結構描述，所以還原序列化時不需要先載入結構描述。
```c#
// 使用 BinaryFormatter 序列化 DataSet
protected void btnBinarySerializing_Click(object sender, EventArgs e)
{
DataSet companyList = CreateDataSet();
PopulateDataSet(companyList);

// 設定輸出成二進位格式
companyList.RemotingFormat = SerializationFormat.Binary;

using (FileStream fs = new FileStream(MapPath("CompanyList.bin"), FileMode.Create))
{
BinaryFormatter formatter = new BinaryFormatter();
formatter.Serialize(fs, companyList);
}
}

// 使用 BinaryFormatter 還原序列化 DataSet
protected void btnBinaryDeserializing_Click(object sender, EventArgs e)
{
DataSet companyList;

using (FileStream fs = new FileStream(MapPath("CompanyList.bin"), FileMode.Open))
{
BinaryFormatter formatter = new BinaryFormatter();
companyList = (DataSet)formatter.Deserialize(fs);
}
BindingDataSet(companyList);
}
```

## 合併 DataSet
```c#
public void Merge(DataRow[] rows);
public void Merge(DataSet dataSet);
public void Merge(DataTable table);
public void Merge(DataSet dataSet, bool preserveChanges);
public void Merge(DataSet dataSet, bool preserveChanges, MissingSchemaAction missingSchemaAction);
```

- **dataSet**：合併對象。  
當使用 A.Merge(B) 時，表示 A 資料集合併 B 資料集，B 資料集的資料將被併入到 A 資料集中。
- **preserveChanges**：若要保留目前 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中的變更，則為 true，否則為 false。  
當 key 重複時，預設會將 B 資料集中的資料覆寫到 A 資料集中。  
若希望保留 A 資料集的資料，不希望被覆寫，設定 preserveChanges 參數為 true 即可。
- **missingSchemaAction**：合併時，若發生欄位不相符時的處理原則。
```c#
DataSet companyList1 = CreateDataSet2();
PopulateDataSet2(companyList1);

DataSet companyList2 = CreateDataSet2();
PopulateDataSet3(companyList2);

companyList1.AcceptChanges();
companyList2.AcceptChanges();

companyList1.Tables["Employee"].Rows[0]["Salary"] = 0;
companyList1.Merge(companyList2);   

//雖然 companyList1 資料中的 Salary 已改成 0, 但合併後，會被 companyList2 覆寫

companyList1.Merge(companyList2, true);         //若 preserveChanges 設成 true ， 則不會覆寫
```

## 如何由現有的 Datatable 產生 Group By 的 Datatable

- 1. 先建立 Group By 欄位的 Datatable
- 2. 再透過 Relaction 欄位求加總或平均。

下面例子，假說有個員工資料表，你希望依據現有 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 中的資料，以 FirstName 為群組欄位，求得平均年齡。
```c#
//原始資料
DataTable employees = TestData.GetEmployeeDataTable();

//利用原始資料, 建立一個 Distinct FirstName 欄位的 DataTable
DataTable grouped = employees.DefaultView.ToTable("GEmployee", true, new[] { "FirstName" });

DataSet ds = new DataSet("myDataSet");
ds.Tables.Add(employees);
ds.Tables.Add(grouped);

//建立這二個 datatable 的關連
ds.Relations.Add("Relation1", grouped.Columns["FirstName"], employees.Columns["FirstName"]);

//藉由關連欄位，取得子表單的總計 Child 是關鍵字
grouped.Columns.Add("AvgAge", typeof(int), "Avg(Child.Age)");
```

## 如何具名 DataSet 中的 DataTable

當透過查詢取得一個含有多個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) ，通常我們會使用索引值取得每一個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) ，  如果你想要讓每一個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 都有一個名字，可以使用以下的方法：  

#### 取得 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 後為每個 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 命名
```c#
ds.Tables[0].TableName = "Orders";
ds.Tables[1].TableName = "OrderDetails";
```

#### 建立 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 對應表
```c#
myDataAdapter.TableMappings.Add("Table1", "Orders");
myDataAdapter.TableMappings.Add("Table2", "OrderDetails");
myDataAdapter.Fill(myDataSet);
```
## 參考資料  

- [\[ADO.NET\] 如何使用 DataTable / 搜尋 過濾 資料](http://www.dotblogs.com.tw/yc421206/archive/2009/10/20/11152.aspx)
- 
-