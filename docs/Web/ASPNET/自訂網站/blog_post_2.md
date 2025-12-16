---
title: 使用ASP.NET內建物件
layout: default
parent: 自訂網站
nav_order: 2
description: "使用ASP.NET內建物件"
date: 2012-10-02
tags: [Web,ASPNET,自訂網站]
postid: "1435323241582104086"
---
ASP.NET 提供了幾個系統物件，可以用來存取許多相當有用的資訊，例如取得使用者IP，瀏覽器種類等等。  這類物件必須透過 [Page](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.aspx) 物件的屬性取得，包含：  

- [Request](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.request.aspx) ：這個物件包含目前 HTTP 要求的相關資訊。
- [Response](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.response.aspx) ：這個物件包含回應資訊，允許您將資訊，透過 HTTP 回應給用戶端。
- [Context](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.context.aspx) ：這個物件提供網頁執行內容的相關資訊。
- [Browser](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.browser.aspx) ：取得或設定關於用戶端瀏覽器的資訊。
- [Server](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.server.aspx) ：這個屬性提供常用的 [HtmlEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.htmlencode.aspx) 和 [MapPath](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.mappath.aspx) 方法。
- [Application](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.application.aspx) ：取得目前 Web 要求的 HttpApplicationState 物件。
- [Session](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.session.aspx) ：這個屬性提供目前要求的工作階段（Session）資訊。
- [Trace](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.trace.aspx) ：追蹤並顯示關於 Web 要求的執行詳細資料。

# Response

## Response 方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [AddHeader](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addheader.aspx) | Adds an HTTP header to the response stream. |
|  | [AppendCookie](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.appendcookie.aspx) |  |
|  | [AppendHeader](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.appendheader.aspx) |  |
|  | [AppendToLog](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.appendtolog.aspx) |  |
|  | [BinaryWrite](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.binarywrite.aspx) | Writes binary characters to the HTTP response. |
|  | [Clear](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.clear.aspx) |  |
|  | [ClearContent](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.clearcontent.aspx) |  |
|  | [ClearHeaders](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.clearheaders.aspx) |  |
|  | [End](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.end.aspx) | Completes the response |
|  | [Flush](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.flush.aspx) | 強迫送出目前存放在緩衝區的資料，並清除目前存放在緩衝區的資料 |
|  | [Redirect](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.redirect.aspx) |  |
|  | [SetCookie](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.setcookie.aspx) |  |
|  | [TransmitFile](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.transmitfile.aspx) |  |
|  | [Write](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.write.aspx) | Writes a text string to the HTTP response. |
|  | [WriteFile](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.writefile.aspx) | Writes a file to the HTTP response with buffering. |
|  | [WriteSubstitution](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.writesubstitution.aspx) | This allow you to dynamically update that cached output. |

##### Response.Redirect與Server.Transfer差別

