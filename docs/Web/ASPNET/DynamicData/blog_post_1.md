---
title: Working with ASP.NET Dynamic Data
layout: default
parent: DynamicData
nav_order: 1
description: "Working with ASP.NET Dynamic Data"
date: 2013-01-03
tags: [Web,ASPNET,DynamicData]
postid: "2862504112484348375"
---
在軟體開發過程中，常會需要不斷地重複顯示與編輯資料。  ASP.NET Dynamic Data Website 是一種可以讓您只需要利用極少的程式碼或是根本不使用程式碼，建立資料導向的應用程式。  Scaffolding 是 Dynamic Data 中的一個重要機制，它指的就是由系統自動根據資料庫中的結構描述(Database Schema)，產生網頁範本的機制。   

# Getting Started with Dynamic Data Websites

ASP.NET Dynamic Data is a set of **user interface templates** and **controls** for creating **data-driven** websites.   You attach a **data context** to a [Dynamic Data MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) object and can then work with that data to read, filter, insert, update, and delete.     Dynamic Data does not generate code specific to a particular data schema.   Rather, it is a series of extensible templates that can work with any LINQ to SQL, Entity Framework, or custom data context.   

To start creating a new website with Dynamic Data, you typically follow these steps:

- 1. **Create a scaffold site** that includes the default presentation layer templates used by ASP.NET Dynamic Data.
- 2. **Define a data context** that represents your data source and model by using either LINQ to SQL, LINQ to Entities, or a custom model.
- 3. **Register your data context** with the website's [MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) instance inside the **Global.asax** file.
- 4. Customize the **URL routing**, **page and field templates**, **business logic**, and **validation** in the website. (This step is optional.)

## 1. Creating a Dynamic Data Website

Visual Studio ships with two ASP.NET Dynamic Data project templates：

- ASP.NET Dynamic Data Entities Web Site
- ASP.NET Dynamic Data LINQ to SQL Web Site

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGwCBmkTa6Jq4uSKy98euj-PqxzAXJ46Cf2S7ly4tOfHS2GGLFo8zSKDgftQelvCwH894U3q14HUyOAYRvAO_w9BULVpxCKg-5HexUz_GF-v8Sn0qRL-P-C4Dtt8GyQEnj4aGtTON1yKQ/s699/DynamicDataTemplate.png)

#### Dynamic Data Website Sturcture

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMze553ebkQjJBEpMwoqkmjx_FmJhzkRWk8rUN6YL4DFE9MQHkVeUTDKSu1OudUxOsbVYbSS0Jz4bMetMRtmq1PkIZvi5aoUTKwNAuhar_scVTrHozfebs_429nezQGoMCXjaIiB10VfQ/s261/DynamicDataTemplate-File-Sturcture.png)

## 2. Defining a Data Context

You create a data context by adding either a **LINQ to SQL Classes (DBML)** file or an **ADO.NET Entity Data Model (EDMX)** file to your project.   You can also create a custom model through code.

## 3. Registering Your Data Context with the MetaModel

The next step is to register your data context with [System.Web.DynamicData.MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) .   The [MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) defines the connectivity or mapping between the **scaffold templates** and your **data layer**.  

### 3.1) Creating a static and readonly MetaModel

[MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) 類別是用來表示 Dynamic Data 要使用的資料。  當建立動態資料專案時，系統會先在 global.asax 自動加入一個唯讀 [MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) 型別的靜態變數。  這個變數即表示 Dynamic Data 要使用的資料庫。  
```c#
private static MetaModel s_defaultModel = new MetaModel();
public static MetaModel DefaultModel {
get {
return s_defaultModel;
}
}
```

### 3.2) Register DataContext

接下來必須在 Application\_Start 事件中，透過該 [MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) 實體來註冊資料庫模型，並設定 Routing 。  這些動作，系統已自動幫我們建立一個**RegisterRoutes**方法，來分離這些操作。  要註冊資料庫模型必須使用 [MetaModel.RegisterContext](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.registercontext.aspx) 方法，來註冊你要使用的資料模型。  

