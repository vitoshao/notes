---
title: 網站巡覽控制項
layout: default
parent: 驗證與瀏灠控制項
nav_order: 2
description: "網站巡覽控制項"
date: 2012-10-02
tags: [Web,ASPNET,驗證與瀏灠控制項]
postid: "134796803996976022"
---
# Choosing a Method to Navigate Pages

## Client-Side Navigation

There are many ways to navigate from one page to another in ASP.NET.   It's helpful to first identify these ways and then examine each in detail.   The following are the methods for page-to-page navigation in an ASP.NET site:  

#### HyperLink Control:

One of the easiest ways to navigate to a different Web page is to provide a [HyperLink](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hyperlink.aspx) controlon the form and set the [NavigateUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hyperlink.navigateurl.aspx) property to the desired destination.  
```xml
<asp:HyperLink ID="HyperLink1" runat="server" NavigateUrl="WebForm1.aspx" Target="_blank">WebForm1</asp:HyperLink>
```

#### javascritp : document.location

Another means of forcing client-side navigation is through JavaScript.   You can chang the document object's location property to a new URL.   The document object represents the Web page in client-side JavaScript; setting its location propert  
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function GoToWebForm1() {
document.location = "WebForm1.aspx";
}
</script>
</head>
<body>
<input id="Button1" type="button" value="Goto WebForm1" onclick="return GoToWebForm1()" />
</body>
```

## Cross-Page Posting

Cross-page posting is frequently desired in a scenario where data is collected on one Web page and processed on another Web page that displays the results.   In such a scenario, a [Button](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.aspx) control typically has its [PostBackUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.postbackurl.aspx) property set to the Web page to which the processing should post back.   The page that receives the PostBack receives the posted data from the first page for processing.   This page is referred to as the processing page.  

當指定控制項的 [PostBackUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.postbackurl.aspx) 屬性，實際上 ASP.NET 是在主機端執行 Server.Transfer ，所以我們就可以使用 [PreviousPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.previouspage.aspx) 屬性來取得前一頁面上的資訊。  這種做法常被拿來當做收集資料時使用，就像 Wizard 的做法，例如：結帳作業流程，會一頁一頁的收集使用者輸入的資料。  

若要取得前頁控制項的資料，可以透過 [FindControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.findcontrol.aspx) 方法，先找到控制項，再取得值。  或者，你也可以在 Page 最上頭的宣告中，加入 [PreviousPageType](http://msdn.microsoft.com/zh-tw/library/ms228169.aspx) 宣告，這樣就可以直接使用**強型別**的叫用方法。  

第一頁
```xml
<asp:LinkButton ID="LinkButton1" runat="server" Text="Using PostBackUrl in LinkButton"
PostBackUrl="WebForm3.aspx" />
```
```c#
public string TextBox1_Data
{
get { return TextBox1.Text; }
}
```

第二頁

底下範例示範如何取得 [PreviousPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.previouspage.aspx) 中的資料。
```xml
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="WebForm3.aspx.cs" Inherits="WebForm3" %>
<%@ PreviousPageType VirtualPath="WebForm2.aspx" %>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
//Page PreviousPage = this.PreviousPage;
if (PreviousPage == null)
{
myDebug.ResponseBR("No PreviousPage");
}
else
{
//前一頁網址
myDebug.ResponseBR("PreviousPage:" + Page.Request.UrlReferrer.ToString());

//取得前一網頁中的控制項資料
myDebug.ResponseBR(((TextBox)PreviousPage.FindControl("TextBox1")).Text);

//使用 StrongType 直接取得前一網頁中的 public peoperty
myDebug.ResponseBR("PreviousPage.TextBox1_Data = " + PreviousPage.TextBox1_Data);
}
}
```

## Client-Side Browser Redirect

The [Page.Response](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.response.aspx) object contains the [Redirect](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.redirect.aspx) method allow to redirect users to another page based on the result of their request or PostBack to the server.   For example, a user might click Submit Order on your page. After processing the order successfully, you might then want to redirect the user to an order details page.  
```xml
<asp:Button ID="btnRedirect" runat="server" Text="Redirect" OnClick="btnRedirect_Click" />
```
```c#
protected void btnRedirect_Click(object sender, EventArgs e)
{
Response.Redirect("WebForm3.aspx");
}
```

The redirect is accomplished by sending a HTTP response code of **302** to the browser along with the URL of the page to which to redirect.   The address that is displayed in the browser is updated to reflect the new URL location.   Note that this comes at the cost of performing an extra round trip to the server.  

The [PreviousPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.previouspage.aspx) property does not get populated when using the [Redirect](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.redirect.aspx) method.   To access data from the original page, you need to resort to traditional methods of passing data,  such as placing the data into cookies, placing the data into session state variables, or passing the data in the query string.  

## 補充說明 HTTP CodeState 301 302

當使用 Redirect 時，其實是回應給 Client 端之後，由 Client 端再進行一次 Get Request 。所以會往返 Server 端二次。  針對第一個網頁的回應，會送出 302 狀態碼，第二個網頁才是正常的 200 狀態碼。如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg43uGSa0R4OYMOEv47kqb5-v3d0FkBJYG6jGOJXSvtxFa7Y_8iiVdI9uqggNjJOikMFzyPMCZmymiqPQ4PawXg1hTMe81rnKshbX2mHjF8oC66Gorttjk0plgiAGuxaqA6GTKws4as2Gk/s850/redirect-step.png)

####  302 狀態碼，表示這是一個暫時的網頁。
```c#
Response.Redirect("WebForm3.aspx");
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJXJrRKNK2pk9MUS5TrwH6cFgeVidxCR2pczJ2J_I9fYM1rnkJU-nFlkQCiHFLkny8aO-lSk7brBggA0bFzWTTZRwSE8rn_pJmTLpiitvxsHaalWocBiGFNokayd8DY0oyMIc_6pOX4CE/s665/redirect-302.png)

