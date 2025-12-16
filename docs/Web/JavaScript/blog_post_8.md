---
title: jQuery Ajax
layout: default
parent: JavaScript
nav_order: 8
description: "jQuery Ajax"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "2728968968878423324"
---
AJAX 代表 Asynchronous Javascript And Xml ，其中的 Asynchronous 意味著不採行傳統 Postback 整個網頁表單送回伺服器的做法，而是透過 XmlHttpRequest 物件與伺服器溝通，再以 Javascript 解析伺服器端所傳回的資料。這種做法，避免了傳統Postback期間，網頁會歷經消失、等待、重新建立的過程，給予使用者更流暢的操作感受。  

jQuery 對與後端溝通的相關作業提供了一系統的函數，其中最基層的函數叫做 [$.ajax()](http://api.jquery.com/jQuery.ajax/) 。  它也有一系列的衍生函數，如 [jQuery.get()](http://api.jquery.com/jQuery.get/) 、 [jQuery.post()](http://api.jquery.com/jQuery.post/) 、 [jQuery.getScript()](http://api.jquery.com/jQuery.getScript/) 、 [jQuery.getJSON()](http://api.jquery.com/jQuery.getJSON/) ，可以用更精簡的方法使用在特定的需求上。  通常這些方法以滿足大部份需求，除非你有需要進一步控制整個溝通細節，才需回頭使用 [jQuery.ajax()](http://api.jquery.com/jQuery.ajax/) 。  

# Calling a Web Service with $.ajax()

The [$.ajax()](http://api.jquery.com/jQuery.ajax/) method enables you to call a web service and then update your page with the results.   This method performs an **asynchronous HTTP request**, passes data to the request, and gets the results.  

This method takes several name-value pairs as a parameter, called settings.   The settings include the URL of your request, the content-type, the HTTP method (GET or POST), and the user name and password of the request.   In fact, there are more than 20 settings with which you can work.  

## 看範例先

### 如何使用 javascript 取得 server 上的資料

在使用 jQuery 呼叫 web service 之前，我們先來回顧一下如何使用 javascript 叫用 Web Service 。
```javascript
<script type="text/javascript">
var http_request;

function GetData() {
http_request = new XMLHttpRequest();
if (http_request) {
http_request.onreadystatechange = FinishFunction;
http_request.open('GET', 'DataPage.aspx', true);    //使用非同步呼叫
http_request.send(null);
}
}
function FinishFunction() {
if (http_request.readyState == 4 && http_request.status == 200) {
$("#hello").text(http_request.responseText);
}
}
</script>
```

### 如何使用 $.ajax() 方法取得 server 上的資料
```javascript
<script type="text/javascript">
$(function () {
$("#btnAjax").click(function () {
$.ajax({
type: "get",
url: "DataPage.aspx",
success: function (result) { SuccessHandler(result); }
});
});
function SuccessHandler(result) {
$("#hello").text(result);
}
});
</script>
```

### 如何使用 jQuery.get() 方法取得 server 上的資料
```javascript
<script type="text/javascript">
$(function () {
$("#btnGet").click(function () {
$.get("DataPage.aspx", null, SuccessHandler);
});
function SuccessHandler(result) {
$("#hello").text(result);
}
});
</script>
```

### 如何使用 .load() 方法取得 server 上的資料
```javascript
<script type="text/javascript">
$(function () {
$("#btnLoad").click(function () {
$("#hello").load("DataPage.aspx");
});
});
</script>
```

## 使用 $.ajax() 叫用 Web Service

The [$.ajax()](http://api.jquery.com/jQuery.ajax/) method enables you to call a web service and then update your page with the results.  This method takes several name-value pairs as a parameter, called settings.   The settings include the URL of your request, the content-type, the HTTP method ...  

####  [$.ajax()](http://api.jquery.com/jQuery.ajax/) 的定義：
```javascript
jQuery.ajax( url [, settings ] )
jQuery.ajax( [ settings ] )
```

其中的 settings 包括：

- url ：Location of the service。
- type ：要求的型態。[GET | POST | PUT  | DELETE] verb
- contentType ：傳送給 Server 的資料型態。[application/json | application/xml]
- dataType ：預期 Server 回傳的資料型態。[json | xml]
- data ：傳送給 Server 的資料。
- data ：傳送給 Server 的資料。
- success ：A function to be called if the request succeeds.
- error ：A function to be called if the request fails.
- username ：A username to be used in response to an HTTP access authentication request.
- password ：A password to be used in response to an HTTP access authentication request.

底下，先來看個簡單例子：
```javascript
<script type="text/javascript">
$(function () {
jQuery.support.cors = true; // for cross domain

$("#Button1").click(function () {

var url = "../WebService1.asmx/Hello2";
var data = "{'name': 'vito'}";

$.ajax({
type: "POST",
dataType: "json",
contentType: "application/json",
url: url,
data: data,
success: SuccessHandler,
error: ErrorHandler
});
});

function SuccessHandler(data, textStatus, jqXHR) {
alert('data = ' + data.d + '\r\n' +
'status = ' + textStatus + '\r\n' +
'readyState = ' + jqXHR.readyState);
}
function ErrorHandler(jqXHR, textStatus, errorThrown) {
alert('readyState = ' + jqXHR.readyState + '\r\n' +
'status = ' + textStatus + '\r\n' +
'error = ' + errorThrown);
}
});
</script>
```
```c#
[WebService(Namespace = "http://tempuri.org/")]
[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
[System.ComponentModel.ToolboxItem(false)]
[System.Web.Script.Services.ScriptService]
public class WebService1 : System.Web.Services.WebService
{
[WebMethod]
public string Hello(string name)
{
return "Hello " + name;
}
}
```

settings 是由 key/value 組成的集合，所有設定項目都是 optional，幾個項目的說明如下：（注意大小寫）

- url ：指定要進行呼叫的位址
- type ：請求方式，POST/GET。
- data ：要傳給server的參數值，使用Key/value對，它為被附加在網址後頭
- contentType ：傳送至Server的編碼類型 [application/json |application/xml ]
- dataType ：預期Server傳回的資料類型，如果沒指定，jQuery會根據 HTTP MIME [Type](http://msdn.microsoft.com/zh-tw/library/bb310818.aspx)自動選擇以 responseXML 或 responseText 傳入你的 success callback。
- xml：
- html：
- script：
- json：
- jsonp：
- text：
- success ：請求成功時執行函式。
- error ：請求發生錯誤時執行函式。
- global
- 

### retrive xml data
```c#
XmlDocument xd = new XmlDocument();
xd.LoadXml(@"
<xml version='1.0' >
<users>
<user id='first'>
<name>Name of first user</name>
<email>first@contoso.com</email>
</user>
<user id='second'>
<name>Name of second user</name>
<email>second@contoso.com</email>
</user>
</users>
</xml>
");
Response.ContentType = "text/xml";
Response.Write(xd.OuterXml);
```
```xml
<input id="Button7" type="button" value="retrive xml data using $.ajax()" /><br />
<select id="Select1">
</select>
```
```javascript
<script type="text/javascript">
$(function () {
$("#Button7").click(function () {
$.ajax({
type: "get",
url: "DataPage.aspx?type=xml",
dataType: "xml",
success: function (result) { SuccessHandler(result); }
});
});
function SuccessHandler(result) {
$(result).find("user").each(function () {
var id = $(this).attr("id");
var name = $(this).find("name").text();
$("<option>").attr("value", id).text(name).appendTo("#Select1");
});
}

$("#Select1").change(function () {
alert($("#Select1").val());
});
});
</script>
```

### retrive json data
```c#
string data = "[{\"id\":\"first\" ,\"name\":\"vito\",\"email\":\"vito@gmail.com\"},";
data += "{\"id\":\"second\",\"name\":\"shao\",\"email\":\"shao@gmail.com\"}]";

Response.ContentType = "text/plain";
Response.Write(data);
```
```xml
<input id="Button8" type="button" value="retrive json data using $.ajax()" /><br />
<select id="Select1">
</select>
```
```javascript
<script type="text/javascript">
$(function () {
$("#Button8").click(function () {
$.ajax({
type: "get",
url: "DataPage.aspx?type=json",
dataType: "json",
success: function (result) { SuccessHandler(result); }
});
});
function SuccessHandler(result) {
$.each(result, function () {
var id = this.id;
var name = this.name;
$("<option>").attr("value", id).text(name).appendTo("#Select1");
});
}
});
</script>
```

## 使用 $.ajax() 叫用 WebPage

$.ajax() 方法，也可以用來叫用網頁中的方法，如此一來，你就不用特地再使用 web service 才能取得資料，而且叫用方法和上節一模一樣，  
```c#
[WebMethod]
public static string Hello(string name)
{
return "Hello " + name + " \r\n" + DateTime.Now.ToString();
}
```
```javascript
<script type="text/javascript">
$(function () {
$("#Button2").click(function () {
var url = "WebForm9.aspx/Hello";
var data = "{'name': 'vito'}";

$.ajax({
type: "POST",
dataType: "json",
contentType: "application/json",
url: url,
data: data,
success: function (result) { SuccessHandler(result); },
error: ErrorHandler
});
});
function SuccessHandler(result) {
alert(result.d);
}
function ErrorHandler(result) {
alert(result.status + ' ' + result.statusText);
}
});
</script>
```

Notice that, in this case, you do not need to use the ScriptService attribute.   ASP.NET assumes that code marked as a WebMethod inside a page is meant to be called from AJAX.

# 其他與 Ajax 相關的 jQuery 方法

The jQuery library exposes some additional jQuery methods that allow you to work with AJAX.   These methods act as shorthand methods to the .ajax() method.  

- [jQuery.get()](http://api.jquery.com/jQuery.get/) ：Load data from the server using a HTTP GET request.
- [jQuery.post()](http://api.jquery.com/jQuery.post/) ：Load data from the server using a HTTP POST request.
- [jQuery.getScript()](http://api.jquery.com/jQuery.getScript/) ：Load a JavaScript file from the server using a GET HTTP request, then execute it.
- [jQuery.getJSON()](http://api.jquery.com/jQuery.getJSON/) ：Load JSON-encoded data from the server using a GET HTTP request.
- [.load()](http://api.jquery.com/load/) ：Load data from the server and place the returned HTML into the matched element.
- [.ajaxSend()](http://api.jquery.com/ajaxSend/) ：Attach a function to be executed before an Ajax request is sent. This is an Ajax Event.
- [.ajaxSuccess()](http://api.jquery.com/ajaxSuccess/) ：Attach a function to be executed whenever an Ajax request completes successfully. This is an Ajax Event.
- [.ajaxStart()](http://api.jquery.com/ajaxStart/) ：Register a handler to be called when the first Ajax request begins. This is an Ajax Event.
- [.ajaxStop()](http://api.jquery.com/ajaxStop/) ：Register a handler to be called when all Ajax requests have completed. This is an Ajax Event.

### jQuery.get()
```javascript
jQuery.get( url [, data ] [, success(data, textStatus, jqXHR) ] [, dataType ] )
```

Load data from the server using a HTTP GET request.
```javascript
var url = "DataPage.aspx";
var param = "{'id': '1'}";

$.get(
url,
param,
function (result) { alert(result[0].name); }, 
"json");
```

### jQuery.post()
```javascript
jQuery.post( url [, data ] [, success(data, textStatus, jqXHR) ] [, dataType ] )
```

Load data from the server using a HTTP POST request.
```javascript
var url = "DataPage.aspx";
var param = "{'id': '1'}";

$.post(
url,
param,
function (result) { alert(result[0].name); }, 
"json");
```

### .load()
```javascript
.load( url [, data ] [, complete(responseText, textStatus, XMLHttpRequest) ] )
```

Load data from the server and place the returned HTML into the matched element.
```javascript
var url = "DataPage.aspx";
var param = "{'id': '1'}";
$("#div1").load(url, param);
```