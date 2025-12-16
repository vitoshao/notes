---
title: 自訂 HTTP Modules and HTTP Handlers
layout: default
parent: 自訂網站
nav_order: 3
description: "自訂 HTTP Modules and HTTP Handlers"
date: 2013-01-23
tags: [Web,ASPNET,自訂網站]
postid: "8325551739088184122"
---
一般狀況下，當 IIS 收到Http Request，它會視這個Request的類型，交由不同的 handler 去處理。  例如，當 IIS 收到使用者提出一個網頁 aspx 需求，就會執行 asp.net page handler 以處理請求。  當 IIS 收到的是一個 asmx 需求， IIS 就會執行 asp.net sevice handler 以處理請求。  若你須要自行處理特定的需求，就必須針對這個需求設計特定的 HTTP Handler 或者 HTTP Module。  

- **HTTP Module** 類似 IIS 早期版本中的 ISAPI Filter ，是用來將任何需求加上某些處理，例如驗證需求或壓縮回應等功能。
- **HTTP Handler** 類似 IIS 早期版本中的 ISAPI Extension ，用來定義 Http 的處理程序，以處理特定型態的 Http Web Request。

這二者有個主要的不同點就是，一般 Handler 所處理的需求，都會針對特定的檔案路徑，或者特定的副檔名。

所以，若要建立一個 IIS 的擴充功能，你可以先判斷這個功能是否是針對特定的 url/extension ，來決定是要建立一個 Module 或者 Handler 。

下列項目，是 ASP.NET Web Application 專案下，用來建立 Module 或 Handler 的項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjHRG_d_JWbRQzouJeMIBYaH-QOOhxfTyySTYrPrYRygXX8QKCZ1BzFfqFYs8xjBnXSrbkIQl-UuTsNhVQR8HqGbtHW9uVwPp04jSc_XkuV0xWWRy4tByXpGuomZLfSeHBn7WQ4KRWLfM/s528/HttpHandle-HttpModule.png)

#### 什麼是 ISAPI ？

ISAPI（Internet Server Application Prigramming Interface），  它主要是由 Process Software 和 Microsoft Corporation 共同訂定的一組 **Internet Server API**。  其主要目的就是要改進 HTML 的特性，讓你可以延伸伺服器的特性，以發展各式各樣的服務。  

ISAPI 的程式分兩種： Filter 與 Extension 。  IIS 6.0 的 ISAPI Filters 與 Extensions 都是附掛在 **w3wp.exe** 中的一種延伸功能，它們並沒有管線化的設定，而是分別做不同的用途。   

#### 什麼是 ISAPI 伺服器擴充程式？

ISAPI 擴充程式是一種特別的 DLL，其內部必須依照 ISAPI 的規範，並提供某些特定函式。  它主要是用來開發能掛載在 IIS 上的 Web 應用程式的模組，例如 ASP 的核心檔案 asp.dll 即是一種 ISAPI Extension；  ASP.NET 的核心檔案 **aspnet\_isapi.dll** 也是一個 ISAPI Extension。  當 ISAPI 擴充程式被安裝完成之後，若 IIS 收到該服務的請求，就會載入該擴充程式，並把請求交給該擴充程式來處理。  

#### 什麼是 ISAPI 篩選器？

ISAPI 篩選常式 (Filter) 也是在啟用 ISAPI 的 HTTP 伺服器上執行的 DLL，用來篩選出入伺服器的資料。  它主要是用來開發加強 IIS 現有功能的模組，像是處理驗證、壓縮以及資料加密等，都可以利用 ISAPI Filter 來實作。  

到了 IIS7 開始，提供二種方便的方法： module 和 handler ，讓我們可以輕鬆撰寫伺服器的擴充功能。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhr4Q8qyMzP6xrQ5gGwSPrPPOKxKU-GiSqVUWgFrCrOEIgTHQnApB940ob0KsahveyN15gwvE0T2S6wvR_IT_jzIY816f7H8Wm180wF9_WwaXQGPDEakbCrURLvB69MKkzN4j6wxvIl7ss/s844/IHttpModule-ClassDiagram.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbJat3ebpc78qA_zBQfsoX8UrJGLv4bhSFg3_DL0NLBTQf0AjyTVtMalvVU-1WXYCEHOKhyaqrDi1xwftAvclwGFyMYyz0AzpEwjhPh8Z_-M2vQDlAJZ2NScIgGGE0YkE0DOfJ_AOByEs/s826/IHttpHandler-ClassDiagram.png)

# HttpModule

