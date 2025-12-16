---
title: 在已安裝應用程式中取得 Google OAuth 2.0 授權
layout: default
parent: Google
nav_order: 5
description: "在已安裝應用程式中取得 Google OAuth 2.0 授權"
date: 2015-04-08
tags: [API,Google]
postid: "1753757905767728207"
---
在前篇文章中簡單介紹如何在網站應用程式中取得 Google OAuth 2.0 授權，  本篇文章要說明的是如何在「已安裝應用程式中取得 Google OAuth 2.0 授權」。  這二者的授權流程是相似的，比較相異的有以下三點：  

- 申請的憑證類型不同，必須是「已安裝應用程式」類型。
- 憑證中的 Client ID, Client Secret 是嵌在程式碼之中的。
- OAuth Server 回傳的 Authorization Code 是直接放在應用程式的 Title bar 或者 http://localhost 網址的參數中。

另外要注意的是，若要使用「已安裝應用程式」類型，你的系統中必須含有預設的瀏灠器，並且該應用程式有權限存取該瀏灠器。  

# 申請「已安裝應用程式」憑證

存取 Google API 必須先至 [Google Developers Console](https://console.developers.google.com) 申請應用程式憑證。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZrVF6ivVolwyAA_dGFm-ZammMlG8EuyE1sApdHnGAulIw7OTXp-Cv_rirs6rmkEw8RH9Ktgh3H0dBfFXvE-R4knK8IMGRFKoGErjhHyRvfEcWkzhndyjGXc5WTG4NtObbV18rJ2cmMQs/s680/BloggerAPI-10.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiD_wYcJLm9Dn1n8Hpi0jTLaHwvi76rzfaZblHNkhoa6s6fuavsHeZxMGbz5-Mo1z0HExWa3NoXCtPcRjjCqtOREjz4xguOsuCu4zfoieGMkfLvPo1cwYxIKLJu-OC2fjXIhrWLFKEQemI/s486/BloggerAPI-03.png)

# 取得 Access Token

若是要在已安裝的應用程式中取得存取憑證，做法也可以參考上一篇＜[在網站應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/aspnet-google-oauth2.html)＞的作業流程，以取得存取憑證。  只不過這麼一來，就必須透過 Browser 元件自行處理回應的訊息。  另外較方便的方式就是下載 Google 提供的「用戶端類別庫」，可以在已安裝的應用程式中很方便的取得 OAuth 2.0 的存取憑證。  

## 類別庫下載

- Google APIs OAuth Client Library
- Google APIs Client Library
- Google APIs Core Library

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjAZo0Uj0ly_4rQhg2oq39FyN6XrGFi5EEkrZX6SPTOrGwQNfN02vFMfv97H2fB9Rdqn1LnqqNlABHPwQfgIrDiVVllI9pjE-Sj9dneLJW9JP6z28CoWKpdLtgc5-1a9wq9y2LedV3Klhk/s850/google-web-client-01.png)

## UserCredential 和 AuthorizationCodeFlow

