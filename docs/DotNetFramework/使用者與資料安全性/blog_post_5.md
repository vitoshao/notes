---
title: 存取控制清單
layout: default
parent: 使用者與資料安全性
nav_order: 5
description: "存取控制清單"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "2699513786148041864"
---
# 存取控制清單 (Access Control List, ACL) 

前一章節中，我們透過 RBS 架構下的驗證與授權機制 (Principal Permission)，達到限制特定使用者或群組對程式碼的存取。  

存取控制清單 (ACL) 也是一種權限控管的機制，是作業系統用來管控資源的使用權限檔案，如：資料夾、印表機、註冊機碼等。  它是由存取控制項 (ACE) 所組成，每一項 ACE 指明了某物件是否可被存取。  例如，若想阻止系統管理員以外的任何人讀取某個檔案，就可以建立一個特定的 ACE，並套用到該檔案的存取控制清單 (ACL) 即可。  

ACL 包含二種類型：  

- discretionary access control list (DACL)：自主式存取控制清單
- system access control list (SACL)：系統存取控制清單

# 自主式存取控制清單 ( Discretionary Access Control List, DACL )

## 什麼是自主式存取控制清單（Discretionary Access Control List, DACL）

自主式存取控制 (DAC) 是一種針對使用者或群組，允許或拒絕他們存取資源的一種機制。  它使用存取控制項 (ACE) 來判斷使用者或群組的存取權限。  例如，若想阻止系統管理員以外的任何人讀取某個檔案，就可以建立一個特定的 ACE，並套用到該檔案的存取控制清單 (ACL) 即可。  

在 Windows 作業系統裡，每一個系統物件都擁有自己的 ACL 權限表，  以 NTFS 檔案系統內的檔案來說好了，每個檔案都有六種基本權限：Full Control、Modify、Read＆Execute、Read、Write、特殊權限。  而在 [進階] 選項中，還能更進一步的設定檔案的所有權限，包含檔案的 7 種延伸權限，故檔案的權限設定總共有 13 種，如下圖：。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZ2Nt1ep1XOGSgGGCg_1UthbbZKHHwq4rqIGVGXrTpN7cdpOG_3mWWuHlnqiif1ylfXho7FpMumyBzMXKAM6N-GwfOR7hHYhR-c-ddRjv4Gn2a7ErgK322XBkQxXGtdh6Upz-ho-1K2RE/s850/file_security.png)

## DACL 的管控機制

若將使用者看成主體(subject)，檔案或資源看成客體(object)。  那麼 RBS 機制就是將權限管制表記錄在主體身上，而 DACL 則是將權限管制表記錄在客體身上。  

這個管制表就稱為存取控制清單 (ACL)，而清單內則是由存取控制項 (ACE) 所組成，每一項 ACE 指明了某物件是否可被存取(如圖)。  當 Process 想要存取一個受保護的物件，系統會先檢查該物件的 DACL 中的 ACEs 項目，以決定是否授與 Process 的存取權限。  若該物件沒有 DACL，系統就會授與任何人完整的存取權限。  若該物件有 DACL，卻不含任何 ACEs，系統則會禁止任何人的存取。  

如 Alice 和 Bob 擁有資料庫之存取權限，在資料庫維護存取控管清單上，Alice 僅有讀取權限，Bob 則擁有讀取、寫入、修改與刪除權限。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5nKpOiD0C68I8s7Krr5QjmcCa3rG-mWxGiQWk1NP86ffF-P0jP-1H-21TVhuZtHBUTFJJS3o2-ltoNpKpQ1-ePN4zPrBxYeDBapRuxyDU-d1nSY9o8sGDcHfa_sffW95y_cp-yl9xl1o/s493/acl.png)

## 系統如何計算有效的使用權限

#### 明確的與繼承的使用權限

在指派使用權限給物件時，有二種方法

- **明確指派：(Explicit Permissions)**  
當指派 DACL 給一個物件時，就是在建立一個明確的使用限權。例如，指派明確的使用權限給每個檔案、資料夾、登錄機碼。
- **繼承指派：(Inherited Permissions)**  
繼承的使用權限比明確指派有效率多了。它使用尤其父層物件傳播下來的權限。  有了這樣子的機制，在建立新物件時，就不需要明確指定使用權限。新的物件會繼承其父層的使用權限。

#### 使用權限是累積的