請參考：[http://www.dotblogs.com.tw/jimmyyu/archive/2009/11/10/11503.aspx](http://www.dotblogs.com.tw/jimmyyu/archive/2009/11/10/11503.aspx)

## Response 屬性

| 屬性 | 資料 | 說明 |
| --- | --- | --- |
| [Buffer](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.buffer.aspx) | True | If True, the response is buffered before sending it back to the user.   &lt;br&gt;  If False, the response is sent back to the user in chunks. |
| [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.cache.aspx) | System.Web.HttpCachePolicy |  |
| [Cookies](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.cookies.aspx) | System.Web.HttpCookieCollection |  |
| [Expires](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.expires.aspx) | 0 |  |
| [ExpiresAbsolute](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.expiresabsolute.aspx) | 0001/1/1 上午 12:00:00 |  |
| [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.filter.aspx) |  |  |
| [IsClientConnected](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.isclientconnected.aspx) | True | 取得瀏覽端是否還處於連線狀態 |
| [Status](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.status.aspx) | 200 OK |  |
| [StatusCode](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.statuscode.aspx) | 200 |  |

# Request

## Request 方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [MapPath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.mappath.aspx) | 將用戶端目前要求之 URL 的虛擬路徑對應至伺服器上的實體路徑。 |
|  | [SaveAs](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.saveas.aspx) | Saves the request to a file. |
|  | [ValidateInput](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.validateinput.aspx) | 是否對透過 Cookies、Form 和 QueryString 屬性執行潛在危險之驗證 |

## Request 屬性

| 屬性 | 資料 | 說明 |
| --- | --- | --- |
| [ApplicationPath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.applicationpath.aspx) | / |  |
| [AppRelativeCurrentExecutionFilePath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.apprelativecurrentexecutionfilepath.aspx) | ~/C08\_Programming\_the\_Web\_Application/WebForm2.aspx | 取得應用程式的虛擬路徑 |
| [Browser](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.browser.aspx) | System.Web.Mobile.MobileCapabilities | 取得用戶端瀏覽器的資訊。 |
| [ClientCertificate](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.clientcertificate.aspx) | System.Web.HttpClientCertificate |  |
| [Cookies](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.cookies.aspx) | System.Web.HttpCookieCollection | 取得用戶端送出的 Cookie 的集合。 |
| [FilePath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.filepath.aspx) | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx | 目前要求的虛擬路徑。 |
| [Files](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.files.aspx) | System.Web.HttpFileCollection |  |
| [Filter](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.filter.aspx) |  |  |
| [Headers](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.headers.aspx) | Cache-Control=no-cache&Connection=Keep-Alive&Content-Length=429&Content-Type=application%2fx-www-form-urlencoded&Accept=text%2fhtml%2c+application%2fxhtml%2bxml%2c+\*%2f\*&Accept-Encoding=gzip%2c+deflate&Accept-Language=zh-TW&Cookie=ASP.NET\_SessionId%3drzsgttim0kc0sagrgm4l0nvi&Host=localhost%3a60755&Referer=http%3a%2f%2flocalhost%3a60755%2fC08\_Programming\_the\_Web\_Application%2fWebForm2.aspx&User-Agent=Mozilla%2f5.0+(compatible%3b+MSIE+9.0%3b+Windows+NT+6.1%3b+WOW64%3b+Trident%2f5.0%3b+NP06) |  |
| [HttpMethod](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.httpmethod.aspx) | POST |  |
| [IsAuthenticated](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.isauthenticated.aspx) | True |  |
| [IsLocal](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.islocal.aspx) | True |  |
| [IsSecureConnection](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.issecureconnection.aspx) | False |  |
| [LogonUserIdentity](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.logonuseridentity.aspx) | System.Security.Principal.WindowsIdentity |  |
| [Params](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.params.aspx) | | Name | Value | 說明 |&lt;br&gt;| --- | --- | --- |&lt;br&gt;| \_\_EVENTVALIDATION | /wEWEAKy+bbNBgKf7OeoAgLw/96vBwKu+JOJCwKKkYyiBAKx6YT7DQKiotE5AveptakBAvGhyJwOApnC0/UMAu7CqaMOAvTX2fcMAuPv3sAFAqfzke4GAubYlJQIArur8Z0KILtAbiO7t33VgM69Ar+iv7lOgtaa23CBQcz9pJqpbQg= |  |&lt;br&gt;| \_\_VIEWSTATE | /wEPDwUKLTc4NzI2MDczNmQYAQUeX19Db250cm9sc1JlcXVpcmVQb3N0QmFja0tleV9fFgIFDGNiSW5jbHVkZVVybAUKY2JTaG93VGV4dGOWKpza/lXYoFZ5KMT+pWaUtalW0DTOMQ0iHhxiDaw+ |  |&lt;br&gt;| ALL\_HTTP | HTTP\_CACHE\_CONTROL:no-cache  &lt;br&gt;HTTP\_CONNECTION:Keep-Alive  &lt;br&gt;HTTP\_CONTENT\_LENGTH:420  &lt;br&gt;HTTP\_CONTENT\_TYPE:application/x-www-form-urlencoded  &lt;br&gt;HTTP\_ACCEPT:text/html, application/xhtml+xml, \*/\*  &lt;br&gt;HTTP\_ACCEPT\_ENCODING:gzip, deflate  &lt;br&gt;HTTP\_ACCEPT\_LANGUAGE:zh-TW  &lt;br&gt;HTTP\_COOKIE:ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o  &lt;br&gt;HTTP\_HOST:localhost:60755  &lt;br&gt;HTTP\_REFERER:http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx  &lt;br&gt;HTTP\_USER\_AGENT:Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| ALL\_RAW | Cache-Control: no-cache  &lt;br&gt;Connection: Keep-Alive  &lt;br&gt;Content-Length: 420  &lt;br&gt;Content-Type: application/x-www-form-urlencoded  &lt;br&gt;Accept: text/html, application/xhtml+xml, \*/\*  &lt;br&gt;Accept-Encoding: gzip, deflate  &lt;br&gt;Accept-Language: zh-TW  &lt;br&gt;Cookie: ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o  &lt;br&gt;Host: localhost:60755  &lt;br&gt;Referer: http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx  &lt;br&gt;User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| APPL\_MD\_PATH |  |  |&lt;br&gt;| APPL\_PHYSICAL\_PATH | D:\myMCTS\VitoPractice\70-528\Practice70515\ |  |&lt;br&gt;| ASP.NET\_SessionId | ent0y41l14cd4yizcegchl0o |  |&lt;br&gt;| AUTH\_PASSWORD |  |  |&lt;br&gt;| AUTH\_TYPE | NTLM |  |&lt;br&gt;| AUTH\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| btnParams | Request's Params |  |&lt;br&gt;| cbShowText | on |  |&lt;br&gt;| CERT\_COOKIE |  |  |&lt;br&gt;| CERT\_FLAGS |  |  |&lt;br&gt;| CERT\_ISSUER |  |  |&lt;br&gt;| CERT\_KEYSIZE |  |  |&lt;br&gt;| CERT\_SECRETKEYSIZE |  |  |&lt;br&gt;| CERT\_SERIALNUMBER |  |  |&lt;br&gt;| CERT\_SERVER\_ISSUER |  |  |&lt;br&gt;| CERT\_SERVER\_SUBJECT |  |  |&lt;br&gt;| CERT\_SUBJECT |  |  |&lt;br&gt;| CONTENT\_LENGTH | 420 |  |&lt;br&gt;| CONTENT\_TYPE | application/x-www-form-urlencoded |  |&lt;br&gt;| GATEWAY\_INTERFACE |  |  |&lt;br&gt;| HTTP\_ACCEPT | text/html, application/xhtml+xml, \*/\* |  |&lt;br&gt;| HTTP\_ACCEPT\_ENCODING | gzip, deflate |  |&lt;br&gt;| HTTP\_ACCEPT\_LANGUAGE | zh-TW |  |&lt;br&gt;| HTTP\_CACHE\_CONTROL | no-cache |  |&lt;br&gt;| HTTP\_CONNECTION | Keep-Alive |  |&lt;br&gt;| HTTP\_CONTENT\_LENGTH | 420 |  |&lt;br&gt;| HTTP\_CONTENT\_TYPE | application/x-www-form-urlencoded |  |&lt;br&gt;| HTTP\_COOKIE | ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o |  |&lt;br&gt;| HTTP\_HOST | localhost:60755 |  |&lt;br&gt;| HTTP\_REFERER | http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| HTTP\_USER\_AGENT | Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| HTTPS |  |  |&lt;br&gt;| HTTPS\_KEYSIZE |  |  |&lt;br&gt;| HTTPS\_SECRETKEYSIZE |  |  |&lt;br&gt;| HTTPS\_SERVER\_ISSUER |  |  |&lt;br&gt;| HTTPS\_SERVER\_SUBJECT |  |  |&lt;br&gt;| INSTANCE\_ID |  |  |&lt;br&gt;| INSTANCE\_META\_PATH |  |  |&lt;br&gt;| LOCAL\_ADDR | ::1 |  |&lt;br&gt;| LOGON\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| PATH\_INFO | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| PATH\_TRANSLATED | D:\myMCTS\VitoPractice\70-528\Practice70515\C08\_Programming\_the\_Web\_Application\WebForm2.aspx |  |&lt;br&gt;| QUERY\_STRING |  |  |&lt;br&gt;| REMOTE\_ADDR | ::1 |  |&lt;br&gt;| REMOTE\_HOST | ::1 |  |&lt;br&gt;| REMOTE\_PORT |  |  |&lt;br&gt;| REMOTE\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| REQUEST\_METHOD | POST |  |&lt;br&gt;| SCRIPT\_NAME | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| SERVER\_NAME | localhost |  |&lt;br&gt;| SERVER\_PORT | 60755 |  |&lt;br&gt;| SERVER\_PORT\_SECURE | 0 |  |&lt;br&gt;| SERVER\_PROTOCOL | HTTP/1.1 |  |&lt;br&gt;| SERVER\_SOFTWARE |  |  |&lt;br&gt;| URL | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  | | 取得 QueryString、Form、Cookies 和 ServerVariables 項目的組合集合。 |
| [Path](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.path.aspx) | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx | 取得目前要求的虛擬路徑。 |
| [PathInfo](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.pathinfo.aspx) |  |  |
| [PhysicalApplicationPath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.physicalapplicationpath.aspx) | D:\myMCTS\VitoPractice\70-528\Practice70515\ | 取得目前正在執行的伺服器應用程式的根目錄之實體檔案系統路徑。 |
| [PhysicalPath](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.physicalpath.aspx) | D:\myMCTS\VitoPractice\70-528\Practice70515\C08\_Programming\_the\_Web\_Application\WebForm2.aspx | 取得對應到要求的 URL 之實體檔案系統路徑。 |
| [QueryString](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.querystring.aspx) | User=vito&Page=2 | 取得 HTTP 查詢字串變數的集合。 |
| [RawUrl](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.rawurl.aspx) | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |
| [ServerVariables](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.servervariables.aspx) | | Name | Value | 說明 |&lt;br&gt;| --- | --- | --- |&lt;br&gt;| ALL\_HTTP | HTTP\_CACHE\_CONTROL:no-cache  &lt;br&gt;HTTP\_CONNECTION:Keep-Alive  &lt;br&gt;HTTP\_CONTENT\_LENGTH:438  &lt;br&gt;HTTP\_CONTENT\_TYPE:application/x-www-form-urlencoded  &lt;br&gt;HTTP\_ACCEPT:text/html, application/xhtml+xml, \*/\*  &lt;br&gt;HTTP\_ACCEPT\_ENCODING:gzip, deflate  &lt;br&gt;HTTP\_ACCEPT\_LANGUAGE:zh-TW  &lt;br&gt;HTTP\_COOKIE:ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o  &lt;br&gt;HTTP\_HOST:localhost:60755  &lt;br&gt;HTTP\_REFERER:http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx  &lt;br&gt;HTTP\_USER\_AGENT:Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| ALL\_RAW | Cache-Control: no-cache  &lt;br&gt;Connection: Keep-Alive  &lt;br&gt;Content-Length: 438  &lt;br&gt;Content-Type: application/x-www-form-urlencoded  &lt;br&gt;Accept: text/html, application/xhtml+xml, \*/\*  &lt;br&gt;Accept-Encoding: gzip, deflate  &lt;br&gt;Accept-Language: zh-TW  &lt;br&gt;Cookie: ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o  &lt;br&gt;Host: localhost:60755  &lt;br&gt;Referer: http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx  &lt;br&gt;User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| APPL\_MD\_PATH |  |  |&lt;br&gt;| APPL\_PHYSICAL\_PATH | D:\myMCTS\VitoPractice\70-528\Practice70515\ |  |&lt;br&gt;| AUTH\_PASSWORD |  |  |&lt;br&gt;| AUTH\_TYPE | NTLM |  |&lt;br&gt;| AUTH\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| CERT\_COOKIE |  |  |&lt;br&gt;| CERT\_FLAGS |  |  |&lt;br&gt;| CERT\_ISSUER |  |  |&lt;br&gt;| CERT\_KEYSIZE |  |  |&lt;br&gt;| CERT\_SECRETKEYSIZE |  |  |&lt;br&gt;| CERT\_SERIALNUMBER |  |  |&lt;br&gt;| CERT\_SERVER\_ISSUER |  |  |&lt;br&gt;| CERT\_SERVER\_SUBJECT |  |  |&lt;br&gt;| CERT\_SUBJECT |  |  |&lt;br&gt;| CONTENT\_LENGTH | 438 |  |&lt;br&gt;| CONTENT\_TYPE | application/x-www-form-urlencoded |  |&lt;br&gt;| GATEWAY\_INTERFACE |  |  |&lt;br&gt;| HTTP\_ACCEPT | text/html, application/xhtml+xml, \*/\* |  |&lt;br&gt;| HTTP\_ACCEPT\_ENCODING | gzip, deflate |  |&lt;br&gt;| HTTP\_ACCEPT\_LANGUAGE | zh-TW |  |&lt;br&gt;| HTTP\_CACHE\_CONTROL | no-cache |  |&lt;br&gt;| HTTP\_CONNECTION | Keep-Alive |  |&lt;br&gt;| HTTP\_CONTENT\_LENGTH | 438 |  |&lt;br&gt;| HTTP\_CONTENT\_TYPE | application/x-www-form-urlencoded |  |&lt;br&gt;| HTTP\_COOKIE | ASP.NET\_SessionId=ent0y41l14cd4yizcegchl0o |  |&lt;br&gt;| HTTP\_HOST | localhost:60755 |  |&lt;br&gt;| HTTP\_REFERER | http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| HTTP\_USER\_AGENT | Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) |  |&lt;br&gt;| HTTPS |  |  |&lt;br&gt;| HTTPS\_KEYSIZE |  |  |&lt;br&gt;| HTTPS\_SECRETKEYSIZE |  |  |&lt;br&gt;| HTTPS\_SERVER\_ISSUER |  |  |&lt;br&gt;| HTTPS\_SERVER\_SUBJECT |  |  |&lt;br&gt;| INSTANCE\_ID |  |  |&lt;br&gt;| INSTANCE\_META\_PATH |  |  |&lt;br&gt;| LOCAL\_ADDR | ::1 |  |&lt;br&gt;| LOGON\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| PATH\_INFO | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| PATH\_TRANSLATED | D:\myMCTS\VitoPractice\70-528\Practice70515\C08\_Programming\_the\_Web\_Application\WebForm2.aspx |  |&lt;br&gt;| QUERY\_STRING |  |  |&lt;br&gt;| REMOTE\_ADDR | ::1 |  |&lt;br&gt;| REMOTE\_HOST | ::1 |  |&lt;br&gt;| REMOTE\_PORT |  |  |&lt;br&gt;| REMOTE\_USER | VITO-2011W7\Administrator |  |&lt;br&gt;| REQUEST\_METHOD | POST |  |&lt;br&gt;| SCRIPT\_NAME | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  |&lt;br&gt;| SERVER\_NAME | localhost |  |&lt;br&gt;| SERVER\_PORT | 60755 |  |&lt;br&gt;| SERVER\_PORT\_SECURE | 0 |  |&lt;br&gt;| SERVER\_PROTOCOL | HTTP/1.1 |  |&lt;br&gt;| SERVER\_SOFTWARE |  |  |&lt;br&gt;| URL | /C08\_Programming\_the\_Web\_Application/WebForm2.aspx |  | | 取得 Web 伺服器變數的集合。 |
| [TotalBytes](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.totalbytes.aspx) | 429 |  |
| [Url](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.url.aspx) | http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx | 目前要求的 URL 資訊。 |
| [UrlReferrer](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.urlreferrer.aspx) | http://localhost:60755/C08\_Programming\_the\_Web\_Application/WebForm2.aspx | 前一個要求之 UR 資訊。L |
| [UserAgent](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.useragent.aspx) | Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; NP06) | 取得用戶端瀏覽器的字串資訊 |
| [UserHostAddress](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.userhostaddress.aspx) | ::1 |  |
| [UserHostName](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.userhostname.aspx) | ::1 |  |
| [UserLanguages](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.userlanguages.aspx) | System.String[] |  |

