---
title: 用戶端狀態管理
layout: default
parent: State and Caching
nav_order: 1
description: "用戶端狀態管理"
date: 2012-10-02
tags: [Web,ASPNET,State and Caching]
postid: "475127328079962480"
---
HTTP 是沒有狀態（Stateless）的通訊協定，ASP.NET 提供數種狀態管理機制，分為用戶端與伺服器端兩種。  

- 用戶端包含 View state 、Control state、Hidden fields、Cookies 、Query strings 五種
- 伺服器包含 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) state、Session state、Profile Properties 三種

# ViewState

當ASP.NET網頁被執行的時候，網頁上的資料，會被雜湊成字串儲存在網頁的隱藏欄位。  它的目的是用來保持伺服器控制的狀態，等到該網頁下次向主機提出 HTTP 要求時，就可以用來區分。  比方說，一個 TextBox 的值為 ABC ，"ABC" 就會被記錄在 ViewState 之中，當發生 postback 事件時，伺服器就可以根據現有 Textbox 中的值和 ViewState 中的值做比對，決定是否引發 TextChange 事件。  
```xml
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" 
value="/wEPDwUKMTIxNDIyOTM0Mg9kFgICAw9kFgICAQ8PFgIeBFRleHQFEzQvNS8yMDA
2IDE6Mzc6MTEgUE1kZGROWHn/rt75XF/pMGnqjqHlH66cdw==" />
```

## View State 的安全考量

網頁訊息在傳送前，ViewState 中的資料都會先 Hash + Encode ，所以它只有經過編碼，並非加密的資料。  如果針對較機密性的資料，可以使用 [ViewStateEncryptionMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.viewstateencryptionmode.aspx) 屬性執行加密。  

#### 1. 整個網站加密

在 Web.config 中設定 &lt;page&gt; 的 viewStateEncryptionMode 屬性。
```xml
<configuration> 
<system.web> 
<pages viewStateEncryptionMode="Always"/> 
</system.web> 
</configuration>
```

#### 2. 特定網頁加密

在網頁上頭的 ＠ Page 指示詞中，啟用加密功能  
```xml
<%@ Page Title="" Language="C#" ViewStateEncryptionMode="Always" %>
```

## 停用 ViewState

預設上 ViewState 功能會自動啟動，若要停用這個功能可以在 @Page 宣告中，將 [EnableViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.enableviewstate.aspx) 屬性設成 false 即可。

尤其是由資料庫中撈取展示用的資料時，若不關閉 ViewState ，不僅浪費 CPU 產生 ViewState 的時間，也浪費網頁傳輸的時間。

#### 整個網頁停用
```xml
<%@ Page Language="C#" EnableViewState="false"%>
```

#### 特定控制項停用
```xml
<asp:TextBox ID="TextBox1" runat="server" EnableViewState="false" />
```

## Reading and Writing Custom ViewState Data

#### 讀寫 ViewState