When you write your own HTTP module, the module's code is executed every time a page is requested from the server or a response is sent back to a requestor.   This might be to implement a specific security model, to do extra logging, to rewrite a request, or to perform some similar function.   In this way, HTTP modules are like the old **Internet Server API (ISAPI)** filters; however, all ASP.NET modules are managed (or, in other words, executed) within the CLR.  ASP.NET itself uses HTTP modules for things such as session state and caching.  

通常我們設計一個「ASP.NET 模組」，大都是要用來撰寫 web application 共用的功能，例如：權限判斷、錯誤處理等。  在 ASP.NET 中已有一些預設的模組，例如： [FormsAuthenticationModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationmodule.aspx) 、 [RoleManagerModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.rolemanagermodule.aspx) 、 [UrlRoutingModule](http://msdn.microsoft.com/zh-tw/library/system.web.routing.urlroutingmodule.aspx) 。  

在 Global.asax 裡頭，我們通常也會用來做一些初始化的事情，例如： UrlRewrite 或者 ApplicationErrorHandle 。  你也可以將這些程式碼獨立出來，由自訂模組中來啟動執行。  

使用模組的優點在於不用和整個網站挷在一起，也且也可以在不更動任何現有的程式碼原則下，將特定的需求以另外新增的模組來執行運作。  而且可以藉由 web.config 的設定，隨時啟用或停用該功能。  

底下為定義在 machine.config 中的預設模組 (.Net 4.0)
```xml
<httpModules>
<add name="OutputCache" type="System.Web.Caching.OutputCacheModule" />
<add name="Session" type="System.Web.SessionState.SessionStateModule" />
<add name="WindowsAuthentication" type="System.Web.Security.WindowsAuthenticationModule" />
<add name="FormsAuthentication" type="System.Web.Security.FormsAuthenticationModule" />
<add name="PassportAuthentication" type="System.Web.Security.PassportAuthenticationModule" />
<add name="RoleManager" type="System.Web.Security.RoleManagerModule" />
<add name="UrlAuthorization" type="System.Web.Security.UrlAuthorizationModule" />
<add name="FileAuthorization" type="System.Web.Security.FileAuthorizationModule" />
<add name="AnonymousIdentification" type="System.Web.Security.AnonymousIdentificationModule" />
<add name="Profile" type="System.Web.Profile.ProfileModule" />
<add name="ErrorHandlerModule" type="System.Web.Mobile.ErrorHandlerModule, System.Web.Mobile, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
<add name="ServiceModel" type="System.ServiceModel.Activation.HttpModule, System.ServiceModel.Activation, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
<add name="UrlRoutingModule-4.0" type="System.Web.Routing.UrlRoutingModule" />
<add name="ScriptModule-4.0" type="System.Web.Handlers.ScriptModule, System.Web.Extensions, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
</httpModules>
```

## 建立 HttpModule

要建立自訂的 HttpModule 模組，你可以自訂一個實作 [IHttpModule](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpmodule.aspx) 介面的類別，或者直接新增一個「ASP.NET 模組」項目。  你必須覆寫 [Init](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpmodule.init.aspx) 方法，並在這個方法中訂閱適當的 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 事件。  

以下範例示範自訂一個 ErrorModule 模組，用來處理 application.Error 事件。
```c#
public class MyErrorModule : IHttpModule
{
public void Init(HttpApplication application)
{
application.Error += new EventHandler(ApplicationOnError);
}

//錯誤處理程式
public void ApplicationOnError(Object source, EventArgs e)
{
//取得目前 HTTP 要求的 System.Web.HttpContext 物件
HttpContext ctx = HttpContext.Current;

//由 HttpContext 物件取得例外狀況
Exception exception = ctx.Server.GetLastError();
string errInfo = exception.InnerException.StackTrace.ToString();

//取得目前 HTTP 的回應和要求物件
HttpResponse response = ctx.Response;
HttpRequest request = ctx.Request;

//取出要求物件中的 QueryString 資訊
NameValueCollection QuertStrings = request.QueryString;

//回應要求
response.Write(errInfo.Replace("\r\n", "<br>"));

ctx.Server.ClearError();
}

public void Dispose()
{
//清除程式碼置於此。
}
}
```

## 註冊 HttpModule

要使用自訂模組，必須先在網站的 web.config 檔案中註冊這個模組。不同版本的 IIS ，使用上有些許不同：

### 使用 IIS6 或 IIS7 傳統模式
```xml
<system.web>
<httpModules>
<add name="CustomErrorModule" type = "MyErrorModule" />
</httpModules>
</system.web>
```

若這個檔案是使用 Web Application 網站建立，或是使用類別庫開發的，這時候的型別設定，必須加上命名空間，如下範例：

type = "命名空間.類別名稱, 組件名稱"

例如：
```xml
<system.web>
<httpModules>
<add name="CustomErrorModule" type = "MyPractice.Common.MyErrorModule, MyPractice" />
</httpModules>
</system.web>
```

### 使用 IIS7 整合管線模式

若是使用 IIS7 整合管線模式，必須將以上設定，由 &lt;system.web&gt; 區段改成 &lt;system.webServer&gt; 區段，如下範例。
```xml
<system.webServer>
<modules>
<add name="CustomErrorModule" type = "MyPractice.Common.MyErrorModule, MyPractice" />
</modules>
</system.webServer>
```

# HttpHandler

HTTP Handler 簡單講就是要處理用戶端的 HTTP request，ASP.NET 本身就是一個 HTTP Handler，專門用來處理 .aspx .ascx .asmx 等檔案。  因此若我們想處理特殊的類型請求，我們也可以建立自已的處理常式。  

通常如果要使用自訂 HTTP Handler，大部份的目的就是要處理特別的檔案類型。  幾個常見的應用，如：URL rewrite，防止圖檔的盜連，產生驗證圖檔等等。  

handler 就類似過去 IIS 中的 ISAPI extension ，是真正處理特定請求的主體程式。  它會處理用戶端提出的請求，並將處理過後的訊息回應給用戶端。  ASP.NET 中的 Page 類別本身就是實作 [IHttpHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.aspx) 。  
```c#
public class Handler1 : IHttpHandler
{
public bool IsReusable｛get;}

//處理 request 的函式
public void ProcessRequest(HttpContext context)
{}   
}
```

## 建立 HttpHandler

要建立 HttpHandler 處理常式，你可以自訂一個實作 [IHttpHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.aspx) 介面的類別，或者直接新增一個「ASP.NET 處理常式」項目。  並且完成 [IsReusable](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.isreusable.aspx) 屬性和 [ProcessRequest](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.processrequest.aspx) 方法。  

- [IsReusable](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.isreusable.aspx) ：表示 [IHttpHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.aspx) 執行個體是否可重複使用，可重複使用則為 true，否則為 false。  
If [IsReusable](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.isreusable.aspx) is set to true, your handler will be put into a pool to help increase performance across requests.
- [ProcessRequest](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.processrequest.aspx) ： 這個方法用來處理 HTTP request 提出的要求，當自訂的處理常式被執行時，就會執行這個方法。  你必須在這個方法中撰寫 response 的程式碼。

以下範例示範自訂一個 EmpPhotoHandler 處理常式，用來取得儲存在資料庫中的員工照片資料。
```c#
public class EmpPhotoHandler : IHttpHandler
{
public void ProcessRequest(HttpContext context)
{
string EmpID = context.Request.QueryString["ID"];
byte[] photo = GetEmpPhoto(EmpID);

if (photo != null)
{
context.Response.ContentType = "image/jpeg";
context.Response.BinaryWrite(photo);
}
else
{
context.Response.Write("./images/noimage.gif");
}
}

public bool IsReusable
{
get
{
return false;
}
}
}
```

## 註冊 HttpHandler

註冊 HttpHandler 和註冊 HttpModule 一樣，須依 IIS 版本選擇使用設定區段。以下示範使用 IIS6 的版本。
```xml
<system.system.web>
<httpHandlers>
<add path="\*.Photo" verb="\*" type="Practice70515.Common.EmpPhoto" />
</httpHandlers>
</system.system.web>
```

上面這個設計，使用 path 屬性指定要處理的檔案類型，當 IIS 收到任何使用 .Photo 結尾的需求，就會轉交給 EmpPhoto 這個 handler 來處理。

以上設定，你也可以執行 IIS 中的「處理常式對應」功能來設定：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjONwReUTv_e42n-GDJNqrjEQiM_Tmxe3BXfQ4oo4kcHrbtrmPkxnf5a5ofcQ7Dq8RJ4hc5WMqdPMbHBbDFZYknUxrkmD0dS-bJt60nu60t2xZrmqXTLxFXIQ4gPHzWeyl37GvfD65can0/s626/iis-httphandler-map.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNT36YxmkDKx4SfN3mudGbt2rMAXKydT1nANiixNggATV6MN_GRKqT0kZ6rBYliIqrJDPkh3CTKdDnzy5ovSRGSXS3wpILhdWZSiGM90d_BxLEmpqb62SjOrsnIO5hAMwO0ldhHYEEecs/s850/setting-handler.png)

