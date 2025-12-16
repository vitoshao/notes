---
title: Microsoft AJAX Library
layout: default
parent: JavaScript and AJAX
nav_order: 3
description: "Microsoft AJAX Library"
date: 2012-12-26
tags: [Web,ASPNET,JavaScript and AJAX]
postid: "6910027973072042275"
---
# 使用 ASP.NET AJAX Library

[Ajax Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 是從 .Net 3.5 開始被加進來，它是一組 client 端的 API ，而且其架構設計，很多看起來就像伺服器端的控制項一樣。   它提供許多好用的新功能，包含：  

- 局部網頁更新。
- 透過 client script 叫用 web service 。

簡單講 Microsoft® AJAX Library 就是一套 JavaScript 程式庫。  它由兩個相關部分組成：JavaScript 語言擴充功能和一組基底類別庫，以提供預先定義的服務和工具給開發人員運用。  

#### ※擴充功能

關於擴充功能的部份，例如：MicrosoftAJAX.js 和 MicrosoftAJAXWebForms.js 就屬於 JavaScript 語言擴充功能，  我們如果要使用這些功能，並不需要直接加入參考或嵌人這些檔案，只要透過 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項代勞即可。  只要將下列程式碼放入 ASP.NET AJAX 網頁中，即可保證一個或兩個 [Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 的 .js 檔案會下載到用戶端。  
```xml
<asp:ScriptManager runat="server" ID="ScriptManager1" />
```

#### ※基底類別庫

至於基底類別庫的部份，它就像 JQuery ，是一套物件導向式的 JavaScript Library 。  透過它所提供的設計模型，就可以使用物件的方法來設計客戶端的腳本語言。  

## The Microsoft AJAX Library Namespaces

底下列表是這個類別庫的命名空間

- [Global](http://msdn.microsoft.com/zh-tw/library/bb310818.aspx) ：基底物件以及 JavaScript 中 Array、Boolean、Error、Number、Object、String 型別的擴充。
- [Sys](http://msdn.microsoft.com/zh-tw/library/bb397702.aspx) ：
- [Sys.Net](http://msdn.microsoft.com/zh-tw/library/bb310860.aspx) ：
- [Sys.Serialization](http://msdn.microsoft.com/zh-tw/library/bb310840.aspx) ：
- [Sys.Services](http://msdn.microsoft.com/zh-tw/library/bb311017.aspx) ：這個命名空間主要提供用戶端驗證的功能，何如如 authentication services 和 profile services 。
- [Sys.UI](http://msdn.microsoft.com/zh-tw/library/bb397431.aspx) ：
- [Sys.WebForms](http://msdn.microsoft.com/zh-tw/library/bb397566.aspx) ：

# 使用 AJAX Library 實現物件導向設計

因為 Javascript 本身缺乏 OO 的特色， [Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 其中的一個主要功能就是用來擴充 Javascript 在這方面的不足，以支援如 namespace, inheritance, interface,fields, properties, enum, event, refection...  

## Create Custom Classes

下面範例,，我們自訂一個 MyMath 類別，其主要步驟如下：

1. 註冊一個命名空間
2. 以所註冊的命名空間來建立類別的建構子
3. 實作類別內容 (fields, properties, methods, ...)
4. 註冊該類別
```javascript
//======================================
// 1. 註冊一個命名空間
//======================================
Type.registerNamespace("MyNameSpace");

//======================================
// 2. 以所註冊的命名空間來建立類別的建構子
//======================================
MyNameSpace.MathFun =
function (n1, n2) {
this._n1 = n1;
this._n2 = n2;
this.ReturnInt = false;
}

//======================================
// 3. 實作類別內容
//======================================
MyNameSpace.MathFun.prototype =
{
//======================================
// Fields
//======================================
ReturnInt : Boolean,

//======================================
// Properties
//======================================
set_n1: function (value) {
this._n1 = value;
},
get_n1: function () {
return this._n1;
},
set_n2: function (value) {
this._n2 = value;
},
get_n2: function () {
return this._n2;
},

//======================================
// Methods
//======================================
Add: function () {
return this._n1 + this._n2;
},
Sub: function () {
return this._n1 - this._n2;
},
Mul: function () {
return this._n1 \* this._n2;
},
Div: function () {
if (!this.ReturnInt){
return this._n1 / this._n2;
}
else{
return Math.round(this._n1 / this._n2);
}
}
}

//==============================================================
// 4. 註冊該類別
//==============================================================
MyNameSpace.MathFun.registerClass('MyNameSpace.MathFun');
```

#### Type 類別

[Type](http://msdn.microsoft.com/zh-tw/library/bb310818.aspx) 類別是類別庫中一個重要的類別，包含在 [Global](http://msdn.microsoft.com/zh-tw/library/bb310818.aspx) 命名空間中。  這個類別會建立型別系統及啟用型別反映，讓我們可以在 ECMAScript 程式碼中，達到以下功能：  

- 物件導向程式設計功能。
- 註冊程式碼關聯性及結構，包括類別、介面、基底類別和列舉型別。

在設計自訂類別時，有以下幾點原則參考：  

- 所有原型成員都以逗號分隔，原型中的最後一個成員後面不需要逗號。
- 私用欄位，習慣以底線 (\_) 開頭來命名。
- 透過 this 指標來參考原型中的成員，效能方面會比較好。
- 習慣使用 "get\_" 或 "set\_" 前置詞來取得或設定屬性。

#### 命名空間

使用 Type.RegisterClass 方法註冊
```javascript
Type.registerNamespace("Demo");
```

#### 建立類別建構子

要建立類別的建構子，必須定義一個 function 給類別名稱，其語法如下：  
```javascript
Demo.Person = function(firstName, lastName, emailAddress) {
this._firstName = firstName;
this._lastName = lastName;
this._emailAddress = emailAddress;
}
```

#### 實作類別

要實作類別，必須定義一個 prototype ，其語法如下：  
```javascript
Demo.Person.prototype = {

getFirstName: function() {
return this._firstName;
},

getLastName: function() {
return this._lastName;
},

getName: function() {
return this._firstName + ' ' + this._lastName;
},

dispose: function() {
alert('bye ' + this.getName());
}
}
```

#### 註冊類別

要註冊類別，必須叫用 registerClass 方法。這個方法包含三個參數：

- typeName：full name of the class
- baseType：the base class for inheritance ,
- Set null if your class is stand alone.
- Set [Sys.UI.Control](http://msdn.microsoft.com/zh-tw/library/bb310848.aspx) or [Sys.UI.Behavior](http://msdn.microsoft.com/zh-tw/library/bb311020.aspx) if your class is meant to be an AJAX control or a behavior.
- interfaceTypes：indicates the interfaces that the class must implement.
```javascript
Demo.Person.registerClass('Demo.Person', null, Sys.IDisposable);
```

#### Access Modifier

大部分的物件導向程式設計語言都包含了「存取修飾詞」(Access Modifier) 的概念，此概念可讓您指定要在哪種環境下 (例如在外部程式中、相同命名空間裡的內部類別，或是僅於特定程式碼區塊中) 提供類別或成員。  JavaScript 並未包含存取修飾詞。但是，Microsoft AJAX Library 按照慣例會將具有底線字元 ("\_") 開頭名稱的成員視為私用，而且無法在所屬的類別以外加以存取。  

#### Enumerations

列舉型別是一種類別，內含一組具名整數常數。您可以將值當成屬性來存取，如下列範例所示：  
```javascript
// Define an enumeration type and register it.
Demo.Color = function(){};
Demo.Color.prototype = 
{
Red:    0xFF0000,
Blue:   0x0000FF,
Green:  0x00FF00,
White:  0xFFFFFF 
}
Demo.Color.registerEnum("Demo.Color");
```

#### Inheritance

繼承為某類別從另一個類別衍生的能力。衍生類別會自訂繼承基底類別的所有欄位、屬性、方法與事件。  列範例包含兩個在指令碼中定義的類別：Person 和 Employee，其中 Employee 衍生自 Person。  
```javascript
// 建立衍生類別
Demo.Employee = function(firstName, lastName, emailAddress, team, title) {
// 初始化父類別，呼叫父類別建構子以 Array 傳入.
Demo.Employee.initializeBase(this, [firstName, lastName, emailAddress]);

this._team = team;
this._title = title;
}
Demo.Employee.prototype = {

getTeam: function() {return this._team;},
setTeam: function(team) {this._team = team;},
getTitle: function() {return this._title;},
setTitle: function(title) {this._title = title;},
// 覆寫父類別 toString 方法
toString: function() {
return Demo.Employee.callBaseMethod(this, 'toString') + '\r\n' + this.getTitle() + '\r\n' + this.getTeam();
}
}
// 註冊衍生類別，繼承父類別
Demo.Employee.registerClass('Demo.Employee', Demo.Person);
```

#### 注意：

若自訂的類別繼承自 [Ajax Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 物件，則必須通知類別庫自訂的 script 已經完成載入。  方法就是叫用 [Sys.Application](http://msdn.microsoft.com/zh-tw/library/bb310856.aspx) 物件的 notifyScriptLoaded 方法。  
```javascript
if (typeof(Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```

#### Interfaces

介面，用以定義實作介面的類別輸入與輸出需求。不管類別實作了其他哪些功能，此介面都可與實作相同介面的類別進行互動。

## Using Custom Classes

要引用上面的自訂類別，必須在 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 中註冊該腳本。
```xml
<head id="Head1" runat="server">
<title></title>
<script language="ecmascript" type="text/javascript">
function bAdd_onclick() {
var f = new MyNameSpace.MathFun(9, 3);

alert(f.Add());
}
function bSub_onclick() {
var f = new MyNameSpace.MathFun(9, 3);
alert(f.Sub());
}
function bMul_onclick() {
var f = new MyNameSpace.MathFun(9, 3);
alert(f.Mul());
}
function bDiv_onclick() {
var f = new MyNameSpace.MathFun(9, 3);
f.set_n2(2);
alert(f.Div());
}
function bDiv2_onclick() {
var f = new MyNameSpace.MathFun(9, 3);
f.set_n2(2);
f.ReturnInt = true;
alert(f.Div());
}
</script>
</head>
<body>
<form id="form1" runat="server">
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference path="MyMathFun.js" />
</Scripts>
</asp:ScriptManager>
<div>
<input id="bAdd" type="button" value="Add" onclick="return bAdd_onclick()" />
<input id="bSub" type="button" value="Sub" onclick="return bSub_onclick()" />
<input id="bMul" type="button" value="Mul" onclick="return bMul_onclick()" />
<input id="bDiv" type="button" value="Mul" onclick="return bDiv_onclick()" />
<input id="bDiv2" type="button" value="Mul 2" onclick="return bDiv2_onclick()" />
</div>
</form>
</body>
```

# 使用 AJAX Library 管理用戶端事件

這章節主要說明如何使用用戶端程式碼的事件。

## 用戶端的 Page Event

Microsoft AJAX Library 中的 [Sys.Application](http://msdn.microsoft.com/zh-tw/library/bb310856.aspx) 物件會提供 init 、 load 及 unload 事件，而且該物件也繼承基底 Component 類別中的 disposing 和 propertyChanged 事件。所有這些事件都適用於瀏覽器用戶端應用程式的生命週期。  

- [init](http://msdn.microsoft.com/zh-tw/library/bb397532.aspx)：Raised after all scripts have been loaded but before objects are created.
- [load](http://msdn.microsoft.com/zh-tw/library/bb383829.aspx)：Raised after all scripts have been loaded and after the objects in the application have been created and initialized.
- [unload](http://msdn.microsoft.com/zh-tw/library/bb310821.aspx)：Raised before all objects in the client application are disposed.

就像 server code 一樣，在 client code 中的每個事件也都有其生命週期。  當網頁載入執行時，也可以視需求欄截事件。  就像 code-behind 中的 Page\_Load 事件，我們也可以 client code 中設計 Sys.Application.load 事件。  

要註冊用戶端事件，Of course 必須先加入 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項，  然後用 **add\_event** 語法來註冊事件，也就是對每個處理常式呼叫一次 add\_ 存取子，您就可以將其他處理常式附加到事件中。  下面例子示範如何註冊 [Sys.Application.Load](http://msdn.microsoft.com/zh-tw/library/bb383829.aspx) 事件。  
```javascript
Sys.Application.add_load(handler);
Sys.Application.remove_load(handler);
```
```javascript
// Attach a handler to the load event.
Sys.Application.add_load(PageLoad);

function PageLoad() {
// Redirect to alternate page if not business hours.
var d = new Date();
if (!(8 < d.getHours() < 17)) {
window.location = "AfterHours.aspx";
}
}
```

## 用戶端的 PostBact Event

#### PageRequestManager 類別

若我們想要處理非同步回傳的事件，就會使用到 Sys.WebForm 命名空間，這個命名空間包含 partial-page rendering 相關的類別。  例如 [PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別，就是用來管理 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 控制項的部分網頁更新，並且可用來定義適用於非同步呈現之頁面部分的額外事件。  

要使用 [PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別，並不需要建立這個類別的執行個體，只要在頁面中使用 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 控制項， [PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別便會自動建立執行個體。  你可以呼叫 [getInstance](http://msdn.microsoft.com/zh-tw/library/sys.webforms.pagerequestmanager.getinstance.aspx) 方法就可取得 [PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別的執行個體。  更詳細的網頁局部呈現資訊，請參閱[網頁局部呈現概觀](http://msdn.microsoft.com/zh-tw/library/bb386573.aspx)。  

下表列出您可以使用的用戶端事件清單 (列表順序也就是由 [PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別呼叫的順序)

- **initializeRequest** ：Raised before the async PostBack starts.
- **beginRequest** ：Raised as the async PostBack is sent to the server.
- **pageLoading** ：Raised when the async PostBack response first comes back from the server.
- **pageLoaded** ：Raised after the content has been loaded from the results of the async PostBack.
- **endRequest** ：Raised when the async PostBack has been completed.
- 

**範例**
```javascript
<script type="text/javascript" language="javascript">

var PRM = Sys.WebForms.PageRequestManager.getInstance();
PRM.add_initializeRequest(InitializeRequestHandler);
PRM.add_beginRequest(BeginRequestHandler);
PRM.add_endRequest(EndRequestHandler);

function InitializeRequestHandler(sender, args) {
var inst = Sys.WebForms.PageRequestManager.getInstance();
var elem = args.get_postBackElement();

//若非同步回傳還在執行中
if (inst.get_isInAsyncPostBack()) {
if (elem.id == 'btnLongProcessing') {
args.set_cancel(true);      //取消本次非同步回傳 (不想重複執行)
SetMessage('visible', 'MsgDiv', 'Still Processing...');
}
else if (elem.id == 'btnCancelProcessing') {
inst.abortPostBack();       //中止先前的非同步回傳
SetMessage('visible', 'MsgDiv', 'Process Canceled');
}
}
}

// Async Request 開始
function BeginRequestHandler(sender, args) {
var elem = args.get_postBackElement();
if (elem.id == 'btnLongProcessing') {
SetMessage('visible', 'MsgDiv', 'Processing...');
}
}

// Async Request 結束
function EndRequestHandler(sender, args) {
SetMessage('hidden', 'MsgDiv', 'finished');
}

function SetMessage(visstring, elem, msg) {
var adiv = $get(elem);
adiv.style.visibility = visstring;
adiv.innerHTML = msg;
}
</script>
```
```xml
<body>
<form id="form1" runat="server">
<asp:ScriptManager ID="ScriptManager1" runat="server" />
<div>
<div id="MsgDiv"></div>
<asp:UpdatePanel ID="UpdatePanel1" runat="server">
<ContentTemplate>
<asp:Button ID="btnLongProcessing" runat="server" Text="Asyn PostBack" onclick="btnLongProcessing_Click" />
<asp:Button ID="btnCancelProcessing" runat="server" Text="Cancel Asyn PostBack" onclick="btnCancelProcessing_Click" />
</ContentTemplate>
</asp:UpdatePanel>
</div>
</form>
</body>
```

# 使用 AJAX Library 建立用戶端控制項

Microsoft AJAX Library 可以幫我們建立具有 AJAX 功能的用戶端控制項。  依據功能不同， AJAX Library 提供以下三個基底物件，供我們繼承使用：  

- [Sys.Component](http://msdn.microsoft.com/zh-tw/library/bb397516.aspx) ：  
這個物件是 Control 和 Behavior 類別的基底類別。  這個類別不產生 UI 項目，只提供 function 給網頁使用。  繼承這個類別，代表其 lifetime 將交由 ASP.NET AJAX Client Library 直接管理。  例如，[Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) 控制項就是這類型的控制項。
- [Sys.UI.Control](http://msdn.microsoft.com/zh-tw/library/bb310848.aspx) ：  
這個物件是用來建立 AJAX Client Controls 的基底類別。  當它被放置在網頁中時，就會像 input box 一樣，成為 DOM 的一個項目，
- [Sys.UI.Behavior](http://msdn.microsoft.com/zh-tw/library/bb311020.aspx) ：  
這個物件是用來建立具有 AJAX 行為的基底類別。  AJAX Behavior 也是一個控制項，可以用來擴充與其相關聯的控制項的功能。  例如，你可以建立一個行為，當控制項獲得焦點時，就彈出一個視窗。  再將這個行為套用到指定 button, input box, hyperlink 身上。

## Creating an AJAX Client Component

An AJAX client component is a class you create that derives from the [Sys.Component](http://msdn.microsoft.com/zh-tw/library/bb397516.aspx) class.  You derive from this class because you intend to create a class that gets managed by the AJAX Library but does not work directly with the UI.  
```javascript
Type.registerNamespace("AjaxEnabled");

AjaxEnabled.PasswordStrengthComponent = function () {
//初始化基底類別中的建構子
AjaxEnabled.PasswordStrengthComponent.initializeBase(this);
}

AjaxEnabled.PasswordStrengthComponent.prototype = {

//覆寫 initialize 方法 - ( 透過叫用 Type.callBaseMethod 方法, 指定/覆寫 Base 的 initialize 方法 )

initialize: function () {
AjaxEnabled.PasswordStrengthComponent.callBaseMethod(this, 'initialize');

// add custom initialization 
}
}

//register class as a Sys.Component
AjaxEnabled.PasswordStrengthComponent.registerClass('AjaxEnabled.PasswordStrengthComponent', Sys.Component);

//notify loaded
if (typeof (Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```
```xml
<body>
<form id="form1" runat="server">
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference Path="PasswordStrengthComponent.js" />
</Scripts>
</asp:ScriptManager>
<div>

<asp:TextBox ID="TextBoxPassword1" runat="server" TextMode="Password" Width="200" onkeyup="_OnKeypress()"></asp:TextBox>
<asp:Label ID="LabelStrength" runat="server" Text=""></asp:Label>

<script language="ecmascript" type="text/javascript">
function _OnKeypress() {
var checker = new AjaxEnabled.PasswordStrengthComponent();
var pass = document.getElementById("TextBoxPassword1").value;
var strength = checker.returnPasswordStrength(pass);
document.getElementById("LabelStrength").innerText = strength;
}
</script>
</div>
</form>
</body>
```

執行時，會依不同的輸入長度，使用不同訊息，表示密碼強度  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJBR2rOFif8PN3N29xhWDC365dMh5bJUoxISAvxajwq96lttcsBg7Toq5aQTzxswNcs6fwlTtHhPjv5BufbOaNY33lexUQ52jTk_OQpuuywrFkf2sxvnkpWz3Xof5ofAXLHAn57zXVthc/s385/ajas-client-component-r1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiLTHJI_cGzOuQKhMxz6LGJEuRe6SVAInhMjffDL1YZo1pecnDHVHJ-VboiHE1DS2cjdGwKNTHohiQ36Yc5JZCQ64WwEqlF4M3PekJLNXdpipFGHx3lBxZg6gyOIHVT2W-nOjZVOFlZsmY/s310/ajas-client-component-r2.png)

## Creating an AJAX Client Control

AJAX client control is a control that you write that is meant to provide additional, clientside functionality to a DOM element (like a button or an input box).   A client control is meant to work as a single, encapsulated control much the same way as the ASP.NET server controls or custom controls you would write.  

For example, consider a password strength control that works with a text box to turn the text box different colors as the user types.   These colors are based on the strength of the password after each key press. To start this control, you might define the following constructor:  

### 1) 設計 AJAX Client Control

注意最後註冊類別時，必須繼承至 [Sys.UI.Control](http://msdn.microsoft.com/zh-tw/library/bb310848.aspx) 類別。
```javascript
Type.registerNamespace("Practice70515");

//create constructor
PasswordStrengthControl = function (element) {
// 初始化基底類別，並將參數以 Array 傳入父類別建構子
PasswordStrengthControl.initializeBase(this, [element]);
this._weakCssClass = null;
this._mediumCssClass = null;
this._strongCssClass = null;
}

//define class
PasswordStrengthControl.prototype = {

//initialize the UI control

initialize: function () {

// 1) 透過叫用 Type.callBaseMethod 方法, 指定覆寫 Base (Sys.UI.Control) 的 initialize 方法
PasswordStrengthControl.callBaseMethod(this, 'initialize');

// 2) create a delegate
this._onKeyupHandler = Function.createDelegate(this, this._onKeyup);

// 3) $addHandlers : register this method as a handler
// para1 : 你想要攔截對象
// para2 : { 你想要攔截事件: 對應的執行方法 }
// para3 : 執行個體
$addHandlers(this.get_element(), { 'keyup': this._onKeyup }, this);
},

//dispose the UI control

dispose: function () {
$clearHandlers(this.get_element());
PasswordStrengthControl.callBaseMethod(this, 'dispose');
},

// define key up event
// the event name is used in createDelegate and $addHandlers
_onKeyup: function (e) {

//get password text
var pass = this.get_element().value;
var strength = this.returnPasswordStrength(pass);

switch (strength) {
case "Weak":
this.get_element().className = this._weakCssClass;
break;
case "Medium":
this.get_element().className = this._mediumCssClass;
break;
case "Strong":
this.get_element().className = this._strongCssClass;
break;
}
},

//define properties
get_weakCssClass: function () {
return this._weakCssClass;
},
set_weakCssClass: function (value) {
this._weakCssClass = value;
},
get_mediumCssClass: function () {
return this._mediumCssClass;
},
set_mediumCssClass: function (value) {
this._mediumCssClass = value;
},
get_strongCssClass: function () {
return this._strongCssClass;
},
set_strongCssClass: function (value) {
this._strongCssClass = value;
},

returnPasswordStrength: function (password) {
var strPass = new String(password.toString());
if (strPass.length < 5) {
return "Weak";
}
else {
if (strPass.length < 8) {
return "Medium";
}
else {
return "Strong";
}
}
}
}

// register class as a Sys.Control 
// indicate that the class inherits Sys.UI.Control.
PasswordStrengthControl.registerClass('PasswordStrengthControl', Sys.UI.Control);

//notify loaded
if (typeof (Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```

### 2) 在網頁中，加入 AJAX Client Control
```xml
<head id="Head1" runat="server">
<title></title>
<style type="text/css">
.weak{border: thin solid #FF0000;}
.medium{border: thin solid #FFFF00;}
.strong{border: medium solid #008000;}
</style>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference Path="PasswordStrengthControl.js" />
</Scripts>
</asp:ScriptManager>
<script language="javascript" type="text/javascript">
var app = Sys.Application;
app.add_init(appInit);
function appInit(sender, args) {
$create(
PasswordStrengthControl,
{ weakCssClass: 'weak', mediumCssClass: 'medium', strongCssClass: 'strong' },
null,
null,
$get('TextBox1'));
}
</script>
<asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
</div>
</form>
</body>
```

### 3) 執行結果

執行時，會依不同的輸入長度，使用不同顏色，表示密碼強度  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKzGT5w0j_hfLLemEBUYOwbjgNI6UynAI31ZL1dsYalPJfyV3oYtN_mhG_rHGS6ZQbPCxY67gxcnOhJY7K6YOb63lZWjkJ8lywLCjTD6LvAdUt62ecTB3B5t31xOUpEX1XbjjYuyOFt-I/s273/ajas-client-control-r1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhiOzP7e0udT0IG-zB9e2AzHWqi4gUqgYIWbfcq4yuiagucJt3dANsyx9UevYvbY6zFflog6ru5oWkvcMwR17wIkWPbCtLeDqTEZ9KrUkOQ4OU5XmFt6BbSqwgC2U6-daKOPExAXCmqmP0/s278/ajas-client-control-r2.png)

## 如何將 AJAX Client Control 封裝到 Custom Server Control

A more robust solution for creating AJAX-enabled controls is to embed your client-side functionality into a custom control.   To create a custom control that embeds your client script, you must define a server-side class and use it to write the custom control.  

The class itself must inherit from a control. This can be either an existing control you intend to extend or a base control (like System.Web.UI.Control).   The control must also implement the interface IScriptControl.  

### 1) 設計 AJAX 自訂控制項

若要將上節中的 Ajax Client Control (.js) 包進自訂控制項 (Custom Server Control)，除了原本設計自訂控制項就必須繼承的 [Control](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.aspx) 類別外，還必須實作 [IScriptControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.iscriptcontrol.aspx) 介面。  並且利用 [ScriptControlDescriptor](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptcontroldescriptor.aspx) 類別，來定義用戶端控制項物件。  以下是程式碼簡單的步驟說明：  

- 實作 [IScriptControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.iscriptcontrol.aspx) 介面
- 在 [GetScriptDescriptors](http://msdn.microsoft.com/zh-tw/library/system.web.ui.iscriptcontrol.getscriptdescriptors.aspx) 方法中，定義 client control 要做的事情。
- 在 [GetScriptReferences](http://msdn.microsoft.com/zh-tw/library/system.web.ui.iscriptcontrol.getscriptreferences.aspx) 方法中，取得 client control 要執行的 JavaScrtip。
- 在 OnPreRender 事件中檢查頁面是否有 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項。
- 在 Render 事件中註冊 JavaScrtip 。
```c#
using System;
using System.Collections.Generic;
using System.Web;
using System.Web.UI.WebControls;
using System.Web.UI;

namespace C06_AJAX_and_ClientSide_Scripting
{
public class PasswordStrengthControl : TextBox, IScriptControl
{
private ScriptManager sMgr;

// add three fields to the control for managing the Password text box style

public string WeakCssClass;
public string MediumCssClass;
public string StrongCssClass;

// Implete the method GetScriptDescriptors of IScriptControl Interface .
// This method is meant to define the properties and events that work with the client control. 
// Here, you want to add three property descriptors, one for each password strength style

public IEnumerable<ScriptDescriptor> GetScriptDescriptors()
{
ScriptControlDescriptor descriptor =
new ScriptControlDescriptor("PasswordStrengthControl", this.ClientID);

descriptor.AddProperty("weakCssClass", this.WeakCssClass);
descriptor.AddProperty("mediumCssClass", this.MediumCssClass);
descriptor.AddProperty("strongCssClass", this.StrongCssClass);
return new ScriptDescriptor[] { descriptor };
}

// Implete the method GetScriptReferences of IScriptControl Interface .
// This method is meant to get a reference to the JavaScript that is used by this custom control. 
// In this case, the JavaScript will be embedded in the same assembly

public IEnumerable<ScriptReference> GetScriptReferences()
{
ScriptReference reference = new ScriptReference();
reference.Path = ResolveClientUrl("PasswordStrengthControl.js");
return new ScriptReference[] { reference };

//ScriptReference reference = new ScriptReference();
//reference.Assembly = "Practice70515";
//reference.Name = "PasswordStrengthControl.js";
//return new ScriptReference[] { reference };
}

protected override void OnPreRender(EventArgs e)
{
if (!this.DesignMode)
{
//test for the existence of a ScriptManager
sMgr = ScriptManager.GetCurrent(Page);
if (sMgr == null)
throw new HttpException("A ScriptManager control must exist on the page.");
sMgr.RegisterScriptControl(this);
}
base.OnPreRender(e);
}
protected override void Render(HtmlTextWriter writer)
{
if (!this.DesignMode)
sMgr.RegisterScriptDescriptors(this);

base.Render(writer);
}
}
}
```

### 2) 將 Javascript 嵌入自訂控制項

1. Copy the PassTextBox.js file created in the prior lab to your PassTextBox class library project
2. In Solution Explorer, view properties for the PassTextBox.js file. Set the **Build Action** property to **embedded resource**.          
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFCU5I2H87dprtQJrFFYqoKGly4Mq_PrZWloJQRV_b5NWH0M45JlRSOJcsMPxac9vTdTqEkOHrSOfIhJRD5ymv_gh1TqyHBcCWLN8UlXR_V0lSFXIkV3rE1Cg_agirdR-aMXG33GsJKog/s241/embeded-resource.png)
3. Open the project's **AssemblyInfo** file and add the following **WebResource** assembly definition:   
```c#
[assembly: System.Web.UI.WebResource("AjaxEnabled.PassTextBox.js", "text/javascript")]
```

### 3) 在網頁中，加入 AJAX 自訂控制項
```xml
<head id="Head1" runat="server">
<title></title>

<style type="text/css">
.weak{border: thin solid #FF0000;}
.medium{border: thin solid #FFFF00;}
.strong{border: medium solid #008000;}
</style>

</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server" />

<cc1:PasswordStrengthControl ID="PasswordStrengthControl1" TextMode="Password" runat="server"
WeakCssClass="weak" MediumCssClass="medium" StrongCssClass="strong"></cc1:PasswordStrengthControl>
</div>
</form>
</body>
```

## Creating an AJAX Behavior Client Controls

在 AJAX Control Tool Kids 中，有些控制項本身就是一個完成獨立的 AJAX 控制項，  有些則是屬於 Extender 類別，可用來擴充其他控制項的功能。AJAX Behavior Client Controls 就是指這類可擴充功能的控制項。  它其實就像前一節介紹的 AJAX 控制項，差別在於，它可於設計階段被套用到 DOM 模型中的項目。  

### 1) 設計 AJAX Behavior Client Control

底下程式碼，和上節不同的，只有第 98 行，這個控制項必須繼承至 [Sys.UI.Behavior](http://msdn.microsoft.com/zh-tw/library/bb311020.aspx) 類別。
```javascript
Type.registerNamespace("Practice70515");

//create constructor
PasswordStrengthExtender = function (element) {
// 初始化基底類別，並將參數以 Array 傳入父類別建構子
PasswordStrengthExtender.initializeBase(this, [element]);
this._weakCssClass = null;
this._mediumCssClass = null;
this._strongCssClass = null;
}

//define class
PasswordStrengthExtender.prototype = {

//initialize the UI control

initialize: function () {

// 1) 透過叫用 Type.callBaseMethod 方法, 指定覆寫 Base (Sys.UI.Control) 的 initialize 方法
PasswordStrengthExtender.callBaseMethod(this, 'initialize');

// 2) create a delegate
this._onKeyupHandler = Function.createDelegate(this, this._onKeyup);

// 3) $addHandlers : register this method as a handler
// para1 : 你想要攔截對象
// para2 : { 你想要攔截事件: 對應的執行方法 }
// para3 : 執行個體
$addHandlers(this.get_element(), { 'keyup': this._onKeyup }, this);
},

//dispose the UI control

dispose: function () {
$clearHandlers(this.get_element());
PasswordStrengthExtender.callBaseMethod(this, 'dispose');
},

// define key up event
// the event name is used in createDelegate and $addHandlers
_onKeyup: function (e) {

//get password text
var pass = this.get_element().value;
var strength = this.returnPasswordStrength(pass);

switch (strength) {
case "Weak":
this.get_element().className = this._weakCssClass;
break;
case "Medium":
this.get_element().className = this._mediumCssClass;
break;
case "Strong":
this.get_element().className = this._strongCssClass;
break;
}
},

//define properties
get_weakCssClass: function () {
return this._weakCssClass;
},
set_weakCssClass: function (value) {
this._weakCssClass = value;
},
get_mediumCssClass: function () {
return this._mediumCssClass;
},
set_mediumCssClass: function (value) {
this._mediumCssClass = value;
},
get_strongCssClass: function () {
return this._strongCssClass;
},
set_strongCssClass: function (value) {
this._strongCssClass = value;
},

returnPasswordStrength: function (password) {
var strPass = new String(password.toString());
if (strPass.length < 5) {
return "Weak";
}
else {
if (strPass.length < 8) {
return "Medium";
}
else {
return "Strong";
}
}
}
}

// register class as a Sys.Control 
// indicate that the class inherits Sys.UI.Control.
PasswordStrengthExtender.registerClass('PasswordStrengthExtender', Sys.UI.Behavior);

//notify loaded
if (typeof (Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```

### 2) 設計 AJAX Behavior 自訂控制項

底下程式碼，和上節最大的不同在於，當 Behavior Control 在使用時，都會有一個 Target Control 。  這個 Control，會被當做參數傳進 [GetScriptDescriptors](http://msdn.microsoft.com/zh-tw/library/system.web.ui.iscriptcontrol.getscriptdescriptors.aspx) 方法，所以我們必須在這個方法中，取得其 ClientID ，  然後建立 [ScriptBehaviorDescriptor](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptbehaviordescriptor.aspx) 類別。  
```c#
using System;
using System.Collections.Generic;
using System.Web;
using System.Web.UI;

namespace C06_AJAX_and_ClientSide_Scripting
{
[TargetControlType(typeof(Control))]
public class PasswordStrengthExtender : ExtenderControl
{
public string WeakCssClass;
public string MediumCssClass;
public string StrongCssClass;

protected override IEnumerable<ScriptDescriptor> GetScriptDescriptors(Control targetControl)
{
ScriptBehaviorDescriptor descriptor =
new ScriptBehaviorDescriptor("PasswordStrengthExtender", targetControl.ClientID);

descriptor.AddProperty("weakCssClass", this.WeakCssClass);
descriptor.AddProperty("mediumCssClass", this.MediumCssClass);
descriptor.AddProperty("strongCssClass", this.StrongCssClass);
return new ScriptDescriptor[] { descriptor };
}

protected override IEnumerable<ScriptReference> GetScriptReferences()
{
ScriptReference reference = new ScriptReference();
reference.Path = ResolveClientUrl("PasswordStrengthExtender.js");
return new ScriptReference[] { reference };
}

protected override void OnPreRender(EventArgs e)
{
if (!this.DesignMode)
{
//test for the existence of a ScriptManager
ScriptManager sMgr = ScriptManager.GetCurrent(Page);
if (sMgr == null)
throw new HttpException("A ScriptManager control must exist on the page.");
}
base.OnPreRender(e);
}
}
}
```

### 3) 在網頁中，加入 AJAX Behavior 自訂控制項

#### 
```xml
<head id="Head1" runat="server">
<title></title>
<style type="text/css">
.weak{border: thin solid #FF0000;}
.medium{border: thin solid #FFFF00;}
.strong{border: medium solid #008000;}
</style>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
<asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
<cc1:PasswordStrengthExtender ID="PasswordStrengthExtender1" runat="server" 
TargetControlID="TextBox1"
WeakCssClass="weak" MediumCssClass="medium" StrongCssClass="strong" />
</div>
</form>
</body>
```

# Microsoft AJAX Library  練習

以下為練習內容，主題說明都在上面。

## Create and Use an AJAX Component

#### Create the AJAX Component
```javascript
// 1
//create a new ASP.NET Web site

// 2
//Add a new JavaScript file to the site. Right-click the Web site and select Add New Item.
//In the Add New Item dialog box, select AJAX Client Library. Name the file passwordstrengthcomponent.js.

// 3
//Open the newly created JavaScript file. At the top of the file, add code to register a
//new namespace. The following shows an example:

Type.registerNamespace("AJAXEnabled"); ;

// 4
//Next, define the constructor for your JavaScript class as a function. This is a simple
//AJAX component so not much happens here. The following shows an example:

//create constructor
AJAXEnabled.PasswordStrengthComponent = function () {
AJAXEnabled.PasswordStrengthComponent.initializeBase(this);
}

// 5
//The next step is to define the inside of the class. You do so by creating its prototype.
//Inside the prototype, declare a function called returnPasswordStrength that takes a
//password, checks its value, and returns its strength. The following shows an example
//class definition that also includes the dispose method:

//define class
AJAXEnabled.PasswordStrengthComponent.prototype = {
initialize: function() {

//add custom initialization here
AJAXEnabled.PasswordStrengthComponent.callBaseMethod(this, 'initialize');
},

returnPasswordStrength: function (password) {
var strPass = new String(password.toString());
if (strPass.length < 5) {
return "Weak";
}
else {
if (strPass.length < 8) {
return "Medium";
}
else {
return "Strong";
}
}
},

dispose: function () {
//add custom dispose actions here
AJAXEnabled.PasswordStrengthComponent.callBaseMethod(this, 'dispose');
}
}

//6
//Finally, add code to the class to register it with the Microsoft AJAX Library by calling
//the registerClass method of the component. Be sure to indicate that the class inherits
//the Sys.Component class from the library. The following code shows an example that
//includes notification to the application that the script has been fully loaded:

//register class as a Sys.Component
AJAXEnabled.PasswordStrengthComponent.registerClass('AJAXEnabled.PasswordStrengthComponent', Sys.Component);

//notify script loaded
if (typeof (Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```

#### Call the AJAX Component from a Web Page
```xml
<head id="Head1" runat="server">
<title> </title>

<script language="javascript" type="text/javascript">
function _OnKeypress() {
var checker = new AJAXEnabled.PasswordStrengthComponent();
var pass = document.getElementById("TextBoxPassword").value;
var strength = checker.returnPasswordStrength(pass);
document.getElementById("LabelStrength").innerText = strength;
}
</script>

</head>
<body>
<form id="form1" runat="server">
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference Path="PasswordStrengthComponent.js" /><!--reference .js file -->
</Scripts>
</asp:ScriptManager>
<div style="font-size: large; font-weight: bold">User Login </div>
<hr />
<br />
User Name:
<br />
<asp:TextBox ID="TextBoxUserName" runat="server" Width="200"> </asp:TextBox>
<br />
Password:
<br />                                                                           <!-- register event -->
<asp:TextBox ID="TextBoxPassword" runat="server" TextMode="Password" Width="200" onkeyup="_OnKeypress()"> </asp:TextBox>
<asp:Label ID="LabelStrength" runat="server" Text=""> </asp:Label>
<br />
<input id="Button1" type="button" value="Submit" />
</form>
</body>
```

## Create and Use an AJAX Client Control

#### Create an AJAX Client Control
```javascript
Type.registerNamespace("AJAXEnabled");    //create constructor  AJAXEnabled.PassTextBox = function (element) {      AJAXEnabled.PassTextBox.initializeBase(this, [element]);      this._weakCssClass = null;      this._mediumCssClass = null;      this._strongCssClass = null;  }    //define class  AJAXEnabled.PassTextBox.prototype = {        //initialize the UI control        initialize: function() {          AJAXEnabled.PassTextBox.callBaseMethod(this, 'initialize');          this._onKeyupHandler = Function.createDelegate(this, this._onKeyup);          $addHandlers(this.get_element(), {'keyup' : this._onKeyup}, this);      },        dispose: function() {          $clearHandlers(this.get_element());          AJAXEnabled.PassTextBox.callBaseMethod(this, 'dispose');      },        //define key press event        _onKeyup: function (e) {            //get password text          var pass = this.get_element().value;          var strength = this.returnPasswordStrength(pass);            switch (strength) {              case "Weak":                  this.get_element().className = this._weakCssClass;                  break;              case "Medium":                  this.get_element().className = this._mediumCssClass;                  break;              case "Strong":                  this.get_element().className = this._strongCssClass;                  break;          }      },        //define properties      get_weakCssClass: function() {          return this._weakCssClass;      },      set_weakCssClass: function(value) {          this._weakCssClass = value;      },      get_mediumCssClass: function() {          return this._mediumCssClass;      },      set_mediumCssClass: function(value) {          this._mediumCssClass = value;      },      get_strongCssClass: function() {          return this._strongCssClass;      },      set_strongCssClass: function(value) {          this._strongCssClass = value;      },        returnPasswordStrength: function(password) {          var strPass = new String(password.toString());          if (strPass.length < 5) {              return "Weak";          }          else {              if (strPass.length < 8) {                  return "Medium";              }              else {                  return "Strong";              }          }      }  }    //register class as a Sys.Control  AJAXEnabled.PassTextBox.registerClass('AJAXEnabled.PassTextBox',Sys.UI.Control);    //notify loadedif (typeof(Sys) !== 'undefined') Sys.Application.notifyScriptLoaded();
```

#### Use the AJAX Client Control on a Web Page
```c#
<head id="Head1" runat="server">
<title> </title>
<style type="text/css">
.weak{border: thin solid #FF0000;}
.medium{border: thin solid #FFFF00;}
.strong{border: medium solid #008000;}
</style>
</head>
<body>
<form id="form1" runat="server">
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference Path="passtextbox.js" />
</Scripts>
</asp:ScriptManager>
<asp:CalendarExtender ID="CalendarExtender1" runat="server" TargetControlID="TextBox1">
</asp:CalendarExtender>
<asp:TextBox ID="TextBox1" runat="server"> </asp:TextBox>
<div style="font-size: large; font-weight: bold">User Login </div>
<hr />
<br />
User Name:
<br />
<asp:TextBox ID="TextBoxUserName" runat="server" Width="200"> </asp:TextBox>
<br />
Password:
<br />
<asp:TextBox ID="TextBoxPassword" runat="server" TextMode="Password" Width="200"> </asp:TextBox>
<asp:Label ID="LabelStrength" runat="server" Text=""> </asp:Label>
<br />
<input id="Button1" type="button" value="Submit" />

<script language="javascript" type="text/javascript">
var app = Sys.Application;
app.add_init(MyInit);

function MyInit(sender, args) {
$create(AJAXEnabled.PassTextBox,
{
weakCssClass: 'weak',
mediumCssClass: 'medium',
strongCssClass: 'strong'
},
null, null, $get('TextBoxPassword'));
}
</script>

</form>
</body>
```

## Encapsulate an AJAX Client Control as a Custom Control

#### Embed an AJAX Client Control as a Custom Control

#### Steps:

1. create a new ASP.NET Web site named **AjaxEnabled**
2. Add a new class library named **PassTextBox**
3. Add reference ：System.Web、System.Web.UI、System.Web.UI.WebControls;。
4. Set default namespace of the project to **AjaxEnabled** and also set the assembly name to **AjaxEnabled**.
5. Indicate **AjaxEnabled** class inherits from the TextBox control and implements the **IScriptControl**.  
Next, add a private variable to track a [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control at the class level.  
And, add three fields to the control for managing the Password text box style properties based on password strength
6. Implete the method **GetScriptDescriptors** of **IScriptControl** Interface .  
This method is meant to define the properties and events that work with the client control.
7. Implete the method **GetScriptReference.** of **IScriptControl** Interface .  
This method is meant to get a reference to the JavaScript that is used by this custom control.  
In this case, the JavaScript will be embedded in the same assembly.
8. fill out the rest of this class.
9. The final step is to embed your JavaScript file as a resource to this class.  

1. Copy the PassTextBox.js file created in the prior lab to your PassTextBox class library project
2. In Solution Explorer, view properties for the PassTextBox.js file. Set the Build Action property to embedded resource.          
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFCU5I2H87dprtQJrFFYqoKGly4Mq_PrZWloJQRV_b5NWH0M45JlRSOJcsMPxac9vTdTqEkOHrSOfIhJRD5ymv_gh1TqyHBcCWLN8UlXR_V0lSFXIkV3rE1Cg_agirdR-aMXG33GsJKog/s241/embeded-resource.png)
3. Open the project's AssemblyInfo file and add the following Web resource assembly definition:   
```c#
[assembly: System.Web.UI.WebResource("AjaxEnabled.PassTextBox.js", "text/javascript")]
```
10. Finally, build the project.  
You should now have a custom server control based on the TextBox class   that also uses an embedded AJAX UI control targeted to work with the Microsoft AJAX Library.

#### Code:
```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

namespace AjaxEnabled
{
public class PassTextBox : TextBox, IScriptControl
{

// 5 
// add a private variable to track a ScriptManager control

private ScriptManager sMgr;

// add three fields to the control for managing the Password text box style

public string WeakCssClass;
public string MediumCssClass;
public string StrongCssClass;

// 6 
// Implete the method GetScriptDescriptors of IScriptControl Interface .
// This method is meant to define the properties and events that work with the client control. 
// Here, you want to add three property descriptors, one for each password strength style

protected virtual IEnumerable<ScriptDescriptor> GetScriptDescriptors()
{
ScriptControlDescriptor descriptor =
new ScriptControlDescriptor("AjaxEnabled.PassTextBox", this.ClientID);

descriptor.AddProperty("weakCssClass", this.WeakCssClass);
descriptor.AddProperty("mediumCssClass", this.MediumCssClass);
descriptor.AddProperty("strongCssClass", this.StrongCssClass);
return new ScriptDescriptor[] { descriptor };
}

// 7
// Implete the method GetScriptReferences of IScriptControl Interface .
// This method is meant to get a reference to the JavaScript that is used by this custom control. 
// In this case, the JavaScript will be embedded in the same assembly

protected virtual IEnumerable<ScriptReference> GetScriptReferences()
{
ScriptReference reference = new ScriptReference();
reference.Assembly = "AjaxEnabled";
reference.Name = "AjaxEnabled.PassTextBox.js";
return new ScriptReference[] { reference };
}

// 8 
// fill out the rest of this class.
// This code is straightforward and common to all controls of this nature. 

protected override void OnPreRender(EventArgs e)
{
if (!this.DesignMode)
{
//test for the existence of a ScriptManager
sMgr = ScriptManager.GetCurrent(Page);

if (sMgr == null)
throw new HttpException("A ScriptManager control must exist on the page.");

sMgr.RegisterScriptControl(this);
}
base.OnPreRender(e);
}

protected override void Render(HtmlTextWriter writer)
{
if (!this.DesignMode)
sMgr.RegisterScriptDescriptors(this);

base.Render(writer);
}

IEnumerable<ScriptDescriptor> IScriptControl.GetScriptDescriptors()
{
return GetScriptDescriptors();
}

IEnumerable<ScriptReference> IScriptControl.GetScriptReferences()
{
return GetScriptReferences();
}
}
}
```

#### Use the Custom AJAX Control on a Web Page

At the top of the page, add a directive to register the custom control.   This directive should point to the assembly that contains the custom control created previously.  
```xml
<%@ Register Assembly="AjaxEnabled" Namespace="AjaxEnabled" TagPrefix="ajaxEnabled" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml">
<head id="Head1" runat="server">
<title></title>
<style type="text/css">
.weak{border: thin solid #FF0000;}
.medium{border: thin solid #FFFF00;}
.strong{border: medium solid #008000;}
</style>
</head>
<body>
<form id="form1" runat="server">

<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>

<ajaxEnabled:PassTextBox ID="textbox1" runat="server" width="200" TextMode="Password" 
WeakCssClass="weak" MediumCssClass="medium" StrongCssClass="strong"></ajaxEnabled:PassTextBox>

</form>
</body>
</html>
```
## 參考資料  

- [JavaScript語言教學](http://tr.ilvs.ilc.edu.tw/~bbfish/learn/javascript/javascript.htm)
- [使用原型模型建立用戶端元件類別](http://msdn.microsoft.com/zh-tw/library/bb386415.aspx)
- [使用 Microsoft](http://msdn.microsoft.com/zh-tw/library/bb386453.aspx)[Ajax Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 建立自訂用戶端指令碼
- [Microsoft](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx)[Ajax Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) 用戶端參考
- [深究 Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/magazine/cc163300.aspx)
- [Inside the Microsoft AJAX Library](http://msdn.microsoft.com/en-us/magazine/cc163300.aspx)
- [建立擴充項控制項讓用戶端行為與 Web 伺服器控制項產生關聯](http://msdn.microsoft.com/zh-tw/library/bb386403.aspx)