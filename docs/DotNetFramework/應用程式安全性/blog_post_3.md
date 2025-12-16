---
title: 程式碼安全性
layout: default
parent: 應用程式安全性
nav_order: 3
description: "程式碼安全性"
date: 2012-03-15
tags: [DotNetFramework,應用程式安全性]
postid: "5868366090105510176"
---
在Windows作業系統中，一般我們比較常用的安全系統是以使用者或角色為主的安全性機制(role-base security, **RBS**)，  透過對使用者或角色的管理，達到權限控管的目的，這也是大家比較熟悉的權限管理機制。  

如果當我們使用一個外來的組件來存取磁碟，為了確保它內部無法執行任何的惡意行為，你希望限定這個組件只能存取特定目錄，像這樣子的狀況，就無法使用 RBS 達到規範，而必須透過 CAS 管控到程式碼身上才有辨法。  

# 什麼是程式碼安全性 ( Code Access Security, CAS )

**程式碼存取安全性**（Code Access Security, **CAS**）是由.NET 之後才開始加入Windows，採用和 RBS 一致的模型，它是由 CLR 所管理，使用 **evidence-based** 的安全機制，控管應用程式的使用權限。  例如，當使用一個外來的組件時，為了確保它內部無法執行任何的惡意行為，我們可以使用 CAS 控管，限定它只可以進行讀取，無法進行寫入與刪除； 亦或者當執行一個組件必須到某個網站下載資料時，我們也可以限定它僅僅可以連結到這個指定的網址捉取資料，限定它無法連結至其他網址或發送Email等行為。  像這樣子的狀況，都無法使用 RBS 達到規範，而必須管控到更細節的程式碼身上才有辨法。  .NET所提供 CAS 機制，正是用來允許設計師，使用限定程式碼可執行的工作範圍，進而達到應用程式的安全性。  

**CAS 是利用 Secure Library 的概念**，也就是以安全的基礎函式庫為基礎。  例如，當應用程式要去存取網路時，必須利用.NET Framework 所提供的 Socket、Web、TCP 等網路組件才能存取，而這些組件會要求對應的權限，如 DncPermission、WebPermission、SocketPermission 等等。  也就是說，不同的資源透過相對應的權限類別以控制組件執行的權限。  如果，設計者在呼叫該組件時降低了這些權限或是拒絕存取，則 Socket、Web、 TCP 等網路組件在要求權限時就會產生例外，藉此建立以程式碼為對象的安全機制。  

**CAS 是一個安全系統**，它允許系統管理員或程式開發人員直接控制應用程式的授權 (authorization) 以達到安全性的需求。  它可以控管授權的項目，包含了幾乎所有 RBS 機制可以控管授權的項目例如：  

- The file system
- The registry
- Printers
- The event logs

除此之外，CAS 也可以控管 RBS 無法控管的資源，例如：允許應用程式是否可以送出 Web Request，或者是否允許應用程式執行 DNS 查詢需求。  這些控管的目的都是因為有一些惡意程式會利用系統功能的呼叫，以侵入使用者隱私，所以最好還是要使用CAS來限制應用程式的存取權限。  

CAS 只適用於 Managed 應用程式，無法管理 Unmanaged 應用程式，Unmanaged 應用程式須以作業系統的 RBS 機制另外控管。  

若某個組件受 CAS 限制權限，那麼這個組件就被認定為**部分信任**組件(partially trusted)。  部分信任的組件，每次存取受保護的資源時，都會被要求 CAS 的檢查，以便套用程式碼存取安全性。  

#### PS.

**部分信任**的應用程式大都是指從外部來源 (例如網際網路) 載入的應用程序，必須在沙箱中執行 (例如，在 IE 中)。  在桌面上或本機內部網路上安裝的應用程式，都會以完全信任來執行。  **完全信任**的應用程序不會受CAS影響，除非將它們標記為安全性透明，因為它們都是完全受信任的。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggg3g4U9tb1zaFyn7JMe5jrKMxL3wJF5PltW3r6FCgKQbQjlDK_cIqUs8r8UEA18RCCl-mMRtqYemrYeRnPfFawj2mj0JlEh8Y3erONBCmppb9hY__hhpWrSU9_8IDLRVvYdF7u98D7Nw/s850/cas_policy.png)

# CAS的元素

