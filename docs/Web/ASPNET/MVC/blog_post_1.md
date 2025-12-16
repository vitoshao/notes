---
title: ASP.NET MVC
layout: default
parent: MVC
nav_order: 1
description: "ASP.NET MVC"
date: 2013-01-03
tags: [Web,ASPNET,MVC]
postid: "5450724984655442771"
---
# The ASP.NET MVC Architecture

## The ASP.NET MVC Architecture

An **ASP.NET MVC** application has a different architecture, page processing model, conventions, and project structure than an ASP.NET web form site.  Requests to an ASP.NET MVC application are handled by the [UrlRoutingModule](http://msdn.microsoft.com/zh-tw/library/system.web.routing.urlroutingmodule.aspx) [HttpModule](http://msdn.microsoft.com/en-us/library/ms178468.aspx) .  This module parses the request and selects a **route** for that request based on a configuration that you define.    

Ultimately, the request is routed to one of the many **controller** classes that you write to manage request processing.   It is the selected controller's job to access your data and business logic (the **model**), connect it for display (the **view**), and send the response back to the user.   The controller handles all requests-view and updates (posts).  

The architecture of an ASP.NET MVC application.  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihtl4eu8B8ZfKrmpJFtyx2PzQz2UIFjCIgNx4rZUpoaLvDv8mbvWS705kQF1sWqbptMWPOzp0XZ4CxplucewDQaJ6rzTpXfOS-b80PACe5oriYuHe79qCAKmL963Y2ztmeJ3NSdJV1AVg/s327/mvc-architecture.png)

MVC stands for *model-view-controller*. MVC is a pattern for developing applications that are well architected, testable and easy to maintain.   MVC-based applications contain:  

- **M**odels：Classes that represent the **data** of the application and that use validation logic to enforce       **business rules** for that data.
- **V**iews：Template files that your application uses to dynamically generate       **HTML** responses.
- **C**ontrollers：Classes that handle incoming browser requests, retrieve       **model** data, and then selecting a **view** for displaying results.

## The ASP.NET MVC Request Life Cycle

