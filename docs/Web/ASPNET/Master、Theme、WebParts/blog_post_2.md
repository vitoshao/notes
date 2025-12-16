---
title: Master Page
layout: default
parent: Master、Theme、WebParts
nav_order: 2
description: "Master Page"
date: 2012-10-08
tags: [Web,ASPNET,Master、Theme、WebParts]
postid: "7579817612663865260"
---
Advantages of using master pages include the following:

- allow you to centralize the common functionality of your pages.
- allow you to create one controls and code and apply the results to a set of pages.
- allow you to control over the layout of the final pages
- allow you to customize the master pages from individual content pages.

# 主版頁面

- Master page is defined with the file extension **.master** 。
- Master page inherits from the [MasterPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.aspx) Class。
- Master page contain an **@ Master** directive 。
- Master pages use [ContentPlaceHolder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.contentplaceholder.aspx) controls to defines an area for content pages。

## 建立 MasterPage 和 ContentPage

下面是一個主版頁面與內容頁面的範例：

#### Master Page

繼承 [MasterPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.aspx) 類別。
```xml
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site1.master.cs" Inherits="Practice.Site1" %>

<head id="Head1" runat="server">
<title></title>
<asp:ContentPlaceHolder ID="head" runat="server">
</asp:ContentPlaceHolder>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ContentPlaceHolder ID="ContentPlaceHolder1" runat="server">

</asp:ContentPlaceHolder>
</div>
</form>
</body>
```
```c#
public partial class Site1 : System.Web.UI.MasterPage
{
protected void Page_Load(object sender, EventArgs e)
{
}
}
```

#### Content Page

使用 [MasterPageFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.masterpagefile.aspx) 屬性，指定使用的主版頁面。
```xml
<%@ Page Language="C#" ...... MasterPageFile="Site1.Master" %>

<asp:Content ID="Content1" ContentPlaceHolderID="head" runat="server">
</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="ContentPlaceHolder1" runat="server">
</asp:Content>
```

有一點要注意：若在 Master Page 的 [ContentPlaceHolder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.contentplaceholder.aspx) 中有置放任何控制項，只要 Content Page 有指定使用該區塊，那麼在 Master Page 的控制項都會清除。  除非 Content Page 完全沒有使用該 [ContentPlaceHolder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.contentplaceholder.aspx) ，那麼在主版頁的控制項才會出現在 Content Page。  

例如，在 [MasterPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.aspx) 中有個選單，若 ContentPage 要使用自已的選單，就可以加入一個 &lt;Content&gt;&lt;/Content&gt; ，它自然就會覆蓋主版頁的選單。  若不加入 Content 控制項，就會延用主版頁的選單。

## MasterPage 等級：

1. 頁面等級（page level）：在每個頁面繫結主版頁面。  ```xml
<%@ Page Language="C#" MasterPageFile="MySite.Master" %>
```
2. 應用程式等級（application level）：設定 web.config 。  ```xml
<pages masterPageFile="MySite.Master" />
```
3. 目錄等級（folder level）：設定目錄中的 web.config 。

## MasterPage 事件的順序：