通常會用到轉址，就是要將第一個網頁轉到第二個網頁去，那麼 301,302 有什麼差別。  這狀態碼只是用來告知用戶端一個訊息，通常一般使用者使用不到這個資訊，但是對於像搜尋引擎就有差別。  當搜尋引擎收到 302 訊息時，它把它認為只是暫時失效網頁，所以還是算這個網頁為效點閱；  但若是 301 ，則把它認為是永久失效網頁，會以目的頁才算有效點閱。  

#### 在 .NET 4 之前，若要回應轉址且回應 301 狀態碼，必須這麼做：
```c#
Response.StatusCode = 301;
Response.AddHeader("Location", "WebForm3.aspx");
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikoUwRKH_vgdWCvQ_yxh9tJY7GEjqhWwioa9uOpHKQTrKkf5_Lf_CD7nMoYIETBtN75duT7t3yorgIsfZBXRe58lYwBSnaysfvjb_9Y9DagQOUzV8KUFhtlnYEFJEXkh9wlr0t56ZAISU/s669/redirect-301.png)

#### 從 .NET 4 開始，若要回應轉址且回應 301 狀態碼，可以直接呼叫 RedirectPermanent 方法：
```c#
Response.RedirectPermanent("WebForm3.aspx");
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpbGEfAiZHGi8rrNkhLt-Bua60z3RKVNqbgoN2dqvffkeca6urg46k0GdvT8SV10DAq1mS6popzWxZQjoiN-TrgZvDjxIEwjJyO2LgD-_malSMsm6HxrEMqzEAymLPzoQ8TZvZPr2A0qY/s655/RedirectPermanent-301.png)

## Server-Side Transfer

Sometimes you might need to transfer the server-side processing of an entire Web page over to another page.   These scenarios are rare and tend to add needless confusion to your code and application.   However, you might need to hide the name of the page processing the request from the user.   You can do so using the Page.Server.Transfer method.  

The [Transfer](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.transfer.aspx) method transfers the entire context of a Web page over to another page.   The page that receives the transfer generates the response back to the user's browser.   In doing so, the user's Internet address in his or her browser does not show the result of the transfer.   The user's address bar still reflects the name of the originally requested page.  

當使用 [Transfer](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.transfer.aspx) 時，它是在 Server 端就將整個資料傳送到目的頁處理，所以只會往返 Server 端一次。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgH2P8VMtsEEG0GXJjaCdkD5UC4nGG_rhs9lHy99KMs5Kc_KiVTv7n-2p1PypjBikgdlP1ZFhZYRGM9dflR9jjaygsHu5j9dyJkKiPQYaBXeuA58LplE9PQvI9_0iS-roJuZnJK4qTAp3I/s850/transfer-step.png)

#### Server.Transfer
```c#
public void Transfer(string path);
public void Transfer(IHttpHandler handler, bool preserveForm);
public void Transfer(string path, bool preserveForm);
```

The [Transfer](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.transfer.aspx) method has an overload that accepts a Boolean parameter called **preserveForm**.   You can set this parameter to indicate if you want to keep the form and query string data.   It is generally better to set this to false.   You can also access the [PreviousPage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.previouspage.aspx) property during a transfer (just as you would with cross-page posting).  

