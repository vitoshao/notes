---
title: LINQ to SQL
layout: default
parent: LINQ
nav_order: 6
description: "LINQ to SQL"
date: 2013-04-09
tags: [DotNetFramework,LINQ]
postid: "8593682703683567710"
---
由上節說明（[Link to DataSet](http://vito-note.blogspot.com/2013/04/linq-to-dataset.html)）可以知道中，透過 LINQ 可以很快速地查詢 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 中的靜態資料。  但是這樣子並沒有顯現出 LINQ 最大的功能，那就是：設計階段強型別檢查 (strong type-checking at design time)。  

要使用 LINQ to SQL 時，必須先建立連結到資料庫元素的 O/R map 。  這個 map 一旦建立，存取資料庫的語法就好像在寫物件式的程式碼，就變的既簡單又快速。  

使用 LINQ to SQL 的幾個必須步驟：  

1. Add [System.Data.Linq](http://msdn.microsoft.com/zh-tw/library/system.data.linq.aspx) namespace which contains the [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) object, which is the core object that connects the database
2. Create an O/R map that connects objects to data tables, columns, and more.
3. Connect to your database using the [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) object of LINQ to SQL.
4. Use the features of LINQ and LINQ to SQL to work with your database.   
This includes writing queries using strongly typed objects.       In addition, you can use the features of LINQ to SQL to insert, update, and delete data.

# Mapping Objects to Relational Data

要使用物件來表達資料庫，首要工作就是建立相對應的物件。  底下三種方法都是用來建立 O/R map 物件的方法  

## Mapping with the Visual Studio Designer Tool

建立 O/R map 最簡單的方法就是透過 Visual Studio 的設計工具，它可以將資料庫物件直接拖拉到設計工具上，然後自動的幫我們建立資料庫物件類別。  

The [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) Classes file is of type \*.dbml.   The file contains XML that defines the metadata of your database.  Behind this XML file, you will find a layout file for use by the designer (.layout), and a code file (.vb or .cs).  The code file contains the actual objects against which you write your database code.  

### 新增 LINQ to SQL 類別檔

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1h3dj7q0xY1ZwMIAObfVUHeUNWD8ENKs2LhnbUf1xs2Kj4wDduW8fgVVYLLAY43XUNpCdAZ__Pkg_lM4q9ARLfmLFGmQV9-gjMd0OeWRbgDtyv9buuc4UA-9O2qChyK9mjJwYgpIIJdE/s537/LinqToSql-add-class.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiYMHg8azb95X-6t3qbEodTEmDsfcuGR-8GFrF7Q4xzVso8PEWLyMEVXcmSd9tvNX12gz9iXHGFVmeEdpH01oUoDKsFoa81glo4-gtunfxwX6SrFZCKq-2a0Xu-gZLknS-mN1poExZacw/s244/LinqToSql-files.png)  

#### LINQ to SQL 設計工具

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNb9qSsbVelV_P1Ce_rDEqr8G8PZLj5TOKs9INWHkTE26jqEJ8ZCIttREWKDeeNcGvKzCInFMwy0GieW3AZ8oU4fXCg-RCVRuX6yopVNjpQMnJJpKldXodR_AXA0kMj748_rVDNaSQzqU/s850/LinqToSql-designer.png)

### 建立的 DataContext 內容

#### 底下類別是工具自動建立的物件內容

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjt-gdRd0d8iEdO0n9mEI8gwD_Wyda9B6K_NpNDFLlq23oYqhS_riffon9Dev7U5FjQkEwMhA1SsZ_Mr5mnk5OyvBxakld9prO1ecRjEm2-bH_Md6hX0g3hBRbsxYvHr0cQXQhI2I-aqdc/s793/LinqToSql-dbml-context.png)

