---
title: 監測管理事件
layout: default
parent: EventLog與系統監控
nav_order: 1
description: "監測管理事件"
date: 2012-02-15
tags: [DotNetFramework,EventLog與系統監控]
postid: "7882606357370664396"
---
在程式中，我們常常會需要去檢查電腦的某些狀況，例如：目前某個服務的狀態或者磁碟空間剩餘多少等等。此外，要是這些狀態還可以自動回報，那就更好了。  沒錯，WMI 就是這樣一個好物。  

Windows管理規範 (Windows Management Instrumentation, WMI) 是 Windows 作業系統的服務元件，  它提供系統管理員方便管理應用程式與資源的工具，同時可以透過程式去存取應用程式、服務和其他電腦元件的管理資訊 (例如，組態設定和屬性值)。  底下列表為WMI的幾個主要功能：  

- 1. 在遠端電腦器上啟動一個工作。
- 2. 設定一個在特定日期和時間執行的工作。
- 3. 遠端啟動電腦。
- 4. 獲得本機或遠端電腦的已安裝程序列表。
- 5. 查詢本機或遠端電腦的 Windows 事件日誌。

# 使用WMI監測管理事件

[System.Management](http://msdn.microsoft.com/zh-tw/library/system.management.aspx) 命名空間是 WMI 的核心，  透過 WMI 可以監測管理應用程式、裝置、系統所引發的事件。  下表是 [System.Management](http://msdn.microsoft.com/zh-tw/library/system.management.aspx) 底下的幾個主要的物件：  

- [EventQuery](http://msdn.microsoft.com/zh-tw/library/system.management.eventquery.aspx) ：表示 WMI 事件查詢。
- [ObjectQuery](http://msdn.microsoft.com/zh-tw/library/system.management.objectquery.aspx) ：表示傳回執行個體 (Instance) 或類別的管理查詢。
- [ManagementObjectSearcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementobjectsearcher.aspx) ：根據指定的查詢擷取管理物件的集合。
- [ManagementObjectCollection](http://msdn.microsoft.com/zh-tw/library/system.management.managementobjectcollection.aspx) ：表示經由 WMI 所擷取的各種管理物件集合。
- [ManagementObject](http://msdn.microsoft.com/zh-tw/library/system.management.managementobject.aspx) ：表示 WMI 執行個體。
- [ManagementBaseObject](http://msdn.microsoft.com/zh-tw/library/system.management.managementbaseobject.aspx) ：包含管理物件的基本項目。
- [ConnectionOptions](http://msdn.microsoft.com/zh-tw/library/system.management.connectionoptions.aspx) ：指定要進行 WMI 連接所需的所有設定。
- [ManagementScope](http://msdn.microsoft.com/zh-tw/library/system.management.managementscope.aspx) ：表示管理作業的範圍 (命名空間)。
- [ManagementEventWatcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.aspx) ：訂閱暫時性事件告知，該告知以指定的事件查詢為基礎。
- [EventWatcherOptions](http://msdn.microsoft.com/zh-tw/library/system.management.eventwatcheroptions.aspx) ：指定管理事件監看的選項。

## 如何存取 WMI 資訊

透過 WMI ，可以監測 Windows 裡各式各樣有用的資訊。底下是如何使用 WMI 進行監測的簡單步驟：   

1. 使用 [ManagementScope](http://msdn.microsoft.com/zh-tw/library/system.management.managementscope.aspx) 定義管理範圍
2. 叫用 [ManagementScope.Connect](http://msdn.microsoft.com/zh-tw/library/system.management.managementscope.connect.aspx) 方法，建立連線。
3. 使用 [ObjectQuery](http://msdn.microsoft.com/zh-tw/library/system.management.objectquery.aspx) 宣告一個查詢語法。
4. 使用 [ManagementObjectSearcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementobjectsearcher.aspx) 建立查詢物件。
5. 叫用 [ManagementObjectSearcher.Get](http://msdn.microsoft.com/zh-tw/library/system.management.managementobjectsearcher.get.aspx) 方法，取得 [ManagementObjectCollection](http://msdn.microsoft.com/zh-tw/library/system.management.managementobjectcollection.aspx) 型態的查詢結果。
```c#
//1.建立一個管理範圍 ManagementScope
ManagementScope scope = new ManagementScope(@"\\localhost\root\cimv2");

//2.連上ManagementScope
scope.Connect();

//3.使用 ObjectQuery 類別, 建立一個 WQL query (Wmi Query Language)
ObjectQuery query = new ObjectQuery("SELECT \* FROM Win32_OperatingSystem");

//4.建立一個查詢管理物件 ManagementObjectSearcher
ManagementObjectSearcher searcher = new ManagementObjectSearcher(scope, query);     //傳入上面的 scope 和 query 當作參數

//5.透過 Get 方法, 取得查詢結果
ManagementObjectCollection searcherResult = searcher.Get();

//列舉結果
foreach (ManagementObject m in searcherResult)
{
this.listBox1.Items.Add(m.ToString());
Console.WriteLine(m.ToString());
//\\VITO-2011W7\root\cimv2:Win32_OperatingSystem=@

Console.WriteLine("Computer Name : {0}", m["csname"]);
Console.WriteLine("Windows Directory : {0}", m["WindowsDirectory"]);
Console.WriteLine("Operating System: {0}", m["Caption"]);
Console.WriteLine("Version: {0}", m["Version"]);
Console.WriteLine("Manufacturer : {0}", m["Manufacturer"]);
//Computer Name : VITO-2011W7
//Windows Directory : C:\Windows
//Operating System: Microsoft Windows 7 旗艦版 
//Version: 6.1.7601
//Manufacturer : Microsoft Corporation
}
```

## 如何訂閱 WMI 事件

#### 以同步模式訂閱管理事件

當 WMI 系統的內容被改變的時候，就會引發 WMI 事件。  所以若想主動要得知這些訊息，就可以透過 [ManagementEventWatcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.aspx) 物件來訂閱這些事件。  例如：當某個應用程式被啟動了，或者某個服務被終止了，我們都可以透過訂閱事件，來監控這些改變。  底下是如何訂閱 WMI 事件的簡單步驟：   

1. Create a new [ManagementEventWatcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.aspx) object.
2. Associate an [EventQuery](http://msdn.microsoft.com/zh-tw/library/system.management.eventquery.aspx) object with it.
3. Call the [WaitForNextEvent](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.waitfornextevent.aspx) method.
4. [Stop](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.stop.aspx) the notifications.

要監聽 WMI 事件，必須透過 ManagementEventWatcher 物件。再叫用 WaitForNextEvent 方法即可。這個方法使用**同步作業**模式。  
```c#
// 建立查詢
WqlEventQuery query = new WqlEventQuery("__InstanceCreationEvent",
new TimeSpan(0, 0, 10),
"TargetInstance isa \"Win32_Process\"");

// 建立一個事件監控物件, 並且訂閱符合查詢語法的事件
ManagementEventWatcher watcher = new ManagementEventWatcher(query);

// 等待下一個事件發生,timeout=10s
ManagementBaseObject obj = watcher.WaitForNextEvent();

// 顯示事件回應的訊息
Console.WriteLine("Process {0} has been created, path is: {1}",
((ManagementBaseObject)obj["TargetInstance"])["Name"],
((ManagementBaseObject)obj["TargetInstance"])["ExecutablePath"]);

// 停止監看
watcher.Stop();
```

#### 以非同步模式訂閱管理事件

上面例子，透過叫用 [WaitForNextEvent](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.waitfornextevent.aspx) 方法，等待事件發生的回應，這個方法是**同步作業**的，程式會被 lock 住，直到回應發生，或 Timeout 為止。  另外也可以使用**非同步方法**，訂閱事件，步驟如下：  

1. Create a new [ManagementEventWatcher](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.aspx) object.
2. Associate an [EventQuery](http://msdn.microsoft.com/zh-tw/library/system.management.eventquery.aspx) object with it.
3. Subscribe the [EventArrived](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.eventarrived.aspx) event.
4. [Start](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.start.aspx) the notifications.
5. [Stop](http://msdn.microsoft.com/zh-tw/library/system.management.managementeventwatcher.stop.aspx) the notifications.

使用非同步方式時，必須叫用 ManagementEventWatcher.Start 方法，才會開始監聽事件。  下面這個例子，就是以非同步方式來監測 USB 的插拔事件。  其回應事件的方法，必須接二個參數 **Object** 和 [**EventArrivedEventArgs**](http://msdn.microsoft.com/zh-tw/library/system.management.eventarrivedeventargs.aspx) 。  
```c#
ManagementEventWatcher managementEventWatcher;

//啟動偵測
private void btnStartMonitorUSB_Click(object sender, EventArgs e)
{
managementEventWatcher = new ManagementEventWatcher("Select \* From __InstanceOperationEvent WITHIN 10 WHERE TargetInstance ISA \"Win32_DiskDrive\"");
managementEventWatcher.Start();
managementEventWatcher.EventArrived += new EventArrivedEventHandler(managementEventWatcher_EventArrived);

updateDelegate = new UpdateListBoxDelegate(UpdateListBox); 
}

//偵測事件的回應方法 （object, EventArrivedEventArgs）
private void managementEventWatcher_EventArrived(object sender, EventArrivedEventArgs e)
{
ManagementBaseObject newEvent = e.NewEvent, newEventTarget = (newEvent["TargetInstance"] as ManagementBaseObject);
if (newEventTarget["InterfaceType"].ToString() == "USB")
{
switch (newEvent.ClassPath.ClassName)
{
case "__InstanceCreationEvent":
this.Invoke(updateDelegate, new object[] { Convert.ToString(newEventTarget["Caption"]) + " has been plugged in" });
break;
case "__InstanceDeletionEvent":
this.Invoke(updateDelegate, new object[] { Convert.ToString(newEventTarget["Caption"]) + " has been plugged out" });
break;
}
}
}

//停止偵測
private void btnStopMonitorUSB_Click(object sender, EventArgs e)
{
managementEventWatcher.Stop();
}
```

# 範例演練

## 如何列舉磁碟機空間
```c#
//1.建立查詢語法
string sQuery = "SELECT Size, Name FROM Win32_LogicalDisk where DriveType=3";

//2.依據查詢語法，建立搜尋器
ManagementObjectSearcher oSearcher = new ManagementObjectSearcher(sQuery);

//3.透過搜尋器，取得搜尋結果
ManagementObjectCollection oReturnCollection = oSearcher.Get();

//4.列舉結果
foreach (ManagementObject oMObject in oReturnCollection)
{
Console.WriteLine("Resource Name: {0} , Size : {1}", 
oMObject["Name"].ToString(),oMObject["Size"].ToString());
} 
```

## 如何列舉遠端電腦的磁碟機空間
```c#
//建立WMI連線
ConnectionOptions oConn = new ConnectionOptions();
oConn.Username = "Username";
oConn.Password = "Password";
oConn.EnablePrivileges = true;

//建立連接到 WMI 的範圍物件
ManagementScope oMScope = new ManagementScope(@"\\192.168.0.168\root\cimv2", oConn);
oMScope.Connect();

if (oMScope.IsConnected)
{ 
//建立查詢語法
ObjectQuery oQuery = new ObjectQuery("SELECT Size, Name FROM Win32_LogicalDisk WHERE DriveType=3");

//依據查詢語法，建立搜尋器
ManagementObjectSearcher oSearcher = new ManagementObjectSearcher(oMScope, oQuery);

//透過搜尋器，取得搜尋結果
ManagementObjectCollection oReturnCollection = oSearcher.Get();

//列舉結果
Console.WriteLine("Server : {0}", oMScope.Path.Server);
foreach (ManagementObject oMObject in oReturnCollection)
{
Console.WriteLine("Disk {0} {1} GB ", oMObject["Name"].ToString(), Math.Round((double)System.UInt64.Parse(oMObject["Size"].ToString()) / 1024 / 1024 / 1024,2));
}
}
//Server : 192.168.0.168
//Disk C: 183.07 GB 
//Disk D: 49.81 GB 
```

## 如何列舉網路介面卡
```c#
ManagementObjectSearcher oSearcher = new ManagementObjectSearcher(
"SELECT \* FROM Win32_NetworkAdapterConfiguration");

ManagementObjectCollection oReturnCollection = oSearcher.Get();

foreach (ManagementObject oMObject in oReturnCollection)
{
Console.WriteLine("Description : " + oMObject["Description"]);
Console.WriteLine("MacAddress : " + oMObject["MacAddress"]);
}
```

## 如何列舉已經停止的服務
```c#
ManagementObjectSearcher oSearcher = new ManagementObjectSearcher(
"SELECT \* FROM Win32_Service WHERE Started = FALSE");

ManagementObjectCollection oReturnCollection = oSearcher.Get();

foreach (ManagementObject oMObject in oReturnCollection)
{
Console.WriteLine("Service : " + oMObject["Caption"]);
} 
```

## 如何監看一個Win32程式被啟動執行
```c#
string sQuery = "SELECT \* FROM __InstanceCreationEvent WITHIN 2 WHERE TargetInstance ISA 'Win32_Process'";
//this query string means send notification of the creation of Win32_Process instances, with a 2-second polling interval."

//訂閱一個事件監控
ManagementEventWatcher oWatcher = new ManagementEventWatcher(sQuery);
oWatcher.Options.Timeout = new TimeSpan(0, 0, 60);  //設定Timeout=60s

//開始監看啟動事件，並等待事件發生
ManagementBaseObject oEvent = oWatcher.WaitForNextEvent();

ManagementBaseObject oMBObject = (ManagementBaseObject)oEvent["TargetInstance"];
Console.WriteLine("程式 {0} 被啟動，程式來源路徑：{1}", oMBObject["Name"], oMBObject["ExecutablePath"]);
oWatcher.Stop();

//Output:
//程式 Notepad2.exe 被啟動，執行路徑：C:\Program Files\Notepad2\Notepad2.exe
//程式 EWinnerStart.exe 被啟動，程式來源路徑：C:\TWEWinner\EWinnerStart.exe
```

## 如何監看一個Win32服務被暫停
```c#
EventQuery DemoQuery = new EventQuery();
string sQueryString =
"SELECT \* FROM __InstanceModificationEvent WITHIN 1 WHERE TargetInstance ISA 'Win32_Service' AND TargetInstance.State = 'Paused'";

//訂閱一個事件監控
ManagementEventWatcher oWatcher = new ManagementEventWatcher(sQueryString);
oWatcher.Options.Timeout = new TimeSpan(0, 0, 60);

//開始監看啟動事件，並等待事件發生
ManagementBaseObject oEvent = oWatcher.WaitForNextEvent();

ManagementBaseObject oMBObject = (ManagementBaseObject)oEvent["TargetInstance"];
Console.WriteLine("服務 {0} ( {1} ) 被暫停。", oMBObject["DisplayName"], oMBObject["Description"]);
oWatcher.Stop();

//Output:
//服務 TEST_WindowsService ( TEST  WindowsService ) 被暫停。
```

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [Win32_Service class](http://msdn.microsoft.com/en-us/library/windows/desktop/aa394418.aspx)
- [Win32_Process class](http://msdn.microsoft.com/en-us/library/windows/desktop/aa394372.aspx)
- [WMI Classes](http://msdn.microsoft.com/en-us/library/windows/desktop/aa394554%28v=vs.85%29.aspx)
- [WMI Code Creator自動產生WMI的程式碼](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=8572)