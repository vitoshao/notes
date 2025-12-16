---
title: WebPart
layout: default
parent: Master、Theme、WebParts
nav_order: 3
description: "WebPart"
date: 2012-10-08
tags: [Web,ASPNET,Master、Theme、WebParts]
postid: "5237987728182993300"
---
# 什麼是網頁組件(WebPart) 

[ASP.NET Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) 是一種可以用來顯示、隱藏、移動的元件。  它可以讓使用者自行於瀏覽器中修改 Web 網頁的內容、外觀和行為。  這些修改可以套用在個別使用者，或者所有使用者。  使用者也可以將修改後的內容儲存下來，以保留到未來的瀏覽器工作階段使用，以達到個人化網頁的功能。  

[Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) 通常使用於：

- 最新消息清單
- 最新活動月曆
- 搜尋區塊
- 透過RSS取得的文章清單
- 由網路取得的氣象資訊

不管是標準控制項或自訂控制項，任何的控制項都可以當作是 [WebPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpart.aspx) 。  只要在 Visual Studio 設計工具中，就不需要撰寫任何程式碼就可以使用 [WebPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpart.aspx) 。

# The WebParts Namespace

ASP.NET includes 13 [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) controls in the designer Toolbox.  These controls and classes can be found inside the [System.Web.UI.WebControls.WebParts](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.aspx) namespace.  The following list provides a highlevel overview of the most important WebParts classes.  

#### Manager

- 1. [WebPartManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.aspx) ：  
This control is required on every page that includes Web Parts.  It manages all the Web Part controls and their events on the specified page.
- 2. [ProxyWebPartManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.proxywebpartmanager.aspx) ：

#### Zone

- 3. [WebPartZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartzone.aspx) ：  
This control is used to define an area on your page in which [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) can be hosted.
- 4. [EditorZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.editorzone.aspx) ：  
This control provides an area on the page where [EditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.editorpart.aspx) controls can exist.
- 5. [CatalogZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogzone.aspx) ：  
This control defines an area in which a [CatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogpart.aspx) control can exist on the page.
- 6. [ConnectionsZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.connectionszone.aspx) ：

#### Part

- [WebPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpart.aspx) ：  
This a a base class for custom [ASP.NET Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) controls.
- [CatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogpart.aspx) ：  
This control providers the user UI for managing a group of [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx)that can be added to a Web Part page.
- 7. [PageCatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.pagecatalogpart.aspx) ：  
This control is similar to the [CatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogpart.aspx) control.       However, it only groups those [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) that are part of a specific page.
- 8. [DeclarativeCatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.declarativecatalogpart.aspx) ：  
This control allows you to declare [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) that should be available to add to a page or to the entire site.
- 9. [ImportCatalogPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.importcatalogpart.aspx) ：  
This control allows enables users to import a description file that describes settings on a [WebPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpart.aspx) control or server control that a user wants to add to a WebPartZoneBase zone.
- [EditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.editorpart.aspx) ：  
This control allows users to define customizations,   such as the modification of property settings, for the specified Web Part.
- 10. [AppearanceEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.appearanceeditorpart.aspx) ：
- 11. [BehaviorEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.behavioreditorpart.aspx) ：
- 12. [LayoutEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.layouteditorpart.aspx) ：
- 13. [PropertyGridEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.propertygrideditorpart.aspx) ：

WebPart Controls  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNU17hHqqmAC64_suxCxaSwU-6gOjLuD1Gldw4s8SOCFIGWQbuq0yAlsEGWDkOhgHtQvmH10VnbIPfyvbkm4d3zo4lOLK2vrKhXWRqFdAHPEdEYUf-WVwjb474Rxd8wttE7Xi_kDCFqxE/s850/webpart-controls.png)

# Defining Web Part Zones

**Zones** also allow you to set common styles for the [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) that are placed within the zone.   This is called the Web Part's chrome.   The chrome includes the Web Part's header style, the menu styles, outline borders, content styles, edit mode style, and more.  

