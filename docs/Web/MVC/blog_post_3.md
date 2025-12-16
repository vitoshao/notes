---
title: Controller
layout: default
parent: MVC
nav_order: 3
description: "Controller"
date: 2013-03-19
tags: [Web,MVC]
postid: "7831971368716362288"
---
Controller 的工作主要負責從 View 中接收指令，然後把指令發送到相應的 Model 處理，在 Model 處理完成後就再把處理結果回傳到 View 去。  Controller 的基底是 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 類別。  所有的 Controller 都存放在 **Controllers** 目錄。  

每一個 Controller 都包含許多 action method ，用來對應到使用者的操作行為。  這關係通常是１對１的，例如，若要提供使用者執行 CRUD 四種操作，你就必須建立 create, run, update, delete 四個 action。  

# Creating Controllers

By right-click the Controllers folder, you will see the Add [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) dialog box.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSmti0VFFpRK7Ti1PtCUMn56_AvmmGOw3lLQxOARnTrz1BycV2RH3cp0np067aWDMWLaIbjDGXTJIo1KKI0b6E2iIcajy53LbN-UlUNSWg7i0Tku_-YtQQ-qQCRI1nCBydsukTr9plM5o/s714/mvc-add-controller.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjGg-I7CAWb1ycgtmgkccNvuaXJot9-DjS81nB5xGMr9NEY3Zt4yCidw2IAIJTXcnXkgx8f62pudLU4qk9MmgF43c4l39dJz5-V3newRlqycXXD9VgOBqNwFUnVwHxNx0S_SxWgnhDxxpg/s615/mvc-add-controller2.png)

This tool will create a **controller template**.  By default, this controller template will generates code with the following methods : Index, Details, Create, Edit, and Delete.  
```c#
public ViewResult Details(string id)
{
Customers customers = db.Customers.Single(c => c.CustomerID == id);
return View(customers);
}
```

This action method would then be called based on the URL routing.   By default, a call to http://mySite/customer/details/ALFKI would invoke the CustomerController.Details method and pass the customer ID (ALFKI) as a parameter.   The view would then be returned as the Detail.aspx page inside the Views/Customer folder.   The view would then use the Customer object sent to it to display information  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinCLGxLvR3EwdnwIBhznYmbYkaWHR-ykGxVSIjMf4t6H8dJjgvh8eWI9jgmm0tabi0TAcwX6956R_5EoFt55PnKtv07AP6TSEkXVqcTgdnE_SZK1ita5CKvJJOzCSBj5yTuPNWO56IKXw/s835/mvc-sample1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinCLGxLvR3EwdnwIBhznYmbYkaWHR-ykGxVSIjMf4t6H8dJjgvh8eWI9jgmm0tabi0TAcwX6956R_5EoFt55PnKtv07AP6TSEkXVqcTgdnE_SZK1ita5CKvJJOzCSBj5yTuPNWO56IKXw/s835/mvc-sample1.png)

## 範例：加入控制器

當 Model 部份建立好之後，你就可以透過工具來快速的建立與該 Model 相關連的 Controller .  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSmti0VFFpRK7Ti1PtCUMn56_AvmmGOw3lLQxOARnTrz1BycV2RH3cp0np067aWDMWLaIbjDGXTJIo1KKI0b6E2iIcajy53LbN-UlUNSWg7i0Tku_-YtQQ-qQCRI1nCBydsukTr9plM5o/s714/mvc-add-controller.png)

MVC 本身包含很多 Scaffold 範本，可以幫助開發者快速的做出基本的程式架構，底下因為要使用 Entity Framework ，所以選用「具有讀取/寫入動作和檢視、使用 Entity Framework 的 MVC 控制器」。  在這個畫面中，如果你找不到先前建立的 Model 類別，可以對網站先執行建置，讓系統取得該類別的參考。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9RDhAksQG25qpXkfVZsArNhgwuLvagP97hqcNQiY7r4Vzt-g1n0OeDfgMRIKi98oIWdurw3h_K_HDzqC2Fc-Vy8PxLJMRVEik5bTWPEl9xesBe7KSuQxeZ0YA1uJbqxVHVOshO6hcfmg/s615/mvc-add-controller-2.png)

