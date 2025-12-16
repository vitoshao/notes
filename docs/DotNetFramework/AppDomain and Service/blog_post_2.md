---
title: 設定AppDomain組態
layout: default
parent: AppDomain and Service
nav_order: 2
description: "設定AppDomain組態"
date: 2012-03-06
tags: [DotNetFramework,AppDomain and Service]
postid: "3097005207567191537"
---
# 前言

當建立一個 AppDomain 時，我們也可以修改它的屬性設定以適應要載入的組件。  例如，當我們想載入一個不明的組件到某個 AppDomain ，我們可以建立一個較小使用權限的 AppDomain ，以降低安全性弱點的風險。   AppDomain 不僅可以提供隔離單位，還可以降低被攻擊時所可能造成的損害。  尤其當呼叫組件執行時，適當的限制 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 的權限，將可大大減少惡意行為風險的發生。  例如，當我們使用一個外購的組件進行指定目錄的備份監控，要如何肯定這個程式不會讀取其他目錄的資料呢？或者如何提防它是否會透過網路將資料外傳呢？  反之，當我們設計一個組件，若沒有做任何安全性的限制，它會不會變成漏洞，反而被其他組件拿來當成跳板，執行不該有的行為呢？

# 如何使用 AppDomain 限制 Assembly 啟動時的權限

要限制 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 組態，有許多方式，本節僅要說明如何使用[識別項 (Evidence)](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 來設定 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 的組態。  

.NET Framework針對程式碼在執行時，內建三個安全性原則(Policy)：企業、電腦及使用者。  每個原則下管理著一群程式碼群組(Code Group)，每個程式碼群組繫結一個權限集合，整個展開如下圖  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7Wa-3y3msxKGnTuU0vB3Q8K9YhhefQYFn4o0XoVISsLCjQTuOZvmjAZSvFADNKUs78Yn8naQS1oIEfMI4u8DrYSUJP01a7AF6LvMn4bNI439uCLxZgFRHfw_hVpjFQseQroRXcdzUgPk/s828/CAS04.png)

