---
title: Ajax Web Forms
layout: default
parent: JavaScript and AJAX
nav_order: 1
description: "Ajax Web Forms"
date: 2012-10-26
tags: [Web,ASPNET,JavaScript and AJAX]
postid: "6125044401809342953"
---
# 什麼是 AJAX

[AJAX](http://zh.wikipedia.org/wiki/AJAX) 為「Asynchronous JavaScript and XML」（非同步的JavaScript與XML技術），是一種廣泛應用在瀏覽器的網頁開發技術。  Ajax 是多項技術的綜合應用，其概念是由 Jesse James Garrett 所提出。  利用這項技術將可增進網頁的回應能力以提供使用者與主機端的互動功能，以達到網頁**非同步處理**。  

AJAX 網頁是屬於用戶端(展示層)的技術，在用戶端的瀏覽器必須使用 JavaScript 的 **XMLHttpRequest** 物件，送出非同步的 Http Request，  此時只會將指定的欄位資料傳送至伺服器端，而網頁的其他資料並不會進行回傳(PostBack)，接著再透過 Http Response 方式將更新後的執行結果下載至用戶端的瀏覽器。  最後透過 JavaScript 將回應的執行結果以背景執行的方式寫回 DHTML 或 DOM(DocumentObject Model) 指定的標籤區塊，此時指定的區塊即會進行更新。由於傳送和接收資料是屬於非同步的模式，因此使用者可以在不換頁的情形下繼續在網頁上進行任何操作。  

#### 網頁同步處理

舉列來說，當網頁中有多個區塊，若只有最新消息的區塊需要進行更新，而網頁的其他區塊如選單、廣告、版權頁…等不需要進行更新，  在傳統的動態伺服器網頁就必須將整份網頁全部回傳(PostBack)至伺服器端，再重新產生(Render)，此種方式會增加網路傳送資料量，且使用者操作較不方便。  

#### 網頁非同步處理

如果上述情形採用非同步方式來處理的話，就能在不換頁的情形下，只更新最新消息的區塊。  且網頁只將最新消息區塊回傳至伺服器端，再將更新後的最新消息載入至用戶端，  網頁其他的部份如選單、廣告、版權頁…等區塊並不會回傳至伺服器端也不會刷新頁面。  

由於 AJAX 是由 DHTML、DOM、XML、JavaScript 及動態伺服器技術(ASP.NET, ASP, PHP等)等多種技術整合而成的應用，  因此必須撰寫大量 JavaScript 用戶端指令碼來進行網頁的背景處理，此種作法相當麻煩，  微軟為了讓程式開發人員更容易開發 AJAX 網頁，於 2007 年推出 ASP.NET AJAX ，讓開發人員在不需要撰寫 JavaScript 用戶端指令碼的情形下，也可以快速開發 AJAX 網頁。    

下列項目都是與 AJAX 相關的元素：

- **Microsoft AJAX Library** ：  
The [Microsoft AJAX Library](http://msdn.microsoft.com/zh-tw/library/bb397536.aspx) is a set of JavaScript files that make programming client-side JavaScript easier.   It provides an object-oriented model to the AJAX scripting language.   This includes support for classes, namespaces, event handling, data types, and more.   The library also has support for error handling, debugging, and globalization.
- **Asp.net AJAX server controls** ：  
AJAX server controls enable partial-page updates, communicate with a server process to indicate progress, and periodically update portions of a page.
- **AJAX control toolkit** ：  
The AJAX Control Toolkit is a set of community-created and supported controls that show off the power of AJAX.   These controls can be used in your Web pages to enable many client-side features typically reserved for applications running on the desktop,   such as masked edit boxes, slider controls, filtered text boxes, modal pop-ups, and much more.
- **Client-side web service support** ：  
ASP.NET provides support for calling Web services asynchronously from the client using JavaScript Object Notation (**JSON**) serialization and       **XML**.

#### The AJAX Extensions controls

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjf1jfbokyet3j2Z51YhMDExqeYxxoGoFEpjc2pGgZ2MR_6wZaQjr201NyuDcGDL4lV9EixnDmjF_w0yz4OR-LKlD_M0juPUAAyh3Ig2PlwBpAJIZ9iwQtaRDrly0dXvXG5F9Ho6chnGCk/s601/AjaxServerControls.png)

# The ScriptManager and ScriptManagerProxy Controls

Each page you write that leverages ASP.NET AJAX requires one (and only one) instance of a [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control.   The [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control is responsible for pushing the **Microsoft AJAX Library** down to the client when your page is requested.   It also manages partial-page updates, progress indicators, and more.  
```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
```

####  [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) 常用屬性：

- [AsyncPostBackTimeout](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.asyncpostbacktimeout.aspx) ：設定非同步的逾期時間。
- [EnablePartialRendering](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.enablepartialrendering.aspx) ：設定是否啟用 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 控制項進行非同步回傳。(預設值為 true)
- [EnablePageMethods](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.enablepagemethods.aspx) ：設是是否允許透過 Script 叫用網頁中的靜態方法。(預設值為 false)
- [AsyncPostBackTimeout](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.asyncpostbacktimeout.aspx) ：非同步回傳的逾時時間
- [AjaxFrameworkMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.ajaxframeworkmode.aspx) ：設定下載指令碼檔的模式。  
當使用 [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx)控制項時，預設會啟用下載相關的 Microsoft AJAX 指令碼檔案，你可以停用這個行為，或者明確的指定要下載哪些指令碼檔案。
- Enabled ：自動包含 MicrosoftAjax.js 指令碼檔
- Disabled ：停用所有的 Microsoft AJAX 指令碼功能
- Explicit ：明確指定要參考的指令碼檔

By default, the [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control's [EnablePartialRendering](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.enablepartialrendering.aspx) property is set to True.   This indicates that the page supports partial-page updates.   You can use this property to turn this feature off if needed.  

The [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) is also used to register custom scripts with the Microsoft AJAX Library.  In this way, if you write a script, it can be registered and managed with the Microsoft AJAX Library.  

#### Using AJAX with Master Pages and User Controls

A page can contain only a single [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control.  Using [ScriptManagerProxy](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanagerproxy.aspx) control to present this problem.  The [ScriptManagerProxy](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanagerproxy.aspx) control can be used either by child pages that use a master page  that already defines a [ScriptManager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.aspx) control or by user and custom controls.

# The UpdatePanel Control

The [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) control allows you define areas of a page that should PostBack to the server independent of the rest of the page.

The [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) control is a container for other controls.   The controls you put inside the [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) control that cause a PostBack to the server will be managed as partial-page updates.  

#### Controlling Partial-Page Updates

You can combine multiple [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) controls on the same page. Each can update portions of the page independently .   You might also have controls that cause standard PostBacks on the same page as those that cause asynchronous PostBacks.   In each of these cases, you need to be able to control how and when the page elements update.   The [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) exposes the [UpdateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.updatemode.aspx) and [ChildrenAsTriggers](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.childrenastriggers.aspx) properties for controlling when a PostBack should occur to trigger an update of content contained in an UpdatePanel.  

- [UpdateMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.updatemode.aspx) ：  

- [Always](http://msdn.microsoft.com/zh-tw/library/bb155262.aspx)：預設值  
若 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 的屬性設為 Always ，網頁內任何控制項只要發生 PostBack ，都可以引發該 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 更新。                       
這裡提到的 PostBack 包含自已 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx)           內的控制項，或其他 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項，或不在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項所引發的 Postback。
- [Conditional](http://msdn.microsoft.com/zh-tw/library/bb155262.aspx)：  
若 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 的屬性設為 Conditional ，就只有 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項所引發 PostBack ，才可以更新該 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 。            
若巢狀架構中的 Child [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 設為 Conditional ，它仍會被 Parent [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 的 Postback 更新。            
在下列情況下，會更新 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 控制項的內容：  

- 明確呼叫 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 控制項的 Update 方法。
- 更新父面板時，子面板會更新。
- Postback 是由定義的 Trigger Control 所回傳的。
- [ChildrenAsTriggers](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.childrenastriggers.aspx) ：預設值為 true。  
這個屬性用來指定子控制項所引發的 Postback ，是否觸發 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 更新。  當值設成 True 時，在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項所引發的 Postback ，都可以更新這個 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 。  若設成 False ，則無法更新這個 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 。  
1)所以將 UpdateMode=Always 且 ChildrenAsTriggers=False 是沒有意義的，也不允許這麼設定。  
2)若設成 UpdateMode=Always 且 ChildrenAsTriggers=True ，意義同只有 UpdateMode=Always。  
3)若設成 UpdateMode=Conditional 且 ChildrenAsTriggers=True ，意義同只有 UpdateMode=Conditional。  
4)若設成 UpdateMode=Conditional 且 ChildrenAsTriggers=False 則 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 不會更新。  
此時必須在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 的 &lt;Triggers&gt; 區段中，加入 [AsyncPostBackTrigger](http://msdn.microsoft.com/zh-tw/library/system.web.ui.asyncpostbacktrigger.aspx) 控制項，以明確指定可以觸發更新的控制項。  

| UpdateMode | ChildrenAsTriggers | 說明 |
| --- | --- | --- |
| Always | True | 任何位置的控制項所引發的 postback 都可以更新。 |
| Always | False | 不允許。 |
| Conditional | True | 在同一個 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項所引發的 postback 才可以更新。 |
| Conditional | False | 都不會更新。 |

## 如何指定特定控制項引發的 Postback 才可以更新？

當 UpdateMode=Conditional 且 ChildrenAsTriggers=False 時，則此時 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內的控制項都無法更新。  這時可以在 &lt;Triggers&gt; 區段中，利用 [AsyncPostBackTrigger](http://msdn.microsoft.com/zh-tw/library/system.web.ui.asyncpostbacktrigger.aspx) 控制項的 **ControlID** 屬性指定可以觸發非同步更新的控制項。  
```xml
<asp:UpdatePanel ID="UpdatePanel3" runat="server" UpdateMode="Conditional" ChildrenAsTriggers="False">
<ContentTemplate>
<asp:Panel ID="Panel3" runat="server" GroupingText="UpdatePanel3 (Mode=Conditional , ChildTrigger=False)">
Now : <asp:Label ID="labTime3" runat="server" Text="Time 3"></asp:Label><br />
<asp:Button ID="btnSubmit3" runat="server" Text="Submit" onclick="btnSubmit3_Click" />
<asp:Button ID="btnAsyncPostBackTrigger" runat="server" Text="AsyncPostBackTrigger" onclick="btnAsyncPostBackTrigger_Click" />
</asp:Panel>
</ContentTemplate>
<Triggers>
<asp:AsyncPostBackTrigger ControlID="btnAsyncPostBackTrigger" />
</Triggers>
</asp:UpdatePanel>
```

上面這個例子利用 [AsyncPostBackTrigger](http://msdn.microsoft.com/zh-tw/library/system.web.ui.asyncpostbacktrigger.aspx) 控制項的 **ControlID** 屬性指定 btnAsyncPostBackTrigger 這個控制項，可以觸發非同步更新。  

另外，也可以再透過 **EventName** 屬性，指定特定觸發非同步更新的事件，例如 Button 控制項有 Click、Command、Binding 等事件都可以指定。  若沒有指定，以控制項的預設事件為主，例如 Button 控制項的預設事件就是 Click 。

## 如何更新在 UpdatePanel 外面的控制項？

若要更新 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 外面的控制項，而且該控制項不在任何 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 裡面，  這種狀況必須在 Server 端使用 [ScriptManager.RegisterDataItem](http://msdn.microsoft.com/zh-tw/library/system.web.ui.scriptmanager.registerdataitem.aspx) 方法，將資料傳送給特定的控制項。  並且在 Client 端的網頁中，利用 [Sys.WebForms.PageRequestManager](http://msdn.microsoft.com/zh-tw/library/bb311028.aspx) 類別的 [pageLoading](http://msdn.microsoft.com/zh-tw/library/bb397523.aspx) 事件，回應由 Server 端所回傳的資料。  
```c#
protected void btnUpdateOutside_Click(object sender, EventArgs e)
{
//若要更新 UpdatePanel 外面的控制項，必須透過 RegisterDataItem 方法，將自訂資料傳送至控制項
if (ScriptManager1.IsInAsyncPostBack)
{
ScriptManager1.RegisterDataItem(labTime0, DateTime.Now.ToString());
}
}
```
```xml
<asp:ScriptManager ID="ScriptManager1" runat="server">
</asp:ScriptManager>
<script type="text/javascript" language="javascript">      Sys.WebForms.PageRequestManager.getInstance().add_pageLoading(PageLoadingHandler);      function PageLoadingHandler(sender, args) {          var dataItems = args.get_dataItems();          if ($get('labTime0') !== null)              $get('labTime0').innerHTML = dataItems['labTime0'];      }    </script>    <asp:Label ID="labTime0" runat="server" Text="Time 0"></asp:Label>    <asp:UpdatePanel ID="UpdatePanel1" runat="server">      <ContentTemplate>          Now : <asp:Label ID="labTime1" runat="server" Text="Time 2"></asp:Label><br />          <asp:Button ID="btnUpdateOutside" runat="server" Text="Update outside controls" onclick="btnUpdateOutside_Click" />      </ContentTemplate>  </asp:UpdatePanel>  ```

## 如何在非同步的 Postback 執行期間，在頁面顯示訊息？

當非同步回傳啟動時，會引發 beginRequest 事件；結束時會引發 [endRequest](http://msdn.microsoft.com/zh-tw/library/bb383810%28v=vs.100%29.aspx) 事件。  
```javascript
var rm = Sys.WebForms.PageRequestManager.getInstance();

rm.add_beginRequest(BeginRequestHandler);
rm.add_endRequest(EndRequestHandler);
function BeginRequestHandler(sender, args) 
{
var elem = args.get_postBackElement();
if (elem.id == 'btnLongProcessing')
{
$get('msg').innerHTML = "Processing ...";
}
}
function EndRequestHandler(sender, args) {
$get('msg').innerHTML = "";
}
```

## 如何判斷非同步的 Postback 還在執行中，並禁止新的非同步回傳，或者取消舊的非同步回傳？

我們可以在 [initializeRequest](http://msdn.microsoft.com/zh-tw/library/bb397460%28v=vs.100%29.aspx) 事件中，判斷非同步回傳是否還在執行中。  再透過 abortPostBack 方法取消舊的非同步回傳，或者透過 set\_cancel 方法禁止新的非同步回傳  
```javascript
var rm = Sys.WebForms.PageRequestManager.getInstance();

rm.add_initializeRequest(InitializeRequestHandler);
function InitializeRequestHandler(sender, args) {
var prm = Sys.WebForms.PageRequestManager.getInstance();
var elem = args.get_postBackElement();

if (elem.id == 'btnCancelProcessing')
{
if (prm.get_isInAsyncPostBack()) 
{
prm.abortPostBack();    //取消舊的非同步回傳
}
}
else if (elem.id == 'btnLongProcessing')
{
if (prm.get_isInAsyncPostBack()) 
{
args.set_cancel(true);  //禁止新的非同步回傳
$get('msg').innerHTML = "Still Processing ...";
}
}
}
```

## 如何讓 UpdatePanel 內的控制項導致回傳，而非執行非同步回傳？

若要讓位於 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內部的控制項導致回傳，而不是非同步回傳，可以使用 [PostBackTrigger](http://msdn.microsoft.com/zh-tw/library/system.web.ui.postbacktrigger.aspx) 即可。

# The UpdateProgress Control

The [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) control is used to provide information in the form of graphics or text that is displayed to the user during a partial-page update.   For example, you might display an animated .gif image that shows the system is processing while you wait for the partial-page update to complete.  

You can define some information you wish to display to the user inside the ProgressTemplate tag of the [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) control.  The information inside this element can be an image, text, or similar HTML content.  It will be rendered to the browser as a hidden &lt;div&gt; tag that gets shown when the partial-page update is executed.  

若將 [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 放在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 裡，若該 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 發生局部更新時，UpdateProgress 就會顯現。  
若將 [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 放在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 外層，則任何 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 發生局部更新時，UpdateProgress 都會顯現。  
除非有指定 [AssociatedUpdatePanelID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.associatedupdatepanelid.aspx) 屬性，則只有指定的 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 發生局部更新時 [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 才會顯現。  
```xml
<asp:UpdateProgress ID="UpdateProgress1" runat="server" AssociatedUpdatePanelID="UpdatePanel6">
<ProgressTemplate>
<img src="wait.gif" alt="" />The data is updating
</ProgressTemplate>
</asp:UpdateProgress>
<asp:UpdateProgress ID="UpdateProgress2" runat="server" AssociatedUpdatePanelID="UpdatePanel7">
<ProgressTemplate>
<img src="wait.gif" alt="" />The data is inserting
</ProgressTemplate>
</asp:UpdateProgress>

<asp:UpdatePanel ID="UpdatePanel6" runat="server" UpdateMode="Conditional">
<ContentTemplate>
<asp:Button ID="Button6" runat="server" Text="Update" onclick="Button6_Click" />
</ContentTemplate>
</asp:UpdatePanel>
<asp:UpdatePanel ID="UpdatePanel7" runat="server" UpdateMode="Conditional">
<ContentTemplate>
<asp:Button ID="Button7" runat="server" Text="Insert" onclick="Button7_Click"/>
</ContentTemplate>
</asp:UpdatePanel>
```

####  [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 常用屬性：

- [AssociatedUpdatePanelID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.associatedupdatepanelid.aspx) ：設定與 UpdatePregress 產生關聯的 [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 控制項。
- [DisplayAfter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.displayafter.aspx) ：在多少時間後，若還沒取得非同步結果，即顯示 UpdateProgress. (預設值為 500, 即 0.5 秒)
- [DynamicLayout](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.dynamiclayout.aspx) ：設定 [UpdateProgress](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updateprogress.aspx) 是否動態呈現：  

- True：動態呈現（預設值），即不顯示時不佔用網頁空間。
- False：不動態呈現，即不顯示時也會佔用網頁空間。

# The Timer Control

The ASP.NET [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) control is an AJAX control that can be used to update portions of a page on a periodic, timed basis.   This is useful if you need to update an image such as an advertisement on a Web page or perhaps a value like a stock or news ticker.   The [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) control can also be used to simply run code on the server on a periodic basis.  

You can add a [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) control directly to an [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) control. In this case, the [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) automatically  triggers a partial-page update of the given [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) based on a time defined by the [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) control's [Interval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.interval.aspx) property (set to milliseconds).   
```xml
<asp:UpdatePanel ID="UpdatePanel8" runat="server">
<ContentTemplate>
<asp:Timer ID="Timer1" runat="server" Interval="1000" ontick="Timer1_Tick"></asp:Timer>
<asp:Label ID="labTime" runat="server" Text="Label"></asp:Label>
</ContentTemplate>
</asp:UpdatePanel>
```
```c#
protected void Timer1_Tick(object sender, EventArgs e)
{
labTime.Text = System.DateTime.Now.ToLongTimeString();
}
```

這個 [Timer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.timer.aspx) 如果不是放在 [UpdatePanel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.updatepanel.aspx) 內部，則必須使用 [AsyncPostBackTrigger](http://msdn.microsoft.com/zh-tw/library/system.web.ui.asyncpostbacktrigger.aspx) 去指定，以達到局部更新的效果。