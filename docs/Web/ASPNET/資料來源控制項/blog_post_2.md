---
title: 資料來源控制項(3)
layout: default
parent: 資料來源控制項
nav_order: 2
description: "資料來源控制項(3)"
date: 2013-04-09
tags: [Web,ASPNET,資料來源控制項]
postid: "3493029016745377923"
---
# ObjectDataSource

Many Web applications work with a middle tier, or business layer, for retrieving and working with application data.   This middle tier encapsulates database code inside of classes.   Web developers can then call methods on these classes to select, insert, modify, and delete data.  

The [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control can be added to a page and configured to create an instance of a middle-tier object and call its methods to retrieve, insert, update, and delete data.  

#### ObjectDataSource屬性

- [TypeName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.typename.aspx) ：Gets or sets the       **name of the class** that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) object represents.
- [DataObjectTypeName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.dataobjecttypename.aspx) ：Gets or sets the **name of a class** that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control uses       **for a parameter** in an update, insert, or delete data operation, instead of passing individual values from the data-bound control.
- [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) ：Gets or sets the name of the method or function that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control invokes to **retrieve data**.
- [InsertMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.insertmethod.aspx) ：Gets or sets the name of the method or function that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control invokes to **insert data**.
- [UpdateMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.updatemethod.aspx) ：Gets or sets the name of the method or function that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control invokes to **update data**.
- [DeleteMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.deletemethod.aspx) ：Gets or sets the name of the method or function that the [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control invokes to **delete data**.
- [FilterExpression](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.filterexpression.aspx) ：Gets or sets a **filtering expression** that is applied when the method that is specified by the [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) property is called.

## 簡單範例演練

底下這個例子，我們先定義了 ShipperLogic 和 Shipper 物件，並提供查詢、新增、修改、刪除的靜態方法。
```c#
namespace Practice70515.Northwind
{
[DataObject()]
public class ShipperLogic
{
private static string _cnnString =
ConfigurationManager.ConnectionStrings["NorthwindConnectionString"].ToString();

[DataObjectMethod(DataObjectMethodType.Select)]
public static DataTable GetAllShippers()
{
SqlDataAdapter adp = new SqlDataAdapter("SELECT \* FROM Shippers", _cnnString);
DataSet ds = new DataSet("shippers");
adp.Fill(ds, "shippers");
return ds.Tables["shippers"];
}

[DataObjectMethod(DataObjectMethodType.Insert)]
public static void InsertShipers(Shipper shipper)
{

}

[DataObjectMethod(DataObjectMethodType.Delete)]
public static void DeleteShipers(Shipper shipper)
{

}

[DataObjectMethod(DataObjectMethodType.Update)]
public static void UpdateShipers(Shipper shipper)
{
myDebug.Write(shipper.CompanyName);
}
}

public class Shipper
{
public Shipper() { }
public Shipper(int shipperid, string companyname, string phone) 
{
ShipperID = shipperid;
CompanyName = companyname;
Phone = phone;
}

public int ShipperID { get; set; }
public string CompanyName { get; set; }
public string Phone { get; set; }
}
}
```

[ObjectDataSource.TypeName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.typename.aspx) = ShipperLogic ，表示 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 所代表的物件類別。  
[ObjectDataSource.DataObjectTypeName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.dataobjecttypename.aspx) = Shipper ，表示傳給新增、修改、刪除方法的參數型別。  
並將 [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) 設定為該物件的 GetAllShippers 這個方法,...。  
最後放置一個資料繫結控制項，繫結到這個資料來源。  
```xml
<asp:ObjectDataSource ID="ObjectDataSource1" runat="server" 
TypeName="Practice70515.Northwind.ShipperLogic"
DataObjectTypeName="Practice70515.Northwind.Shipper"
SelectMethod="GetAllShippers" 
UpdateMethod="UpdateShipers"
InsertMethod="InsertShipers"
DeleteMethod="DeleteShipers"
>
</asp:ObjectDataSource>

<asp:DetailsView ID="DetailsView1" runat="server"
DataSourceID="ObjectDataSource1"
AllowPaging="True">
<Fields>
<asp:CommandField 
ShowDeleteButton="True" 
ShowEditButton="True" 
ShowInsertButton="True" />
</Fields>
</asp:DetailsView>
```

執行結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1a04rrYiZ42a3r9HUpT7m2HFRi-QPM6Iqjl0-CqkmSIDxFAWgyN2Aehp983XOrg_OdIEKkWWuf7npE11eJHNWHPBuf3cg-VL1WAe1YBdk-w1fhfcq35o3ystM3I30dR5rwZ1kfLAtJng/s247/ctl-object-datasource.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuxt_e9X_33zZ6UC0ujN5H7hmIjynTwDrJ3fUkZIvsXF00FviHfmKUjDttuyFbpHloPTpnOAAOanKK9BrzqoWYeWCfmcrJdtF4R8BDnnmmnZlNeoTbMg_URaky5Lj4obOcPiLxfov3JCE/s301/ctl-object-datasource2.png)