授與一個使用者或所屬群組的使用權限是用累積(sumulative)的概念的。  例如，使用者vito，同時為 IT 和 Managers 群組的成員，  若某一個檔案的 ACL 給 vito 只有 Read 權限，給 IT 群組 Modify 權限，給 Managers 群組 Full Control 權限。  如此一來，vito將有 Full Control 權限。  

#### 拒絕權限永遠覆寫授與權限

在多個 ACL 中，若有一個是拒絕存取用的 ACL，那麼它將覆寫授與存取權限的 ACL。  如上例中，若 IT 群組明確的被拒絕存取，即使 vito 為 Managers 群組的成員，且該群組有 Full Control 權限，vito 也無法存取該檔案。

# 系統存取控制清單 ( System Access Control List, SACL )

系統存取控制清單 (SACL)，有時也稱為稽核 ACE，它讓系統管理員可以記錄誰嘗試存取安全性物件。  SACL 與 DACL 類似，它們都是使用 ACE 來定義特定資源的存取規則。  但是與 DACL 不同的是，它不會限制資源的存取，而是當使用者嘗試存取資源時，會產生事件記錄，並記載到 Windows 事件記錄中的安全性類別。  

不過，預設上，即使指派了 SACL 給資源，Windows 也是不會記錄稽核事件的。它必須還得啟動物件的存取稽核原則才行。該項設定位於本機安全性原則管理工具裡頭。如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEPI3sZdaI-rKQBLGf5NjG1agmIgCDcBJn7-YyiEftjQLCwCGoyEOJ2weXMbrHNSMRU1KPY0gIow02j583awCPMrc8FsZeQFqTS1zdsd6pL3yr7LrqMvxQsMMrzXpBwguuIqiOUP47lco/s850/local-security-policy.png)

double-click 稽核物件，勾選失敗，可啟動失敗稽核，勾選成功，可啟動成功稽核。

# 如何檢視與設定組件中的 ACLs

## 有哪些資源提供 ACL 存取

.NET Framework 提供下列資源 ACL 的存取，所有資源的存取控制項目都定義在 [System.Security.AccessControl](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.aspx) 命名空間。  雖然所有資源都有存取控制清單，但不同資源使用的是不一樣的存取控制清單。  

此外，每一種資源型別各有三種不同功能的 ACL 類別：

#### **ObjectSecurity**：安全性類別。這個類別封裝了 DACL 和 SACL 類別。

所有的安全性類別都是繼承自 [NativeObjectSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.nativeobjectsecurity.aspx) 

1. [DirectorySecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.directorysecurity.aspx) ：表示目錄的存取控制和稽核安全性。
2. [FileSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesecurity.aspx) ：表示檔案的存取控制和稽核安全性。
3. [RegistrySecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.registrysecurity) ：表示登錄機碼的 Windows 存取控制 (Access Control) 安全性。
4. [CryptoKeySecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.cryptokeysecurity.aspx) ：提供無需直接管理存取控制清單 (ACL)，即可控制對密碼編譯金鑰物件之存取的功能。
5. [EventWaitHandleSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.eventwaithandlesecurity.aspx) ：表示套用至具名系統等候控制代碼 (Wait Handle) 的 Windows 存取控制安全性。
6. [MutexSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.mutexsecurity) ：表示具名 Mutex 的 Windows 存取控制安全性。
7. [SemaphoreSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.semaphoresecurity.aspx) ：表示具名號誌 (Semaphore) 的 Windows 存取控制安全性。

#### **ObjectAccessRule**：存取規則類別。

封裝 ACE 的類別。它們都是繼承自 AccessRule ，而 AccessRule 繼承自 AuthorizationRule 。

1. [FileSystemAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemaccessrule.aspx) ：表示檔案或目錄的存取規則。
2. [RegistryAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.registryaccessrule.aspx) ：表示登錄機碼的存取規則。
3. [CryptoKeyAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.cryptokeyaccessrule.aspx) ：表示密碼編譯金鑰的存取規則
4. [EventWaitHandleAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.eventwaithandleaccessrule.aspx) ：表示檔案或目錄的存取規則。
5. [MutexAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.mutexaccessrule.aspx) ：表示具名 Mutex 的存取規則。
6. [SemaphoreAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.semaphoreaccessrule.aspx) ：表示具名號誌的存取規則。

#### **ObjectAuditRule**：稽核規則類別。

