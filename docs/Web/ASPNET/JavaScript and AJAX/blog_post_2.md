---
title: Client-Side Script
layout: default
parent: JavaScript and AJAX
nav_order: 2
description: "Client-Side Script"
date: 2012-10-26
tags: [Web,ASPNET,JavaScript and AJAX]
postid: "5700334615621151164"
---
#### JavaScript

It is important to note that JavaScript is not new.   
It is also not really Java.   
It is a C-based scripting language invented by Netscape to add client scripting capabilities to a browser.   
It is now supported as part of every major browser on the market.   
The actual language is controlled and managed by a standards body, the European Computer Manufacturers Association (ECMA).   
Because it is widely deployed and offers developers the ability to write code on the client, it has become increasingly popular.   
This was especially true once features for communicating between the client script and server code using JSON and XML became prevalent.  

However, the JavaScript language still lacks basic object-oriented concepts found in modern programming languages.   It also lacks a standard framework for developers to program against.   The [Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) was written (in JavaScript) to provide these constructs.  This library combined with the AJAX Server Controls, and the support for Visual Studio IntelliSense for JavaScript makes building AJAX-enabled applications more approachable.  

JavaScript 是一種簡單的程式設計語言，它實際上只是一種瀏覽器實作標準化 [ECMAScript](http://zh.wikipedia.org/wiki/ECMAScript) 語言(標準化的腳本程式語言)的結果，其它像 VBScript 也是。  這種語言的主要用途是將功能嵌入 HTML 網頁中，讓使用者的動作與網頁中的項目結合起來。  例如利用 JavaScript，您可以透過程式設計方式重新命名、編輯、加入或刪除所顯示文件中的項目，也可以處理這類項目所引發的任何事件。  此外，您還可以執行任何瀏覽器相關動作，例如開啟或彈出新的視窗，或叫用常用的 XMLHttpRequest 物件並以非同步的方式呼叫遠端 URL (這也是 AJAX 特殊之處)。  

# Creating your own Client Scripts

要在網頁中加入 Javascript 有以下幾種方法：

- 直接在網頁中加入 Script 區塊。
- 在執行階段，於 server-code 中，使用 [ClientScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.aspx) 類別動態新增 JavaScript 到頁面。
- 在 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項中註冊 JavaScript .

## 1. Adding Script Blocks to Your ASP.NET Page

You can define a script block and a function inside the head section of the .aspx markup or define a link to an external JavaScript file.  

#### define script function in script block
```xml
<head>
<script language="javascript" type="text/javascript">
function Collapse() {
if (DivCollapse.style.display == "") {
DivCollapse.style.display = "none";
document.forms[0].ButtonCollapse.value = "Open";
}
else {
DivCollapse.style.display = "";
document.forms[0].ButtonCollapse.value = "Close";
}
}
</script>
</head>
```

#### define a link to .js file
```xml
<script type="text/javascript" src="MyScript.js"></script>
```

## 2. Adding Script Dynamically to Your ASP.NET Page

用戶端指令碼是以索引鍵和型別唯一識別的。 具有相同索引鍵和型別的指令碼會被視為重複項目。  一組指定的型別和索引鍵組合只能向網頁註冊一個指令碼，若嘗試註冊已註冊的指令碼並不會建立該指令碼的複本。   

透過 [Page.ClientScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.clientscript.aspx) 屬性，可以取得 [ClientScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.aspx) 物件。  它可以用來管理 Web 應用程式中的用戶端指令碼。透過這個類別的可以將 javascript 註冊到網頁中。  這個物件提供多種方法用來註冊不同需求的用戶端指令碼：  

- [RegisterClientScriptBlock](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerclientscriptblock.aspx) ：向 Page 物件註冊用戶端指令碼。
- [RegisterOnSubmitStatement](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registeronsubmitstatement.aspx) ：向 Page 物件註冊 OnSubmit 陳述式。
- [RegisterStartupScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerstartupscript.aspx) ：向 Page 物件註冊啟始指令碼。
- [RegisterClientScriptInclude](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerclientscriptinclude.aspx) ：將指定的 .js 檔的連結加到網頁中。
```c#
public static void RegisterClientScriptBlock(Control control,   Type type, string key, string script, bool addScriptTags);
public static void RegisterClientScriptBlock(Page page,         Type type, string key, string script, bool addScriptTags);

public static void RegisterClientScriptInclude(Control control, Type type, string key, string url);
public static void RegisterClientScriptInclude(Page page,       Type type, string key, string url);

public static void RegisterStartupScript(Control control,       Type type, string key, string script, bool addScriptTags);
public static void RegisterStartupScript(Page page,             Type type, string key, string script, bool addScriptTags);

public static void RegisterOnSubmitStatement(Control control,   Type type, string key, string script);
public static void RegisterOnSubmitStatement(Page page,         Type type, string key, string script);
```

### 1) 替 Control 的 Event 加上 Script

下面這個例子，當 script 被加入到控制項後，postback之後仍會被記住。
```c#
protected void Button2_Click(object sender, EventArgs e)
{
Button1.Attributes.Add("onclick", "alert('test');");
}
```

### 2) 使用 RegisterClientScriptBlock 註冊 Script
```c#
public static void RegisterClientScriptBlock(Control control, Type type, string key, string script, bool addScriptTags);
public static void RegisterClientScriptBlock(Page page, Type type, string key, string script, bool addScriptTags);
```

使用 [RegisterClientScriptBlock](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerclientscriptblock.aspx) 方法可以將指定的 script 加到網頁中。  
加入的指令碼區塊會被插入至所呈現頁面的**頂端**，如 &lt;form&gt; 標記的下方。  
其中 addScriptTags 參數是用來指定是否要由系統幫我們加入 &lt;script&gt; 標籤。  

這個方法會將 script 插 &lt;form&gt; 標記的下方，表示大部份的元件都尚未載入時，該指令碼就已經被註冊上去了。  
```xml
<div>
Enter Password<br />
<asp:TextBox ID="TextBox1" runat="server" Width="250"></asp:TextBox>&nbsp;&nbsp;<span id="passwordStrength"></span>
<br />
<asp:CheckBox ID="CheckBox1" runat="server" Text="執行密碼檢核" 
AutoPostBack="true" OnCheckedChanged="CheckBox1_CheckedChanged" />
</div>
```
```c#
protected void CheckBox1_CheckedChanged(object sender, EventArgs e)
{
if (CheckBox1.Checked)
{
string passFunc = @"
function CheckPassword() 
{
var passLen = document.forms[0].TextBox1.value.length;
if (passLen < 4) {
document.getElementById('passwordStrength').innerText = 'weak';
document.getElementById('passwordStrength').style.color = 'red';
}
else if (passLen < 6) {
document.getElementById('passwordStrength').innerText = 'medium';
document.getElementById('passwordStrength').style.color = 'blue';
} else if (passLen > 9) {
document.getElementById('passwordStrength').innerText = 'strong';
document.getElementById('passwordStrength').style.color = 'green';
} 
}";
//註冊用戶端指令碼
Page.ClientScript.RegisterClientScriptBlock(this.GetType(),"CheckPasswordScript", passFunc, true);

//在 TextBox 中加入呼叫 script 的事件
TextBox1.Attributes.Add("onkeyup", "CheckPassword()");
}
else
{
//remove the event from the text box
TextBox1.Attributes.Remove("onkeyup");
}

}
```

### 3) 使用 RegisterOnSubmitStatement 註冊 Script

使用 [RegisterOnSubmitStatement](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registeronsubmitstatement.aspx) 方法會先在網頁中，將你的 script 自動寫進去 function WebForm\_OnSubmit() ，  同時在 form 標籤中加入 **onsubmit="javascript:return WebForm\_OnSubmit();"** 事件。  
```c#
string key = "SubmitCheck";
string script = @"alert('test');";
Page.ClientScript.RegisterOnSubmitStatement(this.GetType(), key, script);
```
```javascript
<script type="text/javascript">
function WebForm_OnSubmit() {
alert('test');
return true;
}
</script>

<form method="post" action="WebForm1.aspx" onsubmit="javascript:return WebForm_OnSubmit();" id="form1">
...
</form>
```

### 4) 使用 RegisterStartupScript 註冊 Script

使用 [RegisterStartupScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerstartupscript.aspx) 方法可以將指定的 script 加到網頁中，並將指令碼區塊插入至所呈現頁面的**底端**，如  &lt;/form&gt; 標籤的上方。  

這個方法最常被用在網頁載入完成後，必須執行一些初始化的工作。  例如，你想要使用 Focus() 將焦點設定在某個元件上，就可以利用這個方法來註冊程式碼，因為這個方法會將程式碼插入至頁面的**底端**，表示所有元件都已經載入完成。  如果使用 [RegisterClientScriptBlock](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerclientscriptblock.aspx) 註冊程式碼，就會發生找不到元件的錯誤。  
```c#
key = "StartupTest";
script = @" <script> function StartupTest() { alert('test RegisterStartupScript'); }  </script>";
Page.ClientScript.RegisterStartupScript(this.GetType(), key, script);
```
```javascript
....
<script>
function StartupTest() { alert('test RegisterStartupScript'); } 
</script>
</form> 
</body>
```

### 5) 使用 RegisterClientScriptInclude 註冊 Script 檔

使用 [RegisterClientScriptInclude](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.registerclientscriptinclude.aspx) 方法可以將指定的 .js 檔的連結加到網頁中。不過要注意的是，這段指令碼會插入至 &lt;form&gt; 標籤的下方。  
```c#
protected void btnRegisterClientScriptInclude_Click(object sender, EventArgs e)
{
ClientScript.RegisterClientScriptInclude("ScriptInclude", "test.js");
}
```

### 6) 使用 Page.Header.Controls.Add 方法，將 .js 檔 include 在 head 區塊裡
```c#
protected void btnHeadAddScript_Click(object sender, EventArgs e)
{
HtmlGenericControl script = new HtmlGenericControl("script");
script.Attributes.Add("language", "javascript");
script.Attributes.Add("src", "test.js");
this.Page.Header.Controls.Add(script);
}
```

## 3. Registering Client Scripts with the ScriptManager Control

若網頁使用 AJAX 功能時，我們通常會先在頁面中加入 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項，這時候若要動態加入 script ，就可以透過 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 控制項。  它允許直接使用宣告方式或透過程式碼的方式來註冊客戶端 Script 。  
```c#
public static void RegisterClientScriptBlock(Control control, Type type, string key, string script, bool addScriptTags);
public static void RegisterClientScriptBlock(Page page, Type type, string key, string script, bool addScriptTags);
public static void RegisterClientScriptInclude(Control control, Type type, string key, string url);  public static void RegisterClientScriptInclude(Page page, Type type, string key, string url);    public static void RegisterStartupScript(Control control, Type type, string key, string script, bool addScriptTags);  public static void RegisterStartupScript(Page page, Type type, string key, string script, bool addScriptTags);    public static void RegisterOnSubmitStatement(Control control, Type type, string key, string script);  public static void RegisterOnSubmitStatement(Page page, Type type, string key, string script);  ```

### 1) To add a script declaratively

You can use  **&lt;Scripts&gt;** collection element of the [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control to add a script to the ScriptManager.  For example:  
```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Scripts>
<asp:ScriptReference Name="SiteScripts.js" />
</Scripts>
</asp:ScriptManager>
```

### 2) To add a script programmatically

在 server code 中使用 [ScriptReference](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptreference.aspx) 類別，將用戶端指令碼加入到 Scripts 集合。
```c#
protected void btnAddScriptReference_Click(object sender, EventArgs e)
{
ScriptReference sr = new ScriptReference("test.js");
ScriptManager1.Scripts.Add(sr);
}
```

Note that if your JavaScript file is embedded in an assembly you can add the Assembly attribute to the [ScriptReference](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptreference.aspx) tag and point to the .dll file.   This is useful when you do not have full source code files or are working with precompiled objects.

### 3) 使用 RegisterClientScriptBlock 方法註冊 Script

使用 ScriptManager.RegisterClientScriptBlock 方法，和 Page.ClientScript.RegisterClientScriptBlock 的用法大至相同，其他同名的方法也都可以使用，只是參數有所不同，要注意。
```c#
protected void btnScriptManager_RegisterClientScriptBlock_Click(object sender, EventArgs e)
{
//判斷網頁是不是有使用 ScriptManager 物件
if (ScriptManager.GetCurrent(this.Page) == null)
{
Page.ClientScript.RegisterStartupScript(this.Page.GetType(), "alert", "alert('Page.ClientScript.RegisterClientScriptBlock');", true);
}
else
{
ScriptManager.RegisterStartupScript(this.Page, this.Page.GetType(), "alert", "alert('ScriptManager.RegisterClientScriptBlock');", true);
}
}
```

## 複合指令碼

假設一個網頁要用到多個 JavaScript 檔案，由於下載多個檔案較下載一個檔案耗時，這時候可以將多個 JavaScript 檔案結合為單一複合指令碼。  方法是在 ScriptManager 控制項中利用 &lt;CompositeScript&gt; 標籤加入指令碼的參考。
```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">
<CompositeScript>
<Scripts>
<asp:ScriptReference Path="~/Scripts/Script1.js" />
<asp:ScriptReference Path="~/Scripts/Script2.js" />
<asp:ScriptReference Path="~/Scripts/Script3.js" />
</Scripts>
</CompositeScript>
</asp:ScriptManager>
```

## RolloverButton 範例

RolloverButton 是在網頁中常見的一種效果，就是使用一張圖片表示一個按鈕，但是當滑鼠移到按鈕上方時，會以另一張圖形顯示。  底下，我們就使用上頭提到的技巧來實作這個效果。  

首先，我們在頁上中放置一個ImageButton 。  
```xml
<asp:ImageButton ID="ImageButton1" runat="server" ImageUrl="~/Images/ad1.jpg"/>
```

接著，在 Page\_Load 事件中加入以下程式碼。  
```c#
protected void Page_Load(object sender, EventArgs e)
{
string script = @"
if (document.images) {
MyButton = new Image;
MyButtonShaded = new Image;
MyButton.src = '../Images/ad1.jpg';
MyButtonShaded.src = '../Images/ad2.jpg';
}
else {
MyButton = '';
MyButtonShaded = '';
}";

Page.ClientScript.RegisterClientScriptBlock(this.GetType(), "RolloverScript", script, true);

ImageButton3.Attributes.Add("onmouseover",
"this.src = MyButtonShaded.src; window.status='Oh Yes! Click here!';");
ImageButton3.Attributes.Add("onmouseout",
"this.src = MyButton.src; window.status='';");
}
```
## 參考資料  

- [ASP.NET 用戶端指令碼](http://msdn.microsoft.com/zh-tw/library/dd410060.aspx)
- [Microsoft AJAX](http://msdn.microsoft.com/zh-tw/library/ee341002%28v=vs.100%29.aspx)
- [深究 Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/magazine/cc163300.aspx)
- [使用Microsoft AJAX Library去建造簡單的class](http://bibby.be/2008/09/microsoft-AJAX-libraryclass.html)

# Creating your own Client Callbacks

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEge-GHMZjKBrn3J-AWpD30IAzIuXrMkv3Wmr7lB3l7tjBFTB7XODO1ztbunMAX24jwVZPFs6eVlrJIERjjW5WglKu2GvGL3pbxbnDeyaunCwmf6Hv_ZXbtYcTs-DSF_SS0zGjl4z2qppW0/s521/page-lifecycle-postback-vs-callback.png)

要設計 Client 端 Callback ，可參照以下步驟：  

#### Server Side

1. 在**主機端**實作 System.Web.UI.**ICallbackEventHandler** 介面：  
這個介面主要有二件事：處理用戶端的呼叫和回傳結果給用戶端。  
所以必須實作以下二個方法：  

- 實作 **RaiseCallbackEvent** 方法：處理用戶端的呼叫
- 實作 **GetCallbackResult** 方法：回傳結果給用戶端
2. 在用戶端建立回呼函式：  
回呼函式是讓主機端執行完畢後呼叫用。
3. 在用戶端建立一個呼叫主機端函式：  
這個函式的實際呼叫程式碼，可以由主機端的 [GetCallbackEventReference](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.getcallbackeventreference.aspx) 方法取得。

####  [GetCallbackEventReference](http://msdn.microsoft.com/zh-tw/library/system.web.ui.clientscriptmanager.getcallbackeventreference.aspx) 是一個多載方法，主要用法如下：
```c#
public string GetCallbackEventReference(Control control, string argument, string clientCallback, string context);
//control
//argument : 從用戶端指令碼傳遞到伺服器的引數
//clientCallback : 用戶端的回呼函式名稱
//context : 在啟始回呼 (Callback) 之前，於用戶端上評估的用戶端指令碼
```
```c#
public partial class CallBack_Default : Page, ICallbackEventHandler
{
protected void Page_Load(object sender, EventArgs e)
{
if (!this.IsCallback)
{
//使用 GetCallbackEventReference 產生呼叫主機端的程式碼
string cbReference = Page.ClientScript.GetCallbackEventReference(this, "arg", "ReceiveServerData", "context");
string callbackScript = @"
function CallServer(arg, context) 
{ " + 
cbReference + @";
}";
//將呼叫主機端的程式碼註冊到頁面
Page.ClientScript.RegisterClientScriptBlock(this.GetType(), "CallServer", callbackScript, true);
}
}

protected string returnValue;

//處理從 Client 端傳來的值，其中 eventArgument 為觸發事件的參數
public void RaiseCallbackEvent(string eventArgument)
{
returnValue = " <font style='color:" + eventArgument + "'>" + System.DateTime.Now.ToString() + " </font>";
}

//處理要回傳給 Client 端的值
public string GetCallbackResult()
{
return returnValue;
}
}
```
```xml
<head id="Head1" runat="server">
<title> </title>
<script type="text/ecmascript">
function GetServerTime() {
var your_arg = "";

if (document.getElementById('Radio1').checked) {
your_arg = document.getElementById('Radio1').value;
}
else if (document.getElementById('Radio2').checked) {
your_arg = document.getElementById('Radio2').value;
}
else {
your_arg = document.getElementById('Radio3').value;
}
CallServer(your_arg, "");
}

function ReceiveServerData(rValue) {
document.getElementById("Msg").innerHTML = rValue;
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<input id="Radio1" type="radio" value="Red" name="timecolor" />Red <br />
<input id="Radio2" type="radio" value="Green" name="timecolor" />Green <br />
<input id="Radio3" type="radio" value="Blue" name="timecolor" checked />Blue <br />
<input type="button" value="get server time" onclick="GetServerTime()" /> <br />
<span id="Msg"> </span>
</div>
</form>
</body>
```
## 參考資料  

- [用戶端回呼實作 (C#) 範例](http://msdn.microsoft.com/zh-tw/library/ms178210%28v=vs.80%29.aspx)
- 
-