To define this zone, you add a [WebPartZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartzone.aspx) control to your page.   To place [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) inside this zone, you have to add the [ZoneTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartzone.zonetemplate.aspx) control inside the WebPartZone.   The [ZoneTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartzone.zonetemplate.aspx) control lets you add other ASP.NET controls to the zone and combine them into actual Web Parts.  
```xml
<asp:WebPartManager ID="WebPartManager1" runat="server">
</asp:WebPartManager>

<asp:WebPartZone ID="WebPartZone1" runat="server" style="width: 200px; height: auto" BackColor="LightSkyBlue">
<ZoneTemplate>
<!--Add content to the zone-->
</ZoneTemplate>
</asp:WebPartZone>

<asp:WebPartZone ID="WebPartZone2" runat="server" style="width: 200px; height: auto" BackColor="LightGoldenrodYellow">
<ZoneTemplate>
<!--Add content to the zone-->
</ZoneTemplate>
</asp:WebPartZone>
```

# Creating Web Parts

用來建立網頁組件常見的方法有三種：

- 使用現有的控制項 standard control
- 使用使用者控制項 user control
- 使用自訂控制項 custom control

## Building Web Parts with User Controls

Web Parts are defined inside the zones on your page.   They can be added to the zones at design time or run time, or they can be configured to be there by the user.  
```xml
<table>
<tr>
<td valign="top">
<asp:WebPartZone ID="WebPartZone1" runat="server" style="width: 200px; height: auto"  >
<ZoneTemplate >
<uc2:urNow ID="urNow1" runat="server" title="現在時刻" />
</ZoneTemplate>
</asp:WebPartZone>
</td>
<td valign="top">
<asp:WebPartZone ID="WebPartZone2" runat="server" style="width: 200px; height: auto">
<ZoneTemplate>
<uc1:ucWeather ID="ucWeather1" runat="server" title="目前天氣" />
</ZoneTemplate>
</asp:WebPartZone>
</td>
</tr>
</table>
```

Notice the addition of the **title** attribute to the user control definition.   This tells the Web Part what title to display according to the Web Part's chrome style setting.

## Creating a Web Part Control from an Existing ASP.NET Control

You can also create a Web Part simply by inserting a standard ASP.NET control inside a &lt;ZoneTemplate&gt; element.  
```xml
<asp:WebPartZone ID="WebPartZone3" runat="server" style="width: 200px; height: auto">
<ZoneTemplate>
<asp:Panel ID="Literal1" runat="server" title="The Weather">
<table>
<tr>
<td><img src="http://www.cwb.gov.tw/V7/symbol/weather/gif/day/02.gif" alt="" width="45" height="45" /></td>
<td>
目前天氣： 有霾<br />
溫度： 25°C<br />
風向： 西北、風速：11 公里/小時<br />
濕度： 57%
</td>
</tr>
</table>
</asp:Panel>
</ZoneTemplate>
</asp:WebPartZone>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_Ct6_8E92zJsnjkCwDqPRezMn-VJCVTrw3lIhs9HciVEoEkhYa5gtIb5ohBKUWJfIp9UpJQiy2GIuNuzX9c0Frxwc_Tebv26u814EwhaMmT3x59rOylYkNvAy-BgVbkE9XZFMOhrU9fw/s428/webpart-picture1.png)

# Enabling Users to Arrange and Edit Web Parts

The [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) on your page can be displayed to the user in several different ways.  The mode of display is dependent on what the user is doing.  You can change the display mode of the [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) on a page by setting the [DisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.displaymode.aspx) property of [WebPartManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.aspx) control.   

## Web Part Display Modes

1. [BrowseDisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.browsedisplaymode.aspx)：
- This is the standard way in which users browse webpages.
- This is the default mode.
2. [DesignDisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.designdisplaymode.aspx)：
- This mode enables users to drag [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) to different locations.
-
3. [EditDisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.editdisplaymode.aspx)：
- This mode like design mode and enables users to drag Web Parts.
- Additionally, users can select Edit from the [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) menu to edit the title, size, direction, window appearance, and zone of [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) by using [AppearanceEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.appearanceeditorpart.aspx) and [LayoutEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.layouteditorpart.aspx) controls.
- To use this mode, you must add an [EditorZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.editorzone.aspx) control to your webpage, and then add either [AppearanceEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.appearanceeditorpart.aspx) or [LayoutEditorPart](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.layouteditorpart.aspx) , or both.
4. [CatalogDisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.catalogdisplaymode.aspx)：
- This mode enables users to add additional [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) that you specify by using a [CatalogZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogzone.aspx) control.
- This mode is available only after you add a [CatalogZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogzone.aspx) to your webpage.
5. [ConnectDisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.connectdisplaymode.aspx)：
- This enables users to manually establish connections between controls by interacting with a [ConnectionsZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.connectionszone.aspx) control.

## How to enable different display mode

1. set up the page with a [CatalogZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.catalogzone.aspx) control.
2. change the [DisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.displaymode.aspx) property when the user clicks the Edit button.
```xml
<asp:Button ID="btnEdit" runat="server" Text="Edit" onclick="btnEdit_Click" />
<asp:CatalogZone ID="CatalogZone1" runat="server" HeaderText="Manage Web Parts">
<ZoneTemplate>
<asp:PageCatalogPart ID="PageCatalogPart1" runat="server">
</asp:PageCatalogPart>
</ZoneTemplate>
</asp:CatalogZone>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
ViewState["mode"] = "browse";
}
}