封裝 Audit ACE 的類別。它們也都是繼承自 AuditRule ，而 AuditRule 繼承自 AuthorizationRule 。

1. [FileSystemAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemauditrule.aspx) ：
2. [RegistryAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.registryauditrule.aspx) ：
3. [CryptoKeyAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.cryptokeyauditrule.aspx) ：
4. [EventWaitHandleAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.eventwaithandleauditrule.aspx) ：
5. [MutexAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.mutexauditrule.aspx) ：
6. [SemaphoreAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.semaphoreauditrule.aspx) ：

## .NET Framework 中的 ACEs

同樣的，雖然各種 ACL 都是由 ACE 組成，但不同資源的 ACE 當然也就不一樣。  例如，雖然檔案系統和登錄資料庫都有 Full Control 和 Delete 使用權限，但 Read&Execute 僅用於檔案和資料夾，而 Query Value 僅用於登錄資料庫。  所以，每個資源都有其專用的類別，不同資源各自的使用權限與功能都類似，且所有類別皆繼承自共同的基底的類別。例如：  

1. [FileSystemRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemrights.aspx) ：這個列舉型別用來指定**檔案**和**資料夾**的使用權限。
2. [RegistryRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.registryrights.aspx) ：這個列舉型別用來指定**登錄資料庫**的使用權限。
3. [CryptoKeyRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.cryptokeyrights.aspx) ：指定授權規則控制存取或稽核的**密碼編譯金鑰**作業。
4. [EventWaitHandleRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.eventwaithandlerights.aspx) ：指定可套用至具名系統**事件物件**的存取控制 (Access Control) 權限。
5. [MutexRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.mutexrights.aspx) ：指定可套用至具名系統 **Mutex 物件**的存取控制權限。
6. [SemaphoreRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.semaphorerights.aspx) ：指定可套用至具名系統**號誌 (Semaphore) 物件**的存取控制 (Access Control) 權限。

## 與 ACE 相關的列舉型別：

####  [AccessControlType](http://msdn.microsoft.com/zh-tw/library/w4ds5h86.aspx) 

指定 AccessRule 物件是用於允許存取還是拒絕存取。這些值不是旗標，它們也不可組合。

- Allow：允許存取 secured object。
- Deny：禁止存取 secured object。

####  [AccessControlSections](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.accesscontrolsections.aspx) 

指定要儲存或載入的安全性描述元 (Security Descriptor) 區段。這個列舉值不可組合使用。

- None ：沒有區段。
- Audit ：系統存取控制清單 (SACL)。
- Access ：自主式存取控制清單 (DACL)。
- Owner ：擁有人。
- Group ：主要群組。
- All ：整個安全性描述元。

####  [FileSystemRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemrights.aspx) 

定義檔案或目錄建立存取規則和稽核規則時要使用的存取權限。這個列舉值可組合使用。

- FullControl  ：
- Read ：
- Write ：
- Modify ：
- Delete ：
- ReadAndExecute ：
- ListDirectory ：
- Other members ：

####  [AuditFlags](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.auditflags.aspx) 

稽核嘗試存取安全物件的條件。這個列舉值可組合使用。

- None ：不稽核
- Success ：稽核**成功的存取嘗試**。
- Failure ：稽核**失敗的存取嘗試**

## 牛刀小試

若要針對檔案系統設定 ACL ，大至步驟如下：  

1. 建立 [FileSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesecurity.aspx) 檔案安全性類別的執行個體
2. 使用 [FileSystemAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemaccessrule.aspx) 類別建立存取規則  
或使用 [FileSystemAuditRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemauditrule.aspx) 類別建立稽核規則
3. 使用 [FileSystemRights](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemrights.aspx) 列舉型別設定權限
4. 叫用 [File.SetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.setaccesscontrol.aspx) 方法，套用權限設定
```c#
string fileName = "test.xml";
string account=".\vito";
FileSystemRights rights;
AccessControlType controlType;

rights = FileSystemRights.ReadData;
controlType = AccessControlType.Allow;

//=====================================================
// 建立檔案的 ACL 
//=====================================================

FileSecurity fSecurity = File.GetAccessControl(fileName);
FileSystemAccessRule fAccessRule = new FileSystemAccessRule(account, rights, controlType);

//=====================================================
// 加入存取規則，並套用至指定的檔案
//=====================================================

// 在 ACL 中加入存取規則
fSecurity.AddAccessRule(fAccessRule);

// 套用至指定的檔案
File.SetAccessControl(fileName, fSecurity);

//=====================================================
// 移除存取規則，並套用至指定的檔案
//=====================================================

// 移除在 ACL 中的存取規則
fSecurity.RemoveAccessRule(fAccessRule);

// 套用至指定的檔案
File.SetAccessControl(fileName, fSecurity);
```