在常見的網頁架構中，通常一個網址就會對應到一個實體的檔案，稱之為網頁，裡面存放著 HTML 標記或程式碼。  在 MVC 架構中，就不再是這樣子的概念，一個網址必須先經由 routing 處理，判斷正確的控制器(controller)，然後再將須求交由控制器處理。  在 [System.Web.Routing](http://msdn.microsoft.com/zh-tw/library/system.web.routing.aspx) 命名空間裡的 [UrlRoutingModule](http://msdn.microsoft.com/zh-tw/library/system.web.routing.urlroutingmodule.aspx) 主要就是負責這項工作。  

所有的路由定義都必須在 Global.asax 檔案裡的 Application\_Start 事件中註冊到 [RouteTable](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routetable.aspx) 。  透過 [RouteTable.Routes](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routetable.routes.aspx) 屬性可以取得所有的路由設定，若要加入新的路由設定，只要叫用 [MapRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.maproute.aspx) 這個擴充方法即可。  
```c#
public static Route MapRoute(this RouteCollection routes, string name, string url);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults, object constraints);
...
```
```c#
RouteCollection routes = RouteTable.Routes;
routes.MapRoute(
"Default", // 路由名稱
"{controller}/{action}/{id}", // URL 及參數
new { controller = "Home", action = "Index", id = UrlParameter.Optional } // 參數預設值
);
```

經由路由處理後，需求會轉成 [RequestContext](http://msdn.microsoft.com/zh-tw/library/system.web.routing.requestcontext.aspx) 物件再交由 [System.Web.Mvc](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.aspx) 命名空間裡的 [MvcRouteHandler](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.mvcroutehandler.aspx) 物件處理。  [MvcRouteHandler](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.mvcroutehandler.aspx) 會依據  [RequestContext](http://msdn.microsoft.com/zh-tw/library/system.web.routing.requestcontext.aspx) 物件建立 [MvcHandler](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.mvchandler.aspx) 執行個體，然後藉由委派選取處理 HTTP 要求的控制器。  

控制器再透過 [ControllerActionInvoker](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controlleractioninvoker.aspx) 類別，依需求決定執行的方法 (action) 。  

這個 action 就會接受使用者傳進來的資料，經由 model 中的邏輯運算後，將相對應的 view 回應給使用。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgs_ocDku6MMFGiuwjvZ1b7ORa9AxHfJa1CuAQOci3T-qPXFA69nuMg65Ghf2FlLKbd0BRpgKCUSHWhliP627YpWBf6WzCCXLe_94dYesYdp4zmMFu4h-vBHErZjlLj2aYEjrxts42HQqY/s727/mvc-lifecycle.png)

## 簡單的 MVC 範例

MVC 專案在設計的過程中，一般會依 Model→Controller→View 這三個順序建立。

### Models

- Model 主要負責應用程式的商業邏輯部份。
- 例如資料庫的存取、資料格式的驗證、資料結構的定義。
- Model 並不依賴 View 或 Controller ，只要專注做好資料存取、驗證、定義的責任即可。
```c#
using System;
using System.ComponentModel.DataAnnotations;

namespace MvcApplication2.Models
{
public class CutsomModel
{
[Required]
[Display(Name = "賓客名稱")]
public string CutsomName { get; set; }
}
}
```

### Controllers

- Controller 屬於協調者角色，負責協調 View 與 Model 二個部份。
- Controller 藉由 URL 判別應該執行的 Action 方法。
- 如果有必要，就透過 Model 類別，進行邏輯運算。
- 最後叫用 View 取得要顯示的資料內容，並回傳到用戶端。
```c#
public class CustomController : Controller
{
//預設的 action
public ActionResult Index()
{
return View();   //return Index View
}

//這個 action 回應的內容是一個要求輸入的畫面
public ActionResult Custom()
{
return View();  //return Custom View
}

//這個 action 接收 post 資料，並將流程導至 Welcome Action
[HttpPost]
public ActionResult Custom(CutsomModel custom)
{
return RedirectToAction("Welcome", custom);  
}

//顯示輸入資料的 action
public ActionResult Welcome(CutsomModel custom)
{
ViewBag.Message = "Hello " + custom.CutsomName + ",</br></br>Welcome for your visit.";
return View();  //return Welcome View
}
}
```

### Views

- View 的責任是接收頁面傳來的資料，以及回應資料給顯示頁面。
- View 依據 Controller 傳遞進來的資料，執行邏輯判斷以決定輸出內容。但是 View 並沒有綁死特定的Controller，任何Controller只要能提供 View 所需要的資料，View 就可以依據顯示邏輯回應。
```xml
@{ViewBag.Title = "Hello";}
<h2>Thanks for your visit</h2>
```
```xml
@model MvcApplication2.Models.CutsomModel
@{
ViewBag.Title = "Custom";
Layout = "~/Views/Shared/_Layout.cshtml";
}

<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>

<h2>Hello</h2>

@using (Html.BeginForm())
{
<div>
Please  input your name : 
@Html.EditorFor(model => model.CutsomName)
@Html.ValidationMessageFor(model => model.CutsomName)
</div>
<p>
<input type="submit" value="OK" />
</p>
}
```
```xml
@model MvcApplication2.Models.CutsomModel
@{
ViewBag.Title = "Welcome";
Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>Welcome</h2>

@Html.Raw(ViewBag.Message)
```

# The Structure of an ASP.NET MVC Application

## ASP.NET MVC Application Structure

Many of the files and folders are the same as those you would find in a standard ASP.NET website (such as App\_Data, Web.config, and Scripts).   However, some of the other folders are specific to ASP.NET MVC. The following describes these folders inside this project template:  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1fbLMWL6CiCUWcsESYZsqzVk7d4YFPNnWYqDTr9kqAM3wZ4RuvLD8r2CcKNQ1Y4YpwbujBa2wrAQ5wzzsry3fj1XhvL9TOrXFX9oBxyS0dyAIr31CCtmtAaSF8pM_1d2Ai7lCKC5T9i8/s234/mvc-file-structure.png)  

- **Content** ：  
Use this folder for static files in your site, such as images. You can also place style sheets and standard HTML files here.
- **Controllers** ：  
This is where you put your controller classes. In general, each entity  in your model has a single controller. You might also define additional controllers  for other types of processing, such as LoginController, HomeController, and  NavigationController. [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) classes are named with the EntityController  convention.
- **Models** ：  
This is where you put code that represents your business model.   In most sites, this code interacts with the database and processes business rules.   If your model is an Entity Framework or LINQ to SQL model, you can put the DBML or EDMX file and its related code here.   Your model can also be a separate DLL file or class library that is referenced by the project (in which case this folder would be empty).
- **Views** ：  
This folder is where you put the views for your application (ASPX pages, ASCX user controls, and master pages).   The convention is to create a folder for each controller in your application.   Therefore, if you have a controller called EmployeeController, you would have a folder called Employee in the Views folder.   The Employee folder would contain pages used for rendering different user activity related to an Employee in your model.   The routing engine uses this convention to search for views.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgT8ds4hyuz4BG608yyEpmdLVWsLZFzKugRpQEIFLzqnFFGndnZWxlZkFHTnpUPRrbdKOls-Va00t_QTQ7ggLsNGQ6Ls5lhorB7XekwMWNuZLqDe8srSnkqiYIxJwz1CnJowffv1GmCCw/s220/mvc-file-structure-controllers.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSWe8xz3_ejSX2BddJJhpS52obPBm8vkKIitDHzTB1VSBg-dhGQjTA8vYntt7Gi-5FMB_k_awLjYqnKrr0wGo-7z_WfFVMO5lXsbDfL_DclDNvgQb8hZU1Dl6MtGpLddFX2sVSQi445mE/s226/mvc-file-structure-views.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4UnQJW0QzTPqq18HurygIvCuO7MDXvd60NxndFwWqvMIQNzc-XRpEfJY7CZJizUtYPIt8IS0jBWGBWSjsT48aOzSJYdkkiqN8LJcY8lN6c_mc4Gfxh50nvkF_G_Vyx78jdBWmP6SdqBc/s265/mvc-file-structure-moduls.png)