#### 注意：

上面這個例子中，Shipper.GetAllShippers 這個方法回傳的是一個 DataTable 。  實際上只要有實作 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 、 [IListSource](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.ilistsource.aspx) 、 [IDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.idatasource.aspx) 、 [IHierarchicalDatasource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ihierarchicaldatasource.aspx) 介面的資料， [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 都可以處理。  也就是說，像 DataTable 、 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 、 Collection 等等，都可以被 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 使用。

## Passing Parameters

提供給 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 使用的參數集合有好幾個，每一種參數物件都會對應到一種操作方法。  例如：  

- &lt;SelectParameters&gt;：是給 [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) 屬性用的。
- &lt;InsertParameters&gt;：是給 [InsertMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.insertmethod.aspx) 屬性用的。

參數值的來源可以是頁面上的各種物件，例如：Cookie, Control, Session, QueryString, Form, or Profile object。例如：  

- [QueryStringParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.querystringparameter.aspx) ：
- [CookieParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.cookieparameter.aspx) ：
- [ControlParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.controlparameter.aspx) ：
- [FormParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.formparameter.aspx) ：
- [ProfileParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.profileparameter.aspx) ：
- [SessionParameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sessionparameter.aspx) ：
- [Parameter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.parameter.aspx) ：所有 ASP.NET 參數型別的基底類別

底下這個例子是一個使用 [QueryString](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.querystring.aspx) 當做參數來源的例子：
```xml
<asp:ObjectDataSource ID="ObjectDataSource2" runat="server" 
TypeName="Practice70515.ClassObject.Employees" 
SelectMethod="GetEmployee" OldValuesParameterFormatString="original_{0}">
<SelectParameters>
<asp:QueryStringParameter 
Name="EmployeeID"           //參數的名稱
QueryStringField="EID"      //設定參數所繫結至的查詢字串欄位名稱。
Type="Int32" 
DefaultValue="0" />
</SelectParameters>
</asp:ObjectDataSource>

<asp:DetailsView ID="DetailsView2" runat="server"
DataSourceID="ObjectDataSource2"
AllowPaging="True">
</asp:DetailsView>
```
```c#
namespace Practice70515.ClassObject
{
public class Employees
{
private static string _cnnString =
ConfigurationManager.ConnectionStrings["NorthwindConnectionString"].ToString();
[DataObjectMethod(DataObjectMethodType.Select)]
public static DataTable GetEmployee(int EmployeeID)
{
string query = "";
if (EmployeeID != 0)
query = string.Format("SELECT \* FROM Employees WHERE EmployeeID = '{0}'", EmployeeID);
else
query = "SELECT \* FROM Employees";
SqlDataAdapter adp = new SqlDataAdapter(query, _cnnString);
DataSet ds = new DataSet("Employees");
adp.Fill(ds, "Employees");
return ds.Tables["Employees"];
}
｝
｝
```

接著把這個 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 設定給 [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 一類的控制項上，你就可以透過以下的 URL ，將參數傳遞給查詢的方法。  
```xml
http://localhost:61006/WebForm1.aspx?EID=3
```

## Inserting, Updating, and Deleting

The attributes [InsertMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.insertmethod.aspx) , [UpdateMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.updatemethod.aspx) , and [DeleteMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.deletemethod.aspx) can be mapped directly to an object's methods   that are to be called when these activities are invoked.   You then use parameter definitions to map values to these method calls.  
```xml
<asp:ObjectDataSource ID="ObjectDataSource2" runat="server" 
TypeName="Practice70515.ClassObject.Employees" 
DataObjectTypeName="xBLLibrary.BLL.DBA" 
SelectMethod="GetEmployee" 
DeleteMethod="DeleteEmployee" 
InsertMethod="InsertEmployee" 
UpdateMethod="UpdateEmployee" OldValuesParameterFormatString="original_{0}">
<DeleteParameters>
<asp:Parameter Name="EmployeeID" Type="Int32" />
</DeleteParameters>
<InsertParameters>
<asp:Parameter Name="FirstName" Type="String" />
<asp:Parameter Name="LastName" Type="String" />
</InsertParameters>
<SelectParameters>
<asp:QueryStringParameter Name="QueryPrams" QueryStringField="EID"  Type="Int32" />
</SelectParameters>
</asp:ObjectDataSource>
```

####  [OldValuesParameterFormatString](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.oldvaluesparameterformatstring.aspx) 屬性

[ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 的 [OldValuesParameterFormatString](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.oldvaluesparameterformatstring.aspx) 屬性是用來設定傳遞給 Delete 或 Update 方法參數名稱的格式。  它會將資料繫結控制項的 DataKeyNames 屬性中的欄位名稱以指定的格式名稱傳遞給 Delete 或 Update 方法。   
```xml
<asp:ObjectDataSource ID="ObjectDataSource2" runat="server" 
TypeName="Practice70515.ClassObject.Employees" 
SelectMethod="GetEmployee" 
DeleteMethod="DeleteEmployee" 
InsertMethod="InsertEmployee" 
UpdateMethod="UpdateEmployee"
OldValuesParameterFormatString="original_{0}">
<SelectParameters>
<asp:QueryStringParameter Name="EmployeeID" QueryStringField="EID" Type="Int32" />
</SelectParameters>
<InsertParameters>
<asp:Parameter Name="FirstName" Type="String" />
<asp:Parameter Name="LastName" Type="String" />
</InsertParameters>
<UpdateParameters>
<asp:Parameter Name="FirstName" Type="String" />
<asp:Parameter Name="LastName" Type="String" />
<asp:Parameter Name="Title" Type="String" />
<asp:Parameter Name="BirthDate" Type="DateTime" />
</UpdateParameters>
</asp:ObjectDataSource>

<asp:DetailsView ID="DetailsView2" runat="server"
DataSourceID="ObjectDataSource2"
DataKeyNames="EmployeeID"
AutoGenerateRows="false"
AllowPaging="True">
<Fields>
<asp:BoundField DataField="EmployeeID" HeaderText="EmployeeID" InsertVisible="false" ReadOnly="true" />
<asp:BoundField DataField="FirstName"  HeaderText="FirstName"/>
<asp:BoundField DataField="LastName"   HeaderText="LastName"/>
<asp:BoundField DataField="Title"      HeaderText="Title"      InsertVisible="false" />
<asp:BoundField DataField="BirthDate"  HeaderText="BirthDate"  InsertVisible="false" />
<asp:CommandField
ShowInsertButton = "true" 
ShowEditButton = "true" 
ShowDeleteButton = "true" />
</Fields>
</asp:DetailsView>
```

在這個例子中，[ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 的 UpdateParametersAttribute 定義了四個參數。  在 [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) 中的 DataKeyNames 設定為 EmployeeID，所以這個欄位的值也會以特定的參數名稱格式 (original\_{0})，傳遞到 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 指定的 Delete 或 Update 方法。  因此在資料類別中的 Update 方法，就必須包含這些參數項。  
在這個例子中，[ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 並沒有指定 DeleteParametersAttribute ，但因為 [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) 中的 DataKeyNames 設定為 EmployeeID ，所以在資料類別中的 Delete 方法，就必須包含接收 EmployeeID 的參數項。    
```c#
[DataObjectMethod(DataObjectMethodType.Update)]
public static int UpdateEmployee(string FirstName, string LastName, string Title, DateTime BirthDate, int original_EmployeeID )
{
string sSQL = "Update Employees" + Environment.NewLine + 
"SET" +
"  FirstName = @FirstName," +
"  LastName  = @LastName ," +
"  Title = @Title," +
"  BirthDate = @BirthDate" + Environment.NewLine + 
"WHERE" +
"  EmployeeID = @EmployeeID";
SqlConnection oConn = new SqlConnection(_cnnString);
SqlCommand oCmd = new SqlCommand(sSQL, oConn);
oCmd.Parameters.Add("@EmployeeID", SqlDbType.Int).Value = original_EmployeeID;
oCmd.Parameters.Add("@FirstName", SqlDbType.NVarChar, 10).Value = FirstName;
oCmd.Parameters.Add("@LastName", SqlDbType.NVarChar, 20).Value = LastName;
oCmd.Parameters.Add("@Title", SqlDbType.NVarChar, 30).Value = Title;
oCmd.Parameters.Add("@BirthDate", SqlDbType.DateTime).Value = BirthDate;

int result = 0;

try
{
oConn.Open();
result = oCmd.ExecuteNonQuery();
}
catch (SqlException e)
{
myDebug.ResponseBR(e.Message);
}
finally
{
oConn.Close();
}
return result;
}

[DataObjectMethod(DataObjectMethodType.Delete)]
public static void DeleteEmployee(int original_EmployeeID)
{
myDebug.Write(original_EmployeeID.ToString());
}
```

## Defining a Filter

To define a filter for an [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control, you set the [FilterExpression](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.filterexpression.aspx) attribute to a valid filter.   This filter will be applied after the data is retrieved from the database.  You can also use FilterParameters to map values from the page to the filter expression by defining a filter expression.  
```xml
<asp:ObjectDataSource ID="ObjectDataSource3" runat="server" 
TypeName="Practice70515.ClassObject.Employees" 
SelectMethod="GetAllEmployees"
FilterExpression="EmployeeID='{0}'">
<FilterParameters>
<asp:QueryStringParameter Name="EmployeeID" QueryStringField="EID" Type="Int32" />
</FilterParameters>
</asp:ObjectDataSource>
```

## Sorting and Paging

當查詢資料的結果可能很大時，使用分頁就變成是必要的技巧。  分頁可以在查詢時就做好，再丟給繫結控制項。  也可以將所有查詢結果都丟給繫結控制項，由繫結控制項自行控制顯示資訊。  當然，在查詢時就做好，再丟給繫結控制項效能會是比較好的。  

#### 自訂分頁

在瞭解 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 的分頁功能前，我們先來看看如何自訂一個分頁查詢。

- 1. 首先在 BL 物件中提供一個接受 PageIndex 和 PageSize 參數的查詢方法。
- 2. 叫用 [ObjectDataSource.Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) 以引發 [Selecting](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selecting.aspx) 事件。
- 3. 在 [Selecting](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selecting.aspx) 事件中，將頁面上的 PageIndex 和 PageSize 傳遞給 BL 物件中的查詢方法。
- 4. 在 [Selected](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selected.aspx) 事件中，取得 BL 物件查詢方法的回傳值。

程式碼範如下：
```c#
[DataObjectMethod(DataObjectMethodType.Select)]
public static DataTable GetPagedCustomers(int pageStart, int pageSize, string sortCol)
{
if (pageSize <= 0)
pageSize = 10;

string sSQL = "SELECT \* FROM Customers";
if (sortCol != "")
sSQL = sSQL + " Order BY " + sortCol;

SqlDataAdapter adp = new SqlDataAdapter(sSQL, _cnnString);
DataSet ds = new DataSet("Customers");
adp.Fill(ds, pageStart, pageSize, "Customers");
return ds.Tables["Customers"];
}
```
```c#
protected void btnPageQuery_Click(object sender, EventArgs e)
{
ObjectDataSource4.Select();
}

protected void ObjectDataSource4_Selecting(object sender, ObjectDataSourceSelectingEventArgs e)
{
int pageStart, pageSize, pageIndex;
pageSize = int.Parse(txtPageSize.Text == "" ? "0" : txtPageSize.Text);
pageIndex = int.Parse(txtPageIndex.Text == "" ? "0" : txtPageIndex.Text);
pageStart = (pageIndex - 1) \* pageSize;
e.InputParameters["pageStart"] = pageStart;
e.InputParameters["pageSize"] = pageSize;
}

protected void ObjectDataSource4_Selected(object sender, ObjectDataSourceStatusEventArgs e)
{
DataTable dt = (DataTable)e.ReturnValue;
GridView1.DataSource = dt;
GridView1.DataBind();
}
```
```xml
頁數:<asp:TextBox ID="txtPageIndex" runat="server"></asp:TextBox> 
每頁筆數:<asp:TextBox ID="txtPageSize" runat="server"></asp:TextBox>
<asp:Button ID="btnPageQuery" runat="server" Text="Query" onclick="btnPageQuery_Click" />

<asp:ObjectDataSource ID="ObjectDataSource4" runat="server"
TypeName = "Practice70515.Northwind.Customers"
SelectMethod = "GetPagedCustomers"
onselecting="ObjectDataSource4_Selecting" 
onselected="ObjectDataSource4_Selected"
>
<SelectParameters>
<asp:Parameter Name="pageStart" Type="String" DefaultValue="0" />
<asp:Parameter Name="pageSize" Type="String" DefaultValue="6" />
<asp:Parameter Name="sortCol" Type="String" DefaultValue="CustomerID" />
</SelectParameters>
</asp:ObjectDataSource>
<asp:GridView ID="GridView1" runat="server"></asp:GridView>
```

#### 在 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 做好分頁

以上做法只是為了瞭解 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 叫用 [Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) 方法的步驟，以及瞭解如何在程式中設定 [Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) 方法的參數值。

當我們搭配繫結控制項時，這些動作都會由繫結控制項去執行，我們只要做好相關的屬性設定即可。

When the page is run, the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) control passes sort and paging information to the data source.   However, because the paging is happening before the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) is bound, the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) does not know the number of pages to display.   Therefore, you need to implement your own custom paging in this scenario to advance the PageIndex property of the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) control on user request.   Once you reset this value, the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) will pass the value on to the ObjectDataSource.  

#### [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 中和分頁或排序相關的屬性：

- [MaximumRowsParameterName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.maximumrowsparametername.aspx) ：Gets or sets the name of the business object data retrieval method parameter that is used to indicate the number of records to retrieve for data source paging support.
- [StartRowIndexParameterName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.startrowindexparametername.aspx) ：Gets or sets the name of the data retrieval method parameter that is used to indicate the value of the identifier of the first record to retrieve for data source paging support.
- [SortParameterName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.sortparametername.aspx) ：Gets or sets the name of the business object that the [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) parameter used to specify a sort expression for data source sorting support.
- [SelectCountMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectcountmethod.aspx) ：設定擷取資料總筆數的函式名稱
- [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) ：設定擷取資料的函式名稱
```xml
<asp:ObjectDataSource ID="ObjectDataSource7" runat="server"
TypeName = "Practice70515.Northwind.Customers"
SelectMethod                = "GetPagedCustomers" 
SelectCountMethod           = "GetCustomerCount" 
EnablePaging                = "true"
StartRowIndexParameterName  = "pageStart"
MaximumRowsParameterName    = "pageSize" 
SortParameterName           = "sortCol" >
</asp:ObjectDataSource>
<asp:GridView ID="GridView7" runat="server" 
DataSourceID="ObjectDataSource7" 
AllowPaging="true" 
AllowSorting="true" 
PageSize="6" >
</asp:GridView>
```
```c#
public class Customers
{
[DataObjectMethod(DataObjectMethodType.Select)]
public static DataTable GetPagedCustomers(int pageStart, int pageSize, string sortCol)
{
if (pageSize <= 0)
pageSize = 10;

string sSQL = "SELECT \* FROM customers";
if (sortCol != "")
sSQL = sSQL + " Order BY " + sortCol;

SqlDataAdapter adp = new SqlDataAdapter(sSQL, _cnnString);
DataSet ds = new DataSet("Customers");
adp.Fill(ds, pageStart, pageSize, "Customers");
return ds.Tables["Customers"];
}

public int GetCustomerCount()
{
string sSQL = "SELECT COUNT(\*) FROM Customers";

DBService db = DBService.CreateService();
return (int) db.DBObject.GetScalar(sSQL);
}
}
```

#### 在 [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 才做分頁

底下這個範例也可以達到分頁效果，它在 BL 物件中是叫用 GetAllCustomers 取得所有資料，再交由 [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 控制項執行分頁效果。
```xml
<asp:ObjectDataSource ID="ObjectDataSource5" runat="server"
TypeName="Practice70515.Northwind.Customers"
SelectMethod = "GetAllCustomers">
</asp:ObjectDataSource>
<asp:GridView ID="GridView2" runat="server" DataSourceID="ObjectDataSource5"
AllowPaging="true" 
AllowSorting="true" 
PageSize="5">
</asp:GridView>
```

## Caching Data

You can tell ASP.NET to cache your [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control. This will keep the data in memory between page calls.   This can increase performance and scalability of your application if the data is shared and accessed often.  

- [EnableCaching](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.enablecaching.aspx) ：設定 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 控制項是否啟用快取的資料。
- [CacheDuration](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.enablecaching.aspx) ：設定 [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) 控制項 [SelectMethod](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.selectmethod.aspx) 方法快取的時間長度，以秒為單位。
```xml
<asp:ObjectDataSource ID="ObjectDataSource5" runat="server"
TypeName="Practice70515.Northwind.Customers"
SelectMethod = "GetAllCustomers" 
EnableCaching = "true" 
CacheDuration = "60" >
</asp:ObjectDataSource>
```

## Creating a DataObject Class

If you know your business object will be used as an ObjectDataSource,  you can define attributes on your class that make consuming your class inside an [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) easier inside the designer.   These attributes are used to predefine which methods you intend as Select, Insert, Update, and Delete.  

- [DataObject](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataobjectattribute.aspx) ：這個宣告可以指明該類別是一個 [DataObject](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataobjectattribute.aspx) 。
- [DataObjectMethod](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataobjectmethodattribute.aspx) ：這個屬性宣告可以指明該方法是屬於什麼類型的方法。{ Fill | [Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) | Update | Insert | Delete }

By defining these attributes, you make the designer aware of your business object's intentions.  This can ease the burden of configuring an [ObjectDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.aspx) control when using large business objects with many methods.  
```c#
namespace Practice70515.ClassObject
{
[DataObject()]
public class Shipper
{
[DataObjectMethod(DataObjectMethodType.Select)]
public static DataTable GetAllShippers()
{
...
}
}
}
```

# LinqDataSource

這個 [LinqDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.aspx) 控制項允許在 ASP.NET 網頁中使用 LINQ 語法存取資料物件(data object)中的資料。   這裡的資料物件可以是記憶中的資料集合或者是表示資料庫資料的物件。  

- [ContextTypeName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.contexttypename.aspx) ：取得或設定資料內容類別。
- [TableName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.tablename.aspx) ：取得或設定要擷取的資料的資料表名稱。
```xml
<asp:LinqDataSource ID="LinqDataSource1" runat="server"
ContextTypeName="Practice70515.C03_Data_Source_and_Data_Bound_Controls.NorthwindDataContext"
TableName="Customers"
EnableDelete="True"
EnableInsert="True"
EnableUpdate="True"
OrderBy="CustomerID"
Where="Country == @Country">
<WhereParameters>
<asp:QueryStringParameter DefaultValue="USA" Name="Country" QueryStringField="country" Type="String" />
</WhereParameters>
</asp:LinqDataSource>
<asp:GridView ID="GridView1" runat="server" DataSourceID="LinqDataSource1" AllowPaging="true" PageSize="5" >
</asp:GridView>
```

[LinqDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.aspx) 的使用方法大至同其它的 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datasource.aspx) 控制項，不過，關於查詢的部份，就有其獨特的 LINQ-style 宣告。  例如上例中的 [TableName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.tablename.aspx) 、 [OrderBy](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.orderby.aspx) 、 [Where](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.where.aspx) 等屬性。

# EntityDataSource

The [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) control works much like the [SqlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sqldatasource.aspx) control.   However, the [SqlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sqldatasource.aspx) control is tied to a specific database model,   whereas the [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) control works with an **Entity Data Model** that gets mapped to an actual data store.  

下面程式碼只是一個 **Entity Data Model** 外觀，   建立實體模型的詳細作法可參考 [LINQ to Entities](http://127.0.0.1:8080/Documents/70562/LINQ/LINQ-and-ADONET.aspx)  
```c#
using System.Data.Objects;
using System.Data.Objects.DataClasses;

namespace Northwind_EDMX
{
public partial class MyEntity : ObjectContext
{    }

public partial class Customers : EntityObject
{    }
public partial class Employees : EntityObject
{    }
public partial class Order_Details : EntityObject
{    }
public partial class Orders : EntityObject
{    }
}
```

## 簡單範例演練

要設定 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) ，有以下幾個屬性要注意：

- [EntitySetName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.entitysetname.aspx) ：設定 Entity 物件名稱。
- [ConnectionString](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.connectionstring.aspx) ：設定使用 Provider 為        **System.Data.EntityClient** 的連接字串。這個連接字串是由建立模型時產生的，字串裡面包含了描述物件的 metadata 的位置。
- [DefaultContainerName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.defaultcontainername.aspx) ：預設容器名稱
```xml
<asp:EntityDataSource ID="EntityDataSource7" runat="server"
ConnectionString="name=MyEntity" 
DefaultContainerName="MyEntity"
EntitySetName="Customers" >
</asp:EntityDataSource>
<asp:GridView ID="gv" runat="server" AllowPaging="True" DataSourceID="EntityDataSource7" 
AutoGenerateColumns="False" AllowSorting="True" 
DataKeyNames="CustomerID" >
<Columns>
<asp:CommandField ShowEditButton="True" ShowSelectButton="True" />
<asp:BoundField DataField="CompanyName" HeaderText="CompanyName" SortExpression="CompanyName" />
<asp:BoundField DataField="ContactName" HeaderText="ContactName" SortExpression="ContactName" />
<asp:BoundField DataField="ContactTitle" HeaderText="ContactTitle" SortExpression="ContactTitle" />
</Columns>
</asp:GridView>
```

You can also work with the data source controls from code.
```c#
protected void Page_Init(object sender, EventArgs e)
{
EntityDataSource dsEntity = new EntityDataSource();

dsEntity.ConnectionString = "name = NorthwindEntities";
dsEntity.DefaultContainerName = "NorthwindEntities";
dsEntity.EntitySetName = "Customers";

gv.DataSource = dsEntity;
gv.DataBind();
}
```

## Selecting Data to Return

The **Select** property allow you to define a string in this property that will be passed to ObjectQuery&lt;T&gt;.   In this way, you can write LINQ to Entities code against the data source for the **Select**, **Where**, and **OrderBy** attributes.  

### Using Select Attribute

這個範例，使用 **Select** 屬性，指定要選取的特定欄位，並且 rename 成特定名稱。  
```xml
<asp:EntityDataSource ID="EntityDataSource2" runat="server"
ConnectionString = "name=MyEntity" 
DefaultContainerName = "MyEntity"
EntitySetName = "Order_Details"
Select = "it.OrderId as Id, it.UnitPrice as Price, it.Quantity as Qty, it.UnitPrice \* it.Quantity as Total"
Where = "it.Quantity > 0" >
</asp:EntityDataSource>
```

#### What is "it" ?

上面例子中，我們在 [Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) 和 [Where](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.where.aspx) 屬性中，使用 it 這個名稱，這個 "it" 是什麼呢？  

它其實很簡單，就是查詢語法中，傳回的投射（projection）物件的別名。  先參考一下下面的例子，我們使用 o 當作 Order\_Details 的別名，這是因為我們使用自訂 Command 的方法，所以可以自訂別名。  但上面例子，我們使用 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 內定的查詢，當然就得使用 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 內定的預設別名，所以這是固定的別名，不可以改。

### Specifying a Custom Query

這個範例，使用 **CommandText** 屬性，設定一個 Entity SQL Expression 的自訂查詢，以篩選資料。這個 Expression 和 LINQ to Entity 語法不同。  
```xml
<asp:EntityDataSource ID="EntityDataSource3" runat="server"
ConnectionString="name=MyEntity" DefaultContainerName="MyEntity"
CommandText="
Select o.OrderId, o.UnitPrice, o.Quantity, (o.UnitPrice \* o.Quantity) as Total 
From Order_Details as o
Where o.Discount > 0
Order by o.ProductId ">
</asp:EntityDataSource>
```

### Specifying Additional Objects to Return

You can use **Include** property to return the additional object collections.  
```xml
<asp:EntityDataSource ID="EntityDataSource8" runat="server"
ConnectionString="name=MyEntity" 
DefaultContainerName="MyEntity"
EntitySetName="Orders"
Include="Order_Details">
</asp:EntityDataSource>
```

## Ordering & Filtering Results

You can use   **OrderBy** property to specify an order and   **Where** property to filter data.  
```xml
<asp:EntityDataSource ID="EntityDataSource4" runat="server"
ConnectionString="name=MyEntity" 
DefaultContainerName="MyEntity"
EntitySetName  = "Order_Details"
Select         = "it.OrderId as Id, it.UnitPrice as Price, it.Quantity as Qty, it.UnitPrice \* it.Quantity as Total"
OrderBy        = "it.Id"
Where          = "it.Discount > 0"
>
</asp:EntityDataSource>
```

## Defining Parameters

The [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) control allows you to define several parameters that can be used with the **@paramName** syntax inside various properties, including Select, Where, and CommandText.  

These ParameterCollection objects include **CommandParameters**,       **DeleteParameters**,  **OrderByParameters**, **SelectParameters**, **UpdateParameters**, and       **WhereParameters**.  

Each of these parameter collections might contain an ASP.NET parameter control such as       **ControlParameter**, **FormParameter**, **QueryStringParameter**,       **CookieParameter**, and similar parameters.   This allows you to pass values from your website dynamically to the [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) control.  

[Where](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.linqdatasource.where.aspx) 屬性的條件值可以透過 Controls、Form、QueryString、Cookie 等來源取得。  下面這個例子，我們直接利用頁面上 TextBoxPrice 控制項的值，當作 WhereParameters 的數據來源。  
```xml
<asp:TextBox ID="TextBoxPrice" runat="server" Text="100"></asp:TextBox>
<asp:EntityDataSource ID="EntityDataSource5" runat="server"
ConnectionString="name=MyEntity" 
DefaultContainerName="MyEntity"
EntitySetName  = "Order_Details"
Select         = "it.OrderId as Id, it.UnitPrice as Price, it.Quantity as Qty, it.UnitPrice \* it.Quantity as Total"
OrderBy        = "it.Id"
Where          = "it.UnitPrice > @UnitPrice"
>
<WhereParameters>
<asp:ControlParameter ControlID="TextBoxPrice" Name="UnitPrice"
DbType="Int32" PropertyName="Text" DefaultValue="0" />
</WhereParameters>
</asp:EntityDataSource>
```

## Paging, Sorting, Editing, and Updating Data

The [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) control supports a few additional attributes that you can use to enable specific scenarios.  
```xml
<asp:EntityDataSource ID="EntityDataSource6" runat="server"
ConnectionString="name=MyEntity" 
DefaultContainerName="MyEntity"
EntitySetName  = "Order_Details"
EnableInsert="true" 
EnableUpdate="true"
EnableDelete="true" 
AutoPage="true" 
AutoSort="true"
>
</asp:EntityDataSource>
<asp:GridView ID="GridView6" runat="server"
AllowPaging="true" AllowSorting="true"
DataSourceID="EntityDataSource6">
<Columns>
<asp:CommandField ShowDeleteButton="True" ShowEditButton="True" ShowSelectButton="True" />
</Columns>
</asp:GridView>
```

## 簡維物件 (Flattening Objects)

當使用 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 控制項時，我們會使用 [EntitySetName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.entitysetname.aspx) 屬性指定 Entity 物件名稱，  此時 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 預設會將每一個 Entity 的 instance 包裝成 [ICustomTypeDescriptor](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.icustomtypedescriptor.aspx) 型別的物件，稱為[簡維物件](http://msdn.microsoft.com/zh-tw/library/ee404746.aspx) (Flattening Objects)。  

預設 [EnableFlattening](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.enableflattening.aspx) 屬性為 True ，若設成 False ，則 entity object 不會被包裝成簡維物件。  

另外，若 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 是使用 CommandText 建立查詢，或者使用 [Select](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.objectdatasource.select.aspx) 屬性產生投影查詢，  其回傳結果是[匿名型別集合(Anonymous Type)](http://msdn.microsoft.com/zh-tw/library/bb738512%28v=vs.100%29.aspx)，此時 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 將不會包裝實體物件。  

下面範例示範如何取得包裝函式物件中的實體物件  
```c#
protected void GridView1_RowDataBound(object sender, GridViewRowEventArgs e)
{
var dataItem = e.Row.DataItem;
var entity = dataItem as Orders;
if (entity == null)
{
var td = dataItem as ICustomTypeDescriptor;
if (td != null)
{
entity = (Orders)td.GetPropertyOwner(null);
myDebug.WriteLine("OrderID:{0}, OrderDate:{1}", entity.OrderID, entity.OrderDate);
}
}
}
//OrderID:10248, OrderDate:1996/7/3
//OrderID:10249, OrderDate:1996/7/5
//OrderID:10250, OrderDate:1996/7/8
```