ViewState 是一個 Dictionary 集合，底下是它的讀取與寫入的範例：  
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
ViewState.Add("Visits", 1);
}
}
protected void Button2_Click(object sender, EventArgs e)
{
if (ViewState["Visits"] != null)
ViewState["Visits"] = (int)ViewState["Visits"] + 1;
else
ViewState["Visits"] = 1;
}
```

※注意事項：ViewState 也可以存放 object 型態的資料，只要是**可序列化**的型別，都可以存放在 ViewState 。

#### 某些控制項的 ViewState 被停用後，為何還可以取的到值

當 ViewState 被停用後，則網頁 postback 之後將無法透過 ViewState 取得伺服器控制項原先的狀態。  但是，某些控制項(如：TextBox, CheckBox)為什麼即使停用 ViewState 後，在 postback 之後仍然可以取得狀態值呢？  這是因為 postback 之後，ASP.NET 會執行二項與處理資料相關的事件: [LoadViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.loadviewstate.aspx) 和 [LoadPostData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ipostbackdatahandler.loadpostdata.aspx) 。  只要有實作 [IPostBackDataHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ipostbackdatahandler.aspx) 介面的控制項，都會在 [LoadPostData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ipostbackdatahandler.loadpostdata.aspx) 事件中被重新載入狀態。  所以一些表單的控制項，如：TextBox, CheckBox ...，即使停用  ViewState 時，在 postback 後，其狀態值還是可以被記錄下來。  

#### ViewState 與 Page 事件的順序

下面這個例子，我們在 Button\_Click 事件中，遞增某個 ViewState 的值，觀察它在網頁生成的各個階段的變化，可以看的出來：  

- PostBack 後，至少要到 **Page\_PreLoad** 才能取得 ViewState 值。
- 若在 Click 事件中更新 ViewState 的值，則必須在 Click 事件後的事件才會取的到新的值，如： **Page\_LoadComplete** 。

也就是說， ViewState 物件，至少要在 Page\_PreLoad 之後才會載入完畢。  
若在控制項的 Postback 事件中，有任何異動，則必須在 Page\_LoadComplete 之後，取到的值才會是新的。  

所以若要載入先前 ViewState 上的資料到控制項，可以在 Page\_Load。  
若要儲存控制項上的資料到 ViewState ，可以在 Page\_PreRender。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYE37rKtCZrXCUdlnFQxa-EEkWPbTljo2TKgD_KfguzYnxgBknWQFkG2CQWKLJ8POk62OyI_I24FAk1mPWuobykipQjulr66pDwm1VLAUglB778jokHFiOCk8czfsghD9GgCiCPF8h_KQ/s639/viewstate-lifecycle.png)

#### Page 的 ViewState 的載入與儲存

當頁面載入時，系統會自動載入 ViewState 。同樣的，當頁面送出到用戶端之前，也必須將 ViewState 寫到頁面中。  它分別由 Control 物件的以下二個事件處理。

- [LoadViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.loadviewstate.aspx)  ：Restores view-state information from a previous page request that was saved by the [SaveViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.saveviewstate.aspx) method.
- [SaveViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.saveviewstate.aspx) ：Saves any server control view-state changes that have occurred since the time the page was posted back to the server.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjs0Z5kNxfynBpxsUxF7uLlgzUFTZl08TH85i4PJBP-hhf9sAb6IHm1KYBjJqdQbY8LTGl4TG5oVGZXL2kh5JRkuZ1qjd4wO8GkBOxgVELkBtaLitB1FW7c8wa1woYz_sKatV_wIbhkD-I/s256/viewstate-load-save.png)

## ViewStateMode

由 .NET 4.0 開，對於 ViewState 增加了更彈性的 [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 設計，  各種 Control(包含Page) 都有這個個屬性，它允許控制項自行決定是否聽從父(上層)控制項的 ViewState 設定， [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 具有以下三個列舉值設定：  

- Inherit ：繼承父控制項的設定。
- Enabled ：即使父控制項的ViewState已停用，仍然啟用自身的ViewState。
- Disabled ：即使父控制項的ViewState已啟用，仍然停用自身的ViewState。

在 MSDN 的 [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 介紹有提到：

The [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) property of a page or a control has an effect only if the [EnableViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.enableviewstate.aspx) property is set to true.   If the [EnableViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.enableviewstate.aspx) property is set to false, view state will be turned off even if the [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) property is set to Enabled.  

所以，唯有當 [EnableViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.enableviewstate.aspx) 設為 true 時，使用 [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 才有意義。  

例如：若希望一個網頁停用 ViewState ，但其中有一個特定的控制項必須使用 ViewState ，你可以這麼做。  
```xml
<%@ Page Language="C#" EnableViewState="true" ViewStateMode="Disabled" %>