protected void btnEdit_Click(object sender, EventArgs e)
{
string mode = (string)ViewState["mode"];
//switch modes
if (mode == "browse")
{
ViewState["mode"] = "edit";
btnEdit.Text = "Done";
WebPartManager1.DisplayMode = WebPartManager1.SupportedDisplayModes["Catalog"];
}
else
{
ViewState["mode"] = "browse";
btnEdit.Text = "Edit";
WebPartManager1.DisplayMode = WebPartManager1.SupportedDisplayModes["Browse"];
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKuBhXYnS68Avs2T9m9gtRR0npr9v_EQ2ow6tKsx3J5281iw6qy1rF_uHbj-dg6uDxa-CQQ9LSqzllg-TeAtxH9h0Olc8VsiDR9io43ssD_Egm_w5w1nezTVUPaXZ6t0XalqbEnpK-tiM/s343/webpart-editmode.png)

# Connecting Web Parts

- A [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) connection is a link or association between two server controls that enables them to share data
- A connection always involves exactly two controls: one is the provider of data, and the other is the consumer of the data from the provider.
- A provider control can establish connections with multiple consumers at the same time.
- A consumer control can connect to only one provider at a time.
- Any two server controls, in order to form a connection, must reside within a WebPartZoneBase type of zone.

## Creating a Static Connection

### What is Static Connection

- establish the connection during the development process
- cannot be changed by the user

Static connections typically involve a provider and one or more consumer Web Parts.  The provider provides the connection data.  The consumer receives the provider data.

### How to create Static Connection

1. Add two server control in &lt;ZoneTemplate&gt;
```xml
<asp:WebPartZone ID="WebPartZoneProvider" runat="server">
<ZoneTemplate>
<uc1:Provider ID="Provider1" runat="server" />
<uc2:Consumer ID="Consumer1" runat="server" />
</ZoneTemplate>
</asp:WebPartZone>
```

2. Add a &lt;StaticConnections&gt; element as a child of the &lt;asp:WebPartManager&gt; element to contain one or more declared static connections.

3. Within the &lt;StaticConnections&gt; element, declare an &lt;asp:WebPartConnection&gt; element
```xml
<asp:WebPartManager ID="WebPartManager1" runat="server">
<StaticConnections>
<asp:WebPartConnection
ID="conn1"
ProviderID="Provider1" ProviderConnectionPointID="GetTextBoxValue"
ConsumerID="Consumer1" ConsumerConnectionPointID="ShowTextBoxValue">
</asp:WebPartConnection>
</StaticConnections>
</asp:WebPartManager>
```

4. Provider and Consumer 
```c#
public partial class Provider : System.Web.UI.UserControl
{
string _textBoxValue = "";
[ConnectionProvider("TextBox provider", "GetTextBoxValue")]
public string GetTextBoxValue()
{
return _textBoxValue;
}
protected void Button1_Click(object sender, EventArgs e)
{
_textBoxValue = TextBoxProvider.Text;
}
}
```
```c#
public partial class Consumer : System.Web.UI.UserControl
{
[ConnectionConsumer("TextBox consumer", "ShowTextBoxValue")]
public void ShowTextBoxValue(string textBoxValue)
{
LabelConsumer.Text = textBoxValue;
}
}
```

- ConsumerID ：Indicates the ID of the consumer control in the connection.
- ConsumerConnectionPointID ：Indicates the ID of a special callback method in the consumer used to establish the connection.
- ProviderID ：Indicates the ID of the provider control in the connection.
- ProviderConnectionPointID ：Indicates the ID of a special callback method in the provider used to establish the connection.

## Creating a Dynamic Connection

### Enabling Dynamic Connections

### Establishing Dynamic Connections Among Web Parts

# Personalizing Web Parts

Web Parts support **personalization**.   Personalization allows changes to the layout to be stored for each user so that the user sees the same layout the next time he or she goes to the page.  Web Parts personalization relies on **client-side cookies**.  It uses these cookies to look up settings in the ASPNETDB SQL Server database.  Typically, when you store personalized settings, you will also need to authenticate users by using either Windows or Forms authentication.  

## Enabling Personalization for Custom Controls

Page developers can enable personalization by applying the [Personalizable](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.personalizableattribute.aspx) attribute to a public property of a [Web Parts](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx) control.   The control can be a custom control inherited from WebPart, a user control, or any other control you can create a property on, as long as it resides in a [WebPartZone](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartzone.aspx) zone and there is a [WebPartManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.webpartmanager.aspx) control somewhere on the page.  

你只要在 WebPart 元件中，設計一個 public property，並套用 [Personalizable](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webparts.personalizableattribute.aspx) ，  就可以啟用個人化資料的記錄功能。  

例如下面這個例子，當發生 Button\_Click 事件時，頁面上的資料就會自動被儲存在 ASPNETDB 的 aspnet\_PersonalizationPerUser 資料表中。  當網頁載入時，你也可以直接由這個屬性讀取資料。  
```c#
public partial class Provider : System.Web.UI.UserControl
{
[Personalizable]
public string MyData
{
get;
set;
}
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
if (MyData != null)
{
txtMyData.Text = MyData;
}
}
}
protected void Button1_Click(object sender, EventArgs e)
{
MyData = txtMyData.Text;
}
}
```

## 啟用共用的設定 Enabling Shared Personalization

Web Parts 的個人化功能預設是自動啟動的，也就是你不需要做任何設定，當任何使用者在網頁上調整任何 WebPart 組件，這些變動都會自動被記錄下來。  但是這些資料變動的能見度都僅限個別使用者，也就是不會引響到其他人的個人化設定。  如果你希望網站管理員或特定人員可以執行套用到所有使用者的個人化設定，就必須在 Web.config 中啟動共用「**共用個人化（shared personalization）**」的設定。  

要啟用這個功能，必須在 &lt;authorization&gt; 區段中加入 **enterSharedScope** 命令，並指定授權的使用者。  
```xml
<authorization>
<allow verbs="enterSharedScope" users="vito" roles="admin" />
</authorization>
```

被授權的使用者就可以編輯所有人都可見的個人化設定。

## Disabling Personalization for a Page

You can also disable personalization for a page, which is useful if you want to take advantage of personalization on some pages but not on others.   To disable personalization on a page, set the WebPartManager.Personalization.Enabled attribute to false.  
```xml
<asp:webPartManager ID="webPartManager1" runat="server">
<Personalization Enabled="False" />
</asp:webPartManager>
```
## 參考資料  

- [ASP.NET Web Parts Overview](http://msdn.microsoft.com/zh-tw/library/hhy9ewf1.aspx)
- 
-