---
title: Picasa API
layout: default
parent: Google
nav_order: 4
description: "Picasa API"
date: 2015-04-08
tags: [API,Google]
postid: "2113411182182017950"
---
Google 的 Picasa Web Albums 是一個網路相簿功能的服務，你可以透過 Picasa Web Albums Data API 類別庫來存取裡頭的服務內容，例如建立相簿，上傳相片等等的操作。  

# 類別庫下載

要存取 Picasa API ，可以下載 Google 提供的 Google Data API Photos Library 來協助處理。  

(1) 透過 NuGet 下載 Google Data API Client Library 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrSVhGtoi0Wjx3FHSu2yYJVaoKk0k7blyX8OFZSTo1QHiBe0pnKSl7CeYcXTh4lHnpvgTXSIimMglu0oMDvhDZQc-COsXG2Pfd-tb4zSRbKoCzG2on-Q00VlNJlhD_G-TpQqjgXYspRTQ/s642/GData-API-Client-Library.png)

(2) 透過 NuGet 下載 Google Data API Photos Library 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjobgJkhN-geZoEUPylm69jHgXHj0OcXqbwSo9d-ayNx_iPsfY1j2JchlYd9huRtpSZE9eTmcEaTo8rcmtIGKbVP-DdFcddTBYMbSdaLt0pjfT0WfUKRuLYt03sYSGp16KNEYO3f6I7sQo/s664/GData-API-Photos-Library.png)

# 存取公開的資訊

##### 顯示設定

一般來說在 Picasa 中的相簿，可以設定以下三種等級的分享：（照片本身無法單獨設定分享）

1. 公開
2. 僅限知道連結者
3. 只有你

當透過 Picasa Api 存取服務時，依據使用者對其個人資料的分享狀態，可區分成「公開資訊」和「非公開資訊」二類。  如果 APP 要讀取的是 Picasa 相簿中的公開資訊，則不需要任何授權，也不需要提供 ApiKey 就可以直接讀取。  若要讀取的是非公開資訊或是修改資料，則必須先取得授權。  底下示範幾個讀取公開資訊的方法：  

### 取得相簿列表
```c#
//建立 PicasaService
PicasaService service = new PicasaService("MyPicasaService");

//使用 userid 建立一個 Uri , 這個 API 可以回傳使用者的(公開)相簿列表
string userid = txtUserID.Text;
string uri = PicasaQuery.CreatePicasaUri(userid);

//建立 request
AlbumQuery query = new AlbumQuery(uri);
query.Access = PicasaQuery.AccessLevel.AccessPublic;

//取得執行結果
PicasaFeed feed = service.Query(query);

//列舉回傳的資料
StringBuilder sb = new StringBuilder();
int count = 0;
foreach (AtomEntry entry in feed.Entries)
{
Album album = new Album();
album.AtomEntry = entry;
sb.AppendLine(string.Format("{0}) AlbumID={1}  AlbumTitle={2}  NumPhotos={3}  Access={4} Uri={5}",
++count, album.Id, album.Title, album.NumPhotos, album.Access, album.AtomEntry.AlternateUri));
}
txtList.Text = sb.ToString();
```

PS.   若使用者將相簿設定成非公開，那麼該相簿就不會包含在上述程式碼的回傳值中。  若要取得非公開的相簿，則必須先經過授權。

### 取得相片列表
```c#
//建立 PicasaService
PicasaService service = new PicasaService("MyPicasaService");

//使用 userid + albumid 建立一個 Uri , 這個 API 可以回傳使用者的(公開)相簿列表
string userid = txtUserID.Text;
string albumid = txtAlbumId.Text;
string uri = PicasaQuery.CreatePicasaUri(userid, albumid);

//建立 request
PhotoQuery query = new PhotoQuery(uri);

//取得執行結果
PicasaFeed feed = service.Query(query);

//列舉回傳的資料
StringBuilder sb = new StringBuilder();
int count = 0;
foreach (AtomEntry entry in feed.Entries)
{
Photo photo = new Photo();
photo.AtomEntry = entry;
sb.AppendLine(string.Format("{0}) PhotoID={1}  PhotoTitle={2}  PhotoSummy={3}  Uri={4}",
++count, photo.Id, photo.Title, photo.Summary, photo.PhotoUri));
}
txtList.Text = sb.ToString();
```

# 存取非公開的資訊

如果要存取 Picasa 裡頭屬於**非公開**的資訊，則應用程式必須先取得使用者的授權後才可以存取。  目前 Picasa 所提供的 API ，最新的版本為 V2 ，且該版文件中有提到，建議使用 OAuth2 方式來取得授權。  因為不曉得如何將 Token 與 Picasa 類別庫結合，所以底下範例是以「用戶端登入」的方式取得授權，  也就是用戶端執行時必須先提供使用者帳號與密碼才能存取非公開的資料。  

