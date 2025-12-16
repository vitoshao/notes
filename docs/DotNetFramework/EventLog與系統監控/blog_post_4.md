---
title: Event Log
layout: default
parent: EventLog與系統監控
nav_order: 4
description: "Event Log"
date: 2012-02-15
tags: [DotNetFramework,EventLog與系統監控]
postid: "6658016198805484892"
---
要記錄log，可以用各種方法，EventLog 只是 Windows 本身提供的一種機制，可以用來記錄應用程式的狀況。

# 瞭解事件記錄

事件記錄的組成可分成三個部分：

1. 事件記錄檔( [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.aspx))：
- 事件記錄檔是事件的分類，例如系統預設的日誌：應用程式類、安全性、系統...。
- 手動建立：可以在註冊區的 HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\ 底下手動加入一個事件記錄檔。
- 程式建立：使用 [EventLog.CreateEventSource](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.createeventsource) 這個靜態方法建立。
2. 事件( [EventLogEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlogentry.aspx))：
- 每個事件來源歸屬在一個事件記錄檔裡。
- 包含訊息文字、訊息類別、事件識別碼、工作類別等資訊。
- 必須先取得 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog) 的執行個體，再由 [WriteEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.writeentry.aspx) 方法寫入。
3. 事件來源( EventSource )：
- 其名稱是唯一的，不允許重複。
- 事件來源必須歸屬在一個事件記錄檔裡。
- 用來表示事件發生的來源，通常就是以應用程式名稱為主。

