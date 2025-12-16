---
title: 自訂非同步網頁
layout: default
parent: 自訂網站
nav_order: 4
description: "自訂非同步網頁"
date: 2013-01-23
tags: [Web,ASPNET,自訂網站]
postid: "5516961953763288988"
---
如果只要想使用非同步作業執行運算，可以使用和 WinForm 相同的 APM 技巧即可。  這裡特別指的是網頁的非同步作業，其考量點會和 WinForm 的非同步有所差異。  在 WinForm 中，非同步作業常用來執行耗時的工作，並且適時的回應使用者工作進度。  但是在 WebFrom 中，不管是否使用非同步作業，都比須等待整個網頁處理完畢後才會傳送回應到客戶端，所以網頁非同步不是用來回應使用者工作進度的。  

在 WebForm 中使用非同步執行長時間工作的最主要目的是希望善用 ThreadPool 。  因為當 IIS 收到一個要求時，就會由 ThreadPool 中佔用一個 Thread 來處理要求，一直到執行完畢回應用戶端為止。  若 ThreadPool 中的 Thread 被佔用完了，IIS 就會先將用戶端來的要求加入要求佇列(Request Queue)，但是如果佇列又１達到 IIS 的上限，IIS 就會暫時無法提供服務，並回應    **HTTP 503 服務無法使用** 的錯誤訊息。  

使用網頁的非同步作業來執行工作，它會將工作交由 CLR 的 Thread 去執行，然後釋放原先 IIS 的 Thread 回 ThreadPool 。  等執行結束再跟 ASP.NET 的 ThreadPool 請求一個 Thread 來回應要求。這樣子 ASP.NET 的 ThreadPool 就有比較多空閒的 Thread 去服務更多的 request 。  

IIS 設定佇列長度參考如下圖示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlF6E6zzGV6mxUG0bmYGOXyC0n5QiX-Qg4IDjMlFp7o2HCVpNu_z5nH_l9gwjyzPSEbGKnT5UQCKy5w5SMpIbfRnWccEpDat8IXy8XGUMudRp5gYMT7MgsosJLfL-fTRHHUXAbfCJUr_k/s550/iis-threadpool-size.png)

# 非同步的 Handlers

You create an asynchronous handler much like you would a synchronous handler.   You use a similar interface called [IHttpAsyncHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpasynchandler.aspx) and then override the **IsReusable** property and the **BeginProcessRequest** method.    You also provide a callback method that gets called when the asynchronous operation completes.   Finally, you write code inside the **EndProcessRequest** method to deal with any cleanup when the process completes.  

#### 1. 建立非同步 Handlers

