---
title: Tracing 網站
layout: default
parent: 除錯與系統監控
nav_order: 2
description: "Tracing 網站"
date: 2012-10-17
tags: [Web,ASPNET,除錯與系統監控]
postid: "4710871934263001517"
---
當問題不容易在 Visual Studion 中發現時，就可以利用 ASP.NET 提供網頁監測功能進行追蹤。  要使用網頁監測功能，只要透過設定將「追蹤」功能啟用即可。  它可以針對個別網頁或整個網站進行追蹤，例如：記錄使用者資訊，網頁執行時間，錯誤訊息等等。  

# 啟用 ASP.NET 網站的追蹤

Tracing is the process of emitting data about a running application.   In ASP.NET, this data is logged to a trace log file that you can access through a Web browser.  

## 使用 Web.config 設定啟用追蹤

#### 網頁層級的追蹤

若要針對個別網頁追蹤，必須在網頁最上方的 Page 指示詞中加入 Trace="true" 屬性就可以啟用追蹤功能。 
```xml
<%@ Page Title="" Language="C#" Trace="true" %>
```

#### 應用程式層級的追蹤

若要針對整個網站設定追蹤，只要在 web.config 中的 &lt;system.web&gt; 區段宣告以下的設定即可。
```xml
<!--啟用偵錯--> 
<trace                   
enabled="true"              <!-- 啟用追蹤 -->
requestLimit="10"           <!-- Report 只產生10份 -->
pageOutput="false"          <!-- 在個別頁面顯示追蹤資訊  -->
traceMode="SortByTime"      <!-- 追蹤結果的排序次序 -->
localOnly="true"            <!-- 只有本機才看的到 -->
mostRecent="true"           <!-- 蒐集最新的Report為主 -->
/>
```

## 使用網站管理工具啟用追蹤 ( Web Site Administration Tool ,WSAT )

「網站管理工具」可由 Visual Studio 選單中的 [網站]→[ASP.NET 組態] 開啟。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEQa3FW52yQ7PADMh6cgVERrK-Qwo3ghwC7AMww-TaVX4AZca_NX92RyflE-ubqP2VLCdM4ioQoKt4YlSmh0RMyfnVvoqKC6WAjh-4zFb03NbtYNrlLEz47o7v7YLCV1cN4TAhmd2BdwE/s344/vs-wsat-menu.png)

或者直接在方案總管中，點選[ASP.NET 組態]工具

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8BmJFeQgCCV3rAIIb0_oaLKHJY01wx7M9kQ6S6u1IgslHPi9uI9oRNbWEBDx_XlrVdypniAzBTvglY8zdZfCdjbBRg23l-gAL5FCit_Eh6IPusDRaB5QrLdZMHPYpNB3H9ueHl6YguUk/s330/vs-wsat-icon.png)

接著就可以看到如下的網站管理工具畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgI9AKNQ7FRGHX0ivb2hGZ2FsO5GkQ9DH0RZ6PA5XKfWiv4WCbsjJ3OxUaV147i4ekDjq8K-SjC6FoEN-y4BDP5W0o-1ABRGAQfrI0z5JBfcTIhqBvn0ci5T66fwUgrGrXlFNFzcZrYpzk/s560/vs-wsat.png)

## 檢視網站的追蹤資訊

當啟用追蹤功能之後，你可以將網頁的 pageOutput 屬性設為 true ，就可以直接在網頁下方看見追蹤資訊。  若不想被使用者直接看到追蹤資訊，就可以將 pageOutput 屬性設為 false ，不過此時記錄仍然會被記錄下來。   這時就必須使用追蹤檢視器 (Trace.axd) ，來檢視偵側的資訊。  這個追蹤檢視器網頁可以由網站根目錄下直接開啟。例如，若 http://server/application/ 是網站的根目錄，則開啟 **http://server/application/trace.axd** 即可瀏覽該網站的追蹤資訊。  

如下圖所示即為追蹤資訊的頁面，裡頭包含所有 Page 資訊，例如，QueryString, Cookie, Session, ServerVraiables, 執行時間等等。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0N_QJ1oXt94YU2FG-pSK5ttrKfZ5TqjVRQNvJExbRnyG8pCRWLMsbWZjJjV2ikTHW85myK5QC0Zk3m3Uyj9khXh3jVYQBOeACewiQjcwdA9wOLU701i_bDs2P0jCOxk11Qn3M7Jwxchk/s822/web-trace-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRimgqvrd7IoIdxKBJFiXzxYpAvKNtxm13oifwalhXzvrJRtKbJkHI7dAEJNOTuJQJHjNTdUzmOAogeAENS6tGROoky6e4COo_BDHM6GXJBvmule2bpCVOLfq6Pdwaaa_aYHq3_nW5FfY/s822/web-trace-02.png)