# Server

## Server 方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [ClearError](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.clearerror.aspx) |  |
|  | [CreateObject](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.createobject.aspx) |  |
|  | [HtmlDecode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.htmldecode.aspx) |  |
|  | [HtmlEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.htmlencode.aspx) |  |
|  | [MapPath](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.mappath.aspx) |  |
|  | [Transfer](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.transfer.aspx) |  |
|  | [UrlDecode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.urldecode.aspx) |  |
|  | [UrlEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.urlencode.aspx) |  |
|  | [UrlPathEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.urlpathencode.aspx) |  |
| S | [UrlTokenDecode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.urltokendecode.aspx) |  |
| S | [UrlTokenEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.urltokenencode.aspx) |  |

## Server 屬性

| 屬性 | 資料 | 說明 |
| --- | --- | --- |
| [MachineName](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.machinename.aspx) | VITO-2011W7 |  |
| [ScriptTimeout](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.scripttimeout.aspx) | 30000000 |  |

# Context 物件

The [Page.Context](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.context.aspx) property is an [HttpContext](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.aspx) object that provides access to a variety of objects related to the HTTP request and response.   Many of these objects are redundant, providing access to Page members including Cache, Request, Response, Server, and Session.   However, the [Context](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.context.aspx) object includes several unique methods:  