[RegisterContext](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.registercontext.aspx) 這個方法是多載的，在註冊資料庫模型時，也可以同時使用 [ContextConfiguration](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.contextconfiguration.aspx) 類別的屬性，定義資料內容的初始設定資訊，例如：設定啟用哪些 Table 要啟用 Scaffolding 。   
```c#
public void RegisterContext(Type contextType, ContextConfiguration configuration);
public void RegisterContext(DataModelProvider dataModelProvider, ContextConfiguration configuration);
```

#### 3.2.1) 設定所有的 Table 都啟用 Scaffolding

若希望資料模型中的所有 Table 都自動產生樣板檢視網頁，可以將 [ScaffoldAllTables](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.contextconfiguration.scaffoldalltables.aspx) 設定成 **true** 即可。
```c#
void Application_Start(object sender, EventArgs e)
{
DefaultModel.RegisterContext(
typeof(NorthwindDataContext), 
new ContextConfiguration() { ScaffoldAllTables = true }
);
}
```

#### 3.2.1) 設定特定的 Table 或 Column 啟用 Scaffolding

若僅特定的 Table 要啟用 Scaffolding，則必須將 [ScaffoldAllTables](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.contextconfiguration.scaffoldalltables.aspx) 設定成 **false** ，  並在 Metadata class 上，使用 [ScaffoldTable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldtableattribute.aspx) 和 [ScaffoldColumn](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldcolumnattribute.aspx) 屬性，以指定類別或資料行是否使用 Scaffolding。   

下面範例使用 [ScaffoldTable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldtableattribute.aspx) 屬性設定 Product 資料模型要啟用 Scaffolding 機制
```c#
[ScaffoldTable(true)]
public partial class Product 
{
}
```

針對個別資料表中的個別欄位使用 [ScaffoldColumn](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldcolumnattribute.aspx) 屬性啟用 Scaffolding 機制  
```c#
//使用 Metadata 來進一步描述要啟用或停用 Scaffolding 的欄位
[MetadataType(typeof(Customer_Metadata))]
public partial class Customer
{
}

public class Customer_Metadata
{
[ScaffoldColumn(false)]
public object PasswordHash;

[ScaffoldColumn(false)]
public object PasswordSalt;
}
```

### 3.3) Register Routes

由前面說明知道，動態資料是利用 ASP.NET 路由比對來處理 URL 要求。  若使用**獨立頁面模式**，在這種模式下，List、Detail、Insert 和 Update 等工作都會使用獨立的頁面來執行。  因為 Delete 作業不需要特定的網頁範本，所以 List 及 Details 這兩個頁面中都會顯示刪除功能。   如下面 routing 的設定，每一個 Table 的每一種功能，都會對應到不同的網頁範本。  
```c#
void Application_Start(object sender, EventArgs e)
{
RegisterRoutes(RouteTable.Routes);
}

public static void RegisterRoutes(RouteCollection routes)
{
// 註冊資料模型 (並將所有 Table 都設定成 Scaffold)
DefaultModel.RegisterContext(typeof(NorthwindDataContext), new ContextConfiguration() { ScaffoldAllTables = true });

//定義使用獨立頁面模式, 也就是每個 table 的每個 action, 會有不同的 uri
routes.Add(
new DynamicDataRoute("{table}/{action}.aspx")
{
Constraints = new RouteValueDictionary(new { action = "List|Details|Edit|Insert" }),
Model = DefaultModel
}
);
}
```

若要使用 **combined-page** 模式，在這種模式下，List、Detail、Insert 和 Update 等工作都會使用相同的頁面來執行。  如下面 routing 的設定，第一個路由啟用特定資料表上某一頁面的所有作業。第二個路由可以讓頁面巡覽到一筆資料錄的詳細資料。  使用這種路由設定，必須設外部索引鍵欄位，才可巡覽到關聯性資料表。   
```c#
void Application_Start(object sender, EventArgs e)
{
RegisterRoutes(RouteTable.Routes);
}

public static void RegisterRoutes(RouteCollection routes)
{
// 註冊資料模型 (並將所有 Table 都設定成 Scaffold)
DefaultModel.RegisterContext(typeof(NorthwindDataContext), new ContextConfiguration() { ScaffoldAllTables = true });

//定義使用 combined-page 模式設定路由
routes.Add(new DynamicDataRoute("{table}/ListDetails.aspx")
{
Action = PageAction.List,
ViewName = "ListDetails",
Model = DefaultModel
});
routes.Add(new DynamicDataRoute("{table}/ListDetails.aspx")
{
Action = PageAction.Details,
ViewName = "ListDetails",
Model = DefaultModel
});
}
```

