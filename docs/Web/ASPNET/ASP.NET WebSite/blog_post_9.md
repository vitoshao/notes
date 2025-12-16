---
title: 網頁生命週期
layout: default
parent: ASP.NET WebSite
nav_order: 9
description: "網頁生命週期"
date: 2013-01-31
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "3481719989310184314"
---
# Application

## The ASP.NET Application Life Cycle

#### 1. 送出請求

應用程式生命週期啟始於使用者提出網頁需求(page request)。  

#### 2. 將請求交由處理管道（Process Pipeline）

將請求導進入Process Pipeline(一種程序管道)，再交由 runtime 處理。  

- 在IIS 5.0以及6.0以及7.0的傳統模式  
這個模式下的 **ASP.NET 要求管線**會與 **Web 伺服器管線**分開。      當 Web 伺服器收到要求時，會檢查要求檔案的副檔名、判斷應該處理要求的 ISAPI 擴充程式，然後將要求傳遞至適當的 ISAPI 擴充程式。      ASP.NET 只會處理已對應的副檔名，例如 .aspx、.ascx、.ashx 和 .asmx。      若是其它資料檔 .jpg 或 .htm 檔，通常不會對應至 ASP.NET，所以 ASP.NET 不會對 .htm 檔執行驗證或授權檢查。
- IIS 7.0的整合模式  
這個模式中只有一個通用的管道來處理所有應用程式內的資源請求，這使得資料檔或aspx網頁都可以用相同的管道來處理。

#### 3. 建立 [**ApplicationManager**](http://msdn.microsoft.com/zh-tw/library/system.web.hosting.applicationmanager.aspx) 實體