按下「加入」之後，系統便會自動產生 Controller 及 View ：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7UxTKx9mpoF2sJLE2apmpH4bivVnLqickav-ASEibXRRCbzDArXGlybfT3MzQPcEwaxa7xE-6Oec5HrlMtrWxYc0Fh7wEmUgmq1wDI2P4ppR0PEsrVBH1BNqu84gCaNT_sq41WcQ27K8/s285/mvc-add-controller-3.png)

# Returning Different ActionResult Objects

在 MVC 架構中，Controller 負責整個程式的流程走向，當它接收到客戶端提出的 Request ，  它會透過 action method 向 Model 提取資料，再將資料送至 View 中產生回應所需要的畫面或資料，並將結果回應給客戶端。  

在 Controller 中，只要是 public 的方法，預設都會是 Action 方法，若不想成為 Action 方法，可以加上 NonAction 屬性。  

## 回傳 ActionResult 物件

Action 方法預設的回傳型別為 [ActionResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.actionresult.aspx) 類別  它是最常見的回傳型別，也是許多種回傳型別的基底類別。  如果要回傳一個網頁，就必須回傳一個 ViewResult 型別；如果要轉向到另一個 controller ，就必須回傳一個 RedirectResult 。   下面九個是 action method 常用的回傳型別：  

- [ViewResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewresult.aspx)(View) ：return a webpage
- [PartialViewResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.partialviewresult.aspx)(PartialView) ：Used to send a section of a view to be rendered inside another view.
- [RedirectResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.redirectresult.aspx)(Redirect) ：Used to redirect to another controller and action method based on a URL.
- [RedirectToRouteResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.redirecttorouteresult.aspx)(RedirectToAction, RedirectToRoute) ：Used to redirect to another action method.
- [ContentResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.contentresult.aspx)(Content) ：Used to return a custom content type
- [JsonResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.jsonresult.aspx)(Json) ：Used to return a message formatted as JSON
- [JavaScriptResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.javascriptresult.aspx)(JavaScript) ：Used to return JavaScript code
- [FileResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.fileresult.aspx)(File) ：Used to send binary output as the response.
- [EmptyResult](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.emptyresult.aspx) ：Used to return nothing (void) as the result.

controller 提供數個 helper method 可以用來產生要回傳的型別物件，例如：叫用 View() 方法，可以回傳 ViewResutl ，叫用 Redirect() 可以回傳一個 RedirectResult 。  各個回傳型別相對應的 helper method ，註明在上面清單中的括號中。  

除了以上回傳型別，基本的字串、數值，布林也都可以是回傳的資料型別。  另外還有一些特殊的回傳型別，例如：HttpNotFoundResult 會回傳 404 的錯誤，或是直接使用 HttpStatusCodeResult 回傳指定的狀態代碼。  

#### ActionResult 類別

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDXxDHdlmstDJ2BpaAEVb721uhxTV5Ekx1jjRIr0MCdFd4rybxrGPn_Nk40I_FR0aSwbPb_lBBWBIVrDzg1gmhBTsvxz1ZHmPNbTSR7YI72t7eOhLeSwd1HmGZROlNW1kVzBEb02JNvwg/s800/mvc-action-result-cd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDXxDHdlmstDJ2BpaAEVb721uhxTV5Ekx1jjRIr0MCdFd4rybxrGPn_Nk40I_FR0aSwbPb_lBBWBIVrDzg1gmhBTsvxz1ZHmPNbTSR7YI72t7eOhLeSwd1HmGZROlNW1kVzBEb02JNvwg/s0/mvc-action-result-cd.png)

