---
title: Google OAuth 2.0
layout: default
parent: Google
nav_order: 1
description: "Google OAuth 2.0"
date: 2015-04-08
tags: [API,Google]
postid: "6666047310851175999"
---
「**開放授權**（[OAuth](http://zh.wikipedia.org/wiki/OAuth)）」已經成為一種標準協定，其目的是要讓**使用者可以授權第三方軟體可以存取其儲存在另外服務提供者的某些特定資訊**。  「**第三方**」可以是一個網站程式，也可以是安裝程式（如桌機程式、手機程式），或者是一個 javascript 程式，簡稱「Client」。  詳細的 OAuth 2.0 定義可參考[RFC6749](https://tools.ietf.org/html/rfc6749)。  

例如：  若一個 APP 要存取你存放在 Google Blogger 中非公開的資訊，則這個 APP 必須要先獲得你「同意授權」，就可以存取這些非公開的資訊，而不需要你提供帳號密碼給這個 APP 知道。  因此開放授權比傳統的授權方式，更具以下優勢：

- 使用者不需告訴應用程式密碼。
- 使用者可以細分授權項目。
- 使用者可隨時撤消授權。

# Google OAuth 2.0

[OAuth](http://zh.wikipedia.org/wiki/OAuth)  即然是標準協定，Google OAuth 2.0 自然也遵守這樣子的協定。  在 Google 的開放授權規範中，它要求應用程式送給 Google 的每一項要求，不管存取的是公開或非公開資訊，都必須指明應用程式的身分，所以在使用較新版本的 Google API 之前，必須先替你的應用程式申請憑證。  

- 申請網址：[Google Developers Console](https://console.developers.google.com/)。
- 申請步驟：請參考下文介紹。

## Google OAuth 2.0 授權流程

底下是 Google OAuth 2.0 針對已安裝應用程式（Installed App）的授權流程圖。  若是針對網站應用程式，其流程大同小異，只是處理方式不太相同而已。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNt0dVwVR7Ht1CglrMSXUoQ5Ur55LDwL9W5xFwQ3KK6zEAwf7a_InoIRoczK_PCJPhhv_xRUzalJ594QAkvSLC6GgoS7GEIkd_IUcSpFGn7oeUywV1g0IQhZWnr_hIAvNs792SVGHFIBU/s387/google-api-flow-for-installed.png)

流程圖說明：

當你的應用程式要存取 Google 服務之前，你必須先替你的應用程式申請一組憑證。  申請憑證完成時，你會取得一組**用戶端ID**（ClientID）和**用戶端密碼**（ClientSecret），  這個憑證資訊要妥善保存，在上面的流程圖中的 Request Token 和 Exchange Code 步驟都必須使用到這個資訊。  

當應用程式要存取 Google API 時，它必須先獲得使用者的同意授權，若使用者同意授權，Google Server 就會回傳一個授權碼(Code)給應用程式，  應程程式再根據這個授權碼還有自已的識別憑證與 Google Server 交換存取憑證（Token），接下來應用程式才可以使用這個存取憑證（Token）去存取經過使用者授權的資訊。  

下圖就是一個典型的授權畫面，這個步驟稱為「同意」（consent）。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjF0ntJkwNp-KNsw8kZPyGGZCrprAwuqJPVnK396_menNUWxibhxhxPj-ZG-ktj73wG8w9S8cTlluoG5PdCuWG0MibJVnZ1SUBIfSOXNp2fNN_BVh68lpP9LFb1yNXrFuK8EXQ47OFyjWg/s521/google-api-consent.png)

當使用者同意授權 APP 存取權限時，Google OAuth Server 就會回傳一組授權碼給 APP ，APP 可以利用這個授權碼再跟 Google OAuth Server 交換存取憑證，  底下資訊就是 Google 服務所回傳的一組憑證內容，上面記載著 access token 、 refresh token 以及憑證有效期限等相關資訊。  
{
"access_token":"ya29.OAFiRRbMV732FcMgaR7i8MPjmrFD5Sq22uoKQwX0FuQzXJ4kgLqmt4_Ntf0Afo0WZYC5JH4qMdpXPw",
"token_type":"Bearer",
"expires_in":3600,  		/* 1 hour */
"refresh_token":"1/QfKVG5NFKc4CbV85jkQ6t-cEZFsjhfqrvO1bnbWdHy4",
"Issued":"2015-03-16T13:40:56.776+08:00"
}

## Access Token 與 Refresh Token

當程式在 consent 頁面取得使用者授權憑證時，該憑證中會包含 access token 、 refresh token 。  當程式要透過 API 存取資源時，必須在送出去的 request 中包含 access token 已資識別。  這個憑證是有期限的，而且為了安全起見，它的有效期限通常也不會太長，在 Google OAuth 2.0 中的預設值為一小時（3600秒）。  當 access token 過了有效期限時，應用程式可以用 refresh token 對 OAuth Server 要求新的 access token 。  整個 API 運作時的流程大至如下：  

- send API request with access token
- If access token is invalid, try to update it using refresh token
- if refresh request passes, update the access token and re-send the initial API request
- If refresh request fails, ask user to re-authenticate

## 為什麼要同時使用 Access Token 與 Refresh Token

access token 通常生命期較短，萬一遭攔截或洩露時，可以減輕被駭客拿來濫用的情況。  而 refresh token （更新憑證）, 萬一遭攔截或洩露，不會有什麼做用，因為駭客必須同時取得 client id 和 secret 才能重新獲取 access token。  所以 refresh token 可以避免使用長生命週期 access token 可能造成的風險。  

而且，當 access token 失效時，用戶端只要根據原有的 refresh token ，不需要再透過使用者，就可以重新取得新的 access token ，而不用打擾到使用者。

## 授權規定

前面提過，公開授權（OAuth）是針對應用程式授權，讓這個 APP 有權限去存取 Google 服務中所提供的資訊。  但也不是所有的資訊都需要經過授權，像是一篇部落格文章，若是其屬性設定為公開，那麼 APP 就可以不經使用者授權而直接讀取。  所以在 Google 眾多的服務中，每種服務的授權規定都不盡相同，大至可區分以下幾類：  

- 完全開放：該服務中的所有內容，完全開放讓 APP 自由存取。
- 完全不開放：該服務中的所有內容，APP 都必須先取得使用者的同意（consent）才可以存取。
- 部份開放：該服務中的所有內容，若是屬於公開性質的，則 APP 僅需提供 ApiKey 就可以存取；若是非公開性質的，則必須先取得使用者的同意（consent）才可以存取。
- 

當應用程式存取服務時，若遇到非公開性質的設定，就必須先取得使用者的同意（consent）才能獲得存取權限，所以在存取 Google API 前，必須先了解各 API 的授權需求或使用方法，而這些資訊都可以在 [Google 線上 API 文件](https://developers.google.com/apis-explorer/#p/) 中找到相關說明。

# 申請應用程式憑證

Google 要求應用程式送出來的每一個 request 中都要包含可以用來識別這個應用程式的資訊，而且如果這個 request 是要存取非公開的資訊，它還必須要提供經過使用者同意授權後的 access token 。  所以，在應用程式開始存取 Google API 之前，就必須先向 Google 申請一個應用程式的用戶端憑證。  

在 Google 的申請頁面中有二種應用程式識別憑證：

- **應用程式的用戶端（OAuth）**：這種用戶端憑證可用來讓 Google 識別應用程式，以便取得使用者的 OAuth 授權，籍以存取使用者分享的資訊。
- **應用程式的金鑰 （API key）**：有些使用者分享的資訊，Google並沒有要求一定先經過授權才能存取，但會要求要提供這個金鑰，以便識別存取的應用程式。

憑證申請的網址為： [https://console.developers.google.com/](https://console.developers.google.com/) 網站，進入網站後，你必須先建立一個專案，然後依下列步驟申請憑證。  

## 1.建立專案

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6fle_4Jt17Vd_-xIjlaO1aJizBOzvmXmw-Y97u8fdXxCloX0jdr880qmWAFy8pAsaTdngQ30PVopt-JdcF-WTeN2JMZ9sv-wx7q4PItbhfCEfHIti0SlLduAOhK-q4X024aeIH31xO4Y/s524/BloggerAPI-01.png)

## 2.申請應用程式憑證

申請憑證頁面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZrVF6ivVolwyAA_dGFm-ZammMlG8EuyE1sApdHnGAulIw7OTXp-Cv_rirs6rmkEw8RH9Ktgh3H0dBfFXvE-R4knK8IMGRFKoGErjhHyRvfEcWkzhndyjGXc5WTG4NtObbV18rJ2cmMQs/s680/BloggerAPI-10.png)

### 2.1 建立新的用戶端ID

這個設定主要是向 Google 申請一組給 APP 使用的「用戶端ID」和「用戶端密碼」。你可以依你的應用程式類型，申請適當的用戶端類型。  

這個「應用程式類型」指的是你希望 Google 以什麼方式回應使用者同意授權。  若是選擇「Web applications」，則同意授權碼會傳到指定的 **redirect URIs**，程式必須在這個端點位置撰寫程式碼去取得使用同意授權碼，以便交換 Token 。  若是選擇「Installed applications」，則同意授權碼會直接回傳到原先 consent 頁面的標題列或原本瀏灠器的網址列中，所以可以在回應的內容中直接取得授權碼，以便交換 Token。  若是選擇「Service Account」，則是適合該應用程式執行時不需要使用者授權的作業。  

**「已安裝的應用程式」用戶端類型**

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiD_wYcJLm9Dn1n8Hpi0jTLaHwvi76rzfaZblHNkhoa6s6fuavsHeZxMGbz5-Mo1z0HExWa3NoXCtPcRjjCqtOREjz4xguOsuCu4zfoieGMkfLvPo1cwYxIKLJu-OC2fjXIhrWLFKEQemI/s486/BloggerAPI-03.png)

