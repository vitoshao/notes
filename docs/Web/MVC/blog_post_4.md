---
title: Views
layout: default
parent: MVC
nav_order: 4
description: "Views"
date: 2013-03-19
tags: [Web,MVC]
postid: "8681402515046123916"
---
View 是用來顯示使用者介面。  所有的 View 都存在 **Views** 目錄下的子目錄中。  每一個 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 名稱，在 **Views** 中都應該對應到一個相同名稱的子目錄，  然後再依據該控制器的 action method 名稱加入適當的 View 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibk1QQxEeXqc9ofzPjBcPEB-1YRVAvBv0zu1SR12akgokc3WLRgOvUyi6SmHJk90LUs-Y1ULU76gYuSOcELOiPLOhZWPGJWoBsb2hxEgaWepqJ9aGATZKaqtgINBSTsEOYXKyfrCuYctw/s245/mvc-Views1.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgISWB4M05PW-PhL-Ddms21sWykDtmbwDM0mt6LG-x-v73wbJIbqAlhYHH1Qg4wbpNt2HLQNRyoLPyDsZrXRbaGOMq3emJJnywr9w9yYuiQy8K5ndKfqCxcoUtSiMT4dPSgYsVn-1csQK8/s247/mvc-Views2.png)

在一般情況下，action method 執行完畢後所回傳的 View ，其名稱會和 action method 的名稱相同。  但是，你也可以回傳一個特定名稱的 Veiw ，例如：return View("SomeView").  
```c#
public ActionResult Edit(string id)
{
Customers customers = db.Customers.Single(c => c.CustomerID == id);
return View(customers);                 //return Edit View
}

public ActionResult Edit(string id)
{
Customers customers = db.Customers.Single(c => c.CustomerID == id);
return View("VIPEdit", customers);      //return VIPEdit View
}
```

# Creating View

View 是用來展示資料用的，所有輸入的邏輯運算應該在 controller 中就已經完成，所以 View 不須要有 code-behind 類別。  若要取得 controller 傳進來的資料，只要在標籤中透過 inline code 就可以。  

## View 檢視引擎

View 的  inline code ，在 MVC3 中，支援二種寫法，所以在建立檢視時，你必須先決定使用哪一種檢視引擎。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_28QDx09oUqJoDXf9U2mhjGsF6xmoI9lOld7cK8A59L30VdBgWXcCetvf558u7QocmmO6xmKVlNtlyhJXNpkbDDgySDmtpY1OOFlTbqAJQjkD2p0-n40ii5xgeWrY2Zuv3opiP0XI7sw/s303/mvc-view-engine.png)

### ASPX 檢視引擎（ASPX View Engine）

使用 ASPX 檢視引擎建立的 View ，副檔名為 .aspx ，其程式碼部份必須使用 &lt;% ... %&gt; 包起來。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0CpJzyRBrpTINqLgGEpqsEOPOJXyObJxLqNHlNqLXbip4n5zSr7YisEstfpkCTS8aslF_foRPbtQisc0OTUJIbfV30mxNF0Z18N-698nNHah1bTj8QzrnyRhugsTbFercdked3UKR_yQ/s758/mvc-view-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEifve-h1Fh7m7Fke0A8w928bcnN6CmDOyLW3bE3jwbGwSO0lO6RxPPxpyCPLYxU6Zj664jhb5guUUoOgu3rHnzsmK7UjKgHW8BINcy-Ky4BsFbjsPQDkNdmUzJwS0IuxzySVWaWkh1qycs/s284/mvc-view-02.png)

### Razor 檢視引擎（Razor View Engine）