## 自訂網頁追蹤

[System.Diagnostics](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.aspx) 命名空間提供了 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 、 [Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 兩個物件用來輸出追蹤的訊息。 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 物件是用在專案的Debug模式，而 [Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 是用在上線環境中執行的追蹤。   Debug、Trace 模式預設會將訊息顯示在輸出視窗，如果要輸出至其他目標，則必須指定 Listener。  例如下面範例，就可以在輸出視窗看到追蹤的訊息。  
```c#
protected void btnTrace_Click(object sender, EventArgs e)
{
System.Diagnostics.Trace.WriteLine("Custom Category", "btnTrace_Click called");
System.Diagnostics.Debug.WriteLine("TestDebug");
}
//btnTrace_Click called: Custom Category
//TestDebug
```

Page 物件也包含一個 [Trace](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.trace.aspx) 成員，可以用來將追蹤資料輸出到追蹤記錄裡(trace log)，當然這必須先將網頁追蹤功能開啟。  其使用方法如下：  
```c#
protected void btnTrace_Click(object sender, EventArgs e)
{
Page.Trace.Write("Test Write Message");
Page.Trace.Warn("Test Warn Message");       //Warn：以紅色字體顯示訊息
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9xxe0B5EoCAQNKDV8EnhlD1TjUOfcP2lDbchasbmT8q9dvuFBykkbLe6l46Nynp0SX5dOCax6eirAy021a1y9YtIT0HVsx_mvSHkez2NEHNi5Ai_YN9HQAyJ8GzUrYjSYZ8vL8IOZKMI/s850/page-trace-message.png)

另外，我們也可以在網頁中訂閱 [Trace.TraceFinished](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.tracefinished.aspx) 事件，透過程式碼取得追蹤資料。
```c#
protected void Page_Load(object sender, EventArgs e)
{
Trace.TraceFinished += Trace_TraceFinished;
}

void Trace_TraceFinished(object sender, TraceContextEventArgs e)
{
//列舉追蹤記錄    
foreach (TraceContextRecord r in e.TraceRecords)    
{        
Response.Write(r.Message + "<br/>");        
}
}
```

## 追蹤 AJAX 應用程式

在上面的例子中，我們使用 Debug 或 Trace 物件，將追蹤的資訊輸出到**輸出視窗**。  因為這些程式碼都是在主機端執行的，所以沒有問題。  如果要追蹤的資訊是在 java-script 之中，那就必須使用 [Sys.Debug](http://msdn.microsoft.com/zh-tw/library/bb397422%28v=vs.100%29.aspx) 物件才可以輸出追蹤的資訊。  
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function button1_onclick() {
Sys.Debug.trace("output tracing to textarea");
//Sys.Debug.assert( (3 == 2)  ,"Button1 clicked");
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server"></asp:ScriptManager>
<input id="Button1" type="button" value="script debug" onclick="button1_onclick()" /> 
</div>
</form>
</body>
```

另外，也可以在頁面放置一個 TextArea 控制項，並將 **ID** 設成 [TraceConsole](http://msdn.microsoft.com/zh-tw/library/bb310977%28v=vs.100%29.aspx) ，  這樣，Ajax Library 就知道要將追蹤的訊息輸出到這個文字區塊。  
```xml
<body>
<form id="form1" runat="server">
<div>
<textarea id="TraceConsole" cols="20" rows="2"></textarea>
<asp:ScriptManager ID="ScriptManager1" runat="server"></asp:ScriptManager>
<input id="Button1" type="button" value="script debug" onclick="button1_onclick()" /> 
</div>
</form>
</body>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1UArQUMhMhx41KREykp-atFMk3-LAUFUI_lzXYnZa9Jj03T5CrnfmC59ShuRx_mXAzO_t-W8RVx8RpArXMl38ypO4Z9BTjYtEl5IBhFHUS7l_IrGqpTAJ4cbzfhPP2Pa8L5Mi4yW_q1A/s283/tracing-output-to-textarea.png)
## 參考資料  

- [ASP.NET 追蹤概觀](http://msdn.microsoft.com/zh-tw/library/bb386420.aspx)
- [HOW TO：啟用 ASP.NET 應用程式的追蹤](http://msdn.microsoft.com/zh-tw/library/0x5wc973%28v=VS.100%29.aspx)