- 建立非同步 Handlers，必須實作一個 [IHttpAsyncHandler](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpasynchandler.aspx) 介面的類別，並完成該介面必要的 [BeginProcessRequest](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpasynchandler.beginprocessrequest.aspx) 和 [IsReusable](http://msdn.microsoft.com/zh-tw/library/system.web.ihttphandler.isreusable.aspx) 等方法。
- 在 [BeginProcessRequest](http://msdn.microsoft.com/zh-tw/library/system.web.ihttpasynchandler.beginprocessrequest.aspx) 方法中，建立實作 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult.aspx) 介面的類別的實體。並叫用該類別的啟動方法。

#### 2. 將執行作業物件化

- 自訂一個實作 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult.aspx) 介面的類別。
- 在自訂類別中，新增一個函式，在此函式中撰寫想要在非同步作業中執行的程式碼。
- 在自訂類別中，新增一個用來啟動非同步作業的方法，在這個方法中，使用 ThreadPool.QueueUserWorkItem 將工作放至執行緒佇列中。

#### 非同步 Handler 範例
```c#
public class PhotoHandlerAsync : IHttpAsyncHandler
{
public IAsyncResult BeginProcessRequest(HttpContext context, AsyncCallback callback, object extraData)
{
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
string EmpID = (context.Request.QueryString["ID"] != null) ? context.Request.QueryString["ID"] : "9411994";
string jobid = EmpID;

myDebug.WriteLine("[{2}][{0}]BeginProcessRequest...       {1}", threadid, Tools.GetNow(), jobid);
PhotoHandlerOperation asyncOP = new PhotoHandlerOperation(context, callback, jobid);

myDebug.WriteLine("[{2}][{0}]BeginProcessRequest queued...{1}", threadid, Tools.GetNow(), jobid);
asyncOP.StartAsync(jobid);

return asyncOP;
}

public void EndProcessRequest(IAsyncResult result)
{
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
string jobid = (result.AsyncState != null) ? result.AsyncState.ToString() : "";
myDebug.WriteLine("[{2}][{0}]EndProcessRequest...         {1}", threadid, Tools.GetNow(), jobid);
}

public bool IsReusable
{
get { return false; }
}

public void ProcessRequest(HttpContext context)
{
throw new NotImplementedException();
}
}

public class PhotoHandlerOperation : IAsyncResult
{
private bool _completed;
private object _state;
private AsyncCallback _callback;
private HttpContext _context;

public PhotoHandlerOperation(HttpContext context, AsyncCallback callback, object state)
{
_callback = callback;
_context = context;
_state = state;
_completed = false;
}

public object AsyncState
{
get { return _state; }
}

public System.Threading.WaitHandle AsyncWaitHandle
{
get { return null; }
}

public bool CompletedSynchronously
{
get { return false; }
}

public bool IsCompleted
{
get { return _completed; }
}

public void StartAsync()
{
ThreadPool.QueueUserWorkItem(new WaitCallback(StartAsyncOperation), null);
}

public void StartAsync(object extraData)
{
ThreadPool.QueueUserWorkItem(new WaitCallback(StartAsyncOperation), extraData);
}

public void StartAsyncOperation(object workItemState)
{
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
string jobid = (workItemState!=null)? workItemState.ToString() : "";
string EmpID = (workItemState != null) ? workItemState.ToString() : "9411994";

HttpRequest request = _context.Request;
HttpResponse response = _context.Response;

myDebug.WriteLine("[{2}][{0}]StartAsyncOperation...       {1}", threadid, Tools.GetNow(), jobid);

byte[] photo = GetEmpPhoto(EmpID);
if (photo != null)
{
response.ContentType = "image/jpeg";
response.BinaryWrite(photo);
}
else
{
response.Write("./images/noimage.gif");
}
myDebug.WriteLine("[{2}][{0}]FinishAsyncOperation...      {1}", threadid, Tools.GetNow(), jobid);

_completed = true;
_callback(this);
}

private byte[] GetEmpPhoto(string EmpID)
{
Thread.Sleep(500);
...
}
}

//[9411981][11]BeginProcessRequest...       14:15:32 8915
//[9411983][ 5]BeginProcessRequest...       14:15:32 8915
//[9411984][14]BeginProcessRequest...       14:15:32 8915
//[9411984][14]BeginProcessRequest queued...14:15:32 9155
//[9411983][ 5]BeginProcessRequest queued...14:15:32 9135
//[9411981][11]BeginProcessRequest queued...14:15:32 9135
//[9411984][ 6]StartAsyncOperation...       14:15:32 9195
//[9411982][ 5]BeginProcessRequest...       14:15:32 9275
//[9411983][14]StartAsyncOperation...       14:15:32 9195
//[9411982][ 5]BeginProcessRequest queued...14:15:32 9335
//[9411981][11]StartAsyncOperation...       14:15:32 9295
//[9411982][ 5]StartAsyncOperation...       14:15:32 9355
//[9411984][ 6]FinishAsyncOperation...      14:15:33 4455
//[9411984][ 6]EndProcessRequest...         14:15:33 4475
//[9411983][14]FinishAsyncOperation...      14:15:33 4705
//[9411982][ 5]FinishAsyncOperation...      14:15:33 4765
//[9411983][14]EndProcessRequest...         14:15:33 4835
//[9411982][ 5]EndProcessRequest...         14:15:33 5035
//[9411981][11]FinishAsyncOperation...      14:15:33 5455
//[9411981][11]EndProcessRequest...         14:15:33 5665
```

#### 若使用同步的 Handlers
```c#
public void ProcessRequest(HttpContext context)
{
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
string EmpID = (context.Request.QueryString["ID"] != null) ? context.Request.QueryString["ID"] : "9411994";
string jobid = EmpID;

HttpRequest request = context.Request;
HttpResponse response = context.Response;

myDebug.WriteLine("[{2}][{0}]BeginProcessRequest...    {1}", threadid, Tools.GetNow(), jobid);

byte[] photo = GetEmpPhoto(EmpID);
if (photo != null)
{
response.ContentType = "image/jpeg";
response.BinaryWrite(photo);
}
else
{
response.Write("./images/noimage.gif");
}

myDebug.WriteLine("[{2}][{0}]EndProcessRequest...      {1}", threadid, Tools.GetNow(), jobid);
}

private byte[] GetEmpPhoto(string EmpID)
{
Thread.Sleep(500);
...
}
//[9411983][ 6]BeginProcessRequest...    14:18:52 7479
//[9411981][ 5]BeginProcessRequest...    14:18:52 7479
//[9411982][13]BeginProcessRequest...    14:18:52 7759
//[9411983][ 6]EndProcessRequest...      14:18:53 2819
//[9411982][13]EndProcessRequest...      14:18:53 3089
//[9411981][ 5]EndProcessRequest...      14:18:53 3319
//[9411984][ 6]BeginProcessRequest...    14:18:53 3349
//[9411984][ 6]EndProcessRequest...      14:18:53 8640
```

# 非同步的網頁

ASP.NET 使用 ThreadPool 來服務 request，但是 ThreadPoll 資源有限，若剛好有多個 request 都提出耗時的需求，那麼 ThreadPool 就比較容易被消耗完，導至效能瓶頸 (performance bottleneck)。  所以使用非同步執行耗時工作，它會釋放 ASP.NET 的 ThreadPool 資源，交尤其他 Thread 去執行，等執行結束再由 ASP.NET 的 ThreadPool 接手管理。  這樣子 ASP.NET 的 ThreadPool 就有比較多空閒的 Thread 去服務更多的 request 。  

#### 1. 啟用非同步網頁

在 @ Page 宣告中，加上 **Async="true"** 屬性宣告。  這個屬性會讓 Page 實作 IHttpAsynchHnadler 介面。  
```xml
<%@ Page Language="C#" AutoEventWireup="true" Async="true" %>
```

#### 2. 建立非同步作業的開始和結束函式

接下來在 code-behind 中，建立非同步作業開始的方法(method)，和結束的方法。

- BeginAsyncOperation ：啟動非同步作業事件。
- EndAsyncOperation ：結束非同步作業事件。
```c#
protected IAsyncResult BeginProcessRequest(object sender, EventArgs e, AsyncCallback cb, object extraData)
{
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
string jobid = extraData.ToString();

myDebug.WriteLine("[{2}][{0}]BeginProcessRequest...       {1}", threadid, Tools.GetNow(), jobid);

PhotoHandlerOperation imageOperation = new PhotoHandlerOperation(this.Context, cb, extraData);
imageOperation.StartAsync(jobid);

myDebug.WriteLine("[{2}][{0}]BeginProcessRequest queued...{1}", threadid, Tools.GetNow(), jobid);
return imageOperation;
}
protected void EndProcessRequest(IAsyncResult result)
{
string jobid = result.AsyncState.ToString();
string threadid = Thread.CurrentThread.ManagedThreadId.ToString().PadLeft(2);
myDebug.WriteLine("[{2}][{0}]EndProcessRequest...         {1}", threadid, Tools.GetNow(), jobid);
}
```

#### 3. 在 Page\_Load 事件中，叫用 [AddOnPreRenderCompleteAsync](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.addonprerendercompleteasync.aspx) 方法
```c#
public void AddOnPreRenderCompleteAsync(BeginEventHandler beginHandler, EndEventHandler endHandler);
public void AddOnPreRenderCompleteAsync(BeginEventHandler beginHandler, EndEventHandler endHandler, object state);
```

[Page.AddOnPreRenderCompleteAsync](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.addonprerendercompleteasync.aspx) 這個方法是用來註冊非同步作業的開始和結束事件處理常式的委派。  在使用上有幾點須要知道：  

- 即使註冊了多個處理常式，同一時間只會執行一個處理常式。不過可以在一個處理常式中，同時執行多個非同步作業。
- 必須在 PreRender 之前，使用 [AddOnPreRenderCompleteAsync](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.addonprerendercompleteasync.aspx) 註冊非同步處理常式。
- 非同步處理常式會在 PreRender 和 PreRenderComplete 事件之間被呼叫執行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhn-D41eIN7Jy2rzi7XJwhDDbnVMWzw6d_rEOhFeGDY20R2t-C4DK1IB53_qlZ5LppWEzK87EOWH63ks4v30Syqc8fSyaaU23DLNHhxLODr8FAnMPgVCsw0jqjzWIvciRxchLjsp844eK0/s729/web-async.png)
```c#
protected void Page_Load(object sender, EventArgs e)
{
//在網頁中使用非同步執行方法
AddOnPreRenderCompleteAsync(BeginProcessRequest, EndProcessRequest, "job1");
AddOnPreRenderCompleteAsync(BeginProcessRequest, EndProcessRequest, "job2");
AddOnPreRenderCompleteAsync(BeginProcessRequest, EndProcessRequest, "job3");
AddOnPreRenderCompleteAsync(BeginProcessRequest, EndProcessRequest, "job4");
}

[job1][ 5]BeginProcessRequest...       13:57:09 7294
[job1][ 5]BeginProcessRequest queued...13:57:09 7324
[job1][ 6]StartAsyncOperation...       13:57:09 7334
[job1][ 6]FinishAsyncOperation...      13:57:10 3374
[job1][ 6]EndProcessRequest...         13:57:10 3384
[job2][ 6]BeginProcessRequest...       13:57:10 3384
[job2][ 6]BeginProcessRequest queued...13:57:10 3394
[job2][ 5]StartAsyncOperation...       13:57:10 3394
[job2][ 5]FinishAsyncOperation...      13:57:10 8634
[job2][ 5]EndProcessRequest...         13:57:10 8634
[job3][ 5]BeginProcessRequest...       13:57:10 8644
[job3][ 5]BeginProcessRequest queued...13:57:10 8644
[job3][ 6]StartAsyncOperation...       13:57:10 8654
[job3][ 6]FinishAsyncOperation...      13:57:11 3775
[job3][ 6]EndProcessRequest...         13:57:11 3785
[job4][ 6]BeginProcessRequest...       13:57:11 3785
[job4][ 6]BeginProcessRequest queued...13:57:11 3795
[job4][ 5]StartAsyncOperation...       13:57:11 3795
[job4][ 5]FinishAsyncOperation...      13:57:11 8835
[job4][ 5]EndProcessRequest...         13:57:11 8835
}
```