<asp:Label ID="Label1" runat="server" Text="Label"></asp:Label><br />
<asp:Label ID="Label2" runat="server" Text="Label"></asp:Label><br />
<asp:Label ID="Label3" runat="server" Text="Label" ViewStateMode="Enabled"></asp:Label><br />
```

上層控制項(Page)的 ViewState 被停用了，但是第三個 Label 使用 [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 再啟用 ViewState 。  如果上層控制項(Page)將 [EnableViewState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.enableviewstate.aspx) 設成 false ，則子控制項的 [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.viewstatemode.aspx) 將沒有做用。

# Control State

ControlState 功能就像 ViewState ，只不過它只能使用在自訂控制項上。  因為在建立自訂控制項時，有時候必須記錄某些狀態值，但為了避免使用者關閉 ViewState 功能，所以可以改用 ControlState 。  

#### 使用 ControlState 的步驟方法：

1. 覆寫 [OnInit](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.oninit.aspx) 方法  
在這個方法中叫用 [RegisterRequiresControlState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.registerrequirescontrolstate.aspx) 方法，向網頁註冊必須保存控制項的狀態。每個要求都必須這樣執行。
2. 覆寫 [SaveControlState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.savecontrolstate.aspx) 方法  
在這個方法中將要儲存的資料儲存到 ControlState 物件中。  
這個方法會傳回伺服器控制項的目前狀態。如果沒有與控制項相關的狀態，這個方法就會傳回 Null 參照。
3. 覆寫 [LoadControlState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.loadcontrolstate.aspx) 方法  
在這個方法中讀取 ControlState 物件中的資料。  
這個方法會從 [SaveControlState](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.savecontrolstate.aspx) 方法所儲存的上一頁要求中，還原控制項狀態資訊。

MSDN範例：[控制項狀態和檢視狀態的比較範例](http://msdn.microsoft.com/zh-tw/library/1whwt1k7.aspx)

## 範例：使用 ControlState 儲存狀態
```c#
public partial class ucLogin_ControlState : UserControl
{
public delegate void LogInHandle(string name);
public delegate void LogOutHandle(string name);

public event LogInHandle onLogIn;
public event LogOutHandle onLogOut;

private MyControlState myControlState = new MyControlState();
public int LoginErrorTimes { get; set; }
public string LastLoginTime { get; set; }
public string Account
{
get { return txtAccount.Text; }
set { txtAccount.Text = value; }
}
public string Password
{
get { return txtPassword.Text; }
set { txtPassword.Text = value; }
}

protected void btnLogin_Click(object sender, EventArgs e)
{
if ((txtAccount.Text == "1") && (txtPassword.Text == "1"))
{
if (onLogIn != null)
{
onLogIn(Account);
}
btnLogin.Visible = false;
btnLogout.Visible = true;
}
else
{                
LoginErrorTimes = LoginErrorTimes + 1;
}
}

protected void btnLogout_Click(object sender, EventArgs e)
{
btnLogin.Visible = true;
btnLogout.Visible = false;
if (onLogOut != null)
{
onLogOut(Account);
}
}

protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
LoginErrorTimes = 0;
}

protected void Page_PreRender(object sender, EventArgs e)
{
litErrorTimes.Text = LoginErrorTimes.ToString();
litLastLoginTime.Text = "LastLoginTime:" + LastLoginTime + " <br>Now:" + System.DateTime.Now.ToString();
LastLoginTime = System.DateTime.Now.ToString();
}

protected override void OnInit(EventArgs e)
{
base.OnInit(e);
Page.RegisterRequiresControlState(this);
}

/// <summary>
/// 自訂一個類別，用來記錄要儲存的資訊
/// </summary>
[Serializable()]
private class MyControlState
{
//登入錯誤的次數
public int LoginErrorTimes { get; set; }
//上次登入的時間
public string LastLoginTime { get; set; }
}

