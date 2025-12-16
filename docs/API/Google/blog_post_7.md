---
title: Picasa Web Album with OAuth2
layout: default
parent: Google
nav_order: 7
description: "Picasa Web Album with OAuth2"
date: 2015-06-25
tags: [API,Google]
postid: "1289292476419891286"
---
Picasa Web Albums 原本是 Picasa 公司提供的網路相簿，後來被 Google 併了，隨後也將它與 Google+ Photos 整合在一起。  近日 Google 正式推出 [Google Photos](https://photos.google.com/) ，雖然是一個類似 Flickr 型態的服務，但底層架構還是同原先的 Picasa Web Album 或 Google+ Photos 。  

之前使用 Google GData Photos 類別庫存取 Picasa Web API，可以使用 ClientLogin 方式，但近日 Goolge 已經不再支援這個選項，所以只能透過 OAuth 2.0 或 Google+ Sing-In 的方式。  這篇文章就是要介紹如何透過 OAuth 授權方式，存取 Picasa Web Albums 。  另外要提到一點，Google 最近推出的 Google Photos 不知道會不會提供新的 API ，也沒相關說明，  目前如果要存取 Google 相簿，還是針對 Picasa Web Albums Data API Version 2.0 ，搭配的類別庫則是 Google Data API Client Library ，版本 2.2.0.0。  

# 類別庫下載

1. Google APIs Client Library
- Google APIs Client Library ：#1.9.1
- Google APIs Core Client Library ：#1.9.1
2. Google APIs Auth Client Library ：#1.9.1
3. Google Data API Client Library
- Google GData Photos ：#2.2.0.0
- Google GData Client ：#2.2.0.0
- Google GData Extensions ：#2.2.0.0

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga1qZz2hY7mbQTxtl4XS5iKksZKhuZWGJXLatfLsUI0V9j95_SPlh6ezzpi2kFs37t8uMA4N03evaG8p16gzm2vAFS0EchkMD7v7eXGMwroXyNDcjrK-V5eCz2oOGOME7ttCWKFrG7IWs/s583/picasa-v2-01.png)

# Installed Applications

[Google APIs Auth Client Library](https://www.nuget.org/packages/Google.Apis.Auth/) 是 Goolge 提供用來存取 OAuth 2.0 授權的類別庫，  針對 Google 所提供的每個服務，通常 Google 也都會提供相關存取類別庫，供開發者方便存取該項資源，而且二個類別庫通常會彼此整合，方便在存取資源時無須理會 OAuth2.0 授權的細節。  例如： Blogger API 或 Google Drive API 所提供的用戶端類別庫，都可以輕鬆的整合 OAuth2 授權憑證。  不過， 存取 Picasa 相簿的 API 所提供的用戶端類別庫並沒有與 Google APIs Auth Client Library 完全整合，所以 OAuth 部份雖然還是可以利用 Google APIs Auth Client Library 中的元件取得，但是**必須自行判斷 Token 的有效性，並且自行將 access-token 資訊帶入每一次送出的 request 之中**。  

## 建立憑證與更新憑證

Credential 的建立同先前介紹的，可以透過 Google Apis Auth Client Library 方便取得。  只不過，在取得 Credential 時，必須額外判斷舊有的授權憑證是否已經過期。  若是過期了，可叫用 RefreshTokenAsync 方法更新憑證。  
```c#
private UserCredential GetCredential(string tokenfolder, string[] scopes)
{
try
{
UserCredential credential;
string email = "vitoshao@gmail.com";

//Init ClientSecret
string clientSecretFile = @"D:\myDLL\client_secrets_installed.json";
ClientSecrets clientSecret = null;
using (var stream = new FileStream(clientSecretFile, FileMode.Open, FileAccess.Read))
{
clientSecret = GoogleClientSecrets.Load(stream).Secrets;
}

//Init Credential
credential = GoogleWebAuthorizationBroker.AuthorizeAsync(
clientSecret,
scopes,
email,
CancellationToken.None,
new FileDataStore(tokenfolder)
).Result;

// 判斷舊有的授權憑證是否已經過期,若過期則 RefreshToken
if (credential.Token.IsExpired(SystemClock.Default))
{
bool result = credential.RefreshTokenAsync(CancellationToken.None).Result;
}

return credential;
}
catch (Exception ex)
{
throw ex;
}
}
```

## 存取 Picasa 相簿

要存取 Picasa 相簿資料，可以透過定義在 Google.GData.Photos 命名空間中的 PicasaService 來協助處理。

### 存取公開資訊

如果存取的資料是屬於公開範圍，例如讀取公開的相簿，只要如下做法即可：
```c#
string userId = "112836917220567148331";

//建立 PicasaService
PicasaService service = new PicasaService("MyApplication");

//送出 Request
string uri = PicasaQuery.CreatePicasaUri(userId);
AlbumQuery album_query = new AlbumQuery(uri);
album_query.Access = PicasaQuery.AccessLevel.AccessPublic;
PicasaFeed feed = service.Query(album_query);

//輸出
foreach (AtomEntry entry in feed.Entries)
{
Album album = new Album();
album.AtomEntry = entry;
Console.WriteLine(album.Title);
}
```

### 存取非公開資訊

如果存取的資料是屬於**非**公開範圍，如讀取非公開的相簿，你必須先取得 OAuth Token ，並將該資料自行帶入每一次送出去的 request 之中。  
```c#
string[] scopes = { "https://picasaweb.google.com/data/" };
string userId = "112836917220567148331";

UserCredential credential = GetCredential("auth.picasa", scopes);
if (credential != null)
{
if (credential.Token.IsExpired(SystemClock.Default))
{
bool result = credential.RefreshTokenAsync(CancellationToken.None).Result;
}

//建立 PicasaService
PicasaService service = new PicasaService("MyApplication");

//設定 AccessToken 
var requestFactory = new GDataRequestFactory("MyApplication");
requestFactory.CustomHeaders.Add(string.Format("Authorization: Bearer {0}", credential.Token.AccessToken));
service.RequestFactory = requestFactory;

//送出 Request
string uri = PicasaQuery.CreatePicasaUri(userId);
AlbumQuery album_query = new AlbumQuery(uri);
album_query.Access = PicasaQuery.AccessLevel.AccessAll;
PicasaFeed feed = service.Query(album_query);

//輸出
foreach (AtomEntry entry in feed.Entries)
{
Album album = new Album();
album.AtomEntry = entry;
Console.WriteLine(album.Title);
}
}
```

# Web Applications

若要使用 ASP.NET 要存取 Google Picasa Web Album 的服務內容，如果申請的用戶端憑證是屬於 Installed Application 性質，那麼直接 ASP.NET 的 code behine 中直接使用上述做法，也就是利用 GoogleWebAuthorizationBroker 類別取得存取憑證。  如果申請的憑證是 Web applications 性質，你可以參考＜[在網站應用程式中取得 Google OAuth 2.0 授權](http://vito-note.blogspot.com/2015/04/aspnet-google-oauth2.html)＞這篇文章的做法，將取得的 Token 加入到 PicasaService.RequestFactory 屬性即可。