### 建立相簿
```c#
string username = txtUserName.Text;
string password = txtPassword.Text;
string userid = txtUserID.Text;
string title = txtAlbumTitle.Text;
string summary = txtAlbumSummary.Text;
string visibility = "private";  

//建立 PicasaService
PicasaService service = new PicasaService("MyPicasaService");
service.setUserCredentials(username, password);

//建立 AlbumEntry
Album album = new Album();
album.Title = title;
album.Summary = summary;
album.Access = visibility;

//取得 API 的 Uri
string uri = PicasaQuery.CreatePicasaUri(userid);

//建立 request
AlbumQuery query = new AlbumQuery(uri);

//取得執行結果
AtomEntry result = service.Insert(uri, album.AtomEntry);
Album new_album = new Album();
new_album.AtomEntry = result;

//列舉回傳的資料
txtList.Text = string.Format("AlbumID={0}  AlbumTitle={1}  NumPhotos={2}  Access={3} Uri={4}",
new_album.Id, new_album.Title, new_album.NumPhotos, new_album.Access, new_album.AtomEntry.AlternateUri);
```

#### album.Access (Visibility)

這個屬性是相簿的公開範圍，可設定選項包含：

- protected : 只有你
- private : 僅限知道連結的使用者
- public : 公開的
-

### 上傳相片
```c#
string username = txtUserName.Text;
string password = txtPassword.Text;
string userid = txtUserID.Text;
string albumid = txtAlbumId.Text;
string photo_title = txtPhotoTitle.Text;
string photo_summary = txtPhotoSummary.Text;
string filename = txtPhotoFile.Text;

//建立 PicasaService
PicasaService service = new PicasaService("MyPicasaService");
service.setUserCredentials(username, password);

//取得 API 的 Uri
string uri = PicasaQuery.CreatePicasaUri(userid, albumid);

//建立 File Stream
FileInfo fileInfo = new FileInfo(filename);
FileStream stream = fileInfo.OpenRead();
string extname = fileInfo.Extension.Substring(1);
string contentType = "image/" + extname;   // image/jpeg, image/png ...
string slugHeader = photo_title;

//取得執行結果
AtomEntry result = service.Insert(new Uri(uri), stream, contentType, slugHeader);

//列舉上傳的資料
Photo photo = new Photo();
photo.AtomEntry = result;
txtList.Text = string.Format("PhotoID={0}  PhotoTitle={1}  PhotoSummy={2}  Uri={3}",
photo.Id, photo.Title, photo.Summary, photo.PhotoUri);
```

### 修改相片資訊
```c#
string username = txtUserName.Text;
string password = txtPassword.Text;
string userid = txtUserID.Text;
string albumid = txtAlbumId.Text;
string photoid = txtPhotoId.Text;
string photo_title = txtPhotoTitle.Text;
string photo_summary = txtPhotoSummary.Text;

//建立 PicasaService
PicasaService service = new PicasaService("MyPicasaService");
service.setUserCredentials(username, password);

//取得 API 的 Uri
string uri = string.Format(@"https://picasaweb.google.com/data/entry/api/user/{0}/albumid/{1}/photoid/{2}",
userid, albumid, photoid);

//建立 Request
PhotoQuery query = new PhotoQuery(uri);

//查詢 Photo 
PicasaFeed feed = service.Query(query);

//變更 Photo
PicasaEntry entry = (PicasaEntry)feed.Entries.First();
entry.Title.Text = photo_title;
entry.Summary.Text = photo_summary;
AtomEntry result = entry.Update();

//列舉上傳的資料
Photo photo = new Photo();
photo.AtomEntry = result;
txtList.Text = string.Format("PhotoID={0}  PhotoTitle={1}  PhotoSummy={2}  Uri={3}",
photo.Id, photo.Title, photo.Summary, photo.PhotoUri);
```

# 解決 Invalid Credentials 錯誤

[Picasa Web Albums Data API](https://developers.google.com/picasa-web/docs/1.0/developers_guide_dotnet) 是 Google 提供的一組用來存取 Picasa 相簿照片的 API 。  在用戶端程式中，使用 [Picasa Web Albums Data API](https://developers.google.com/picasa-web/docs/1.0/developers_guide_dotnet) 存取 google 帳戶時，為了安全性，預設這功能是被關閉的。  如果你收到＂ Invalid Credentials ＂錯誤訊息，你可以在帳戶的設定中，**啟用**「安全性較低的應用程式存取權限」（allow less secure apps to access your account）。  

圖一：「檢查安全性較低的應用程式存取權限」是否「已允許」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiLrhd24iNebPHB1P38RJJZH0zB5YMt7088bkjZF2hD631Z4cgPQFaDLhhwekFLCTNaQjgZjSnjasEtIjZ7L7AqcQ2HpCMifS8PwPRFeeDN8EglXL_N1Vojj25YvR-_lLnsRsdDgoCgBPU/s702/invalid-credentials-1.png)

圖一：若尚未啟用，請開啟這個功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKD_uOLiSBQLtikv_nPUlhEd11_dvlYjwoWpOEfZyxOq3noTFqdimPxie5T8I3zdo6rRTxodNviKlsuEMKmZVclLEBNVYwTCTYcZhOy2R7xwoUIj2zTI8ICNYmEc3mPxi693taXkd38M4/s770/invalid-credentials-2.png)

你可以開啟底下連結，直接進入這個功能設定。
[https://www.google.com/settings/security/lesssecureapps](https://www.google.com/settings/security/lesssecureapps)
## 參考資料  

- [Picasa Web Albums Data API](https://developers.google.com/picasa-web/)
- 
-