下面範例示範如何建立登錄資料庫的存取規則和稽核規則：
```c#
FileSecurity File_Security = new FileSecurity();
FileSystemAccessRule Access_Rule = new FileSystemAccessRule("vito", FileSystemRights.Read, AccessControlType.Allow);
FileSystemAuditRule Audit_Rule = new FileSystemAuditRule("vito", FileSystemRights.Read, AuditFlags.Failure);

MutexSecurity Mutex_Security = new MutexSecurity();
MutexAccessRule Mutex_AccessRule = new MutexAccessRule("vito", MutexRights.FullControl, AccessControlType.Allow);
MutexAuditRule Mutex_AuditRule = new MutexAuditRule(new NTAccount("vito"), MutexRights.Delete, AuditFlags.Success);

RegistrySecurity Registry_Security = new RegistrySecurity();
RegistryAccessRule Registry_AccessRule = new RegistryAccessRule("vito", RegistryRights.FullControl, AccessControlType.Allow);
RegistryAuditRule Registry_AuditRule = new RegistryAuditRule(new NTAccount("vito"), RegistryRights.Delete, 
InheritanceFlags.ContainerInherit, PropagationFlags.NoPropagateInherit,AuditFlags.Success);
```

## 如何讀取資源的 ACLs

#### 讀取 DACL

若要取得資源的 ACLs ，可依照下列步驟：

1. 建立 [NativeObjectSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.nativeobjectsecurity.aspx) 衍生類別的執行個體，如 [DirectorySecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.directorysecurity.aspx) 、 [FileSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesecurity.aspx) 。  
要建立 [NativeObjectSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.nativeobjectsecurity.aspx) 衍生類別的執行個體，除了建立 &lt;ResourceObject&gt;Security 類別外，通常也可以由相關類別中的 [GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.directory.getaccesscontrol.aspx) 方法取得。例如：  