下面這個例子，當叫用 Transfer 方法時，且將參數 preserveForm 設成 False ，則以下程式碼中的第(4)和第(5)點將取不到資料。
```c#
protected void btnTransfer_Click(object sender, EventArgs e)
{
Server.Transfer("WebForm3.aspx", false);
}
protected void btnTransfer2_Click(object sender, EventArgs e)
{
//參數 preserveForm 設成 false ，表示清除 System.Web.HttpRequest.QueryString 和 System.Web.HttpRequest.Form 集合
Server.Transfer("WebForm3.aspx", false);
}
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
//Page PreviousPage = this.PreviousPage;
if (PreviousPage == null)
{
myDebug.ResponseBR("No PreviousPage");
}
else
{
//1) 取得前一頁網址
myDebug.ResponseBR("PreviousPage Url= " + Page.Request.UrlReferrer.ToString());

//2) 使用 Find Control 取得前一網頁中的控制項資料
string value1 = ((TextBox)PreviousPage.FindControl("TextBox1")).Text;
myDebug.ResponseBR("PreviousPage's TextBox1 = " + value1);

//3) 使用 StrongType 取得前一網頁中的控制項資料
myDebug.ResponseBR("PreviousPage.TextBox1_Data = " + PreviousPage.TextBox1_Data);

//4) 取得前一頁表單中 TextBox1 的值
string value2 = ((Request.Form["TextBox1"] == null) ? "" : Request.Form["TextBox1"].ToString());
myDebug.ResponseBR("Request.Form['TextBox1'] = " + value2);

//5) 取得網址中 QueryString 
string name = ((Request.QueryString["name"] == null) ? "" : Request.QueryString["name"].ToString());
myDebug.ResponseBR("Request.QueryString['name'] = " + name);
}
}
```

## 如何以新視窗開啟連結

目前大部份瀏灠器在連結新網頁時，遇到 Target="\_blank" 屬性，大都會以新頁籤方式開啟。  若要以新視窗開啟，大都只能使用無 toolbar 的視窗才行，方法變更如下。  
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function GoToWebForm2() {
window.open("WebForm1.aspx","NewWindow","toolbar=no");
}
</script>
</head>
<body>
<!--使用 Link-->
<asp:HyperLink ID="HyperLink1" runat="server" NavigateUrl="WebForm1.aspx" onclick="window.open(this.href,'NewWindow','toolbar=no');return false" >WebForm1</asp:HyperLink>
<!--使用 Button-->
<input id="Button1" type="button" value="Goto WebForm2" onclick="return GoToWebForm2()" />
</body>
```

# Using the Site Map Web Server Control

[Site Map Web Server Control](http://msdn.microsoft.com/zh-tw/library/c4hb2k3a.aspx) 指的是工具列中的巡覽類別的控制項，包括 Menu 、SiteMapPath、TreeView，它們都可以拿來製作網站地圖。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJ9ZM8UAAxTKtwQWK-SDztshx9evzDFEv7O1XbzscFBPBtGOQjM38Ogy0Mln9Dl9U76rnyK-TsB3cvvip4GeOFFl8moltguXiKB0FrMhgD6F0mbvwbALLsOeecb2UR11W4_poUYp9IbvU/s303/SiteMapControls.png)

要製作網站地圖，必須要有一個**網站地圖資料檔**，它是一個副檔名為 .sitemap 的 XML 文件檔。  它包含整個網站的階層架構，你可以任意的維護這個檔案，以調整階層架構，或者隱藏某些不想顯示的節點。  

底下是一個網站地圖資料檔的範例。  地圖資料檔中的每一個節點都使用 &lt;siteMapNode&gt; 項目表示。  
```xml
<?xml version="1.0" encoding="utf-8" ?>
<siteMap>
<siteMapNode url="~/Index.aspx" title="首頁"  description="首頁">

<siteMapNode title="Creating Custom Web Controls" url=""  description="">
<siteMapNode title="Working with User Controls"  description="Working with ..." url="~/C10_Creating_Custom_Web_Controls/WebForm1.aspx"/>
<siteMapNode title="Working with Custom Web Server Controls"  description="Working with ..." url="~/C10_Creating_Custom_Web_Controls/WebForm2.aspx"/>
</siteMapNode>

<siteMapNode title="Web Server Controls" url="">
<siteMapNode title="Web Server Controls" url="~/C02_Web_Server_Controls/WebForm1.aspx"></siteMapNode>
<siteMapNode title="Common Web Server Controls" url="~/C02_Web_Server_Controls/WebForm2.aspx"></siteMapNode>
<siteMapNode title="Specialized Web Server Controls" url="~/C02_Web_Server_Controls/WebForm3.aspx"></siteMapNode>
</siteMapNode>