#### 精簡後的 DataContext 類別 (\*.dbml)
```c#
using System.Data.Linq.Mapping;

namespace Northwind_DBML
{
public partial class NorthwindDataContext : DataContext
{
private static MappingSource mappingSource = new AttributeMappingSource();

#region 擴充性方法定義
partial void OnCreated();
partial void InsertOrders(Orders instance);
partial void UpdateOrders(Orders instance);
partial void DeleteOrders(Orders instance);
partial void InsertOrder_Details(Order_Details instance);
partial void UpdateOrder_Details(Order_Details instance);
partial void DeleteOrder_Details(Order_Details instance);
partial void InsertProducts(Products instance);
partial void UpdateProducts(Products instance);
partial void DeleteProducts(Products instance);
#endregion

public System.Data.Linq.Table<Orders> Orders{get;}
public System.Data.Linq.Table<Order_Details> Order_Details{get;}
public System.Data.Linq.Table<Products> Products{get;}
}

public partial class Orders
{ }
public partial class Order_Details
{ }
public partial class Products
{ }
}
```

## Mapping with the Command-Line Tool

透過命令列工具 SqlMetal.exe ，也可以產生 .dbml 檔和 O/R code。  這個方法最適合針對一整個大型資料庫做 mapping 時使用。例如：  

#### To generate a .dbml file
sqlmetal "C:\Code\Northwind and Pubs Dbs\pubs.mdf" /language:vb /dbml:Pubs.dbml  
#### To create the code
sqlmetal "C:\Code\Northwind and Pubs Dbs\pubs.mdf" /language:vb /code:Pubs.vb

## Mapping Objcts to the Entity in the Code Editor

這方法就是自行撰寫 O/R map 程式碼，方法大至依照以下步驟：

#### 1. 建立對應到資料庫的 Table 的類別檔，並引用以下命名空間
```c#
using System.Data.Linq;
using System.Data.Linq.Mapping;

[Table]
public class MyEmployees
{
...
｝
```

#### 2. 使用 [Table](http://msdn.microsoft.com/zh-tw/library/system.data.linq.mapping.tableattribute.aspx) 屬性來描述自訂的類別，以對應到實體資料庫的 Table。

#### 3. 使用 [Column](http://msdn.microsoft.com/zh-tw/library/system.data.linq.mapping.columnattribute.aspx) 屬性描述該類別的 Property ，以對應到實體 Table 的 Column。
```c#
namespace C17_LINQ
{
[Table(Name = "Employees")]
public class Employees
{
private int _empID;

[Column(IsPrimaryKey = true, Name = "EmployeeId", Storage = "_empID")]
public int EmployeeId
{
get
{
return this._empID;
}
set
{
this._empID = value;
}
}

[Column(Name = "FirstName")]
public string FirstName { get; set; }

[Column(Name = "LastName")]
public string LastName { get; set; }

[Column(Name = "Age")]
public int Age { get; set; }

[Column(Name = "City")]
public string City { get; set; }
}
}
```

#### 4. 建立一個可以用來公開這些 Table 類別的 Context 類別。

