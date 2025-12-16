---
title: 資料同步問題
layout: default
parent: 執行緒
nav_order: 2
description: "資料同步問題"
date: 2012-02-01
tags: [DotNetFramework,執行緒]
postid: "3929015066306450258"
---
**同步處理機制**是設計 Multi-Thread 時必須特別注意的工作，主要的目的是要限制，當兩個或多個執行緒需要同時存取同一個共用資源時，一次只能有一個執行緒可以使用該資源。  為了避免其他執行緒同時存取，該資源將被獨佔鎖定，這樣的機制就稱為「同步處理機制」。  針對單一簡單的變數運算或者區塊的程式，.NET提供了多種不同的機制以達到同步鎖定( [Synchronization Locks](http://msdn.microsoft.com/zh-tw/library/ms228964.aspx) )的目的。  

# 避免碰撞 (Avoiding Collisions)

「Avoiding Collisions」翻成**避免碰撞**或者**避免衝突**，指的就是  在一個多執行緒同時運作的機制中，如何避免不同執行緒間，同時存取同一資料所造成的資料錯誤。  
```c#
Count = Count + 1;
```

在上面的這段程式碼，雖然只有一行，但是實際上，它需要執行下列步驟：  
```c#
(1).將變數值載入暫存器。
(2).遞增暫存器中的值。
(3).將暫存器中的值複製回記憶體。
```

假設有ＡＢ二個執行緒同時進這段任務，若Ａ執行緒執行完步驟(2)，準備進行步驟(3)的時候，Ｂ執行緒剛好也執行完步驟(2)，那麼就會導至整體的結果不正確。  所以說，這三個步驟，是不可以被切割的作業。那要如何解決這樣子的問題？  .Net 特別提供了 [Interlocked](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked) 類別，為多重執行緒共用的變數提供不可部分完成的作業 (Atomic Operation)。   

## 使用 Interlocked 類別，處理 Atomic 作業。

針對不同狀況， [Interlocked](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked) 類別提供以下方法，以執行 Atomic Operation：  

- [Add](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked.add) ：將兩個32或64位元整數加相，並以總和取代為第一個整數，成為不可部分完成的作業。
- [Decrement](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked.decrement) ：遞減特定變數並將結果儲存起來，成為不可部分完成的作業。
- [Increment](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked.increment) ：遞增特定變數並將結果儲存起來，成為不可部分完成的作業。
- [Exchange](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked.exchange) ：將某個值指定給某個變數。
- [Read](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked.read) ：傳回 64 位元的值 (載入為不可部分完成的作業)。
```c#
public class Counter
{
public static int Count;
}
static void UpdateCount()
{
for (int x = 1; x <= 10000; ++x)
{
//Counter.Count = Counter.Count + 1;
Interlocked.Increment(ref Counter.Count);   //使用 Interlocked.Increment 執行遞增，以避免競爭的情況。
//參數是一個 reference value
}
}
```
```c#
public void UpdateCount()
{
Interlocked.Increment(ref _count);
if (_count % 2 == 0) // An even number
{
Interlocked.Increment(ref _evenCount);
}
}
//使用 incrementing operations 後，雖然每行指令都是 thread-safe，但是合起來一起看卻沒有 thread-safe
```

上例簡單的方法就可以確保遞增作業是 atomic 。不過， [Interlocked](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked) 類別只適用在少數 .Net 的型別。  如果需要同步存取自訂型別或以不可分割方式處理較大的程式區段，就必須使用到同步鎖定來解決問題。

# 同步鎖定 (Synchronization Locks)

如果一個資源可以被多執行緒同時共享存取時，這時就必須考慮到「資料同步」的問題，資料在多執行緒下共享時，容易因為同時多個執行緒可能更新同一個物件的資訊，而造成物件資料的不同步，因為資料的不同步而可能引發的錯誤通常不易察覺，而且可能是在您程式執行了幾千幾萬次之後，才會發生錯誤。  所以 .NET 提供了幾種同步鎖定的機制，以處理資料同步的問題。  

## 使用 lock 處理同步鎖定

使用 [Interlocked](http://msdn.microsoft.com/zh-tw/library/system.threading.interlocked) 只能鎖定簡單的單行運算，利用 [lock](http://msdn.microsoft.com/zh-tw/library/c5kehkcz.aspx) 關鍵字可處理同步鎖定，它可以鎖定一整個區塊的程式碼。  當有某個執行緒已經在鎖定區內執行時，同步鎖定會封鎖其他執行緒存取該段程式碼的機會。  

lock &lt;font style="color:Red"&gt;cannot&lt;/font&gt; be used on &lt;font style="color:Red"&gt;value types&lt;/font&gt; such as integer, it can be used only on &lt;font style="color:Red"&gt;reference types.&lt;/font&gt;  
```c#
lock (this)
{
_count += 1;
if (_count % 2 == 0)
_evenCount += 1;
}
```
```c#
void DoWork_LockTest(object obj)
{
lock (this)
{
Console.WriteLine("{2} No.{0} Running-Thread[{1}]", obj, Thread.CurrentThread.ManagedThreadId, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
Thread.Sleep(1000);
Console.WriteLine("{2} No.{0} Complete-Thread[{1}]", obj, Thread.CurrentThread.ManagedThreadId, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
}
}

private void button13_Click(object sender, EventArgs e)
{
for (int i = 1; i <= 5; i++)        // Create and start five numbered threads. 
{
Thread t = new Thread(DoWork_LockTest);
t.Start(i);
Console.WriteLine("{1} No.{0} Start", i, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
Thread.Sleep(500);
}
}

//輸出結果: 由結果看的出來, 產生了5個Thread, 而且lock區間, 一次只允許一個線程進入
//16:00:14 6178 No.1 Start
//16:00:14 6334 No.1 Running-Thread[10]
//16:00:15 1482 No.2 Start
//16:00:15 6474 No.1 Complete-Thread[10]
//16:00:15 6474 No.2 Running-Thread[11]
//16:00:15 6630 No.3 Start
//16:00:16 1778 No.4 Start
//16:00:16 6614 No.2 Complete-Thread[11]
//16:00:16 6614 No.3 Running-Thread[12]
//16:00:16 6926 No.5 Start
//16:00:17 6754 No.3 Complete-Thread[12]
//16:00:17 6754 No.4 Running-Thread[13]
//16:00:18 6894 No.4 Complete-Thread[13]
//16:00:18 6894 No.5 Running-Thread[14]
//16:00:19 7034 No.5 Complete-Thread[14]  
```

## 使用 Monitor 處理同步鎖定

[Monitor](http://msdn.microsoft.com/zh-tw/library/x090d6tf.aspx) 是.Net提供的一套同步鎖定的機制，功能和 [lock](http://msdn.microsoft.com/zh-tw/library/c5kehkcz.aspx) 類似，只是以物件化的方式提供同步鎖定的功能。  底下列出該類別幾個重要的靜態方法：  

- [Enter](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.enter) ：在指定物件上建立獨佔的鎖定。
- [TryEnter](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.tryenter) ：嘗試在指定物件上建立獨佔的鎖定。
- [Exit](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.exit) ：釋出指定物件的獨佔鎖定。
- [Wait](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.wait) ：釋出物件的鎖並且封鎖目前的執行緒，直到這個執行緒重新取得鎖定為止。
- [Pulse](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.pulse) ：通知等候佇列中的執行緒，鎖定物件的狀態有所變更。(喚醒一個)

[Monitor](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor) 類別控制物件存取的方式是將物件的鎖定授與單一執行緒。  物件鎖定提供限制存取程式碼區塊的能力，通稱為關鍵區段 (Critical Section)。  執行緒擁有某物件鎖定的期間，其他執行緒都無法獲得那個鎖定。  除非其他的執行緒正在使用不同的鎖定物件來執行程式碼，  否則您也可以使用 [Monitor](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor) 來確保其他執行緒都無法存取由鎖定擁有者執行之應用程式碼的區段。  
```c#
//這種try...finally...是典型的同步處理架構
//
Monitor.Enter(this);        //建立獨佔的鎖定
try
{
_count += 1;
if (_count % 2 == 0)
_evenCount += 1;
}
finally
{
Monitor.Exit(this);     //釋出物件的鎖定
}
```
```c#
void DoWork_MonitorTest(object obj)
{
Monitor.Enter(this);        //建立獨佔的鎖定
try
{
Console.WriteLine("{2} No.{0} Running-Thread[{1}]", obj, Thread.CurrentThread.ManagedThreadId, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
Thread.Sleep(1000);
Console.WriteLine("{2} No.{0} Complete-Thread[{1}]", obj, Thread.CurrentThread.ManagedThreadId, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
}
finally
{
Monitor.Exit(this);     //釋出物件的鎖定
}

}

private void button15_Click(object sender, EventArgs e)
{
for (int i = 1; i <= 5; i++)        // Create and start five numbered threads. 
{
Thread t = new Thread(DoWork_MonitorTest);
t.Start(i);
Console.WriteLine("{1} No.{0} Start", i, string.Format("{0:HH:mm:ss ffff}", DateTime.Now));
}
}

//輸出結果: 由結果看的出來, 產生了5個Thread, 而且 Monitor 區間, 一次只允許一個線程進入
//16:12:08 5528 No.1 Start
//16:12:08 5528 No.2 Start
//16:12:08 5528 No.1 Running-Thread[15]
//16:12:08 5528 No.3 Start
//16:12:08 5528 No.4 Start
//16:12:08 5528 No.5 Start
//16:12:09 5668 No.1 Complete-Thread[15]
//16:12:09 5668 No.3 Running-Thread[17]
//16:12:10 5808 No.3 Complete-Thread[17]
//16:12:10 5808 No.4 Running-Thread[18]
//16:12:11 5948 No.4 Complete-Thread[18]
//16:12:11 5948 No.5 Running-Thread[19]
//16:12:12 6088 No.5 Complete-Thread[19]
//16:12:12 6088 No.2 Running-Thread[16]
//16:12:13 6228 No.2 Complete-Thread[16] 
```

Pulse 方法的範例，請參考[http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.pulse.aspx](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor.pulse.aspx)

# 其他同步方法

## ReaderWriterLock 類別

[ReaderWriterLock](http://msdn.microsoft.com/zh-tw/library/d1aawyz4.aspx) 類別可將讀取和寫入的獨占鎖定分開來管理，例如允許多個執行緒同時讀取，但僅鎖定一個獨佔的寫入執行緒。  如果大部份作業都在讀取資源，這個方式的效能會比 [Monitor](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor) 佳。下表是這個類別所提供的方法：  

- [AcquireReaderLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.acquirereaderlock) ：取得讀取器的鎖定。
- [ReleaseReaderLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.releasereaderlock) ：釋放讀取器的鎖定。
- [AcquireWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.acquirewriterlock) ：取得寫入器的鎖定。
- [ReleaseWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.releasewriterlock) ：釋放寫入器的鎖定。
- [UpgradeToWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.upgradetowriterlock) ：將讀取器鎖定升級至寫入器鎖定。
- [DowngradeFromWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.downgradefromwriterlock) ：將執行緒的鎖定狀態還原到還沒有呼叫 [UpgradeToWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.upgradetowriterlock) 之前的狀態。
- [ReleaseLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.releaselock) ：無論執行緒取得鎖定的次數為多少，都會釋放鎖定。
- [RestoreLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.restorelock) ：將執行緒的鎖定狀態還原到還沒有呼叫 [ReleaseLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.releaselock) 之前。

當有 thread 取得 ReaderLock 時，其他 thread 還是可以取得 ReaderLock。但是若其他 thread 想要取得 **WriterLock**，則**必須等所有已經取得 ReaderLock 的 thread 都釋放出 ReaderLock** 才行。  
```c#
ReaderWriterLock rwLock = new ReaderWriterLock();
int rwCounter = 0;

//============================================================================
// acquire a Reader lock
//============================================================================
private void Reader()
{
try
{
rwLock.AcquireReaderLock(Timeout.Infinite);      //AcquireReaderLock 取得讀取器的鎖定 (若超過100ns,會丟出例外)
try
{
Console.WriteLine("Counter:{0}  ThreadID:{1}  AcquireReader:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
Thread.Sleep(1000);
}
finally
{
rwLock.ReleaseReaderLock();     //釋放讀取器的鎖定
Console.WriteLine("Counter:{0}  ThreadID:{1}  ReleaseReader:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
}
catch (ApplicationException)
{
Console.WriteLine("Failed to get a ReaderLock.  Counter:{0}  ThreadID:{1}  Release:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
}

//============================================================================
// acquire a Writer lock
//============================================================================
private void Writer()
{
try
{
rwLock.AcquireWriterLock(Timeout.Infinite);      //AcquireReaderLock 取得寫入器的鎖定 (若超過1000ns,會丟出例外)
try
{
rwCounter++;
Console.WriteLine("Counter:{0}  ThreadID:{1}  AcquireWriter:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
Thread.Sleep(1000);

}
finally
{
rwLock.ReleaseWriterLock();     //釋放寫入器的鎖定
Console.WriteLine("Counter:{0}  ThreadID:{1}  ReleaseWriter:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
}
catch (ApplicationException)
{
Console.WriteLine("Failed to get a WriterLock.  Counter:{0}  ThreadID:{1}  Release:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
}
private void button5_Click(object sender, EventArgs e)
{
for (int i = 0; i < 10; i++)
{
Thread reader = new Thread(Reader);
reader.Start();

if ((i % 2) == 1)
{
Thread writer = new Thread(Writer);
writer.Start();
}
}

}
```

這個機制也支援將已取得 ReaderLock 的 thread ，升級成 WriterLock。不過也是要遵守上面原則，等所有讀取器都釋放資源。
```c#
if (rwCounter == 6)
{
//============================================================================
// UpgradeToWriterLock and DowngradeFromWriterLock 這二個方法必須一起使用
//============================================================================

LockCookie cookie = rwLock.UpgradeToWriterLock(Timeout.Infinite);   //升級到寫入鎖定，回傳 LockCookie 物件，
try
{
rwCounter++;
Console.WriteLine("Counter:{0}  ThreadID:{1}  UpgradeToWriterLock:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
catch (ApplicationException)
{
Console.WriteLine("Failed to get a UpgradeToWriterLock. ");
}
finally
{
rwLock.DowngradeFromWriterLock(ref cookie);             //利用上面那個 LockCookie 物件，將寫入鎖定降為讀取鎖定
Console.WriteLine("Counter:{0}  ThreadID:{1}  DowngradeFromWriterLock:{2}", rwCounter, Thread.CurrentThread.ManagedThreadId, Common.Tools.GetNowFFFF());
}
}
```

#### 注意事項 MSDN 記載：

&gt;   .NET Framework 有兩個讀取器-寫入器鎖定： [ReaderWriterLockSlim](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlockslim) 和 [ReaderWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.acquirereaderlock) 。   [ReaderWriterLockSlim](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlockslim) 建議用於所有新的程式開發。 [ReaderWriterLockSlim](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlockslim) 與 [ReaderWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.acquirereaderlock) 相似，  但前者採取簡化的遞迴規則及升級和降級鎖定狀態。 [ReaderWriterLockSlim](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlockslim) 會避免許多可能發生的死結狀況。  此外， [ReaderWriterLockSlim](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlockslim) 的效能大幅優於 [ReaderWriterLock](http://msdn.microsoft.com/zh-tw/library/system.threading.readerwriterlock.acquirereaderlock) 。

## WaitHandle 類別

[WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 是個抽象類別，它封裝了作業系統層級用來鎖定物件的機制。  接下來的四種機制都是屬於 Windows Kernel Object，衍生自 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) ，可以用來處理跨不同 AppDomain 或 process 的鎖定。  下表是 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 的幾個重要方法或屬性：  

- [WaitOne](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle.waitone) ：封鎖目前的執行緒，直到目前的 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 收到信號為止。  
如果目前的執行個體有收到信號，則為 true。如果目前的執行個體一直沒有收到訊號提示，WaitOne 就一定不會傳回。
- [WaitAll](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle.waitall) ：等候指定陣列中的所有元素都收到信號。
- [Close](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle.close) ：將目前 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 所持有的所有資源全部釋出。

## Mutex 類別

[Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 是一種基本的同步處理，可對只有一個執行緒之共用資源進行獨佔式存取。  如果有一個執行緒取得 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) ，其他想取得 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 的執行緒就會被 block 住，直到第一個執行緒釋出 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 為止。  

- 繼承抽象類別 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 。
- 
- [Mutex](http://msdn.microsoft.com/zh-tw/library/01985e8f.aspx) 類別，類似 [Monitor](http://msdn.microsoft.com/zh-tw/library/system.threading.monitor) 類別，但可以跨不同 AppDomain 或 process 鎖定。
- [Mutex.WaitOne](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex.waitone.aspx)：封鎖目前的執行緒，直到目前的 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.waithandle) 收到信號為止。
- [Mutex.ReleaseMutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex.releasemutex)：釋出 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 一次。
- [OpenExisting](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex.openexisting) ：開啟現有的具名 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 。如果系統 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 不存在，則此方法會擲回例外狀況，而不會建立系統物件。
```c#
//1. Create an instance of the Mutex class to be shared across any threads:
//2. Inside a new thread, create an if statement calling the Mutex class's WaitOne method to wait until the lock is available:
//3. Create a try/finally block inside the if statement block.
//4. Inside the try portion of the try/finally block, do the work you need to do while having exclusive access to the Mutex object.
//5. release the Mutex by calling Mutex.ReleaseMutex:
//6. Optionally, create an else block after the if statement block to deal with not getting the lock:

private static Mutex gMutex;
static void MutexWorker(object obj)
{
Thread t = Thread.CurrentThread;
Console.WriteLine("[{2}] No.{0} Thread[{1}] WaitOne", num, t.ManagedThreadId, Common.Tools.GetNow());
gMutex.WaitOne();                 // WaitOne 等待鎖定
try
{
Console.WriteLine("[{2}] No.{0} Thread[{1}] Enter Mutex", num, t.ManagedThreadId, Common.Tools.GetNow());
Thread.Sleep(2000);
}
finally
{
Console.WriteLine("[{2}] No.{0} Thread[{1}] Release Mutex", num, t.ManagedThreadId, Common.Tools.GetNow());
gMutex.ReleaseMutex();       // ReleaseMutex 釋放鎖定
}
}

private void button17_Click(object sender, EventArgs e)
{
gMutex = new Mutex();
for (int i = 0; i < 5; i++)
{
Thread t = new Thread(MutexWorker);
t.Start(i);
Console.WriteLine("No.{0}  Thread[{1}] Start-{2} ", i, t.ManagedThreadId, Common.Tools.GetNow());
}
}

//[12:37:37 9928] No.0 Thread[10] Thread Started
//[12:37:38 0003] No.1 Thread[11] Thread Started
//[12:37:38 0040] No.0 Thread[10] WaitOne
//[12:37:38 0040] No.0 Thread[10] Enter Mutex          --> 第１個 Enter
//[12:37:38 0053] No.2 Thread[12] Thread Started
//[12:37:38 0065] No.1 Thread[11] WaitOne
//[12:37:38 0065] No.3 Thread[13] Thread Started
//[12:37:38 0090] No.2 Thread[12] WaitOne
//[12:37:38 0090] No.4 Thread[14] Thread Started
//[12:37:38 0115] No.3 Thread[13] WaitOne
//[12:37:38 0115] No.4 Thread[14] WaitOne
//[12:37:40 0040] No.0 Thread[10] Release Mutex        --> 第１個 Release 前，都不能 Enter
//[12:37:40 0040] No.1 Thread[11] Enter Mutex          --> 第２個 Enter
//[12:37:42 0046] No.1 Thread[11] Release Mutex
//[12:37:42 0046] No.2 Thread[12] Enter Mutex          --> 第３個 Enter
//[12:37:44 0046] No.2 Thread[12] Release Mutex
//[12:37:44 0046] No.3 Thread[13] Enter Mutex          --> 第４個 Enter
//[12:37:46 0049] No.3 Thread[13] Release Mutex
//[12:37:46 0049] No.4 Thread[14] Enter Mutex          --> 第５個 Enter
//[12:37:48 0049] No.4 Thread[14] Release Mutex
```

若要跨 AppDomain / process 使用 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) ，則必須使用具名的 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 建立物件。   [OpenExisting](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex.openexisting) 方法會嘗試開啟現有的具名 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 。如果系統 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 不存在，則此方法會擲回例外狀況，而不會建立系統物件。  
```c#
Mutex theMutex = null;
try 
{
theMutex = Mutex.OpenExisting("AAAAA");   //判斷任何A ppDomain 中，是否有 AAAAA 鎖定
}
catch (WaitHandleCannotBeOpenedException)
{
// Cannot open the mutex because it doesn't exist
}
// Create it if it doesn't exist
if (theMutex == null)
{
theMutex = new Mutex(false, "AAAAA");
}
```

## Semaphore 類別

- 繼承抽象類別 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/9f7e54k1.aspx) 。
- [Semaphore 類別](http://msdn.microsoft.com/zh-tw/library/kt3k0k2h.aspx)，類似 [Mutex](http://msdn.microsoft.com/zh-tw/library/system.threading.mutex) 類別。
- 可以跨不同 AppDomain 或 process 鎖定。
- 可以指定並行項目的最大數目(maximum number of concurrent entries)，簡單講就是可以限定同時進入存取關鍵區段(Critical Section)的數量。

每當執行緒進入號誌時( [WaitOne](http://msdn.microsoft.com/zh-tw/library/system.threading.semaphore.waitone) )，號誌上的計數會遞減，而在執行緒釋放號誌時( [Release](http://msdn.microsoft.com/zh-tw/library/system.threading.semaphore.release) )，計數會遞增。  當計數為零時，後續的要求會封鎖，直到其他執行緒釋放號誌為止。  當所有執行緒都釋放號誌時，計數會達到之前建立號誌時所指定的最大值。  
```c#
private static Semaphore _pool;     // A semaphore that simulates a limited resource pool.
private static int _padding;        // A padding interval to make the output more orderly.
private static void SemaphoreWorker(object num)
{
Thread t = Thread.CurrentThread;
Console.WriteLine("[{2}] No.{0} Thread[{1}] WaitOne", num, t.ManagedThreadId, Common.Tools.GetNow());
_pool.WaitOne();                            //封鎖目前的執行緒，直到目前的 WaitHandle 收到信號為止。

Console.WriteLine("[{2}] No.{0} Thread[{1}] Enter Semaphore", num, t.ManagedThreadId, Common.Tools.GetNow());

int padding = Interlocked.Add(ref _padding, 100);   // A padding interval to make the output more orderly.
Thread.Sleep(1000 + padding);

Console.WriteLine("[{2}] No.{0} Thread[{1}]  Release the Semaphore, remain semaphore count: {3}", num, t.ManagedThreadId, Common.Tools.GetNow(), _pool.Release() + 1);  
}

private void button9_Click(object sender, EventArgs e)
{
_pool = new Semaphore(0, 3);        // 建立一個Semaphore物件，可接受的數目，初始值=0，上限=3
for (int i = 1; i <= 5; i++)        
{
Thread t = new Thread(SemaphoreWorker);
Console.WriteLine("[{2}] No.{0} Thread[{1}] Thread Started", i, t.ManagedThreadId, Common.Tools.GetNow());
t.Start(i);
}

Thread.Sleep(500);    //等待半秒，讓所有thread都開始，但被 Semaphore block

// 主程式一開始時就握住了全部的semaphore count， 現在釋出3個，讓等待中的thread可以進入semaphore
Console.WriteLine("Main thread calls Release(3)");
_pool.Release(3);
}

//[12:21:53 3218] No.1 Thread[6] Thread Started
//[12:21:53 3231] No.2 Thread[16] Thread Started
//[12:21:53 3243] No.1 Thread[6] WaitOne
//[12:21:53 3256] No.2 Thread[16] WaitOne
//[12:21:53 3268] No.3 Thread[17] Thread Started
//[12:21:53 3281] No.3 Thread[17] WaitOne
//[12:21:53 3281] No.4 Thread[18] Thread Started
//[12:21:53 3293] No.4 Thread[18] WaitOne
//[12:21:53 3293] No.5 Thread[19] Thread Started
//[12:21:53 3306] No.5 Thread[19] WaitOne
//Main thread calls Release(3)                                                          --> 釋放３個，剩餘數３
//[12:21:53 8306] No.5 Thread[19] Enter Semaphore                                       --> 第１個進去
//[12:21:53 8306] No.1 Thread[6] Enter Semaphore                                        --> 第２個進去
//[12:21:53 8306] No.4 Thread[18] Enter Semaphore                                       --> 第３個進去，剩餘數０
//[12:21:55 4306] No.5 Thread[19]  Release the Semaphore, remain semaphore count: 1     --> 第１個結束後，呼叫 Release，剩餘數１
//[12:21:55 4306] No.3 Thread[17] Enter Semaphore                                       --> 第４個進去，剩餘數０
//[12:21:55 5306] No.1 Thread[6]  Release the Semaphore, remain semaphore count: 1      --> 第２個結束後，呼叫 Release，剩餘數１
//[12:21:55 5306] No.2 Thread[16] Enter Semaphore                                       --> 第５個進去
//[12:21:55 6306] No.4 Thread[18]  Release the Semaphore, remain semaphore count: 1     --> 第３個結束後，呼叫 Release，剩餘數１
//[12:21:57 3306] No.3 Thread[17]  Release the Semaphore, remain semaphore count: 2     --> 第４個結束後，呼叫 Release，剩餘數２
//[12:21:57 5306] No.2 Thread[16]  Release the Semaphore, remain semaphore count: 3     --> 第４個結束後，呼叫 Release，剩餘數３
```

## EventWaitHandle 類別

- [EventWaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.eventwaithandle) 分成「[AutoResetEvent](http://msdn.microsoft.com/zh-tw/library/45zkwyy6.aspx)」和「[ManualResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.aspx)」二種做法。
- 這二個類別都是繼承自 [EventWaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.eventwaithandle.aspx)，而 [EventWaitHandle](http://msdn.microsoft.com/zh-tw/library/system.threading.eventwaithandle) 則是繼承抽象類別 [WaitHandle](http://msdn.microsoft.com/zh-tw/library/9f7e54k1.aspx)
- 可以跨不同 AppDomain 或 process 鎖定。

### AutoResetEvent 類別

- 當叫用 [AutoResetEvent.WaitOne](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.waitone.aspx) 方法時，該執行緒會進入 WaitSleepJoin 狀態，執行區塊會被封鎖，開始等候解除封鎖的信號，此時執行區塊狀態為 nonsignaled。
- 當叫用 [AutoResetEvent.Set](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.set.aspx) 方法時，該執行緒會被解除封鎖回復到 Running 狀態，此時執行區塊狀態為 signaled。
- 當狀態為 signaled 時，系統會允許一個等候的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 進入封鎖區塊，並**自動將狀態回復成未收到信號(nonsignaled)**。
```c#
//==================================================================
// 這個範例示範 AutoResetEvent
// 當 autoEvent.Set(); 被呼叫後，系統只允許一個Thread進入block區塊
//==================================================================
private static AutoResetEvent autoEvent = new AutoResetEvent(false);    //建立一個AutoResetEvent，初始狀態設定為未收到信號
private static void autoEventWork()
{
Console.WriteLine("Thread{0}: Call WatiOne()", Thread.CurrentThread.ManagedThreadId);

autoEvent.WaitOne();  //等候通知
Thread.Sleep(1000);

Console.WriteLine("Thread{0}:{1}", Thread.CurrentThread.ManagedThreadId, Thread.CurrentThread.ThreadState);
}
//建立5個Thread
private void button11_Click(object sender, EventArgs e)
{
for (int i = 1; i <= 5; i++)        // Create and start five numbered threads. 
{
Thread t = new Thread(new ThreadStart(autoEventWork));
t.Start();                     // Start the thread, passing the number.
}
Thread.Sleep(500); 
}
//這個button用來引發Set()
private void button12_Click(object sender, EventArgs e)
{
autoEvent.Set();    //將事件的狀態設定為 signaled，讓一個或多個等候執行緒繼續執行。
}

//===================================================
//輸出結果
//===================================================
//Thread10: Call WatiOne()    //產生5個thread
//Thread12: Call WatiOne()
//Thread11: Call WatiOne()
//Thread13: Call WatiOne()
//Thread14: Call WatiOne()
//exec autoEvent.Set()        //呼叫一次Set()
//Thread13:Running            //因為狀態會自動Reset了,所以只讓一個Thread執行, 

//exec autoEvent.Set()        //每呼叫一次Set(),都只讓一個Thread執行
//Thread12:Running            

//exec autoEvent.Set()        
//Thread10:Running 
```

### ManualResetEvent 類別

[ManualResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.aspx) 類別操作方法大至與 [AutoResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.aspx) 相同，最主要的差別在叫用 [ManualResetEvent.Set](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.set.aspx) 方法後必須再呼叫 [ManualResetEvent.Reset](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.reset.aspx) 方法，執行緒才會處於未收到信號的狀態。  

- 當叫用 [ManualResetEvent.WaitOne](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.waitone.aspx) 方法時，該執行緒會進入 WaitSleepJoin 狀態，執行區塊會被封鎖，開始等候解除封鎖的信號，此時執行區塊狀態為 nonsignaled。
- 當叫用 [ManualResetEvent.Set](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.set.aspx) 方法時，該執行緒會被解除封鎖回復到 Running 狀態，此時執行區塊狀態為 signaled。
- 當狀態為 signaled 時，系統會允許所有等候的 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread) 進入封鎖區塊，並**自動將狀態回復成未收到信號(nonsignaled)**。
- 直到收到 [Reset](http://msdn.microsoft.com/zh-tw/library/system.threading.eventwaithandle.reset) 訊號， [ManualResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent) 才會將狀態變成未收到信號(nonsignaled)。
```c#
//==================================================================
// 這個範例示範ManualResetEvent
// 當 manualEvent.Set(); manualEvent.Reset(); 都被呼叫後，所有Thread都將進入block區塊
//==================================================================
private static ManualResetEvent manualEvent = new ManualResetEvent(false);  //建立一個ManualResetEvent，初始狀態設定為未收到信號
private static void menuEventWork()
{
Console.WriteLine("Thread{0}: Call WatiOne()", Thread.CurrentThread.ManagedThreadId);

manualEvent.WaitOne();  //等候通知 
Thread.Sleep(1000);

Console.WriteLine("Thread{0}:{1}", Thread.CurrentThread.ManagedThreadId, Thread.CurrentThread.ThreadState);
}
//建立5個Thread
private void button14_Click(object sender, EventArgs e)
{
for (int i = 1; i <= 5; i++)        // Create and start five numbered threads. 
{
Thread t = new Thread(new ThreadStart(menuEventWork));
t.Start();                     // Start the thread, passing the number.
}
Thread.Sleep(500); 
}
//這個button用來引發Set()
private void btn_Set_Click(object sender, EventArgs e)
{
manualEvent.Set();
}
//這個button用來引發Reset()
private void btn_ReSet_Click(object sender, EventArgs e)
{
manualEvent.Reset();
}

//===================================================
//輸出結果
//===================================================
//Thread03: Call WatiOne()     //產生5個thread
//Thread11: Call WatiOne()
//Thread12: Call WatiOne()
//Thread13: Call WatiOne()
//Thread14: Call WatiOne()    //因為狀態是Off,所以都被block住

//exec manualEvent.Set()      //呼叫一次Set(),變更狀態為On
//                            //因為狀態不會自動被Off
//Thread03:Running            //所以等待中的 thread 一個個都執行了
//Thread12:Running
//Thread14:Running
//Thread11:Running
//Thread13:Running

//Thread15: Call WatiOne()    //此時再產生5個thread
//Thread16: Call WatiOne()
//Thread17: Call WatiOne()
//Thread18: Call WatiOne()
//Thread19: Call WatiOne()
//Thread15:Running            //因為狀態還是On,所以也都執行了
//Thread18:Running
//Thread17:Running
//Thread16:Running
//Thread19:Running

//exec manualEvent.Reset()    //呼叫一次ReSet(),變更狀態為Off

//Thread08: Call WatiOne()    //再產生5個thread
//Thread20: Call WatiOne()
//Thread21: Call WatiOne()
//Thread22: Call WatiOne()
//Thread23: Call WatiOne()    //因為狀態是Off,所以都被block住

//exec manualEvent.Set()      //呼叫一次Set(),變更狀態為On
//Thread22:Running            //所以等待中的 thread 再次全部都執行了
//Thread23:Running
//Thread21:Running
//Thread08:Running
//Thread20:Running
```

#### 總結：

- [Set](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.set.aspx) 方法會將執行緒區塊的狀態變成 On，以便讓等候中的執行緒通過。
- [Reset](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.reset.aspx) 方法會將執行緒區塊的狀態變成 Off，以封鎖等候中的執行緒。
- 若使用 [AutoResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.aspx) 物件，在呼叫 [Set](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.set.aspx) 之後，會讓第一個等候中的執行緒通過，然後自動將狀態重設成 Off ，後面等待中的執行緒就無法通過，必須繼續等待。
- 若使用 [ManualResetEvent](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.aspx) 物件，在呼叫 [Set](http://msdn.microsoft.com/zh-tw/library/system.threading.autoresetevent.set.aspx) 之後，因為狀態不會自動回復，會一直維持在 On ，所以等候中的執行緒就會一一通過。  必須等到 [Reset](http://msdn.microsoft.com/zh-tw/library/system.threading.manualresetevent.reset.aspx) 被叫用後，其狀態值才會變成 Off ，尚在等候中的執行緒就會繼續被封鎖。

# 同步方法總結
```c#
//===========================================================================================
// lock
//===========================================================================================
lock (this) 
{
//...
}

//===========================================================================================
// Monitor
//===========================================================================================
Monitor.Enter(this);        //建立鎖定
try
{
//...
}
finally
{
Monitor.Exit(this);     //釋出鎖定
}

//===========================================================================================
// ReaderWriterLock
// 將讀寫操作分開管理，允許多個執行緒同時讀取，但僅鎖定一個獨佔的寫入執行緒
//===========================================================================================
ReaderWriterLock readwriteLock = new ReaderWriterLock();

readwriteLock.AcquireReaderLock(100);          //建立 讀取器 鎖定 (若超過100ns,會丟出例外)
try
{
//...
}
finally
{
readwriteLock.ReleaseReaderLock();          //釋出 讀取器 鎖定
}

readwriteLock.AcquireWriterLock(1000);          //建立 寫入器 鎖定 (若超過100ns,會丟出例外)
try
{
//...
}
finally
{
readwriteLock.ReleaseWriterLock();          //釋出 寫入器 鎖定
}

//===========================================================================================
// Mutex
// 可以跨不同 AppDomain 
//===========================================================================================
Mutex mutex = new Mutex();
mutex.WaitOne();                        // WaitOne 建立鎖定
try
{
//...
}
finally
{
mutex.ReleaseMutex();               // ReleaseMutex 釋放鎖定
}

//===========================================================================================
// Semaphore
// 可以跨不同 AppDomain 
// 可以設定同時存取的數量
//===========================================================================================
// 可接受的數目 上限=3，起始值=0    
Semaphore semaphore = new Semaphore(0, 3);      
semaphore.WaitOne();                    // WaitOne 建立鎖定
try
{
//...
}
finally
{
semaphore.Release();                // Release 釋放鎖定
}

//===========================================================================================
// AutoResetEvent
// 進入 WaitOne 之後，狀態自動 Reset 成 Off
//===========================================================================================
//建立一個AutoResetEvent，初始狀態設定為未收到信號
AutoResetEvent autoEvent = new AutoResetEvent(false);   
autoEvent.WaitOne();                                            //等候通知

//當收到 Set 訊號，狀態為 signaled
//此時系統允許一個等候的 Thread 進入封鎖區塊
//進入封鎖區塊後，狀態自動變回 nonsignaled

//===========================================================================================
// ManualResetEvent
// 進入 WaitOne 之後，狀態不變，直到程式碼呼叫 Reset ，狀態才會變成 Off
//===========================================================================================
//建立一個 ManualResetEvent，初始狀態設定為未收到信號
ManualResetEvent manualEvent = new ManualResetEvent(false);     
manualEvent.WaitOne();                                          //等候通知

//當收到 Set 訊號，狀態為 signaled
//此時系統允許所有等候中的 Thread 進入封鎖區塊
//進入封鎖區塊後，狀態不會變

//必須等到收到 Reset 訊號，狀態才會變成 nonsignaled
//等候中的 Thread 就不能再進入封鎖區塊
```

# Deadlocker

兩段程式碼，因需要的資源互相被鎖定，以至無法完成任務，如下列程式碼，
```c#
class Deadlocker
{
object ResourceA = new Object();
object ResourceB = new Object();
public void First()
{
lock (ResourceA)
{
lock (ResourceB) //因為Start2鎖住ResourceB,必須等到Start2釋放資源才進的去
{
//Added sleep to give enough time for B to launch before 
Thread.Sleep(10);
Console.WriteLine("First");  //因為互相lock住,所以進不來
}
}
}
public void Second()
{
lock (ResourceB)
{
lock (ResourceA) //因為Start1鎖住ResourceA,必須等到Start1釋放資源才進的去
{
//Added sleep to give enough time for A to launch before 
Thread.Sleep(10);
Console.WriteLine("Second");  //因為互相lock住,所以進不來
}
}
}
}
private void button7_Click(object sender, EventArgs e)
{
Deadlocker deadlock = new Deadlocker();
ThreadStart firstStart = new ThreadStart(deadlock.First);
ThreadStart secondStart = new ThreadStart(deadlock.Second);

Thread first = new Thread(firstStart);
Thread second = new Thread(secondStart);

first.Start();
second.Start();

first.Join();
second.Join();
}
```
## 參考資料  

- [同步處理原始物件概觀](http://msdn.microsoft.com/zh-tw/library/ms228964.aspx)
- [執行緒同步處理](http://msdn.microsoft.com/zh-tw/library/ms173179.aspx)