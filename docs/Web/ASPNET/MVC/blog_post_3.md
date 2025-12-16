---
title: MVC Routing
layout: default
parent: MVC
nav_order: 3
description: "MVC Routing"
date: 2013-03-19
tags: [Web,ASPNET,MVC]
postid: "318406375046161478"
---
Routing 是用來定義網址的格式，它是在 ASP.NET 3.5 被加入的新功能，在過去，要實現這個功能，可能要自已撰寫程式，或者使用第三方元件。  有了這個內建功能，要使用 Routing 的好處，就變的方便許多。  

本篇內容僅針對 MVC 中所使用的 Routing 規則。  

# 認識 MVC Routing

MVC 將所有路由規則儲存在靜態物件 RouteTable.Routes ，當 IIS 收到一個 MVC 要求，會先交由 [UrlRoutingModule](http://msdn.microsoft.com/zh-tw/library/system.web.routing.urlroutingmodule.aspx) (一個 HttpModule) 處理。  然後在交給 [MvcRouteHandler](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.mvcroutehandler.aspx) 依據 URL 的內容以決定對應的 controller 和 action method。  

## Routing 的組成

底下是一組預設的Routing 規則：  
```c#
routes.MapRoute(
"Default",                      // 路由名稱
"{controller}/{action}/{id}"    // URL 的路由樣式
);
```

路由樣式說明：

- 每一組 {xxx} 稱為「**路由空間**」(**PlaceHolder**)，裡頭的名稱稱為「**路由參數**」(**route parameters**)。
- 路由樣式用斜線「 / 」來分開每一個「**段落**」(**Segment**)，所以路由樣式不能以斜線 ( / ) 開頭。。
- 一個 Segment 允許放多個 **PlaceHolder**。

### 範例一
```c#
//1.1
routes.IgnoreRoute("{resource}.axd/{\*pathInfo}");

//1.2
routes.MapRoute(
"RouteRule1", // 路由名稱
"{catelog}-{type}", // URL 及參數
new { controller = "Product", action = "Detail" } // 參數預設值
);

//1.3
routes.MapRoute(
"RouteRule2", // 路由名稱
"{controller}/{action}/{id}", // URL 及參數
new { controller = "Product", action = "Detail" } // 參數預設值
);
```

範例說明：

1. IgnoreRoute ：忽略比對
- 第1行程式碼 [IgnoreRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.ignoreroute.aspx) 方法中的{resource}，代表一個路由變數，要取別的名稱也可以，它就只代表一個**路由空間**(PlaceHolder)，表示一個「位置」。
- 第1行程式碼中的 pathInfo，也是代表一個路由變數，要取別的名稱也可以，只是代表一個變數的位置。  但是 pathInfo 名稱前的星號 ( **\*** ) 代表 **CatchAll** 的意思，也就是取得網址後頭全部的內容。  例如：若網址是 /TEST.axd/a/b/c/d 則 \*pathInfo 所得到的值為 a/b/c/d，如果沒加上星號，該 pathInfo 就會等於 a 而已。
2. 例中的 RouteRule2 規則，表示路由規則由3個路由參數組成，而 controller,action,id 等名稱都表示一個路由參數名稱。  其中，{controller} 和 {action} 是保留給 MVC 用的，這二個名稱不能亂用。  {id} 則會對應到 [Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) Method 的參數名稱。
3. 由於 Routing 在做比對的時候，**第一個被比對成功的規則就會直接被採用**。  所以 RouteRule1 必須寫在 RouteRule2 之前，因為 RouteRule1 的規則比較嚴僅。  如果寫反了，像 http://localhost/book-it 這樣的網址，就會對應到 RouteRule2 規則而發生錯誤。

## 使用 MapRoute 方法設定 Routing 

ASP.NET 的執行會由 Global 中的 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 的 Application\_Start() 事件開始，所以可以在此定義所有的 Routing 規則。  所有的 Routing 規則會存在 [RouteTable.Routes](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routetable.routes.aspx) 這個**公開的靜態屬性**中，它是一個 [RouteCollection](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routecollection.aspx) 型別  

- [MapRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.maproute.aspx) ： 用來定義必須遵守的 Routing 規則。
- [IgnoreRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.ignoreroute.aspx) ： 用來定義忽略的 Routing 規則。
```c#
public static Route MapRoute(this RouteCollection routes, string name, string url);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults, object constraints);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults, string[] namespaces);
public static Route MapRoute(this RouteCollection routes, string name, string url, object defaults, object constraints, string[] namespaces);

public static void IgnoreRoute(this RouteCollection routes, string url);
public static void IgnoreRoute(this RouteCollection routes, string url, object constraints);
```

## 如何路由參數的預設值

路由參數的預設值是由 [RouteValueDictionary](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routevaluedictionary.aspx) 類別所組成。

在 MVC 中的 Routing 設定，每一組設定都必須要能對應 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 與 [Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) ，才不會發生 Exception 。  若 [MapRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.maproute.aspx) 方法中的第二個參數，在 Url Pattern 中，沒有定義 {controller} 和 {action} 路由參數，則必須使用第三個參數，以定義預設值。  

### 例
```c#
routes.MapRoute(
"Default",                      // 路由名稱
"{controller}/{action}/{id}"    // URL 的路由樣式
new { controller = "Home", action = "Index", id = UrlParameter.Optional } 　// 路由參數的預設值
);
```

上面範例，分別指定了 controller, action, id 三個路由參數的預設值分別為 Home, Index, Optional。  其中第三個路由參數，其預設值設定為 Optional，表示這個 id 參數是可以忽略的。

### 例
```c#
routes.MapRoute(
"Default5", // 路由名稱
"Archive/{year}/{month}/{day}", // URL 及參數
new { controller = "Bolg", action = "Archive", day=1 } // 參數預設值
);
```

上面範例，分別指定了 year, month, day 三個路由參數。  這個路由規則會執行 BolgControll 中的 Archive 方法，並傳入 {year},{month},{day} 三個路由參數的值。  若 Url 中沒有 {day} ，則預設值為 1 .

## 設定 Routing 的約束條件

要使用約束條件必須定義 [MapRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.maproute.aspx) 方法中的第四個參數，它是用來更嚴僅的定義路由規則。  例如定義 Segment 的長度，或者路由參數值的大小範圍等。  
```c#
routes.MapRoute("MonthlyReport",
"{year}/{month}",
new {controller="Reports", action="MonthReport", month = DateTime.Now.Month, year = DateTime.Now.Year},
new { month = @"\d{2}", year = @"\d{4}" }
);
```

上面的 routing 設定，會達到以下幾點需求：  

當使用者輸入 http://localhost/2010/07 這樣子的網址時，就可以對應到 ReportsController 的 MonthReport 方法，並帶入參數 year=2010, month=07  
當使用者輸入 http://localhost/ 時，預設會執行相同的方法，且傳入當時的年月資料。  
當使用者輸入 http://localhost/100/01 時，則不使用這一組 routing 比對。

## Routing 的例外設定

### 將實體檔案加入 Routing

在 MVC 系統中，當需求網址可以對應到一個實體檔案時，例如： aspx 網頁檔，或 txt 文字檔，這時候就會略過 ASP.NET Routing 的轉換機制，所以這也是為什麼 WebForm 可以和 MVC 和平共處的原因之一。  

但是，如果你希望所有檔案都必須經過 ASP.NET Routing 機制的比對才行，這時只要在 Global.asax.cs 檔案裡的 RegisterRoutes 方法中，將 [RouteCollection](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routecollection.aspx) 加上 [RouteExistingFiles](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routecollection.routeexistingfiles.aspx) 屬性即可，如下程式碼：  
```c#
routes.RouteExistingFiles = true;
```

### 設定特定檔案不使用 Routing

若你希望有些程式不要使用 ASP.NET MVC 的執行管線，你也可以使用 [IgnoreRoute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.routecollectionextensions.ignoreroute.aspx) 方法來定義跳過路由的規則，它就會交由 IIS 判斷要由何種 Handler 處理。  
```c#
//忽略所有 \*.aspx 的網址路徑 
routes.IgnoreRoute("{resource}.aspx/{\*pathInfo}");

忽略所有在 Page 目錄下的所有程式與檔案
routes.IgnoreRoute("Page/{\*pathInfo}");
```
## 參考資料  

- [\[Will\]ASP.NET MVC 開發心得分享 (21)：Routing 觀念與技巧](http://blog.miniasp.com/post/2011/08/01/ASPNET-MVC-Developer-Note-Part-21-Routing-Concepts-and-Skills.aspx)
- 
-