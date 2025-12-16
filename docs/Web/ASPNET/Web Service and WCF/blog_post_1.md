---
title: XML Web Service
layout: default
parent: Web Service and WCF
nav_order: 1
description: "XML Web Service"
date: 2012-10-26
tags: [Web,ASPNET,Web Service and WCF]
postid: "1072814322778261386"
---
Web Service 是一種以服務為導向的架構技術，透過標準的 Web 協議提供服務，目的是讓不同平台的應用服務可以互動操作。  根據W3C的定義，Web服務（Web service）應當是一個軟體系統，用以支持網路間不同機器的互動操作。  網路服務通常是許多應用程式介面（API）所組成的，它們透過網路上的遠端伺服機，執行客戶所提交服務的請求。  

Web Service 透過 WSDL 文件提供該服務的定義描述。  Web Service 採用 SOAP 協議進行 XML 格式的訊息傳遞。  SOAP 協議必須繫結到某個傳輸協定，這個傳輸協定通常是 http, https, 但也可以是 stmp 或 xmpp.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguy_gps8esuzN88Erji23fqc6wElWGJRhCFPuQHCGSZ5xQhniOoZ_rvgDu90aY3s92n8XZobmhChtdoC8IORJQmF_2kygFd9DseEcGpc1Xx00ZaLZ0G1IK256bGCbQTvRdzOQ0pR6GuP0/s584/web-service-model.png)

# 建立 Web Service

## 如何建立 Web Service

在 VS2005 裡有 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 專案範本，在該專案範本中，提供       Web Service 項目，可以用來建立 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 。           
但是，在 VS2008 之後，已移除 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 專案範本，若要建立       Web Serivce ，可以在網站專案中直接加入 Web Service 項目即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1riyQyGG7IcH73rCJ7gBUYwE6SZ_Zo4Fg18Ku-NQYtlPWROiKHfnBGxqqI4t_4YQ_2RGSiJsj_k70VF_tOR6IzGrF4zcLGXnAITpppo7Q047Uxa15EjfVw1Hn-XWHh6l88XpD7gphJb0/s811/web-service-add-web-service.png)

## The WebService Class

[WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 類別是 ASP.NET 用來建立 Web Service 的基底類別，就像 ASP.NET 網頁是由 Page 基底類別構成，所以要設計 Web Service 必須繼承 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 類別。  

但是建立 xml web service 是否要繼承這個類別是選擇性的，並非絕對必須。  當你想存取 ASP.NET 的功能時，才必須繼承這個類別，例如：使用 session 等。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuBTUK8LQKt77zsWnRHYMjrRXOueFONCD6r1efsErYcvrlZnhmdE-bdIzp5mYTQJ00ANmaviy33utwjzit2f7MUPSl3KYz98tlrKmDoeUbMUyeqyGzsztzgxn8UlxXiLmuwbFrHCC8dEM/s555/web-service-class.png)
```c#
namespace WebService1
{
public class Service1 : System.Web.Services.WebService 
{
...
}
}
```

## The WebServiceAttribute Class