**「網路應用程式」用戶端類型**

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzakj0UMIvx95VlGqnwAtltZD7wYg6yqmsR2a1bDJV1YUcQ0b900uVC-Umtk4PFbBI6QbEhV-CJTVz-YGNyS2c-ZaOgS6xgPMSfYz0x6SB7f7ZCojo8BrL9UXC3eMKSFJ8549lrbLjIUc/s489/BloggerAPI-11.png)

### 2.2 建立應用程式金鑰

當應用程式去存取 Google 服務時，有些服務 Google 並不要求使用者授權就允許應用程式直接存取，但會要求必須應用程式必須提供「APIKey」，主要目的是讓 Google 服務可以識別哪個 APP 正在存取它的資料，以便監督或進行流量控管等。  如果你應用程式要存取這類 API ，就必須申請一個應用程式金鑰。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJSNFy5RfEWzqGl_34CnLm5pspd7uZfC0fZPsUTOvUiw88ixus5H196zaKH2ul4MwzRaplyfe73cmKnIZXzVNGw9msqBrRswfN6mpt61yrZRfR25P0hvzTnzxi5i9aSijMHM2KkTgINTY/s525/BloggerAPI-04.png)

## 3.完成畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEji509fzj-3k5MO-x2uiylnlIUeQVzDrqbJMxbea6AwXixW26iRNAyZhhXWFD0xrKJyrrlWSh2yLHYTQGe5M6UNHQCclQ6b8qI_WWD7Yy-Q8bSbV_MjUVAjMzs0OQJyJdTgyNKgMfjd3eo/s850/BloggerAPI-05.png)

