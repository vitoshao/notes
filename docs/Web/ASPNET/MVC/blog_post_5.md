---
title: MVC Validation
layout: default
parent: MVC
nav_order: 5
description: "MVC Validation"
date: 2013-03-20
tags: [Web,ASPNET,MVC]
postid: "201935458357990081"
---
一般網頁驗證可分成 Client 與 Server 端上進行，在以往若要同時撰寫二端的驗證程式都必須花費不少功夫，  現在 MVC 3.0 整合了多項技術，利用 Model Metadata 的設定，讓程式可以自動產生驗證功能的程式碼。  

# MVC Validation

## Server-side Validation

要驗證資料的有效性，我們可以在 server-side 撰寫驗證程式碼。其步驟如下：

1.在 View Page 中加入 [ValidationExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.aspx) ：

- [ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage.aspx) ：輸出驗證錯誤的訊息
- [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) ：輸出驗證錯誤的訊息清單

2.在 Server 端，透過 [ModelState](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.modelstate.aspx) 來自訂錯的內容
```xml
@model MVC4_EX1.Models.GuestBook
Title:
@Html.EditorFor(model => model.Title)
@Html.ValidationMessageFor(model => model.Title)
Content:
@Html.EditorFor(model => model.Content)
@Html.ValidationMessageFor(model => model.Content)
```
```c#
[HttpPost]
public ActionResult Create(GuestBook guestbook)
{
if (string.IsNullOrEmpty(guestbook.Title))
this.ModelState.AddModelError("Title", "Title不可以為空白");
if (string.IsNullOrEmpty(guestbook.Content))
this.ModelState.AddModelError("Content", "Content不可以為空白");

if (ModelState.IsValid)
{
db.GuestBooks.AddObject(guestbook);
db.SaveChanges();
return RedirectToAction("Index");
}
return View(guestbook); 
}
```

在上面程式碼中，我們使用 [ModelState.AddModelError](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.addmodelerror%28v=vs.118%29.aspx) 來加入自訂的錯誤訊息，  它會顯示在 View 的 [Html.ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage%28v=vs.118%29.aspx) 或 [Html.ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) 的位置，若沒有錯誤訊息則不會顯示。  隨後可利用 [ModelState.IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.isvalid.aspx) 來確認 Model 的有效性，以決定是否要取用 Model 的資料或是提示使用者錯誤訊息。  

程式的執行結果是：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjOjd3CBvnfv0auAefBuCnlcIJzwuFsMrBCEJVaHodVoBbfA_MoWbHH-qpDUSSYCwCBxapCPwDUkRiCdWenZjXjO1UBfD4OB2H5JkH8jlSoEU8l3KHOMYytNDN4q7UjgExHau-IE7pkyt4/s443/mvc-custom-varidation.png)

## Client-side Validation

ASP.NET MVC 本身並未內建自動化的 client validation，它是直接使用 Dynamic Data 所提供的 **Model Validation** 功能。  

**Model Validation** 指的是使用 metadata 來設定欄位資訊和驗證功能。  要套用這個功能，你只要在 Model 層中建立該物件類別的 metadata ，並直接加上驗證指令，  它就會配合用戶端的 Client-side library 做協同處理，達到 client-validation 的功能。  

這些 MetadataType 屬性，也就是所有的驗證規則，都包含在 [System.ComponentModel.DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) 命名空間之中。  要使用 client-validation 的步驟如下：  

1. 先建一個 metadata 類別 (using [DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) )
2. 再建立一個該資料模型的 partial 類別，並透過 [MetadataType](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.metadatatypeattribute.aspx) 屬性指定其 metadata 類別

#### Create Metadta Class
```c#
public class GuestBookMetadata
{
[Required]
public string Title { get; set; }

[Required]
public string Content { get; set; }
}

[MetadataType(typeof(GuestBookMetadata))]
public partial class GuestBook
{
}
```

#### Controller