<siteMapNode title="Input Validation and SiteNavigation" url="">
<siteMapNode title="Input Validation" url="~/C03_Input_Validation_and_Site_Navigation/WebForm1.aspx"></siteMapNode>
<siteMapNode title="Site Navigation " url="~/C03_Input_Validation_and_Site_Navigation/WebForm2.aspx"></siteMapNode>
<siteMapNode title="SiteMapPath " url="~/C03_Input_Validation_and_Site_Navigation/SiteMapPath1.aspx"></siteMapNode>
</siteMapNode>

</siteMapNode>
</siteMap>
```

## Using the SiteMap Class

The [SiteMap](http://msdn.microsoft.com/zh-tw/library/system.web.sitemap.aspx) class provides programmatic access to the site navigation hierarchy from within  your code-behind page. Its two primary properties are [RootNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.rootnode.aspx) and CurrentNode, and both  return [SiteMapNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.aspx) instances.  

#### SiteMap 類別的屬性

- [RootNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemap.rootnode.aspx) ：
- [CurrentNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemap.currentnode.aspx) ：

#### SiteMapNode 類別的屬性

- [RootNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.rootnode.aspx) ：
- [ParentNode](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.parentnode.aspx) ：
- [PreviousSibling](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.previoussibling.aspx) ：
- [NextSibling](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.nextsibling.aspx) ：
- [HasChildNodes](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.haschildnodes.aspx) ：
- [Title](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.title.aspx) ：
- [Url](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.url.aspx) ：
- [Description](http://msdn.microsoft.com/zh-tw/library/system.web.sitemapnode.description.aspx) ：
```c#
SiteMapNode currentNode = SiteMap.CurrentNode;
SiteMapNode rootNode = SiteMap.RootNode;

myDebug.ResponseBR("RootNode Url = {0}", rootNode.Url);
myDebug.ResponseBR("CurrentNode Url = {0}", currentNode.Url);
myDebug.ResponseBR("CurrentNode Title = {0}", currentNode.Title);
myDebug.ResponseBR("CurrentNode Description = {0}", currentNode.Description);

//RootNode Url = /Index.aspx
//CurrentNode Url = /C03_Input_Validation_and_Site_Navigation/SiteMapPath1.aspx
//CurrentNode Title = SiteMapPath 
//CurrentNode Description = 
```

## Displaying Site Map Information to Users

The site map information is just an XML file.   To display this information, you need to put a navigational control on a Web page.   These controls can take a site map file or a [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control as their data source and display information accordingly.  

A [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control is simply a control designed to provide you with programmatic access to a site map file.   This control can also be used by the **navigation controls** to provide a source for their data.   You can use a [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control by dragging it onto your page.   It will automatically connect with the site map file you have defined for your site.  

#### SiteMapDataSource 屬性

- [StartingNodeOffset](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.startingnodeoffset.aspx) ：Gets or sets a positive or negative integer offset from the starting node 。
- [ShowStartingNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.showstartingnode.aspx) ：是否顯示開始節點

The [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) does not have a visual representation to a user.   Instead, it manages access to your site map data.   To show the data to a user you must add a navigational control and connect it to the [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control.   There are three main navigational controls available in ASP.NET ：[Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx)、[TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx)、[SiteMapPath](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemappath.aspx) .  

The [SiteMapPath](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemappath.aspx) Control automatically picks up the site map file; you do not need to configure a data source.   
```xml
<form id="form1" runat="server">
<asp:SiteMapDataSource ID="SiteMapDataSource1" runat="server" />
<div>
<h1>SiteMapPath</h1>
<asp:SiteMapPath ID="SiteMapPath1" runat="server">
</asp:SiteMapPath>

<h1>TreeView</h1>
<asp:TreeView ID="TreeView1" runat="server" 
DataSourceID="SiteMapDataSource1">
</asp:TreeView>

<h1>Menu</h1>
<asp:Menu ID="Menu1" runat="server" 
DataSourceID="SiteMapDataSource1" 
Orientation="Vertical" 
MaximumDynamicDisplayLevels="5" >
</asp:Menu>
</div>
</form>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga3DRrNl0j6AU3qcVVOLl4sXeEW1-8UVehKdv5s-icvxoFq9NVQ17zEKC17r4Kyx8zeciIpwOzFh0PD04gqUN6RnH7qUin5ziZPHB1KSCtg6i3oR3m0iwSZA_UI8xp4Y_NZM406ldRxkU/s433/navigation-controls.png)
## 參考資料  

- [SiteMapPath 控制項, SiteMapProvider 簡介](http://www.allenkuo.com/genericarticle/view851.aspx)