# 如何取得公開授權（OAuth）

申請應用程式的憑證後，接下來就可以開始撰寫程式，在這個文章中，只說明如何取得 OAuth2.0 的 Access Token ，  至於如何將這個存取憑證套用到 Google 服務的存取，則另文討論。  

## 如何取得使用者的同意（consent）

要取得使用者的同意，可以利用 Google APIs OAuth2 Client Library 來協助建立，這時候就必須使用到前面替應用程式申請憑證時，所取得的「用戶端ID」和「用戶端密碼」。  範例程式碼如下：  
```c#
using Google.Apis.Auth.OAuth2;      // Oauth2 會用到
using Google.Apis.Util.Store;       // FileDataStore 會用到

private void btnOAuth_Click(object sender, EventArgs e)
{
UserCredential credential;
credential = GoogleWebAuthorizationBroker.AuthorizeAsync(
new ClientSecrets
{
ClientId = myClientID,
ClientSecret = myClientSecret,
},
new[] { BloggerService.Scope.Blogger },
user_account,                               //如果只有一人, 可以使用任何固定字串, 例如: "user"
CancellationToken.None,
new FileDataStore("Blogger.Auth.Store")     //用來儲存 Token 的目錄
).Result;

// 將取得的 token ，顯示出來檢示一下。
txtFileName.Text = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), @"Blogger.Auth.Store\Google.Apis.Auth.OAuth2.Responses.TokenResponse-user");
txtAccessToken.Text = credential.Token.AccessToken;
txtRefreshToken.Text = credential.Token.RefreshToken;
txtIssued.Text = credential.Token.Issued.ToString();
txtExpiresIn.Text = credential.Token.ExpiresInSeconds.ToString();
}
```