![](http://127.0.0.1:8080/_images/cs/log_event.png)

# 存取事件記錄

在.NET的 [System.Diagnostics](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.aspx) 命名空間裡的 [EventLog](http://msdn.microsoft.com/zh-tw/library/34xfdz6a.aspx) 物件，提供了與存取 Windows 事件記錄相關的方法。  一般使用 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog) 類別的靜態方法即可存取 Windows 的事件記錄。  但是，若有一連串的動作，也可以先建立一個 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog) 的執行個體，再透過該執行個體，進行存取動作。  

#### 與 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog) 相關的方法整理

|  | 建立 | 刪除 | 取得 | 判斷是否存在 |
| --- | --- | --- | --- | --- |
| 事件記錄檔 | [CreateEventSource](http://msdn.microsoft.com/zh-tw/library/2awhba7a.aspx) | [Delete](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.delete.aspx) | [EventLog.GetEventLogs](http://msdn.microsoft.com/zh-tw/library/74e2ybbs.aspx) | [Exists](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.exists.aspx) |
| 事件來源 | [CreateEventSource](http://msdn.microsoft.com/zh-tw/library/2awhba7a.aspx) | [DeleteEventSource](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.deleteeventsource.aspx) |  | [SourceExists](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.sourceexists.aspx) |
| 事件 | [WriteEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.writeentry.aspx) | [Clear](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.clear.aspx) | [instLog.Entries](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.entries.aspx) |  |

#### [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog) 的多載建構子
```c#
public EventLog(string logName);
public EventLog(string logName, string machineName);
public EventLog(string logName, string machineName, string source);
```
```c#
EventLog log1 = new EventLog("application");    //建立 application 事件記錄檔的執行個體。
EventLog log2 = new EventLog("MyApp");          //建立 MyApp 事件記錄檔的執行個體。
```

## 瀏覽事件記錄檔
```c#
public static EventLog[] GetEventLogs();
public static EventLog[] GetEventLogs(string machineName);
```
```c#
//取得所有事件記錄檔。
EventLog[] EventLogs = EventLog.GetEventLogs();     
foreach (EventLog el in EventLogs)
{
Console.WriteLine("{0} ({1})", el.LogDisplayName, el.Log);
}
//應用程式 (Application)
//安全性 (Security)
//系統 (System)
//Demo Event Log (Demo Event Log)
// ...
```

## 建立事件記錄檔

前面提到，事件記錄檔可以在 registry 中，自行定義，也可以使用程式碼。  如果使用程式碼，則可以透過靜態方法 [EventLog.Exists](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.exists) ，先判斷事件記錄檔是否已存在，再決定是否建立新的事件記錄檔。  

[EventLog.CreateEventSource](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog.createeventsource) ：這個方法可以用來建立新的資料來源，也可以用來建立新的自訂記錄檔。  
```c#
public static void CreateEventSource(string source, string logName);
public static void CreateEventSource(string source, string logName, string machineName);
public static void CreateEventSource(EventSourceCreationData sourceData);
```
```c#
string LogFile = "My Test Log";
string EventSource = "Event Source Testing";

//判斷記錄檔是否存在於本機電腦上。
if (!EventLog.Exists(LogFile))                     
{
//建立事件記錄檔。
EventLog.CreateEventSource(EventSource, LogFile);   
}

//刪除事件記錄檔。
EventLog.Delete(LogFile);         
```

## 建立事件來源

建立事件來源可以先透過靜態方法 [EventLog.SourceExists](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.sourceexists) ，先判斷事件記錄檔是否已存在，再決定是否建立新的事件來源。  

&lt;font color="red"&gt;注意：&lt;/font&gt;  事件來源雖然與事件記錄檔相關，但是不同事件記錄檔之間也不允許建立相同的事件來源。  
例如，在 LogA 中建立了 EventSourceA ，就不能在 LogB 中建立 EventSourceA 。  
但是，這不表示 LogB 中的事件就不能使用 EventSourceA 。  
```c#
string LogFile = "My Test Log";
string EventSource = "Event Source Testing";

if (!EventLog.SourceExists(EventSource))       //判斷事件來源是否登錄在本機電腦上。
{
//建立事件來源
EventLog.CreateEventSource(EventSource, LogFile);        
}
//刪除事件來源
EventLog.DeleteEventSource(source);     
```

## 建立事件

要建立一個事件，可以使用靜態方法 [EventLog.WriteEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.writeentry) 直接寫入。  若有一連串的動作，也可以先建立一個 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLog) 的執行個體，再透過該執行個體，進行存取。  
```c#
public void WriteEntry(string message);
public void WriteEntry(string message, EventLogEntryType type);
public void WriteEntry(string message, EventLogEntryType type, int eventID);
public void WriteEntry(string message, EventLogEntryType type, int eventID, short category);
public void WriteEntry(string message, EventLogEntryType type, int eventID, short category, byte[] rawData);

public static void WriteEntry(string source, string message);
public static void WriteEntry(string source, string message, EventLogEntryType type);
public static void WriteEntry(string source, string message, EventLogEntryType type, int eventID);
public static void WriteEntry(string source, string message, EventLogEntryType type, int eventID, short category);
public static void WriteEntry(string source, string message, EventLogEntryType type, int eventID, short category, byte[] rawData);
...
```

#### [EventLog.WriteEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.writeentry) 有數個多載方法，其中幾個方法的參數簡單說明如下：

- source：事件來源。
- message：事件訊息內容。
- type：一個事件型別(等級)，必須是 [EventLogEntryType](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLogentrytype.aspx) 列舉型別中的值。
- eventID：事件的應用程式特定識別項。
- category：與訊息相關的應用程式特定的子分類。

####  [EventLogEntryType](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.EventLogentrytype.aspx) ：事件記錄檔項目的事件型別。

- Error       ：錯誤事件。這表示是使用者應該知道的重要問題，通常是功能或資料的遺失。
- Warning     ：警告事件。這表示不是立即重要的問題，但是可能表示將來會引發問題的狀況。
- Information ：資訊事件。這表示重要成功的作業。
- SuccessAudit：成功稽核事件。這表示受稽核存取嘗試成功時所發生的安全性事件，例如登錄成功。
- FailureAudit：失敗稽核事件。這表示受稽核的存取嘗試失敗時發生的安全性事件，例如無法開啟檔案。

#### 建立事件

建立事件時，可以透過具名的事件記錄檔的執行個體建立，也可以使用靜態方法直接新增。  不過，不管用哪一個方法，都必須替這個事件指定一個資料來源。  

使用靜態方法建立事件時，若指定的資料來源建是新的，那麼這個資料來源會預設使用[應用程式]這個記錄檔。  
```c#
EventLog.WriteEntry("Event Source", "Event Message");

//具現化一個 EventLog ，並指定記錄檔， 再執行 WriteEntry
EventLog Log1 = new EventLog("myEventLog");     
Log1.Source = "myEvent Source";
Log1.WriteEntry("Event Message 1...", EventLogEntryType.Information);

//具現化一個 EventLog ，不指定記錄檔， 
EventLog Log2 = new EventLog();                     //沒有指定 LogName，會自動根據 Source 判斷相符的 LogName
Log2.Source = "myEvent Source";
Log2.WriteEntry("Event Message 2..", EventLogEntryType.Information);

//建立帶有[事件識別項]和[事件分類]的Event
EventLog log3 = new EventLog();                     
Log2.Source = "myEvent Source";
Log2.WriteEntry("Event Message 3", EventLogEntryType.Information, 1314, Convert.ToInt16(3));   //最後這二個參數，都是由應用程式自行定義的值。
```

## 清除事件
```c#
EventLog log = new EventLog("Demo Event Log");
log.Clear();        //清除 Demo Event Log 裡的所有事件記錄
```

## 瀏覽事件

EventLog.Entries : 取得特定 [EventLog](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlog.aspx) 下的所有事件。該集合由 [EventLogEntry](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.eventlogentry.aspx) 組成。
```c#
EventLog log = new EventLog("Demo Event Log");     //取得事件記錄檔。

foreach (EventLogEntry eln in log.Entries)
{
Console.WriteLine("[Source]:{0}  [Message]:{1}", eln.Source, eln.Message);
}
```

## 範例：列舉應用程式記錄檔中 (Application Log) ，等級為 Warning 和 Error 的訊息
```c#
EventLog log = new EventLog("Application");     //create Application Log。
foreach (EventLogEntry entry in log.Entries)
{
if (entry.EntryType == EventLogEntryType.Warning || entry.EntryType == EventLogEntryType.Error)
{
Console.WriteLine("[發生時間]:{0}  [來源]:{1}  [訊息內容]:{2}", entry.TimeGenerated, entry.Source, entry.Message);
}
}
```

#### **Note**

If a source has already been mapped to a log and you remap it to a new log, you must restart the computer for the changes to take effect.  
http://msdn.microsoft.com/en-us/library/system.diagnostics.eventlog.source.aspx  

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [管理事件記錄檔](http://msdn.microsoft.com/zh-tw/library/4f69axw4%28v=vs.80%29.aspx)
- 
-