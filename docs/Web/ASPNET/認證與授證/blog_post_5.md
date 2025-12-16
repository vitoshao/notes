---
title: 使用AJAX進行Form驗證
layout: default
parent: 認證與授證
nav_order: 5
description: "使用AJAX進行Form驗證"
date: 2012-12-31
tags: [Web,ASPNET,認證與授證]
postid: "4236619631533535848"
---
# Profiles and Authentication

**Profiles Service** 和 **Authentication Service** 本來是 [ASP.NET Forms Authentication system](http://msdn.microsoft.com/en-us/library/ff647070.aspx) 機制底下所提供的標準服務。  而 **AJAX Extensions Library** 則是透過 script proxy 方式，在 [Sys.Services](http://msdn.microsoft.com/zh-tw/library/bb311017%28v=vs.100%29.aspx) 命名空間底下，提供 script 方法，讓用戶端可以直接存取這些服務。   

#### The Authentication service 

The Authentication service allows users to provide credentials in order to receive an authentication cookie, and is the gateway service to allow custom user profiles provided by ASP.NET.   Use of the ASP.NET AJAX authentication service is compatible with standard ASP.NET Forms authentication,   so applications currently using Forms authentication (such as with the [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) control) will not be broken by upgrading to the AJAX authentication service.  

#### The Profile service 

The Profile service allows the automatic integration and storage of user data based on membership as provided by the Authentication service.   The stored data is specified by the web.config file, and the various profiling service providers handle the data management.   As with the Authentication service, the AJAX Profile service is compatible with the standard ASP.NET profile service,   so that pages currently incorporating features of the ASP.NET Profile service should not be broken by including AJAX support.

# 使用 AJAX Library 進行表單驗證

## 如何使用 AJAX 進行表單驗證

### 1. 啟用驗證服務

若要從用戶端指令碼使用驗證服務，您必須在應用程式的 Web.config 組態檔中設定 **authenticationService** 屬性，明確啟用驗證服務：
```xml
<system.web.extensions>
<scripting>
<webServices>
<authenticationService enabled="true" />
</webServices>
</scripting>
</system.web.extensions>
```

### 2. 使用 Sys.Services.AuthenticationService 驗證使用者

只要 AJAX authentication service 被設定之後，用戶端就可以直接使用 [Sys.Services.AuthenticationService](http://msdn.microsoft.com/zh-tw/library/bb310861.aspx) 物件。  這個物件提供透過 Web Service 或 WCF Servie 來驗證使用者的方法。  以下是它提供的幾個方法或屬性：  

- login：驗證使用者的憑證。
- logout：登出目前已驗證的使用者。
- isLoggedIn：取得目前使用者的驗證狀態。
```javascript
Sys.Services.AuthenticationService.login(userName, password, isPersistent, customInfo, redirectUrl, loginCompletedCallback, failedCallback, userContext);
Sys.Services.AuthenticationService.logout(redirectUrl, logoutCompletedCallback, failedCallback, userContext);
Sys.Services.AuthenticationService.get_isLoggedIn();
```

#### 注意事項

若要取得或設定用戶端 API 屬性的屬性值，您必須呼叫名稱前置詞為 get\_ 和 set\_ 的屬性存取子方法。

## 範例程式碼
```javascript
<head id="Head1" runat="server">
<title></title>
<script language="javascript" type="text/javascript">
//登入
function btnAuthentication_onclick() {

var AuthService = Sys.Services.AuthenticationService;
AuthService.set_defaultLoginCompletedCallback(OnLoginCompleted);
AuthService.set_defaultFailedCallback(OnLoginFailed);

var username = $get("username");
var password = $get("password");

Sys.Services.AuthenticationService.login(
username.value, password.value, false,null,null,null,null,"User Context");

}

//登入完成要做的事情
function OnLoginCompleted(validCredentials, userContext, methodName) {
if (validCredentials == true) {
alert('你已登入完成.'); 
}
else {
alert("Oops! You gave us bad credentials!");
}
}

//登入失敗要做的事情
function OnLoginFailed(error, userContext, methodName) {
alert('登入失敗..\n\n' + error.get_message()); 
}

//登出
function btnLogout_onclick() {
Sys.Services.AuthenticationService.logout(null, LogoutServiceCompleted, LogoutServiceFailed, "Context Info");
}
function LogoutServiceCompleted(result, userContext, methodName) {
alert('你已完成登出.'); 
}
function LogoutServiceFailed(error, userContext, methodName) {
alert('登出失敗..\n\n' + error.get_message()); 
} 
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
username:<input id="username" type="text" /><br />
password:<input id="password" type="text" /><br />
<input id="btnAuthentication" type="button" value="驗證" onclick="return btnAuthentication_onclick()" />
</div>
</form>
</body>
```

## 如何在 Client 端判斷登入狀態
```javascript
function btnIsLogin_onclick() {
alert(Sys.Services.AuthenticationService.get_isLoggedIn());
}
```

## 如何在 Server 端判斷登入狀態
```c#
protected void btnIsAuthenticatedServer_Click(object sender, EventArgs e)
{
if (User.Identity.IsAuthenticated)
myMessage.Show(this, "已登入");
else
myMessage.Show(this, "尚未登入");
}
```

# 使用自訂的 Web Service 進行表單驗證

前面例子，我們使用 ASP.NET 提供的 Forms Authentication services 進行驗證，也就是使用 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 的資料進行驗證。  如果要自訂驗證，可以透過 Web Service 來進行。這個 Web Service 必須提供二個方法：login 和 logout 。  同時它們的簽名必須和預設的 ASP.NET AJAX Authentication services 的方法相同。  

## 設計 Web Service

這個 Web Service 有二點注意事項：  
1) 由於這個 Web Service 將允許透過 Script 叫用，所以必須設定 [[ScriptService](http://msdn.microsoft.com/zh-tw/library/system.web.script.services.scriptserviceattribute.aspx)] 屬性。  
2) 如果這個 Web Service 會使用到 Session ，要設定 **EnableSession = true** 屬性。  
```c#
using System;
using System.Web;
using System.Web.Services;
using System.Web.Services.Protocols;
using System.Web.Script.Services;
using System.Web.Security; 

[ScriptService]
[WebService] 
public class AuthService : System.Web.Services.WebService {

[WebMethod]
public bool Login(string userName, string password, bool createPersistentCookie) 
{
if ((userName == "vito") && (password == "2213"))
{
FormsAuthentication.SetAuthCookie(userName, createPersistentCookie);
return true;
}
return false;
}
[WebMethod]
public void Logout() 
{
FormsAuthentication.SignOut();
} 
}
```

## 指定 Web Service 路徑

要指定這個 web service 的路徑讓 script 知道，有以下幾個方法：

#### 在 ScriptManager 的 [AuthenticationService](http://msdn.microsoft.com/zh-tw/library/bb310861.aspx) 項目中指定。
```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">
<AuthenticationService Path="AuthService.asmx" /> 
</asp:ScriptManager>
```

#### 在 Script 中，使用 AuthenticationService.set\_path 方法指定。
```javascript
function btnLoginCustom_onclick() {
var username = $get("username");
var password = $get("password");
Sys.Services.AuthenticationService.set_path("AuthService.asmx");
Sys.Services.AuthenticationService.login(username.value, password.value, false, null, null, OnLoginCompleted, OnLoginFailed, "Context Info"); 
}
```

經過以上的設定，我們就可以透過與上節同樣的叫用方法，使用自訂授權來驗證。

# 使用自訂的 WCF Service 進行表單驗證

若要透過 WCF Service 進行表單驗證，其做法大至同 Web Service ，只要設計一個支援 AJAX 的 WCF 服務即可。

## WCF Service 程式設計

因為我們希望可以直接透過 AJAX 叫用服務，所以必須在專案中加入「已啟用 AJAX 的 WCF 服務」項目。  然後同樣的，必須設計 login 和 logout 方法。  
```c#
using System;
using System.ServiceModel;
using System.ServiceModel.Activation;
using System.ServiceModel.Web;
using System.Text;
using System.Web.Security;

[ServiceContract(Namespace = "")]
[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
public class AuthServiceWcfRest
{
[OperationContract]
public bool Login(string userName, string password, bool createPersistentCookie)
{
if ((userName == "vito") && (password == "2213"))
{
FormsAuthentication.SetAuthCookie(userName, createPersistentCookie);
return true;
}
return false;
}

[OperationContract]
public void Logout()
{
FormsAuthentication.SignOut();
} 
}
```

## 頁面程式設計

這裡的做法，與上節中的做法幾乎是一模一樣的。
```xml
<head id="Head1" runat="server">
<title></title>
<script language="javascript" type="text/javascript">

//登入
function btnLogin_onclick() {
var username = $get("username");
var password = $get("password");
Sys.Services.AuthenticationService.login(username.value, password.value, false, null, null, OnLoginCompleted, OnLoginFailed, "Context Info");
}
//登入完成要做的事情
function OnLoginCompleted(validCredentials, userContext, methodName) {
if (validCredentials == true) {
alert('你已登入完成.');
}
else {
alert("Oops! You gave us bad credentials!");
}
}
//登入失敗要做的事情
function OnLoginFailed(error, userContext, methodName) {
alert('登入失敗..\n\n' + error.get_message());
}

//登出
function btnLogout_onclick() {
Sys.Services.AuthenticationService.logout(null, OnLogoutCompleted, OnLogoutFailed, "Context Info");
}
function OnLogoutCompleted(result, userContext, methodName) {
alert('你已完成登出.');
}
function OnLogoutFailed(error, userContext, methodName) {
alert('登出失敗..\n\n' + error.get_message());
}

//判斷登入狀態
function btnIsLogin_onclick() {
alert(Sys.Services.AuthenticationService.get_isLoggedIn());
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
<AuthenticationService Path="AuthServiceWcfRest.svc" /> 
</asp:ScriptManager>
<h3>Form Authentication Using WCF RESTful service </h3>
username:<input id="username" type="text" /><br />
password:<input id="password" type="text" /><br />
<input id="btnLogin" type="button" value="登入" onclick="return btnLogin_onclick()"/>
<input id="btnLogout" type="button" value="登出" onclick="return btnLogout_onclick()" />

<br /><br />
<asp:Button ID="btnIsAuthenticatedServer" runat="server" Text="IsAuthenticated (server-side)" onclick="btnIsAuthenticatedServer_Click" />
<input id="btnIsLogin" type="button" value="IsAuthenticated (clent-side)" onclick="return btnIsLogin_onclick()" />
</div>
</form>
</body>
```
## 參考資料  

- [Understanding ASP.NET AJAX Authentication and Profile Application Services](http://www.asp.net/web-forms/tutorials/aspnet-ajax/understanding-asp-net-ajax-authentication-and-profile-application-services)
- 
-