## Context 方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [AddError](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.adderror.aspx) |  |
|  | [ClearError](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.clearerror.aspx) |  |
|  | [RewritePath](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.rewritepath.aspx) | 使用指定的路徑重寫 URL。 |

## Context 屬性

| 屬性 | 資料 | 說明 |
| --- | --- | --- |
| [AllErrors](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.allerrors.aspx) |  |  |
| [IsCustomErrorEnabled](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.iscustomerrorenabled.aspx) | True |  |
| [IsDebuggingEnabled](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.isdebuggingenabled.aspx) | True |  |
| [Timestamp](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.timestamp.aspx) | 2012/10/2 上午 11:52:29 |  |

# Browser

取得瀏覽器相關的資訊

## Request.Browser 的方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [AddBrowser](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.addbrowser.aspx) |  |
|  | [GetClrVersions](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.getclrversions.aspx) | 傳回安裝在用戶端的所有 .NET Framework CLR 版本。 |
|  | [IsBrowser](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.isbrowser.aspx) | 用戶端瀏覽器是否與指定瀏覽器相同。 |

## Request.Browser 的屬性

| 屬性 | 資料 | 說明 |
| --- | --- | --- |
| [ActiveXControls](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.activexcontrols.aspx) | True | 是否支援 ActiveX |
| [AOL](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.aol.aspx) | False | 是否是 [AOL](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.aol.aspx) (America Online) 瀏覽器 |
| [BackgroundSounds](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.backgroundsounds.aspx) | True | 是否支援 &lt;bgsounds&gt;標籤 |
| [Browser](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.browser.aspx) | IE |  |
| [Browsers](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.browsers.aspx) | System.Collections.ArrayList |  |
| [ClrVersion](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.clrversion.aspx) | 0.0 | 取得用戶端安裝的 .NET Framework 版本 |
| [Cookies](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.cookies.aspx) | True | 是否支援 Cookie |
| [Crawler](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.crawler.aspx) | False | 是否為 Web [Crawler](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.crawler.aspx) 搜尋引擎 |
| [Frames](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.frames.aspx) | True | 是否支援 HTML 框架 |
| [IsColor](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.iscolor.aspx) | True |  |
| [IsMobileDevice](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.ismobiledevice.aspx) | False |  |
| [Item](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.item.aspx) | ... |  |
| [JavaApplets](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.javaapplets.aspx) | True |  |
| [JavaScript](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.javascript.aspx) | True |  |
| [JScriptVersion](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.jscriptversion.aspx) | 5.6 |  |
| [MobileDeviceManufacturer](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.mobiledevicemanufacturer.aspx) | Unknown |  |
| [MobileDeviceModel](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.mobiledevicemodel.aspx) | Unknown |  |
| [MSDomVersion](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.msdomversion.aspx) | 9.0 |  |
| [NumberOfSoftkeys](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.numberofsoftkeys.aspx) | 0 |  |
| [Platform](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.platform.aspx) | WinNT |  |
| [Tables](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.tables.aspx) | True |  |
| [VBScript](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.vbscript.aspx) | True |  |
| [Version](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.version.aspx) | 9.0 | 取得瀏覽器完整版本號碼 |
| [W3CDomVersion](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.w3cdomversion.aspx) | 1.0 |  |
| [Win16](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.win16.aspx) | False |  |
| [Win32](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.httpcapabilitiesbase.win32.aspx) | True |  |