[System.Security.Policy](http://msdn.microsoft.com/zh-tw/library/system.security.policy.aspx) 命名空間包含**程式碼群組**、**成員資格條件**和**辨識項**。 這三種類別都是用來建立給 CLR 套用的安全性原則。  Evidence classes are the input to security policy and membership conditions are the switches（參數）; together these create policy statements and determine the granted permission set.  Policy levels and code groups are the structure of the policy hierarchy.Code groups are the encapsulation of a rule and are arranged hierarchically in a policy level.  

## 什麼是「辨識項」(Evidence)

任何安全系統都會有一個認證的機制，CAS 也不例外。  一般以人為對象的安全系統 (RBS)，其認證機制通常會使用帳號密碼來辯識。  但是 CAS 的對象是應用程式，它則是利用**辨識項 (Evidence)** 來認證組件。  辨識項就像是程式碼的通行證一樣，CLR 會檢查這個通行證，以便決定是否讓程式碼存取資源。  

簡單講，**辨識項**就是一組資訊，給 CLR 辨別用，以便決定該授與程式碼哪些權限。  

例如常見的辨識項格式有：組件由哪個資料夾或網站執行、組件的簽名、發行者、強式名稱、hash。CLR 就是透過這資訊來認證組件。  

所以說，辨識項並沒固定格式，它可能是任何事物，只要安全性原則可辨認的物件即可。  

CLR 會在執行階段時，才經由組件的辨識項決定該組件屬於哪一個**程式碼群組 (CodeGroup)**。相對的，組件的執行權限，就是由程式碼群組所賦予的。  

#### 下表是 [System.Security.Policy](http://msdn.microsoft.com/zh-tw/library/system.security.policy.aspx) 命名空間裡常用的 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 類別：

1. [ApplicationDirectory](http://msdn.microsoft.com/zh-tw/library/system.security.policy.applicationdirectory.aspx) ：以**應用程式目錄**做為原則評估的辨識項。
2. [GacInstalled](http://msdn.microsoft.com/zh-tw/library/system.security.policy.gacinstalled.aspx) ：確認程式碼是否為全域組件快取 (**GAC**) 中的組件。
3. [Hash](http://msdn.microsoft.com/zh-tw/library/system.security.policy.hash.aspx) ：以組件**雜湊值 (Hash Value)**做為原則評估的辨識項。
4. [Publisher](http://msdn.microsoft.com/zh-tw/library/system.security.policy.publisher.aspx) ：以組件**發行者**的數位簽章做為原則評估的辨識項。
5. [Site](http://msdn.microsoft.com/zh-tw/library/system.security.policy.site.aspx) ：以組件的來源**網站**做為原則評估的辨識項。例如：www.microsoft.com
6. [StrongName](http://msdn.microsoft.com/zh-tw/library/system.security.policy.strongname.aspx) ：程式碼組件的**強式名稱**做為原則評估的辨識項。
7. [URL](http://msdn.microsoft.com/zh-tw/library/system.security.policy.url.aspx) ：程式碼組件的來源 [URL](http://msdn.microsoft.com/zh-tw/library/system.security.policy.url.aspx) 做為原則評估的辨識項。例如：http://www.microsoft.com/news/index.htm
8. [Zone](http://msdn.microsoft.com/zh-tw/library/system.security.policy.zone.aspx) ：程式碼組件的安全性**區域**(Security Zone) 做為原則評估的辨識項。

其實辨識項它也就是程式碼群組的成員資格條件，請參考下圖：

![CAS_Type](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjkfq4i-6uc75LKkMm8xJMZKPnOJGMpkkHJeOOGaARUg9YP3zHJgpZiqhMF2VRpmGq1nhfx27uP4O8BZkjPra0a03iQsUGGqz6Q4oyWGwWYilnmzzjy_ejImqGVUNNVkGnI7yySfixaxrY/s427/CAS_Type.png)

#### 如何建立辨識項：
```c#
Evidence evd = new Evidence();

// Create a Url name.
Url url = new Url("http://www.microsoft.com");
evd.AddHostEvidence(url);
evd.AddAssemblyEvidence(url);

// Create a strong name.
StrongName mSN = new StrongName(new StrongNamePublicKeyBlob(null), "SN01", new Version("0.0.0.0"));
evd.AddHostEvidence(mSN);
evd.AddAssemblyEvidence(mSN);
```

#### 列舉組件的辨識項：
```c#
Assembly[] asms = AppDomain.CurrentDomain.GetAssemblies();
foreach (Assembly asm in asms)
{
Console.WriteLine(asm.ToString());

Evidence evidence = asm.Evidence;
object[] evidenceArray = new object[evidence.Count];
evidence.CopyTo(evidenceArray, 0);
foreach (object obj in evidenceArray)
{
Console.WriteLine(obj.ToString());
}
}
```

下表是其中一個組件的 Evidence ，
```xml
<System.Security.Policy.GacInstalled version="1"/>

<System.Security.Policy.Hash version="2">
<hash algorithm="SHA1" value="F2B76C91B22A271D84DAD801191B74D3A85BED15"/>
<hash algorithm="SHA256" value="D5EEABC20682B71014E55EA35FA0A8FFBFFCAEBA8DBE2A8B52C00C4B146286B2"/>
<hash algorithm="MD5" value="EC4F3A5008A3F8A45973784CB3B97540"/>
</System.Security.Policy.Hash>

<StrongName version="1" Key="00000000000000000400000000000000" Name="mscorlib" Version="4.0.0.0"/>

<System.Security.Policy.Url version="1">
<Url>file:///C:/Windows/Microsoft.Net/assembly/GAC_32/mscorlib/v4.0_4.0.0.0__b77a5c561934e089/mscorlib.dll</Url>
</System.Security.Policy.Url>

<System.Security.Policy.Zone version="1">
<Zone>MyComputer</Zone>
</System.Security.Policy.Zone>
```
```c#
Assembly testAssembly = Assembly.LoadFile(@"D:\myBlog\TestProj\TestForAppDomain\TestForAppDomain\bin\Debug\TestForAppDomain.exe");

Evidence evidence = testAssembly.Evidence;
object[] evidenceArray = new object[evidence.Count];
evidence.CopyTo(evidenceArray, 0);
foreach (object obj in evidenceArray)
{
Console.WriteLine(obj.ToString());
}
```
```xml
<System.Security.Policy.Hash version="2">
<hash algorithm="SHA1" value="1720F486CCF9801D95AF42F9FFE8D0E36DB107C6"/>
<hash algorithm="SHA256" value="547D24E42BCCE76F3C0F26F77FE0BAFCCB0119A195DCB4A3D3BD4916475FD07F"/>
<hash algorithm="MD5" value="19429C66435A0228802DD88BBC263DDB"/>
</System.Security.Policy.Hash>

<System.Security.Policy.Url version="1">
<Url>file:///D:/myBlog/TestProj/TestForAppDomain/TestForAppDomain/bin/Debug/TestForAppDomain.exe</Url>
</System.Security.Policy.Url>

<System.Security.Policy.Zone version="1">
<Zone>MyComputer</Zone>
</System.Security.Policy.Zone>
```

#### 辨識項可分成二類：
Evidence 類別有二個用來儲存辨識項集合：主辨識項和組件辨識項。
- **主辨識項 (Host Evidence)**：  
執行階段才由主應用程式提供的辨識項，這類辨識項通常是程式碼的來源或組件上的數位簽章。  
來源辨識項：例如 Url、Site、Zone ，是形容某一組件來源的辨識項。  
簽章辨識項：例如 Strong Name、Publisher 辨識項。  
來源辨識項的資訊因為位於主應用程式中，所以由主應用程式提供。  
Strong Name 和 [Publisher](http://msdn.microsoft.com/zh-tw/library/system.security.policy.publisher.aspx) 這二種簽章的識別雖然內建在組件中，但是它必須先由主應用程式驗證後再傳遞至原則。
- **組件辨識項 (Assembly Evidence)**：  
組件辨識項是組件它自己的一部分，它只能在組件產生時加入。例如 [Hash](http://msdn.microsoft.com/zh-tw/library/system.security.policy.hash.aspx) 、 [Publisher](http://msdn.microsoft.com/zh-tw/library/system.security.policy.publisher.aspx) 或 [StrongName](http://msdn.microsoft.com/zh-tw/library/system.security.policy.strongname.aspx)。  
不過安全系統的預設原則會忽略組件提供的辨識項。
```c#
object[] hostEvidence =
{
new Zone(SecurityZone.Internet),
new Url("http://www.codeplex.com/hostevidence")
};

object[] assemblyEvidence =
{
new Zone(SecurityZone.MyComputer),
new GacInstalled()
};

Evidence evidence = new Evidence(hostEvidence, assemblyEvidence);

evidence.AddHost(new Site("www.codeplex"));
evidence.AddAssembly(new Url("http://www.codeplex.com/assemblyevidence"));
```

## 什麼是「使用權限」(Permission)

Permission 就是 CAS 機制中的一個存取控制項，在.NET Framework 組態工具裡，共有 19 個預設的使用權限可供配置。  例如：「檔案對話方塊」這個權限，可以用來決定一個組件是否可以開啟對話方塊或儲存對話方塊。  每個權限都對應到 [System.Security.Permissions](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.aspx) 命名空間裡的二個成員：命令式使用及宣告式使用 ( imperative use & declarative use )。  

下圖中，在組態管理工具中，共有19個預設的使用權限可用，在 [System.Security.Permissions](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.aspx) 裡，也都有相對應的類別。

![CAS_Type](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjuGO1ozCpwTiNgEZPV7r3B8bsUzHQQ3RQKxfuTRPfITk98e8lVsnuQOEcoyVtgAm66Daefu-9NQUpwQKZBsluqw9YnPxjSasJtg8tCJXfRSwc9Lypjj6z6m9LXR2lt7cO6dJry_C87aBA/s494/cas_permission.png)

權限集合 and 使用權限

![CAS_Type](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjd3Oc51PN0ZRbXxiQ0zx3aNYE7xXORoLDnw8kXJjUBdtwIfpASQsVpVMGExAN9STTUEQezWs4S08Hfv6yLMyFFMXxD-QklYf-SOiAeaXI2Lx_r1jP4XKuEsB5Fz7VxAjKiiZmFplgxEUo/s808/cas_permission2.png)
```c#
[WebPermission(SecurityAction.Deny, ConnectPattern = @"http://www\.microsoft\.com/")]
private void button7_Click(object sender, EventArgs e)
{
HttpWebRequest myWebRequest = (HttpWebRequest)WebRequest.Create("http://www.microsoft.com");
Console.WriteLine("由於權限設定不允許連線這個網站,所以程式碼不會到這一行");
}
```

## 什麼是「權限集合」(Permission Set)

權限集合是 CAS 機制中的存取控制清單 (Access Control List, ACL)，在 .NET Framework 組態工具裡，有七個預設的權限集合，也可以自行再定義擴充。而毎個權限集則可以設定數種使用權限。  

#### .NET Framework 預設的七個權限集合，如下表所示，每個權限集合都包含相關的使用權限：

1. FullTrust：完全信任
2. Execution：執行
3. LocalIntranet：內部區域網路
4. Internet：網際網路
5. SkipVerification：略過驗證
6. Everything：
7. Nothing：

![Permission Set](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEje1pOEx2newA_7x7o-CwKwMq1iHPA25cALiPXboQ2X5HGj5KAHU0ahWRWbhBJuv_k2lcAPYcM6QYmc77wJd87eS0-pnBdBZ_MdSgyTP9v5ddi0EBfMdkeyov0qkG_VBqr17n5JCSDjgeA/s427/CAS_PermissionSet.png)  ![Default Permission Set of Internet PermissionSet](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpsy8MYNCJxK8IHK6LHwOfKfwV1HzkXALc9tlCBTIJKDw5qDfbRcZwETFD7Yhd93tA79zBeTCDnqZtRl-YVpTLYeKfeF8EjOFNF95IAba15v-y-2txzTiwKZd-jcPh6AcSyQfA7D5caAA/s427/cas_intranet_default_permissionset.png)  

底下程式碼示範 PermissionSet 的使用
```c#
//Open a new PermissionSet.
PermissionSet myPermSet = new PermissionSet( PermissionState.None);

// Add a permission to the permission set.
myPermSet.AddPermission(new FileIOPermission (FileIOPermissionAccess.Read, @"C:\Windows"));
myPermSet.AddPermission(new FileIOPermission( FileIOPermissionAccess.Write, @"C:\Inetpub"));
myPermSet.AddPermission(new RegistryPermission( RegistryPermissionAccess.Write, @"HKEY_LOCAL_MACHINE\Software"));
myPermSet.Demand();
```

#### 列舉 PermissionSet

底下程式碼會取得系統中，符合目前組件的權限集合，而不是列舉所有的權限集合，
```c#
private void ListMachinePermissionSets()
{
IEnumerator policyEnumerator = SecurityManager.PolicyHierarchy();
while (policyEnumerator.MoveNext())
{
PolicyLevel currentLevel = (PolicyLevel)policyEnumerator.Current;
if (currentLevel.Label == "Machine")
{
IList namedPermissions = currentLevel.NamedPermissionSets;
IEnumerator namedPermission = namedPermissions.GetEnumerator();
while (namedPermission.MoveNext())
{
Console.WriteLine("\t" + ((NamedPermissionSet)namedPermission.Current).Name);
}
}
}
}
```

#### 在沙箱中，使用自訂的 PermissionSet
```c#
private void button25_Click(object sender, EventArgs e)
{
string pluginFolder = @"D:\myBlog\TestProj\TestForAppDomain\TestForAppDomain\bin\Debug";
string plugInPath = Path.Combine(pluginFolder, "TestForAppDomain.exe");

AppDomainSetup setup = new AppDomainSetup();
setup.ApplicationBase = pluginFolder;

Evidence hostEvidence = new Evidence();
hostEvidence.AddHost(new Url(pluginFolder));

PermissionSet pset = GetNamedPermissionSet("myPermissionSet");
pset.AddPermission(new FileIOPermission(FileIOPermissionAccess.PathDiscovery | FileIOPermissionAccess.Read, pluginFolder));

AppDomain sandbox = AppDomain.CreateDomain("sbox", null, setup, pset);
sandbox.ExecuteAssembly(plugInPath);
AppDomain.Unload(sandbox);
}

private static PermissionSet GetNamedPermissionSet(string name)
{
IEnumerator policyEnumerator = SecurityManager.PolicyHierarchy();
PolicyLevel level = PolicyLevel.CreateAppDomainLevel();
while (policyEnumerator.MoveNext())
{
PolicyLevel currentLevel = (PolicyLevel)policyEnumerator.Current;
if (currentLevel.Label == "Machine")
{
NamedPermissionSet copy = currentLevel.GetNamedPermissionSet(name);
return (PermissionSet)copy;
}
}
return null;
}
```

## 什麼是「程式碼群組」(Code Groups)

「程式碼群組」的作用是將**組件**與**權限集合**產生關聯。

#### 程式碼群組的屬性中有二項資訊：

- 成員資格條件 (membership condition)：設定 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 類別的詳細資訊。
- 使用權限集合 (permission set)：設定程式碼群組的權限。

前面說過，「程式碼群組」的作用是將**組件**與**權限集合**產生關聯。  這裡所指的組件，指的是符合特定條件的所有組件。  例如：若在程式碼群組中，將組件的條件類型設定為 ApplicationDirectory ，表示與應用程式同一目錄或子目錄下的所有組件。  

例：下圖程式碼群組的設定指出，只要符合 [URL](http://msdn.microsoft.com/zh-tw/library/system.security.policy.url.aspx) 類型的成員，且符合指定的 [URL](http://msdn.microsoft.com/zh-tw/library/system.security.policy.url.aspx) 格式，都將套用到「唯讀組件集合」這個自訂的權限集合。  

![Default Permission Set of Internet](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOueiMUzxCUE-DtVCEzeFuNSjV2C-Owmz16GnJaLhsm_Wqwb-aROWEIoDG1-yPb2a_ePiu1ERhOmD10P4nor0xBRDrjNqSmJn-4sAWVXzopLhAa3kugSRK-cTJd2_fMElaCjRDj-Mb8xI/s850/cas_codegroup3.png)

#### 預設的程式碼群組共５個：

- My\_Computer\_Zone：本機電腦（完全信任）。
- LocalIntranet\_Zone：內部網路。
- Tursted\_Zone：信任的網站。
- Internet\_Zone：網際網路。
- Restricted\_Zone：。限制的網站（Nothing）

例如下圖中， Internet\_Zone 這個程式碼群組，它使用**區域**這個類型的 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 。也就是這個程式碼群組以**區域** (Zone) 做為組件辨識的類型。  而且更明確的指明是網際網路(Internet)這個區域類型。（區域共分6種來源地區，如 Internet 、 Intranet 、 MyComputer）  

![Default Permission Set of Internet](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgX3_aHlQPSO4ZKoFgQdh4P451nD8lkvLqDkpQed_e_Jejmz2VRf1ppSKgMl-QP9_oAxO8r1JeXjxVmO8KLrf1RBGQeXvoSYw203w_E1ZuPeTA9gN1Uk4OBHgJBm6NkIggDwpbnU8l1gAA/s427/cas_codegroup1.png)

#### 同一程式碼群組的組件具有相同的權限

這機制跟使用者群組有點像，我們設定好使用者群組的權限，再把使用者加入，那麼這些使用者就具有相同權限。  程式碼群組也是如此，但是它沒有手動將組件加入程式碼群組的步驟。  因為，組件是在 CLR 執行階段時，經由組件的辨識項判斷，才知道此時組件屬於哪個程式碼群組。  

例如，如果群組的成員資格條件為 "Code from the www.microsoft.com Web site"，執行階段便會檢查辨識項，判斷程式碼的來源是否為 www.microsoft.com。  

若要指定程式碼群組的權限，可以切換到使用權限集合這個頁簽，如下圖所示。  不過此時你會發現，它無法單獨指定一個權限，只能指定權限集合。  是的，沒錯，程式碼群組只能指派 Permission Set 以獲得 Permission，不能直接指派 Permission，而且只能套用一個 Permission Set 。  

![Default Permission Set of Internet](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSJs98uXPqKWhOI8GXW4nZonVd26Dat6lGNbEzlp_sUlVVRSsiVZdVuGOjbn2LDp4KvFp8BCNFOsYq__FrdtNPNIC3mdMf5EbL6K21swfsrpvSLJBTW2myb-3_7svmo155ksuwn4BfgC4/s427/cas_codegroup2.png)

雖然一個 [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) 只能指派一個［成員資料條件］及單一的［權限集合］，但是，就像一個 User 可以同時隸屬於不同的 User Group，一個組件也可以同時是多個 [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) 的成員。  具有多個 [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) 的組件，將會取得所有這些 [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) 的權限（union of the permission sets)。  另外，一個 [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) 也可以巢狀到另一個 CodeGroup，這樣子，一個 CodeGoup 就可能同時符合多個Evidence Type。  

#### 使用程式碼建立程式碼群組

下面程式碼示範如何使用程式建立 CodeGroup，要建立 code-group 必須決定要列事項：

- code-group 的層級
- 使用什麼 evidence
- 授與 code-group 什麼權限集合
```c#
protected CodeGroup(IMembershipCondition membershipCondition, PolicyStatement policy);
```
```c#
CodeGroup machineCodeGroupRoot = null;

//To access a security level

PolicyLevel policyMachineLevel = PolicyLevel.CreateAppDomainLevel();
machineCodeGroupRoot = policyMachineLevel.RootCodeGroup;

//To provide evidence value

Evidence evidence = new Evidence();
evidence.AddHost(new Site("www.microsoft.com"));

//Register a code-group

byte[] key = Assembly.GetCallingAssembly().GetName().GetPublicKey();
StrongNamePublicKeyBlob cdeGroupKey = new StrongNamePublicKeyBlob(key);

PermissionSet ps = new PermissionSet(PermissionState.None);
ps.AddPermission(new SecurityPermission(SecurityPermissionFlag.Execution));

UnionCodeGroup  codegroup = new UnionCodeGroup(
new StrongNameMembershipCondition(cdeGroupKey, null, null),
new PolicyStatement(ps,PolicyStatementAttribute.Nothing));

codegroup.Name = "MyGroup";
machineCodeGroupRoot.AddChild(codegroup);
SecurityManager.SavePolicyLevel(policyMachineLevel); //at last save the policy
```

#### 下表是一些與程式碼群組相關的類別

- [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) ：  
Represents the abstract base class from which all implementations of code groups must derive.
- [NamedPermissionSet](http://msdn.microsoft.com/zh-tw/library/system.security.namedpermissionset.aspx) ：  
Defines a permission set that has a name and description associated with it.
- [PolicyStatement](http://msdn.microsoft.com/zh-tw/library/system.security.policy.policystatement.aspx) ：  
Represents the statement of a [CodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.codegroup.aspx) describing the permissions and other information that apply to code with a particular set of evidence.
- [FirstMatchCodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.firstmatchcodegroup.aspx) ：  
Allows security policy to be defined by the union of the policy statement of a code group and that of the first child code group that matches.
- [UnionCodeGroup](http://msdn.microsoft.com/zh-tw/library/system.security.policy.unioncodegroup.aspx) ：  
Represents a code group whose policy statement is the union of the current code group's policy statement and the policy statement of all its matching child code groups.

# 安全性原則 ( Security Policy )

## 什麼是安全性原則

- 安全性原則由一些[**程式碼群組**]及[**權限集合**]邏輯性地組成。
- CLR 依據安全性原則授與程式碼的使用權限。
- 系統管理員可以彈性地以不同層級配置安全性原則。預設的四種原則層級：**企業**、**本機電腦**、**使用者**、**應用程式**。
- 預設的層級中，企業和使用者都是完任信任所有的程式碼，只有本機電腦有對CAS使用權限做限制。

每個安全性原則會定義幾個**程式碼群組**，並將每個程式碼群組與一組**使用權限**相關聯。  

安全性系統會使用 [Evidence](http://msdn.microsoft.com/zh-tw/library/system.security.policy.evidence.aspx) 來判斷組件所屬的程式碼群組。在考慮完所有的辨識項 (Evidence) 之後，組件會與一個或多個程式碼群組產生關聯，而授與給此組件的使用權限，包括所有與相符程碼群組關聯的使用權限。  

雖然預設安全性原則適用於大部分的情況，但是系統管理員仍可依據組織的特定需求來修改或自訂安全性原則。  Runtime 會根據安全性原則，將使用權限同時授與組件 (Assembly) 和應用程式定義域 (AppDomain)。

## CAS 和作業系統安全性如何配合

CAS和作業系統安全性是共存的，當要決定某個組件是否有權限執行某個動作時，**程式碼存取安全性**和**作業系統安全性**都會同時被評估，然後取兩者之間**最嚴格**的權限集。  例如，某個組件由 CAS 取得寫入 C:\ 的權限，但是執行這個組件的使用者並沒有這個寫入 C:\ 的權限，那麼這個組件就沒有權限寫入這個資料夾。

# 如何設定執行階段安全性原則

要限制程式的安全性原則，可以在程式碼中透過，宣告組件的屬性 (Attribute) 指定適當的權限。  也可以由 .NET Framework 2.0 組態設定工具中設定。  另外，Caspot.exe 是微軟提供的一個以命令列的方式變更安全性原則的工具，也可以達到相同功能。  

## 使用組件宣告方式，限制組件的執行權限

透過 [FileIOPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.fileiopermission.aspx) 類別，限制這個函式只能讀取 C:\temp 目錄中的檔案
```c#
[assembly: FileIOPermission(SecurityAction.RequestOptional, Read = @"C:\\temp")]
namespace ClassLibrary1
{
public class Class2
{
public static string Open(string sFileName)
{
StreamReader reader = new StreamReader(sFileName); 
string Content = reader.ReadToEnd(); 
reader.Close();
return Content;
}
public static void Save(string sFileName, string Content)
{
StreamWriter writer = new StreamWriter(sFileName);
writer.Write(Content);
writer.Close();
}

｝
｝
```
```c#
private void button4_Click(object sender, EventArgs e)
{
string FileName = txtFileName.Text;

//執行類別庫的讀取功能
ClassLibrary1.Class2.Open(FileName);  //這行指令可正常執行

//執行類別庫的寫入功能                //這行指令會產生 FileIOPermission 使用權限要求失敗。
ClassLibrary1.Class2.Save(FileName, "ABC" + Environment.NewLine + System.DateTime.Now.ToString()); 
}
```

## 使用組態設定工具，限制組件的執行權限

底下過程為使用組態設定工具，設定組件A只可以唯讀 C:\temp ，設定完成後：(本示範僅限.net framework 2.0的組件)  
1.組件A可以讀取 C:\temp (含子目錄)  
2.組件A不可以寫入 C:\temp  
3.組件A也不可以讀寫其他目錄  

#### (1)建立 Permission Set

Step1:在[使用者]原則底下的[使用權限集合]，新增一個 Permission Set，名稱為：唯讀組件  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgy0BTJEnynRTDyIG-ydp9W7l1Yew163REePxTnI1HZb1O86OHHF1o7-qGA1zaWCE72PdpwfPnBqV-q6fh2ut4TCcg6p59E8zMtmE0ocsehJZdpaG94CL32nECNw2_3avNoWMsD7_OOjDk/s494/cas_tutorial1.png)  
Step2:授與執行組件的權限給這個集合。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQLNRaFALW_yIxV8KQ00v3LHuhBNN5JQ-nyRB0eJ_Uz4kezpUVaBV2RddzCQJP1OQvO_8-PUFZ1wrPEHgu8jc2gzjeCiezdUCi3t7gYJ8va2NBj6BCLwm3JueCfpEnVqR9lzxpdMuJujQ/s738/cas_tutorial2.png)  
Step3:授與檔案IO的權限給這個集合，並設定指定路徑唯讀。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg74yja4x2Bapk9DsGBXlc3HkHWSt8W7KYN8em-4kptd5AP7O91e6HUFO9yNR8jAxI3BrkzWFpkt9ETc1b5uXazHZogebEgYnXNhGjI0a1JXaXMZI47VqLggUK9PXRBnH2Dm78-SbWhsSI/s766/cas_tutorial3.png)  
Step4:完成。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMRDtVQOH-S4mB7tvh3S8u3BboPTdGlWYTVKuBRbTLlIfGpE1gawyWjwXsbyuf7kTvRR7lavMZuIUseeW8ND6XiVWefwliOXf2y_Wq7keu7cGS7ZuEZgMuqjRHrRf8j9bAPy7CjHoX3qY/s494/cas_tutorial4.png)  

#### (2)建立 Code Group

Step1:在[使用者]原則底下的[程式碼群組][All\_Code]，新增一個 Code Group，名稱為：唯讀群組  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQ1Lcr91IqeLSUsz-0xrDL8Z5hPaQeLcN4GGvpNPt-wqd1x7-sfjbcQfIMX58pq4QfoahlG3fivibzdEuRq71tud6LEF_P1cQsblFIC6-qS7qk4DTJbmgzB-LTt-tpfIN2wPYM-j3l6Cw/s494/cas_tutorial5.png)  
Step2:選擇條件類型  
因為我們要設定的組件具有強式名稱，所以可以使用強式名稱這個類型來指定這個組件。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQ2p0-T3V6GJS5_IdGjWUJ7NdZC01NAXRyOPcavQ6pkESViMiuxrPwlLOEZvp5YI2LCrR_XKnYXT3OVtriRv1upA7K6AhNoh5tdeg4EcSlp4VPHP3RFPbC5wZA3PZYdpmXTs6ZXhcdGmU/s494/cas_tutorial6.png)  
要不然，使用 [URL](http://msdn.microsoft.com/zh-tw/library/system.security.policy.url.aspx) 這個類型來指定也可以。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrrevxWy9xivFdhq4eRUgWtZsvd6HHY926YzLB4Ae-T9BF52LqfbiyII4BVJRD2FAJiqrj0WNnIMtxtGWnMyLTlL0kth5GyScgmuDlVEGnaIMrXsncBb5qQib_ZAxXVDONBJ2Thi7onos/s427/cas_tutorial9.png)  
Step3:設定要套用的權限集合。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0VZTzDZw7vhQctZSPeHexwXCdDNlVwGRciy5uAwTGVCbFnmTkTqq7y131S9rB3LW8rVfC0FZlgypAC2DdCYKhzr2E-2XO-E2qqm-NyKF3hJeG2uCRuqi84F4LzWWRXovx-DVSZGVq4SE/s494/cas_tutorial7.png)  
Step4:完成。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7gPRL0vscu5hU3qaAE-MJhU02mhxASJr_lYSNR85fEuPCZ-OgzoUfA5Fk2IGV1Qovt4nHnjRiZaWRVTlb2sU_oC3aSBhFdFObay4uEqIMyyp-SjV5ELJB8L7RNKTX-DttxApEceNyQA8/s494/cas_tutorial8.png)  
Step5:開啟設定好的CG，勾選下圖選項，以限制這個 CG 不繼承它的上層 All\_Code 的完全信任權限。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga8wsIbkw6YJgpfZv2dPn_o5vplcQKB51Ri2g1KHdG29dWaONKTnFaNsxKS19jeTNMHN2NQhfpwN4MfikthUE3HaruJt_75WiA_6XArMN0cl7nDu6bEvk8mp8n01cYzhhvkpY8YuchyphenhyphenBM/s427/cas_tutorial10.png)

## 使用 Caspol.exe 設定執行階段安全性原則

Caspol.exe是微軟提供的工具，稱為「程式碼存取安全性原則工具」。可透過命令列方式，執行安全性原則的操作。  
Caspol -? 可查詢全部的參數及說明，詳情可參考[使用程式碼存取安全性原則工具 (Caspol.exe) 設定安全性原則](http://msdn.microsoft.com/zh-tw/library/a0ke3k86.aspx)  
另外可參考保哥的介紹：[CasPol.exe 程式碼存取安全性原則工具幾個常用的指令](http://blog.miniasp.com/post/2008/09/07/CasPolexe-Code-Access-Security-commands.aspx)  

底下是幾個常見指令示範：

#### 查詢組件符合的群組
```c#
caspol -resolvegroup "C:\temp\test.exe"
```

#### 查詢組件擁有的權限
```c#
caspol -resolveperm "C:\temp\test.exe"
```

#### 將組件設為「完全信任」
```c#
caspol -addfulltrust "C:\temp\test.exe"
```

#### 將最近儲存的本機電腦原則(Machine)原則復原。
```c#
caspol -machine -recover
```

假設想要將 Intranet 上某個共享目錄設為完全信任，可以參考以下的做法：
```c#
CasPol.exe -m -pp off -ag 1.2 -url file://///server/share/\* FullTrust
```

- -m ：設定為本機電腦原則(Machine)
- -pp off ：關掉訊息回應
- -ag 1.2 ：加入一個 codegroup 到 1.2 這個群組下面。一般 1.2 指的就是 「LocalIntranet」群組。
- -url ：必須使用一個 UrlMembershipCondition 當做成員資格條件。
- FullTrust ：透用「完全信任」權限集合。


- [.NET Framework Security : Code Access Security - 應用程式篇](http://www.dotblogs.com.tw/code6421/archive/2008/05/07/3833.aspx)
- [程式碼存取安全性](http://msdn.microsoft.com/zh-tw/library/c5tk9z76.aspx)
- [.NET Framework 中的安全性](http://msdn.microsoft.com/zh-tw/library/fkytk30f%28VS.80%29.aspx)
- [瞭解 .NET Framework 2.0 的程式碼存取安全性有哪些新功能](http://msdn.microsoft.com/zh-tw/magazine/cc163700.aspx)
- [CasPol.exe 程式碼存取安全性原則工具幾個常用的指令](http://blog.miniasp.com/post/2008/09/07/CasPolexe-Code-Access-Security-commands.aspx)
- [安全性原則管理](http://msdn.microsoft.com/zh-tw/library/z020bk4f)
- [安全性原則模型](http://msdn.microsoft.com/zh-tw/library/ck90k585.aspx)