在完成上述步驟後，一個基本的動態資料網站就建立完成。  它預設也會幫我們建立 filter (篩選欄位)，但這個 filter 預設只會包含 boolean 型態的欄位和具關連性的欄位。  這個自動建立的網頁，也同時也支援了 create, edit, delete, view detail, connect to data related to a particular row, paging, field validation..等功能。

## The Dynamic Data Classes

與 Dynamic Data 相關的類別都定義在 [System.Web.DynamicData](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.aspx) 命名空間中。  這些類別包含建立網頁中動態資料的類別、建立自訂欄位的控制項、管理 routing 用的類別等。  

另外，相關的 [MetaModel](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.metamodel.aspx) 類別都定義在 [System.Data.Linq.Mapping](http://msdn.microsoft.com/zh-tw/library/system.data.linq.mapping.aspx) 命名空間中。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1JtcxHxNvfKwzmphbiA3wJGy0c4IK9Urhtr4kymOEy7Nf-RK-UuK9GWnMZVkKN6TfJWHw2AI31l1zCQwUgdBfkLBHh8xaiMvDCN4mrcu1vtuSTXEIhvHcTRfYQKnLlL-q5IKhcLpEJRA/s850/dd-dynamicdata-and-metamodel.png)

# Extending and Customizing Dynamic Data

ASP.NET Dynamic Data is fully extensible. You can customize how fields are edited,   how data is displayed, the look of the pages, the URL routing, data validation, and more.  

## Defining Custom Routing

#### Custom Routing

Dynamic Data uses the same routing features from [System.Web.Routing](http://msdn.microsoft.com/zh-tw/library/system.web.routing.aspx) that are used by ASP.NET MVC.  This routing engine maps intelligent Uniform Resource Identifiers (URIs) that are defined based on user actions to actual pages in your site,   which makes the URIs easier to understand for users and search engines.  

假設一個 DD 網站有以下幾個樣板頁面 List.apsx, Edit.aspx, Insert.aspx, Detail.aspx.  這些頁面會由 Routing engine 依不同的 action 叫用，而這個對應規則就定義在 routing table 之中。  [DynamicDataRoute](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.aspx) 類別是用來表示一組 routing rule 的類別，建立後把它加入到 [RouteTable.Routes](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routetable.routes.aspx) 這個靜態屬性即可。  

####  [DynamicDataRoute](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.aspx) 屬性：

- [Action](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.action.aspx) ：取得或設定路徑的動作名稱
- [Model](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.model.aspx) ：取得或設定路徑套用的資料模型。
- [ViewName](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.viewname.aspx) ：取得或設定與路徑相關聯的 .aspx 頁面名稱。
- [Table](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.dynamicdataroute.table.aspx) ：取得或設定路徑的資料表名稱。
```c#
public static void RegisterRoutes(RouteCollection routes)
{
//將所有 Table 都設定成 Scaffold
DefaultModel.RegisterContext(typeof(NorthwindDataContext), new ContextConfiguration() { ScaffoldAllTables = true });

//使用獨立頁面模式定義一個 routing rule, 也就是每個 table 的每個 action, 會有不同的 URI.
DynamicDataRoute ddRute = new DynamicDataRoute("{table}/{action}.aspx");
ddRute.Constraints = new RouteValueDictionary(new { action = "List|Details|Edit|Insert" });
ddRute.Model = DefaultModel;

//加入 routing rule
routes.Add(ddRute);
}
```

上面程式碼表示，當執行 http://mysite/Products/Edit.aspx?ProductID=1 時，  將自動路由到 /DynamicData/PageTemplates/Edit.aspx 頁面，並讀取 Products 這個 Table 中的 ProductID=1 這筆資料。  

#### Custom the URI sturcture

若不喜歡上面的路由規則，你也可以自訂路由規則。  例如，若設定成 {action}/{table}.aspx ，則須求的網址就應該是 http://mysite/Edit/Products.aspx?ProductID=1 。  

#### Custom Routing for combined-page mode

上面例子中的設定，使用不同 page 執行不同操作。  然而 DD 的樣板也支援在單一頁面中，同時操作不同的操作。  這個樣板頁面就是 ListDetail.aspx 。  這個頁面除了顯示清單資料，也包含 Detail 資料區塊，讓你可以在同一頁面完成操作。  要使用這個頁面，必須將預設的 routing 修改成以下的 routing 。  
```c#
routes.Add(new DynamicDataRoute("{table}/ListDetails.aspx")
{
Action = PageAction.List,
ViewName = "ListDetails",
Model = DefaultModel
});

routes.Add(new DynamicDataRoute("{table}/ListDetails.aspx")
{
Action = PageAction.Details,
ViewName = "ListDetails",
Model = DefaultModel
});
```

上面這個範例，其設定目的是希望，不管是要顯示清單，或顯示單筆明細，都會使用 ListDetails.aspx 網頁。

#### Creating a custom route to specific page

上面範例，系統預設使用 Page Templates 中的樣板檔案。  若你希望使用自訂的樣板檔案，你只須在 CustomPages 目錄中建立一個和 Entity 相同名稱的目錄，並且建立 action pages (list.aspx, edit.aspx...)即可。  則 routing engine 會優先使用在 CustomPage 中的樣板。(底下建立客製樣板章節有較詳細說明)

## Adding Metadata to Your Data Context

You can create partial classes to add Metadata to your Data Context.  

For example,   if you want to format a value for display, you can use the [DisplayFormat](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribute.   You can also use the [Display](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayattribute.aspx) attribute to change the name of a column.  
```c#
[MetadataType(typeof(ProductMetadata))]
public partial class Products
{
}

public class ProductMetadata
{
public object ProductID { get; set; }
public object ProductName { get; set; }
public object SupplierID { get; set; }
public object CategoryID { get; set; }

[ScaffoldColumn(false)]
public object QuantityPerUnit { get; set; }

[ScaffoldColumn(false)]
public object UnitsOnOrder { get; set; }

[ScaffoldColumn(false)]
public object ReorderLevel { get; set; }

[DisplayFormat(ApplyFormatInEditMode = false,DataFormatString = "{0:c}")]
[Display(Name = "單價")]
public object UnitPrice { get; set; }

[Display(Name = "已停產")]
public object Discontinued { get; set; }

[Display(Name = "庫存量")]
public object UnitsInStock { get; set; }
}
```

上面例子，經過一些屬性設定，則網頁就會依據 Metadata 中的設定來顯示內容。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjxTAZ9q-xU_AWzT7YRcZuTKNv8IRN8Ud0qQ_WpVb1MVapoFa8IM2r6ecqDtd4TQSksSxjJy8itkYtZlRVUoYLmJlLoEu1bnET9fmoVqgA2YGO4m_QrWtrb9RypFVcmYBz1Xm091EFPeEo/s758/dd-using-metadata-to-setup-display.png)

這些用來定義中繼資料的屬性類別，都定義在 [System.ComponentModel.DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) 命名空間。底下是幾個常用類別：

#### 資料模型相關屬性

- [Association](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.associationattribute.aspx) ：指定實體成員的外部索引鍵關聯性。
- [DataType](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.datatypeattribute.aspx) ：指定與資料欄位產生關聯的其他型別名稱。
- [EnumDataType](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.enumdatatypeattribute.aspx) ：讓 .NET Framework 列舉型別對應至資料行。
- [ScaffoldColumn](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldcolumnattribute.aspx) ：指定類別或資料行是否使用 Scaffolding。
- [ScaffoldTable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldtableattribute.aspx) ：指定類別或資料的資料表 (Data Table) 是否使用 Scaffolding。
- [Editable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.editableattribute.aspx) ：指出資料欄位是否可以編輯。

#### 驗證相關屬性

- [Required](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.requiredattribute.aspx) ：指出需要使用資料欄位值。
- [StringLength](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) ：指定資料欄位中允許的最小和最大字元長度。
- [CustomValidation](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.customvalidationattribute.aspx) ：指定用於驗證的屬性或類別的執行個體的自訂驗證方法。
- [Validation](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.validationattribute.aspx) ：做為所有驗證屬性的基底類別
- [Range](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.rangeattribute.aspx) ：指定資料欄位值的數值範圍條件約束。
- [RegularExpression](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) ：指定 ASP.NET Dynamic Data 中的資料欄位值必須符合指定的規則運算式
- [Url](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.urlattribute.aspx)  ：提供 URL 驗證。

#### 顯示相關屬性

- [Display](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayattribute.aspx) ：提供一般用途屬性，可讓您為實體部分類別的型別和成員指定可當地語系化的字串。
- [DisplayFormat](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayformatattribute.aspx) ：指定 ASP.NET Dynamic Data 顯示和格式化資料欄位的方式。
- [DisplayColumn](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displaycolumnattribute.aspx) ：設定外部索引鍵參考時的顯示欄位。例如當 Orders.EmployeeID 參考到 Employees.EmployeeID ，系統很聰明的自動以 LastName 為顯示欄位。但是，你也可以使用 DisplayColumn("FirstName") 改成 FirstName 才是外部索引鍵參考時的顯示欄位。
- [UIHint](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.uihintattribute.aspx)   ：指定 Dynamic Data 用來顯示資料欄位的範本或使用者控制項。

## Adding Custom Validation

要設定資料驗證，有以下幾個方式

#### 使用驗證屬性

我們可以在 metadata 的 property 上直接加入驗證 attribute ，驗證屬性包括： [Range](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.rangeattribute.aspx) , [StringLength](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) , [Required](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.requiredattribute.aspx) , and [RegularExpression](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) 。  
```c#
[Required(), StringLength(50)]
public object ProductName { get; set; }

[Range(1, 999, ErrorMessage = "庫存量必須介於 1 ~ 999 之間")]
public object UnitsInStock { get; set; }
```

#### 使用 Partial Methods 自訂驗證方法

當建立 data content 模型時，該模型會包含許多個擴充方法的定義，例如：每個資料欄位的 OnChanging 和 OnChanged 方法。  我們可以利用這些方法來撰寫自訂的驗證規則。  

下面這個例子，我們在 UnitPrice 的 OnChanging 事件中加入自訂的邏輯判斷。  當驗證不符時，只要丟出一個 [ValidationException](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.validationexception.aspx) ，DD 就會自動將這個錯誤顯示出來。  
```c#
partial void OnUnitPriceChanging(decimal? value)
{
if (value < 0)
{
throw new ValidationException("單價不可以小於0");
}
}
```

## Creating Custom Field Templates

在 **FieldTemplates** 目錄中，系統預設我們建立了許多自訂的樣板控制項。  這些自訂樣板控制項都是繼承 [FieldTemplateUserControl](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.fieldtemplateusercontrol.aspx) 並且以資料型別命名，而且每個型別都同時包含一個**顯示用**和一個**編輯用**的自訂控制項。  

For example, the controls used to render DateTime values are **DateTime.ascx** and **DateTime\_Edit.ascx**.   The former is simply a Literal control that is used when a DateTime value is rendered for display.   The edit version includes a TextBox, a RequiredFieldValidator, a RegularExpressionValidator, a DynamicValidator, and a CustomValidator control.   This composite user control is used whenever a DateTime value is displayed for editing.  

You can edit the field template controls or create new ones in order to provide additional behavior in your website  

### Customizing an Existing Field Template

下面這個例子示範直接修改一個現有的自訂欄位樣板。將啟用必要欄位輸入的欄位，以不同背景色顯示。  

首先，我們將 Text\_Edit.ascx 控制項中的 RequiredFieldValidator 的 Enable 設成 True .
```xml
<asp:RequiredFieldValidator runat="server" ... Enabled="true" />
```

再由 code-behind 的程式碼中，在 OnDataBinding 事件中做如下修改.
```c#
protected override void OnDataBinding(EventArgs e) {
base.OnDataBinding(e);
if(Column.MaxLength > 0) {
TextBox1.MaxLength = Math.Max(FieldValueEditString.Length, Column.MaxLength);
}

if (this.RequiredFieldValidator1.Enabled)
{
TextBox1.BackColor = System.Drawing.Color.SandyBrown;
}
}
```

其他編輯用的控制項，如 DataTime\_Edit.ascx, Decimal\_Edit.ascx, Integer\_Edit.ascx, and MultilineText\_Edit.ascx 也都可以這麼變更，以便讓所有型別的輸入控制項，都有同樣效果。

### Createing a New Field Template Control

You can create your own custom field template user controls by adding a new 「**Dynamic Data/FieldTemplates**」 item.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhv9woF30JFO2vxXrRCHGaxw1APBrg5c-uFApio_Godhho_Qyco7uFsMwbDeeYc-cnJvgVnDH_ha2ixPBxx2A1TXT27ADaN4zz44XVasFm4RHOEMk3X1F1Baxs5zcIshkmWe0iMIweCwqA/s507/dd-custom-field-template.png)

When you use the Dynamic Data Field item template,   Visual Studio will actually generate two controls: one for display and one for editing。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjuyG0vOM30aib1TPEBMyR9ZhWk0USSOp-d611tWFiVBICskYC3V0B5e1PHcIwIlHPHCOYZ6ZxuR9mhgEnnFvXAE5nc2PdsF7rs8zxWnQNpGh-7rgT921NS294RDor4aWISGBJkLULlAC0/s250/dd-custom-field-template2.png)
```xml
<asp:Calendar ID="Calendar1" runat="server" CssClass="DDTextBox"></asp:Calendar>
```
```c#
public partial class DynamicData_FieldTemplates_CalendarPicker_EditField : System.Web.DynamicData.FieldTemplateUserControl {
protected void Page_Load(object sender, EventArgs e) {

}

protected override void OnDataBinding(EventArgs e)
{
base.OnDataBinding(e);

if (this.FieldValue!=null)
{
DateTime dte = DateTime.Parse(this.FieldValue.ToString());
Calendar1.SelectedDate = dte;
Calendar1.VisibleDate = dte;
}
}
}
```

