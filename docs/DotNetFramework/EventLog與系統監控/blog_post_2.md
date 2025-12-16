---
title: 監測效能
layout: default
parent: EventLog與系統監控
nav_order: 2
description: "監測效能"
date: 2012-02-15
tags: [DotNetFramework,EventLog與系統監控]
postid: "601844326387065135"
---
# 處理序(Process)

- A process is a memory slice that contains resources.
- An isolated task performed by the operating system.
- An application that is being run.

Process元件提供對電腦上正在執行處理序的存取。  以最簡單的話來說，處理序就是正在執行的應用程式。  執行緒則是作業系統配置處理器時間的基本單位。  執行緒可以執行處理序的任何部分程式碼，包括目前正由另一個執行緒執行的部分在內。  

[System.Diagnostics](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.aspx) 命名空間的 [Process](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.aspx) 類別提供了對本機和遠端處理序 (Process) 的存取，讓程式能夠啟動和停止本機系統處理序。

#### 下表為 Porcess 類別所提供用來辨別特定的處理序的方法：

- [GetCurrentProcess](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.getcurrentprocess.aspx) ：回傳目前的處理序
- [GetProcessById](http://msdn.microsoft.com/zh-tw/library/76fkb36k.aspx) ：取得指定ID的處理序
- [GetProcessesByName](http://msdn.microsoft.com/zh-tw/library/wbt7d3cy.aspx) ：取得指定Name的處理序
- [GetProcesses](http://msdn.microsoft.com/zh-tw/library/1f3ys1f9.aspx) ：取得指定電腦上執行的所有處理序

#### 下表為 Porcess 類別中幾個重要的方法：

- [Start](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.start.aspx) ：啟動處理序資源，並將它與 [Process](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.aspx) 元件相關聯。
- [Kill](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.kill.aspx) ：立即停止相關的處理序。
- [StandardInput](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.standardinput.aspx) ：取得用來寫入應用程式輸入的資料流。
- [StandardOutput](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.standardoutput.aspx) ：取得用來讀取應用程式輸出的資料流。
- [WaitForExit](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.waitforexit.aspx) ：設定要等待相關的處理序結束的時間，並且阻止目前的執行緒執行，直到等候時間耗盡或者處理序已經結束為止。
- [CloseMainWindow](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.closemainwindow.aspx) ：關閉有使用者介面的處理序，方法是傳送關閉訊息至其主視窗。
- [Close](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.close.aspx) ：釋放與這個元件相關的所有資源。

#### ProcessStartInfo 類別：

[ProcessStartInfo](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.processstartinfo.aspx) 類別是用來設定處理序啟動時的相關資訊。下表是幾個常用屬性：

- [Arguments](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.processstartinfo.arguments.aspx) ：設定啟動應用程式時要使用的命令列引數集。
- [FileName](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.processstartinfo.filename.aspx) ：設定要啟動的應用程式。
- [UserName](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.processstartinfo.username.aspx) ：設定要在啟動處理序時使用的使用者名稱。
- [Password](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.processstartinfo.password.aspx) ：設定啟動處理序時要使用的使用者密碼。

&lt;!-- more --&gt;    
## 如何取得處理序資訊
```c#
//取得本身 Process
Process CurrentProcess = Process.GetCurrentProcess();
Console.WriteLine("[Current Process Name]:{0}  [Process ID]:{1}", CurrentProcess.ProcessName, CurrentProcess.Id);

//透過 GetProcessById 取得某個 Process
Process theProcess = Process.GetProcessById(10276);
Console.WriteLine("[Process Name]:{0}  [Process ID]:{1}", theProcess.ProcessName, theProcess.Id);

//透過 GetProcessesByName ，取得所有符合條件的 Process[]
Process[] Processes = Process.GetProcessesByName("Practice.vshost");
foreach (Process theProcess in Processes)
{
Console.WriteLine("[Process Name]:{0}  [Process ID]:{1}", theProcess.ProcessName, theProcess.Id);
}
```

## 如何列舉執行中的處理序
```c#
Process[] AllProcesses = Process.GetProcesses();
foreach (Process proc in AllProcesses)
{
Console.WriteLine("[Process Name]:{0}  [Process ID]:{1} ", proc.ProcessName, proc.Id);
}
```

## 如何啟動外部程式

叫用 [Process.Start](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.process.start.aspx) 即可啟動外部程式，它有幾種不同的覆載方法：
```c#
//===================================================
//範例１. 直接叫用 Start ，啟動應用程式
//===================================================
Process.Start("notepad.exe");

//===================================================
//範例２. 直接叫用 Start ，啟動應用程式，並傳遞參數
//===================================================
Process.Start("explorer.exe", @"http://www.microsoft.com");  //執行IE,並開啟指定網頁
Process.Start("notepad.exe", @"C:\setup.txt");               //執行notepad，並傳送一個檔名當參數，以開啟指定檔案

//===================================================
//範例３. 使用 ProcessStartInfo ，指定啟動應用程式的資訊
//===================================================

//指定 Process.StartInfo 物件
Process myProcess = new Process();
myProcess.StartInfo.FileName = "notepad.exe";
myProcess.StartInfo.Arguments = @"C:\setup.txt";
myProcess.Start();

//透過 ProcessStartInfo 物件
ProcessStartInfo info = new ProcessStartInfo();
info.FileName = "notepad.exe";
info.Arguments = @"C:\setup.txt";
Process.Start(info);

//===================================================
//範例４. 透過指定其檔案名稱、使用者名稱、密碼和網域，建立新的處理序
//===================================================

string filename = "";
string username = "";
string password = "";
string domain = "";
SecureString SecurePassword = new SecureString();
for (Int32 i = 0; i < password.Length; i++)
{
SecurePassword.AppendChar(Convert.ToChar(password[i]));
}
Process.Start(filename, username, SecurePassword, domain);
```

## 如何關閉處理序
```c#
Process[] processes = Process.GetProcessesByName("Notepad2");   //先取得要關閉的處理序名稱(Notepad2)
foreach (Process proc in processes)
{
proc.CloseMainWindow();     // 關閉處理序
proc.WaitForExit();         // 等待關閉     
}
```

## 如何判斷開啟的某個處理序被中止了
```c#
Process myProc = null;
private void btnStart_Click(object sender, EventArgs e)
{
myProc = new Process();

myProc.Exited += Process_Exited;    //設定程式中止時的委派事件
myProc.EnableRaisingEvents = true;
myProc.SynchronizingObject = this;

myProc.StartInfo.FileName = "notepad.exe"; 
myProc.StartInfo.WindowStyle = ProcessWindowStyle.Maximized; //放到最大
myProc.Start();
}

private void Process_Exited(object sender, EventArgs e)
{
MessageBox.Show("記事本已被關閉");
myProc.Exited -= Process_Exited;
}

private void btnClose_Click(object sender, EventArgs e)
{
myProc.CloseMainWindow();   // 關閉處理序
myProc.WaitForExit();       // 等待關閉  
}
```

## 如何由開啟的應用程式的標準資料流輸入輸出資料

假設有一個 Console 程式，它會等待使用者輸入資料後進行處理，再輸出執行結果。底下程式碼示範如何由標準資料流輸入資料，並取得執行結果。
```c#
//===================================================
//Console程式
//===================================================
static void Main(string[] args)
{
string inputText = Console.ReadLine();
Console.WriteLine("InputText : {0}", inputText);
}

//===================================================
//表單程式
//===================================================
Process proc = new Process();
ProcessStartInfo startInfo = new ProcessStartInfo();
startInfo.FileName = @"ConsoleApplication1.exe";
startInfo.Arguments = "300";

//設定重新導向
startInfo.UseShellExecute = false;          //指定是否要使用作業系統 Shell 來啟動處理序。
startInfo.RedirectStandardInput = true;     //指定應用程式由標準資料流讀取
startInfo.RedirectStandardOutput = true;    //指定應用程式由標準資料流輸出

//啟動程序
proc.StartInfo = startInfo;
proc.Start();

//寫入資料到標準資料流
StreamWriter myStreamWriter = proc.StandardInput;
myStreamWriter.WriteLine("abc");
myStreamWriter.Close();

//等待程序結束
proc.WaitForExit();

//由標準資料流讀取資料
string outputText = proc.StandardOutput.ReadToEnd().Trim();     //InputText : abc

//釋放資源
proc.Close();
```

# Windows 效能監視器

以下資料來自MSDN: [效能監視入門指南](http://technet.microsoft.com/zh-tw/library/dd744567%28v=ws.10%29)

## 什麼是效能監視器

**效能監視**是指評估電腦完成應用程式與系統工作的速度。  而整體效能又常常受限於資源使用上的影響，如硬碟存取速度、ＣＰＵ速度、記憶體大小、網路速度等等。  透過監視應用程式使用這些資源的狀況，可以分析應用程式整體的處理能力，進而擬定改進計畫，以提高系統處理能力。  

**「Windows 效能監視器」**是一個 Microsoft Management Console (MMC) 嵌入式管理單元，它可用來追蹤應用程式對效能造成的影響，並且在達到使用者定義的最佳效能門檻值的時候，適時的產生警告訊息或採取處理行動。  它結合了過去獨立的監視工具(包括[效能記錄及警示]、[伺服器效能警告器]及[系統監視器])，提供了可用於自訂「資料收集器集合工具」及「事件追蹤」工作階段的圖形化介面。  與先前各自獨立的工具相比，效能監視器最主要的優點是它將這些工具的功能結合為單一介面，讓您可以使用常用方式來定義要收集的資料。也讓您可以將計數器群組複製到其他電腦使用。

## 效能監視器的主要功能：

- **資料收集器集合工具 (Data Collector Sets)**   	
資料收集器集合工具是 Windows 效能監視器中的重要元件，它是將資料收集器組織成可重複使用的元素，供不同效能監視案例使用的 XML 物件。  	在將一組資料收集器儲存為「資料收集器集合工具」後，便可透過單一屬性變更，將排程等作業套用到整個集合工具。  	您可以排程重複的「資料收集器集合工具」收集作業以建立記錄檔、將它載入 [效能監視器] 以即時方式檢視資料，並將它儲存為範本以便在其他電腦使用。
- **可用於建立記錄的精靈與範本**  	
您也可以透過精靈介面新增計數器至記錄檔，並排程啟動、停止和收集持續時間。此外，您可以將此設定儲存成範本，便可收集後續電腦上相同的記錄，而不需重複資料收集器選取和排程程序。「效能記錄及警示」功能已加入 Windows 效能監視器中，可與任何資料收集器集合工具搭配使用。
- **所有資料收集 (包括排程) 的一致性屬性設定**  	
無論是要建立「資料收集器集合工具」供單次使用或持續地記錄活動，建立、排程和修改的介面都相同。如果證明「資料收集器集合工具」對未來的效能監視很有用，就不需要重新建立。可以將它重新設定或複製成範本。
- **方便使用的診斷報告**  	
Windows Server 2003 [伺服器效能警告器] 的使用者，可以在 Windows 效能監視器中找到相同類型的診斷報告。您可以更快速地產生報告，而且可以從使用任何「資料收集器集合工具」收集的資料產生報告。這讓您可以重複產生報告，以及評估建議的變更對效能的影響或報告的建議。Windows 效能監視器也包含預先設定的效能與診斷報告，可讓您進行快速分析與疑難排解

# 使用效能計數器 (Performance Counters)

## 效能計數器

程式效能的好壞對使用者的感觀影響很大，所以必須依賴有效的評估方法，才能真正找出問題的癥結。利用 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 物件所提供的方法，可輕鬆收集及取得評估的資料。 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 物件的值是儲存在**Windows登錄資料庫**中，除了利用程式碼取得，也可以直接透過PerformanceCounter元件。下圖是PerformanceCounter控制項的屬性欄位。

![](http://127.0.0.1:8080/_images/cs/performance01.png)

Windows有許多內建的效能計數器，幾乎可以評量程式開發人員所關心的所有資源。  效能計數器因電腦上所安裝的應用程式不同而不同，因此必須了解自已電腦上有哪些計數器，以避免重複開發已存在功能的計數器。

## 如何使用系統工具監控效能計數器

首先，我們先看看，在 win7 底下，如何使用系統提供的效能監視器監控效能計數器。

- 1.開啟電腦管理/效能/監視工具/效能監視器
- 2.點選綠色＋字功能鈕以新增計數器。  
![](http://127.0.0.1:8080/_images/cs/performance02.png)
- 3.展開「Processor」這個分類，我們只挑選Processor類別底下的「%Processor Time」這個計數器。
- 4.因為這台機器是４核心，所以底下會有４個例項，我們只挑選0,1二項，按新增，加到右側清單中後，再按確定。這時候就可以在效能監視器看到回傳結果。  
![](http://127.0.0.1:8080/_images/cs/performance03.png)

![](http://127.0.0.1:8080/_images/cs/performance04.png)

## 如何透過程式碼取得效能計數器的資訊

要如何透過程式碼取得以上相關資訊，我們先看看在 [System.Diagnostics](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.aspx) 命名空間底下，與效能監視器相關的類別：

- [**InstanceDataCollectionCollection**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedatacollectioncollection.aspx) ：  
提供 [InstanceDataCollection](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedatacollection.aspx) 物件的強型別集合。  
由 [PerformanceCounterCategory.ReadCategory](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.readcategory.aspx) 方法回傳的集合型別。
- [**PerformanceCounterCategory**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.aspx) ：  
用來管理及操控 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 物件及其分類。  
叫用 [PerformanceCounterCategory.GetCategories](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.getcategories.aspx) 方法，可以取得所有的 [PerformanceCounterCategory](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.aspx) 。
- [**PerformanceCounter**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) ：  
效能計數器類別。透過這個類別，可以讀取相關效能計數器的值。
- [**InstanceDataCollection**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedatacollection.aspx) ：  
提供 [InstanceData](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedata.aspx) 物件的強型別集合。
- [**InstanceData**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedata.aspx) ：  
執行個體資料。
- [**CounterCreationData**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.countercreationdata.aspx) ：  
用來定義效能計數器 ([PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx)) 物件所需的相關屬性。有下列三種屬性：  

- CounterType：計數器的型別
- CounterName：計數器的名稱
- CounterHelp：計數器的說明字串
- [**CounterCreationDataCollection**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.countercreationdatacollection.aspx) ：  
表示 [CounterCreationData](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.countercreationdata.aspx) 物件的強型別 (Strongly Typed) 集合。  
這個集合含有所有的計數器和執行個體 (Instance) 資料。  
集合含有每個計數器的 [InstanceDataCollection](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedatacollection.aspx) 物件。每個 [InstanceDataCollection](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedatacollection.aspx) 物件含有該執行個體所有計數器的效能資料。  
因此，資料要以計數器名稱，再以執行個體名稱建立索引。
- [**CounterSample**](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.countersample.aspx) ：  
效能計數器未經處理資料 (Raw Data) 的結構。

## 使用 PerfomanceCounterCategory 自訂效能計數器分類

[PerformanceCounterCategory](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.aspx) 是用來管理及操控 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 物件及其分類。它提供五個靜態方法來操控效能計數器的分類。  

- Create：建立 PerfomanceCounterCategory。
- Delete：刪除 PerfomanceCounterCategory。
- Exists：判斷 PerfomanceCounterCategory 是否已存在。
- GetCategories：取得所有的 PerfomanceCounterCategory。
- ReadCategory：讀取指定分類下的計數器和效能資料。
```c#
public static PerformanceCounterCategory Create(
string categoryName,                        //分類名稱
string categoryHelp,                        //分類的描述
PerformanceCounterCategoryType categoryType,//PerformanceCounterCategoryType : 指定分類是 MultiInstance、SingleInstance ...
string counterName,                         //新計數器名稱
string counterHelp                          //計數器描述
)
```
```c#
//列舉本機所有 PerformanceCounterCategory
PerformanceCounterCategory[] categories = PerformanceCounterCategory.GetCategories();  //取得所有 PerformanceCounterCategory
foreach (PerformanceCounterCategory category in categories)
{
Console.WriteLine(category.CategoryName);
}

//建立一個 PerformanceCounterCategory
PerformanceCounterCategory DemoCategory = new PerformanceCounterCategory("TestCategory");
if (!PerformanceCounterCategory.Exists("TestCategory"))     //判斷 PerformanceCounterCategory 是否存在
{
PerformanceCounterCategory.Create("TestCategory", "Test Category Desc.", PerformanceCounterCategoryType.SingleInstance, "TestCounter", "Test Counter  Desc.");
}

//刪除 PerformanceCounterCategory
PerformanceCounterCategory.Delete("TestCategory");

//讀取分類中的效能計數器的執行個體
PerformanceCounterCategory pcCategory = new PerformanceCounterCategory("Processor");
InstanceDataCollectionCollection instanceCollection = pcCategory.ReadCategory();
```

![](http://127.0.0.1:8080/_images/cs/performance05.png)

## 使用 PerformanceCounter 讀取效能計數器資料

[PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 建構函式：
```c#
public PerformanceCounter(string categoryName, string counterName);
public PerformanceCounter(string categoryName, string counterName, bool readOnly);
public PerformanceCounter(string categoryName, string counterName, string instanceName)
public PerformanceCounter(string categoryName, string counterName, string instanceName, bool readOnly);
```

在建構 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 之後，透過 [PerformanceCounter](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.aspx) 所提供的方法或屬性，可以取得或變更計數器的值。  

- [RawValue](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.rawvalue.aspx) ：取得或設定計數器的值。
- [NextSample](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.nextsample.aspx) ：取得計數器範例值。
- [NextValue](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.nextvalue.aspx) ：取得計數器樣本值。
- [Increment](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.increment.aspx) ：計數器的值逐一遞增。
- [Decrement](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.decrement.aspx) ：計數器的值逐一遞減。
- [IncrementBy](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.incrementby.aspx) ：計數器的值按指定的數量遞增或遞減。
- [InstanceData.Sample](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedata.sample.aspx) ：InstanceData的效能計數器樣本。
- [InstanceData.RawValue](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.instancedata.rawvalue.aspx) ：取得與效能計數器樣本(sample)相關的未經處理資料的值。

#### 範例一：取得每一個CPU使用率
```c#
PerformanceCounter cpu0 = new PerformanceCounter("Processor", "% Processor Time", "0", true);
PerformanceCounter cpu1 = new PerformanceCounter("Processor", "% Processor Time", "1", true);
PerformanceCounter cpu2 = new PerformanceCounter("Processor", "% Processor Time", "2", true);
PerformanceCounter cpu3 = new PerformanceCounter("Processor", "% Processor Time", "3", true);
PerformanceCounter cpu_all = new PerformanceCounter("Processor", "% Processor Time", "_Total", true);

//設定每秒取樣一次
private void timer2_Tick(object sender, EventArgs e)
{
Console.WriteLine("{0} {1} {2} {3} {4} {5}", string.Format("{0:HH:mm:ss }", DateTime.Now), 
cpu0.NextValue(), cpu1.NextValue(), cpu2.NextValue(), cpu3.NextValue(), pcCPU.NextValue());

//Time       cpu0  cpu1  cpu2  cpu3 cpu_all
//========  ===== ===== ===== ===== ======
//09:31:28  29.23     0  4.61     0   8.46
//09:31:29  46.15 12.31 44.62  9.23  28.08
//09:31:30  10.77 24.62 30.77     0  16.54
//09:31:31   3.08    20  9.23     0   8.08
//09:31:32   7.69  9.23     0     0   4.23
//09:31:33  13.85 16.92 18.46     0  12.31
//09:31:34  16.92  1.54 10.77     0   7.31
//09:31:35  27.69 13.85 15.38  3.08     15
//不曉得為什麼 "_Total"這個instance的總和不等於前面4個加起來的
//直接使用系統的效能監視器觀察也同樣是這個樣子.
//但觀察數值,好像 total = ( cpu0 + cpu1 + cpu2 + cpu3 ) / 4 
}
```

#### 範例二：透過InstanceData取值
```c#
//從 PerformanceCounterCategory.ReadCategory 這個方法傳回的集合，含有所有的計數器和執行個體 (Instance) 資料。
//集合含有每個計數器的 InstanceDataCollection 物件。
//每個 InstanceDataCollection 物件含有該執行個體所有計數器的效能資料。
//因此，資料要以計數器名稱，再以執行個體名稱建立索引。

//判斷是否存在 Processor 這個 PerformanceCounterCategory
if (PerformanceCounterCategory.Exists("Processor"))
{
// 取得名稱為 Processor 的 PerformanceCounterCategory 的執行個體
PerformanceCounterCategory category = new PerformanceCounterCategory("Processor");

// 讀取這個 PerformanceCounterCategory 裡的所有計數器和效能物件執行個體資料。
InstanceDataCollectionCollection idColCol = category.ReadCategory();

foreach (DictionaryEntry idCol in idColCol)
{
Console.WriteLine("Counter = {0}", idCol.Key);

foreach (DictionaryEntry entry in (InstanceDataCollection)idCol.Value)
{
InstanceData instData = (InstanceData)entry.Value;
Console.WriteLine("  InstanceName = {0}, RawValue = {1}", instData.InstanceName, instData.Sample.RawValue);
}
}
}
//Counter = c2 transitions/sec
//  InstanceName = 0, RawValue = 0
//  InstanceName = 1, RawValue = 0
//  InstanceName = 2, RawValue = 0
//  InstanceName = 3, RawValue = 0
//  InstanceName = _Total, RawValue = 0
//Counter = % c2 time
//  InstanceName = 0, RawValue = 0
//  InstanceName = 1, RawValue = 0
//  InstanceName = 2, RawValue = 0
//  InstanceName = 3, RawValue = 0
//  InstanceName = _Total, RawValue = 0
//Counter = interrupts/sec
//  InstanceName = 0, RawValue = 10460733
//  InstanceName = 1, RawValue = 5012298
//  InstanceName = 2, RawValue = 6024041
//  InstanceName = 3, RawValue = 6017372
//  InstanceName = _Total, RawValue = 27514444
//Counter = % c1 time
//  InstanceName = 0, RawValue = 1064119780
//  InstanceName = 1, RawValue = 410456782
//  InstanceName = 2, RawValue = 1322741070
//  InstanceName = 3, RawValue = 448465920
//  InstanceName = _Total, RawValue = 811445888
//  ......
```

## 建立自訂的效能計數器

要建立效能計數器的方法，要用 [PerformanceCounterCategory.Create](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.create.aspx) 方法，這跟建立分類是同一個方法。  Create 共有二個多載方法，可以用來建立效能計數器。  上面範例使用到的方法，是在建立分類時，同時建立一個計數器。下面範例則是在建立分類時，同時建立多個計數器。  

[PerformanceCounterCategory](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecountercategory.aspx) 建構函式：
```c#
public static PerformanceCounterCategory Create(
string categoryName,
string categoryHelp,
PerformanceCounterCategoryType categoryType,
CounterCreationDataCollection counterData
)
```

[CounterCreationData](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.countercreationdata.aspx) 建構函式：
```c#
public CounterCreationData(string counterName, string counterHelp, PerformanceCounterType counterType)
```

#### 範例三：建立自訂的效能計數器
```c#
//建立 CounterCreationData 集合
CounterCreationDataCollection counterData = new CounterCreationDataCollection();
counterData.Add(new CounterCreationData("Sales", "Number of total sales", PerformanceCounterType.NumberOfItems64));
counterData.Add(new CounterCreationData("Active Users", "Number of active users", PerformanceCounterType.NumberOfItems64));

//建立自訂效能計數器分類 , 同時包含指定計數器。
PerformanceCounterCategory.Create("MyApp Counters","Counters describing the performance of MyApp", PerformanceCounterCategoryType.SingleInstance, counterData);
```
**注意事項**  
若想透過程式碼建立自訂的效能計數器到分類中，只能加到自訂的分類之中，無法新增至內建的分類。  
而且，Create 方法也沒有提供在現有的分類中，加入計數器的方法。  
所以，若想在現有分類中加入計數器，只能刪除分類，再重新建立分類及所有計數器。

## 提供資料給效能計數器

建立效能計數器之後，我們就可以在程式碼之中，隨時更新這個計數器的值。  底下這個範例，我們使用一個 Timer ，每 400ns 隨機更新資料，再透過系統的效能監視器進行觀查。  

#### 更新效能計數器的注意事項：

因為同一時間可能會有多個 Thread 同時存取效能計數器，所以要避免直接更新 [RawValue](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.rawvalue.aspx) ，要使用執行緒安全的方法去更新 [RawValue](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.performancecounter.rawvalue.aspx) 的值。  

#### 範例四：提供資料給效能計數器
```c#
private void timer3_Tick(object sender, EventArgs e)
{
string categoryName = txtCategoryName.Text;
string counterName = txtCounterName.Text;

PerformanceCounter pc = new PerformanceCounter(categoryName, counterName, false);
if (pc.RawValue == 0)
pc.RawValue = 50;           //直接給定一個值
else
{
Random random = new Random();
int rawValue = random.Next(1, 30) - 15;
pc.IncrementBy(rawValue);       //使用 Atomic 方法，更新 RawValue 
}
//pc.RawValue = 7;
//pc.Decrement();
//pc.Increment();
//pc.IncrementBy(3);
}
```

下面這張圖表，就是使用系統提供的效能監視器，追蹤我們自訂的效能計數器的結果。  

![](http://127.0.0.1:8080/_images/cs/performance06.png)