- [RegistryKey.GetAccessControl](http://msdn.microsoft.com/zh-tw/library/microsoft.win32.registrykey.getaccesscontrol.aspx) ：傳回目前登錄機碼的存取控制安全性。
- [Directory.GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.directory.getaccesscontrol.aspx) ：擷取目錄的存取控制清單 (ACL) 項目。
- [File.GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.getaccesscontrol.aspx) ：擷取指定檔案的存取控制清單 (ACL) 項目。
2. 呼叫執行個體的 [GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) 方法以取得存取規則的集合( [AuthorizationRuleCollection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.authorizationrulecollection.aspx) ) 。
3. 逐一檢查 [AuthorizationRuleCollection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.authorizationrulecollection.aspx) 中的項目，以分析每個 ACL。

雖然用來取得 ACL 物件的類別不盡相同，但方法是相同的。不過要注意，每一種 ACL 物件的 [GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) 方法，回傳的集合，其內含項目是不同的。例如：

- [RegistrySecurity.GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) ： 回傳包含 [RegistryAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.registryaccessrule.aspx) 的 [AuthorizationRuleCollection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.authorizationrulecollection.aspx) 。
- [DirectorySecurity.GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) ： 回傳包含 [FileSystemAccessRule](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesystemaccessrule.aspx) 的 [AuthorizationRuleCollection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.authorizationrulecollection.aspx) 。

#### 取得檔案的存取控制清單： [File.GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.getaccesscontrol.aspx) 
```c#
public static FileSecurity GetAccessControl(string path);
public static FileSecurity GetAccessControl(string path, AccessControlSections includeSections);
```

#### 取得目錄的存取控制清單： [Directory.GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.directory.getaccesscontrol.aspx) 
```c#
public static DirectorySecurity GetAccessControl(string path);
public static DirectorySecurity GetAccessControl(string path, AccessControlSections includeSections);
```

#### 取得與指定之安全識別項相關聯的存取規則集合： [GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) 
```c#
public AuthorizationRuleCollection GetAccessRules(bool includeExplicit, bool includeInherited, Type targetType);
```
```c#
// 1) 建立 NativeObjectSecurity 衍生類別的執行個體
DirectorySecurity dSecurity = new DirectorySecurity(@"C:\Program Files", AccessControlSections.Access);

// 1) 透過 Directory.GetAccessControl 也可以取得相同執行個體
DirectorySecurity dSecurity2 = Directory.GetAccessControl(@"C:\Program Files", AccessControlSections.Access);

// 2) 呼叫 GetAccessRules 方法，以取得 AuthorizationRuleCollection
AuthorizationRuleCollection accessRules = dSecurity.GetAccessRules(true, true, typeof(NTAccount));

// 3) 逐一檢查 AuthorizationRuleCollection 執行個體中的項目
foreach (FileSystemAccessRule accessRule in accessRules)
Console.WriteLine(accessRule.IdentityReference + ": " + accessRule.AccessControlType + " " + accessRule.FileSystemRights);

//CREATOR OWNER                 : Allow 268435456
//NT AUTHORITY\SYSTEM           : Allow 268435456
//NT AUTHORITY\SYSTEM           : Allow Modify, Synchronize
//BUILTIN\Administrators        : Allow 268435456
//BUILTIN\Administrators        : Allow Modify, Synchronize
//BUILTIN\Users                 : Allow -1610612736
//BUILTIN\Users                 : Allow ReadAndExecute, Synchronize
//NT SERVICE\TrustedInstaller   : Allow 268435456
//NT SERVICE\TrustedInstaller   : Allow FullControl

// 底下方法可以用來驗證特定使用者的存取權限

string NtAccountName = "VITO-2011W7\\vito"; 
foreach (FileSystemAccessRule rule in accessRules)
{
if (rule.IdentityReference.Value.Equals(NtAccountName, StringComparison.CurrentCultureIgnoreCase))
{
if ((rule.FileSystemRights & FileSystemRights.WriteData) > 0)
{
Console.WriteLine("{0} has WriteData permission to {1}", NtAccountName, dir);
}
if ((rule.FileSystemRights & FileSystemRights.ReadAndExecute) > 0)
{
Console.WriteLine("{0} has ReadAndExecute permission to {1}", NtAccountName, dir);
}
if ((rule.FileSystemRights & FileSystemRights.Delete) > 0)
{
Console.WriteLine("{0} has Delete permission to {1}", NtAccountName, dir);
}
}
}
```
```c#
// 1) 建立 NativeObjectSecurity 衍生類別的執行個體
RegistrySecurity rs = Registry.LocalMachine.GetAccessControl();

// 2) 呼叫 GetAccessRules 方法，以取得 AuthorizationRuleCollection
AuthorizationRuleCollection arc = rs.GetAccessRules(true, true, typeof(NTAccount));

// 3) 逐一檢查 AuthorizationRuleCollection 執行個體中的項目
foreach (RegistryAccessRule ar in arc)
Console.WriteLine(ar.IdentityReference + ": " + ar.AccessControlType + " " + ar.RegistryRights);

//Everyone                  : Allow ReadKey
//NT AUTHORITY\RESTRICTED   : Allow ReadKey
//NT AUTHORITY\SYSTEM       : Allow FullControl
//BUILTIN\Administrators    : Allow FullControl
```

#### 讀取 SACL

若要分析 SACLs ，可使用相同步驟，但呼叫 [GetAuditRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getauditrules.aspx) 來替換 [GetAccessRules](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.commonobjectsecurity.getaccessrules.aspx) 。
```c#
// 1) 建立 NativeObjectSecurity 衍生類別的執行個體
DirectorySecurity dSecurity = new DirectorySecurity(@"C:\Program Files", AccessControlSections.Access);

// 1) 透過 Directory.GetAccessControl 也可以取得相同執行個體
DirectorySecurity dSecurity2 = Directory.GetAccessControl(@"C:\Program Files", AccessControlSections.Access);

// 2) 呼叫 GetAccessRules 方法，以取得 AuthorizationRuleCollection
AuthorizationRuleCollection accessRules = dSecurity.GetAuditRules(true, true, typeof(NTAccount));

// 3) 逐一檢查 AuthorizationRuleCollection 執行個體中的項目
foreach (FileSystemAccessRule accessRule in accessRules)
Console.WriteLine(accessRule.IdentityReference + ": " + accessRule.AccessControlType + " " + accessRule.FileSystemRights);
```

## 如何設定 ACLs

#### 使用 [SetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.setaccesscontrol.aspx) 

[SetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.setaccesscontrol.aspx) 是用來將 ACLs 套用至指定的物件上。若要設定 ACLs ，可依照下列步驟：

- 1. 呼叫 [GetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.directory.getaccesscontrol.aspx) 方法以取得 [NativeObjectSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.nativeobjectsecurity.aspx) 衍生類別的執行個體，如 [DirectorySecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.directorysecurity.aspx) 、 [FileSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.filesecurity.aspx) 。
- 2. 加入或移除 ACL 項目。
- 3. 呼叫 [SetAccessControl](http://msdn.microsoft.com/zh-tw/library/system.io.file.setaccesscontrol.aspx) 方法執行寫入作業。

同樣的，因為資源不同，就必須使用不同的控制存取安全物件，
```c#
//File 的 SetAccessControl
public static void SetAccessControl(string path, FileSecurity fileSecurity);

//Directory 的 SetAccessControl
public static void SetAccessControl(string path, DirectorySecurity directorySecurity);
```
```c#
string dir1 = @"C:\temp\sub1";
string account = "vito";
FileSystemRights rights = FileSystemRights.DeleteSubdirectoriesAndFiles | FileSystemRights.FullControl;
AccessControlType rule = AccessControlType.Allow;

// 1)加入安全性 
DirectorySecurity dirSecurity1 = Directory.GetAccessControl(dir1);              //取得目前現有安全性
dirSecurity1.AddAccessRule(new FileSystemAccessRule(account, rights, rule));    //加入安全性
Directory.SetAccessControl(dir1, dirSecurity1);                                 //套用新的安全性

// 2)移除安全性 
DirectorySecurity dirSecurity2 = Directory.GetAccessControl(dir1);
dirSecurity2.RemoveAccessRule(new FileSystemAccessRule(account, rights, rule)); // 移除 AccessRule 規則
Directory.SetAccessControl(dir1, dirSecurity2);                                 // 套用規則

// 3)新設安全性 
string dir2 = @"C:\temp\sub2";
DirectorySecurity dirSecurity3 = new DirectorySecurity();
dirSecurity3.AddAccessRule(new FileSystemAccessRule(account, rights, rule));
if (!Directory.Exists(dir2))
Directory.CreateDirectory(dir2, dirSecurity3);
else
Directory.SetAccessControl(dir2, dirSecurity3);
```

#### 使用 [SetAccessRuleProtection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.objectsecurity.setaccessruleprotection.aspx) 

當我們使用 File 或 Directory 的時候，它們的存取權限預設都會繼承父項物件。  這個情況包含變更父項物件的存取權限時，其子物件的存取權限預設會因為繼承特性而自動跟著變更。  或者當我們將某個子目錄移動到另一個目錄之下，這個子目錄的存取權限，也會因為繼承特性而自動跟著變更。  

[SetAccessRuleProtection](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.objectsecurity.setaccessruleprotection.aspx) 這個方法就是用來設定或移除與這個 [ObjectSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.accesscontrol.objectsecurity.aspx) 物件相關聯的存取規則保護。  
```c#
SetAccessRuleProtection(bool isProtected, bool preserveInheritance);
```

- isProtected：true 表示使用目前物件的存取規則，不因繼承而改變；false 表示允許因繼承而變更存取規則。
- preserveInheritance：true 表示保留繼承的存取規則，而 false 則表示移除繼承的存取規則。如果 isProtected 為 false，則忽略這個參數。

下面這個例子，我們套用 dir1 的存取權限到新建立的 dir2 身上，並將 dir2 的 ACL 設定成保護。  這樣子，不管將來 dir2 上層的權限如何變更，或被搬移到哪個目錄之下，它的存取權限都不會因為繼承而被變更。  
```c#
string dir1 = @"C:\temp\test1";
string dir2 = @"C:\temp\test2";

DirectorySecurity dirSecurity = Directory.GetAccessControl(dir1, AccessControlSections.All);

bool isProtected = true;
bool isPreserved = true;
dirSecurity.SetAccessRuleProtection(isProtected, isPreserved);

Directory.CreateDirectory(dir2, dirSecurity);
```


- [ACL 技術概觀](http://msdn.microsoft.com/zh-tw/library/ms229742.aspx)
- [小朱®：判別式存取控制表 (Discretionary Access Control List) 實作](http://www.dotblogs.com.tw/regionbbs/archive/2011/07/28/designing.dacl.concept.and.prototype.aspx)