#### 執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgESq62l_FmG72Nu6RZIKMjIZx6TD3i-BsjrOQGle2fjjN_LCaAO8RnlftAibTgnDl2qHip4LEWZ29Fm_mJKOKBCc1dC9dyvY-OOPJmzoTpX3ukYNjbTbYvwaL5y5AnTdJEqcWTyYI8wVg/s456/CustomHttpHandler_EmpPhoto.png)

#### 設定 IIS6 與 IIS7 注意事項

若要兼顧 IIS6 及 IIS7，可在 web.config 中同時保留 httpHandlers(for IIS6) 及 handlers(for IIS7) 裡的相同定義，  但記得要加上&lt;validation validateIntegratedModeConfiguration="false" /&gt;，不然IIS7會因為定義重複出現而發生錯誤  
```xml
<system.webServer>
<validation validateIntegratedModeConfiguration="false" />
</system.webServer>
```

## 泛型處理常式

ASP.NET 另外提供一個稱為泛型處理常式的樣板網頁，它的副檔名為 .ashx ，是一種特定型式的 HTTP handler 。  你一樣可以用它來撰寫特定的處理常式，但不需要額外註冊就能使用。  當需求轉交到 ASP.NET 時，ASP.NET 會再將這個需求轉交給自訂的 HTTP handler 處理。

