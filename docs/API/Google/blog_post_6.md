---
title: 在網站應用程式中取得 Google OAuth 2.0 授權
layout: default
parent: Google
nav_order: 6
description: "在網站應用程式中取得 Google OAuth 2.0 授權"
date: 2015-04-23
tags: [API,Google]
postid: "8753286827259926396"
---
Google OAuth 2.0 伺服器支援 Web 程式存取，如 ASP.NET, PHP, Java, Python 等。  只要程式端握有存取憑證，不管使用者是否有在線上，都可以直接存取 Google API 。  

# 架構概說

在 Google 的 Using OAuth 2.0 for Web Server Applications 文件中，有底下這張循序圖，說明了 OAuth2 使用流程。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjG-98u2nxqJ3KrW8DhBofOusuo1q9vqvOZDkOKAxOJyIZuV9gLy0nqJt1ZUJs2eS9ru8BXPOcFfyaQZoiK2wDD6oXaQ5Di8EcHEAEBqoI36qVB0JJMlqIpB8ussaHD0updOWb7XN6czKU/s364/webflow.png)

步驟說明：

- 將 user 導向 Google 的 consent 頁面以便向 OAuth Server 要求 Token 。
- 使用者同意授權。
- Google 回傳一個授權碼到指定的端點位址
- 在該端點位址中，利用這個授權碼(Code)與 Google 交換存取憑證(Token)。
- 利用這個存取憑證(Token)叫用 Google API。

# 申請「網路應用程式」憑證

