---
title: Asynchronous Controller
layout: default
parent: MVC
nav_order: 2
description: "Asynchronous Controller"
date: 2013-03-19
tags: [Web,MVC]
postid: "5552426335691038862"
---
IIS 有一個自已的 TreadPool 用來處理用戶端提交的需求，TreadPool 中的 Thread 是有數量限制的，如果遇到以下狀況，就比較容易導至執行緒耗盡（thread starvation）的情況：  

- 大量的用戶端存取
- 頻繁的 I/O bound 工作
- 頻繁的外部網路存取工作

適時的採用非同步處理的作業模式可以減少系統被卡住的情形。  因為當非同步作業執行時，原先的執行緒會被釋放交還給 IIS ，讓它可以繼續處理其他用戶端的請求。  等到非同步作業執行結束，再將工作交還給 IIS 處理。  

在 DotNet 4.5 中，只要使用[非同步控制器動作](https://msdn.microsoft.com/zh-tw/library/ee728598%28v=vs.100%29.aspx)，就可以輕鬆將原本同步作業調整成非同步作業模式。  

### 建立非同步控制器

當建立控制器時，只要勾選「使用非同步控制器動作」，該控制器中自動建立的 action method 就會自動加入 async 和 await 等關鍵字以達到非同步作業的目的。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8j2uJaulK9h17hdAvgWWqBmiEMUZYja1zK15EkpcaXBeW8vP-DUIBijJBAgRa0Hz3EIHgk9ixMc7DXib82tzM9N6NWrC6CJdYHwhYpMLRLrbx1q1GEE_n4CxOiDF0OoYY4zLLOWcSaG8/s0/mvc-async-01.png)

下列圖示中使用紅色記號，指出非同步作業的程式碼：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFYxfwyvKdK7KJG7K2VcK7f-MMgyGXM-UdmaqyjT3wFA6c6lxK24irBbCvT-zr1j5K8WWy9v3PRV-8y9EPJLf8jjcPb-4R4OR0hTgv9fRDdiBQg0RrQvncjLFl0nDD5Eukocpp7QIFJXs/s0/mvc-async-02.png)

- async 關鍵字是用來讓編譯器知道，這個 Index 方法執行結束後必須產生回呼，而且回傳 Task&lt;ActionResult&gt; 物件。
- 當編譯器遇到 await 關鍵字時，它會暫停原先 Index 方法的運作，並建立新的 Task 去執行非同步作業的 method ，直到非同步作業方法執行完畢。
- 叫用 ToList 的非同步方法。

上面範例有二個叫用非同步方法的地方： ToListAsnyc 和 FindAsync 。  要注意的是 linq 本身只會設定 sql 內容，真正要採用非同步的地方是將指令送到資料庫執行時，而在叫用 ToList 前，sql 指令是不會被執的，所以非同步方法會在 ToList 上，而不是在 departments = db.Departments 的描述上。  

同樣的，若是處理 Add、Update、Delete 等狀況時， SaveChange 才是真正將指令送到資料庫執行的時刻，這時必須改叫用它的非同步方法。  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfkFmfWeuynQ83CbGDgc2Xv4r9PXiKdCysneOuFk0nSDBumGUHnVoyGYdwdpWTw8kdPleAYjDmQnK6IErFrHdS56s-7zPW17P95YqARWtbqtzGVz0n_7M9rfw_9Pl4mHsSMzGcNkVtnlY/s800/mvc-async-03.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfkFmfWeuynQ83CbGDgc2Xv4r9PXiKdCysneOuFk0nSDBumGUHnVoyGYdwdpWTw8kdPleAYjDmQnK6IErFrHdS56s-7zPW17P95YqARWtbqtzGVz0n_7M9rfw_9Pl4mHsSMzGcNkVtnlY/s0/mvc-async-03.png)
## 參考資料  

- [ASP.NET MVC 開發心得分享 (18)：非同步控制器開發](http://blog.miniasp.com/post/2010/06/06/ASPNET-MVC-Developer-Note-Part-18-AsyncController.aspx)
- [ASP.NET 應用程式與非同步處理](http://huan-lin.blogspot.com/2013/01/aspnet-application-and-asynchronous.html)
- [Async and Stored Procedures with the Entity Framework in an ASP.NET MVC Application](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application)
- [在 ASP.NET MVC 中使用非同步控制器](https://msdn.microsoft.com/zh-tw/library/ee728598%28v=vs.100%29.aspx)
- [Async Programming : Introduction to Async/Await on ASP.NET](https://msdn.microsoft.com/en-us/magazine/dn802603.aspx)