這個類別必須繼承 [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 物件，[DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 物件可以幫我們透過資料庫連接對應到所有實體的來源。
```c#
namespace C17_LINQ
{
public class NorthwindCustomMap : DataContext
{
public Table<Employees> employees;

// This constructor will call DataContext.DataContext()
public NorthwindCustomMap(string connection) : base(connection)
{
}
}
}
```

# Query Data with LINQ to SQL

要使用 [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) 查詢資料就和使用 LINQ Query 的方法是一樣的。  最大的不同在於 [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) 可以透過 O/R Map 物件連接到真正的資料庫。  

## 使用自訂的 DataContext 物件，操作 LINQ to SQL 查詢

使用自訂的 [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 物件必須提供一個連線字串，以便連接資料庫。
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

//Table<MyEmployees> Employees = db.GetTable<MyEmployees>();
Table<MyEmployees> Employees = db.employees;

IEnumerable<MyEmployees> query =
from emp in Employees
where emp.FirstName.Length > 5
select emp;

gv1.DataSource = query;
gv1.DataBind();
```

## 使用自動產生的 DataContext 物件，操作 LINQ to SQL 查詢

使用自動產生的 DBML 檔，它會自動設定連線字串，並且建立繼承自 [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 的物件。  我們只須建立它的 instance 就可以直接使用查詢。  
```c#
NorthwindDataContext db = new NorthwindDataContext();

var query = from emp in db.Employees
where emp.FirstName.Length > 5
select emp;

gv1.DataSource = query;
gv1.DataBind();
```

# Inserting, Updating, and Deleting with LINQ to SQL

LINQ to SQL makes inserting, updating, and deleting data in your database a very simple process.   It creates the connections between your O/R map and your database.   You simply need to make a modification to object instances and then save the changes.   You work with your object model to add a new instance to a collection, modify a given object, or remove an object from the collection.   Once complete, you call the [SubmitChanges](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.submitchanges.aspx) method of the [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) object to write the results back to the database.  

For example, if you wish to add a new order to the database, you start by creating a new order object.   You then add it to the Orders table using the method [InsertOnSubmit](http://msdn.microsoft.com/zh-tw/library/bb386940.aspx) .   When you are ready to submit all changes, you call [SubmitChanges](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.submitchanges.aspx) .  

底下是 [ITable](http://msdn.microsoft.com/zh-tw/library/system.data.linq.itable.aspx)型別公開的新增或刪除的方法：  

- [InsertOnSubmit](http://msdn.microsoft.com/zh-tw/library/system.data.linq.itable.insertonsubmit.aspx) ：在這個資料表中加入一個 **pending insert** 狀態的實體。
- [DeleteOnSubmit](http://msdn.microsoft.com/zh-tw/library/system.data.linq.itable.deleteonsubmit.aspx) ：將此資料表中的指定實體，置於 **pending delete** 狀態。
- [InsertAllOnSubmit](http://msdn.microsoft.com/zh-tw/library/system.data.linq.itable.insertallonsubmit.aspx) ：將集合的所有實體加入至 pending insert 狀態中的 [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 。
- [DeleteAllOnSubmit](http://msdn.microsoft.com/zh-tw/library/system.data.linq.itable.deleteallonsubmit.aspx) ：將集合中的所有實體置於 pending delete 狀態。

當變更資料結束時，必須叫用 [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) 的 [SubmitChanges](http://msdn.microsoft.com/zh-tw/library/bb347070%28v=vs.100%29.aspx) 方法，以便將結果寫回資料庫。

底下範例示範如何使用 [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) 執行 CRUD 。

### 新增
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

Orders newOrder = new Orders();
newOrder.CustomerID = "VINET";
newOrder.EmployeeID = 5;
newOrder.OrderDate = DateTime.Now;

db.orders.InsertOnSubmit(newOrder);
db.SubmitChanges();
```

**預設值欄位問題**

在資料庫中的資料表欄位，有時候我們會設定成不允許 Null ，並且設有預設值。  在過去使用 SqlCommand 物件進行新增資料時，在 Insert 的 SQL 字串中可以不去理會這些欄位，並不會有什麼問題。  可以當使用 LINQ to SQL 自動建立的類別時，如果沒有給定這些欄位初值，就會產生錯誤。  如果要使用預設值，你必須將這些欄位的［自動產生的值（Auto Generated Value）］屬性設為 True ，該值才會由系統自動產生。如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhP2dcnEZmAbfdlXV4TWBqk-fdxSuoHppHkgzpe_oECfZUr080D_BPY4Ezg0c9AGBpvjB057B4WI4f3J8wm3snZunF02l2_pYAdIoWXUDziLzpPPE0INmRVT7Xwp2vs312K5Qxx2vWCb-k/s596/linq-auto-generated-value.png)

使用上面方法，如果重拉 O/R map 時，就得重新再設定一定；或者你也可以使用以下方法，在實體類別中的 OnCreate() 事件中給定一個值，以避免這個問題。
```c#
public partial class Total_DailyTrade
{
partial void OnCreated()
{
this.CreatedTime = System.DateTime.Now;
}
}
```

**自動編號欄位問題**

在資料庫中的資料表欄位，若設定成自動產生編號，該欄位的［自動產生的值］屬性**不需要**設定，要取得新增資料後產生的編號，只需要以下做法即可：  
```c#
StockNews news = new StockNews();
news.StockID = "2233";
news.Title = "Title";
news.PublishDate = DateTime.Now.ToString("yyyyMMdd");
dc.StockNews.InsertOnSubmit(news);
dc.SubmitChanges();

//取得新增資料後產生的編號
int id = news.ID;
```

### 修改

若要修改資料，必須透過查詢先取得資料，更新後，再呼叫 [SubmitChanges](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.submitchanges.aspx) 回存到資料庫。
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

//建立查詢語法
var query = from order in db.orders
where order.OrderID == 11081
select order;
//執行查詢語法
Orders updateOrder = query.First();

//以上,可以用另一種較簡單寫法,如下:
//var updateOrder = db.orders.First(o => o.OrderID == 11081);

//變更資料
updateOrder.OrderDate = DateTime.Now;

//更新資料庫資料
db.SubmitChanges();
```

**LINQ to SQL 資料庫更新衝突問題**

像上面例子，我們將資料取出進行資料修改，修改完後再Submit回資料庫，如果在修改這段時間，資料庫中的該筆資料發生了變更，就會導至「資料列找不到，或者已變更」（Row not found or changed）的錯誤訊息發生。  要處理這種衝突問題，可參考保哥的這篇文章：「[解決 LINQ to SQL 資料庫更新衝突的情形](http://blog.miniasp.com/post/2008/05/24/Resolve-LINQ-to-SQL-Change-Conflict-Exception.aspx)」。  不過

### 刪除

若要刪除資料，只須將要刪除資料的執行個體傳給 [DeleteOnSubmit](http://msdn.microsoft.com/zh-tw/library/bb763473.aspx) 方法即可。
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

var deleteOrder = db.orders.First(o => o.OrderID == 11081);

db.orders.DeleteOnSubmit(deleteOrder);
db.SubmitChanges();
```

### 查詢
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

var query = from order in db.orders
where order.OrderID == 10248
orderby order.OrderID
select order;

gv1.DataSource = query.ToArray();
gv1.DataBind();
```

# Join with LINQ to SQL

#### Inner Join

#### 使用 where 條件關連欄位
```c#
protected void btnJoin_LinqToSql_Click(object sender, EventArgs e)
{
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

var query = from O in db.orders
from OD in db.order_details
where O.OrderID == OD.OrderID && O.OrderID == 10248
select new { O.OrderID, OD.ProductID, OD.UnitPrice, OD.Quantity };

gv1.DataSource = query.ToArray();
gv1.DataBind();

myDebug.WriteLine(query.ToString());
//SELECT [t0].[OrderID], [t1].[ProductID], [t1].[UnitPrice], [t1].[Quantity]
//FROM [dbo].[Orders] AS [t0], [dbo].[Order Details] AS [t1]
//WHERE ([t0].[OrderID] = [t1].[OrderID]) AND ([t0].[OrderID] = @p0)
}
```

#### 直接利用資料表的關連性
```c#
protected void btnJoin_LinqToSql_Relaction_Click(object sender, EventArgs e)
{
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

var query = from O in db.orders
from OD in O.Order_Details
where O.OrderID == 10248
select new
{ O.OrderID, OD.ProductID, OD.UnitPrice, OD.Quantity };

gv1.DataSource = query.ToArray();
gv1.DataBind();

myDebug.WriteLine(query.ToString());
//SELECT [t0].[OrderID], [t1].[ProductID], [t1].[UnitPrice], [t1].[Quantity]
//FROM [dbo].[Orders] AS [t0], [dbo].[Order Details] AS [t1]
//WHERE ([t0].[OrderID] = @p0) AND ([t1].[OrderID] = [t0].[OrderID])
}
```

#### Left Join
```c#
protected void btnLeftJoin_LinqToSql_Click(object sender, EventArgs e)
{
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext db = new NorthwindContext(connString.ConnectionString);

var query = from O in db.orders
join OD in db.order_details on O.OrderID equals OD.OrderID into ODS
from OD in ODS.DefaultIfEmpty()
where O.OrderID == 10248
select new
{ O.OrderID, OD.ProductID, OD.UnitPrice, OD.Quantity };

gv1.DataSource = query.ToArray();
gv1.DataBind();

myDebug.WriteLine(query.ToString());

//SELECT [t0].[OrderID], [t1].[ProductID] AS [ProductID], [t1].[UnitPrice] AS [UnitPrice], [t1].[Quantity] AS [Quantity]
//FROM [dbo].[Orders] AS [t0]
//LEFT OUTER JOIN [dbo].[Order Details] AS [t1] ON [t0].[OrderID] = [t1].[OrderID]
//WHERE [t0].[OrderID] = @p0
}
```