runtime 會為每個使用者建立一個 [ApplicationManager](http://msdn.microsoft.com/zh-tw/library/system.web.hosting.applicationmanager.aspx) 實體，用來執行應用程式的 AppDomain 環境，每個 AppDomain 都是獨立的，彼此隔離，以免全域變數互相存取。  

#### 4. 建立 [**HostingEnvironment**](http://msdn.microsoft.com/zh-tw/library/system.web.hosting.hostingenvironment.aspx) 實體

在 AppDomain 被建立之後，會再建立一個 [HostingEnvironment](http://msdn.microsoft.com/zh-tw/library/system.web.hosting.hostingenvironment.aspx) 實體，用來存取主機內的資源，如檔案目錄。  

#### 5. 建立核心物件

這個階段，ASP.NET 會產生處理請求的核心物件(core object)，如： [HttpContext](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.aspx) 、 [HttpRequest](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.aspx) 、 [HttpResponse](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.aspx) 。  

#### 6. 建立 [**HttpApplication**](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 實體

ASP.NET 建立一個 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 實體(或是重複使用已建立實體)，用來追縱 appliction 的事件。  在建立 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 同時，也會建立適當的 modules ，對需求進行額外的事件處理，如 [FormsAuthenticationModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationmodule.aspx) 、 [UrlRoutingModule](http://msdn.microsoft.com/zh-tw/library/system.web.routing.urlroutingmodule.aspx) 。  
（這個步驟在網站啟動後只會在首次遇到需求時被執行一次，它是 Global.asax 的基底類別）  

#### 7. 交由 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 負責處理

最後，所有請求都會被送進 [HttpApplication](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplication.aspx) 管道處理。  這個管道包含各種處理模組，如 validating requests, mapping URLs, accessing cache 等。

## The Application Events

#### 1. Application\_Start

這個事件通常是因為初次請求應用程式頁面時，被IIS所觸發。  
在這個事件中可以初始化Application層級的變數  

#### 2. Application\_End

這個事件通常都是在應用程式被關閉(Shutdown)，通常可以釋放資源，或是記錄特定事件(Log)  

#### 3. Application\_Error

這個事件是在非預期的例外事件產生，並被拋到應用程式層級，我們可以在這裡進行應用程式最壞狀況的處理，例如進行Log。  

#### 4. Application\_LogRequest

這個機制是當應用程式接到請求的時候，都會被觸發。  

#### 5. Application\_PostLogRequest

當完成請求後會被觸發。  

其他的 Application\_ 事件，請參考MSDN [http://support.microsoft.com/kb/312607](http://support.microsoft.com/kb/312607)  

- Application\_Init：在應用程式被實例化或第一次被調用時，該事件被觸發。對於所有的HttpApplication物件實例，它都會被調用。
- Application\_Disposed：在應用程式被銷毀之前觸發。這是清除以前所用資源的理想位置。
- Application\_Error：當應用程式中遇到一個未處理的異常時，該事件被觸發。
- Application\_Start：在HttpApplication 類的第一個實例被創建時，該事件被觸發。它允許你創建可以由所有HttpApplication 實例訪問的物件。
- Application\_End：在HttpApplication 類的最後一個實例被銷毀時，該事件被觸發。在一個應用程式的生命週期內它只被觸發一次。
- Application\_BeginRequest：在接收到一個應用程式請求時觸發。對於一個請求來說，它是第一個被觸發的事件，請求一般是用戶輸入的一個頁面請求（URL）。
- Application\_EndRequest：針對應用程式請求的最後一個事件。
- Application\_PreRequestHandlerExecute：在 ASP.NET 頁面框架開始執行諸如頁面或 Web 服務之類的事件處理程式之前，該事件被觸發。
- Application\_PostRequestHandlerExecute：在 ASP.NET 頁面框架結束執行一個事件處理程式時，該事件被觸發。
- Applcation\_PreSendRequestHeaders：在 ASP.NET 頁面框架發送 HTTP 頭給請求客戶（流覽器）時，該事件被觸發。
- Application\_PreSendContent：在 ASP.NET 頁面框架發送內容給請求客戶（流覽器）時，該事件被觸發。
- Application\_AcquireRequestState：在 ASP.NET 頁面框架得到與當前請求相關的當前狀態（Session 狀態）時，該事件被觸發。
- Application\_ReleaseRequestState：在 ASP.NET 頁面框架執行完所有的事件處理程式時，該事件被觸發。這將導致所有的狀態模組保存它們當前的狀態資料。
- Application\_ResolveRequestCache：在 ASP.NET 頁面框架完成一個授權請求時，該事件被觸發。它允許緩存模組從緩存中為請求提供服務，從而繞過事件處理程式的執行。
- Application\_UpdateRequestCache：在 ASP.NET 頁面框架完成事件處理程式的執行時，該事件被觸發，從而使緩存模組存儲回應資料，以供回應後續的請求時使用。
- Application\_AuthenticateRequest：在安全模組建立起當前用戶的有效的身份時，該事件被觸發。在這個時候，用戶的憑據將會被驗證。
- Application\_AuthorizeRequest：當安全模組確認一個用戶可以訪問資源之後，該事件被觸發。
- 
- Session\_Start：在一個新用戶訪問應用程式 Web 站點時，該事件被觸發。
- Session\_End：在一個用戶的會話超時、結束或他們離開應用程式 Web 站點時，該事件被觸發。

# Webpage

## ASP.NET 頁面事件的執行順序

&lt;!--#include file="page-life-cycle.htm" --&gt;    
#### PreInit

這個階段用來進行物件初始化，例如： MasterPage, Theme, 動態產生控制項。  

#### PreLoad

ViewState 會在這個階段前載入，所以必須在這個階段才讀取的到。  所以若你要變更 ViewState ，至少要在這個階段之後。  

#### Load

這個階段初始化以及ViewState(控件的狀態)都會被建立完成。  Load事件是由Page的Load最先執行，再執行其下的控件的Load事件。  

#### Control Event

這個事件是 PostBack 後才會執行。緊跟著 Page\_Load 之後。  

#### PreRender

ViewState 會在這個階段前儲存，所以必須在這個階段才讀取的變更後的值。  

#### Render

在這個階段可以撰寫一些我們的自訂控件的需求。

## ViewState 與 Page 事件的順序

在 WebPage 的生命週期中，會有二個事件用來讀取和寫入 ViewState 。

- [LoadViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.loadviewstate.aspx)  ：Restores view-state information from a previous page request that was saved by the [SaveViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.saveviewstate.aspx) method.
- [SaveViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.saveviewstate.aspx) ：Saves any server control view-state changes that have occurred since the time the page was posted back to the server.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYE37rKtCZrXCUdlnFQxa-EEkWPbTljo2TKgD_KfguzYnxgBknWQFkG2CQWKLJ8POk62OyI_I24FAk1mPWuobykipQjulr66pDwm1VLAUglB778jokHFiOCk8czfsghD9GgCiCPF8h_KQ/s639/viewstate-lifecycle.png)

所以若要變更 ViewState ，必須在 Page\_PreLoad 之後。  另外，你可能利用 postback 事件去更改 ViewState 值，所以若你想利用 ViewState 值，以管理控制項狀態，通常會在 Page\_PreRender 執行。

# Controls

## 動態加入控制項

若要在執行階段，動態加入控制項，如果沒有使用 MasterPage ，可以在 Page\_PreInit 事件中。  若有用 MasterPage ，則必須在 Page\_Init 事件中。  

由於 Theme 在 Page\_PreInit 就載入結束，所以如果你在 Page\_Init 事件才加入控制項，就必須自行呼叫 [Control.ApplyStyleSheetSkin](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.applystylesheetskin.aspx) 方法，以套用樣式。
```c#
protected void Page_PreInit(object sender, EventArgs e)
{
btnTestButton1.Text = "Test Button 1";
btnTestButton1.Click += btnTestButton1_Click;

Literal1.Text = "<br>";
PlaceHolder1.Controls.Add(Literal1);

btnTestButton2.Text = "Test Button  2";

PlaceHolder1.Controls.Add(btnTestButton1);
PlaceHolder1.Controls.Add(btnTestButton2);
}

protected void Page_Load(object sender, EventArgs e)
{
//若在 Page_Init 才載入控制項，則必須手動套用樣板
btnTestButton1.ApplyStyleSheetSkin(this);
}

public void btnTestButton1_Click(object sender, EventArgs e)
{
myMessage.Show(this, "Test Button 1 Click");
}
```

## 控制項事件的執行順序

所有的控制項和 Page 都是繼承自 Control 物件，所以都使用相同的生命週期。  例如：當 Page 發生 Load 事件，則會相繼發生其子控制項的 Load 事件。  

另外，要注意動態載入的控制項，其事件順序就得看它的實體何被建立的。  

另外，若在 Page\_Render 中才執行動態加入控制項，就無效了。