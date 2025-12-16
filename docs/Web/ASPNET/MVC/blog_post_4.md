---
title: MVC Authentication
layout: default
parent: MVC
nav_order: 4
description: "MVC Authentication"
date: 2013-03-19
tags: [Web,ASPNET,MVC]
postid: "3966562042991040513"
---
在 MVC 中要使用認證，它使用的機制完全和 ASP.NET Web Form 的做法相同。  

只不過，在 WebForm 網站中，我們在 web.config 中設定了哪些檔案須要認證，那些檔案不須要認證。  而 MVC 則是針對 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 的 action 。你只要在執行方法上頭加上 [Authorize](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute.aspx) 屬性即可。  

例如，若要限定 ChangePassword 這個方法，只能由登入的使用者才能執行，可以如以下設定。  
```c#
[Authorize]
public ActionResult ChangePassword(ChangePasswordModel model)
{
}
```

底下二種用法，也都表示必須是登入的使用者才能執行
```c#
[Authorize (User="")]
[Authorize (Roles ="")]
```

若要限定特定的使用者或角色才能執行，可以如以下設定。  
```c#
[Authorize (User="vito")]
[Authorize (Roles ="Admin")]
```