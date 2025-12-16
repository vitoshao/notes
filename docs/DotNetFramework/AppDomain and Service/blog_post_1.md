---
title: Windows Service
layout: default
parent: AppDomain and Service
nav_order: 1
description: "Windows Service"
date: 2012-03-06
tags: [DotNetFramework,AppDomain and Service]
postid: "510432350192602658"
---
Microsoft Windows 服務，也就是先前的 NT 服務，可讓您建立長期執行的應用程式，在應用程式本身的 Windows 工作階段 (Session) 中執行。   這些服務可以在電腦啟動時自動啟動，也可以暫停或重新啟動，都不會顯示任何使用者介面。   這些功能使得服務非常適合在伺服器中使用，或每當需要不干擾使用同一部電腦之其他使用者的長期執行功能時使用。   您也可以在不同於登入使用者或預設電腦帳戶的特定使用者帳戶的安全性內容下執行服務。  

# 建立服務程式

## 瞭解先

在設計服務程式之前，首先要瞭解：

1. 服務程式無法使用 F5 或 F11 進行偵錯。
2. 服務程式雖然是.exe，但是無法直接執行；必須替服務程式建立安裝元件。  
安裝元件會負責執行安裝與註冊工作，並且在 Winodws 的「服務控制管理員」( Services Control Manager )建立一個服務項目。[服務控制管理員] 是 Windows 用來管理服務的公用程式。
3. 服務程式由是 Main() 進入，在此區段使用 Run() 方去指定哪些服務要載入至「服務控制管理員」。
4. 服務程式不同視窗程式，訊息應該記錄在 Windows 事件記錄檔中，或以檔案等形式記錄，而不是產生在使用者介面中。
5. 服務程式必須指定在某個帳戶權限下執行，所以儘可能給予該帳戶最小的權限，以降低潛在的危險。
6. 即使該帳戶沒有登入，服務程式仍可隨電腦而啟動。
```c#
static class Program
{
// 應用程式的主要進入點。
static void Main()
{
ServiceBase[] ServicesToRun;
ServicesToRun = new ServiceBase[] 
{ 
//建立要載入到「服務控制管理員」的執行個體
new Service1() 
};
ServiceBase.Run(ServicesToRun);
}
}

public partial class Service1 : ServiceBase
{
public Service1()
{
InitializeComponent();
}

protected override void OnStart(string[] args) { }
protected override void OnStop() { }
protected override void OnPause() { }
protected override void OnContinue() { }
protected override void OnShutdown() { }
}
```
&lt;!-- more --&gt;    
## 建立服務程式的步驟

#### 1. 新增一個服務專案