當建立好自訂欄位控制項之後，若要使用這個控制項，則必須在 metadata 中，使用 [UIHint](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.uihintattribute.aspx) 這個屬性加以宣告，  以指明該欄位資料要以這個自訂的欄位控制項顯示。  
```c#
public partial class Employees
{

}

public class EmployeesMetadata
{
[UIHint("CalendarPicker")]
public object HireDate { get; set; }

[UIHint("CalendarPicker")]
public object BirthDate { get; set; }
}
```

執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnb-V9W2bF0S3rWTVoXdWoFBuj-lwLLMmtHUWoNARHarB8nW91vUvLoNzwx0DqA_7Fu6HZE9ui5_GFYoosRMSSqtcNu9y8xjyX_wK23AuZjdVoYqjJZ0HTEQB6JU9lxBZgYe1pN9DNr1U/s564/dd-using-custom-field-control.png)

## Creating Custom Page Templates

在 **PageTemplates** 目錄中的樣板是給所有的 entity 共用的。  如果說，想要建立一個針對特定 entity 使用的頁面，則可以在 **CustomPages** 目錄中新增樣板。  

要建立特定 entity 使用的樣板，必須在 **CustomPages** 這個目錄中**建立和 entity 物件相同名稱的子目錄**，  然後再子目錄裡新增一個新的樣板頁面，或者複製 PageTemplates 目錄中的檔案來修改即可。  