# Page.Header

## Page.Header 方法

|  | 方法 | 說明 |
| --- | --- | --- |
|  | [ApplyStyleSheetSkin](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.applystylesheetskin.aspx) |  |
|  | [GetRouteUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.getrouteurl.aspx) |  |

## Page.Header 屬性

| 屬性 | 說明 | 資料 |
| --- | --- | --- |
| [Style](http://msdn.microsoft.com/zh-tw/library/system.web.ui.htmlcontrols.htmlcontrol.style.aspx) | 取得樣式表屬性集合 | System.Web.UI.CssStyleCollection |
| [StyleSheet](http://msdn.microsoft.com/zh-tw/library/system.web.ui.htmlcontrols.htmlhead.stylesheet.aspx) | 設定網頁樣式 | ```c#
Style bodystyle = new Style();<br>bodystyle.ForeColor = System.Drawing.Color.Blue;<br>bodystyle.BackColor = System.Drawing.Color.LightCyan;<br>Page.Header.StyleSheet.CreateStyleRule(bodystyle,null,"body");<br>```&lt;br&gt;  &lt;br&gt;以上程式碼，將在&lt;head&gt;區塊裡，產生以下樣式&lt;br&gt;  &lt;br&gt;```xml
<style type="text/css">
body { color:Blue;background-color:LightCyan; }<br></style><br>``` |
| [Title](http://msdn.microsoft.com/zh-tw/library/system.web.ui.htmlcontrols.htmlhead.title.aspx) | 設定網頁標題 | ```c#
Page.Header.Title = "Current tim: " + DateTime.Now;<br>``` |
| [ViewStateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.viewstatemode.aspx) |  | Inherit |
| [Visible](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.visible.aspx) |  | True |