Google API OAuth 類別庫中，它透過 [GoogleWebAuthorizationBroker.AuthorizeAsync](http://contrib.google-api-dotnet-client.googlecode.com/hg/1.9.0/documentation/classGoogle_1_1Apis_1_1Auth_1_1OAuth2_1_1GoogleWebAuthorizationBroker.html#a95045cb55eeb08fadd27b33e0193327c) 靜態方法來建立 [UserCredential](http://contrib.google-api-dotnet-client.googlecode.com/hg/1.9.1/documentation/classGoogle_1_1Apis_1_1Auth_1_1OAuth2_1_1UserCredential.html) 類別，以便向 Google 服務取得 access token 。  UserCredential 是類別庫中，用來記錄 access token 的物件，並且可以在憑證過期時自動 refresh token 。  而 GoogleWebAuthorizationBroker 則是一個 Code Flow 類型的物件，也就是它在執行時其實是包含多個步驟，它會依實際狀況，自動執行必要的步驟。  例如，如果程式找不到本機端儲存的 access token ，則會開啟使用者 consent 頁面，等待使用者授權程式存取他的資料。  如果使用者按下了同意按鈕，應用程式將自動取得 access token ，並將值保存下來，留待下一次執行時使用。  如果該 access token 已過有效期限已，就會自動送出 refresh token 以便更新 access token 。  這一連串的步驟 Google OAuth2 API 都已經包裝起來，我們只要照以下程式碼的設定即可。  

下面這個例子，Broker 會將憑證資料儲存在底下這個檔案中：  "%AppData%\Roaming\Google.Apis.Auth\Google.Apis.Auth.OAuth2.Responses.TokenResponse-user"。  
```c#
public UserCredential GetCredential()
{
UserCredential credential = GoogleWebAuthorizationBroker.AuthorizeAsync(
new ClientSecrets
{
ClientId = "XXXX.apps.googleusercontent.com",
ClientSecret = "et-OimYoEOwuhQgrFaoNInwv",
},
new[] { "https://www.googleapis.com/auth/blogger" },
"user",
CancellationToken.None
).Result;

return credential;
}
```

底下簡單說明建立 UserCredential 物件時，會使用到的參數值：   

### ClientSecrets

在使用 Blogger API 時，你必須先替應用程式申請一組憑證（申請方法請看＜[Google OAuth 2.0](http://vito-note.blogspot.com/2015/04/google-oauth-20.html)＞），再使用核發憑證中的 ClientID 和 Secret 建立 ClientSecrets 。  當 request 無法獲得有效的 access token 時，則必須使用這組資訊，重新建立 access token 。  另外，你也可以將當初向 Google 申請的憑證下載回來，然後在建立 ClientSecrets 時，直接使用開啟檔案的方法，匯入憑證。例如：  
```c#
public UserCredential GetCredential()
{
UserCredential credential;
using (var stream = new FileStream(@"d:\mydll\client_secrets.json", FileMode.Open, FileAccess.Read))
{
credential = GoogleWebAuthorizationBroker.AuthorizeAsync(
GoogleClientSecrets.Load(stream).Secrets,
new[] { "https://www.googleapis.com/auth/blogger" },
"userxxx",
CancellationToken.None,
new FileDataStore("Auth.Blogger")).Result;
}

return credential;
}
```

若要下載已申請的憑證，你可以到 [Google Console](https://console.developers.google.com/)  網站中找到以下連結。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEN5UXjHZT-AyLoO7o7R9PM7TDbrOvItYtRBehCMzPG9DP3tdRRy4Y3b-zJIbQfojFN6kEQVfEQ0FAOGbun4UCX7SDHRNYmAIvBy9TypYD_i8o2nH2MKnWQj-XE7JeaSWeVrjMwiv948M/s758/blogger-api-03.png)

### Scope

[AuthorizeAsync](http://contrib.google-api-dotnet-client.googlecode.com/hg/1.9.0/documentation/classGoogle_1_1Apis_1_1Auth_1_1OAuth2_1_1GoogleWebAuthorizationBroker.html#a95045cb55eeb08fadd27b33e0193327c) 這個方法中的 scope 指的是授權內容的範圍。  在 Google 的 [OAuth](http://zh.wikipedia.org/wiki/OAuth) 實作中，定義了很多 scope ，例如：Blogger、Picasa、Google Map 等等，  當程式開發時，就利用這個參數來告訴 Google 開啟相關的授權資訊讓使用者 consent 。  

例如，若 scope = BloggerService.Scope.Blogger ，授權頁的內容如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwyZK3uVAPHP6i-FYBMnsNmx5c9gyMdkVSnRZQYp_DoFoBW6pb3bSQApWgs-au_9BbPRVkC2FEwtWcNsIfubd3l_HHzMwzzNidgmTze8umf19h797diF3Jo4YW_oxXDVCCqw8gf-RV2Os/s467/blogger-api-01.png)

例如，若 scope = BloggerService.Scope.**BloggerReadonly** ，則授權頁的內容如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDvjtq5OHTVVnKmvRUO6PZC2k55vCzXPL3u874YP_7HuCv4i14uXxz3SpD8BGg8XqOmbOuP4WqC90cZt7UxwE6xyKcNecKyL85tlcfVjPdZjYg2lRAa69KS0GNQPHr0cG6KEafF9k1e9g/s477/blogger-api-02.png)

### user\_account

第3個參數，只是用來識別不同的使用者，如果這個用戶端程式只有一個人使用，那麼使用任何一個固定的字串也是可以的。

### CancellationToken

這個參數是用來設定授權作業時是否可以被中斷。

### FileDataStore

第5個參數是用來指定存放 access token 的目錄，你可以指定一個 File Store 或者 Data Store 物件當做參數值。  這個參數非必要欄位，如果沒有指定，則會自動儲存在預設的位置。  預設的位置會使用 "Google.Apis.Auth" 當做參數值，然後將這個目錄建立在 Environment.SpecialFolder.ApplicationData 之中。  所以上面例子中，Broker 會由 d:\mydll\client\_secrets.json 讀取**用戶端憑證**資料，並將使用者授權後的**存取憑證**資料儲存在底下這個檔案中：  "%AppData%\Roaming\Auth.Blogger\Google.Apis.Auth.OAuth2.Responses.TokenResponse-userxxx"。

## 更新與撤銷存取憑證

當應用程式取得使用者的 OAuth 授權後，若需要更新或撤銷憑證，也可以直接叫用 UserCredential 的方法即可。  
```c#
/// 更新授權憑證
credential.RefreshTokenAsync(CancellationToken.None)

/// 撤銷授權憑證
credential.RevokeTokenAsync(CancellationToken.None)
```
## 參考資料  

- [API Client Library for .NET](https://developers.google.com/api-client-library/dotnet/guide/aaa_oauth)
- 
- 

&lt;!--相關文章--&gt;  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2Q1Ch0u6DdE7o3mJIqe9Q-tlW1K61LeLq7ZCdEAeAF3NC0R3zMj_CxYX_YlXVbOgBUmVX0U7tPO0ylNpH5PN3_JfocbUJ4-yQdOr_TB_dYYQ3UO5Fuf9CpKQVykMY9LOtKVOFiTiPWCA/s120/RelationPage.png)

- [Google OAuth 2.0](http://vito-note.blogspot.com/2015/04/google-oauth-20.html)
- [在網站應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/aspnet-google-oauth2.html)
- [在已安裝應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/invalid-credentials.html)
- [Google Blogger API](http://vito-note.blogspot.com/2015/04/google-blogger-api.html)
- [Google Drive API](http://vito-note.blogspot.com/2015/04/google-drive-api.html)
- [Picasa API](http://vito-note.blogspot.com/2015/04/picasa-api.html)
- [Picasa Web Album with OAuth2](http://vito-note.blogspot.com/2015/06/picasa-web-album-with-oauth2.html)