The Dynamic Data routing will look first in the **CustomPages/EntityName** folders for action pages such as Edit.aspx, Insert.aspx, and List.aspx.   If the file it is looking for is there, it will use this custom page for display.   If not, it will use the default action page inside the PageTemplates directory.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4JgdQEeSvAUA-NX3lAenins-AXrTq7J6vOMfxFLfHYLrSKvDegRaPZV3FCovato7tJi4vXBI8OApnglCVwHPnEnVEPunwIFsaAgANMSja-XgRfsAcqyXQ58sJBlWsmWHGRGyL4mvESfU/s351/dd-custom-template.png)

## Creating Custom Queryable Filter Templates

若要建立一個查詢用的樣板欄位，必須新增一個**使用者自訂控制項**，並且繼承自 [QueryableFilterUserControl](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.queryablefilterusercontrol.aspx) 類別，再覆寫 [GetQueryable](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.queryablefilterusercontrol.getqueryable.aspx) 方法。  並且在 metadata 中，使用 [FilterUIHint](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.filteruihintattribute.aspx) 屬性加以說明，指明使用這個自訂的 Filter 控制項進行篩選。  

1. Create a new UserControl for the filter in **Filters** sub-folder.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJ3BGSpZIjMKqHPiHPb0BhSbuZ7B3Jf_EgCP6xQqENlyMIWNJrmBoVg7HRbDn0QHMc_g2sL7W_WGCdTQac4w6y3H8OirWVD_5Lwjy7yZRWqa47sswWStMRolCVt85j8fq8-GmPmlstjNA/s271/dd-custom-filter-template.png)