# 範例

底下範例，使用類別庫專案來建立 module 和 handler ，最後再將 build 好的組件 deploy 到 IIS 主機上。

## 一、建立 module

1. 新增一個類別庫專案(MyIISExtension)。
2. 加入 System.Web.dll 參考。
3. 新增一個自訂類別(MyModule.cs)。
4. 繼承 [IHttpModule](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpmodule.aspx) 介面，並實作該介面。  
實作 [IHttpModule](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpmodule.aspx) 必須完成 **Init** 方法，這個方法的主要角色就是要初始化這個模組。  這個方法會傳進來一個 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 參數，它代表整個應用程式，你可以在此設定要訂閱該類別的哪些事件。
5. 訂閱一個或多個 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 事件。
6. 撰寫事件的處理方法。
```c#
namespace MyIISExtension
{
public class MyModule : IHttpModule
{
public void Init(HttpApplication context)
{
// 訂閱 EndRequest 事件
context.EndRequest += new EventHandler(OnEndRequest);
}

// 這個方法會在每一個 request 處理完成後，再其結尾再加上一個特定的logo字串
public void OnEndRequest(object sender, EventArgs e)
{
string logo = "<div>Vito's Web Site</div>";
HttpApplication app = sender as HttpApplication;
app.Response.Write(logo);
}

public void Dispose()
{
//throw new NotImplementedException();
}
}
}
```

## 二、建立 handler

- 新增一個自訂類別(MyHandler.cs)。
- 繼承 [IHttpHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.aspx) 介面，並實作該介面。  
實作 [IHttpHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.aspx) 必須完成 **ProcessRequest** 方法，這個方法是這個 handler 的核心，它主要責任就是處理用戶端傳來的需求。  這個方法會傳進來一個 [HttpContext](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.aspx) 實體，你可以由這個變數取得 [HttpRequest](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.aspx) 和 [HttpResponse](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.aspx) 實體。
- 將處理結果，透過 HttpResponse.Write() 方法回應。

底下這個處理常式會回應一個日期時間的字串內容
```c#
namespace MyIISExtension
{
public class MyHandler : IHttpHandler
{
public bool IsReusable
{
get { return true; }
}

public void ProcessRequest(HttpContext context)
{
HttpRequest request = context.Request;
HttpResponse response = context.Response;

DateTime dt;
String useUtc = context.Request.QueryString["utc"];

if (!String.IsNullOrEmpty(useUtc) && useUtc.Equals("true"))
{
dt = DateTime.UtcNow;
}
else
{
dt = DateTime.Now;
}
context.Response.Write(String.Format("<h1>{0}</h1>", dt.ToLongTimeString()));
}
}
}
```

## 三、部署與設定

### 部署

部署的方式有很多種，最簡單的方法就是將 build 好的 dll 檔複製到網站的 \bin 子目錄中即可。

