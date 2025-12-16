---
title: ResolveUrl & ResolveClientUrl
layout: default
parent: ASP.NET WebSite
nav_order: 5
description: "ResolveUrl & ResolveClientUrl"
date: 2012-06-15
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "467095223833807762"
---
若網站系統中存在下列三個檔案  
/Test/test.aspx  
/Test/Blogger.css  
/Styles/treeview.css  

當遇到解析路徑問題時，尤其是在MaterPage或UserControl之中，無法在Design Time取得絕對路徑時，有二個好用的方法：  

- [ResolveClientUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.resolveclienturl.aspx)
- [ResolveUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.resolveurl.aspx)
```c#
Response.Write(ResolveUrl("Blogger.css") );
Response.Write(ResolveClientUrl("Blogger.css") );

//==> /Test/Blogger.css
//==> Blogger.css

Response.Write(ResolveUrl("~/Styles/treeview.css") );
Response.Write(ResolveClientUrl("~/Styles/treeview.css") );

//==> /Styles/treeview.css
//==> ../_Styles/treeview.css
```

結論就是：  
ResolveUrl 回傳的是以根目錄為開始的絕對路徑  
ResolveClientUrl 回傳的是以目前執行程式所在的目錄為基礎的相對路徑