存取 Google API 必須先至 [Google Developers Console](https://console.developers.google.com) 申請應用程式憑證。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZrVF6ivVolwyAA_dGFm-ZammMlG8EuyE1sApdHnGAulIw7OTXp-Cv_rirs6rmkEw8RH9Ktgh3H0dBfFXvE-R4knK8IMGRFKoGErjhHyRvfEcWkzhndyjGXc5WTG4NtObbV18rJ2cmMQs/s680/BloggerAPI-10.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzakj0UMIvx95VlGqnwAtltZD7wYg6yqmsR2a1bDJV1YUcQ0b900uVC-Umtk4PFbBI6QbEhV-CJTVz-YGNyS2c-ZaOgS6xgPMSfYz0x6SB7f7ZCojo8BrL9UXC3eMKSFJ8549lrbLjIUc/s489/BloggerAPI-11.png)

在上面這個設定畫面中，有個項目叫授權的重新導向URI，指的就是用來接收處理 authorization code 的網址。

# 取得 Access Token

要取得 Access Token ，你可以依底下步驟設計，更詳細的說明，請參考文件：[https://developers.google.com/identity/protocols/OAuth2WebServer#formingtheurl](https://developers.google.com/identity/protocols/OAuth2WebServer#formingtheurl)  

## 1. 取得使用者授權

當 APP 要存取 Google 的資源之前，必須先取得使用者的同意授權，你可以將 Scope, Client Id 送至 https://accounts.google.com/o/oauth2/auth 要求使用者授權。  範例說明：  

### 送出需求

https://accounts.google.com/o/oauth2/auth?
scope={0}&
state={1}&
redirect_uri={2}&
response_type=code&
client_id={3}&
approval_prompt=force
- scope：授權範圍。
- state：可任意附加的字串，Server會將此參數值回傳到 RedirectUri 那個網址。
- redirect\_uri：給 Google OAuth Server 回傳 Authrization Code 的網址。
- response\_type：回傳的型別 Code 或 Token，網站類型應用程式不建議使用Token。
- client\_id：。申請的Client ID

程式範例：
```c#
string scope = "https://www.googleapis.com/auth/blogger";

string oauthURL = "https://accounts.google.com/o/oauth2/auth?" +
"scope={0}&state={1}&redirect_uri={2}&response_type=code&client_id={3}&approval_prompt=auto";

string state = "bloglist";

oauthURL = string.Format(oauthURL,
HttpUtility.HtmlDecode(scope),
HttpUtility.HtmlDecode(state),
HttpUtility.HtmlDecode(redirect_uri),
HttpUtility.HtmlDecode(client_id));

HttpContext.Current.Response.Redirect(oauthURL);
```

執行結果所開啟的授權頁面：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdM2u23k3nztp-g-WUlBIV_JEedBa6z75MPF8S2bsH1LVT-sAuSscfikWLsxjY7yopiJezMTFXFm4L8R5wbNzT3TytNrogwdULahSAyamVJ8XAvysYiBKvNXixGJdsgGRk8uN5MPJNgzs/s492/google-oauth-web-01.png)

### 處理回應

如果使用者在 consent 頁面中按下了同意授權按鈕，Google 將會回傳一個 authorization code 到指定的網址，如下範例：
http://localhost:7003/OAuth/oauth.aspx?code=4/eEMTPZGz6SDgON2SalG0x9TcGkWh8CtkUocmPljhcHM.UunbPgwwZF8ZgrKXntQAax10lkHImQI&state=bloglist  
若使用者按下不同意授權，Google 將回傳以下資料：
http://localhost:7003/OAuth/oauth.aspx?error=access_denied&state=bloglist  
所以這個 redirect\_uri 就是用來處理回應的網頁，你可以在這個端點中提取 authorization code ，並做適當的處理。
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!Page.IsPostBack)
{
string code = Request["code"];
string state = Request["state"];

if (code != null)
{
switch (state)
{
case "bloglist":
GetBloggerList(code);
break;
case "user":
GetUserProfile(code);
break;
}
}
else
{
string error = Request["error"];
Response.Write(error);
Response.End();
}
}
}
```

## 2. 取得存取憑證（Access Token）

前面在取得使用者授權的網址中有個參數叫 redirect\_uri ，這個參數值必須等同向 Google 申請網路應用程式憑證時所填寫的網址。  你可以在這個網址中取得授權碼，再和 Google 交換存取憑證（Access Token）。  

### 送出需求

在取得 authorization code 之後，你可以送出以下需求，以取得存取憑證：  
POST https://www.googleapis.com/oauth2/v3/token
Content-Type: application/x-www-form-urlencoded

code={authorization code}&
client_id={your client_id}&
client_secret={your client_secret}&
redirect_uri={your redirect_uri}&
grant_type=authorization_code  
底下是 ASP.NET 的程式碼範例，示範如何送出上述的需求內容：
```c#
protected void Page_Load(object sender, EventArgs e)
{
string code = Request["code"];      //Google傳過來的授權碼
string state = Request["state"];    //傳送自訂參數值

if (code != null)
{
string useremail = ConfigurationManager.AppSettings["UserEmail"];
string appname = ConfigurationManager.AppSettings["AppName"];

Google_WebClient oauth = new Google_WebClient(useremail, state, null, appname);
oauth.Token = oauth.RequestToken(code);
oauth.SaveToken();
}
else
{
Console.WriteLine(Request["error"]);
}
this.ClientScript.RegisterClientScriptBlock(this.GetType(), "Close", "window.close()", true);
}
```
```c#
public class Google_WebClient
{
public TokenResponse RequestToken(string code)
{
string tokenUrl = string.Format("https://www.googleapis.com/oauth2/v3/token");

string queryString = @"code={0}&client_id={1}&client_secret={2}&redirect_uri={3}&grant_type=authorization_code";
string postContent = string.Format(queryString,
HttpUtility.HtmlEncode(code),
HttpUtility.HtmlEncode(client_id),
HttpUtility.HtmlEncode(client_secret),
HttpUtility.HtmlEncode(redirect_uri));

HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(tokenUrl);
request.Method = "POST";
request.ContentType = "application/x-www-form-urlencoded";
using (var sw = new StreamWriter(request.GetRequestStream()))
{
sw.Write(postContent);
}

var result = "";
using (var response = request.GetResponse())
{
using (var sr = new StreamReader(response.GetResponseStream()))
{
result = sr.ReadToEnd();
}
}

GoogleOAuthToken token = JsonConvert.DeserializeObject<GoogleOAuthToken>(result);
return token;
}
}
```

### 處理回應

若需求成功，將回應一個 JSON 格式的 Access Token ，如下：
{
"access_token":"1/fFAGRNJru1FTz70BzhT3Zg",
"expires_in":3920,
"token_type":"Bearer"
}

## 3. 取得離線存取憑證（Offline Access Token）

Access Token 有一定的時效性，且時效通常不會太長，如果超過有效期限，APP 就必須要求使用者重新 Consent 以取得 Access Token 。  可是如果使用者不在線上， APP 又必須存取 API ，這時候就可以使用「離線存取」，它的目的就是不須要使用者重新 Consent 就可以獲得新的 Access Token ，例如 APP 想在離鋒時段下載資料以進行備份。  

如果 APP 需要離線存取，只須要在授權 API 中，加入 access\_type = offline 參數即可。  
https://accounts.google.com/o/oauth2/auth?
scope={0}&
state={1}&
redirect_uri={2}&
response_type=code&
client_id={3}&
approval_prompt=force&
access_type=offline  
在使用者同意授權後，OAuth Server 同樣的會回傳一個 authorization code 到 redirect\_uri ，你就可以拿它去取得 Access Token 。  
POST https://www.googleapis.com/oauth2/v3/token
Content-Type: application/x-www-form-urlencoded

code={authorization code}&
client_id={your client_id}&
client_secret={your client_secret}&
redirect_uri={your redirect_uri}&
grant_type=authorization_code  
不過這時候取得的 Token 資料中，會多一個 refresh token 。  
{
"access_token":"1/fFAGRNJru1FTz70BzhT3Zg",
"expires_in":3920,
"token_type":"Bearer"
"refresh_token":"1/xEoDL4iW3cxlI7yDbSRFYNG01kVKM2C-259HOF2aQbI"
}  
若你用這個方式取得 Refresh Token ，你必須將它儲存起來，之後才能利用它與 OAuth Server 要求新的 Access Token 。

## 4. 使用 Refresh Token 更新 Access Token

當 Access Token 過期，而你又握有 Refresh Token 時，你就可以直接透過它取得新的 Access Token ，無須要求使用者再次 Consent 。
POST https://www.googleapis.com/oauth2/v3/token
Content-Type: application/x-www-form-urlencoded

refresh_token={refresh_token}&
client_id={your client_id}&
client_secret={your client_secret}&
grant_type=refresh_token  
使用 refresh token 重置 token 時，回傳資料中將不會再包含 refresh token ，例如：
{
"access_token":"1/fFAGRNJru1FTz70BzhT3Zg",
"expires_in":3920,
"token_type":"Bearer"
}  
雖然新取得的 token 不含 refresh token ，但是原先的 refresh token 還是有效的，等下次 token 過期時，你還是可以直接用舊有的 refresh token 去交換新的 token 。

## 5. 撤銷使用者授權

若使用者想要撤銷授權，他可以自行到 [https://accounts.google.com/b/0/IssuedAuthSubTokens](https://accounts.google.com/b/0/IssuedAuthSubTokens) 網站撤銷，除此之外，也可以透過程式來撤銷。  
https://accounts.google.com/o/oauth2/revoke?token={0}
- token：這個參數值，可以帶入 access token 或 refresh token 。

如果撤銷成功，回應狀態碼 200 ；撤銷不成功，則回應狀態碼 400 。

# 叫用 Google API

在 Google 眾多的服務中，若要透過 API 存取資源，如果只是讀取較一般性的資料，通常無需授權，或者只需要使用 ApiKey 即可。  只有存取較隱私性或者需要更新到資料時，才會要求一定要使用 access token 。  不過，實際上的要求還是要參考該 API 的規定，底下這個例子，我們示範如何叫用 Google Blogger API 以取得該使用者的所有 Blog 清單。  參考文件：[Retrieving a user's blogs](https://developers.google.com/blogger/docs/3.0/using#RetrievingAUsersBlogs)  

例如取得使用者 Blog 清單的文件記載：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgn9A3y3oZhZ0C6MCIIeFHTgUgKWBs5OoFxXbmJOprfFl51QsQRQ4KBjCPaXHHuQ-LZI8r10UtA0qB43_uDkY3fNDzi3CIJm2Pyazob_VdI9ZZ6uq_koWCVvtx4bKNIEMha8aHzTIVSCio/s837/google-oauth-web-03.png)

該需求中說明這個取得 blog 清單的 API 端點位址，在送出去的 request 中，也必須在 Header 中提供 Authorization 資訊，內容就是 access token 的值。  另外送給 Google 的 Authorization 資訊，都必須標註它是一個 **Bearer** 型式的 Token 。  
```c#
string uri = "https://www.googleapis.com/blogger/v3/users/self/blogs";
string result = "";

HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
request.Method = "GET";
request.Headers.Add(HttpRequestHeader.AcceptLanguage, "zh-tw");
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + access_token);

using (var response = request.GetResponse())
{
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
result = sr.ReadToEnd();
}
}

BlogList bloglist = JsonConvert.DeserializeAnonymousType(result, new BlogList());
foreach (var blog in bloglist.Items)
{
Response.Write(blog.Name + "</br>");
}
```
## 參考資料  

- [Using OAuth 2.0 for Web Server Applications](https://developers.google.com/identity/protocols/OAuth2WebServer)
- [Blogger API: Using the API](https://developers.google.com/blogger/docs/3.0/using#RetrievingAUsersBlogs)
- [使用 OAuth 2.0 整合 Google、Facebok、WindowsLive、Xuite 登入](http://www.dotblogs.com.tw/joysdw12/archive/2012/10/05/75287.aspx)