使用 [Razor](http://weblogs.asp.net/scottgu/archive/2010/07/02/introducing-razor.aspx) 檢視引擎建立的 View ，副檔名為 .cshtml ，其程式碼部份開頭為「@」。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjV5Gpc4E6toO3QmJ7mdkRxA3BGN2JuHZ9jS3SPB1wHZSqYmoraK2is38-GQHmBHTbqSewObnp4w_Ph5Dp1H-OZLnyksPHhYLUPNBn49rstmMecRV768f23PUPQbXxJO4M-2h_QNkO7B-M/s608/mvc-view-03.png)

## 建立 View

MVC 的 View 包含下列幾種不同類型：

- [ViewPage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewpage.aspx) ：(.aspx) MVC 頁面的基底類別
- [ViewMasterPage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewmasterpage.aspx) ：(.Master) MVC 主版頁面的基底類別
- [ViewUserControl](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewusercontrol.aspx) ：(.ascx) MVC 使用者控制項的基底類別

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9VtP-KREyj5nKs7jCBDMAoqMRnTfDN8rW5tnqDcVLXiKj6wqzL0ky_FE_NEic3qOmWC1N4-5goGvBpJiRKiMepG_TV7ybgeuT2VovuvG35ut1dTfXSnkreRL4y_JTdpvDnB3gO4Asg2s/s330/mvc-aspxview.png)

不過，以上項目都是使用 ASPX 檢視引擎模式才有的項目，若是使用 [Razor](http://weblogs.asp.net/scottgu/archive/2010/07/02/introducing-razor.aspx) 檢視引擎，在 Visual Studio 中所對應的項目分別為：

- 檢視頁面 (.cshtml)
- 版面配置頁 (.cshtml)
- 部分頁面 (.cshtml)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAItF7uh3cez3eqv9_9i8Zp0Zz8rrddn1BbeW-6j09zpEQTZTMCcyR2b6cx_srucYIKDCr3I5kNX2_zeNhEkiauXjjblp8GvkVy9FuNfL_kkVBzgWiJNz24-z5Zcy4qyvXmzKi070QmgY/s328/mvc-razorview.png)

當加入一個含有模型類別的控制器時，系統會自動幫我們建立標準的 View Page。  這些 View Page 的副檔名為 .aspx ，繼承自 [System.Web.Mvc.ViewPage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewpage.aspx) 類別 (基底也是 ASP.NET Page 類別) 。  你也可以在 ASP.NET MVC 的 View 中加入 Master Page 或 User Control 。在 ASP.NET MVC 中的 User Control 通常稱之為 **Partial View** 。

# Using the HTML Helper Classes

View 裡面有個輔助物件叫做 [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) ，它提供許多擴充方法可以用來支援 render 頁面控制項的內容，來簡化程式碼的撰寫。  例如，建立下拉選單的清單項目、加密、格式化字串等等。  

An [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) instance is exposed through the [ViewPage.Html](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewpage.html.aspx) property.  
```xml
<p> <label >@ViewData["Msg"].ToString()</label> </p>

<!-- using HtmlHelper -->

<p> @Html.Label(ViewData["Msg"].ToString()) </p>
```

更多 [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) 的用法，請參考 [這篇介紹](http://vito-note.blogspot.com/2013/01/htmlhelper-class.html)

# Creating Strongly Typed Views

## Using Strongly Typed View

由前面我們知道，要由 Model 傳資料到 View ，可以將資料存放在 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 中。  強型別的 View 指的是直接由 Model 傳進來一個特定物件，而不是存放在 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 中。  要建立強型別的View ，你可以在 View 上頭加上型別宣告，或者在建立 View 時，直接設定為強型別檢視。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHDb7QDRJ_Acu_8FRdzqQp5G6bwayIqWDJtx1QgnAgVfB8AVHGce1F7RJV8is2EvUXWcqIIbX-jwgOEbLWdb6Io0vepEICf56gTpJsAe0l2u_GbwuQ7DwdSCgzWoD5rs40ooxZf1xhrRs/s522/mvc-add-views-strongtyped.png)

#### 建立強型別的 View

在 razor 模式中，使用 **@model** 宣示詞來定義這個 View Page 的型別
```xml
//aspx
<%@ Page Language="C#" Inherits="System.Web.Mvc.ViewPage<MvcApplication1.Models.Customers>" %>

//razor
@model MvcApplication1.Models.Customers
```

#### 由 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 中傳送強型別參數給 View 
```c#
public ActionResult Details(string id = null)
{
Customers customers = db.Customers.Single(c => c.CustomerID == id);
if (customers == null)
{
return HttpNotFound();
}
return View(customers);
}
```

#### 在 View 中使用強型別物件
```xml
@model MVC4_EX1.Models.Customers
<div class="display-label">
@Html.DisplayNameFor(model => model.CompanyName)
</div>
<div class="display-field">
@Html.DisplayFor(model => model.CompanyName)
</div>
```

