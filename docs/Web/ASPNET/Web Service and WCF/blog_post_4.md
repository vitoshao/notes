---
title: 建立 RESTful WCF 服務網站
layout: default
parent: Web Service and WCF
nav_order: 4
description: "建立 RESTful WCF 服務網站"
date: 2012-11-09
tags: [Web,ASPNET,Web Service and WCF]
postid: "7798081579147741012"
---
# 什麼是 REST

## REST (Representational State Transfer, 表徵狀態轉移)

REST 是一種軟體架構風格，希望網路上資源都可以用 URL 來指定，讓客戶端可以直接透過 URL 對資源進行 CRUD （創建、獲取、修改、刪除）操作。  RE 是 Representational 的縮寫，它代表由 Client 端透過 URI 取得的資源的具體象徵 (Representational)，  應用程式可以依據取得的具體象徵來轉變其狀態（在瀏覽器上就是畫面的變化），  這樣不斷的反覆過程就是所謂的 Representational State Transfer。  [維基百科：REST](http://zh.wikipedia.org/wiki/REST)

## JSON (Javascript Object Notation)

JSON 是一種以純文字為基礎的資料格式。它也是 REST 服務預設的訊息回應格式。  因為它的結構簡單，很適合用來做為程式溝通或交換資料時使用。  也由於它的輕量化和易於閱讀的特性，目前已廣泛應用於各種技術領域，例如：AJAX, WCF, jQuery。  

你可以透過特定的格式去儲存任何資料(字串,數字,陣列,物件)，也可以透過物件或陣列來傳送較複雜的資料。  底下是一個簡單的 JSON 範例：  
```c#
{
"proudctName": "Computer Monitor",
"price": "229.00",
"specifications": 
{
"size": 22,
"type": "LCD",
"colors": ["black", "red", "white"]
}
}
```

# 建立 REST WCF Service

用來建置 Windows Communication Foundation (WCF) 服務與用戶端應用程式時所需的型別，都包含在 [System.ServiceModel](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.aspx) 命名空間。  底下幾個相關的命名空間：  

- [System.ServiceModel](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.aspx) ： 提供與服務模型相關的類別。
- [System.ServiceModel.Activation](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.activation.aspx) ： 提供與服務模型啟動相關的類別。
- [System.ServiceModel.Web](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.aspx) ： 提供使用 web 服務模型與相關的類別。

要設計 REST 服務，必須引用 [System.ServiceModel.Web](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.aspx) 命名空間。  
該命名空間定義在 System.ServiceModel.Web.dll 組件裡，必須手動加入參考。  
必須注意的是，Framework 版本必須選用**完整版**（如3.5或4.0 full profile），若選用 client profile 會找不到這個 dll。  

## 建立支援 REST 的 WCF 服務

由於.NET 3.5 的支援，我們可以透過相當簡單的方式來建立 REST 的 WCF 服務，若開發人員想透過WCF技術，來開發 [RESTful](http://msdn.microsoft.com/zh-tw/magazine/dd315413.aspx) 的 Service，只需要在VS2008 SP1的專案範本中，選擇 「AJAX-enabled WCF Service」 即可：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRVrsg0sxIRRJdl-zjXuUnUhCD7xOYWwFgDgRGU0hpH0TF6H02xikSdRrzwwIrRBSorxO1mhy-SgIv-5pJI8MtpPXd3AtgC_ItwGv5SfTqGrLuQUexuIidWR9atrTsoFBuW6O_CmkP4vU/s525/wcf-ajax-enabled-template.png)

#### 「AJAX-enabled WCF Service」項目的內容

底下是一個 「AJAX-enabled WCF Service」的簡單範例，當新增一個 「AJAX-enabled WCF Service」項目時，下面幾點注意事項：  

- 會自動加入 [AspNetCompatibilityRequirements](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.activation.aspnetcompatibilityrequirementsattribute.aspx) 屬性。  
若設定成允許 ASP.NET相容性模式，則 WCF 服務會與 ASP.NET 使用相同的管線，類似於 ASMX 服務的方式。  在此模式下， WCF 服務也可提供 ASP.NET 檔案授權、 UrlAuthorization 和 HTTP 工作階段狀態等功能。
- 預設的 [ResponseFormat](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.webinvokeattribute.responseformat.aspx) 為 [WebMessageFormat.Json](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.webmessageformat.aspx) 。  
若要傳回 XML ，必須加入 [WebGet(ResponseFormat=WebMessageFormat.Xml)] 屬性宣告。
- [WebInvoke](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.webinvokeattribute.aspx) ：這屬性可用來設定 HTTP 動詞（例如：POST, PUT, DELETE），預設使用 HTTP POST 方式叫用。
- [WebGet](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.web.webgetattribute.aspx) ：這屬性是用來設定使用 HTTP GET 叫用。這種方法的回傳結果支援快取。
- 在第３行的 Namespace 中要設定一個值。
```c#
namespace WcfService2
{
[ServiceContract(Namespace = "WcfService2")]
[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
public class HRService
{
//一個方法若沒有特別宣告屬性，預設值有：
//1. 必須使用 HTTP POST 叫用
//2. 回傳 Json 格式

[OperationContract]
public Employee Employee1(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID, FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}

[OperationContract]
[WebGet]
public Employee Employee1(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID, FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}

[OperationContract]
[WebGet]
public Employee Employee2(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID, FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}
}

public class Employee
{
public string EmployeeID { get; set; }
public string FirstName { get; set; }
public string LastName { get; set; }
public int Age { get; set; }
}
}
```

#### 「AJAX-enabled WCF Service」項目的組態設定

在這個設定內容裡，與前一節中的 WCF 服務最大的不同在於：  

- 端點行為中加入 **enableWebScript** 屬性。它表示這個 endpoint 是一個支援 **JSON** 資料格式的 **RESTful** 服務，因此可以透過 AJAX 直接叫用。
- 端點的繫結方式採用 **webHttpBinding**。它指出該服務必須透過 HTTP 呼叫，而不是透過 SOAP 呼叫。
```xml
<configuration>

<system.serviceModel>
<serviceHostingEnvironment aspNetCompatibilityEnabled="true" multipleSiteBindingsEnabled="true" />

<behaviors>
<endpointBehaviors>
<behavior name="WcfService2.HRServiceAspNetAjaxBehavior">
<enableWebScript />
</behavior>
</endpointBehaviors>
</behaviors>

<services>
<service name="WcfService2.HRService">
<endpoint address="" 
binding="webHttpBinding" 
contract="WcfService2.HRService" 
behaviorConfiguration="WcfService2.HRServiceAspNetAjaxBehavior"/>
</service>
</services>
</system.serviceModel>

</configuration>
```

# 叫用 REST WCF Service

## 透過 ASP.NET AJAX ScriptManager 叫用 REST WCF Service

不曉得為什麼，在 ScriptManager 底下叫用 WCF 服務，該服務合約的 namespace 屬性值就必須指定為類別的命名空間，才可以成功呼叫。
```c#
namespace WcfService2
{
[ServiceContract(Namespace = "WcfService2")]
[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
public class HRService
{

}
｝
```

其用戶端的叫用方法如下：  
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function Button1_onclick() {
var service = new WcfService2.HRService();
var resutl = service.GetEmployee('001', onSuccess, onFail, null);
}
function onSuccess(result) {
alert(result.FirstName + ' ' + result.LastName);
}
function onFail(result) {
alert(result);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Services>         
<asp:ServiceReference Path="HRService.svc" />       
</Services> 
</asp:ScriptManager>

<input id="Button1" type="button" value="invoke .svc with ajax" onclick="return Button1_onclick()" />
</div>
</form>
</body>
```

## 透過 jQuery 叫用 REST WCF Service

很奇怪，若使用 jQuery 叫用，該服務合約就不用指定 namespace 屬性。

其用戶端的叫用方法如下：
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="./Scripts/jquery-1.8.2.js"></script>

<script language="javascript" type="text/javascript">
function btnAjax_Post_onclick() {
jQuery.support.cors = true;

var empid = $("#txtEmpID").val();
var data = '{"EmpID":"' + empid + '"}';
var uri = "http://localhost:30302/HRService.svc/Employee1";

$.ajax({
type: "POST",
url: uri,
dataType: "json",
data: data,
contentType: "application/json; charset=utf-8",
success: OnSuccess,
error: OnFail
});
}

function btnAjax_Get_onclick() {
jQuery.support.cors = true;
var empid = $("#txtEmpID").val();
var uri = "http://localhost:30302/HRService.svc/Employee2?EmpID=" + empid;

$.ajax({
type: "GET",
url: uri,
dataType: "json",
contentType: "application/json; charset=utf-8",
success: OnSuccess,
error: OnFail
});
}
function OnSuccess(result) {
alert(result.d.EmployeeID + ' ' + result.d.FirstName + ' ' + result.d.LastName);
}
function OnFail(result) {
alert('Service call failed: ' + result.status + '' + result.statusText);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
EmpID: <input id="txtEmpID" type="text" value="007" /><br /><br />

<input id="btnAjax_Post" type="button" value="invoke .svc with ajax post" onclick="return btnAjax_Post_onclick()"/><br /><br />

<input id="btnAjax_Get" type="button" value="invoke .svc with ajax get" onclick="return btnAjax_Get_onclick()" /><br /><br />
</div>
</form>
</body>
```

底下是幾個 jQuery 用來叫用服務的方法：

- [jQuery.ajax](http://api.jquery.com/jQuery.ajax/) ：Perform an asynchronous HTTP (Ajax) request.
- [jQuery.getJSON](http://api.jquery.com/jQuery.getJSON/) ：Load JSON-encoded data from the server using a HTTP GET request.
- [jQuery.post](http://api.jquery.com/jQuery.post/) ：Load data from the server using a HTTP POST request.
- [jQuery.get](http://api.jquery.com/jQuery.get/) ：Load data from the server using a HTTP GET request.
```javascript
jQuery.ajax( url [, settings] )

jQuery.getJSON( url [, data] [, success(data, textStatus, jqXHR)] )
//Returns: jqXHR

jQuery.post( url [, data] [, success(data, textStatus, jqXHR)] [, dataType] )
//Returns: jqXHR

jQuery.get( url [, data ] [, success(data, textStatus, jqXHR) ] [, dataType ] )
//Returns: jqXHR
```

要注意一點，在 jQuery 1.4X 以後，叫用 jQuery 執行方法時，預設就不允許 cross-site 。如果要執行跨網站的方法，必須加入以下程式碼：
```javascript
jQuery.support.cors = true;
```

## 套用 UriTemplate 叫用 REST WCF Service

#### 使用 webHttp 端點行為

要使用 [UriTemplate](http://msdn.microsoft.com/zh-tw/library/system.uritemplate.aspx) 必須使用 **&lt;webHttp&gt;** 端點行為，而不是使用 **&lt;enableWebScript&gt;** 端點行為。
```xml
<configuration>

<system.serviceModel>
<serviceHostingEnvironment aspNetCompatibilityEnabled="true" multipleSiteBindingsEnabled="true" />

<behaviors>
<endpointBehaviors>
<behavior name="WcfService3.HRServiceAspNetAjaxBehavior">
<webHttp helpEnabled="true" />
</behavior>
</endpointBehaviors>
</behaviors>

<services>
<service name="WcfService3.HRService">
<endpoint address="" 
binding="webHttpBinding" 
contract="WcfService3.HRService" 
behaviorConfiguration="WcfService3.HRServiceAspNetAjaxBehavior"/>
</service>
</services>
</system.serviceModel>

</configuration>
```

#### 定義 UriTemplate

[UriTemplate](http://msdn.microsoft.com/zh-tw/library/system.uritemplate.aspx) 是指這一個 [RESTful](http://msdn.microsoft.com/zh-tw/magazine/dd315413.aspx) 服務的呼叫方式(嚴格說起來是描述該資源的Uri位置)，是要以何種 Uri 來表達，透過這種方式建立出來的 WCF 服務，就直接支援了 REST 型態的遠端呼叫方式。  例如，上例中我們給定的『UriTemplate』是"HR/EmpId/{EmpID}"，則這個資源可以透過底下的網址來取得：  
http://localhost:30303/HRService.svc/HR/EmpId/001  
```c#
namespace WcfService3
{
[ServiceContract]
[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
public class HRService
{
[OperationContract]
[WebInvoke(
Method = "POST",
UriTemplate = "HR/EmpId/{EmpID}",
RequestFormat = WebMessageFormat.Json, 
ResponseFormat = WebMessageFormat.Json, 
BodyStyle = WebMessageBodyStyle.WrappedRequest)]
public Employee Employee1(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID+"post", FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}

[OperationContract]
[WebGet(
UriTemplate = "HR/EmpId/{EmpID}",
RequestFormat = WebMessageFormat.Json, 
ResponseFormat = WebMessageFormat.Json, 
BodyStyle = WebMessageBodyStyle.WrappedRequest)]
public Employee Employee2(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID + "get", FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}
}

public class Employee
{
public string EmployeeID { get; set; }
public string FirstName { get; set; }
public string LastName { get; set; }
public int Age { get; set; }
}
}
```

#### 使用 jQuery 方法叫用服務
```javascript
<script language="javascript" type="text/javascript">            function btnAjax_Post_onclick() {            jQuery.support.cors = true;
var empid = $("#txtEmpID").val();              var data = '{"EmpID":"' + empid + '"}';              var uri = "http://localhost:30303/HRService.svc/HR/EmpId/" + empid;                $.ajax({                  type: "POST",                  url: uri,                  dataType: "json",                  data: data,                  contentType: "application/json; charset=utf-8",                  success: OnSuccess,                  error: OnFail              });          }            function btnAjax_Get_onclick() {            jQuery.support.cors = true;
var empid = $("#txtEmpID").val();              var uri = "http://localhost:30303/HRService.svc/HR/EmpId/" + empid;                $.ajax({                  type: "GET",                  url: uri,                  dataType: "json",                  contentType: "application/json; charset=utf-8",                  success: OnSuccess,                  error: OnFail              });          }            function btnPost_onclick() {            jQuery.support.cors = true;
var empid = $("#txtEmpID").val();              var uri = "http://localhost:30303/HRService.svc/HR/EmpId/" + empid;                $.post(uri, null, OnSuccess);          }            function btnGet_onclick() {            jQuery.support.cors = true;
var empid = $("#txtEmpID").val();              var uri = "http://localhost:30303/HRService.svc/HR/EmpId/" + empid;                $.get(uri, null, OnSuccess);          }            function OnSuccess(result) {              alert(result.EmployeeID + ' ' + result.FirstName + ' ' + result.LastName);          }          function OnFail(result) {              alert('Service call failed: ' + result.status + '' + result.statusText);          }        </script>  ```
## 參考資料  

- [\[王寧疆老師線上教學\] 實作 Restful 服務 – 為 WCF 4.0 服務加入 REST 介面](http://www.youtube.com/watch?v=j6va-vXH4-M&list=PL9901020A0FA51A2C&index=10&feature=plpp_video)
- [\[王寧疆老師線上教學\] 實作 WCF 資料服務](http://www.youtube.com/watch?v=OQn3_seD5yw&list=PL9901020A0FA51A2C&index=11&feature=plpp_video)
- [\[王寧疆老師線上教學\] 實作使用 Restful 服務的 Windows Phone 智慧型手機程式](http://www.youtube.com/watch?v=uY8Y9GN80I8&list=PL9901020A0FA51A2C&index=12&feature=plpp_video)