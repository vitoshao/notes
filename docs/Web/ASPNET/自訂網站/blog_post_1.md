---
title: 自訂網站錯誤處理
layout: default
parent: 自訂網站
nav_order: 1
description: "自訂網站錯誤處理"
date: 2012-10-02
tags: [Web,ASPNET,自訂網站]
postid: "3058207436844831681"
---
網站的工作(tasks)，大多在處理 request 與 response 之間的請求，這機制系統會自動幫我們處理。  但是，還有一些比較不一樣的工作，就必須自行撰寫程式碼，以便處理 ASP.NET 與 IIS 的溝通，例如：  

- 網頁的例外處理
- 網頁的組態設定
- 網頁的非同步作業
- 自訂 HTTP Handler

# Page 和 Application 的錯誤處理

通常我們會在 Try/Catch 補捉並且記錄例外狀況，  但是如果例外狀況不是發生在這個區塊中，那麼就可以使用自訂的處理統一補捉這些錯誤，避免用戶端發生網頁錯誤。  錯誤處理可分成**網頁層級**或**應用程式層級**的錯誤  

## 網頁層級的錯誤

若要預防網頁層級的錯誤，可以在網頁中建立 **Page\_Error** 事件處理常式。

- [Server.GetLastError](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.getlasterror.aspx) ：傳回先前的例外狀況。
- [Server.ClearError](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.clearerror.aspx) ：清除先前的例外狀況。
```c#
private void Page_Error(object sender, EventArgs e)
{
// 擷取錯誤
Exception ex = Server.GetLastError();
string ErrorMessage = ex.Message;

// 處理錯誤
Trace.Write("ERROR: " + ErrorMessage);
myDebug.WriteLine("ERROR: " + ErrorMessage);
myDebug.ResponseBR("ERROR: " + ErrorMessage);
Literal1.Text = ErrorMessage;

// 清除錯誤
Server.ClearError();
}
```

處理完錯誤之後，可叫用 [ClearError](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.clearerror.aspx) 清除 Error 或導向錯誤頁面，要不然錯誤還是會反應到網頁上。

## 應用程式層級的錯誤

若發生 Exception 的網頁沒有補捉該例外的話，則這個 Exception 則會觸發 global.asax 的 **Application\_Error** 事件。

通常可以在這個事件中記錄 ErrorLog ，再導到共同的錯誤頁面。  或者直接在此使用 [Server.Transfer](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.transfer.aspx) ，將整個 Page 資訊都丟給共同的錯誤頁面去處理。  
```c#
protected void Application_Error(object sender, EventArgs e)
{
Server.Transfer("~/ErrPage.aspx");
}
```
```c#
public partial class ErrPage : System.Web.UI.Page
{
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
// 擷取錯誤
Exception ex = Server.GetLastError().GetBaseException();

// 處理錯誤
ErrorPage ErrPage = new ErrorPage();
ErrPage.ErrUrl = Request.ServerVariables["HTTP_REFERER"];

if (ex != null)
{
ErrPage.ErrMessage = ex.Message;
Server.ClearError();
}

// 輸出錯誤
litErrUrl.Text = ErrPage.ErrUrl;
litErrMsg.Text = ErrPage.ErrMessage;
}
}
}

public class ErrorPage
{
public string ErrUrl { get; set; }
public string ErrMessage { get; set; }
}
```

# 自訂錯誤頁面

當系統發生錯誤發生時，若不希望讓使用者看到錯誤訊息，就可以透過自訂錯誤頁面提供訊息來指引使用者進行下一個步驟。  要處理這樣子的設定，也可以分成針對單一網頁，或整個網站來設定。  

## 1.網頁層級的設定

透過 Page 的 ErrorPage 屬性，可以指定錯誤發生時的導向頁面。
```xml
<%@ Page Language="C#" ErrorPage="~/ErrPage.aspx"%>
```

## 2.應用程式層級的設定(套用至整個網站)
```xml
<customErrors mode="On" defaultRedirect="~/ErrPage.aspx" />
```

- [defaultRedirect](http://msdn.microsoft.com/zh-tw/library/h0hfz6fc.aspx) ：指定發生錯誤時，要將瀏覽器導向至的預設 URL。
- [mode](http://msdn.microsoft.com/zh-tw/library/h0hfz6fc.aspx) ：顯示錯誤訊息模式  

- On ：啟用自訂錯誤。可在用戶端和本機端顯示自訂的錯誤說明。
- Off：停用自訂錯誤。可在用戶端和本機端顯示 ASP.NET 的錯誤說明。
- RemoteOnly：在用戶端顯示自訂的錯誤，在本機端顯示 ASP.NET 的錯誤。(預設值)

#### 使用 [RedirectMode](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.customerrorssection.redirectmode.aspx) 屬性

CustomErrors 的 [RedirectMode](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.customerrorssection.redirectmode.aspx) 屬性是 ASP.NET 3.5 SP1 之後才加入的屬性，它可以用來設定當使用者被重新導向至自訂的錯誤頁面時，是否要變更其原始的 URL 。  其設定格式如下：   

- ResponseRedirect ：導向後的 URL 為錯誤頁面的網址，不同於原先的網址一樣。
- ResponseRewrite ：導向後的 URL 會被重寫和原先的網址一樣。
```xml
<customErrors mode="On" redirectMode="ResponseRewrite" defaultRedirect="~/ErrPage.aspx" />
```

#### 依錯誤狀態碼，自訂錯誤頁面

每一個錯誤都有一個錯誤狀態碼(HTTP status codes)，當自訂錯誤頁面時，也可以針對不同的錯誤狀態碼，指定特定的錯誤頁面。  
```xml
<customErrors defaultRedirect="~/ErrPage.aspx" mode="On">
<error statusCode="403" redirect="NoAccess.htm" />  
<error statusCode="404" redirect="FileNotFound.htm" />
</customErrors>
```