在建立 metadata 類別之後，controller 會依據 metadata 中的設定去處理商務規則。  接著使用 [ModelState.IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.isvalid.aspx) 方法檢查是否所有的驗證規則都有過關，  如果驗證沒過關，錯誤訊息將被 render 在 ResultView 中，  如果驗證過關，則使用者將被帶到索引頁。  
```c#
[HttpPost]
public ActionResult Create(GuestBook guestbook)
{
if (ModelState.IsValid)
{
db.GuestBooks.AddObject(guestbook);
db.SaveChanges();
return RedirectToAction("Index");
}
return View(guestbook); 
}
```

#### View

底下是一個 Create 型態的強型別 view 。  在這個 View 中，使用 helper 類別中的 [ValidationMessageFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessagefor.aspx) 和 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) 來產生驗證項目。   同時必須要確認在 [ViewPage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewpage.aspx) 中是否有載入相關的 js 檔。  
若為 MVC2 則為 MicrosoftAjax.js 和 MicrosoftMvcValidation.js 。  
若為 MVC3 則為 jquery.validate.min.js 和 jquery.validate.unobtrusive.min.js 。  
若為 MVC4 則為 jqueryval.js 。  
```xml
@model MVC4_EX1.Models.GuestBook

@using (Html.BeginForm()) 
{
@Html.ValidationSummary(true)

<fieldset>
<legend>GuestBook</legend>

<div class="editor-label">
@Html.LabelFor(model => model.Title)
</div>
<div class="editor-field">
@Html.EditorFor(model => model.Title)
@Html.ValidationMessageFor(model => model.Title)
</div>

<div class="editor-label">
@Html.LabelFor(model => model.Content)
</div>
<div class="editor-field">
@Html.EditorFor(model => model.Content)
@Html.ValidationMessageFor(model => model.Content)
</div>
<p>
<input type="submit" value="Create" />
</p>
</fieldset>
}

@section Scripts {
@Scripts.Render("~/bundles/jqueryval")
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMe5QZ_cD7RCGD11zjnFRrgWV2XRnXa3CL1bBu24KiANQqMoJC62Id7RnIng6BPi77sVKvJmuAJH_F91Bus7p4RyYOp7HeCGi5_ufC74KDkyivfGvpdpQRZrbTuH_26enLwQIfhBjm7vw/s482/mvc-scaffolding-validation.png)

## 自訂錯誤訊息

當使用 Model Validation 規則來設定客戶端的驗證，它會使用預設的訊息內容，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUJVt6v_hNeYTsHRZhy2wq76hTb_NCG6IwvhSOiEgTW9yhVAzgV4Dmz6EyFm6QKc7IQtZRYKm2UhWPxlx6E3Xc_kDa7yPbHVb84omBLLB3whu8RweKGxa3JIabTolVwBjst5JDjkARxL0/s488/mvc-validation-01.png)

如果覺得訊息內容不夠友善，可以透過 ErrorMessage 參數來自行指定訊息內容。
```c#
public class GuestBookMetadata
{
[Required(ErrorMessage = "{0}是必要欄位")]
public string Title { get; set; }

[Required(ErrorMessage = "{0}是必要欄位")]
[StringLength( 100, MinimumLength=10, ErrorMessage = "{0}至少要{2}個字，不超過{1}個字")]
public string Content { get; set; }
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdymAqbnItlDq5qxiKrdbwytj40hqavrO-Z5crcdLKeEdpy9Kqn8ETTFDN5mXthGsjsBKXHMk-T67BiOxaQNAIFwqsqYfRhBciKyZ0vmEeqpLFC7PFPTuZL4KXpZemfqdBPz3lsu8C4Gs/s566/mvc-validation-02.png)

### 自訂錯誤訊息的 Format 項目

錯誤訊息不僅可以自訂，還可以使用 String Format 方式來指定訊息的格式。下表為各個驗證規則可設定的格式：