[WebServiceAttribute](http://msdn.microsoft.com/zh-tw/library/system.web.services.webserviceattribute.aspx) 類別不是絕對必要的屬性類別，它主要用來提供 Web Service 的相關資訊給用戶端加入參加時使用。  你可以使用這個屬性類別來指定服務的 namespace 和 description。  
```c#
namespace WebService1
{
[WebService( Description="service for test", Namespace="http://mydomain.com" )]
public class WebService1 : System.Web.Services.WebService
{
...
}
}
```

## The WebMethodAttribute Class

在 Web Service 的方法上頭加入 [WebMethod](http://msdn.microsoft.com/zh-tw/library/byxd99hx.aspx) 這個屬性宣告，表示這是一個要公開的方法，底下是它進一步的屬性(Property)設定：  

- [EnableSession](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.enablesession.aspx) ：指示是否啟用 XML Web Service 方法的工作階段狀態。
- [TransactionOption](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.transactionoption.aspx) ：指示 XML Web Service 方法的交易支援。
- [CacheDuration](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.cacheduration.aspx) ：取得或設定回應保留在快取中應有的秒數。
- [BufferResponse](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.bufferresponse.aspx) ：取得或設定這個要求的回應是否緩衝。
- [Description](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.description.aspx)  ：描述 XML Web Service 方法的描述訊息。

下面這個例子，設定這個方法快取 5 分鐘。
```c#
[WebService(Namespace = "http://tempuri.org/")]
public class WebService1 : System.Web.Services.WebService
{
[WebMethod(CacheDuration=300)]
public string HelloWorld()
{
return "Hello World";
}
}
```

## 在 WebService 中使用多載

若你想提供如下範例中的二個方法，該怎麼辨呢？
```c#
[WebService(Namespace = "http://tempuri.org/")]
[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
public class WebService3 : System.Web.Services.WebService
{
[WebMethod]
public string Add(string a, string b)
{
return a + b;
}

[WebMethod]
public int Add(int a, int b)
{
return a + b;
}
｝
```

1. 使用 [MessageName](http://msdn.microsoft.com/zh-tw/library/system.web.services.webmethodattribute.messagename.aspx) 屬性來指定方法的唯一名稱。  
2. 因為這樣子的架構，不符合 WsiProfiles.BasicProfile1\_1 規格，所以要將 [WebServiceBindingAttribute](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservicebindingattribute.aspx) 中的互通性規格改成 [WsiProfiles.None](http://msdn.microsoft.com/zh-tw/library/system.web.services.wsiprofiles.aspx)   

調整後如下：
```c#
[WebService(Namespace = "http://tempuri.org/")]
[WebServiceBinding(ConformsTo = WsiProfiles.None)]
public class WebService3 : System.Web.Services.WebService
{
[WebMethod(MessageName = "sAdd")]
public string Add(string a, string b)
{
return a + b;
}

[WebMethod(MessageName = "iAdd")]
public int Add(int a, int b)
{
return a + b;
}
｝
```

# 叫用 Web Service

## 加入 Web Service 參考

當專案要使用某個 Web 服務時，必須先將該服務加入 Web 參考。  
在 VS2005 中，可以在專案的右鍵選單中可以找到「加入 Web 參考」這個功能，就可以直接加入 Web Service 參考。  
在 VS2008 之後，同樣的功能，必須由「加入 Service 參考」這個功能中，再點選[進階]→[加入 Web 參考]。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjH09xWxBjkxYU7aW5WQVC7oOAzoLjB3iA3lk-YVI-lBJGrSTXscx05XPfCzKVibtLHb__vSnRqBLVHMswgOjBtsJv0gN1d73K9_L1Ks7uQNaKkjbNfxv_ZRi46hRWSPO9hW4TRESuMv1o/s642/ws-add-web-ref1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiB94LceqBFWdCYjD-LWhrBxpvi2LZ_t17pR-gmEdwxi9L1yxFetLy-GaXrwuChlWAPxRUaVBiE7jL1x3n1FvGcduPYeySh08MQI3_I9AZnaTmtL2fg4Iq6iCAI6mR8IrcN-30rLiljy3c/s660/ws-add-web-ref2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiv-1cb6xkvJtCv0FdLmib7QeOKFmdfzN0cytJ42zu0TL-JSwcP5nDeAap05g25pSPzl8AIA9SHK4_B7IbJ2B6h5HUzjrprmfFLExnP5OWIyVJ9j2EofLgIzX5UQW5R6D6kgOggA7-PvaI/s836/ws-add-web-ref3.png)

上面最後一個步驟中，必須給定一個 Web 參考名稱，這個名稱將被使用於建立 Proxy 類別。  這個 Proxy 物件，就像一個代理人，會幫我們將參數序列化（serializes）成 **SOAP 訊息**（XML格式），並透過網路將 SOAP 訊息傳送到 Web Service。  等 Web Service 處理結束將訊息回傳時，最後也會由 Proxy 物件將其還原序列化（deserializes）後再交給客戶端程式碼。  如此，我們才可以輕鬆地和 Web Service 溝通。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLb3uVjH6FWzNKhs2G1VlRvIblz-P-7QNp22vZzAjyIhbzTVJuvPmvrXFttItGtr0-gwUfXkV0YOZzstld1CV3dvXMjIs3dk7Xlp9LL4DuIVlvpuwDiBp_OF-nFA_MeWu_eC8z6rBxJsI/s588/web-service-model.gif)

Source: [http://msdn.microsoft.com/en-us/library/x05s00wz(v=vs.80).aspx](http://msdn.microsoft.com/en-us/library/x05s00wz%28v=vs.80%29.aspx)

## 叫用 Web Service

#### 叫用 web service 方法

前面提過，藉由 Proxy 物件，叫用 Web Service 便是一件輕鬆容易的事。  不過，要特別注意，若要傳遞參數給叫用的方法，其資料型別必須是**可序列化**。  同樣的，若該方法有回傳值，其資料型別也必須是**可序列化**。  範例如下：  
```c#
//建立 web service 的 proxy 物件
var service2 = new Site1.Service2.WebService2();

//叫用 web service 提供的方法
WebApplication1.Site1.Service2.Emp emp = service2.GetEmp();
string EmpName = emp.Name;
```
```c#
namespace WebService1
{
[WebService(Description="service for test", Namespace="http://mydomain.com")]
public class WebService2 
{
[WebMethod]
public Emp GetEmp()
{
return new Emp("222","333");
}
}

[Serializable]
public class Emp
{
public string ID { get; set; }
public string Name { get; set; }

public Emp() { }  //可序列化類別，必須包含一個無參數的建構子

public Emp(string id, string name)
{
ID = id;
Name = name;
}
}
}
```

#### 繫結 web service 方法

我們也可以將 web service call 繫結到控制項之中。範例如下：
```c#
[WebMethod]
public DataTable GetEmployees()
{
DataTable Employees = new DataTable("Employees");
DataColumn col1 = new DataColumn("ID");
DataColumn col2 = new DataColumn("Name");
DataColumn col3 = new DataColumn("Age");
col3.DataType = typeof(Int16);
Employees.Columns.Add(col1);
Employees.Columns.Add(col2);
Employees.Columns.Add(col3);

Employees.Rows.Add("001", "AAA", 30);
Employees.Rows.Add("002", "BBB", 50);
Employees.Rows.Add("003", "CCC", 40);

return Employees;
}
```
```c#
protected void btnBindingGridView_Click(object sender, EventArgs e)
{
TestWeb.WebService1 ws = new TestWeb.WebService1();
GridView1.DataSource = ws.GetEmployees();
GridView1.DataBind();
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuGa9U6d60yqWPo_WLBM68wXzubmDhVmJwmElJqx-yCU5zMWmA2C81-3iUihQDAwC-lFP4nSL71LkZgnUg_TFSBEuMOWWE9rC7skgPMLN9F-nVs5YGd9mAXLBNgMZTA4TTRYyV-dgPjNI/s225/ws-binding-result.png)

# 如何在 Web Services 中使用 Session

ASP.NET Web Services 預設並未支援 Session，所以在 Web Services 程式碼中不能直接使用 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.session.aspx) ，如果要使用，則必須先做一些設定。  

## 在 Web Service 中存取 Session

- 1. 確認 web.config 組態檔當中 &lt;sessionState&gt; 的 mode 屬性值不是 Off，如果是 Off，則 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.session.aspx) 不會運作，後續的其他設定也不會有作用。
- 2. 確認這個服務類別是否有繼承 System.Web.Services.[WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 類別。繼承 WebService 類別才可以直接使用 ASP.NET 物件，如 Session、Application 等。
- 3. 在方法宣告的 WebMethod 宣告中加入 **EnableSession = true** 屬性。
```c#
public class WebService1 : System.Web.Services.WebService
{
[WebMethod(EnableSession = true)]
public string GetSession()
{
if (Session["webservice"] == null)
return "";
else
return Session["webservice"].ToString();
}
[WebMethod(EnableSession = true)]
public void SetSession()
{
Session["webservice"] = "abcd" + DateTime.Now.ToString();
}
[WebMethod(EnableSession = true)]
public int Counter()
{
int Counter = (Session["Counter"] == null ? 0 : int.Parse(Session["Counter"].ToString()));
Session["Counter"] = Counter + 1;
return Counter;
}
}
```

## 使用 CookieContainer 指定 Web Service 的 Cookie Container

若我們在 IE 中測試上面例子中 Counter 方法，會發現每叫用一次 Counter 會自動加１。  但是如果我們透過以下程式碼去叫用這個服務的方法，不管我們叫用幾次，結果都是一樣的。  
```c#
protected void GetSession_without_CookieContainer(object sender, EventArgs e)
{
localhost.WebService1 ws = new localhost.WebService1();
int counter = ws.Counter();     //0
counter = ws.Counter();         //0
counter = ws.Counter();         //0
}
```

那是因為 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.session.aspx) 的運作預設必須使用 [Cookie](http://msdn.microsoft.com/zh-tw/library/system.net.cookie.aspx) 來存放用以識別用戶端的 SessionID，如果沒有 [Cookie](http://msdn.microsoft.com/zh-tw/library/system.net.cookie.aspx) 的配合，就無法辨識使用者，如此便造成 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.session.aspx) 無法運作。  在 IE 瀏覽器中進行測試 Web Services 時，通常瀏覽器都會支援 Cookie，也因此 [Session](http://msdn.microsoft.com/zh-tw/library/system.web.httpcontext.session.aspx) 可以正常發揮效果；  但是如果在 code-behind 程式碼中呼叫 Web Services 時，則必須在用戶端程式中利用 [CookieContainer](http://msdn.microsoft.com/zh-tw/library/system.net.cookiecontainer.aspx) 來存放 Cookie，以保存 Server 用來識別用戶端的 SessionID，如此才可以延續的使用 Session。  下面例子就是改寫前面的範例，在 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 物件中加入鉗入 [CookieContainer](http://msdn.microsoft.com/zh-tw/library/system.net.cookiecontainer.aspx) 。  
```c#
protected void GetSession_with_CookieContainer(object sender, EventArgs e)
{
localhost.WebService1 ws = new localhost.WebService1();
System.Net.CookieContainer container = new System.Net.CookieContainer();
ws.CookieContainer = container;
int counter = ws.Counter();     //0
counter = ws.Counter();         //1
counter = ws.Counter();         //2
}
```

# 使用 AJAX 叫用 Web Service

使用 JavaScript 呼叫 Web Service ，可以實現**非同步更新網頁**。  透過 ScriptManager 控制項，並指定 **ServiceReference** 屬性，ASP.NET 會自動在 Clinet 端建立該服務的 Proxy 。  就可以輕鬆的透過 Proxy 存取服務，底下是操作的步驟：  

1. 在 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 類別加上 [ScriptService](http://msdn.microsoft.com/zh-tw/library/system.web.script.services.scriptserviceattribute.aspx) 屬性，加入這個屬性，才可以允許使用 Script 呼叫此 WebService。  ```c#
[System.Web.Script.Services.ScriptService]  // 加入這行，才可以允許使用 AJAX 呼叫此 WebService。
public class WebService1 : System.Web.Services.WebService
{
......
}
```
2. 被叫用的方法上頭，必須加上 **WebMethod** 屬性。  
```c#
namespace WebService1
{
[ScriptService]  //啟用 Script 支援
public class WebService1 : System.Web.Services.WebService
{
[WebMethod]     //表示為 web service 中可被外部叫用的方法，若沒有這宣告，則該方法僅限內部叫用
public int LikeCounter()
{
......
}
}
}
```
3. 在網頁中加入 **ScriptManager** 控制項，因為要使用 AJAX 支援。  同時在 **Services** 集合屬性中加入 **ServiceReference** 控制項，以設定 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 的參考位置。  ```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">             
<Services>                 
<asp:ServiceReference Path="~/WebService1.asmx" />             
</Services>
</asp:ScriptManager> 
```
4. 加入 JavaScript 方法。並且在這個 JavaScript 方法中呼叫 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 的方法。其叫用方法必須符合：  
&lt;WebService Namespace&gt;.&lt;WebService ClassName&gt;.&lt;WebService MethodName(param..., Success, fail, userContext)&gt;
5. 設計一個 JavaScript 回呼方法，給非同步作業執行結束時叫用，以處理非同步的執行結果。
```c#
namespace WebService1
{
[ScriptService]  //啟用 Script 支援
public class WebService1 : System.Web.Services.WebService
{
[WebMethod]
public int LikeCounter()
{
int counter = (Application["GoodCounter"] == null ? 0 : int.Parse(Application["GoodCounter"].ToString()));
counter = counter + 1;
Application["GoodCounter"] = counter;
return counter;
}
}
}
```
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="./Scripts/jquery-1.8.2.js"></script> 
<script type="text/javascript">
function btnLike_onclick() {
WebService1.WebService1.LikeCounter(OnSuccess);
}
function OnSuccess(result) {
$("#LikeCounter").text(result);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Services>
<asp:ServiceReference Path="~/WebService1.asmx" />
</Services>
</asp:ScriptManager>

<input id="btnLike" type="button" value="按讚 (ajax)" onclick="btnLike_onclick()" />    
<span id="LikeCounter" ></span>     
</div>
</form>
</body>
```

#### 說明

當 ScriptManager 控制項發現 Services 屬性集合中有 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 的位置參考時，會將此 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 類別轉換為可供 Client 呼叫的 JavaScript，  而這一份 JavaScript 會存在於 http://(host)/WebService1.asmx/jsdebug 這個位置， 然後 Web 網頁再用 Include 的方式嵌入。  所以檢視原始檔就可以看到下行宣告。  
```xml
<script src="WebService1.asmx/jsdebug" type="text/javascript"></script>
```

#### 使用限制

1. 此 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 必須是以 ASP.NET 所建立。
2. XMLHttpRequest **不允許跨網域**呼叫的問題。

#### 解決方式

在網站中建立一個 WebService，再由這個 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 去連結外部的 WebService。

#### 執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIUjwvYO8C7ogSyrgQvGJgcFd2WW53hrmVqw3_WRRH2EZBY7LRHOCOAEIehg3_UYTYwhQS9jkvTb3Vx9_XPLNL1Yoy_eNk7RPTzmSqyPyf_8oc4yMDXAGjTAVH-F1-tLm5aiJHXcwoJKA/s261/ws-ajax-result.png)

#### 叫用格式與回應格式

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKnxS5sBKHmnM52Ygi-Jo06RFZ0OqlYWaVya0JvDhvLTJr4L1eTU8dAN3sWGaB0t5GleqP1G_oLbgsmy7Lh7bpDURkTZbxO9TWn8wDdZYNaXCDpSjY9S2FcgYcTNJbYliqavRlVch-ON0/s711/ws-ajax-content-type.png)

# 使用 JQuery 叫用 Web Service

透過 jQuery 方法，也可以直接使用 JavaScript 呼叫 Web Service 。
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="./Scripts/jquery-1.8.2.js"></script> 

<script type="text/javascript">
function btnLike_onclick() {
jQuery.support.cors = true;
url = "http://localhost:30801/WebService1.asmx/LikeCounter";

$.ajax({
type: "post",
url: url,
dataType: "json",
contentType: "application/json; charset=utf-8",
success: OnSuccess,
error: OnFail
});

}
function OnSuccess(result) {
$("#LikeCounter").text(result.d);
}
function OnFail(result) {
alert('Service call failed: ' + result.status + '' + result.statusText);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<input id="btnLike" type="button" value="按讚 (ajax)" onclick="btnLike_onclick()" />       
<span id="LikeCounter" ></span>     
</div>
</form>
</body>
```

# 使用 Javascript 叫用 Web Service

使用 Javascript 直接叫用 Web Service ，必須使用 [XMLHttpRequest](http://msdn.microsoft.com/en-US/library/ie/ms535874.aspx) 物件。
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="./Scripts/jquery-1.8.2.js"></script> 
<script type="text/javascript">
var req;

function btnLike_onclick() {
url = "http://localhost:30801/WebService1.asmx/LikeCounter";

req = new XMLHttpRequest();
if (req) {
req.onreadystatechange = OnSuccess;
req.open("GET", url, true);
req.send(null);
}

}
function OnSuccess() {
if (req.readyState == 4 && req.status == 200) {
var result = req.responseText;
}
$("#LikeCounter").text(result);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<input id="btnLike" type="button" value="按讚 (ajax)" onclick="btnLike_onclick()" />    
<span id="LikeCounter" ></span>     
</div>
</form>
</body>
```
## 參考資料  

- [為使用 ASP.NET 建立的 XML Web Service 設定安全性](http://msdn.microsoft.com/zh-tw/library/w67h0dw7%28v=vs.100%29.aspx)
- [HOW TO：設定 XML Web Service 進行 Windows 驗證](http://msdn.microsoft.com/zh-tw/library/bfazk0tb%28v=vs.100%29.aspx)
- [HOW TO：使用 SOAP 標頭執行自訂驗證](http://msdn.microsoft.com/zh-tw/library/9z52by6a%28v=vs.100%29.aspx)