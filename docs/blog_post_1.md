---
title: 如何取得特定網頁的文件內容
layout: default
parent: 
nav_order: 1
description: "如何取得特定網頁的文件內容"
date: 2012-06-28
tags: []
postid: "3163928469610498523"
---
若想要透過程式取得特定網頁的內容，可以透過以下幾種元件：  

- [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx)：網頁瀏灠控制項。
- [WebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.webrequest)：這個類別可以對指定 URI 發送需求與接收回應。
- [WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient)：這個類別包裝 WebRequest 類別，也可以對指定的 URI 傳送與接收資料，可以說是 HttpWebRequest 的精簡版

# 由 WebBrowser 控制項中取得

[WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 是一個用來瀏灠網頁用的控制項，當網頁下載完畢時，你只要透過 [WebBrowser.DocumentText](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.documenttext.aspx) 屬性就可以取得完整的網頁內容。  或者由 [WebBrowser.DocumentStream](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.documentstream.aspx) 建立資料流後，再由資料流讀取資料。  同時，為了確保網頁載入完成，上述程式碼應該寫在 [DocumentCompleted](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.documentcompleted.aspx) 事件裡。  
```c#
private void button1_Click(object sender, EventArgs e)
{
webBrowser1.Url = new System.Uri(sUrl);
}

private void webBrowser1_DocumentCompleted(object sender, WebBrowserDocumentCompletedEventArgs e)
{
// 直接由 WebBrowser.DocumentText 的屬性取得 html 內容
string sContent = webBrowser1.DocumentText;

// 使用 WebBrowser.DocumentStream 建立資料流
// 使用 StreamReader 讀入資料流以取得網頁內容

Stream stream = webBrowser1.DocumentStream;
StreamReader reader ;

// 以 UTF8 編碼方式,讀取網頁內容
reader = new StreamReader(stream, System.Text.Encoding.UTF8);
string htmlUTF8 = reader.ReadToEnd();

reader.Close();
}
```

# 由 WebRequest 物件取得

[WebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.webrequest) 是 .Net Framework 中的一個 abstract 類別，其主要功用是用來對特定的 URI 送出 [WebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.webrequest.aspx) 要求，以及處理 [WebResponse](http://msdn.microsoft.com/zh-tw/library/system.net.webresponse.aspx) 回應。  而 [HttpWebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.aspx) 則是它的實作類別，是一個封裝了 Http 通訊協議的物件，支援 Header、Content、Cookie 等屬性。  

通常要去 WebServer 提出要求，只要叫用 [HttpWebRequest.GetResponse](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.getresponse.aspx) 方法即可，這是一個**同步**要求，也就是必須等到伺服器回應結束程式才會繼續。  若要對資源提出**非同步**要求，則可以使用 [BeginGetResponse](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.begingetresponse.aspx) 和 [EndGetResponse](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.endgetresponse.aspx) 方法。   

與 [WebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.webrequest) 相對應的就是 [WebResponse](http://msdn.microsoft.com/zh-tw/library/system.net.webresponse.aspx) 物件，它是 .Net Framework 中用來包裝回應訊息的基底類別，你可以使用其衍生類別 [HttpWebResponse](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebresponse.aspx) 物件來操作這些訊息內容。  

與 Web 伺服器進行溝通時，最常用的就是 GET 與 POST 二種方式，  這二個方法，最大的差異處在於，使用 POST 方法時必須先設定 HttpContent 的標頭資訊，再叫用 [GetRequestStream](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.getrequeststream.aspx) 方法建立一個 Stream 物件，以便將參數資料寫入。  而使用 GET 方法，不用設定 HttpContent 的標頭資訊，且參數列資料直接附加在網址後面即可。  底下分別對這二種方式進一步說明：  

## HTTP　GET
```c#
string url = "http://www.taifex.com.tw/chinese/3/3_1_1.asp";
string param = "qtype=2&commodity_id=specialid&commodity_id2=DTF&syear=2013&smonth=9&sday=2";
string uri = url + "?" + param;

//建立 GET request 
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
request.Method = "GET";

//送出 request 並取得回應內容
string result = "";
using (WebResponse response = request.GetResponse())
{
//處理回應內容
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
result = sr.ReadToEnd();
}
}
Console.WriteLine(result);
```

## HTTP　POST
```c#
string url = "http://www.taifex.com.tw/chinese/3/3_1_1.asp";
string param = "qtype=2&commodity_id=specialid&commodity_id2=DTF&syear=2013&smonth=9&sday=2";

//建立 POST request 
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
request.Method = "POST";
request.ContentType = "application/x-www-form-urlencoded";
request.ContentLength = param.Length;

//如果 Request 有使用到 Cookie 驗證，就必須設定相關的 cookie
//request.CookieContainer = cookie;

//將參數資料以 stream 方式加入到 request 物件中
byte[] bs = Encoding.ASCII.GetBytes(param);
using (Stream stream = request.GetRequestStream())
{
stream.Write(bs, 0, bs.Length);
}

//送出 request 並取得回應內容
string result = "";
using (WebResponse response = request.GetResponse())
{
//處理回應內容
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
result = sr.ReadToEnd();
}
}
Console.WriteLine(result);
```

## 對中文資料編碼

如果送出去的 request 中，包含中文的參數資料，可以先對資料進行編碼，至於應該採用何種編碼方式，在 GET 與 POST 有所不同。  若使用 GET 方式，你必須依據對方伺服器要求的編碼方式為標準；  若使用 POST 方式，你可以在所提交資料的 Header 中，透過 ContentType 屬性加以附註說明，接收資料的 Web 伺服器就能夠正確的解析。  
```c#
string url = "http://www.taifex.com.tw/chinese/3/3_1_1.asp";
string param = "syear={0}&smonth={1}&sday={2}&qtype=2&commodity_id=specialid&commodity_id2=DTF";

Encoding encoding = Encoding.GetEncoding("utf-8");
param = string.Format(param,
HttpUtility.UrlEncode("2014", encoding),
HttpUtility.UrlEncode("01", encoding),
HttpUtility.UrlEncode("30", encoding));

//建立 POST request 
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
request.Method = "POST";
request.ContentType = "application/x-www-form-urlencoded";
request.ContentLength = param.Length;

//將參數資料以 stream 方式加入到 request 物件中
byte[] bs = Encoding.ASCII.GetBytes(param);
using (Stream stream = request.GetRequestStream())
{
stream.Write(bs, 0, bs.Length);
}

//送出 request 並取得回應內容
string result = "";
using (WebResponse response = request.GetResponse())
{
//處理回應內容
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
result = sr.ReadToEnd();
}
}
Console.WriteLine(result);
```

## HttpWebRequest.AllowAutoRedirect

有時候 Web 主機會回應 HTTP 狀態碼 300,301,302 到用戶端，如果我們想要截取這個資訊，就必須將 [AllowAutoRedirect](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.allowautoredirect.aspx) 屬性設成 false ，它的預設值為 ture 。  如果值為 ture ，當送出 [HttpWebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.aspx) 後，伺服器會自動導向新的位置，而不會回應狀態碼給應用程式，而用戶端取得的 [WebResponse](http://msdn.microsoft.com/zh-tw/library/system.net.webresponse.aspx) 將是最後網頁的資訊。  如果值為 false ，所有具 300 至 399 之 HTTP 狀態碼的回應都會傳回至應用程式。你可以依取得的相關回應資訊，再進行其他處理。  
```c#
private WebResponse GetWebResponse_POST2(string url, byte[] para, ref CookieContainer cookie)
{
try
{
HttpWebRequest request = WebRequest.Create(url) as HttpWebRequest;
request.Method = "POST";
request.AllowAutoRedirect = false;

///如果 Request 有使用到 Cookie 驗證則必須設定相關的 cookie
request.CookieContainer = cookie;

//1)設定 Request 的 HTTP 標頭
request.ContentType = "application/x-www-form-urlencoded";
request.ContentLength = para.Length;

//叫用 GetRequestStream 方法建立 Stream 物件，以便寫入參數資料
using (Stream stream = request.GetRequestStream())
{
stream.Write(para, 0, para.Length);  
}

//取得回應
WebResponse response = request.GetResponse();

//判斷狀態碼，再自行做適當的處理
if (((HttpWebResponse)response).StatusCode == HttpStatusCode.Redirect)
{
string redirUrl = "http://" + request.Host + response.Headers["Location"]; //取得Redirect網址
HttpWebRequest request_new = (HttpWebRequest)WebRequest.Create(redirUrl);
request_new.Method = "GET";
request_new.CookieContainer = cookie;
response.Close();
WebResponse response_new = request_new.GetResponse();
return response_new;
}
else
{
return response;
}
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
return null;
}
}
```

## Cookie 的保存

當使用 HttpWebRequest 對伺服器提交要求時，若要重覆使用服器的回應的 Cookie 資訊，就必須由程式自行來管理這個 Cookie 資訊。  底下範例利用 .NET 中的 BinaryFormatter 物件，將 Cookie 資訊儲存到磁碟中，讓該資訊可以永久保存，若下一次要使用時，再將其還原序列化即可。  

首先要知道的是，若要取得伺服器回應的 Cookie 資訊，在送出的 HttpWebRequest 物件中，必須包含一個 CookieContainer 物件。
```c#
//要求網址
string url = "http://localhost:8080/test/login.aspx";

//參數資料
NameValueCollection parameters = new NameValueCollection();
parameters.Add("txt_UserID", "000000");
parameters.Add("txt_PassWD", "111111");
byte[] postData = Encoding.UTF8.GetBytes(myWeb.ToQueryString(parameters));

//建立 request using post method
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
request.Method = "POST";
request.ContentType = "application/x-www-form-urlencoded";

//設定 CookieContainer
CookieContainer cookies = new CookieContainer();
request.CookieContainer = cookies;

//將參數資料以 stream 方式加入到 HttpWebRequest 物件中
request.ContentLength = postData.Length;
using (Stream stream = request.GetRequestStream())
{
stream.Write(postData, 0, postData.Length);
}

//送出 request 並取得回應內容
string content = "";
using (WebResponse response = request.GetResponse())
{
//處理回應內容
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
content = sr.ReadToEnd();
}
}

//將cookie以二進位格式序列化方式儲存
string file = (new Uri(url)).Host + ".bf";
using (Stream stream = File.Create(file))
{
BinaryFormatter formatter = new BinaryFormatter();
formatter.Serialize(stream, cookies);
}
```

上面例子中，因為 Cookie 資訊記錄了使用者登入狀態，之後，若有其他必須頁面必須經過授權才可瀏灠的話，就可以直接附上這個資訊。
```c#
string url = "http://localhost:8080/test/page1.aspx";

//取出磁碟中的 cookie 資訊
CookieContainer cookies = new CookieContainer();
string file = (new Uri(url)).Host + ".bf";
using (Stream stream = File.Open(file, FileMode.Open))
{
BinaryFormatter formatter = new BinaryFormatter();
cookies = (CookieContainer)formatter.Deserialize(stream);
}

//建立 request using post method
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

//設定 cookie 
request.CookieContainer = cookies;

//送出 request 並取得回應內容
string content = "";
using (WebResponse response = request.GetResponse())
{
//處理回應內容
using (StreamReader sr = new StreamReader(response.GetResponseStream()))
{
content = sr.ReadToEnd();
}
}
```

## 關於 HTTP 的小知識

當 Client 送出一個 HTTP Request 時，這個 Request 的內容除了第一行的 Method、Path、Http Version 資訊外，其餘則分成 Head 和 Body 二個部份，彼此之間以一個空白行間隔。  每一個 Reqeust 都會用到 Head 部份，用來標示著該 Reqeust 的相關屬性。  而 Body 部份則是用來存放資料的地方，並非每一個 Request 都會使用到這個部份，必須視使用的 Method (GET,PUT,DELETE,POST...) 而定。  例如發出一個 Get Request 就不會含有 Body 資訊，而 Post Request 就一定會用到，有用到 Body 的 Request 就需要指定 Content-Type 屬性，這也是為什麼上面例子中，  當發出一個 HTTP POST Request 時，必須指定 ContentType="application/x-www-form-urlencoded" 的原因了。  

底下二張圖，是由 Fiddler 截取，它是一個 Get Request 發送的內容以及它的回應內容。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXSXEFEL1rHrHys9PIKaM2YgzSVev7ZVaMYiXmd9zEq8u9tspHNbLah7IBvieBjdlIey-k6k3DVO-V-7RdvqaLyTqI6NRW8WP2PmjqFTQEKgYYpo1mUCWgNePV1x10jI2eX_ae8zbGhhk/s0/http-get-request.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsqFWMsawvJcTw7hKzrIsh0kaZbotjf6Cws4GqOPqwhlMoiT53HXCsa1LgzM2q9J-5NBzE90WsSRK72SKX4wr9asKDDYS3KlAjUl3k-EfFSMHNgbf3smC7NKymo0h7vMQzj4whn96W37Q/s0/http-get-response.png)

底下二張圖則是一個 POST Request 發送的內容以及它的回應內容。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQBwnVZWExWxB5vlX6kazg9Q2tA8r2xovHdTsSTdElsoJW-deQTZHMl9SmGw0Kqo_cjrp30LyygbkFvgOkhZ-32ia-VWCo-92dNHmJo8q7KUXp5VIrVtjvvnSmK6y788zJmXWqGa9dQnQ/s0/http-post-request.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN1FPQGeP6jGkhsmG1UFogWxgMpsA2AiYULouK5i8V1yULYOdpGbHL5UtOWzLNdowKAjIxjtwzZddA4ZbF1gLvWKLeTON8dj9GTdxRmy0CTFUR5hr6KuegyNhztBnbv-5hW-Kn77YITM0/s0/http-post-response.png)

# 由 WebClient 物件取得

[WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient) 類別包裝 [WebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.webrequest) 類別，也可以用來對指定的 URI 傳送與接收資料。  它可以說是 [HttpWebRequest](http://msdn.microsoft.com/zh-tw/library/system.net.httpwebrequest.aspx) 的精簡版，因此幾個常用的存取網頁方法，都可以直接由 [WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.aspx) 輕鬆完成。  

## WebClient GET

若要使用 [WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.aspx)針對特定資源進行 GET 操作，有底下二個方法可用：
- [DownloadString](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.downloadstring.aspx) ：以 string 方式取得網頁的回應內容。
- [DownloadData](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.downloaddata.aspx) ：以 Byte 陣列的方式取得網頁的回應內容。

若要透過 [WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.aspx) 執行 Get 方法，只要叫用 [DownloadString](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.downloadstring.aspx) 方法即可。
```c#
private string GetWebResponse_GET(string url)
{
WebClient wc = new WebClient();
wc.Encoding = Encoding.UTF8;
string result = wc.DownloadString(url);
return result;
}
```

## WebClient POST

若要使用 [WebClient](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.aspx) 針對特定資源進行 POST 操作，有底下二個方法可用：  

- [UploadString](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.uploadstring.aspx) ：Uploads the specified string to the specified resource.
- [UploadValues](http://msdn.microsoft.com/zh-tw/library/system.net.webclient.uploadvalues.aspx) ：Uploads the specified name/value collection to the resource identified by the specified URI.
```c#
private string GetWebResponse_POST(string url, string parameters)
{
WebClient wc = new WebClient();
wc.Encoding = Encoding.UTF8;
wc.Headers[HttpRequestHeader.ContentType] = "application/x-www-form-urlencoded";
string result = wc.UploadString(url, parameters);
return result;
}

private string GetWebResponse_POST(string url, NameValueCollection parameters)
{
WebClient wc = new WebClient();
wc.Encoding = Encoding.UTF8;
wc.Headers[HttpRequestHeader.ContentType] = "application/x-www-form-urlencoded";
byte[] buffer = wc.UploadValues(url, parameters);
string result = MyWebDownload.EncodingData(buffer);
return result;
}
```

叫用方法：  
```c#
var url = "http://www.twse.com.tw/ch/trading/exchange/MI_INDEX/MI_INDEX.php";

//叫用 UploadString
string parameters1 = "qdate=104/01/05&selectType=MS2";
string response1 = GetWebResponse_POST(url, parameters1);

//叫用 UploadValues
NameValueCollection parameters2 = new NameValueCollection();
parameters2.Add("qdate", "104/01/05");
parameters2.Add("selectType", "MS2");
string response2 = GetWebResponse_POST(url, parameters2);
```

# 服務器提交協議衝突 Section=ResponseStatusLine

有時候在下載網頁資料時，會發生「伺服器認可通訊協定違規. Section=ResponseStatusLine」錯誤訊息，在網路上找到一些原因說法，原因可能是回應的內容並沒有以 CRLF 做結束。  解決方法是在 app.config 中加入以下設定：  
```xml
<system.net>
<settings>
<httpWebRequest useUnsafeHeaderParsing="true" />
</settings>
</system.net>
```

如果問題還是存在，將 HttpWebRequest 的 KeepAlive 屬性設成 false 。


- [The server committed a protocol violation. Section=ResponseStatusLine ERROR](http://stackoverflow.com/questions/2482715/the-server-committed-a-protocol-violation-section-responsestatusline-error)