## Using Scaffold Template (鷹架)

在新增 View 的設定視窗中，有個「scaffold 樣板」欄位，它是用來設定 View Page 預設的呈現型態，如 Create, Delete, Details, Edit, List, or Empty 等樣式.   所以，  若要建立一個編輯用的 View ，就可以選用 Edit 樣式；  若要顯示清單資料時，可以選用 List 樣式。  

當選擇 List 樣式時， View Page 的型別會被設定為 IEnumerable&lt;&gt; 泛型列舉型別，所以它可以接收來自 Model 的列舉物件清單。  只要套用 foreach 指令，搭配 [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) 方法，就可以輸出每一筆清單資料。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8EnvG5pv-Gq0XUT4_w62CNMA0dCHGrF-1B71mVv-9YckezAeVJizEZ21iBYQbAtKOM526TRLTk9Wd7ym3SBf8tWYqIjlaCMywQfKGUnUP583JlcA252oxxXfl7lITezNeIguu1-HEMGI/s411/mvc-scaffold.png)
```xml
@model IEnumerable<MvcApplication1.Models.Customers>

<table>
@foreach (var item in Model)
{
<tr>
<td>@Html.DisplayFor(modelItem => item.CompanyName)</td>
<td>@Html.DisplayFor(modelItem => item.ContactName)</td>
<td>@Html.DisplayFor(modelItem => item.ContactTitle)</td>
<td>@Html.DisplayFor(modelItem => item.Address)</td>
<td>@Html.DisplayFor(modelItem => item.City)</td>
<td>
@Html.ActionLink("Edit", "Edit", new { id = item.CustomerID }) |
@Html.ActionLink("Details", "Details", new { id = item.CustomerID }) |
@Html.ActionLink("Delete", "Delete", new { id = item.CustomerID })
</td>
</tr>
}
</table>
```

# Uing Model Validation in Views（資料驗證）

Model Validation 指的是使用 metadata 來設定欄位資訊和驗證功能。  要套用這個功能，你只要在 Model 層中建立該物件類別的 metadata ，並直接加上驗證屬性，  它就會配合用戶端的 Client-side library 做協同處理，達到 client-validation 的功能。  

這些 MetadataType 屬性都包含在 [DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) 命名空間之中。  要使用 Model Validation ，你必須調整以下內容：  

#### 1. Create Metadta Class

1. 先建一個 metadata 類別 (using [DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) )
2. 再建立一個該資料模型的 partial 類別，並透過 [MetadataType](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.metadatatypeattribute.aspx) 屬性指定其 metadata 類別
```c#
public class CustomersMetadata
{
[StringLength(40)]
[Required(ErrorMessage = "Customer id is required.")]
[Display(Name="客戶ID")]
public object CustomerID { get; set; }

[Required(ErrorMessage = "ContactName is required.")]
[Display(Name = "連絡人")]
public object ContactName { get; set; }

[Required(ErrorMessage = "Phone is required.")]
[Display(Name = "連絡電話")]
public object Phone { get; set; }
}

[MetadataType(typeof(CustomersMetadata))]
public partial class Customers
{
}
```

#### 2. Controller

當 metadata 類別建立之後，controller 會依據 metadata 中的設定去處理商務規則。  接著使用 [ModelState.IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.isvalid.aspx) 方法檢查是否所有的驗證規則都有過關，  如果驗證沒過關，錯誤訊息將被 render 在 ResultView 中，  如果驗證過關，則使用者將被帶到索引頁。  
```c#
[MyActionFilter]
public ViewResult Index()
{
return View("ResultView", customer);
}

public ViewResult Index(Customers customer)
{
[HttpPost]
public ActionResult Index(Customers customer)
{
if (ModelState.IsValid)     //模型的驗證狀態
return RedirectToAction("Index");       //if pass, redirect to index action 
else
return View("ResultView", customer);    //if fail, return Index View
}
}
```

#### 3. View