2. inherit from [QueryableFilterUserControl](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.queryablefilterusercontrol.aspx) and override [GetQueryable](http://msdn.microsoft.com/zh-tw/library/system.web.dynamicdata.queryablefilterusercontrol.getqueryable.aspx) method
```xml
<asp:TextBox ID="TextBox1" runat="server" AutoPostBack="True" 
ontextchanged="TextBox1_OnTextChanged"></asp:TextBox>
```
```xml
public partial class DynamicData_Filters_TextFilter : QueryableFilterUserControl
{
private const string NullValueString = "[null]";

protected void Page_Load(object sender, EventArgs e)
{
}

public override Control FilterControl
{
get
{
return TextBox1;
}
}

protected void TextBox1_OnTextChanged(object sender, EventArgs e)
{
OnFilterChanged();
}

public override IQueryable GetQueryable(IQueryable source)
{
string selectedValue = TextBox1.Text;
if (String.IsNullOrEmpty(selectedValue))
{
return source;
}

object value = selectedValue;
if (selectedValue == NullValueString)
{
value = null;
}
if (DefaultValues != null)
{
DefaultValues[Column.Name] = value;
}

return ApplyEqualityFilter(source, Column.Name, value);
}
}
```

3. using [FilterUIHint](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.filteruihintattribute.aspx) attribute to specify the filtering behavior 
```c#
[MetadataType(typeof(EmployeesMetadata))]
public partial class Employees
{

}
public class EmployeesMetadata
{
[FilterUIHint("TextFilter")]
public object FirstName { get; set; }
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzTtjV_ESdH-C91TUM4O8ePBLc3qHrH_QgPzumIFzma1wB0eNJcvW8Z8EwMUaK0Ssoo5_0llh-JO5t4x6w-FjDQbOkfA5Q31-W3Zccm2GPV3ub1mxVwCyI1OpmN622hSN-St9qsgg9pRM/s512/dd-using-custom-filter.png)

# Using Dynamic Controls in Existing Sites

You can use the features of Dynamic Data in existing sites.   For example, you can add the full scaffolding and connect to a data context for CRUD operations,   and you can create custom webpages (or customize existing ones) to take advantage of the lower code, lower markup solution that Dynamic Data offers.  

## Adding Dynamic Data Scaffolding to an Existing Website

若是想在現有的網站中使用 Dynamic Data scaffolding 技術，你必須完成以下步驟，就可以利用以上談到的技巧使用 Dynamic Data ，建立動態網頁以執行 CRUD 操作。  

- a data context
- edit global.asax to register your context and enable custom routing
- copy the DynamicData folder from a blank Dynamic Data site into your existing site.

## Enabling Dynamic Data Inside a Webpage

Dynamic Data 技術中，好用的功能很多，包括 Data Annotation 、 field validation 、 scaffolding 、 routing 等等功能。  若只想在自訂的網頁中使用 Dynamic Data 的 Data Annotation 、 field validation ，但沒有 scaffolding 或 routing 需求的話，  只要在 meta-model 中加入必要的資訊描述即可。其中幾個重點設定如下：  

- [EntitySetName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.entitysetname.aspx) ：在 DataSource 控制項中，使用 [EntitySetName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.entitysetname.aspx) 屬性指定資料來源的 Entity 名稱
- [EnableDynamicData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.datacontrolextensions.enabledynamicdata.aspx) ：在指定的資料控制項(如GridView)中，使用 [EnableDynamicData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.datacontrolextensions.enabledynamicdata.aspx) 以啟用動態資料行為。

下面例子，使用一個 [EntityDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.aspx) 搭配 GridView 載入 Entity Object ，並且開放行內編輯功能。  我們只要將 AutoGenerateColumns 設成 true ，並且透過 [EnableDynamicData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.datacontrolextensions.enabledynamicdata.aspx) 方法，將特定的 entity object ，指定啟用動態資料行為。  那麼就可以輕易地藉由 meta-data 來操作 Data Annotation 、 Field Validation 等效果。  

1. Setting [EntitySetName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.entitydatasource.entitysetname.aspx) to indicate the entity
```xml
<asp:EntityDataSource ID="EntityDataSource1" runat="server"
ConnectionString = "name=MyEntity"
DefaultContainerName = "MyEntity" 
EnableUpdate = "True"
EntitySetName = "Products">
</asp:EntityDataSource>

<asp:GridView ID="GridView1" runat="server"
DataSourceID="EntityDataSource1"
AllowPaging="True" 
AllowSorting="True"
AutoGenerateColumns="true">
<Columns>
<asp:CommandField ShowEditButton="True"/>
</Columns>
</asp:GridView>
```

2. Enable the Dynamic Data in Page\_Init event 
```c#
protected void Page_Init()
{
GridView1.EnableDynamicData(typeof(Products));
}
```

同樣的，在資料模型中，你也可以利用 partial class 來增加 meta-data 的描述設定。
```c#
[MetadataType(typeof(ProductMetadata))]
public partial class Products
{
partial void OnUnitPriceChanging(decimal? value)
{
if (value < 0)
{
throw new ValidationException("單價不可以小於0");
}
}
}

public class ProductMetadata
{
public object ProductID { get; set; }

public object SupplierID { get; set; }
public object CategoryID { get; set; }

[ScaffoldColumn(false)]
public object QuantityPerUnit { get; set; }

[ScaffoldColumn(false)]
public object UnitsOnOrder { get; set; }

[ScaffoldColumn(false)]
public object ReorderLevel { get; set; }

[DisplayFormat(ApplyFormatInEditMode = false, DataFormatString = "{0:c}")]
[Display(Name = "單價")]
public object UnitPrice { get; set; }

[Display(Name = "已停產")]
public object Discontinued { get; set; }

[Display(Name = "品名")]
[Required(), StringLength(50)]
public object ProductName { get; set; }

[Range(1, 999, ErrorMessage = "庫存量必須介於 1 ~ 999 之間")]
public object UnitsInStock { get; set; }
}
```

底下為執行結果，它為自動更據 meta-data 中的設定，自動 scaffolding ，並且執行相關的邏輯運算。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixwEZfVLvntnJykH3q94aSxqZhzOk_Yb1TzRnHCU-v8u79-xv0u1dhHDABXdYmCRQ0c10gVFvdSQw6TO_kACZezRDWi5Wa1Vc-GfrjvfCW_UEbaOg3phg3oWuaBnZheQYGCUOFPavG8kk/s850/dd-using-annotation-in-webpage.png)
## 參考資料  

- [ASP.NET 動態資料 Scaffolding 和頁面範本概觀](http://msdn.microsoft.com/zh-tw/library/cc488540.aspx)
- 
-