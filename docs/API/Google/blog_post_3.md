---
title: Google Blogger API
layout: default
parent: Google
nav_order: 3
description: "Google Blogger API"
date: 2015-04-08
tags: [API,Google]
postid: "5575924453099441178"
---
這篇文章主要介紹如何使用 Blogger API V3 存取 Google Blogger 服務，其中 Blogger API V3 正是 Google 提供的一組用來讓用戶端應用程式存取 Google Blogger 服務的 API。  你除了可以透過這組 API 對 Blogger 進行查詢外，也可以對 Blogger 中的內容進行 post, edit, delete 等操作。  詳細的 Blogger API 說明，請參考 [Blogger API Reference](https://developers.google.com/blogger/docs/3.0/reference/index) 文件。  此外，針對 Blogger API ， Google 也提供了一套 Google Apis Blogger V3 Client Library 類別庫，可用來協助處理存取 Blogger 的相關問題。  

在使用這個 API 之前，你必須先替你的應用程式申請一組 OAuth 2.0 憑證，並且向 Google 申請使用 Google API V3 ，相關的申請作業請參考[另一篇](http://vito-note.blogspot.com/2015/04/google-oauth-20.html)說明。  

# 申請應用程式憑證

Blogger API V3 規定，若應用程式要存取的是使用者公開的資訊，則必須在 request 中提供應用程式的公開金鑰（ApiKey）。  若應用程式要存取的是使用者的非公開資訊，則必須先取得使用者授權。  所以不管是存取哪一種資訊，該應用程式都必須提供自已的憑證，因此在存取 Blogger API 前，你必須先替應用程式申請自已的憑證。  申請流程請參考＜[申請 Google OAuth2.0 憑證](http://vito-note.blogspot.com/2015/04/google-oauth-20.html?#apply%20Google%20OAuth2.0%20credential)＞。  申請完成後，會取得一組 client\_id 和 client\_secret ，如果申請的是 Web 應用程式類型，還會多一個 redirect\_uri 資訊。

# 啟用 Blogger API V3

針對 Google Blogger API ，Google 規定要存取這組 API 前，必須先設定「啟用」才可以存取，所以申請應用程式憑證後，就是設定啟用 Google Blogger API 。  一般 Google API 要設定啟用，可以在 [Google Developers Console](https://console.developers.google.com/) 網站中直接設定啟用即可存取。  但是 Google Blogger API V3 除了要設定啟用外，還必須提出額度申請文件，並且等到審核通過後才可以存取（約5個工作天）。  底下就是申請啟用　Google Blogger API 的步驟：  

### 提出申請

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDXpJpO53cJFUmkAWfs_0bzn6fhHUtC6DuW1bXRT9QO6sPtMoyQzOP1l4aDg55DEjLnLT4lOsyw-w3RhN2sbTfHsZFk6FrytlgBUyBEAnTW4Hi3MCoDl7VYiSzbv3FTuuGYfj4CzsFRW0/s611/BloggerAPI-06.png)

### 填寫資料

填寫完成直接按「提交」即可

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOOO8jYksZbjgQP0wfw7pv1ksIw5gk94K048Pr8qN40K3s7o-nux2SEk3KfatHt6pDU0zXbGJid38yk8evBPzflCx76zrfaz4EeLQ8h2fwBie3R9KjVohm5dL_ZyENzKwxyeA8AhQb8J4/s627/BloggerAPI-07.png)

### 確認啟用

幾天後，在收到的通知信中，開啟連結網址即可啟用 Blogger API V3

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpC-El_fJiX08hBsKr-DDX0AoHa_w7NkjoG7_pUaCmHtVVwzf-TTH6Tia8DSo1dEkkiuyMHeuTqGh_CfDoddhAzsdSBDqY1lZkF-dYsKb0yRWbar4y0EuuszwJTxCaHchAK4whkp6erUA/s850/BloggerAPI-08.png)

### 完成畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbZNFLneTj33nsLcvHg1tDAnKFtC8k7WK8bGbNUrImLs2E2Ch7Fbh7_ZBoV7V-8B0AXt8vvPWCFbHPh5aFup-HLItUop0NAYfMHBHwoomjJ57IFlRCW6P8csCaCAgpO6J4u3twoAyFpDk/s574/BloggerAPI-09.png)

# 使用 HttpWebRequest 存取 Blogger API

在前面文章中已介紹如何取得使用者授權後的存取憑證，接下來我們將利用這個存取憑證來取得使用者授權的資料。  在 .Net 中，要存取 RESTful Service ，最簡單的方就透過 [HttpWebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.aspx) 物件，它可以幫我們送出 http request 。  

## 存取公開資訊

存取 Blogger API 時，若應用程式存取的對像是使用者設定為公開的資訊，則應用程式無需先經過使用者同意，只要在該 API 的 Uri 中加入應用程式的 ApiKey 即可。  例如：若要取得 7934361432930995230 這個 Blog 內容，你只要送出底下這個 request 即可。  
GET https://www.googleapis.com/blogger/v3/blogs/7934361432930995230?key={YOUR-API-KEY}  
若要取得 7934361432930995230 這個 Blog 中的所有 Post 資訊，其 request 就會長這個樣子。
GET https://www.googleapis.com/blogger/v3/blogs/7934361432930995230/posts?key={YOUR-API-KEY}  
底下程式碼示範如何取得特定的部落格文章：
```c#
private void btnGetByWebRequest_Click(object sender, EventArgs e)
{
string blogId = "7209041933557286912";
string postId = "2576223073544589233";
string apiKey = "AIzaSyAp_CtjwR0XSK12HNigongdmD8RGJHI0CU";

string uri = string.Format("https://www.googleapis.com/blogger/v3/blogs/{0}/posts/{1}?key={2}", blogId, postId, apiKey);
string content = "";

//送出 request
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(uri);
WebResponse response = request.GetResponse();

//取得 response
Encoding encoding = Encoding.UTF8;
using (StreamReader reader = new StreamReader(response.GetResponseStream(), encoding))
{
content = reader.ReadToEnd();
}

//解析回應內容
Post post = JsonConvert.DeserializeObject<Post>(content);
Console.WriteLine(post.Title);
}
```

使用 Fiddler 觀看上面程式碼執行時的狀況：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKmUL2VZeZ-l1MatxthI9ef38fDf6tBYQtu_Y9mFwMh-V21i-N7A2Zd_aC1_5p1P0yqWcuEq4HyyOzUK6qDGNo334aTRkGzJ_N97MQZuvb_Xw-JvUyRu8UsbBnSW2izpJcHYZVir7ymKI/s850/google-blogger-01.png)

