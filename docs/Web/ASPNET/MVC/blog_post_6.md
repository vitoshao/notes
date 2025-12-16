---
title: Caching in MVC
layout: default
parent: MVC
nav_order: 6
description: "Caching in MVC"
date: 2013-03-27
tags: [Web,ASPNET,MVC]
postid: "7358056024830168217"
---
在 MVC 中同樣可以使用 ASP.NET 的網頁輸出快取(Output Caching)來提升網頁的效能。Output Caching 可以控制 controller 中的 action 不必每次被 invoke 時都得執行一次。例如，我們常在controller 的 index 方法中列出資料庫中資料的清單，使用 Output caching 就可以避免重複讀取資料庫中相同的資料。

# Output Caching

## Enabling Output Caching

[OutputCacheAttribute](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.outputcacheattribute.aspx) 屬性可以用來快取 controller 中的 method ，也可以快取整個 controller 。

### Caching an Action Method
```c#
[OutputCache(Duration = 10, VaryByParam = "\*")]
public ActionResult CacheTest3()
{
ViewBag.Now = System.DateTime.Now;
ViewBag.Message = "Wellcom to MVC";
return View();
}
```

底下這二張圖分別是使用與不使用 cache 的回應標頭內容。

#### 這是沒有使用 cache 的回應標頭，Cache-Control=private ，表示不用被快取。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjj_ln2YbUqLUm0YAKUuWzCGYZiX5cxk3Sjw2SZh4P0mAEKj5cKL_wZcKvLXt-3MJ3N7V4Ca82ehol29au0cy_haOzzYuTa1N7MuOM4rJSWD1oXrlDYTuIUbWJaCM7MvqtGKQSm-4hHHG0/s362/mvc-no-using-cache.png)

#### 這是有使用 cache 的回應標頭

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxCUeD9GbPKTvGh3sthFFj-8C_sMe-pBgaWzfAbkAy0H_NDIDt8OLELcjY01Bxoz8WhFIk6G0ptMplmkRu1aO57jSYdEg7xNG0ZdyvLNBMO9bxJlwG0Fp7JDXPtb90l8A86R4I-r01TR4/s395/mvc-using-cache.png)

### Caching a Controller

你也可以針對整個 Controller 設定快取，它和 action method 是分開的，像下面這個例子，CacheTest1 的快取是10秒，而整個控制器的快取是20秒。
```c#
[OutputCache(Duration = 20, VaryByParam = "\*")]
public class CacheController : Controller
{
[OutputCache(Duration = 10, VaryByParam = "\*")]
public ActionResult CacheTest1()
{
ViewBag.Now = System.DateTime.Now;
ViewBag.Message = "Wellcom to MVC";
return View();
}
```

#### Note

Don't use the page &lt;%@ OutputCache %&gt; directive in an MVC view. This directive is bleeding over from the Web Forms world and should not be used in an ASP.NET MVC application.

## Disabling Output Caching

You can disable the caching machanism in web.config
```xml
```
&lt;system.web&gt;&lt;caching&gt;&lt;outputCache enableOutputCache="false" /&gt;&lt;/caching&gt;&lt;/system.web&gt;

# Partial Output Cache 
response message is intended for a single user and MUST NOT be cached## 參考資料

- [Output Caching in ASP.net MVC](http://juristr.com/blog/2012/10/output-caching-in-aspnet-mvc/)
- [Improving Performance with Output Caching](http://www.asp.net/mvc/tutorials/older-versions/controllers-and-routing/improving-performance-with-output-caching-cs)
- http://horacerobot.blogspot.tw/2011/01/mvc3-partial-output-cache.htmlMVC 3 Partial Output Cache - Simple Demo