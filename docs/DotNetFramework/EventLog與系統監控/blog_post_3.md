---
title: Debug and Trace
layout: default
parent: EventLog與系統監控
nav_order: 3
description: "Debug and Trace"
date: 2012-02-15
tags: [DotNetFramework,EventLog與系統監控]
postid: "1900249393531115801"
---
# Debugging and Tracing

## Debugger 類別

[Debugger](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debugger.aspx) 類別讓應用程式可以和偵錯程式溝通。底下是Debugger常使用的方法：
```c#
Random r = new Random();   
int x = r.Next( 1, 10);
if (x > 5)
{
Console.WriteLine("x > 5");
Debugger.Break();       //這一行程式碼的目的，相當於在此設立中斷點
}
Debugger.Log(1, "Test1", "This is a test");   //Log：送出一個訊息到預設的輸出(接聽物件)
```

## Debug 和 Trace 類別

[Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 類別是 [Debugger](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debugger.aspx) 的加強版，提供更多的方法。  [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx)、[Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 這二個類別都用來偵錯程式碼的類別。  它們的差別僅僅在建置的時候，若使用 Release 模式，會忽略 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 類別，僅保留 [Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 類別的程式碼。  

底下是 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 類別常用的方法：  

- [Assert](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.assert.aspx) ：如果條件為 false，才輸出呼叫堆疊的訊息方塊。
- [Fail](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.fail.aspx) ：發出錯誤訊息。
- [Print](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.print.aspx) ：將訊息資訊輸出。
- [Write](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.assert.aspx) ：將訊息資訊輸出。
- [WriteIf](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.writeif.aspx) ：如果條件為 true，將訊息資訊輸出。
- [WriteLine](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.writeline.aspx) ：將訊息資訊輸出。
- [WriteLineIf](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.writelineif.aspx) ：如果條件為 true，將訊息資訊輸出。
- [Indent](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.indent.aspx) ：將目前的 IndentLevel 增加一級。
- [Unindent](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.unindent.aspx) ：將目前的 IndentLevel 減少一級。
- [Flush](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.flush.aspx) ：將輸出資料送到 Listener 物件去。
```c#
Debug.WriteLine("Debug Message"); //Debug 模式才會輸出訊息
Trace.WriteLine("Trace Message"); //Debug 或 Release 模式都會輸出訊息

for (int i = 0; i < 5; i++)
{
Debug.Assert( (i!=3) , "i=3");      //假如條件為false，才輸出訊息方塊
}

for (int i = 0; i < 5; i++)
{
if (i == 3)
{
Debug.Fail("ＯＯＸＸ");         //輸出訊息方塊
}
}

for (int i = 0; i < 5; i++)
{
Debug.WriteIf( (i < 2) , string.Format("i={0} , less then 2", i) );   //假如條件成立，才輸出訊息到輸出視窗
}
```
```c#
public static void Assert(bool condition);                          //if false , alert messagebox
public static void Assert(bool condition, string message);          //if false , alert messagebox(message)
public static void Assert(bool condition, string message, string detailMessage);  //if false , alert messagebox(message + detailMessage)
public static void Assert(bool condition, string message, string detailMessageFormat, params object[] args);

public static void WriteLineIf(bool condition, object value);                   //if true output value.tostring
public static void WriteLineIf(bool condition, string message);                 //if true output message
public static void WriteLineIf(bool condition, object value, string category);  //if true output value.tostring + category
public static void WriteLineIf(bool condition, string message, string category);//if true output message + category
```

# 追蹤接聽項 (Trace Listener)

## 什麼是接聽項物件

以上的輸出結果，我們都可以在 Visual Studio 的輸出視窗中看到。  但是，如果執行的是.exe 的程式，或者是一段沒辨法使用單步執行指令的時候，要怎麼觀查呢？  這時候我們就可以利用不同的「接聽項 (Listener)」，將訊息導向其他地方，以進行觀查分析。  

像上面的例子， [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 物件實際的操作方法是，利用一個接聽項(listener)，來接收 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 物件丟出的訊息，再將訊息輸出到指定的地方。  倘若沒有特別指定這個接聽項，就會使用預設的接聽項( [ConsoleTraceListener](http://msdn.microsoft.com/zh-tw/library/f5tkwdf2.aspx) )。  這個接聽項就會把訊息輸出到 Visual Studio 的 IDE 環境中的 [輸出] 視窗  

「接聽項」的用途是用來收集、儲存和傳送追蹤訊息。接聽項會將追蹤輸出，導向至適當的目標，例如記錄檔、視窗或文字檔。  不同的接聽項，會將訊息內容做不一樣的導向輸出。  

底下這個範例，我們附加一個 [TextWriterTraceListener](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.textwritertracelistener.aspx) 接聽項到 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 物件中，讓它將訊息輸出到指定的檔案。  
```c#
Debug.Listeners.Clear();    //清除所有接聽項
Debug.Listeners.Add(new TextWriterTraceListener("Debug_log.txt", "myListener"));    //指定 TextWriterTraceListener 接聽項
Debug.AutoFlush = true;
Debug.WriteLine("Test message. 1");
Trace.WriteLine("Test message. 2");

// Debug_log.txt
Test message. 1  //若建置為Release模式，就不會有這一行
Test message. 2
```

## 設定接聽項物件

[Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 物件的 [Listeners](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.listeners.aspx) 屬性是一個集合，所以是可以同時指定多個不同接聽項的。  以下物件都可以檔成 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 、 [Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 和 [TraceSource](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.tracesource.aspx) 類別的接聽項。  這些接聽項都是繼承自 [TraceListener](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.tracelistener.aspx) 這個抽象基底類別。  

- [DefaultTraceListener](http://msdn.microsoft.com/zh-tw/library/8fdtceh6.aspx) ：會將追蹤或偵錯輸出至 Visual Studio 的 [輸出] 視窗。是 [Debug](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.debug.aspx) 和 [Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 訊息的預設行為。
- [ConsoleTraceListener](http://msdn.microsoft.com/zh-tw/library/f5tkwdf2.aspx) ：會將追蹤或偵錯輸出導向至標準輸出或標準錯誤資料流。
- [EventLogTraceListener](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlogtracelistener.aspx) ：會將追蹤或偵錯輸出重新導向至事件記錄檔。
- [TextWriterTraceListener](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.textwritertracelistener.aspx) ：會將追蹤或偵錯輸出重新導向至 TextWriter 類別的執行個體，或重新導向至任何 Stream 類別。
- [DelimitedListTraceListener](http://msdn.microsoft.com/zh-tw/library/8yacsy65.aspx) ：輸出成有分隔符號格式的文字檔。
- [XmlWriterTraceListener](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.xmlwritertracelistener.aspx) ：輸出成XML格式的文字檔

#### EventLogTraceListener
```c#
//以 test application 為 EventSource , 建立一個 EventLogTraceListener
EventLogTraceListener myListener = new EventLogTraceListener("test application");

//輸出至 事件記錄檔, 來源為 test application
Trace.Listeners.Add(myListener);  
Trace.AutoFlush = true;
Trace.WriteLine("Test message 1");
Trace.WriteLine("Test message 2");
```

#### TextWriterTraceListener
```c#
Trace.Listeners.Add(new TextWriterTraceListener("TextWriterOutput.log", "myListener"));
Trace.TraceInformation("Before writing to console");    //將訊息寫入追蹤接聽程式。
Trace.TraceInformation("Test message.");
Console.WriteLine("Test message");
Trace.TraceInformation("After writing to console");     
Trace.Flush();
```

#### DelimitedListTraceListener
```c#
Trace.Listeners.Add(new DelimitedListTraceListener("DelimitedListTraceListener.log", "myListener"));
Trace.TraceInformation("Before writing to console");    //將訊息寫入追蹤接聽程式。
Trace.TraceInformation("Test message.");
Console.WriteLine("Test message");
Trace.TraceInformation("After writing to console");
Trace.Flush();
```

#### XmlWriterTraceListener
```c#
Trace.Listeners.Add(new XmlWriterTraceListener("XmlWriterTraceListener.log", "myListener"));
Trace.TraceInformation("Before writing to console");    //將訊息寫入追蹤接聽程式。
Trace.TraceInformation("Test message.");
Console.WriteLine("Test message");
Trace.TraceInformation("After writing to console");
Trace.Flush();
```

## 利用組態檔設定追蹤

上面介紹的追蹤功能，我們都是使用程式碼來設定追蹤，相同的功能，我們也可以使用組態檔來管理。
```xml
<configuration>
<system.diagnostics>

<sources>
<source name="tsAPP1" switchName="tsAPP1.Switch" switchType="System.Diagnostics.SourceSwitch">
<listeners>
<add name="myListener"></add>
</listeners>
</source>
</sources>

<switches>
<add name="tsAPP1.Switch" value="Verbose"></add>
</switches>

<sharedListeners>
<add name="myListener"
type="System.Diagnostics.TextWriterTraceListener"
initializeData="SourceSwitch_log.txt"
traceOutputOptions="ProcessId, ThreadId, DateTime"></add>
</sharedListeners>

</system.diagnostics>
</configuration>
```
```c#
TraceSource traceSource = new TraceSource("tsAPP1");

traceSource.TraceData(TraceEventType.Error, 5, "Message 1");  //寫入追蹤資料，含(事件型別、事件辨識項、訊息資料)
traceSource.TraceInformation("Before writing to console");    //寫入追蹤告知性訊息。
Console.WriteLine("Test message.");
traceSource.TraceInformation("After writing to console");
traceSource.Flush();
```

## CorrelationManager 類別

The [CorrelationManager](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.correlationmanager) class provides an easy-to-use mechanism to keep the requests isolated and unique.  

提供一個從單一邏輯作業產生的追蹤可以用專屬作業的獨一無二識別加上標記，以便與另外一個邏輯作業的追蹤區別。

# 其他追蹤類別

## TraceSource 類別

[TraceSource](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.tracesource.aspx) 類別，提供機制讓應用程式可以追蹤程式碼的執行情形，類似Debug或Trace，只是功能再加強，可以追蹤更詳細的訊息內容。
```c#
// 建立一個追蹤來源
TraceSource traceSource = new TraceSource("myTrace", SourceLevels.Verbose);

// 設定追蹤來源的接聽項
traceSource.Listeners.Clear();
traceSource.Listeners.Add(new TextWriterTraceListener("TraceSource_log.txt", "myListener"));

// 追蹤訊息
traceSource.TraceInformation("Before writing to console");    //將訊息寫入追蹤接聽程式。
Console.WriteLine("Test message.");
traceSource.TraceInformation("After writing to console");
traceSource.Flush();

// ====== TraceSource_log.txt ======
//myTrace Information: 0 : Before writing to console
//myTrace Information: 0 : After writing to console
```
```c#
// 定義一個 監聽項
TextWriterTraceListener listener = new TextWriterTraceListener("SourceSwitch_log.txt", "myListener");
listener.TraceOutputOptions = TraceOptions.ProcessId | TraceOptions.ThreadId | TraceOptions.DateTime;   // 設定追蹤輸出選項

// 定義一個 SourceSwitch
SourceSwitch sourceSwitch = new SourceSwitch("tsAPP1.Switch");
sourceSwitch.Level = SourceLevels.Verbose;    // 設定追蹤訊息的層級

// 建立一個追蹤來源
TraceSource traceSource = new TraceSource("tsAPP1");

// 指定追蹤來源的監聽項
traceSource.Listeners.Clear();
traceSource.Listeners.Add(listener);

// 指定追蹤來源的篩選條件(Switch)
traceSource.Switch = sourceSwitch;

// 追蹤訊息
traceSource.TraceData(TraceEventType.Error, 5, "Message 1");  //寫入追蹤資料，含(事件型別、事件辨識項、訊息資料)
traceSource.TraceInformation("Before writing to console");    //寫入追蹤告知性訊息。
Console.WriteLine("Test message.");
traceSource.TraceInformation("After writing to console");
traceSource.Flush();

// ====== SourceSwitch_log.txt ======
//tsAPP1 Error: 5 : Message 1
//    ProcessId=6460
//    ThreadId=10
//    DateTime=2012-02-10T10:41:44.4956258Z
//tsAPP1 Information: 0 : Before writing to console
//    ProcessId=6460
//    ThreadId=10
//    DateTime=2012-02-10T10:41:44.4956258Z
//tsAPP1 Information: 0 : After writing to console
//    ProcessId=6460
//    ThreadId=10
//    DateTime=2012-02-10T10:41:44.4956258Z
```

## TraceSwitch 類別 vs BooleanSwitch 類別
```c#
public BooleanSwitch(string displayName, string description);
public BooleanSwitch(string displayName, string description, string defaultSwitchValue);

public TraceSwitch(string displayName, string description);
public TraceSwitch(string displayName, string description, string defaultSwitchValue);
```

[TraceSwitch](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.traceswitch.aspx) 類別與 [BooleanSwitch](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.booleanswitch.aspx) 類別都是用來設定控制偵錯與追踨的輸出。差別在於：

- [BooleanSwitch](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.booleanswitch.aspx)：使用簡單的開啟/關閉切換，控制偵錯與追踨的輸出。
- 0：disable
- 1：enable
- [TraceSwitch](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.traceswitch.aspx)：提供多層切換，控制追蹤與偵錯的輸出。
- 0：TraceLevel.Off
- 1：TraceLevel.Error
- 2：TraceLevel.Warning
- 3：TraceLevel.Info
- 4：TraceLevel.Verbose
```xml
<configuration>
<system.diagnostics>

<switches>
<add name="LogSwitch1" value="1" />
<add name="LogSwitch2" value="Error" />
</switches>

</system.diagnostics>
</configuration>
```
```c#
//==================================================
//BooleanSwitch 簡單設定偵錯與追踨狀態（開啟/關閉)
//==================================================

BooleanSwitch bSwitch = new BooleanSwitch("LogSwitch1", "是否記錄 Log", "0");

Console.WriteLine("LogSwitch is Enabled : {0}", bSwitch.Enabled);  //True

//==================================================
//TraceSwitch 多層次設定偵錯與追踨狀態（開啟/關閉)
//==================================================

TraceSwitch tSwitch = new TraceSwitch("LogSwitch2", "Log 追踨層級");

Trace.WriteLine("Trace Level = " + tSwitch.Level);
Trace.WriteLineIf(tSwitch.TraceError, "TraceError...");
Trace.WriteLineIf(tSwitch.TraceWarning, "TraceWarning...");
Trace.WriteLineIf(tSwitch.TraceInfo, "TraceInfo...");
Trace.WriteLineIf(tSwitch.TraceVerbose, "TraceVerbose...");
Trace.WriteLineIf(tSwitch.Level == TraceLevel.Warning, "TraceWarning...");
```

# 停止偵錯 and 停止執行

原來［停止偵錯］和［停止執行］是有不同意義的，詳情請參考 MSDN: [How to: Stop Debugging or Stop Execution](http://msdn.microsoft.com/en-us/library/406kfbs1%28v=vs.100%29.aspx)  

![](http://127.0.0.1:8080/_images/cs/stop-debug-and-stop-execution.png)

# StackTrace 和 StackFrame 類別

堆疊追蹤類別 ( [StackTrace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.aspx) ) 是.NET用來記錄執行階段呼叫堆疊的狀態(runtime's call stack)。  
[StackTrace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.aspx) 是由 [StackFrame](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stackframe.aspx) 類別組成，每次呼叫一個方法，就加一個 [StackFrame](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stackframe.aspx) 到堆疊之上。  

- [StackTrace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.aspx) ：Represents a stack trace, which is an ordered collection of one or more stack frames.
- [StackFrame](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stackframe.aspx) ：Represents a function call on the call stack for the current thread.
```c#
private void button15_Click(object sender, EventArgs e)
{
StackTrace stacktrace = new StackTrace(true);
Trace.WriteLine("目前行號: " + stacktrace.GetFrame(0).GetFileLineNumber().ToString());
Trace.WriteLine("目前方法: " + stacktrace.GetFrame(0).GetMethod().Name);
Trace.WriteLine("呼叫名稱: " + stacktrace.GetFrame(1).GetMethod().Name);
method1();
//目前行號: 403
//目前方法: button15_Click
//呼叫名稱: OnClick
}

void method1()
{
StackTrace stacktrace = new StackTrace(true);
Trace.WriteLine("目前行號: " + stacktrace.GetFrame(0).GetFileLineNumber().ToString());
Trace.WriteLine("目前方法: " + stacktrace.GetFrame(0).GetMethod().Name);
Trace.WriteLine("呼叫名稱: " + stacktrace.GetFrame(1).GetMethod().Name);
method2();
//目前行號: 412
//目前方法: method1
//呼叫名稱: button15_Click
}

void method2()
{
try
{
StackTrace stacktrace = new StackTrace(true);
Trace.WriteLine("目前行號: " + stacktrace.GetFrame(0).GetFileLineNumber().ToString());
Trace.WriteLine("目前方法: " + stacktrace.GetFrame(0).GetMethod().Name);
Trace.WriteLine("呼叫名稱: " + stacktrace.GetFrame(1).GetMethod().Name);
Trace.WriteLine("呼叫名稱: " + stacktrace.GetFrame(2).GetMethod().Name);
//目前行號: 421
//目前方法: method2
//呼叫名稱: method1
//呼叫名稱: button15_Click

throw new Exception();

}
catch(Exception ex)
{
Debug.Assert(false, ex.StackTrace);
}

//at Lesson3.method2()  D:\MCTS\MCTS2011\Practice\PracticeMCTS\Chapter10\Lesson3.cs(514)
//at Lesson3.method1()  D:\MCTS\MCTS2011\Practice\PracticeMCTS\Chapter10\Lesson3.cs(489)
//at Lesson3.button15_Click(Object sender, EventArgs e)  D:\MCTS\MCTS2011\Practice\PracticeMCTS\Chapter10\Lesson3.cs(477)
//at Control.OnClick(EventArgs e)  
//at Button.OnClick(EventArgs e)  
//at Button.OnMouseUp(MouseEventArgs mevent)  
//at Control.WmMouseUp(Message& m, MouseButtons button, Int32 clicks)  
//at Control.WndProc(Message& m)  
//at ButtonBase.WndProc(Message& m)  
//at Button.WndProc(Message& m)  
//at ControlNativeWindow.OnMessage(Message& m)  
}
```

說明：

因為每次呼叫一個方法，系統就會加一個 [StackFrame](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stackframe.aspx) 到 [StackTrace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.aspx) 之中，  因此 GetFrame(0) 就表示是目前方法的堆疊，GetFrame(1) 就是上一個方法。  所以，若我們想在某一個 method 中，追蹤其是如何被引發的，就可以 [GetFrame](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.getframe.aspx) 一直追蹤上去。  

另外，在上例中，我們故意在 method2 中引發一個 Exception ，透過 Exception.StackTrace 方法，查看目前 [StackTrace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.stacktrace.aspx) 中的所有資料。

# throw ex 和 throw 有什麼不同

當在設計例外捕捉的時候，如果有些例外狀況不想在 catch 中處理，通常就會使用 throw 或 throw ex 將原始的 exception 往上層丟。  這二種寫都可以達到我們想要的結果，但是它們的差別又在哪裡？  

請看以下範例，  
```c#
private static void Main(string[] args)
{
try
{
exception1();          // line 14
}
catch (Exception ex)
{
Console.WriteLine("exception 1:" + ex.Message);
Console.WriteLine(ex.StackTrace);
}
try
{
exception2();          // line 23
}
catch (Exception ex)
{
Console.WriteLine("exception 2:" + ex.Message);
Console.WriteLine(ex.StackTrace);
}
Console.ReadLine();
}

static void exception1()
{
try
{
DivByZero();    // line 37
}
catch (Exception ex)
{
throw ex;       // line 41
}
}

static void exception2()
{
try
{
DivByZero();    // line 49
}
catch 
{
throw;          // line 53
}
}

static void DivByZero()
{
int x = 0;
int y = 1 / x;      // line 60
}
```

底下是上面範例執行的結果。  由結果來看，二個方法都可以捕捉到相同的錯誤資訊，差別僅在於，  若使用 throw ex ，它會重設 stack trace ，所以 StackTrace 就看不到行 60 這個最底層錯誤訊息的 stack trace 。  若使用 throw ，它會保留原始的 stack trace 內容，所以才看的到行 60 這個最底層錯誤訊息的 stack trace 。  

所以，若要追蹤錯誤，最好使用 throw 來抛出原始例外，才可以查到真正發生程式碼錯誤的位置。  
exception 1:嘗試以零除。
於 ConsoleApplication3.Program.exception1() 於 D:\MyPractice\70-536\ConsoleApplication3\Program.cs: 行 41
於 ConsoleApplication3.Program.Main(String[] args) 於 D:\MyPractice\70-536\ConsoleApplication3\Program.cs: 行 14
exception 2:嘗試以零除。
於 ConsoleApplication3.Program.DivByZero() 於 D:\MyPractice\70-536\ConsoleApplication3\Program.cs: 行 60
於 ConsoleApplication3.Program.exception2() 於 D:\MyPractice\70-536\ConsoleApplication3\Program.cs: 行 53
於 ConsoleApplication3.Program.Main(String[] args) 於 D:\MyPractice\70-536\ConsoleApplication3\Program.cs: 行 23

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [追蹤和稽核應用程式](http://msdn.microsoft.com/zh-tw/library/zs6s4h68.aspx)
- [追蹤接聽項](http://msdn.microsoft.com/zh-tw/library/4y5y10s7.aspx)
-