## 存取非公開資訊

若要存取的是使用者設定為非公開的資訊，那麼在提交的 request 中就必須包含使用者授權後的存取憑證。  底下描述是 Blog API 文件中，對存取 Blog 清單的需求說明。  
```c#
// HTTP request
GET https://www.googleapis.com/blogger/v3/users/userId/blogs

// Authorization
requires authorization 
```

程式碼範例如下：
```c#
string tokenfile = "auth.blogger";
string[] scopes = new[] { "https://www.googleapis.com/auth/blogger" };
Google_WebClient client = new Google_WebClient(useremail, tokenfile, scopes, appname);
if (!client.SetCredential())
{
client.BeginOAuth(this, null);
Console.WriteLine("授權成功");
}
else
{
string uri = string.Format("https://www.googleapis.com/blogger/v3/users/{0}/blogs","self");
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(uri);

request.Headers.Add(HttpRequestHeader.AcceptLanguage, "zh-tw");
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + client.Token.AccessToken);
request.ContentType = "application/json";

using (var response = request.GetResponse())
{
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
string result = sr.ReadToEnd();
Console.WriteLine(result);
}
}
}
```

使用 Fiddler 觀看上面程式碼執行時的狀況：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVRzZBdJ6NqbAr_s9yRYt8DM8J38MJQJIC9gccFYgGMi8NrrLHSJ-g7Ha2_8gU28DNGzde0T3lQFUK3c1mGjMDsZAOikmVRo5vDlC3YyyCqnUBBJhRK08AbTI0vJoytyAS_7A7HLvM5V8/s809/google-blogger-02.png)

# 使用 Google Apis Blogger V3 Client Library 類別庫存取 Blogger API

無論你的應用程式是 Web 型態或者是 Installed 型態，都可以直接使用 Blogger Client Library 類別庫元件。  在這裡只說明如何利用這個類別庫存取 Blogger API, 相關的應用程式憑證申請，以及如何取得使用者同意授權等作業，請看[這裡](http://vito-note.blogspot.com/2015/04/google-oauth-20.html)。  

## 類別庫下載

Google Apis Blogger V3 Client Library 是 Google 提供用來存取 Blogger API 的類別庫，你可以到 NuGet 取得最新版本。  當然使用時必須搭配 Google Apis Client Library 類別庫。  

1. Google APIs Client Library
- Google APIs Client Library ：#1.9.1
- Google APIs Core Client Library ：#1.9.1
2. Google APIs Auth Client Library ：#1.9.1
3. Google Blogger API Client Library
- Google.Apis.Blogger.v3 #1.9.0.480

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcCaVYSa07FNmpQ-RcgpZGa6_D6q0rzG76Jhbd4Za4gsGqIfZ41CXfmMyWxg1Svty8Z0AClpr5hawAtLZvGeKfTjNXs5qE8y57gDiy6ePRa7AecW_nRLlSXGcumdfRuy1sWAroDqys_K8/s629/google-blogger-03.png)