## 範例：不同型別的回傳
```c#
//回傳一個 ViewResult
public ActionResult Index()
{
return View();  //若沒有指定參數，會回傳和 action 同名的 View ，所以這裡會回傳 Index.cshtml 這個檢視。
}
//回傳一個 ViewResult
public ActionResult Index2()
{
return View("List");  //明確指定回傳的 View 是 List 這個檢視。
}
//回傳一個 ViewResult
public ActionResult Index2()
{
return View("List", customer_model);  //同時傳進參數值
}

//回傳一個字串
public string Hello()
{
return "Hello";
}

//回傳一個 JsonResult
public JsonResult HelloJson()
{
List<string> tmp = new List<string>();
tmp.Add("Hello");
tmp.Add("World");
return this.Json(tmp, JsonRequestBehavior.AllowGet);

//["Hello","World"]
}

//回傳一個 RedirectToRouteResult
public ActionResult Edit(Customers customers)
{
if (ModelState.IsValid)
{
db.Customers.Attach(customers);
db.ObjectStateManager.ChangeObjectState(customers, EntityState.Modified);
db.SaveChanges();
return RedirectToAction("Index");  // return RedirectToRouteResult type
}
return View(customers);
}
```

## 使用 jQuery 呼叫 Action Method
```c#
public JsonResult HelloJson()
{
List<string> tmp = new List<string>();
tmp.Add("Hello");
tmp.Add("World");
return this.Json(tmp, JsonRequestBehavior.AllowGet);

//["Hello","World"]
}
```
```javascript
<script type="text/javascript">
$(function () {
var url = "Test1/HelloJson";

$("#Button1").click(function () {
$.ajax({
url: url,
type: "GET",
dataType: "json",
success: CompleteHandler
});
});

$("#Button2").click(function () {
$.getJSON(url, null, CompleteHandler);
});

function CompleteHandler(data) {
$.each(data, function (index, value) {
$("#div1").append(value + "</br>");
});
}
});
</script>
```

# Passing Data to Action Methods

當 client 傳送 request 到 server 端，通常會連同必要的資料一起傳送，這些資料可能來自於 form data, query string, cookie contents 等。  而 action method 是處理 request 的方法，那它要如何接收用戶端傳來的參數資料呢？  

## HTTP Get Parameters

如果用戶端使用 HttpGet 方法提出 request ，ASP.NET MVC 將會自動以 name 去對應這些 name-value 的資料與 action method 中的參數。  例如，若有個 request 的格式為 http://localhost:6666/GuestBook/Create/?title=abc&content=doreme ，  它就會自動對應到 GuestBook.Create(string title, string content) 這個 action method 。  

此外，也可以透過 routing 來指定對應的參數值。  例如，若 routing 設定成 {controller}/{action}/{id}，當收到 http://mySite/GuestBook/details/5 的需求，就會自動對應到 GuestBook.Details(id) 方法。  
```c#
// GET: /GuestBook/Create/5

public ActionResult Create(string title, string content)
{
if (ModelState.IsValid)
{
GuestBook guestbook = new GuestBook();
guestbook.Title = title;
guestbook.Content = content;
db.GuestBooks.AddObject(guestbook);
db.SaveChanges();
}
return RedirectToAction("Index");
}

// GET: /GuestBook/Delete/5

public ActionResult Delete(int id = 0)
{
GuestBook guestbook = db.GuestBooks.Single(g => g.Id == id);
if (guestbook == null)
{
return HttpNotFound();
}
return View(guestbook);
}
```

PS. id 是一個比較特殊的參數，如果 action 方法中的參數名稱剛好就叫 id ，那麼在 URL 中可以省略。  例如： http://localhost:6666/GuestBook/Create/?id=20&content=doreme 等於 http://localhost:6666/GuestBook/Create/20?content=doreme

## HTTP Post Parameters

當用戶端使用 Http Post 方式傳送資料進來，若要處理這類需求，必須在 action 方法上頭加上 [HttpPost](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.httppostattribute.aspx) 屬性。  另外，雖然在 MVC 中，仍然可以透過 Request 物件，直接取得 Client 端傳進來的資料，不過既然開始使用 MVC ，最好還是使用 FormCollection 或直接對物件來進行**模型繫結**（Model Binding）。  

