---
title: Theme
layout: default
parent: Master、Theme、WebParts
nav_order: 1
description: "Theme"
date: 2012-10-08
tags: [Web,ASPNET,Master、Theme、WebParts]
postid: "4162895783398970146"
---
佈景主題 (Themes) 是用來設定控制項或頁面的樣式，讓整個網站看起來會有一致性的風格。  Theme 是樣式的集合，它主要由以下二部份組成：  

- 控制項面板 (.skin) ：用來定義控制項的樣式屬性。
- 樣式表 (.css) ：用來定義樣式。

# 建立佈景主題

建立佈景主題可以供個別網站內的所有網頁使用，也可以供整個 domain 中的所有網站的所有網頁共用。  如果要建立個別網站使用的佈景主題，則必項建立在網站內的 App\_Themes 目錄中。  如果要建立全域的佈景主題，則必項建立在 iisdefaultroot\Aspnet\_client\System\_web\version\ 目錄中。  

## 建立控制項面板（Skin File）

#### 建立控制項面板

控制項面板可分成二種：

- 預設面板：沒有SkinID，自動套用到所有同型的控制項。
- 具名面板：有SkinID，只會套用到有指名相同 SkinID 屬性的同型控制項。
```xml
<%--預設面板--%>
<asp:Button runat="server" BackColor="Red" ForeColor="White" Font-Name="Arial" Font-Size="9px" />
<asp:TextBox runat="server" Font-Name="Arial" Font-Size="9px" />

<%--具名面板--%>
<asp:Button runat="server" SkinID="SubmitButton" BackColor="Red" ForeColor="White" Font-Name="Arial" Font-Size="9px" />
<asp:TextBox runat="server" SkinID="InputField" Font-Name="Arial" Font-Size="9px" />
```

#### 在控制項面板加入Image

控制項面板也可以用來設定影像檔，它常用來設計圖片的切換功能。  
```xml
<asp:Image ID="Image1" runat="server" SkinId="CompanyLogo" ImageUrl="~/images/logoA.jpg" />
```
```xml
<asp:Image ID="Image2" SkinID="CompanyLogo" runat="server" />
```

## 在控制項面板中加入樣式表 CSS

#### 樣式表
.InputBox
{
border: 1px solid #DDDDDD;
font-size: 10pt;
color: #000000;
}
.SubmitButton
{
font-size: 10pt;
font-family:細明體;
}    
#### 嵌入樣式表
&lt;style type="text/css"&gt;
.SubmitButton
{
font-size: 10pt;
font-family:細明體;
}
&lt;/style&gt;    
#### 外部連接樣式表
```xml
<link href="SiteStyles.css" rel="themeName/stylesheet" type="text/css" />
```

#### 在控制項面板中套用樣式表

先前我們在控制項面板中直接設定樣式，我們也可以將樣式表中的定義，套用到控制項面板中。
```xml
<asp:Button runat="server" SkinID="SubmitButton2" CssClass="SubmitButton" />
<asp:TextBox runat="server" SkinID="InputField2" CssClass="InputBox" />
```

# 在頁面中使用佈景主題

[Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 和 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 這二個屬性都是用來指定佈景主題，你可以在網頁開頭指定，也可以在 web.config 組態檔中設定。  

#### 套用到整個網站
```xml
<pages Theme="MyTheme" />
或者
<pages StyleSheetTheme="MyTheme" />
```

#### 套用到單獨網頁
```xml
<%@ Page Language="C#" AutoEventWireup="true" Theme="Theme1" %>
或者
<%@ Page Language="C#" AutoEventWireup="true" StyleSheetTheme="Theme1" %>
```

# 佈景主題的套用原則

[StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 屬性是ASP.NET 2.0 在 Page 類別新增的項目，與 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 的功能完全一樣，都是用來載入樣式表。  如果你只有單獨指定 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 屬性時，預設狀況是 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 就會跟 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 一樣。  

## 佈景主題的套用順序

[StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 和 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 這二個屬性最主要的不同之處在於其套用到網頁的時間點。   [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 指定的佈景主題會在網頁生命週期的**早期**套用，而 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 指定的佈景主題會在網頁生命週期的**晚期**套用。  

因為晚期套用樣式會覆蓋早期套用的，所以決定整個樣式的優先順序為：  

1. 頁面上“@ Page”指示詞中的 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 屬性：**&lt;%@ Page Theme="SampleTheme" %&gt;**
2. Web.config 中的 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 屬性：**&lt;pages Theme="themeName"&gt;**
3. 控制項的屬性。
4. 頁面上“@ Page”指示詞中的 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 屬性：**&lt;%@ Page StyleSheetTheme="SampleTheme" %&gt;**
5. Web.config 中的 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 屬性：**&lt;pages StyleSheetTheme="themeName"&gt;**

底下這個例子，若有四個 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) (Theme1~Theme4)，分別將 Label 控制項定義成紅、橙、黃、綠四種顏色，且四個主題分別被設定在 Web.config 和 @ Page 指示詞中，如下所示
```xml
<pages theme="Theme1" StyleSheetTheme="Theme2" />
```
```xml
<%@ Page Language="C#" Theme="Theme3" StyleSheetTheme="Theme4"%>
```
```xml
<asp:Label ID="Label1" runat="server" Text="Test Text" ForeColor="Black" Font-Bold="true" ></asp:Label>
```

這個 Label 控制項，其樣式的套用原則，優先順序為：Theme3 &gt; Theme1 &gt; Control &gt; Theme4 &gt; Theme2 。

也就是說，上面例子中， Label 控制項的字體顏色原本設定為黑色字，  若要變更它的顏色，只有使用 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 指定才會有效，使用 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.stylesheettheme.aspx) 指定就沒效了。  

在一個網頁中，若你想單獨設定某些控制項的樣式，不受佈景主題影響，其他沒有單獨設定樣式的元件就全套用到佈景主題，這時候就可以用 StyleSheetTheme 屬性指定佈景主題。

# 如何由程式變更佈景主題

與主版頁面相同的，若要由程式碼變更佈景主題，必須在 Page\_PreInit 事件中設定網頁的 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 屬性。
```xml
protected void Page_PreInit(object sender, EventArgs e)
{
//套用整個怖景題
if (Session["Theme"] != null)
Page.Theme = (string)Session["Theme"];

//指定特定控制項套用特定面板
TextBox1.SkinID = "InputField";
}
```

要注意的是， [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 是 Page 物件的屬性，要動態變更 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) ，必須在可以正確存取 Page 物件的地方。

# 如何停用佈景主題

如果要將某個控制項停用佈景主題，可以在控制項中宣告 [EnableTheming](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.enabletheming.aspx) 屬性
```xml
<asp:Button ID="Button3" runat="server" Text="Submit" EnableTheming="false" />
```

若要在某個網頁中停用佈景主題，可以頁面 @ Page 宣示詞中宣告 [EnableTheming](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.enabletheming.aspx) 屬性
```xml
<%@ Page EnableTheming="false" %> 
```

不過，這樣宣告，雖然網頁不會套用佈景主題，但還是會套用 css 樣式。若要完全不套用任何樣式，可以這麼做

第一種: 在 App\_Theme 目錄下新增一個取名為 None 的佈景主題，裡面不要放任何檔案，並在頁面加上 &lt;%@ Page Theme="None" %&gt; 的設定即可。

第二種: 在頁面中設定 &lt;%@ Page Theme="" StyleSheetTheme="" %&gt; 就可以同時關閉 [Theme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.theme.aspx) 與 [StyleSheetTheme](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.StyleSheetTheme.aspx) 了。
## 參考資料  

- [ASP.NET 有時必須同時停用 Theme 與 StyleSheetTheme](http://blog.miniasp.com/post/2008/05/10/Disable-Theme-and-StyleSheetTheme-for-specific-page-in-AspNet.aspx)
-