---
title: 認識ASP.NET
layout: default
parent: ASP.NET WebSite
nav_order: 6
description: "認識ASP.NET"
date: 2012-09-25
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "8378764391544635724"
---
網頁程式不像視窗程式可以直接由單一的 process 執行。  它通常必須掛載在 Web Server 上，等待用戶端的 Web Browser 提出要求。  這二者之間的通訊就是使用 HTTP 協定。  

# 網頁通訊

## Web Server

- The first Web servers were responsible for receiving and processing simple user requests from browsers via HTTP.
- The Web server handled its request and sent a response back to the Web browser.
- The Web server then closed any connection between it and the browser and released all resources that were involved with the request.

![A simple request and response between browser and server in a stateless environment](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1Ick9qeuTaaNRhInvSI5sPAm1CFbirJHbxuUqpj2IGaMF-eIkC4MFI9cX4XPuGitJs4mlQuAr_epWepf_QxRmC97P4zrPOBPX1Z-KoaogvBnh0WmeYdhyphenhyphen7HTLXjgvGkZ0ko-vq1JgyTI/s457/request-and-response.png)

## Web Browser

- The Web browser provides a platform-independent means of displaying Web pages that were written in HTML.
- Although the Web browser's role is simply to present information and collect data from users,   many new client-side technologies enable today's Web browsers to execute code such as JavaScript and to support plug-ins that improve the user's experience, such as JavaScript and AJAX.

## HTTP (Hypertext Transfer Protocol)

#### HTTP

- 一種以文字為基礎的通訊協定，用來向 WebServer 提出需求，或者發送回應給 Browser 。
- WebServer 和 Browser 之間的訊息傳送通常使用 80 port ，若是安全性HTTP (HTTPS) 則使用 443 port。

### Request

當 Browser 向 WebServer 提出需求時，這個訊息內容會長的像底下這個樣子：  
&lt;span style="color:red"&gt;GET&lt;/span&gt; &lt;span style="color:blue"&gt;/default.aspx&lt;/span&gt; &lt;span style="color:green"&gt;HTTP/1.1&lt;/span&gt;
Host: www.corpname.com  
上面需求內容，說明如下：

1. 第一行第一個字稱為 Command ( 或 Verb 或 Method ) ，此例為 GET
2. 接著是 URI ，此例為 /default.aspx
3. 接著是 HTTP Version ，此例為 HTTP/1.1
4. 第二行是網站的識別名稱，通常會對應到一組 IP 。

#### HTTP/1.1 支援的 Methods

- **GET**：Gets a URL from the server
- **POST**：Sends data to the Web server for processing.
- **PUT**：Allows a client to directly create a resource at the indicated URL on the server.
- **DELETE**：Used to delete a resource at the Web server.
- **HEAD**：Retrieves the meta information for a resource.
- **OPTIONS**：
- **CONNECT**：
- **TRACE**：
- **DEBUG**：