### 設定 module

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitPKJRkmkdwuCX9BT7-2MIyh2dFRLRdyz1EYesvbSi6DybHn7rK8fG93KjxgVxHxIKf6N1cJ8lWzH_yky8DY6IxM7S7ahi-HlOu8T0BpjBXLOjWWvVxGfrlzJQnS-5AfQ0njU52_O2XfM/s392/setting-module-handler.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjusbcgMfO8tx-StswoEdS2tI2AD_1YTC9iaRdYAxaPzbLlKWjttTUmLKxDjGZjWKvJdVdXOVCObuX1r7Jt1QrDeOl7pcuSupRig8w92zebenGyNJsSa8TEosN2yLppOypSW6XpQOTUHx4/s774/setting-module.png)
```xml
<system.webServer>
<modules>
<add name="MyModule" type="MyIISExtension.MyModule" />
</modules>
</system.webServer>
```

### 設定 handler

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNT36YxmkDKx4SfN3mudGbt2rMAXKydT1nANiixNggATV6MN_GRKqT0kZ6rBYliIqrJDPkh3CTKdDnzy5ovSRGSXS3wpILhdWZSiGM90d_BxLEmpqb62SjOrsnIO5hAMwO0ldhHYEEecs/s850/setting-handler.png)
```xml
<system.webServer>
<handlers>
<add name="MyHandler" path="\*.tm" verb="\*" type="MyIISExtension.MyHandler" resourceType="Unspecified" preCondition="integratedMode" />
</handlers>
</system.webServer>
```

## 四、測試

上面範例中，我們自訂一個 module ，這個 module 不管什麼類型的 request ，都會在結尾加上一個logo字串。  

另外，我們也自訂了一個 handler ，這個 handler 只要遇到副檔名為 .tm 的 request ，就會負責處理，並且回應一個日期時間內容。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRyWqoea8fdyD6uobJ1AVYiHYQgBDPPM67pBHL_eHslE6L4WOpqSWUtEny1N5MXrimCVYaSlFdOJ2mS0dBTTOuwCfgJHNsnjrz-So3hZN6hm4jAv-YEnmQg2O59dU2fhM9JycRhM07Z8U/s340/sample-module-handler.png)

# IIS 7.0 的 ASP.NET 應用程式生命週期

## IIS 架構

IIS 7.0 和 IIS 6.0 的處理階段之間的主要差異，在於 ASP.NET 如何與 IIS 伺服器整合。    在 IIS 6.0 或 IIS 7.0 的傳統模式(classic mode)中，有兩個要求處理管線。  一條管線供機器碼 ISAPI 篩選常式 (Filter) 以及擴充元件，另一條管線供 Managed 程式碼應用程式元件 (例如 ASP.NET) 使用。    在 IIS 7.0 整合管線模式(integrated pipeline)中，ASP.NET 執行階段會與 Web 伺服器整合，因此會產生統一的要求處理管線以供所有要求使用（同時支援 Native-code 以及 Managed-code 模組）。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhP2fz6N_aQjBGEoWZ7JFtdt-BZs4feK0ID1vN5ZQYOhjmU37SJNoJGE0ZssPo87piLEdKAVYv86PKjent_CV77N0KPcxie_0_HPnoTieEWQPMJBVnjXpAv_f6gexE_tzQfbV_0ntpPFRk/s288/IIS-LifeCycle-C.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiHSE_9cVjSrrgvLd5xDPzzyEmiXk8k3cMN5FfdxU980k5pTS15rKab3MFksam2z3vRGRanyAdKDCk5WXdcjopDVLlb7U9XFxfn8rumghV00ExTpbPjWRJlDfuADlCrg8stXBXF1c5emm4/s288/IIS-LifeCycle-E.png)
## 參考資料  

- [使用 Visual C# .NET 建立 ASP.NET HTTP 模組](http://support.microsoft.com/kb/307996)
- [An HTTP Module for ASP.NET Error Handling](http://www.codeproject.com/Articles/16171/An-HTTP-Module-for-ASP-NET-Error-Handling)
- [Developing IIS7 modules and handlers with the .NET framework](http://mvolo.com/developing-iis7-modules-and-handlers-with-the-net-framework)
- [IIS Module Starter Kit](http://www.microsoft.com/taiwan/technet/iis/learn/IIS_Module_Starter_Kit.aspx)
- [IIS5 IIS6 IIS7的ASP.net 請求處理過程比較](http://m.wangchao.net.cn/bbs/tcdetail_1804869.html)
- [IIS 7.0 的 ASP.NET 應用程式生命週期概觀](http://msdn.microsoft.com/zh-tw/library/bb470252.aspx)