## Blogger 的架構

在 Blogger 的架構中，大抵包含以下四種資源（類別）： Blogs、Posts、Comments、Pages ，  每一種資料都提供多個存取方法，這些方法包括：  

- list：取得該資源清單。
- get：取得指定的資源。
- getByUrl：依據 url 取得指定的資源。
- getByPath：依據 path 取得指定的資源。
- listByUser：依據 user 取得指定的資源清單。
- search：依據 query parameter 取得指定的資源清單。
- insert：新增資源。
- delete：刪除資源。
- patch：更新資源。
- update：更新資源。

不過，並不是每個類別都支援列表中的每個方法，每個類別實際支援的方法如下表：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg_0gC_c63DYf57ssV1PwuN6F8d8TdlxaAqLaY2X8oEjuiJE_UDKxU714h3potZcoWDQjhU5Nfmi7mfKSo6cJtgW52Qk8offOno4tuy0kvL3p3BqOMgFgmfbpp9O4sDCBWTw4iY8qam6PY/s850/blogger-api-04.png)

## 在應用程式中存取 Blogger API

在上面的例子中，我們自行透過 [HttpWebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.aspx) 來和 Google 伺服器溝通，並且使用 [Json.Net](http://json.codeplex.com/) 將收到的內容進行還原序列化。  而 Blogger Client Library 包裝了許多與 Google 伺服器溝通的結節，而且將每個 API 的 Uri 都定義在裡頭，無需自已在程式碼中指定，大大的減少程式碼撰寫的時間。  

### 存取公開資訊

底下程式碼示範如何取得特定的部落格文章：
```c#
string blogId = "7209041933557286912";
string postId = "2576223073544589233";
string apiKey = "AIzaSyAp_CtjwR0XSK12HNigongdmD8RGJHI0CU";

BloggerService service = new BloggerService(new BaseClientService.Initializer()
{
ApiKey = apiKey
}
);

var request = service.Posts.Get(blogId, postId);
Post post = request.Execute();

if (post != null)
{
Console.WriteLine(post.Title);
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjpzTWy0n_xN2pX-oAwzTMpSENTCdy5XCVPnb-Udh0VAaCavIb2AF75Q8ksbjghgzGnZGJnKU8TW0Fajoj7UNdoog3QiSHJ6GH_0BVah5OGhJZEP5cYOYUaFf7CwczGDeQ4GwK36o50bk/s850/google-blogger-04.png)

### 存取非公開資訊

底下程式碼示範如何取得 Blog 清單：
```c#
UserCredential credential = GetCredential();
if (credential != null)
{
BloggerService service = new BloggerService(new BaseClientService.Initializer()
{
HttpClientInitializer = credential
}
);

var request = service.Blogs.ListByUser("self");
BlogList list = request.Execute();

if (list != null)
{
Console.WriteLine(list.Items.Count);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSaCVjwXrNv2mAVNpzChlG6nSYwnmTsmlLfBGI26lZ2-YVWzzo5dAG3gBISUs7FTRB1NfGY4bYet7Uo8uao88-mFAVvoqzL2zhT6rCZcJh3Q9chXT9sUuzmR3Db5jZTfYT4kdUZMosc1Q/s798/google-blogger-05.png)
## 參考資料  

- [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/accounts/docs/OAuth2)
- [The OAuth 2.0 Authorization Protocol](http://tools.ietf.org/id/draft-ietf-oauth-v2-22.html)
- [Google OAuth 2.0 APIs for .NET](https://developers.google.com/api-client-library/dotnet/guide/aaa_oauth#installed_applications)
- [Blogger API: Using the API](https://developers.google.com/blogger/docs/3.0/getting_started)
- [Blogger API Reference](https://developers.google.com/blogger/docs/3.0/reference/index)
- [Blogger API v3 Explorer](https://developers.google.com/apis-explorer/#p/blogger/v3/)
- [Blogger API v3 Class List](https://developers.google.com/resources/api-libraries/documentation/blogger/v3/csharp/latest/annotated.html)