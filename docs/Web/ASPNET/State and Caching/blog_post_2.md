---
title: 伺服器端狀態管理
layout: default
parent: State and Caching
nav_order: 2
description: "伺服器端狀態管理"
date: 2012-10-02
tags: [Web,ASPNET,State and Caching]
postid: "256944745234673627"
---
ASP.NET provides two ways to share information between Web pages without sending the data to the client.  

- **Application state** : information is available to all pages, regardless of which user requests a page
- **Session state** : information is available to all pages opened by a user during a single visit.

Both application state and session state information are lost when the application restarts.  

# Application State

[Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 的資料是儲存在 [HttpApplicationState](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.aspx) 類別的實體中，而且放在 [Page.Application](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.application.aspx) 的屬性裡(使用 key/value 的字典型態)。  

Application 是 ASP.NET 中全域型(Global)的儲存機制，存在 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 中的資料可以被應用程式中的所有 Page 所存取。  

#### Application Events

ASP.NET 提供三種可以初始化 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 變數的事件，而且這些事件都必須在 Global.asax 檔案中執行：

- Application\_Start：用來初始化 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 變數。
- Application\_End：用來釋放資源。
- Application\_Error：用來記錄與處理錯誤。
```c#
protected void Application_Start(object sender, EventArgs e)
{
Application["Visitors"] = 0;
}

protected void Session_Start(object sender, EventArgs e)
{
Application.Lock();
Application["Visitors"] = (int)Application["Visitors"] + 1; 
Application.UnLock();
}

protected void Session_End(object sender, EventArgs e)
{
Application.Lock();
Application["Visitors"] = (int)Application["Visitors"] - 1;
Application.UnLock();
}
```

# Session State

- Session State 儲存在 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 集合物件( [HttpSessionState](http://msdn.microsoft.com/zh-tw/library/system.web.sessionstate.httpsessionstate.aspx) 類別)，也是儲存在伺服器端。  與 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 狀態儲存上的最大差異點就是 SessionState 是區別瀏覽器(或為一個User)的存取範圍。
- Session State 有保留期限，若使用者離開網頁超過設定的 timeout 時，原有的 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 資訊將會遺失，並建立新的 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 。
- 要儲存在 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 裡的資料，必須是可序列化的物件。
```c#
protected void btnSetSession_Click(object sender, EventArgs e)
{
Session["myName"] = "vxxxxxxo";
}

protected void btnGetSession_Click(object sender, EventArgs e)
{
myDebug.ResponseBR("id : " + Session["myName"].ToString());
}
```

#### Session Events

ASP.NET 提供二個事件，可協助管理 User [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) ：

- Session\_Start：此事件發生在 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 開始時，可用來初始化 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 變數。
- Session\_End：此事件發生在 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 結束時，用來釋放 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 資源。

#### 設定 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 模式

[SessionStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.sessionstate.sessionstatemode.aspx) 是用來設定 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 的儲存位置。

- InProc ：預設，存放 WebServer 記憶體。重新啟動伺服器，所有的 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 資料將會遺失。
- StateServer ：存放在 ASP.NET State Service 這個服務之中。
- SQLServer ：存放在 SQL Server 資料庫中，效能同 StateServer ，透過中央儲存狀態的方式，適用於多伺服器的架構。
- Custom ：自訂儲存提供者。
- Off ：停用 Session。

#### 設定無 cookie 的執行階段狀態 ( Cookieless Session State ) 

ASP.NET 預設使用一個 24 位元的 SessionID ，用來辯識每一個連線的使用者，它通常會儲存在 cookie 中 。  若遇到用戶端使用 Cookieless 模式，則 ASP.NET 會將這個 SessionID 內嵌於 URL 之中，如下：  
```c#
http://localhost:60755/(S(stlhk1pxp5hec4bjl0gnaklv))/WebForm1.aspx
```

以下則為使用 Cookieless 模式的設定：
```xml
<sessionState cookieless="true" regenerateExpiredSessionId="true"></sessionState>
```

#### 設定 StateServer 模式，讓 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 資料不再自動消失

預設 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 會使用 InPro 模式，它會將 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) State 儲存在 WebServer 的記憶體中，並保留至 timeout 為止。  如果希望 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) 在應用程式重啟之後仍能夠繼續保存，那麼就必須使用 StateServer 或 SQLServer 模式。  

使用 StateServer 這個模式，必須啟動 ASP.NET State Service，所以必須先確認該服務的啟動狀態！ 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLKtZSb1PHnupedDQ3ovoV9JSJ7vl9MsnSdIgzJd9ILIP8lLcZWo9yZJjDxJ_krpuznMUP_HJxqN5JNGetnwybo6OjWM-fhcB0M-Fc5NaXEvSxHfhR601yyos2LwRlsoM58ZdxtNjYD58/s431/aspnet_state_service.png)

web.config 組態檔，設定如下： 
```xml
<sessionState 
mode="StateServer"
stateConnectionString="tcpip=localhost:42424"
cookieless="false"
timeout="20"/>
```

#### 停用 Session

整個網站停用
```xml
<system.web>
<sessionState mode="off"/>
</system.web>
```

單一網頁停用
```xml
<%@ Page Language="C#" CodeFile="Default.aspx.cs" EnableSessionState = "False" %>
```

# Profile Properties

#### profile properties : 

- allows you to store user-specific data similar to session state not lost when a user's session expires
- The concept of profiles is built into ASP.NET. These profiles can be configured, set, and automatically stored and maintained in a database by ASP.NET.
- stored in a persistent format and associated with an individual user.
- allows you to easily manage user information using strongly typed classes

#### configure : 

need a profile provider , eg. SqlProfileProvider
```xml
<profile defaultProvider="SqlProfileProvider" enabled="true"> 
<providers> 
<add name="SqlProfileProvider" 
type="System.Web.Profile.SqlProfileProvider" 
connectionStringName="LocalSqlServer" 
applicationName="/" /> 
</providers> 
<properties> 
<add name="address" type="string" serializeAs="String"/> 
</properties> 
</profile>
```