在 View 裡，你可以設定驗證後的提示訊息。  例如，底下是一個 Edit 型態的強型別 view ，  在這個 View 中，使用 helper 類別中的 [ValidationMessageFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessagefor.aspx) 和 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) 來產生驗證項目。   
```xml
@model MvcApplication1.Models.Customers
@{
ViewBag.Title = "ViewPage2";
}
<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>

@using (Html.BeginForm())
{
<table>
<tr>
<td>
@Html.LabelFor(model => model.CustomerID)
</td>
<td>
@Html.EditorFor(model => model.CustomerID)
@Html.ValidationMessageFor(model => model.CustomerID)
</td>
</tr>
<tr>
<td>
@Html.LabelFor(model => model.ContactName)
</td>
<td>
@Html.EditorFor(model => model.ContactName)
@Html.ValidationMessageFor(model => model.ContactName)
</td>
</tr>
<tr>
<td colspan="2">
<input type="submit" value="submit" />
</td>
</tr>
</table>
@Html.ValidationSummary(true)
}
```
```xml
@model MvcApplication1.Models.Customers
@{
ViewBag.Title = "ViewPage3";
}

<h1>You Type ...</h1>

CustomID: @Html.Label(Model.CustomerID) <br />
ContactName: @Html.Label(Model.ContactName) <br />
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMe5QZ_cD7RCGD11zjnFRrgWV2XRnXa3CL1bBu24KiANQqMoJC62Id7RnIng6BPi77sVKvJmuAJH_F91Bus7p4RyYOp7HeCGi5_ufC74KDkyivfGvpdpQRZrbTuH_26enLwQIfhBjm7vw/s482/mvc-scaffolding-validation.png)

# Scaffold 樣版

在建立 View 時，有個選項是 Scaffold 樣版，若有選用，則系統會更據 Model 的 Metadata 自動產生 View Page 的基本樣式。它共有６種樣版：Create、Detele、Details、Edit、Empty、List。  

所以 ModelMetadata 不僅可以用來設定資料的驗證，也可以用來設定頁面內容的呈現。底下幾個類別屬性是 Scaffold 在建立樣版時較常會使用到的設定。  

- [DisplayAttribute](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayattribute%28v=vs.100%29.aspx) ：設定標題要顯示的文字。
- [KeyAttribute](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.keyattribute%28v=vs.100%29.aspx) ：在 View 中，會使用 Html.HiddenFor() 來產生頁面資訊。
- [ScaffoldColumnAttribute](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldcolumnattribute.aspx) ：指定該欄位是否使用 Scaffolding 。

# MVC 的 Master Page

###### Razor 與 Aspx 的 MasterPage 比較

|  | Razor | ASPX |
| --- | --- | --- |
| 架構 | 主要由 body 和 section 二種區塊構成，  body 是必要的區塊，且只有一個；  section 區塊可以設定零個或多個。 | 由一個或多個 ContentPlaceHolder 構成所有區塊。 |
| MasterPage  &lt;br&gt;範例 | ```xml
<!DOCTYPE html><br><br><html><br><head><br>    <meta name="viewport" content="width=device-width" /><br>    <title>@ViewBag.Title</title><br>    @RenderSection("header", false)<br></head><br><body><br>    <div><br>        @RenderBody()<br>    </div><br>    @RenderSection("footer", required:false)<br></body><br></html><br>``` | ```xml
<%@ Master Language="C#" Inherits="System.Web.Mvc.ViewMasterPage" %><br><!DOCTYPE html><br><br><html><br><head id="Head1" runat="server"><br>    <meta name="viewport" content="width=device-width" /><br>    <title><asp:ContentPlaceHolder ID="TitleContent" runat="server" /></title><br></head><br><body><br>    <div><br>        <asp:ContentPlaceHolder ID="MainContent" runat="server"><br>        <br>        </asp:ContentPlaceHolder><br>    </div><br></body><br></html><br>``` |
| 內容頁 | ```xml
@{<br>    ViewBag.Title = "Post";<br>    Layout = "~/Views/Shared/_Blog.cshtml";<br>}<br><br>@section header{<br>    @\*設定相關的 meta 資訊\*@<br>    <meta /> <br>}<br><br><h2>Post</h2><br><p>Content</p><br><br>@section footer{<br>    @\*加入本頁特定的 js\*@<br>    <script type="text/javascript"></script><br>}<br>``` | ```xml
<%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/ViewMasterPage1.Master" <br>    Inherits="System.Web.Mvc.ViewPage<dynamic>" %><br><br><asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server"><br>    View1<br></asp:Content><br><br><asp:Content ID="Content1" ContentPlaceHolderID="MainContent" runat="server"><br>    <h2>View1</h2><br></asp:Content><br>``` |

Razor 補充說明：

- 內容頁中，只要不是在 section 中的內容，最後都會由主版頁面的 @RenderBody() 輸出。
- 若主版頁中的 RenderSection 參數包含 required:false ，表示內容頁可以不需要這個區塊。

# Creating Partial (Child) Views

部份檢視 (partial view)，類似 web form 中的使用者控制項(user control)。  也就是可以將部份內容，獨立製作，以達共享的目的。  若使用 aspx View ，它一樣被稱為「使用者控制項」，若使用 razor View ，它則被稱為「部份頁面」。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJNUGaQUUa6k6tsSYHdrswvYj7NwJY_Z4aVSevGVy24fY5rXDmmsCwt8_CH7v38u0wQ7J0jV9qNDuDin2dWYPD7nDXkPjak-3BRVi1zymVsy-Qw9Ngi7exl1emYvoeXS7I8dfm_Nlwz7s/s538/mvc-add-partial-view3.png)

### Creating Partial Views

建立 partial view ，有二個方法： (1)直接新增一個 partial view 項目；(2)新增一個 view , 將其屬性設成部份檢視。  

#### 新增一個 partial view 項目

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNqRwJAZENZ6MNO6Ni8ap6Bj_SkFsd7l0QguTxOeaywZtgSCbW30b5V7PAYrtv5Xfi2IxKMyZztsIRihkvmJnofj5yaD3dXZLz2gcytu74clTwppFcD21c3ZUIWph0UDcp3cvAsUIR9VQ/s616/mvc-add-partial-view2.png)

部份頁面和檢視頁面其實是相同的，只是部份頁面含有預設的html tag，而檢視頁面則是完全空白的檔案。

#### 新增一個部份檢視的 View

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqd6QrMcDNejb8V5EGB3vNqKHS3aP4wX3Zzb7iL3IM_F9GbyAJvvtZMP2DG7qs2cXJRgxk-5ef4BojVEeQ_lGJlepElGhXdSaRtWVR9RnFzXC1grK5I3pH1SEgTUaVs_3jz75iXcsHgXU/s522/mvc-add-partial-view1.png)

### Using Partial Views

要載入 PartialView 有二種模式：

- Render Partial ：透過 ViewName 或 ViewPath 直接載入另一個 [ViewPage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewpage.aspx) 或 PartialPage 。例如將版權宣告設計在一個 PartialPage，就可以方便內嵌到各個頁面。
- Render [Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) ：透過 controller 的 action 方法，以取得 PartialView 的內容。例如在一個顯示部門資料的頁面中，同時顯示包含該部門的員工資料。

與這二個模式相關的 [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) 方法如下：  

- [Html.RenderAction](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.renderaction.aspx) ：叫用指定的子系動作方法，將結果以**內嵌(inside)**形式，嵌在 parent view 之中。
- [Html.RenderPartial](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.renderpartialextensions.renderpartial.aspx) ：將指定的部分檢視，轉譯成 HTML，並直接內嵌在 parent view 之中。

另外，如果想將取得的結果以字串型式回傳，可以使用以下方法：  

- [Html.Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) ：叫用指定的子系動作方法，將結果以**HTML字串**形式傳回結果。
- [Html.Partial](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.partialextensions.partial.aspx) ：將指定的部分檢視，回傳轉譯後的 html 字串。
```c#
// Action