/// <summary>
/// 儲存特定控制項的狀態資訊。
/// </summary>
/// <returns>傳回伺服器控制項的目前狀態。如果沒有與控制項相關的狀態，這個方法就會傳回 null。</returns>
protected override object SaveControlState()
{
if (myControlState != null)
{
myControlState.LastLoginTime = LastLoginTime;
myControlState.LoginErrorTimes = LoginErrorTimes;
return myControlState;
}
else
{
return null;
}
}

/// <summary>
/// 從 SaveControlState 方法所儲存的上一頁要求中，還原控制項狀態資訊。
/// </summary>
/// <param name="state"></param>
protected override void LoadControlState(object state)
{
if (state != null)
{
MyControlState controlState = state as MyControlState;
if (myControlState != null)
{
LastLoginTime = controlState.LastLoginTime;
LoginErrorTimes = controlState.LoginErrorTimes;
}
else
{
LastLoginTime = "";
LoginErrorTimes = 0;
}
}
}
}
```

# Hidden Fields

在 ASP.NET 之中，view state 本身就是使用 hidden fields 來記錄資料，這個控制項雖然包含在網頁之中，但不會顯示任何資訊在頁面上。  簡單講它就是一個隱藏欄位，可以將資訊送到客戶端，但不讓使用者在瀏覽器上看到。  且這個欄位的資訊仍可以透過表單 postback 回後端，以進行進一步的處理。  只是它不像 ViewState 支援編碼或加密。  
```xml
<asp:HiddenField ID="HiddenField1" runat="server" Value="abe123" />
```

# Cookies

A cookie is a small amount of data that you write to the client to be stored and then passed with requests to your site.   You write persistent cookies to a text file on the client machine.  These cookies are meant to survive the user shutting down the browser and reopening it at a later time.   You can also write temporary cookies to the memory of the client's browser.   These cookies are used only during the given Web session. They are lost when the browser closes.  

## Reading and Writing Cookies

Web application creates a cookie by sending it to the client as a header in an HTTP response.  Web browser then submits the same cookie to the server with every new request.  
```c#
protected void btnReadCookie_Click(object sender, EventArgs e)
{
if (Request.Cookies["UserName"] != null)
{
myDebug.ResponseBR(Request.Cookies["UserName"].Value);
}
else
myDebug.ResponseBR("No value defined");
}

protected void btnWriteCookie_Click(object sender, EventArgs e)
{
Response.Cookies["UserName"].Value = "Vito";
Response.Cookies["UserName"].Expires = DateTime.Now.AddMinutes(2);
}
```

- [Expires](http://msdn.microsoft.com/zh-tw/library/system.web.httpcookie.expires.aspx) 是用來設定 Cookie 的保留時間，若沒有設定，瀏覽器會將資訊儲存於記憶體，如果使用者關閉瀏覽器， cookie 就會遺失。
- 因為 cookie 儲存於用戶端的電腦，所以無法直接刪除。如果要刪除 cookie ，將 cookie 的 [Expires](http://msdn.microsoft.com/zh-tw/library/system.web.httpcookie.expires.aspx) 設定為過去的時間即可。

# Query Strings

## 讀取 Query Strings

Query strings are commonly used to store variable values that identify specific context for a requested page.  The values sent to your page via the query string can be retrieved on the server through the [Request.QueryString](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.querystring.aspx) property of Page object.  
```c#
myDebug.ResponseBR("id : " + Request.QueryString["id"]);
myDebug.ResponseBR("name : " + Request.QueryString["name"]);
```

#### 缺點

- Some browsers and client devices impose a 2,083-character limit on the length of the URL.
- values are visible to the user in his or her address bar.

#### 優點

- their data can be included in bookmarks and e-mailed URLs.
- it is the only way to enable a user to include state data when copying and pasting a URL to another user.

#### Server.HtmlEncode

Server.HtmlEncode會將瀏覽器無法處理的特殊字元替換HTML程式碼。例如將「&lt;」符號替換為「&lt;」