| 類別 | Format 項目說明 |
| --- | --- |
| [Compare] | {0}:欄位名稱  &lt;br&gt;{1}:比對的欄位名稱 |
| [CreditCard] | {0}:欄位名稱 |
| [CustomValidation] | {0}:欄位名稱 |
| [EmailAddress] | {0}:欄位名稱 |
| [MaxLength] | {0}:欄位名稱  &lt;br&gt;{1}:設定的長度 |
| [MinLength] | {0}:欄位名稱  &lt;br&gt;{1}:設定的長度 |
| [Phone] | {0}:欄位名稱 |
| [Range] | {0}:欄位名稱  &lt;br&gt;{1}:最大值  &lt;br&gt;{1}:最小值 |
| [RegularExpression] | {0}:欄位名稱  &lt;br&gt;{1}:規則運算式 |
| [Remote] | {0}:欄位名稱 |
| [Required] | {0}:欄位名稱 |
| [StringLength] | {0}:欄位名稱  &lt;br&gt;{1}:最大值  &lt;br&gt;{1}:最小值 |
| [Url] | {0}:欄位名稱 |

### 使用資源檔

上面例子，我們透過 ErrorMessage 參數來指定錯誤訊息的內容，其實這個設定，也可以直接設定在資源檔中，而且，針對不同語系，設定不同的格式內容。  要使用資源檔，必須透過 ErrorMessageResourceType 與 ErrorMessageResourceName 參數來指定資源檔名稱。  
```c#
public class GuestBookMetadata
{
[Required(ErrorMessageResourceType = typeof(Resource1), ErrorMessageResourceName = "RequiredField")]
public string Title { get; set; }

[Required]
public string Content { get; set; }
}
```

資源檔內容

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqZ9vnAeUn7CAyfrNwm4MW5bmXqOrDRZ_OcIwKfBdkAXbnI-V7EZ5VZqAkXrKUb8tnBw5iaow9zKvGl_k9AtouXkH8Z84lxBXU5lhWzxIgKnQwXH_5KrddkKc4rkZKMdpdLUeq_kEAPOg/s430/mvc-validation-03.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWJq5Kz9DdwdVXyZGMoLPbt0rKUJ_5eTFbXsg_oXay9TC_PmlfK4mxUYYdTenTHGG1uUXu1rUABo2E6bY_e8Z87xUqy3mIZvlnAJW3_0iZv1UMLbkrNd8PDGrDZ1KQbj1-Iu0zaLStOhw/s436/mvc-validation-04.png)

執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjysrck_Yy33haakc1gXH3uFy1fdenS1Zr2OCGmWhGnJax_Syzj8CKa2gNrHK_-Wi8EEIWo9KaIe1S8Ol5siZlel608isIuLJ_AlUnr_hliblUiHjVdBcYc_wcrF7UhCx0KeWFraQygbI0/s550/mvc-validation-05.png)

## 使用遠端驗證（Remote）

在驗證規則中，有一個稱為 Remote 的類別，當驗證邏輯無法在用戶端完成，必須將資料送回遠端伺服器以進行驗證就可以使用這個驗證規則。  它會將表單上的欄位資料，透過 Ajax 方式，叫用遠端的 API 以進行驗證。  例如，在註冊系統中，就常會使用這個機制，用以驗證所註冊的帳號是否已經存在資料庫中。  

### 1. 在 Model 中設定 Remote 驗證規則

使用 Remote 驗證，第一步仍然是在 model 的 property 上，套用 Remote 驗證規則。   [RemoteAttribute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.remoteattribute.aspx) 中的第一個參數是 action 方法，第二個參數是 controller 。  
```c#
public class GuestBookMetadata
{
[Required(ErrorMessage = "{0}是必要欄位")]
[Remote("IsTitleAvailable", "GuestBook")] 
public string Title { get; set; }

[Required(ErrorMessage = "{0}是必要欄位")]
[StringLength( 100, MinimumLength=10, ErrorMessage = "{0}至少要{2}個字，不超過{1}個字")]
public string Content { get; set; }
}

[MetadataType(typeof(GuestBookMetadata))]
public partial class GuestBook
{
}
```

### 2. 在 Controller 中，撰寫驗證邏輯的 Action 方法