PS.程式碼存取安全性 (CAS) 的原則部分在 .NET Framework 4 版中已經過時，不再適用。  因此，如果以明確或隱含方式 (透過其他類型和成員) 呼叫過時的原則類型與成員，您可能會遇到編譯警告與執行階段例外狀況。  詳情請參考[程式碼存取安全性原則相容性和移轉](http://msdn.microsoft.com/zh-tw/library/ee191568%28v=VS.100%29.aspx)。  

## 辨識項（Evidence）

[辨識項（Evidence）](http://msdn.microsoft.com/zh-tw/library/69f1bzsz.aspx)是構成安全性原則的資訊集，定義了組件在執行階段的安全性原則，簡單講就是**指明組件是屬於哪些程式碼群組**。  換句話說，就是程式碼群組控制了組件的權限。若要限定組件的使用權限，就可以指派一個 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 給組件。  

當建立 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) ，並載入某個組件執行，這個時候會擁有對該組件 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 的完全控制權。   [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 記載了一些資訊，指明該組件屬於哪些程式碼群組 (Code Group)，而程式碼群組則可以決定該組件的權限。  

通用的 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 裏，記載了組件是從哪個資料夾或網站執行，以及數位簽章。  不過 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 其實可以是由任何型別的物件構成，如數字，字串，自訂型別等。  只要將這些型別，以物件陣列 (object[]) 的型式組成，就可以建構 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 類別。  

#### Evidence Class
```c#
[SerializableAttribute]
[ComVisibleAttribute(true)]
public sealed class Evidence : ICollection, IEnumerable
```

#### Evidence Consturctor
```c#
public Evidence(
Object[] hostEvidence,      //hostEvidence    ：用來建立新執行個體的主應用程式辨識項。
Object[] assemblyEvidence   //assemblyEvidence：用來建立新執行個體的組件辨識項。
)
```
```c#
// Evidence 由 object [] 構成，
// 而 object [] 內可以是任何型別
object[] hostEvidence = { new Zone(SecurityZone.Internet) , 10, "test" };
Evidence ev1 = new Evidence(hostEvidence, null);
ev1.AddHost(new Uri("http://www.microsoft.com"));
```

Evidence 可以指定給 Assembly 或 AppDomain。  
若要指定給 Assembly，就在 [AppDomain.ExecuteAssembly](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassembly) 時指定；  
若要指定給 AppDomain，就在 [AppDomain.CreateDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.createdomain) 時指定。  

## 如何提供主機辨識項給組件

透過指派 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 給某個組件，就可以控制該組件的使用權限。  若要指派 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 給某個組件，必須先建立一個 [System.Security.Policy.Evidence](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 物件，然後將它傳給 [AppDomain.ExecuteAssembly](http://msdn.microsoft.com/zh-tw/library/system.appdomain.executeassembly.aspx) 。  

下面這段程式碼將示範：  如何將指定的組件限定在一個獨立的 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 中執行，並且僅授與 Internet\_Zone 程式碼群組的使用權限集合。  當 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 啟動該組件時，系統會根據它提供的 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 內容，將它指派給 Internet\_Zone 這個程式碼群組。  [Zone evidence](http://msdn.microsoft.com/zh-tw/library/4tzebk3b.aspx) 是.Net內建的一種辨識項，定義了極嚴格的 Internet 使用權限集，我們可以將它指派給 AppDomian 中的組件，以限定組件的權限。  
```c#
public enum SecurityZone
{
NoZone = -1,        //未指定區域。
MyComputer = 0,     //本機電腦區域
Intranet = 1,       //近端內部網路區域
Trusted = 2,        //信任的網站區域
Internet = 3,       //網際網路區域
Untrusted = 4,      //限制的網站區域，需要將未受信任網站的 URL 對應至這個區域。
}
```
```c#
//=====================================================================================
//將組件建立隔離在一個叫 NewDomain 的 AppDomain 之下，並授與 Internet_Zone 程式碼群組的使用權限集合。 
//=====================================================================================

//1)建立主機辨識項
object[] codeGroup = { new Zone(SecurityZone.Internet) };  //建立包含 Internet_Zone 的程式碼群組
Evidence hostEvidence = new Evidence(codeGroup, null);

//2)建立AppDomain
AppDomain newDomain = AppDomain.CreateDomain("NewDomain");

//3)執行組件，並指定一個 Evidence 物件
newDomain.ExecuteAssembly(@"D:\MCTS\MCTS2011\Practice\WindowsFormsApplication1\bin\Debug\WindowsFormsApplication1.exe", hostEvidence);
```

上面程式碼，由於[ExecuteAssembly(String, Evidence)](http://msdn.microsoft.com/zh-tw/library/xthss7ss.aspx)這個建構子已過時，所以會產生以下錯誤。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfpjRpcL6N151HFp-pIQkDzWodmp2sWs517VsbE5YgiSVakxjW3FYFvSdM1hRVSCi6S74X2JTsS8rjzbLA3bzL7zDrf2Yh5o_DGBz2fHXB6K96ncLX_pAoxeDENnhR4Bn1BPEFZzhhYf8/s688/evidence_Obsolete.png)

除非改用其他沒有過時的建構子，要不然可參考以下兩種方式：

- 1. 改以.NET4.0方式取代過時的呼叫：
- Assembly.PermissionSet
- Assembly.IsFullyTrusted
- AppDomain.PermissionSet
- AppDomain.IsFullyTrusted
- 2. 使用舊版CAS原則 (在Configuration使用NetFx40\_LegacySecurityPolicy設定)  
若暫時不想修改程式可以使用這個方法，在App.config增加一個&lt;runtime&gt;項目，並將NetFx40\_LegacySecurityPolicy的enabled屬性設定為True，表是使用舊版的CAS程式碼安全性原則。
```xml
<configuration>
<runtime>
<NetFx40_LegacySecurityPolicy enabled="true"/>
</runtime>
</configuration>
```

若採用第二種方式進行修改，程式碼就可正常編譯執行。  不過，在上面這個例子中的 WindowsFormsApplication1.exe 這個組件，我們原本是利用它去開啟Ｃ糟某個文字檔，當它單獨被執行的時候是正常的。  但是，若使用 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.appdomain.evidence) 指派 SecurityZone.Internet 權限後，當組件被執行後，畫面會出現如下警告訊息(左圖)。  並且，當程式嗎執行到 File IO 時，也會發生 SecurityException (右圖)。若將程式碼中的 SecurityZone.Internet 變更成 SecurityZone.MyComputer 就沒問題，因為 MyComputer Zone 具有讀取該檔案的權利。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhG1V06hpBUp9KG336KcppBqDO7tNxRt5prF4iLuCzV8pezjqDPcMWeqLMJ-d2LzZQKVjXoj_VR-KwjwAy29txo6p12KihOI5enwY93L6fDsnEVrYZAa5bDVjH01RIEtkt4c9z3o1ovgt8/s389/CAS_Warring.png) ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhALWPAkySBhko3hAczhZbDUmiBQlRKTQ4A-b4f_C8AYhaJzyl9kIXtrb3wGFYqJ2y9C8c0L3sgvD7Z-tVmTmND0ERyq8KK9gZqvKGnEGcFAvL4qqNeA7FDqY6Lz14BhjB1PatogkTKk-o/s536/CAS_SecurityException.png)

## 如何提供主機辨識項給應用程式定義域
```c#
//1)建立主機辨識項
object[] codeGroup = { new Zone(SecurityZone.Internet) };
Evidence hostEvidence = new Evidence(codeGroup, null);

//2)建立AppDomain，並指定一個 Evidence 物件
AppDomain newDomain = AppDomain.CreateDomain("NewDomain", hostEvidence);

//3)執行組件，若沒特別設定，皆會套用與 AppDomain 相同 Evidence 物件
newDomain.ExecuteAssembly(@"D:\MCTS\MCTS2011\Practice\WindowsFormsApplication1\bin\Debug\WindowsFormsApplication1.exe");
```

# 如何設定AppDomain Properties

要設定 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 的屬性，必須透過 [AppDomainSetup](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup) 類別。  
若只是單純的變更 [AppDomainSetup](http://msdn.microsoft.com/zh-tw/library/system.appdomainsetup) 的執行個體並不會影響任何已經存在的 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 的屬性。  
它必須當成 [AppDomain.CreateDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain.createdomain) 方法執行時的參數，並且只會對新建立的 [AppDomain](http://msdn.microsoft.com/zh-tw/library/system.appdomain) 有效。  

#### 使用 AppDomainSetup 設定 AppDomain 屬性
```c#
// 設定 AppDomain 屬性
AppDomainSetup domainProperty = new AppDomainSetup();
domainProperty.ApplicationBase = "file://" + System.Environment.CurrentDirectory;  //取得應用程式的目錄名稱。
domainProperty.DisallowBindingRedirects = false;                                   //是否允許組件繫結重新導向
domainProperty.DisallowCodeDownload = true;                                        //是否允許以 HTTP 下載組件
domainProperty.ConfigurationFile = AppDomain.CurrentDomain.SetupInformation.ConfigurationFile;   //設定 AppDomain 的組態檔的位置

// 建立新的應用程式定義域，並以 AppDomainSetup 設定 AppDomain 的屬性。
AppDomain newDomain = AppDomain.CreateDomain("New Domain", null, domainProperty);  
```

#### 檢視目前 AppDomain 的屬性

透過 [AppDomain.SetupInformation](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setupinformation) 屬性，可取得應用程式定義域的屬性設定值。
```c#
//檢視AppDomain目前的設定值
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

#### 附註：

以下截至 MSDN [.NET Framework 4 中的安全性變更](http://msdn.microsoft.com/zh-tw/library/dd233103.aspx) 

從 .NET Framework 4 開始，CLR 不會為電腦提供安全性原則。  以往，.NET Framework 會提供程式碼存取安全性 (CAS) 原則當做嚴格控制和設定 Managed 程式碼功能的機制。  雖然 CAS 原則的功能很強大，不過它可能很複雜而且限制較多。  而且，因為 CAS 也不支援原生應用程式，所以在安全性保證上會有所限制。  在 Windows 7 和 Windows Server 2008 R2 上，系統管理員應該尋求 [Windows 軟體限制原則](http://technet.microsoft.com/zh-tw/library/bb457006.aspx) (Software Restriction Policies , SRP) 或 [AppLocker](http://technet.microsoft.com/zh-tw/library/dd723678.aspx) 等作業系統層級解決方案當做 CAS 原則的取代項目。  SRP 和 AppLocker 原則會提供同時適用於 Managed 程式碼和機器碼的簡單信任機制。就安全性原則解決方案而言，SRP 和 AppLocker 會比 CAS 更簡單而且提供較佳的安全性保證。  

非裝載的應用程式 (亦即，透過 [Windows 檔案總管] 或從命令提示字元執行的應用程式) 現在會以完全信任執行。這包括位於區域網路上共用的應用程式。  裝載或沙箱應用程式會繼續使用其主機 (例如 Internet Explorer、ClickOnce 或 ASP.NET) 所決定的信任原則來執行。  在沙箱中執行的應用程式或控制項會被視為部分信任的。