Page\_PreInit  
&lt;span style="color:Blue"&gt;UserControl.Page\_Init&lt;/span&gt;&lt;br/&gt;  &lt;span style="color:Orange"&gt;MasterPage.Page\_Init&lt;/span&gt;&lt;br/&gt;  Page\_Init&lt;br/&gt;  Page\_InitComplete&lt;br/&gt;  Page\_Load&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Orange"&gt;MasterPage.Page\_Load&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Blue"&gt;UserControl.Page\_Load&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Green"&gt;Button1\_Click&lt;/span&gt;&lt;br/&gt;  Page\_LoadComplete&lt;br/&gt;  Page\_PreRender&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Orange"&gt;MasterPage.Page\_PreRender&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Green"&gt;TextBox1\_PreRender&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Green"&gt;Button1\_PreRender&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Blue"&gt;UserControl.Page\_PreRender&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Blue"&gt;UserControl.TextBox1\_PreRender&lt;/span&gt;&lt;br/&gt;  &nbsp;&nbsp;&nbsp;&nbsp;&lt;span style="color:Blue"&gt;UserControl.Button1\_PreRender&lt;/span&gt;&lt;br/&gt;  Page\_PreRenderComplete&lt;br/&gt;  &lt;/div&gt;    &lt;p&gt;  1.在 Load 事件之前，MasterPage 的事件會先觸發，之後則由 ContentPage 的事件先觸發。  2.在 MasterPage 中，最早的事件是 OnInit 。  &lt;/p&gt;  &lt;/div&gt;  &lt;/div&gt;    &lt;h1 class="collapsable" onclick="displayDiv('MasterPage 與 ContentPage 的互動')"&gt;MasterPage 與 ContentPage 的互動&lt;/h1&gt;  &lt;div id="MasterPage 與 ContentPage 的互動"&gt;    &lt;h2 class="collapsable" onclick="displayDiv('讀取 MasterPage 中的屬性與方法')"&gt;由 ContentPage 讀取 MasterPage 中的屬性與方法&lt;/h2&gt;  &lt;div id="讀取 MasterPage 中的屬性與方法"&gt;    &lt;p&gt;The basic process to reference master page properties from a content page is as follows:&lt;/p&gt;  &lt;ol&gt;  &lt;li&gt;  在 &lt;a target="\_blank" href="http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.aspx"&gt;MasterPage&lt;/a&gt; 中建立公開屬性.&lt;br /&gt;  &lt;div class="source\_code"&gt;&lt;pre class="brush:c#;" title="Master Page"&gt;  public string CompanyName   {      get      {          return (string)ViewState["CompanyName"];      }      set      {          ViewState["CompanyName"] = value;      }  }    protected void Page\_Load(object sender, EventArgs e)  {      CompanyName = "vito corp.";  }
- 在 ContentPage 中設定「**[＠ MasterType](http://msdn.microsoft.com/zh-tw/library/ms228274.aspx)**」宣告，用以建立對主版類別的參考。  
```xml
<%@ Page Title="" Language="C#" MasterPageFile="~/Site1.Master" ...%>

<%@ MasterType VirtualPath="~/Site1.master" %>
```
- 由 ContentPage 中，使用 **Master** 類別，讀取 [MasterPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.masterpage.aspx) 中的 Properties, Methods。  ```c#
protected void Page_PreRender(object sender, EventArgs e)
{
//access Master's Property
Page.Header.Title = Master.CompanyName;

//invoke Master's Method
Page.Header.Title = Master.GetCompanyName();
}
```

## 由 ContentPage 讀取 MasterPage 的 Controls

在內容頁，除了可以讀取主版頁的屬性之外，也可以呼叫 [Master.FindControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.findcontrol.aspx) 方法，利用控制項的名稱找出控制項。
```c#
protected void Page_PreRender(object sender, EventArgs e)
{
//reference Control
Label Label1 = (Label)Master.FindControl("Label1");
Page.Header.Title = Label1.Text;
}
```

## 由 MasterPage 讀取 ContentPage 的 Controls

叫用 ContentPlaceHolder.FindControl 以取得內容頁的控制項。
```c#
Button button1 = ContentPlaceHolder1.FindControl("btnTest") as Button;
if (button1 != null)
{
myMessage.Show(button1.Text + " button exist");
}
```

## 由 MasterPage 叫用 ContentPage 的 Method

透過 ContentPlaceHolder 可以取得 ContentPage 中的 Page 型別，就可以叫用它的方法
```c#
ContentPage_with_MasterPage contentpage1 = ContentPlaceHolder1.Page as ContentPage_with_MasterPage;
contentpage1.hello();
```

不過，以上做法並不好，這將使得主版頁與內容頁綁的太緊。若要執行內容中的方法，也可以引發事件，由內容頁自行訂閱處理，會是比較好的做法。
```c#
public event EventHandler TestButtonClick;

protected void RaiseEvent_Click(object sender, EventArgs e)
{
if (TestButtonClick != null)
TestButtonClick(sender, e);
}
```
```c#
protected void Page_PreInit(object sender, EventArgs e) {
// Create an event handler for the master page's TestButtonClick event  
Master.TestButtonClick += new EventHandler(TestButtonClick); 
}

private void TestButtonClick(object sender, EventArgs e)
{
myMessage.Show("TestButtonClick inf Content Page");
}
```

如果想由 MasterPage 傳遞參數給 ContentPage ，也可以用這個方法。

# 動態改變  MasterPage

要改變 ContentPage 的主版頁，必須在 **Page\_PreInit** 事件之前，變更 Page.MasterPageFile 屬性。
```c#
protected void Page_PreInit(object sender, EventArgs e)
{
if (Session["MasterPageFile"]!=null)
MasterPageFile = (string)Session["MasterPageFile"];
}

protected void btnMaster1_Click(object sender, EventArgs e)
{
Session["MasterPageFile"] = @"~/C09_Customizing_and_Personalizing/Site1.Master";
Response.Redirect(Request.Path);
}

protected void btnMaster2_Click(object sender, EventArgs e)
{
Session["MasterPageFile"] = @"~/C09_Customizing_and_Personalizing/Site2.Master";
Response.Redirect(Request.Path);
}
```

# 巢狀 MasterPage

MasterPage 支援巢狀 MasterPage ，用法也沒什麼特別，但有幾點需注意：

若要找主版頁面上的控制項，就要看在那一層的主版頁面，然後用如下的語法   
```c#
Master.Master.FindControl("ControlID");
```

若要動態變更巢狀 MasterPage 的 MasterPage ，前面有提過，必須在 Page\_PreInit 事件變更，但是，因為 MasterPage 本身沒有這個事件，  所以，必須一律在 ContentPage 中，判斷 MasterPage 是否還有 MasterPage，然後用如下的語法變更。  
```c#
Master.Master.MasterPageFile = "../Master2.master";
```