![](http://127.0.0.1:8080/_images/cs/WinServicePrj.png)

服務專案範本預設會自動建立2個檔案

- Program.cs ：這是應用程式的主要進入點。必須在 Main() 區段裡，將要載入的服務透過 [Run()](http://msdn.microsoft.com/zh-tw/library/6esb073b.aspx) 方法登錄。
- Serivce1.cs：這是服務程式類別檔。裡面主要定義了一個繼承自 [ServiceBase()](http://msdn.microsoft.com/zh-tw/library/5xh5cfw0.aspx) 的服務程式類別。

#### 2. 設定服務程式的名稱

開啟服務程式的 Designer 模式，就可以在其屬性視窗中編輯服務程式的名稱。如下圖：

![](http://127.0.0.1:8080/_images/cs/service_can_pause.jpg)

#### 3. 在服務程式類別中的 [OnStart](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onstart) 與 [OnStop](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onstop) 撰寫程式碼

[OnStart](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onstart) 、  [OnStop](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onstop) 、  [OnPause](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onpause) 、  [OnContinue](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.oncontinue)  這四個方法分別是服務程式狀態改變時的事件，可視需求在事件中撰寫程式碼。  例如，在 [OnStart](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onstart) 方法之中，使用 Timer 或 FileSystemWatcher 等機制，設計一些監看或輪詢的程式。  

另外，要注意的是，若要讓服務支援暫停功能，則必須先將屬性 [CanPauseAndContinue](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.canpauseandcontinue) 設為 true，然後覆寫 [OnPause](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.onpause) 和 [OnContinue](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicebase.oncontinue) 方法。（參考上圖）  
```c#
public partial class Service1 : ServiceBase
{
public Service1()
{
InitializeComponent();
}

protected override void OnStart(string[] args) { }
protected override void OnStop() { }
protected override void OnPause() { }
protected override void OnContinue() { }
protected override void OnShutdown() { }
}
```

# 在服務程式中加入「安裝程式(installer)」

### 1. 加入安裝程式

開啟服務程式的設計檢視，在右鍵選單中按一下「加入安裝程式」

![](http://127.0.0.1:8080/_images/cs/service_add_install.jpg)

這時系統會自動建立一個 ProjectInstaller 類別檔，並加入以下二個元件

- [ServiceProcessInstaller](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceprocessinstaller) ：設定執行這個服務程式的帳戶。
- [ServiceInstaller](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceinstaller) ：設定服務名稱、顯示名稱及啟動類型。

![](http://127.0.0.1:8080/_images/cs/service_2install_object.png)

### 2. 設定 ServiceInstall 元件的屬性

- [StartType](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceinstaller.starttype) ：服務啟動的方式。{自動/手動/停用}
- [Description](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceinstaller.description) ：設定服務的描述。
- [DisplayName](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceinstaller.displayname) ：使用者識別服務的易記名稱。

![](http://127.0.0.1:8080/_images/cs/serviceInstaller-properties.png)

對應到「服務管理員」中的欄位

![](http://127.0.0.1:8080/_images/cs/serviceInstaller-loaded.png)

### 3. 設定 ServiceProcessInstaller 元件的屬性

這個步驟主要要設定 [ServiceProcessInstaller](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceprocessinstaller) 元件的 [Account](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceprocessinstaller.account.aspx) 屬性。  

- **本機服務帳戶** (LocalService)：本機服務帳戶是一個特殊的內建帳戶，類似已驗證的使用者帳戶。  使用這個帳戶執行服務，相當於使用本機電腦上不具權限的使用者。若要對其他遠端伺服器連線，則使匿名憑證。使用 LocalService 可以降低安全上的風險。
- **網路服務帳戶** (NetworkService)：網路服務帳戶是一個特殊的內建帳戶，類似已驗證的使用者帳戶。  使用這個帳戶執行服務，它會使用使用者群組的權限存取資源。  它可以讓服務對網路上的其他電腦做驗證。如果是匿名連線，就不需要這個驗證，例如連接到網頁伺服器的大部份連線。
- **本機系統帳戶** (LocalSystem)：服務以幾乎無任限制的權限執行，並且可向其他任何遠端伺服器提出此電腦的憑證。這個帳戶類型會產生嚴重的安全風險，若你的應用程式中有任何弱點存在，很容易就可以被利用而完全控制使用者電腦。
- **使用者** (User)：此為預設值。當系統在安裝服務時，會提示要求有效的使用者名稱和密碼。  
例如：以本機管理員帳號啟動「.\administrator」。  
另外，你也可以設定 [ServiceProcessInstaller](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.serviceprocessinstaller) 執行個體的 Username 和 Password 的屬性值，就會直接以這個帳戶啟動。

### 4. 設定服務專案的「啟始物件」

在「方案總管」中，以滑鼠右鍵按一下專案，從右鍵選單中按一下「內容」，再由下圖中，從「啟始物件」清單中選擇你的服務專案。

![](http://127.0.0.1:8080/_images/cs/service_startobject.png)

### 5. 建置

完成以上步驟後，就可以進行建置專案。  以上步驟中的加入「安裝程式」，其目的就是要讓程式在進行安裝的時候，系統可以自動幫我們將服務程式註冊到「服務管理員」中。  若少了這個動作，執行 Setup 安裝時，服務程式只會被安裝到指定的目錄，並不會被註冊到「服務管理員」中。

# 安裝服務程式

我們知道服務程式必須被安裝且註冊到「服務控制管理員」才能正常運作。  要執行這個作業有二個方法：一是使用安裝公用程式 InstallUtil.exe 進行安裝；二是建立安裝專案打包服務程式。  

## 使用安裝公用程式 InstallUtil.exe 安裝服務程式

當服務程式加入安裝程式後，其編譯出來的組件，就具有「安裝能力」，我們只要透過 .Net Framework 提供的工具 [InstallUtil.exe](http://msdn.microsoft.com/zh-tw/library/50614e95.aspx) ，就可以將服務順利安裝至系統內。例如：  
```c#
InstallUtil.exe yourservice.exe      //安裝服務
InstallUtil.exe /u yourservice.exe   //解除安裝
```

When InstallUtil.exe runs, the utility looks for classes in the service assembly with the RunInstallerAttribute set to true.   Add classes to the service assembly by adding them to the Installers collection associated with your project installer.   If RunInstallerAttribute is false, the install utility ignores the project installer.

## 使用「安裝專案」打包服務程式

### 1. 加入「安裝專案」

在方案中加入一個「安裝專案」

![](http://127.0.0.1:8080/_images/cs/service_setup.png)

### 2. 加入「專案輸出」

在安裝專案中，點選「加入」-「專案輸出」，專案輸出指的就是要打包的專案

![](http://127.0.0.1:8080/_images/cs/service_output.jpg)

從「專案：」的清單中挑選要輸出的服務專案，並設定成「主要輸出」，再按一下「確定」。

![](http://127.0.0.1:8080/_images/cs/service_output2.jpg)

### 3. 加入「自訂動作」

最後，加入一個「自訂動作 (custom action)」 以進行服務程式的安裝，步驟如下：

- A. 在專案的右鍵選單中，點選「檢視」，再點選「自訂動作」。  
![](http://127.0.0.1:8080/_images/cs/service_setup_custom_action.jpg)
- B. 在「自訂動作」編輯器中，使用右鍵選單加入「自訂動作」。  
![](http://127.0.0.1:8080/_images/cs/service_add_custom_action.jpg)
- C. 在「選取專案中項目」對話視窗中選取「應用程式資料夾」  
![](http://127.0.0.1:8080/_images/cs/service_add_custom_action1.jpg)  
上面畫面按下確定後，會出現以下畫面，這個時候，服務專案的程式已被加入到安裝專案，若還有其他檔案要加入，也可以在這個步驟中加入。  
![](http://127.0.0.1:8080/_images/cs/service_add_custom_action2.jpg)  
按下確定後，主要輸出會被加入自訂動作中的所有四個節點：安裝(Install)、認可(Commit)、復原(Rollback)、解除安裝(Uninstall)  
![](http://127.0.0.1:8080/_images/cs/service_add_custom_action3.jpg)

### 4. 建置安裝專案

當建置完成之後，就可以找到 setup.exe ，它可以用互動的方式安裝服務。  另外還有一個 MSI 檔，它可以自動部署此服務。

# 啟動服務程式

載入服務後，必須啟動該服務。服務才會開始運作。要啟動服務可以使用底下幾個方法：

## 由「服務控制管理員」介面啟動或停用服務

略

## 由指令去啟動或停用服務

註冊服務之後，可以直接在 Commnad 視窗中透過命令啟動服務。例如：
Net Start ServiceName      //啟動服務
Net Stop ServiceName       //停止安裝

## 由程式去啟動或停用服務

過透 [ServiceController](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess.servicecontroller.aspx) 類別，就可以連結到指定的服務。  
這個類別需要 [System.ServiceProcess](http://msdn.microsoft.com/zh-tw/library/system.serviceprocess) (必須手動加入此參考) 與 [System.Threading](http://msdn.microsoft.com/zh-tw/library/system.threading) 命名空間。  
```c#
public ServiceController();
public ServiceController(string name);
public ServiceController(string name, string machineName);
```
```c#
private void btnService_Click(object sender, EventArgs e)
{
// 建立 ServiceController 連接到指定機器的指定服務
ServiceController service = new ServiceController("Service1");  //指定服務名稱
service.MachineName = ".";                                      //指定機器的名稱

if (btnService.Text == "Stop")
{
if (service.Status != ServiceControllerStatus.Stopped)
{
service.Stop();         // 停止服務
}
}
if (btnService.Text == "Start")
{
if (service.Status != ServiceControllerStatus.Running)
{
service.Start();        // 啟動服務
}
}
service.Refresh();
}
```

PS. ServiceName 是服務的名稱，通常服務還有一個顯示名稱，不要搞混了。

## 由程式去啟動或停用服務

自已終結自已的方法
```c#
new ServiceController(this.ServiceName).Stop();
```

#### 補充：

服務程式，在一般狀況下無法 Debug，必須動點手腳，可參考以下幾篇介紹：

- [HOW TO：偵錯 Windows 服務應用程式](http://msdn.microsoft.com/zh-tw/library/7a50syb3%28VS.80%29.aspx)
- [如何對 \[Windows 服務\] 與 \[安裝專案的自訂動作\] 進行除錯](http://blog.miniasp.com/post/2009/04/05/How-to-debugging-Windows-Service-and-Setup-Project-Custom-Action.aspx)
- [Debugging Windows Service Without Deploying It](http://www.codeproject.com/Articles/21887/Debugging-Windows-Service-Without-Deploying-It)

## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [Windows 服務應用程式簡介](http://msdn.microsoft.com/zh-tw/library/d56de412%28v=vs.100%29.aspx)
- 
-