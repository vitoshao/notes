---
title: jQuery 失效問題
layout: default
parent: JQuery
nav_order: 1
description: "jQuery 失效問題"
date: 2016-01-28
tags: [Web,JS,JQuery]
postid: "7163347943702993388"
---
## jQuery 失效問題

在一個 WebForm 中，若使用 UpdatePanel 做局部更新，且 UpdatePanel 中的元件同時使用 jQuery 進行操作，這時 jQuery 功能只會在網頁第一次載入後有效，只要 UpdatePanel 中發生任何 Postback 行為，那麼 jQuery 功能將會失效。  例如下面這個例字，如果按了 Button1 產生 Postback 之後再按 Click 就不會再執行 alert() 功能。  
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="Scripts/jquery/jquery-1.11.2.js"></script>
<script type="text/javascript">
$(document).ready(function () {
$("#Click1").click(function () {
alert('Click1');
});
});
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
<asp:UpdatePanel ID="UpdatePanel1" runat="server" UpdateMode="Conditional">
<ContentTemplate>
<asp:Button ID="Click1" runat="server" Text="Click1" OnClientClick="return false" />
<asp:Button ID="Button1" runat="server" Text="PostBack"/>
</ContentTemplate>
</asp:UpdatePanel>
</div>
</form>
</body>
```

這是因為在 Postback 之後，UpdatePanel 中的元件會重新繪製，但不會再重新執行 $(document).ready() 函式，所以原先的事件綁定行為也就不存在了。

## 解決 jQuery 失效問題

要解決 jQuery 失效問題，有以下二個方法：  

### 將事件綁定設定寫在 Ajax 的 End\_request 事件中

當 ScriptManager 發生 Postback 之後都會引發 EndRequest 事件，所以只要註冊這個事件，並且在這個事件中重新執行控制項的綁定行為。  
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="Scripts/jquery/jquery-1.11.2.js"></script>
<script type="text/javascript">
$(document).ready(function () {
BindEvents();
});
function BindEvents() {
$("#Click2").click(function () {
alert('Click2');
});
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
<script type="text/javascript">
Sys.WebForms.PageRequestManager.getInstance().add_endRequest(EndRequest);
function EndRequest(sender, args) {
if (args.get_error() == undefined) {
BindEvents();
}
}
</script>
<asp:UpdatePanel ID="UpdatePanel2" runat="server" UpdateMode="Conditional">
<ContentTemplate>
<asp:Button ID="Click2" runat="server" Text="Click2" OnClientClick="return false" />
<asp:Button ID="Button2" runat="server" Text="PostBack"/>
</ContentTemplate>
</asp:UpdatePanel>
</div>
</form>
</body>
```

### 將事件綁定設定寫在 pageLoad() 方法中

因為 WebForm 不管是發生 PageLoad 或 Postback ，當返回用戶端時都會執行 pageLoad() 方法，所以也可以將事件綁定的設定寫在 pageLoad() 方法中即可。  
```xml
<head id="Head1" runat="server">
<title></title>
<script type="text/javascript" src="Scripts/jquery/jquery-1.11.2.js"></script>
<script type="text/javascript">
function pageLoad() {
$("#Click3").click(function () {
alert('Click3');
});
} 
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
<asp:UpdatePane3 ID="UpdatePanel1" runat="server" UpdateMode="Conditional">
<ContentTemplate>
<asp:Button ID="Click3" runat="server" Text="Click3" OnClientClick="return false" />
<asp:Button ID="Button3" runat="server" Text="PostBack"/>
</ContentTemplate>
</asp:UpdatePanel>
</div>
</form>
</body>
```
## 參考資料  

- [jQuery does not work properly after ajax partial postback](http://www.jquerybyexample.net/2010/07/jquery-does-not-work-properly-after.html)
- [\[jQuery\] UpdatePanel裡的控制項在PostBack之後發現jQuery事件失效](http://www.dotblogs.com.tw/kevintan1983/archive/2014/02/06/142808.aspx)