**模型繫結**（Model Binding）是 MVC 中一種用來將表單值對應到 action 方法參數的方式。  你可以透過 FormCollection 物件或強型別的模型繫結物件，以取得用戶端傳遞進來的資料。  

### 使用 FormCollection

若你的 View 使用的是一般表單時，你可以使用 FormCollection 取得用戶端傳遞進來的資料。
```xml
@using (Html.BeginForm()) 
{
<div class="editor-label">
Title：@Html.TextBox("Title")
</div>
<div class="editor-field">
Content：@Html.TextBox("Content")
</div>
<p>
<input type="submit" value="Create" />
</p>
}
```
```c#
[HttpPost]
public ActionResult Create1(FormCollection collection)
{
if (ModelState.IsValid)
{
GuestBook guestbook = new GuestBook();
guestbook.Title = collection["Title"].ToString(); ;
guestbook.Content = collection["Content"].ToString(); ;
db.GuestBooks.AddObject(guestbook);
db.SaveChanges();
}
return RedirectToAction("Index");
}
```

### 使用模型繫結物件

若你的 View 使用的是強型別的 View ，你可以使用強型別的模型繫結物件，取得用戶端傳遞進來的資料。
```xml
@model MVC4_EX1.Models.GuestBook
@using (Html.BeginForm()) 
{
<div class="editor-label">
Title：@Html.EditorFor(model => model.Title)
</div>
<div class="editor-field">
Content：@Html.EditorFor(model => model.Content)
</div>
<p>
<input type="submit" value="Create" />
</p>
}
```
```c#
[HttpPost]
public ActionResult Create2(GuestBook guestbook)
{
if (ModelState.IsValid)
{
db.GuestBooks.AddObject(guestbook);
db.SaveChanges();
}
return RedirectToAction("Index");
}
```

## Request and Response Data

You can also get at additional data in the request (HTTP get) or response (HTTP post).   For instance, if your data is not passed or mapped to a parameter, you can use the Controller class's Request and Response properties.   These properties follow the HttpRequest and HttpResponse semantics you are familiar with in ASP.NET.  
```c#
//get a query string value
string name = Request.QueryString("name");

//get a form value
string name = Request.Form["name"];

//add to the response
Response.Write("Hello World")
```

# Passing Data to Views

有時候你想由 Controller 傳遞資料到 View 頁面之中，應該怎麼做呢？  

[ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 是一個 [ViewDataDictionary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.viewdatadictionary.aspx) 型別，  在 ControllerBase (for controller) 和 ViewPageBase (for view) 類別中都有這個屬性可以存取，  所以你可以透過這個屬性，由 controller 傳遞資料給 view 。  
```c#
public ViewResult Index()
{
ViewData["Msg"] = "Hello world";
return View();
}
```
```xml
<p> @ViewData["Msg"].ToString() </p>
```

# Using Action Filters

若你想在 action method 執行前或執行後加入一些邏輯判斷程式，例如，在執行前進行權限的審查，在執行後進行Log的記錄等等，  就可以使用[Action Filters](http://msdn.microsoft.com/zh-tw/library/dd410209.aspx)功能。  

[Action filters](http://msdn.microsoft.com/zh-tw/library/dd410209.aspx) 是一種屬性類別，都是由 [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.filterattribute.aspx) 繼承而來。  你也可以繼承這個類別或繼承 MVC 中已定義好的 [Action filters](http://msdn.microsoft.com/zh-tw/library/dd410209.aspx) 來自訂自已的 [Action filters](http://msdn.microsoft.com/zh-tw/library/dd410209.aspx) 。  

### Action Filters 的類型

MVC 提供了四種不同介面的 action filter

- [IActionFilter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.iactionfilter.aspx) ：Action filters 提供action額外的處理動作，例如：提供額外的資料，檢查回傳的值，或是取消action的動作。
- [IResultFilter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.iresultfilter.aspx) ：Result filters 重新包裝Action Result，可以增加結果的額外處理，像是修改Http的Response。例如：OutputCacheAttribute。
- [IAuthorizationFilter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.iauthorizationfilter.aspx) ：Authorization flters 用來驗證使用者是否有權限執行
- [IExceptionFilter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.iexceptionfilter.aspx) ：Exception filters 用來處理例外，指定例外發生時要執行的action，用來記錄log或是顯示錯誤訊息頁面。例如：HandleErrorAttribute。

上述提到的內建 action filter (如 Authorize 等) ，也是繼承 [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.filterattribute.aspx) 類別，並實作這些介面。  

若要自訂 action filter ，也是要繼承 [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.filterattribute.aspx) 類別，並實作這些介面。  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmRnZbLkhZbFgZf0j8_UHi_5anpe5-h173m5EQ9srfc3DUOJTsV_fOMB2TL9znSBhwljng3LC0WXJF3Ac7HLsBLfXrne-mg3iM1J0GPp9OtkEjvLzeBJ2UhtVHUy8f4JetkT9anPjqNc0/s800/mvc-action-filter.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmRnZbLkhZbFgZf0j8_UHi_5anpe5-h173m5EQ9srfc3DUOJTsV_fOMB2TL9znSBhwljng3LC0WXJF3Ac7HLsBLfXrne-mg3iM1J0GPp9OtkEjvLzeBJ2UhtVHUy8f4JetkT9anPjqNc0/s0/mvc-action-filter.png)

### 如何自訂 Action Filters

要自訂 [Action Filters](http://msdn.microsoft.com/zh-tw/library/dd410209.aspx) ，你只要新增一個類別，並繼承自 [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.filterattribute.aspx) 基底類別或者其衍生類別即可。參考以下範例：  
```c#
public class MyActionFilter : FilterAttribute, IActionFilter, IResultFilter
{
public void OnActionExecuted(ActionExecutedContext filterContext)
{
throw new NotImplementedException();
}

public void OnActionExecuting(ActionExecutingContext filterContext)
{
throw new NotImplementedException();
}

public void  OnResultExecuted(ResultExecutedContext filterContext)
{
throw new NotImplementedException();
}

public void  OnResultExecuting(ResultExecutingContext filterContext)
{
throw new NotImplementedException();
}
}
```

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyyLkRvv2jgfxtEVlPnsc-WGXcLMN09i0p3vqk-yDmMuq4wA9WjfrNnMOs0KSmUeB92hyphenhyphensLv2HbyOVonosf5mGnV33lrVgZi3ggi_k935qbKEDxUGTPIcbKp7h9Ve0v_SSRq94bQH8Lgk/s835/mvc-custom-action-filter.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyyLkRvv2jgfxtEVlPnsc-WGXcLMN09i0p3vqk-yDmMuq4wA9WjfrNnMOs0KSmUeB92hyphenhyphensLv2HbyOVonosf5mGnV33lrVgZi3ggi_k935qbKEDxUGTPIcbKp7h9Ve0v_SSRq94bQH8Lgk/s835/mvc-custom-action-filter.png)

### 如何使用 Action Filters

使用時，只要在 action method 上頭加上 action filter 屬性即可
```c#
[MyActionFilter]
public ViewResult Index()
{
...
}
```

若放在 controller 上頭，表示整個控制器中的 action method 都要套用
```c#
[MyActionFilter]
public class HomeController : Controller
{
public ViewResult Index()
{
...
}
public ViewResult Index2()
{
...
}
｝
```

### Http 動作

在上面的例子中，我們曾經在 action 方法上方套用 [HttpPost] 屬性，這個東西也是一種 Action Filter ，它對應到 http 傳輸協定中所定義的八種動作  （[指令動詞](http://technet.microsoft.com/zh-tw/library/dd569062.aspx)）。  所以，在 MVC 中，我們也可以利用 Action Filter (HttpPost、HttpGet、HttpPut、HttpDelete等）屬性，指定 action 方法可以回應的 http 動作，以增加安全性。