當上面程式執行時，就會開啟以下畫面，要求使用者同意（consent）該 APP 去存取他的資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjF0ntJkwNp-KNsw8kZPyGGZCrprAwuqJPVnK396_menNUWxibhxhxPj-ZG-ktj73wG8w9S8cTlluoG5PdCuWG0MibJVnZ1SUBIfSOXNp2fNN_BVh68lpP9LFb1yNXrFuK8EXQ47OFyjWg/s521/google-api-consent.png)

### 程式說明

上面程式碼片段中，當叫用 AuthorizeAsync() 方法建立一個 UserCredential 物件時，幾個參數說明如下：  

1. clientSecrets：設定 APP 的 Client ID 和 Client Secret 。
2. scopes：設定這個 APP 要求使用到授權的存取範圍，每一組 API 使用一個字串表示。
3. user：這個參數主要是讓 APP 用來識別不同的使用者。
4. taskCancellationToken：設定在取得識別期間，是否可被取消作業。
5. dataStore：  這個參數使用 FileDataStore 來指定一個儲存 token 的目錄，它會建立在 "C:\Users\{user}\AppData\Roaming\" 目錄中。  如果不指定，預設會使用 "Google.Apis.Auth" 。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlkff2eGUDvPXZkMJV0jBAHHZhW_El8poFXkjTtHhwgnF1KfRSlRdsWLZEB0GKMbzURRZdqEkpYq-gMckMH6YuJ7AoBDlHfXRwhR-48EV6qLIKln9FajI1GtAgIx0NrVI2TgzIOyLGNaI/s638/google-api-store-dir.png)

## 如何設定使用者同意畫面中的標題

要變更上圖中的應用程式標題，你可以到 Google Developers Console 中，在「同意畫面」選單功能中找到。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGPolwcn0E1qbVSGuMvRexdkLJsblr-beb5edXLkCddz0IZSUFQauc2DIrIq8LSgpsisHGQJVo86p59rDMWfFWT6m7aJbqN0GZywumF4juSeItk5An7ocmtZMY8JVFdm1UW9bJUEV6V7Q/s780/google-api-consent2.png)

## 如何取得存取憑證

如果你的應用程式是網站程式類型，請參考＜[在網站應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/aspnet-google-oauth2.html)＞。  如果是桌面程式或手機程式這一類型，請參考＜[在已安裝應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/invalid-credentials.html)＞。

## 如何撤銷授權

要撤銷授權也很簡單，只要叫用 [UserCredential.RevokeTokenAsync()](http://contrib.google-api-dotnet-client.googlecode.com/hg/1.9.0/documentation/classGoogle_1_1Apis_1_1Auth_1_1OAuth2_1_1UserCredential.html#ab4bca8df1cbe91326492c859d1121c95) 方法即可。  它除了會撤銷原先授予的存耶憑證，也會將原先的憑證資料由 data store 中刪除。  所以當叫用 [RevokeTokenAsync()](http://contrib.google-api-dotnet-client.googlecode.com/hg/1.9.0/documentation/classGoogle_1_1Apis_1_1Auth_1_1OAuth2_1_1UserCredential.html#ab4bca8df1cbe91326492c859d1121c95) 之後，若要再次取得該使用者的存取授權，就必須再執行 consent 一次。  
```c#
try
{
MyBloggerService myBlogService = new MyBloggerService();
myBlogService.SetCredential(gstrAppName, gstrUserEmail);

myBlogService.RevokeCredential(CancellationToken.None);
MessageBox.Show("Credential Revoked");
}
catch (Exception ex)
{
MessageBox.Show(ex.Message);
}
```
## 參考資料  

- [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/accounts/docs/OAuth2)
- [The OAuth 2.0 Authorization Protocol](http://tools.ietf.org/id/draft-ietf-oauth-v2-22.html)
- [Google OAuth 2.0 APIs for .NET](https://developers.google.com/api-client-library/dotnet/guide/aaa_oauth#installed_applications)
- [Using OAuth 2.0 for Installed Applications](https://developers.google.com/identity/protocols/OAuth2InstalledApp)
- [Blogger API: Using the API](https://developers.google.com/blogger/docs/3.0/getting_started)
- [Blogger API Reference](https://developers.google.com/blogger/docs/3.0/reference/index)
- [Blogger API v3 Explorer](https://developers.google.com/apis-explorer/#p/blogger/v3/)
- [Google OAuth2 C#](http://www.daimto.com/google-oauth2-csharp/#Loading_Stored_refresh-Token?utm_campaign=Stackflow_daimto_anwser&utm_source=daimto_anwser&utm_medium=Stackflow)