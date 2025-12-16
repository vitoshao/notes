---
title: 建立AppDomain
layout: default
parent: AppDomain and Service
nav_order: 3
description: "建立AppDomain"
date: 2012-03-06
tags: [DotNetFramework,AppDomain and Service]
postid: "239830301077430182"
---
# 什麼是應用程式定義域 ( application domain )

一般當執行一個應用程式的時候，系統會建立一個新的**處理序** (process)，不同的處理序，其記憶體的管理或資源的存取基本上是獨立的。  當這個程式需要執行外部應用程式(組件)，它可以直接啟動這個外部組件，但這樣做，系統也會建立一個新的處理序，這麼做除了會多耗用資源外，也有安全上的弱點(security vulnerabilities)。  要避免這些風險的最好方法就是建立一個有限權限的 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) ，然後將要執行的這個外部組件載入這個 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 中執行。  

#### 應用程式定義域 (AppDomain)

- AppDomain 是一個邏輯的儲存空間，允許在單一個處理序(process)中，執行多個組件(assembly)。
- 每一個組件的執行個體，其記憶體空間和資源都是獨立的，無法彼此直接存取。
- IIS 中，ASP.NET 會為每個使用者分別建立一個 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 。
- AppDomain 提供了如 process 的許多功能，但是不用花費建立 process 的額外負載。
- 某一個 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 即使掛了，也不會引響其他的 AppDomian 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZm1BhEi5b3rgKAKNEZfflLXMlGlIv9z6M-2JcCaOZuofdYth-hFXPn-1236fioq9t603c0KFgcq2r-mKrpPVFl2IRZEU0ZBSd5Hr45p_eAmq2BZbkEDd95b9kn9XpREB95VPEUDZQZPQ/s444/AppDomain.png)

AppDomain 的最佳範例為 IIS 的 ASP.NET 工作處理序 (worker process， w3wp.exe)。  如果有 10 個人同時瀏覽某個網站，ASP.NET 會為每個使用者分別建立一個 AppDomain。基本上，ASP.NET 會執行同一個組件的 10 個不同執行個體。  

該組件的每一個 instance 都可以分別儲存一個稱為 userName 的屬性，不需要擔心其他的 instance 是否會存取到自已的內容。  

IIS 中的 Application Pool 是IIS用來管理 Web 應用程式的機制，透過 app pool 的設定加以控制程式所要使用的 .NET 版本。  一個 app pool 啟動一個 process ，底下可以管轄多個 web 應用程式，而每一個 web 應用程式通常只有一個 app domain ，對連線使用者而言，則為相同組件，但各自獨立的 instance 。

一般程式載入時，會由執行階段主機 (run-time  host) 自動幫我們建立 AppDomain。  像Asp.Net、IE、windows本身都是執行階段主機。  它們會自動建立 AppDomain，但是我們也可透過「IIS」或 「.Net Framework 組態工具」來設定這些 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 的行為

# AppDomain 類別