public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, object routeValues);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, string controllerName);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, string controllerName, object routeValues);

public static void RenderAction(this HtmlHelper htmlHelper, string actionName);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, object routeValues);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, string controllerName);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, string controllerName, object routeValues);

// Partial 

public static MvcHtmlString Partial(this HtmlHelper htmlHelper, string partialViewName);

public static void RenderPartial(this HtmlHelper htmlHelper, string partialViewName);
```
```xml
@model MvcApplication1.Models.Customers

CustomID: @Html.Label(Model.CustomerID) <br />
ContactName: @Html.Label(Model.ContactName) <br />

@Html.Action("CustomerOrders", new { CustomerID = Model.CustomerID })  
```
```xml
@model IEnumerable<MvcApplication1.Models.Orders>
@{Layout = null;}

<table>
@foreach (var item in Model)
{
<tr>
<td>@Html.DisplayFor(modelItem => item.CustomerID)</td>
<td>@Html.DisplayFor(modelItem => item.OrderDate)</td>
<td>@Html.DisplayFor(modelItem => item.ShipAddress)</td>
<td>@Html.DisplayFor(modelItem => item.ShipCity)</td>
</tr>
}
</table>
```

上面這個範例，當收到 ../Customers/Index 需求時，  首先 CustomersController.Index 會先被執行，並回傳 IndexView。  然後，由 IndexView 中，再 call out 去執行 CustomersController.CustomerOrders，並回傳 CustomerOrdersView。  而且，CustomerOrdersView 的內容會內嵌在 IndexView 之中。  

如果在 partial view 中想要存取 parent view 中的資料，parent view 可以將資料先存於 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 物件中。  但是， partial view 存取到的資料，只是該資料的複本，任何修改都會局限在複本中而已。

### examples

CopyRightControl is a UserControl in Shared Folder  
ShowTime is an action method in HomeController.  

下列方法都可以用來轉譯使用者控制項.  
```c#
@Html.Partial("CopyRightControl")