## Defining Application Areas

It's no problems using the default project structure for most websites.  However, there are some sites that are very large; keeping all the models, views, and controllers in this single folder set can be difficult to manage.   For such cases, you can define different **MVC project areas** in your application.  

An **area** in ASP.NET MVC is a subset of the project structure based on a logical grouping.  Each area contains area-specific models, views, and controllers.   The root of the site contains the sitewide models, views, and controllers.   This type of partitioning can make it easier to manage your application files.  

#### create an area

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg55jIiofylmbS71Y6SWL29HBu-pwBXjFB_JSipYZNaQ2eRKbQjgb8S0ZJl83naUUHyXiyOV00uptEoCCihXUB20fTqH8b4J-JS8c10iIGT8XwjrhhUq16lPjSy1a3SAArNg7Vr8TrhD9w/s709/mvc-add-areas.png)

#### areas structure

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkimjvctC3VimvA346xtMhYjzbeh8lzWNOpVA8rwbGPbHEFHDrcmVNiqezZR93RX6Y04FQnrfhRwp82cEew0n3YM1oAVdYV_ACDeddde9AiP2a4JtuJjnOEsq-pt28XlM_lA7J6OiLCic/s271/mvc-file-structure-areas.png)

在各個獨立的 areas 中，加入 models, views, and controllers 的方法都和根目錄的做法一樣，只是它們的名命空間會有所不同。  每一個 area 也都包含一個 [AreaRegistration](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.arearegistration.aspx) 類別檔，這些檔案會使用 AreaName/{controller}/{action}/{id} 網址樣式讓 area 對應到 routing engine 。  
```c#
namespace MvcApplication2.Areas.BackSite
{
public class BackSiteAreaRegistration : AreaRegistration
{
public override string AreaName
{
get{return "BackSite";}
}

public override void RegisterArea(AreaRegistrationContext context)
{
context.MapRoute(
"BackSite_default",
"BackSite/{controller}/{action}/{id}",
new { action = "Index", id = UrlParameter.Optional }
);
}
}
}
```

最後，在 Global.asax 中，叫用 [RegisterAllAreas](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.arearegistration.registerallareas.aspx) 方法，以註冊所有 areas 。   
```c#
protected void Application_Start()
{
AreaRegistration.RegisterAllAreas();
}
```

# Running Your First ASP.NET MVC Webpage

Press F5 ( haha.. )

# MVC 4

MVC 4 內建在 VS2012 ，如果使用 VS2010 ，必須額外安裝。  

- [ASP.NET MVC 4 for Visual Studio 2010 SP1 與 Visual Web Developer 2010 SP1](http://www.microsoft.com/zh-tw/download/details.aspx?id=30683)

##### MVC4 新增功能

- New Template Layout
- Mobile Template
- Web API Template
- Web API
- Display Modes
- OAuth
- Bundling and Minification
- Add Controller to any project folder
- Task support
- AllowAnonymous Attributes

##### 
## 參考資料  

- [\[Will\]ASP.NET MVC 開發心得分享 (21)：Routing 觀念與技巧](http://blog.miniasp.com/post/2011/08/01/ASPNET-MVC-Developer-Note-Part-21-Routing-Concepts-and-Skills.aspx)
- [Getting Started with ASP.NET MVC3](http://www.asp.net/mvc/tutorials)
- [ASP.NET MVC 概觀](http://msdn.microsoft.com/zh-tw/library/dd381412.aspx)
- [ASP.NET MVC 4](http://msdn.microsoft.com/zh-tw/library/gg416514%28v=vs.108%29.aspx)
- [Authenticating Users with Forms Authentication](http://www.asp.net/mvc/tutorials/older-versions/security/authenticating-users-with-forms-authentication-cs)