應用程式定義域是使用 [System.AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 類別進行實作，下表是幾個重要的方法或屬性：。  

#### AppDomain 屬性

- [ActivationContext](http://msdn.microsoft.com/zh-tw/library/system.appdomain.activationcontext.aspx) ：取得目前應用程式定義域的啟動內容(activation context)。
- [ApplicationIdentity](http://msdn.microsoft.com/zh-tw/library/system.appdomain.applicationidentity.aspx) ：取得應用程式定義域中的應用程式之識別。
- [ApplicationTrust](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup.applicationtrust.aspx) ：取得 [ApplicationTrust](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup.applicationtrust.aspx) 資訊，該資訊描述授與給應用程式的使用權限，以及該應用程式是否有允許其執行的信任層級。
- [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence.aspx) ：取得與此應用程式定義域相關聯的 Evidence。
- [Id](http://msdn.microsoft.com/zh-tw/library/system.appdomain.id.aspx) ：取得可唯一識別處理序中之應用程式定義域的整數。
- [CurrentDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.currentdomain.aspx) ：取得目前 Thread 的目前應用程式定義域。
- [SetupInformation](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setupinformation.aspx) ：取得這個執行個體的 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 組態資訊。

#### AppDomain 方法

- [CreateDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.createdomain.aspx) ：建立新的應用程式定義域。
- [Unload](http://msdn.microsoft.com/zh-tw/library/system.appdomain.unload.aspx) ：卸載指定的應用程式定義域。
- [ExecuteAssembly](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassembly.aspx) ：在這個AppDomain中，依檔案名稱，執行組件。
- [ExecuteAssemblyByName](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassemblybyname.aspx) ：在這個 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 中，依組件名稱，執行組件。（一般是用來執行 GAC 中的組件）
- [GetAssemblies](http://msdn.microsoft.com/zh-tw/library/system.appdomain.getassemblies.aspx) ：這個在這個應用程式定義域內執行的組件資訊。回傳值是 Assembly[] 型別。
- [Load](http://msdn.microsoft.com/zh-tw/library/system.appdomain.load.aspx) ：將 Assembly 載入這個應用程式定義域中。它可以當成 Assembly.Load 的替代方法。回傳值是 Assembly 型別。

## 如何建立AppDomain
```c#
public static AppDomain CreateDomain(string friendlyName);
public static AppDomain CreateDomain(string friendlyName, Evidence securityInfo);
public static AppDomain CreateDomain(string friendlyName, Evidence securityInfo, AppDomainSetup info);
...
```

底下程式碼示範，如何在 Host Domian 之中，建立一個 Child AppDomian ，以及將組件載入到 Child [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 之中。

#### 使用 [AppDomain.CreateDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.createdomain) 方法建立 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 
```c#
//建立AppDomain
AppDomain myDomain = AppDomain.CreateDomain("NewDomain");
Console.WriteLine("Host domain: " + AppDomain.CurrentDomain.FriendlyName);
Console.WriteLine("Child domain: " + myDomain.FriendlyName);
//Host domain: PracticeMCTS.vshost.exe
//Child domain: NewDomain
```

####  [AppDomainSetup](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup.aspx) 類別

- [AppDomainSetup](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup.aspx) 類別是用來記錄 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 的組態資訊。
- 設定 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 屬性：當 [CreateDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.createdomain.aspx) 時，可以傳入一個 [AppDomainSetup](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup.aspx) 參數，以建立適當屬性的 AppDomain。
- 檢視 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 屬性：透過 [AppDomain.SetupInformation](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setupinformation) ，可以取得 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 的組態資訊。
```c#
AppDomainSetup domainProperty = new AppDomainSetup();
domainProperty.ApplicationBase = "file://" + System.Environment.CurrentDirectory;  //取得應用程式的目錄名稱。
domainProperty.DisallowBindingRedirects = false;                                   //是否允許組件繫結重新導向
domainProperty.DisallowCodeDownload = true;                                        //是否允許以 HTTP 下載組件
domainProperty.ConfigurationFile = AppDomain.CurrentDomain.SetupInformation.ConfigurationFile;   //設定 AppDomain 的組態檔的位置

// 建立新的應用程式定義域，並以 AppDomainSetup 設定 AppDomain 的屬性。
AppDomain newDomain = AppDomain.CreateDomain("New Domain", null, domainProperty);  
```
```c#
AppDomainSetup domainProperty2 = AppDomain.CurrentDomain.SetupInformation;  //取得目前應用程式定義域的屬性
Console.WriteLine(domainProperty2.ApplicationBase);
Console.WriteLine(domainProperty2.ApplicationName);
Console.WriteLine(domainProperty2.DisallowCodeDownload);
Console.WriteLine(domainProperty2.DisallowBindingRedirects);
Console.WriteLine(domainProperty2.ConfigurationFile);

//D:\......\bin\Debug\
//PracticeMCTS.vshost.exe
//False
//False
//D:\......\bin\Debug\PracticeMCTS.vshost.exe.Config
```

####  [ApplicationSecurityInfo](http://msdn.microsoft.com/zh-tw/library/system.security.policy.applicationsecurityinfo.aspx) 類別

這個類別記錄了應用程式的安全性識別項 (security evidence)。

透過以下方法，可以取得啟動應用程式的安全性資訊。
```c#
// ApplicationSecurityInfo 
// 使用從應用程式的資訊清單及其 ActivationContext 所取得的資訊，提供關於啟動資訊清單的應用程式之安全性資訊。

ApplicationSecurityInfo appInfo = new ApplicationSecurityInfo(AppDomain.CurrentDomain.ActivationContext);
Console.WriteLine(appInfo.ApplicationId.Name);
Console.WriteLine(appInfo.ApplicationId.Version);
Console.WriteLine(appInfo.ApplicationId.Culture);
Console.WriteLine(appInfo.DefaultRequestSet.ToString());
```

####  ApplicationSecurityManager 類別

這個類別提供執行以資訊清單為基礎的應用程式之基本資訊。包含下列三個屬性：

- ApplicationTrustManager：取得目前的應用程式信任管理員
- DetermineApplicationTrust：判斷使用者是否核准指定的應用程式以要求的使用權限集合來執行。
- UserApplicationTrusts：取得應用程式信任集合，其中包含使用者的快取信任決策。
```c#
ApplicationTrustCollection trusts = ApplicationSecurityManager.UserApplicationTrusts;
foreach (ApplicationTrust trust in trusts)
{
Console.WriteLine(trust.ApplicationIdentity.FullName);
}

var obj = ApplicationSecurityManager.ApplicationTrustManager;
```

## 如何載入組件到 AppDomain

#### 使用 [AppDomain.ExecuteAssembly](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassembly) 方法載入組件
```c#
public int ExecuteAssembly(string assemblyFile);
public int ExecuteAssembly(string assemblyFile, string[] args);
public int ExecuteAssembly(string assemblyFile, Evidence assemblySecurity);
public int ExecuteAssembly(string assemblyFile, Evidence assemblySecurity, string[] args);
```
```c#
//載入組件到建立的AppDomain中
myDomain.ExecuteAssembly(@"D:\myBlog\TestProj\TestForAppDomain\TestForAppDomain\bin\Debug\TestForAppDomain.exe");

//載入組件到建立的AppDomain中 並傳入命令列參數值
string[] args = new string[] { "A", "B", "C" };
myDomain.ExecuteAssembly(@"D:\myBlog\TestProj\TestForAppDomain\TestForAppDomain\bin\Debug\TestForAppDomain.exe", args);
```

#### 使用 [AppDomain.ExecuteAssemblyByName](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassemblybyname) 方法載入組件
```c#
public int ExecuteAssemblyByName(string assemblyName);
public int ExecuteAssemblyByName(string assemblyName, params string[] args);
public int ExecuteAssemblyByName(string assemblyName, Evidence assemblySecurity);
public int ExecuteAssemblyByName(string assemblyName, Evidence assemblySecurity, params string[] args);
public int ExecuteAssemblyByName(AssemblyName assemblyName, params string[] args);
public int ExecuteAssemblyByName(AssemblyName assemblyName, Evidence assemblySecurity, params string[] args);
```
```c#
//若組件已經建立參考，可以用組件名稱，將組件載入到建立的AppDomain中
myDomain.ExecuteAssemblyByName("TestForAppDomain");
```

## 如何列舉 AppDomain 中的組件

#### 使用 [AppDomain.GetAssemblies](http://msdn.microsoft.com/zh-tw/library/system.appdomain.getassemblies) 方法列舉 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.aspx) 中的組件
```c#
//列舉 AppDomain 下的所有組件
Assembly[] asms = myDomain.GetAssemblies();
foreach (Assembly asm in asms)
{
Console.WriteLine(asm.FullName);
}
//mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
//Microsoft.VisualStudio.HostingProcess.Utilities, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a
//TestForAppDomain, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
//System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
//System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
//System.Drawing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a
//Accessibility, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a
```

## 如何卸載 AppDomain
```c#
//卸載AppDomain
AppDomain.Unload(myDomain); 
```