@{ Html.RenderPartial("CopyRightControl"); }

@Html.Action("ShowTime", "Home")

@{ Html.RenderAction("ShowTime", "Home"); }
```

# Passing data to View

## 利用 ViewData 、 ViewBag 物件

- [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) ：Gets or sets the dictionary for view data.
- [ViewBag](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewbag.aspx) ：Gets or sets the dynamic view data dictionary.
- [TempData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.tempdata.aspx) ：Gets or sets the dictionary for temporary data。
- [ValidateRequest](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.validaterequest.aspx) ：Gets or sets a value that indicates whether request validation is enabled for this request.

[ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 和 [ViewBag](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewbag.aspx) 的資料經過 RedirectToAction 後就會消失，所以若遇到這種情況且要暫時保存資料，就必須使用 [TempData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.tempdata.aspx) 。  

[ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 是 [Dictionary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewdatadictionary.aspx) 型別，而 [ViewBag](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewbag.aspx) 是 [dynamic](http://msdn.microsoft.com/zh-tw/library/dd264741.aspx) 型別，直接使用 [ViewBag](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewbag.aspx) 可以避免轉型。  
```c#
public ActionResult CacheTest1()
{
ViewBag.Now = System.DateTime.Now;
ViewBag.Message = "Wellcom to MVC";

ViewData["Now"] = System.DateTime.Now;
ViewData["Message"] = "Wellcom to MVC";

return View();
}
```
```xml
<div>
<h2>Using ViewBag</h2>

Now : @ViewBag.Now  <br />
Message : @ViewBag.Message  <br />

<h2>Using ViewData</h2>

Now : @ViewData["Now"]  <br />
Message : @ViewData["Message"]  <br />
</div>
```

## 傳送強型別物件

使用強型別撰寫程式，可以減少執行階段錯誤發生的機會。

### 建立 Model

首先先建立一個模組類別
```c#
public class CacheModel
{
public string Message { get; set; }
public DateTime Now { get; set; }
}
```

### 在 Controller 中設定 Model 中的欄位

接著在 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 中，新增一個模組類別的實體，並依需求設定類別的欄位值，同時將它當成參數傳遞給 View 。
```c#
public ActionResult CacheTest2()
{
CacheModel model = new CacheModel();
model.Now = System.DateTime.Now;
model.Message = "Wellcom to MVC";
return View(model);
}
```

### 在 View 中讀取 Model 中的欄位

若在 razor 模式中，必須使用 @model 宣示詞來定義這個 View Page 的型別
```xml
@model MvcApplication1.Models.CacheModel

<!DOCTYPE html>

<html>
<head>
<title>CacheTest2</title>
</head>
<body>
<div>
<h2>Using StrongType</h2>

Now : @Model.Now  <br />
Message : @Model.Message  <br />
</div>
</body>
</html
```
## 參考資料  

- [ASP.NET MVC3 Razor 初心者容易遇到的問題](http://demo.tc/Post/679)
- 
-