# 非同步的網頁 ２

使用 [Page.ExecuteRegisteredAsyncTasks](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.executeregisteredasynctasks.aspx) 是另一種向頁面註冊新的非同步工作的方法。  其主要方法是透過 [PageAsyncTask](http://msdn.microsoft.com/zh-tw/library/system.web.ui.pageasynctask.aspx) 類別，建立一個平行執行物件。  
```c#
IAsyncResult BeginAsyncOperation(object sender, EventArgs e, AsyncCallback callback, object state)
{
...
}
void EndAsyncOperation(IAsyncResult ar)
{
...

}
protected void btnAsync_Click(object sender, EventArgs e)
{
string ThreadID = Thread.CurrentThread.ManagedThreadId.ToString();
myDebug.WriteLine("[" + ThreadID + "] btnAsync_Click Before RegisterAsyncTask 1 " + Tools.GetNow());

//建立 PageAsyncTask 執行個體
PageAsyncTask asyncTask = new PageAsyncTask(BeginAsyncOperation, EndAsyncOperation, null, "job1", true /\*平行處理\*/);

//註冊非同步任務
Page.RegisterAsyncTask(asyncTask);      
}
```
## 參考資料  

- [Page.AddOnPreRenderCompleteAsync 方法](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.addonprerendercompleteasync%28v=vs.100%29.aspx)
- [Page.RegisterAsyncTask 方法](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.registerasynctask%28v=vs.100%29.aspx)
-