當 IIS 收到 Browser 所提出的需求時，  它會先以一個 [Requtst](http://msdn.microsoft.com/zh-tw/library/h55b6cak%28v=vs.100%29.aspx) 物件來 wrap 這個需求內容再交由 ASP.NET 處理，以便日後可於程式碼中存取需求內容。  例如：cookies, query string parameters, request path 等資訊。

### Response

當 ASP.NET 處理完需求，要送給 Browser 回應時，會將訊息 wrap 成 [Response](http://msdn.microsoft.com/zh-tw/library/ms525405.aspx) 物件。  你可以透過這個物件設定 cookies, caching, page expiration 等資訊。  

而 IIS 則會根據 [Response](http://msdn.microsoft.com/zh-tw/library/ms525405.aspx) 物件中內容，回應如下的訊息內容：  
HTTP/1.1 200 OK
Server: Microsoft-IIS/6.0
Content-Type: text/html
Content-Length: 38
&lt;html&gt;&lt;body&gt;Hello, world.&lt;/body&gt;&lt;html&gt;  
上面回應內容，說明如下：

1. communication protoco, status code
2. the type of Web server
3. 回應給 Browser 的內容型態 (a MIME type)
4. the content length
5. the response message

#### 狀態碼說明 (Status Codes)

簡單說，HTTP是一種請求／回應(Request/Response)的網路傳輸協定，當用戶端應用程式(瀏覽器)要求一網頁資訊或資源(Document)時，網站伺服器端就會回傳狀態碼(Status Code)，這些狀態碼通常是以數據化顯示。  使用者最常見到應該是404，其它包括200、301、302、304、403、410、500等等，都是3碼數字組成，第1碼是不同屬性的分類，以下依照數字小到大個別說明。  
&lt;!--#include file="status-code.htm" --&gt;  
PS. IIS7 還有另外擴充一些狀態碼，可參考 MSDN : [The HTTP status codes in IIS 7.0](http://support.microsoft.com/kb/943891)

#### 多用途網際網路郵件延伸標準 (Multipurpose Internet Mail Extensions , MIME)

MIME 類型是用來識別從 WebServer 提供給瀏覽器或郵件用戶端的內容類型。   瀏覽器要求 WebServer 提供內容時，瀏覽器也同時要求該內容的 MIME 類型。 在傳回內容之前，IIS 會將此 MIME 類型隨著 HTTP 標頭中的 Content\_Type 欄位傳回，這樣瀏覽器就可以知道如何處理或顯示該內容。  

MIME type 都是由二部份組成 type/subtype 。底下為一些常見的 MIME Types.  

- **Text**：Textual information
- **Image**：Image data..
- **Audio**：Audio data.
- **Video**：Video data.
- **Application**：Other kinds of data, typically either uninterpreted binary data or information to be processed by an application.

## Submit Data to the Web Server

The HTML &lt;form&gt; tag can be used to create a Web page that collects data from the user and sends the collected data back to the Web server.  There are two HTTP methods that can be used to submit the form data back to the Web server: **GET** and **POST**.  
```xml
<form method="POST" action="getCustomer.aspx">
Enter Customer ID:
<input type="text" name="Id">
<input type="submit" value="Get Customer">
</form>
```

#### Using **GET** verb

When the GET verb is used, the form data is appended to the URL as part of the query string.   The following provides an example:  
GET /getCustomer.aspx?Id=123&color=blue HTTP/1.1
Host: www.xxx.com  
**Note：**  When using Microsoft Internet Explorer and IIS, the limit for a given query string is 1,024 characters.  

#### Using **POST** verb

When the POST verb is used, the form data is placed into the message body of the request as follows:  
POST /getCustomer.aspx HTTP/1.1
Host: www.northwindtraders.com

Id=123&color=blue

# MISC

## Request 屬性

| 屬性 | GET Request | POST Request |
| --- | --- | --- |
| RequestContext | System.Web.Routing.RequestContext |  |
| IsLocal | True |  |
| HttpMethod | GET | POST |
| RequestType | GET | POST |
| ContentType |  |  |
| ContentLength | 0 | 22 |
| ContentEncoding | System.Text.UTF8Encoding |  |
| AcceptTypes | System.String[] |  |
| IsAuthenticated | True |  |
| IsSecureConnection | False |  |
| Path | /C01\_ASPNET\_WebSite/Understanding.aspx |  |
| AnonymousID |  |  |
| FilePath | /C01\_ASPNET\_WebSite/Understanding.aspx |  |
| CurrentExecutionFilePath | /C01\_ASPNET\_WebSite/Understanding.aspx |  |
| CurrentExecutionFilePathExtension | .aspx |  |
| AppRelativeCurrentExecutionFilePath | ~/C01\_ASPNET\_WebSite/Understanding.aspx |  |
| PathInfo |  |  |
| PhysicalPath | D:\myMCTS\VitoPractice\70-528\Practice70515\C01\_ASPNET\_WebSite\Understanding.aspx |  |
| ApplicationPath | / |  |
| PhysicalApplicationPath | D:\myMCTS\VitoPractice\70-528\Practice70515\ |  |
| UserAgent | Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |
| UserLanguages | System.String[] |  |
| Browser | System.Web.Mobile.MobileCapabilities |  |
| UserHostName | ::1 |  |
| UserHostAddress | ::1 |  |
| RawUrl | /C01\_ASPNET\_WebSite/Understanding.aspx |  |
| Url | http://localhost:14081/C01\_ASPNET\_WebSite/Understanding.aspx |  |
| UrlReferrer |  |  |
| Params | ...... |  |
| QueryString | EmpNo=001&EmpName=Vito |  |
| Form |  | EmpNo=001&EmpName=vito |
| Headers | ...... |  |
| ServerVariables | ...... |  |
| Cookies | System.Web.HttpCookieCollection |  |
| Files | System.Web.HttpFileCollection |  |
| InputStream | System.Web.HttpInputStream |  |
| TotalBytes | 0 | 22 |
| Filter | System.Web.HttpInputStreamFilterSource |  |
| ClientCertificate | System.Web.HttpClientCertificate |  |
| LogonUserIdentity | System.Security.Principal.WindowsIdentity |  |

## Request.ServerVariables 變數

| 變數 | 資料 |
| --- | --- |
| ALL\_HTTP | HTTP\_CACHE\_CONTROL:no-cache  &lt;br&gt;  HTTP\_CONNECTION:Keep-Alive  &lt;br&gt;  HTTP\_CONTENT\_LENGTH:211  &lt;br&gt;  HTTP\_CONTENT\_TYPE:application/x-www-form-urlencoded  &lt;br&gt;  HTTP\_ACCEPT:text/html, application/xhtml+xml, \*/\*  &lt;br&gt;  HTTP\_ACCEPT\_ENCODING:gzip, deflate  &lt;br&gt;  HTTP\_ACCEPT\_LANGUAGE:zh-TW  &lt;br&gt;  HTTP\_COOKIE:ASP.NET\_SessionId=kd4egzz12bn2frgf2yvmbnjj  &lt;br&gt;  HTTP\_HOST:localhost:60755  &lt;br&gt;  HTTP\_REFERER:http://localhost:60755/C01\_ASPNET\_WebSite/Understanding.aspx  &lt;br&gt;  HTTP\_USER\_AGENT:Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |
| ALL\_RAW | Cache-Control: no-cache  &lt;br&gt;  Connection: Keep-Alive  &lt;br&gt;  Content-Length: 211  &lt;br&gt;  Content-Type: application/x-www-form-urlencoded  &lt;br&gt;  Accept: text/html, application/xhtml+xml, \*/\*  &lt;br&gt;  Accept-Encoding: gzip, deflate  &lt;br&gt;  Accept-Language: zh-TW  &lt;br&gt;  Cookie: ASP.NET\_SessionId=kd4egzz12bn2frgf2yvmbnjj  &lt;br&gt;  Host: localhost:60755  &lt;br&gt;  Referer: http://localhost:60755/C01\_ASPNET\_WebSite/Understanding.aspx  &lt;br&gt;  User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |
| APPL\_MD\_PATH |  |
| APPL\_PHYSICAL\_PATH | D:\myMCTS\VitoPractice\70-528\Practice70515\ |
| AUTH\_TYPE | NTLM |
| AUTH\_USER | VITO-2011W7\Administrator |
| AUTH\_PASSWORD |  |
| LOGON\_USER | VITO-2011W7\Administrator |
| REMOTE\_USER | VITO-2011W7\Administrator |
| CERT\_COOKIE |  |
| CERT\_FLAGS |  |
| CERT\_ISSUER |  |
| CERT\_KEYSIZE |  |
| CERT\_SECRETKEYSIZE |  |
| CERT\_SERIALNUMBER |  |
| CERT\_SERVER\_ISSUER |  |
| CERT\_SERVER\_SUBJECT |  |
| CERT\_SUBJECT |  |
| CONTENT\_LENGTH | 211 |
| CONTENT\_TYPE | application/x-www-form-urlencoded |
| GATEWAY\_INTERFACE |  |
| HTTPS |  |
| HTTPS\_KEYSIZE |  |
| HTTPS\_SECRETKEYSIZE |  |
| HTTPS\_SERVER\_ISSUER |  |
| HTTPS\_SERVER\_SUBJECT |  |
| INSTANCE\_ID |  |
| INSTANCE\_META\_PATH |  |
| LOCAL\_ADDR | ::1 |
| PATH\_INFO | /C01\_ASPNET\_WebSite/Understanding.aspx |
| PATH\_TRANSLATED | D:\myMCTS\VitoPractice\70-528\Practice70515\C01\_ASPNET\_WebSite\Understanding.aspx |
| QUERY\_STRING |  |
| REMOTE\_ADDR | ::1 |
| REMOTE\_HOST | ::1 |
| REMOTE\_PORT |  |
| REQUEST\_METHOD | POST |
| SCRIPT\_NAME | /C01\_ASPNET\_WebSite/Understanding.aspx |
| SERVER\_NAME | localhost |
| SERVER\_PORT | 60755 |
| SERVER\_PORT\_SECURE | 0 |
| SERVER\_PROTOCOL | HTTP/1.1 |
| SERVER\_SOFTWARE |  |
| URL | /C01\_ASPNET\_WebSite/Understanding.aspx |
| HTTP\_CACHE\_CONTROL | no-cache |
| HTTP\_CONNECTION | Keep-Alive |
| HTTP\_CONTENT\_LENGTH | 211 |
| HTTP\_CONTENT\_TYPE | application/x-www-form-urlencoded |
| HTTP\_ACCEPT | text/html, application/xhtml+xml, \*/\* |
| HTTP\_ACCEPT\_ENCODING | gzip, deflate |
| HTTP\_ACCEPT\_LANGUAGE | zh-TW |
| HTTP\_COOKIE | ASP.NET\_SessionId=kd4egzz12bn2frgf2yvmbnjj |
| HTTP\_HOST | localhost:60755 |
| HTTP\_REFERER | http://localhost:60755/C01\_ASPNET\_WebSite/Understanding.aspx |
| HTTP\_USER\_AGENT | Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |

## ASP.NET 頁面事件的執行順序

&lt;!--#include file="page-life-cycle.htm" --&gt;