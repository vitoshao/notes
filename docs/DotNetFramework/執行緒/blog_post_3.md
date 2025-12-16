---
title: 多執行緒
layout: default
parent: 執行緒
nav_order: 3
description: "多執行緒"
date: 2012-02-01
tags: [DotNetFramework,執行緒]
postid: "1484865139475509859"
---
簡單講，執行緒就是要讓程式有並行處理的能力，也就是希望同一時間可以執行多項作業。  對 Windows 應用程式而言，讓耗費時間的工作在幕後執行，可以讓 UI 仍能保持回應能力。  對伺服器應用程式而言，多執行緒處理可讓不同的執行緒去處理每個接收到的請求。否則，在先前的請求完全得到滿足之前，每個新的請求都不會獲得服務。  

幾底幾個項目，都是.NET 中用以提供達到這樣的功能的類別物件。  

- [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) ：
- [ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.aspx) ：
- [BackgroundWorker](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.aspx) ：
- [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) ：
- [Parallel](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.parallel.aspx) ：

不管使用以上哪一種方式，只要是並行作業的處理，你都必須將所要執行的程式碼用一個函式包起來，而且這個函式必須是**無回傳值的函式**。  而所謂的非同步作業模型，指的就是在另一個執行緒中去執行該函式，這樣主程式就可以繼續下一行程式，不用一直等到函式結束。  若主程序有參數要傳遞給它，也只能使用一個 Object 型別的參數。  例如：。  
```c#
void DoWork()
{
//ToDo..
}

void DoWork_Para(Object state)
{
//ToDo..
}
```

# 使用 Thread 物件

一個執行中的程式稱為一個處理序(Process)，在處理序中，可以建立多個執行緒以共同處理相關的部份程式碼。  

[Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 類別定義在 [System.Threading](http://msdn.microsoft.com/zh-tw/library/system.threading.aspx) 命名空間，可以用來建來執行緒個體，以並行執行部份程式碼。  

#### Thread 建構函式
```c#
public Thread(ThreadStart starter);
public Thread(ParameterizedThreadStart starter);
```

## 如何使用 Thread

##### 建立 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 步驟：

1. 將要在背景執行的程式碼，包裝在一個 void (object) 型態的函式中。
2. 若不須傳遞資料給函式，則使用 [ThreadStart](http://msdn.microsoft.com/zh-tw/library/system.threading.threadstart) 委派來指定執行緒所要執行的函式。
3. 若須要傳遞資料給函式，則使用 [ParameterizedThreadStart](http://msdn.microsoft.com/zh-tw/library/system.threading.parameterizedthreadstart) 委派指定執行緒所要執行的函式。
4. 最後，使用上一步驟建立的 [ThreadStart](http://msdn.microsoft.com/zh-tw/library/system.threading.threadstart) 或 [ParameterizedThreadStart](http://msdn.microsoft.com/zh-tw/library/system.threading.parameterizedthreadstart) 委派為參數，建立 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 執行個體，以啟動執行緒。
```c#
private void DoSomething()
{
Random rnd = new Random(Guid.NewGuid().GetHashCode());
Thread.Sleep(2000 + rnd.Next(-800, 1000));
Console.WriteLine(DateTime.Now.ToString());
}
private void button1_Click(object sender, EventArgs e)
{
//方法１：使用一個 ThreadStart 委派當做 Thread 的參數
ThreadStart start1 = new ThreadStart(DoSomething);
Thread thread1 = new Thread(start1);  
thread1.Start();

//方法２：若委派方法中，只有一個方法叫用，可以精簡如下：
Thread thread2 = new Thread(DoSomething);
thread2.Start();

//方法３：使用 Lambda 表示式

//3.1 直接用一個 delegate 方法當做 Thread 的參數
Thread thread3 = new Thread(
delegate() 
{ 
DoSomething(); 
});
thread3.Start();

//3.2 省略 delegate 關鍵字
Thread thread4 = new Thread(
()=>
{
//...
DoSomething();
//...
});
thread2.Start();

//3.3 若只呼叫一個方法，省略大括號
Thread thread5 = new Thread(
() => DoSomething()
);
thread5.Start();

//3.4 直接以方法名稱表示
Thread thread6 = new Thread(DoSomething);
thread5.Start();

//3.5 最精簡版本
new Thread(DoSomething).Start();
}
```
```c#
private void DoSomething_Para(object data)
{
Random rnd = new Random(Guid.NewGuid().GetHashCode());
Thread.Sleep(2000 + rnd.Next(-800, 1000));
Console.WriteLine(DateTime.Now.ToString() + " Data:" + data.ToString());
}

private void button2_Click(object sender, EventArgs e)
{
ParameterizedThreadStart start1 = new ParameterizedThreadStart(DoSomething_Para);
Thread thread1 = new Thread(start1);
thread1.Start(10);

Thread thread2 = new Thread(
delegate()
{
DoSomething_Para(10);
});
thread2.Start();

Thread thread3 = new Thread(DoSomething_Para);
thread3.Start(10);

//最精簡版本
new Thread(DoSomething_Para).Start(10);
}
```

## Thread 如何回傳結果

[Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 只是一個用來建立執行緒的物件，讓相關的程式碼可以並行執行，它無法直接取得並行程式碼的執行結果，自然無法提供回傳值功能。  如果想要取得執行緒中程式碼的執行結果，就必須使用到[同步處理機制](https://msdn.microsoft.com/zh-tw/library/ms228964%28v=vs.100%29.aspx)，例如底下幾種作法。  

- 使用全域的變數。
- 透過啟動委派時所傳入的參數。

### 使用全域的變數

要取得子執行緒的執行結果是最簡單直覺的方式就是透過共用（全域）的變數，剩下的問題就是判斷執行緒是否已結束。  不過為了確保子執行緒已執行完成，你必須自行加入等待子執行緒完成的機制。  最簡單的方式就是叫用 [Thread.Join](http://msdn.microsoft.com/zh-tw/library/5w9y371x.aspx) 方法，該方法會等到子執行緒結束後才會繼續往下執行。  
```c#
private void DoWork1()
{
value = "hello world";
}

object value = null;
private void button10_Click(object sender, EventArgs e)
{
var thread = new Thread(DoWork1);
thread.Start();
thread.Join();
Console.WriteLine(value.ToString());
}
```

### 透過委派的參數

當叫用 Thread.Start() 方法時，你可以傳遞參數值給要執行的方法，  
```c#
public class MyObject
{
public string data { get; set; }
public string result { get; set; }
}

private void DoWork2(object obj)
{
MyObject myObject = obj as MyObject;
myObject.result = "hello world";
}

private void button11_Click(object sender, EventArgs e)
{
MyObject obj = new MyObject();
obj.data = "some data";

var thread = new Thread(DoWork2);
thread.Start(obj);
thread.Join();
Console.WriteLine(obj.result);
}
```

### 實作 IAsyncResult

要取得執行緒中程式碼的執行結果，你也可以實作 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult.aspx) 介面。  這個方法不單單是執行緒的議題，它算是[非同步程式設計模型（APM）](https://msdn.microsoft.com/zh-tw/library/jj152938.aspx)的範圍。  它利用委派的 BeginInvoke 去叫用 BeginMethod ，等該方法執行完畢後會叫用 EndMethod ，你可以在 EndMethod 透過 EndInvoke 取得執行結果。  底下僅列出簡單範例，詳細做法放在另外一篇：[[非同步程式設計](http://vito-note.blogspot.com/2012/02/blog-post_6.html)]。  
```c#
//建立執行作業的委派簽章
delegate string delegateDoWork(object obj);

public string DoWorkX(object obj)
{
ShowStartLog("DoWorkX", obj);
Thread.Sleep(2000);
return GetNow();
}

public void DoWorkXComplete(IAsyncResult ar)
{
delegateDoWork state = ar.AsyncState as delegateDoWork;
string result = state.EndInvoke(ar);
Console.WriteLine(result);
}

private void bnAsyncResultUsingThread_Click(object sender, EventArgs e)
{
delegateDoWork dgDowork = new delegateDoWork(DoWorkX);
AsyncCallback callback = new AsyncCallback(DoWorkXComplete);

for (int i = 0; i < 5; i++)
{
dgDowork.BeginInvoke(i, callback, dgDowork);
}
}
```

# 使用 ThreadPool 物件

透過 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 物件雖然可以達到並行作業的要求，但是如果有大量的並行作業需要執行，那麼頻繁的建立與釋放執行緒，也是很消耗系統資源的。  這時可以透過[Thread Pool](http://msdn.microsoft.com/zh-tw/library/h4732ks0)（執行緒集區）機制來增加效率。  

## 認識 ThreadPool

Thread Pool 是指先建立好數個 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 來因應大量的 Request 。  當 Request 的數量大於[Thread Pool](http://msdn.microsoft.com/zh-tw/library/h4732ks0)中 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 數量時，就把暫時無法處理的 Request 放置在一個 Queue 的機制中保存起來，等到有其他的工作處理完畢時，再從 Queue 中取出待處理的 Request 以繼續進行處理，直到 Queue 中沒有 Request 為止。   

Thread Pool 功能中必須包含 Queue 的機制、Thread 的狀況掌控，還要能夠依系統的閒忙程度調節 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 數目，以達到系統的最大效能。  要自行設計這些功能是很繁瑣的，好在 .NET 提供了 [Thread Pool Model](http://msdn.microsoft.com/zh-tw/library/h4732ks0%28v=vs.100%29) ，來簡單達到同時將多個工作放到背景中執行。  

在 .NET Framework 的設計中，每個.NET 應用程式在執行階段，CLR 會為應用程式所在的 Process 提供一個 [Thread Pool](http://msdn.microsoft.com/zh-tw/library/h4732ks0%28v=vs.100%29) 。  其中包含了250條可用 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) (在多CPU的機器上，Thread Pool的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 數等於 CPU 數乘上250。例如: .NET 程式在4顆 CPU 的主機上執行時，Thread Pool 中就會有1000條 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 可用)。  Runtime 提供了 Queue 的管理機制，也會主動安排 Pool 中空閒的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 去處理 Queue 中等待執行的程式碼。

## ThreadPool 類別

要使用 [Thread Pool](http://msdn.microsoft.com/zh-tw/library/h4732ks0%28v=vs.100%29) ，要依賴命名空間 [System.Threading](http://msdn.microsoft.com/zh-tw/library/system.threading.aspx) 中的 [ThreadPool](http://msdn.microsoft.com/zh-tw/library/y5htx827.aspx) 類別。  [ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool) 類別的所有 Member 都是 Static/Shared 的，也沒有 Constructor 可以 New 一個執行個體，因為系統中就只有一個統一管理的Thread Pool。  先看看 [ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool) 提供的幾個方法：  

- [QueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.queueuserworkitem) ：佇列要執行的方法。可以使用執行緒集區執行緒時，即可執行這個方法。
- [RegisterWaitForSingleObject](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.registerwaitforsingleobject) ：登錄等候 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 的委派。
- [GetAvailableThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.getavailablethreads) ：擷取 [GetMaxThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.getmaxthreads) 方法所傳回執行緒集區的執行緒最大數目，與目前作用中數目之間的差異。
- [SetMaxThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.setmaxthreads) ：設定可同時作用的執行緒集區要求數目。
- [SetMinThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.setminthreads) ：在切換至管理執行緒建立和解構的演算法之前，設定執行緒集區隨著提出新要求，視需要建立的執行緒最小數目。
- [GetMaxThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.getmaxthreads) ：擷取可並行使用的執行緒集區要求的數目。
- [GetMinThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.getminthreads) ：在切換至管理執行緒建立和解構的演算法之前，擷取執行緒集區隨著提出新要求，視需要建立的執行緒最小數目。
- [UnsafeQueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.unsafequeueuserworkitem) ：將指定的委派佇列排入執行緒集區，但不會將呼叫堆疊散佈到背景工作執行緒。
- [UnsafeRegisterWaitForSingleObject](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.unsaferegisterwaitforsingleobject) ：註冊委派，以等候 WaitHandle，但是不會將呼叫堆疊散佈到背景工作執行緒。
- [WaitCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.waitcallback.aspx) 委派：表示執行緒集區執行緒執行的回呼方法。
- [WaitOrTimerCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.waitortimercallback.aspx) 委派：表示 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 收到信號或逾時時呼叫的方法。

#### 使用 [ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.aspx) 步驟

- 將要在背景執行的程式碼，包裝在一個 void (object) 型態的函式。
- 以該函式名稱為參數，建立 [WaitCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.waitcallback) 委派。
- 以該委派為參數，叫用 [ThreadPool.QueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.queueuserworkitem) 這個靜態方法，以啟動執行緒。
```c#
for (var data = 0; data < 10; data++)
{
//建立WaitCallback物件，表示執行緒集區執行緒執行的回呼方法。
WaitCallback callback = new WaitCallback(DoSomething_Para);

// 佇列要執行的方法，並傳遞參數給指定的工作。
ThreadPool.QueueUserWorkItem(callback, data);
}
```
```c#
private void button4_Click(object sender, EventArgs e)
{
for (var data = 0; data < 10; data++)
{
ThreadPool.QueueUserWorkItem(DoSomething_Para, data);
}
}
```

## ThreadPool 範例

[Thread Pool](http://msdn.microsoft.com/zh-tw/library/h4732ks0) 中有系統預設的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) ，若其中有休眠中的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) ，當使用 [ThreadPool.QueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.queueuserworkitem) 方法時，就會換醒休眠的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 。  這樣子我們就不用一直使用 new [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 去手動建立執行緒。且執行緒集區內的執行緒工作滿檔時，會再自動增加執行緒的數量。  

使用 [QueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.queueuserworkitem) 方法，須要傳入一個 [WaitCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.waitcallback) 委派，所以要執行的方法，必須和 [WaitCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.waitcallback) 委派要有相同的簽名。  另外，要知道的是，使用 [ThreadPool.QueueUserWorkItem](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.queueuserworkitem) 所引發的執行緒，是以背景的執行模式。   
```c#
[ComVisible(true)]
public delegate void WaitCallback(object state);
```
```c#
private void button1_Click(object sender, EventArgs e)
{
// 透過ThreadPool,註冊10個要執行的工作
for (int i = 1; i <= 5; ++i )
{
Console.WriteLine("[{1}] No.{0} QueueUserWorkItem...", i, Common.Tools.GetNow());

// 指明佇列要執行的方法   //當 ThreadPool 內的 thread 有空時，就會執行 DoWork 這個方法
ThreadPool.QueueUserWorkItem(new WaitCallback(DoWork), i);
Thread.Sleep(1000);
}
}

//建立一個和 WaitCallback 相同簽名的方法
static void DoWork(object num)
{
Thread t = Thread.CurrentThread;

Console.WriteLine("[{2}] No.{0} Thread[{1}] Enter...", num, t.ManagedThreadId, Common.Tools.GetNow());
Thread.Sleep(10000);
Console.WriteLine("[{2}] No.{0} Thread[{1}] Complete...", num, t.ManagedThreadId, Common.Tools.GetNow());
}

//[16:37:42 7911] No.1 QueueUserWorkItem...
//[16:37:42 8011] No.1 Thread[6] Enter...
//[16:37:43 7999] No.2 QueueUserWorkItem...
//[16:37:43 7999] No.2 Thread[12] Enter...
//[16:37:44 7999] No.3 QueueUserWorkItem...
//[16:37:44 7999] No.3 Thread[13] Enter...
//[16:37:45 7999] No.4 QueueUserWorkItem...
//[16:37:45 7999] No.4 Thread[14] Enter...
//[16:37:46 8012] No.5 QueueUserWorkItem...
//[16:37:46 8024] No.5 Thread[15] Enter...
//[16:37:52 8013] No.1 Thread[6] Complete...
//[16:37:53 8000] No.2 Thread[12] Complete...
//[16:37:54 8001] No.3 Thread[13] Complete...
//[16:37:55 8001] No.4 Thread[14] Complete...
//[16:37:56 8026] No.5 Thread[15] Complete...
```

## 限定 ThreadPool 中的執行緒數量

一般情況下，集區中的執行緒數量都會自動設成最理想的數量，如果你的程式受這個數量影響而有所限制的話，你也可以使用 [ThreadPool.SetMaxThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.setmaxthreads.aspx) 或 [ThreadPool.SetMinThreads](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.setminthreads.aspx) 方法，自行變更這個限制值。  變更這個限制值，只會影響到目前的process。  下面二個情況是變更這個值常見的原因：thread starvation 、 startup thread speed。  
```c#
int threads;
int completionPorts;

// 取得目前 ThreadPool 的 thread數量。
ThreadPool.GetMaxThreads(out threads, out completionPorts);

ThreadPool.SetMaxThreads(10, 10);       //設定可同時作用的執行緒集區要求最大數目。

ThreadPool.SetMinThreads(5, 5);         //設定可同時作用的執行緒集區要求最小數目。
```

## ThreadPool and WaitHandle

[ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.aspx) 也提供一個機制，讓集區中的執行緒等待同步物件( [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex.aspx) 、 [Semaphore](http://msdn.microsoft.com/zh-tw/library/system.threading.semaphore.aspx) 、 Event )的控制碼，並且在 WaitHandles 收到通知時快速的回呼方法。  這是透過呼叫 [ThreadPool.RegisterWaitForSingleObject](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.registerwaitforsingleobject) 方法來達成。如下範例：  
```c#
public static RegisteredWaitHandle RegisterWaitForSingleObject(
WaitHandle waitObject,          //要登錄的 WaitHandle。
WaitOrTimerCallback callBack,   //通知 waitObject 參數時要呼叫的 WaitOrTimerCallback 委派。 
Object state,                   //傳送至委派的物件。 
TimeSpan timeout,               //等候逾時。如果 timeout=0，則函式會測試物件的狀態並立即傳回。如果 timeout=-1，則絕對不會耗用函式的逾時間隔。
bool executeOnlyOnce            //若為true，表示在呼叫委派之後，執行緒將不再等候 waitObject 參數
)

public delegate void WaitOrTimerCallback(
Object state,
bool timedOut
)
```
```c#
//timedOut:  true，表示 WaitHandle 逾時；false，表示收到信號。 
static void MutexHasFired(object state, bool timedOut)
{
if (timedOut)
Console.WriteLine("Mutex Timed out");
else
Console.WriteLine("Mutex got signaled");
}

private void button4_Click(object sender, EventArgs e)
{
// 建立一個 Mutex 類別 
Mutex mutex = new Mutex(true);

// Register for a notification
ThreadPool.RegisterWaitForSingleObject(
mutex, 
new WaitOrTimerCallback(MutexHasFired),
null, 
Timeout.Infinite, 
true);

// Signal the mutex RegisterWaitForSingleObjectto cause the thread to fire
mutex.ReleaseMutex();
}
```

## SynchronizationContext 物件

[SynchronizationContext](http://msdn.microsoft.com/zh-tw/library/system.threading.synchronizationcontext.aspx) 物件提供在各種同步處理模式中散佈同步處理內容的基本功能。

- [Current](http://msdn.microsoft.com/zh-tw/library/system.threading.synchronizationcontext.current.aspx) ：取得目前執行緒的同步處理內容。
- [Send](http://msdn.microsoft.com/zh-tw/library/system.threading.synchronizationcontext.send.aspx) ：將同步訊息分派至同步處理內容。
- [Post](http://msdn.microsoft.com/zh-tw/library/system.threading.synchronizationcontext.post.aspx) ：將非同步訊息分派至同步處理內容。
```c#
private void button5_Click(object sender, EventArgs e)
{
SynchronizationContext syncContent = SynchronizationContext.Current;
SendOrPostCallback syncDelegate = null;

Console.WriteLine("Before Send : {0}", Common.Tools.GetNow());
syncDelegate = DoWork1;
syncContent.Send(syncDelegate, null);
Console.WriteLine("After Send : {0}", Common.Tools.GetNow());

Console.WriteLine("Before Post : {0}", Common.Tools.GetNow());
syncDelegate = DoWork2;
syncContent.Post(syncDelegate, null);
Console.WriteLine("After Post : {0}", Common.Tools.GetNow());

//Before Send : 16:41:02 1146
//After Send :  16:41:05 1298       // Send 方法會等委派工作完畢才繼續往下執行
//Before Post : 16:41:05 1298
//After Post :  16:41:05 1298       // Post 方法呼叫委派後，就繼續往下執行
}
```

## ThreadPool 如何將結果回傳

與 Thread 相同的， ThreadPool 的作用也只是建立執行緒讓工作可以並行處理，所以它也沒有**回傳值**的概念。  若要取得並行作業的執行結果，一樣要使用到**同步處理機制**。  
```c#
public EventWaitHandle autoWaitHandle = new AutoResetEvent(false);

private class TaskInfo
{
public string result;
public int para1;
public int threadid;
}

public void DoWorkY(object obj)
{
ShowStartLog("DoWorkY", null);

TaskInfo ti = obj as TaskInfo;
Thread.Sleep(2000);
ti.result = GetNow();
ti.threadid = Thread.CurrentThread.ManagedThreadId;

autoWaitHandle.Set();  //通知主執行緒工作做完了.
}

private void bnAsyncResultUsingThreadPool_Click(object sender, EventArgs e)
{
SetStart();

for (int i = 0; i < 5; i++)
{
TaskInfo ti = new TaskInfo();
ti.para1 = i;
ThreadPool.QueueUserWorkItem(DoWorkY, ti);
autoWaitHandle.WaitOne();
Console.WriteLine(ti.result);
}

SetEnd();
}
```

上面例子中，我們利用一個 AutoResetEvent 來處理同步機制，透過它讓主程序知道並行作業已經結束，可以讀取執行的結果。  如果沒有這個 autoWaitHandle.WaitOne() 這一行，主程序會繼續往下走，可能讀取到不正確的 ti 值。

# 使用 Timer 物件

這個 [Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer.aspx) 指的是與執行緒相關的 [System.Threading.Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer) ，而不是 [System.Windows.Forms.Timer](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.timer) 物件。  它提供了一套機制，可以根據指定的時間，去引發一個非同步的呼叫以執行某個方法。  也就是可以透過 [Threading.Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer) 物件，設定排程，它會在時間到的時候，以背景模式去執行指定的工作。  若使用 Forms.Timer 去啟動一個工作，它會使用和 UI 同一個 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) ；而 [Threading.Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer) 會另開一個 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) ，以背景模式執行工作。  

#### 使用 [Threading.Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer.aspx) 步驟

- 將要在背景執行的程式碼，包裝在一個 void (object) 型態的函式。
- 以該函式名稱為參數，建立 [TimerCallback](http://msdn.microsoft.com/zh-tw/library/system.threading.timercallback)  委派。
- 以該委派為參數，建立 [Threading.Timer](http://msdn.microsoft.com/zh-tw/library/system.threading.timer) 執行個體，以啟動執行緒。
```c#
TimerCallback callback = new TimerCallback(DoWork_Para);
string data = "test";

// 使用 Threading.Timer 建立執行緒
System.Threading.Timer timer1 = new System.Threading.Timer(callback);

// 使用 Threading.Timer 建立執行緒，並傳遞參數給指定的工作。
System.Threading.Timer timer2 = new System.Threading.Timer(callback, data, 0, 1000);
```

# 使用 BackgroundWorker 物件

通常使用 [BackgroundWorker](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker) 的目的，大部份都是希望工作還在背景執行中的時候，能夠同時與使用者介面溝通，以便將背景工作的進度，即時反應到 UI 上。  它簡化了建立多執行緒作業的步驟，並透過事件來接聽作業的完成進度。  

#### [BackgroundWorker](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker) 方法

- [RunWorkerAsync](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.runworkerasync.aspx) ：開始執行背景作業。
- [ReportProgress](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.reportprogress.aspx) ：背景作業的完成百分比，從 0 到 100。
- [CancelAsync](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.cancelasync.aspx) ：取消暫止的背景作業。

#### [BackgroundWorker](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker) 事件

- [DoWork](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.dowork) ：當叫用 [RunWorkerAsync](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.runworkerasync) 方法時發生。
- [ProgressChanged](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.progresschanged) ：當叫用 [ReportProgress](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.reportprogress) 方法時發生。
- [RunWorkerCompleted](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.runworkercompleted) ：當工作完成、被取消或引發異常時發生。
```c#
/// <summary>
/// 執行耗時工作
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void _DoWork(object sender, DoWorkEventArgs e)
{
BackgroundWorker worker = sender as BackgroundWorker;
int runTimes = (int)e.Argument;

for (int i = 0; i < runTimes; i++)
{
Thread.Sleep(1000);
Console.WriteLine("{0},{1}", i, GetNow());
int percent = (i + 1) \* 100 / runTimes;
string state = string.Format("完成工作{0}", i+1);
worker.ReportProgress(percent, state); //報告進度
}
}

/// <summary>
/// 接聽進度
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void _ProgressChanged(object sender, ProgressChangedEventArgs e)
{
int progress = e.ProgressPercentage;
label1.Text = progress.ToString() + "%";
}

/// <summary>
/// 接聽完成事件
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void _RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
label1.Text = "Run Completed";
}

private void button12_Click(object sender, EventArgs e)
{
BackgroundWorker worker = new BackgroundWorker();

worker.WorkerReportsProgress = true;

worker.DoWork += _DoWork;
worker.ProgressChanged += _ProgressChanged;
worker.RunWorkerCompleted += _RunWorkerCompleted;

int runTimes = 10;
worker.RunWorkerAsync(runTimes);
}
```

- 若主執行緒要傳遞參數給 \_Dowork ，可以利用呼叫 [RunWorkerAsync](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.runworkerasync.aspx) 時傳送。
- 使用 [ReportProgress](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.backgroundworker.reportprogress.aspx) 報告進度時，除了傳送進度值，也可以利用第二個參數傳送一個 objct 型別的狀態值。
- 若想傳送資訊到 \_RunWorkerCompleted 事件中，可以在 \_Dowork 中，將資料指派給 e.Result 屬性，然後 \_RunWorkerCompleted 發生時，就可以由 e.Result 屬性取得。  ```c#
static void _DoWork(object sender, DoWorkEventArgs e)
{
//Do the work
//...

e.Result = new MyParams();
}
private void _RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
MyParams myParams  = e.Result as MyParams;

//Do the work
//...
}
```
## 參考資料  

- [Managed 執行緒處理](http://msdn.microsoft.com/zh-tw/library/3e8s7xdd.aspx)
- [同步處理多執行緒處理的資料](http://msdn.microsoft.com/zh-tw/library/z8chs7ft.aspx)
- [多執行緒程序的參數和傳回值 (C# 和 Visual Basic)](http://msdn.microsoft.com/zh-tw/library/wkays279.aspx)
- [ThreadPool 類別](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.aspx)
- [執行緒集區](https://msdn.microsoft.com/zh-tw/library/h4732ks0%28v=vs.100%29)
- [ThreadPool.QueueUserWorkItem 方法](http://msdn.microsoft.com/zh-tw/library/4yd16hza.aspx)
- [HOW TO：使用執行緒集區 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/3dasc8as.aspx)
- [HOW TO：執行緒 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/ms173178.aspx)