使用 Remote 驗證，第二步就是在 controller 中撰寫驗證的 action 方法。  
```c#
public class GuestBookController : Controller
{
public ActionResult IsTitleAvailable(string title)
{
GuestBook guestbook = db.GuestBooks.FirstOrDefault(c => c.Title == title);

if (guestbook == null)
return Json(true, JsonRequestBehavior.AllowGet);
else
return Json("This Title is already exist", JsonRequestBehavior.AllowGet);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVrWw_6QfUkMrcJW536Z52GLNv3q6o4urc11wzanTPQL-e14YMi4T65IX1NVoSZX-G4BQLI0nr8ANV3u0A6JXtAAhunxoYc2LhMQKbmkQiqn5GkTPMTBomyQ5ba4tDEPc42yaaQQAZSBY/s652/mvc-validation-06.png)

PS. Json 方法回傳內容中的第一個參數，若為字串型別，前端的 Unobtrusive JS 會當做錯誤訊息回應。

# jQuery.validation

MVC 的 Client 端驗證，整合在 [HtmlHelper](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.htmlhelper.aspx) 中，HtmlHelper 會根據 Metadata 資訊，輸出相關的 HTML 以及驗證程式碼。  從 MVC 3.0 開始，前端驗證都改用熱門的驗證工具： jQuery Validation 與 Unobtrusive JavaAcript 模式。  在 4.0 時已經設定在 Bundle 中，可以使用 @Script.Render("~/bundles/jqueryval") 載入相關的 .js 檔案。  
```xml
@model MvcApplication1.Models.Customers

@Html.LabelFor(model => model.ContactName)
@Html.EditorFor(model => model.ContactName)
@Html.ValidationMessageFor(model => model.ContactName)

@section Scripts {
@Scripts.Render("~/bundles/jqueryval")
}
```

[Unobtrusive Javascript](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript) 是一種將Javascript從HTML結構抽離的設計概念(延伸閱讀:       [kewang](http://kewang.pixnet.net/blog/post/24177235),       [ericsk](http://blog.ericsk.org/archives/699))，避免在HTML標籤中夾雜onchange、onclick Attribute掛載Javascript事件，讓HTML歸HTML、Javascript歸Javascript，功能權責清楚區分，HTML也變得清爽容易閱讀。例如：  
```xml
<input type="text" onclick="window.open('url')" />
```

改用 Unobtrusive Javascript 模式可以增加可讀性：
```xml
<input type="text" data-openwindow="url" />
```

詳細內容請參考黑暗大的 Blog [http://blog.darkthread.net/post-2011-07-27-unobtrusive-jquery-validation.aspx](http://blog.darkthread.net/post-2011-07-27-unobtrusive-jquery-validation.aspx)

# Store Generated Pattern

Entity Framework 有個 [StoreGeneratedPattern](http://msdn.microsoft.com/zh-tw/library/system.data.metadata.edm.storegeneratedpattern.aspx) 屬性，  可以設定在 SSDL 所定義的資料庫結構中的欄位，  當 insert 或 update 發生時，應該採用何種方式更新欄位值。可使用的設定值如下：  

- None：預設值，表示不是由資料庫決定，所以會由程式端取得資料。
- Identity：該欄位由在 insert 時由資料庫決定，update 時不更新。
- Computed：該欄位在 insert 或 update 時都由資料庫決定。

下圖是設定 StoreGeneratedPattern 屬性的位置。  在下圖例子中，當新增一筆 GuestBook 資枓時，因為該 Model 中的 PostTime 欄位的 StoreGeneratedPattern 屬性已設定為 Identity ，所以當資料 Insert 時，該欄位就會自動依據資料庫的預設值屬性去產生。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwHKjJIGMBpFVZcvKLNZaMBeZ-5grmVyoDX3HA_WNoWWklPSCi3FCFGlbNkehZFQuTs2IG8_V60jxEikx9YThyphenhyphenzZgTjI3_8641irfn4X9zfBpxPMhuX0UbOCKI6YnGQUKB3MpOMUUl-FU/s507/mvc-store-generated-pattern.png)