---
title: 使用者的驗證與授權
layout: default
parent: 使用者與資料安全性
nav_order: 6
description: "使用者的驗證與授權"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "2367026951926838239"
---
# 驗證、授權、識別

驗證、授權、識別有什麼差別？

- **驗證 (Authentication)**   
驗證是取得識別認證的程序。  目的是要判斷使用者是不是他所宣稱的那個人，  如帳號密碼機制，是基於帳號密碼為只有本人跟系統本身才知道的 shared secret，所以只要可以正確輸入密碼，系統就可判斷使用者為這個帳號所代表的人物。
- **授權 (Authurization)**   
授權是授與特定識別對特定資源的存取權限。  如會員登入後擁有讀寫資源的權力，而訪客只有讀的權力。
- **識別 (Identification)**   
識別用來分辨使用者是誰，識別必須是獨一無二的，才能正確的分辨出每個人。

系統要知道某個使用者對系統資源的存取權力，包含三個部分

1. 使用者告訴系統他是誰(Identification 機制)。  
‧例：輸入ID
2. 系統判斷使用者是否真的是他宣稱的那個人(Authentication 機制)。  
‧例：輸入Password
3. 系統根據該帳號所擁有的權限驗証該使用者(Auorization 機制)。  
‧例：系統判斷該使用者為會員，給予讀及寫的權利

# 應用程式授權

驗證與授權的過程大至是這樣的：  

例如，一個**認證實體**(Principal)使用特定的使用者名稱和密碼，交由驗證程序進行驗證，如果認證結果為有效，則回傳給該認證實體一個已通過驗證的識別 (Identity)。  之後則可以根據這個識別，授與特定資源的存取權限。  

驗證與授權是為了達到資訊安全所必要的一個過程，.Net Framework 就提供了幾種機制以達到這個目的。例如：  

- **角色為主安全性 ( Role-base security ; RBS )**：這個機制是依據**使用者名稱**或**所屬群組**控制使用者存取資料的權限。
- **存取控制清單 ( access control lists ; ACLs )**：這個機制是提供給作業系統使用的方法，用來追蹤誰能夠存取什麼物件、或者決定哪些動作必須加入log事件。
- **加密工具 ( Cryptography tools)**：這個機制可以用來對資料加密、驗證、簽章。(encrypting, validating, signing)

## ASP.NET 的認證與授權組態

底下是 asp.net 中用來設定認證與授權組態
```xml
<!-- 認證 -->
<authentication mode="Forms">   <!-- 使用表單認證 -->
<forms loginUrl="~/Account/Login.aspx" timeout="2880" />
</authentication>

<!-- 授權 -->
<authorization>
<deny users="?" />            <!-- 禁止匿名者 -->
<allow users="vito"/>         <!-- 允許 vito -->
<deny users="shao"/>          <!-- 禁止 shao -->
</authorization>
```

關於「\*」 和「?」

- \* ：所有使用者，包含通過驗證或未通過驗證的使用者。
- ? ：匿名使用者，尚未通過驗證的使用者。

## System.Security.Principal 命名空間

[System.Security.Principal](http://msdn.microsoft.com/zh-tw/library/System.Security.Principal.aspx) 命名空間中提供角色架構的安全性實作，可以使用它來執行應用程式授權。  若要在 .NET Framework 中使用應用程式授權，必須建立 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 與 [IPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iprincipal.aspx) 物件來代表使用者。  IIdentity 會封裝已驗證的使用者。IPrincipal 是識別使用者及其擁有之任何角色的組合。  下面列出幾個主要類別的說明：  

- [WindowsIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.aspx) ：是 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 的實作，代表一個        **Windows 使用者**帳戶。
- [WindowsPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.aspx) ：是 [IPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iprincipal.aspx) 的實作，主要用來檢查 Windows 使用者**群組**。
- [PrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.security.principal.principalpolicy.aspx) ：指定應該如何替應用程式網域建立 Principal 和 Identity 物件。
- [GenericIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericidentity.aspx) ：代表泛型使用者。
- [GenericPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericprincipal.aspx) ：代表泛型主體。

# WindowsIdentity 類別

## 關於 WindowsIdentity 類別

[WindowsIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.aspx) 類別是用來表示一個 Windows 帳戶。

#### WindowsIdentity 建構子
```c#
public WindowsIdentity(IntPtr userToken);
public WindowsIdentity(IntPtr userToken, string type);
public WindowsIdentity(string sUserPrincipalName);
public WindowsIdentity(string sUserPrincipalName, string type);
...
```

#### WindowsIdentity 屬性

- [AuthenticationType](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.authenticationtype.aspx) ：the authentication method. This is usually “NTLM”.
- [Groups](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.groups.aspx) ：取得目前 Windows 使用者所屬的群組。
- [IsAnonymous](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.isanonymous.aspx) ：if the user is anonymous.
- [IsAuthenticated](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.isauthenticated.aspx) ：if the user is authenticated.
- [IsGuest](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.isguest.aspx) ：if the user is a guest.
- [IsSystem](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.issystem.aspx) ：if the user is part of the system.
- [Name](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.name.aspx) ：the authentication domain and user name of the user
- [Token](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.token.aspx) ：An integer representing the user's authentication token,

#### WindowsIdentity 方法

- [GetAnonymous](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.getanonymous.aspx) ：回傳一個匿名帳戶
- [GetCurrent](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.getcurrent.aspx) ：回傳目前帳戶
- [Impersonate](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.impersonate.aspx) ：回傳一個指定的帳戶，可用來模擬一個指定的身分。建立模擬後，請務必呼叫 [Undo](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsimpersonationcontext.undo.aspx) 方法來結束模擬。
```c#
WindowsIdentity user = WindowsIdentity.GetCurrent();
Console.WriteLine("Name: {0}", user.Name);                                  //VITO-2011W7\Administrator
Console.WriteLine("AuthenticationType: {0}", user.AuthenticationType);      //NTLM (NT LAN Manager, 微軟NT區網的認識機制) 
Console.WriteLine("Token: " + user.Token.ToString());                       //816

Console.WriteLine("Is an authenticated user: {0}", user.IsAuthenticated);    //True
Console.WriteLine("Is an anonymous user: {0}", user.IsAnonymous);            //False
Console.WriteLine("Is a guest: {0}", user.IsGuest);                          //False
Console.WriteLine("Is part of the system: {0}", user.IsSystem);              //False
```

## 使用 WindowsIdentity.Impersonate 建立模擬身分

有時候，某些資源僅開放特定使用者權限，例如存取檔案伺服器上的檔案，這時候可能就得使用模擬身分的功能去執行。
```c#
[DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Unicode)]
public static extern bool LogonUser(string lpszUsername, string lpszDomain, string lpszPassword, int dwLogonType, int dwLogonProvider, out IntPtr token);

[DllImport("kernel32.dll", CharSet = CharSet.Auto)]
public extern static bool CloseHandle(IntPtr handle);

const int LOGON32_PROVIDER_DEFAULT = 0;
const int LOGON32_LOGON_INTERACTIVE = 2; //This parameter causes LogonUser to create a primary token.

private void btnImpersonate_Click(object sender, EventArgs e)
{
IntPtr impersonatedToken = IntPtr.Zero;

try
{
string domainName = Environment.UserDomainName;
string userName = "vito";
string password = "2213";

// 以模擬身分進行認證, 取得模擬身份者的 token
bool returnValue = LogonUser(userName, domainName, password, LOGON32_LOGON_INTERACTIVE, LOGON32_PROVIDER_DEFAULT, out impersonatedToken);

// 檢查認證是否正確
if (false == returnValue)
{
int ret = Marshal.GetLastWin32Error();
Console.WriteLine("LogonUser failed with error code : {0}", ret);
throw new System.ComponentModel.Win32Exception(ret);
}
else
{
// 檢查一下原來的身分
Console.WriteLine("=== Before impersonation ===");
Console.WriteLine("Name：" + WindowsIdentity.GetCurrent().Name);                 //Name：VITO-2011W7\Administrator
Console.WriteLine("Token：" + WindowsIdentity.GetCurrent().Token.ToString());    //Token：1268

// 建立模擬身分
WindowsIdentity newID = new WindowsIdentity(impersonatedToken);                  
WindowsImpersonationContext impersonatedUser = newID.Impersonate();            

Console.WriteLine("=== After impersonation ===");
Console.WriteLine("Name：" + WindowsIdentity.GetCurrent().Name);                 //Name：VITO-2011W7\vito
Console.WriteLine("Token：" + WindowsIdentity.GetCurrent().Token.ToString());    //Token：1288

// 結束使用模擬身分
if (impersonatedUser != null)
impersonatedUser.Undo();            //還原成原來的使用者

if (impersonatedToken != IntPtr.Zero)  // 釋放資源
CloseHandle(impersonatedToken);

// 檢查一下目前身分
Console.WriteLine("=== After closing the context ===");
Console.WriteLine("Name：" + WindowsIdentity.GetCurrent().Name);                 //Name：VITO-2011W7\Administrator
Console.WriteLine("Token：" + WindowsIdentity.GetCurrent().Token.ToString());    //Token：1296
}
}
catch (Exception ex)
{
Console.WriteLine("Exception occurred. " + ex.Message);
}
}
```

# WindowsPrincipal 類別

## 關於 WindowsPrincipal 類別

[WindowsPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.aspx) 類別是用來表示使用者群組的資料，主要用來檢查 Windows 使用者的角色。

#### WindowsPrincipal 建構子
```c#
public WindowsPrincipal(WindowsIdentity ntIdentity)
```

#### WindowsPrincipal 方法

WindowsPrincipal 只有一個 [IsInRole](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.isinrole.aspx) 方法，用來判斷目前使用者是否為其成員。  
```c#
public virtual bool IsInRole(int rid);
public virtual bool IsInRole(SecurityIdentifier sid);
public virtual bool IsInRole(string role);
public virtual bool IsInRole(WindowsBuiltInRole role);
```

## 如何建立 WindowsPrincipal

#### 如何建立 WindowsPrincipal

要建立 [WindowsPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.aspx) 物件，有二個方式：

1. 以 [WindowsIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.aspx) 執行個體為參數，建構 [WindowsPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.aspx) 。
2. 由目前的執行緒中抽取出目前的 [WindowsPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.aspx) 物件。

#### [AppDomain.SetPrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setprincipalpolicy.aspx)
```c#
public void SetPrincipalPolicy(PrincipalPolicy policy);
```

This method specifies how principal and identity objects should be attached to a thread 。

這個方法是用來設定目前執行序的主體 (Principal) ，它必須在使用 Thread.CurrentPrincipal 屬性之前設定，設定值才會有效。  例如，如果您將 Thread.CurrentPrincipal 設定為指定之主體 (例如泛型主體)，然後使用 [SetPrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setprincipalpolicy.aspx) 方法將 [PrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.security.principal.principalpolicy.aspx) 設定為 WindowsPrincipal，則目前的主體仍然會是泛型主體。  

####  [PrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.security.principal.principalpolicy.aspx) 

這個列舉值是用來指定應如何為應用程式網域建立 Principal 和 Identity 物件。 預設值為 UnauthenticatedPrincipal。 

- UnauthenticatedPrincipal：應該建立未驗證實體 (Entity) 的 Principal 和 Identity 物件。未驗證實體具有設定為空字串 ("") 的 [Name](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.name.aspx) 和設定為 false 的 IsAuthenticated。
- NoPrincipal：不應該建立 Principal 和 Identity 物件。
- WindowsPrincipal：應該建立反映與目前所執行的執行緒相關聯作業系統 [Token](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.token.aspx) 的 Principal 和 Identity 物件，並且相關聯的作業系統群組應該對應至角色。
```c#
//==============================================================
//1)透過 WindowsIdentity , 建立 WindowsPrincipal                
//==============================================================

//由目前帳戶取得目前的原則
WindowsIdentity user = WindowsIdentity.GetCurrent();            
WindowsPrincipal principal2 = new WindowsPrincipal(user);

//==============================================================
//2)透過 current thread , 取得 WindowsPrincipal                 
//==============================================================

//由目前執行緒取得目前的原則
AppDomain.CurrentDomain.SetPrincipalPolicy(PrincipalPolicy.WindowsPrincipal);   //設定應用程式定義域的主要原則
WindowsPrincipal principal1 = (WindowsPrincipal)Thread.CurrentPrincipal;        //由目前執行緒取得目前的原則
```

## 如何判別使用者是否具有某個角色權限

WindowsPrincipal.[IsInRole](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsprincipal.isinrole.aspx) ：這個方法可以判斷目前使用者是否為其成員。

[WindowsBuiltInRole](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsbuiltinrole.aspx)  ：這個類別是用來表示**系統內建的群組**。
```c#
// 透過 WindowsIdentity , 建立 WindowsPrincipal
WindowsIdentity user = WindowsIdentity.GetCurrent();
WindowsPrincipal userPrincipal = new WindowsPrincipal(user);

// 判斷目前使用者是否為某內建群組的成員
Console.WriteLine("Is Administrator : {0}", userPrincipal.IsInRole(WindowsBuiltInRole.Administrator));  //True
Console.WriteLine("Is PowerUser     : {0}", userPrincipal.IsInRole(WindowsBuiltInRole.PowerUser));      //False
Console.WriteLine("Is User          : {0}", userPrincipal.IsInRole(WindowsBuiltInRole.User));           //True

// 判斷目前使用者是否為自訂群組的成員
Console.WriteLine("User is in HomeUsers ? {0}", userPrincipal.IsInRole("HomeUsers"));            //True
Console.WriteLine("User is in Administrators ? {0}", userPrincipal.IsInRole("Administrators"));  //True
Console.WriteLine("User is in Guests ? {0}", userPrincipal.IsInRole("Guests"));                  //False
Console.WriteLine("User is in IIS_IUSRS ? {0}", userPrincipal.IsInRole("IIS_IUSRS"));            //False
```

## 如何列舉使用者所屬群組

- [IdentityReference](http://msdn.microsoft.com/zh-tw/library/system.security.principal.identityreference.aspx) ：表示識別 (Identity)，且為 [NTAccount](http://msdn.microsoft.com/zh-tw/library/system.security.principal.ntaccount.aspx) 和 [SecurityIdentifier](http://msdn.microsoft.com/zh-tw/library/system.security.principal.securityidentifier.aspx) 類別的基底類別。
- [NTAccount](http://msdn.microsoft.com/zh-tw/library/system.security.principal.ntaccount.aspx) ：代表使用者或群組帳戶。
- [SecurityIdentifier](http://msdn.microsoft.com/zh-tw/library/system.security.principal.securityidentifier.aspx) ：代表安全識別項 (SID)，並為 SID 提供封送處理 (Marshaling) 和比較作業。
```c#
WindowsIdentity user = WindowsIdentity.GetCurrent();

foreach (IdentityReference refGroup in user.Groups)
{
NTAccount acc = refGroup.Translate(typeof(NTAccount)) as NTAccount;
Console.WriteLine(acc.Value);
}
//Everyone
//VITO-2011W7\HomeUsers
//BUILTIN\Administrators
//BUILTIN\Users
//......
```

若要查詢某網域的自訂群組，可將格式更改為「DomainName\GroupName」。  
```c#
// 若要查詢某網域的自訂群組，可將格式更改為「DomainName\GroupName」，下面二個方法都可以取得 網域名稱
string sMachineName = System.Environment.MachineName;
string UserDomainName = System.Environment.UserDomainName;
Console.WriteLine("MachineName : {0}", sMachineName);           //MachineName : VITO-2011W7
Console.WriteLine("UserDomainName : {0}", UserDomainName);      //UserDomainName : VITO-2011W7

// 判斷目前使用者是否為某網域群組的成員
Console.WriteLine(@"User is in Administrators ? {0}", currentPrincipal.IsInRole(UserDomainName + @"\Administrators"));
```

# PrincipalPermission 類別

## 什麼是 PrincipalPermission 類別

[PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.aspx) 類別和與其相關的 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermissionattribute.aspx) 類別，可用來限定現行主體 (active principal) 必須符合指定的權限要求。  譬如限制某個角色或使用者才可以進入執行方法。   [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.aspx) 可由下列三種屬性參數組合而成：  
```c#
public PrincipalPermission(
string name,            //：指定有權限資格的特定使用者
string role,            //：指定有權限資格的群組成員
bool isAuthenticated    //：true 表示使用者是必須是已驗證的，否則為 false
)
```
```c#
string role = System.Environment.MachineName + @"\IIS_IUSRS";
PrincipalPermission p = new PrincipalPermission(null, role, true);  //限定IIS_IUSRS的成員,且已驗證,才擁有權限
```

## 如何以宣告方式限制使用者或角色存取物件所提供的方法 (Declarative RBS demands)

以宣告方式做為程式碼權限控管的做法是，在每個 method 被執行之前，先進行 RBS 檢查。這種做法是最安全的，因為每個 method 在被執行之前，都必須先確定安全性。  但是，這種做法也有兩個主要的缺點：  

- 僅能對整個方法使用，無法使用在局部的程式碼。
- 錯誤導至的例外狀況，若是被 Windows 攔截的話，程式可能因此被迫停止。

使用宣告式 RBS 要求，程式碼中必須要有下列三個元素：  

- 1. 指定主體安全原則 (principal security policy) ：使用 [AppDomain.SetPrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setprincipalpolicy.aspx) 方法指定。
- 2. 需要有一個 Try/Catch 以攔截非法呼叫的嘗試。
- 3. 在 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermissionattribute.aspx) 屬性裡宣告存取方法的條件。
```c#
private void btnPrincipalPermission_Click(object sender, EventArgs e)
{
// 1. 指定主體原則 (principal security policy)
AppDomain currentDomain = AppDomain.CurrentDomain;
currentDomain.SetPrincipalPolicy(PrincipalPolicy.WindowsPrincipal);

// 2. 攔截非法呼叫
try
{
AdministratorsOnlyMethod();
}
catch (System.Security.SecurityException ex)
{
MessageBox.Show("Current User lacks Administrators Permission");
}
}
```

最後透過一個或多個 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermissionattribute.aspx) 屬性，以限制存取的權限。下面範例是一個多重宣告式需求，**至少必須符合其中一個條件**，程式碼才會允許執行
```c#
[PrincipalPermission(SecurityAction.Demand, Role = @"Administrators")]                                      //必須是 Administrators 成員才可以存取
[PrincipalPermission(SecurityAction.Demand, Role = @"VITO-2011W7\HomeUsers", Name = @"VITO-2011W7\vito")]   //必須是 VITO-2011W7\HomeUsers 成員中的 vito 使用者才可以存取
public void AdministratorsOnlyMethod()
{
// Code that can only be run by Administrators }
}
```

## 如何以命令方式限制使用者或角色存取部份程式邏輯 (Imperative RBS demands)

以命令方式做為程式碼權限控管的做法，可以用來限制 method 之中，部份的程式碼。  

使用命令式 RBS 要求，程式碼中必須要有下列四個元素：  

- 1. 指定主體安全原則 (principal security policy) ：使用 [AppDomain.SetPrincipalPolicy](http://msdn.microsoft.com/zh-tw/library/system.appdomain.setprincipalpolicy.aspx) 方法指定。
- 2. 需要有一個 Try/Catch 以攔截非法呼叫的嘗試。
- 3. 一個 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.aspx) 物件，並在屬性裡設定好限制存取的條件。
- 4. 呼叫 [PrincipalPermission.Demand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.demand.aspx) 方法以定義方法的存取需求。

要注意的是，這四點中的前二點和宣告式RBS要求是一樣的，但是第三點的 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.aspx) 物件則是不相同的。  

- 宣告式的 PrincipalPermission，回傳的是 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermissionattribute.aspx) 物件。
- 命令式的 PrincipalPermission，回傳的是 [PrincipalPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.principalpermission.aspx) 物件。
```c#
// 1) 指定以Windows安全性檔做主體原則 (principal security policy)
System.AppDomain.CurrentDomain.SetPrincipalPolicy(PrincipalPolicy.WindowsPrincipal);

// 2) Try/Catch 以攔截非法呼叫
try
{
// 3) 建立 PrincipalPermission 物件，
string user = "vito";                           // 使用者名稱
string role = @"VITO-2011W7\\IIS_IUSRS";        // 群組名稱
PrincipalPermission p = new PrincipalPermission(user, role, true);  // 限定 IIS_IUSRS 群組下的成員 vito，且已驗證過，才擁有執行程式碼的權限

// 4) 驗證目前主體是否符合權限要求
p.Demand();

Console.WriteLine("Access allowed.");
}
catch (System.Security.SecurityException ex)
{
Console.WriteLine("Access denied: " + ex.Message);
}
```

# 如何實作自訂使用者和角色

若要使用自訂資料庫中的使用者資料來驗證使用者，就必須自行實作 [Principal.IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 和 [Principal.IPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iprincipal.aspx) 介面。  

## 自訂 Identity & Principal

[WindowsIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.windowsidentity.aspx) 類別是 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 的實作，其所提供的屬性與方法都是直接繼承自 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 。  同樣的，FormIdentity、PassportIdentity 也是 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 的實作，主要用來執行 Web 驗證。  若現有的 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 實作都無法滿足需求，我們也可以使用 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 為基底，自行實作自訂類別以擴充它的功能。  
```c#
public class CustomIdentity :IIdentity
{
// 必須實作的屬性
public string AuthenticationType { get; private set; }
public bool IsAuthenticated { get; private set; }
public string Name { get; private set; }

// 自訂擴充屬性
public string FirstName { get; private set; }
public string LastName { get; private set; }
public string Address { get; private set; }
public string City { get; private set; }
public string State { get; private set; }
public string Zip { get; private set; }

// 必須實作一個 constructor
public CustomIdentity()
{
this.Name = String.Empty;
this.IsAuthenticated = false;
this.AuthenticationType = "None";
this.FirstName = String.Empty;
this.LastName = String.Empty;
this.Address = String.Empty;
this.City = String.Empty;
this.State = String.Empty;
this.Zip = String.Empty;
}

public CustomIdentity(bool isLogin, string newAuthenticationType, string newFirstName, string newLastName,
string newAddress, string newCity, string newState, string newZip)
{
// Create a unique username by concatenating first and last name
this.Name = newFirstName + newLastName;
this.IsAuthenticated = isLogin;
this.AuthenticationType = newAuthenticationType;
this.FirstName = newFirstName;
this.LastName = newLastName;
this.Address = newAddress;
this.City = newCity;
this.State = newState;
this.Zip = newZip;
}
}
```
```c#
public class CustomPrincipal :IPrincipal
{
public IIdentity Identity { get; private set;}
private string[] _roles;

public CustomPrincipal(IIdentity identity, string[] roles)
{
Identity = identity;
_roles = new string[roles.Length];
roles.CopyTo(_roles, 0);
Array.Sort(_roles);
}

public bool IsInRole(string role)
{
return Array.BinarySearch(_roles, role) >= 0 ? true : false;
}
}
```
```c#
//建立使用者
CustomIdentity userA = new CustomIdentity(true, "myAuthType","vito","shao","","","","241");                         //指定名稱的使用者
CustomIdentity userB = new CustomIdentity(true, "myAuthType", "peter", "huang", "", "", "", "117");                 //指定名稱的使用者

//建立原則
String[] myRoles1 = new String[] { "IT", "Users", "Administrators" };
CustomPrincipal myPrincipal = new CustomPrincipal(userA, myRoles1);  //依使用者和角色名稱陣列，建立實體

//判斷目前的原則，是否包含指定的角色
Console.WriteLine("IsInRole : {0}", myPrincipal.IsInRole("Users"));   //True
```

## 自訂簡單使用者權限模型 ( User Privilege Model )

若不想使用內建的 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 和 [IPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iprincipal.aspx) 為基底類別，而且只需要 [IIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iidentity.aspx) 和 [IPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.iprincipal.aspx) 所提供的基本功能，可以使用 [GenericIdentity](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericidentity.aspx) 和 [GenericPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericprincipal.aspx) 。  
```c#
public GenericIdentity(string name);
public GenericIdentity(string name, string type);

public GenericPrincipal(IIdentity identity, string[] roles);
```
```c#
//建立使用者
GenericIdentity myUser1 = new GenericIdentity("admin");                 //指定名稱的使用者
GenericIdentity myUser2 = new GenericIdentity("vito", "SmartCard");     //指定名稱和驗證 (Authentication) 類型的使用者

//建立原則
String[] myUser1Roles = new String[] { "IT", "Users", "Administrators" };
GenericPrincipal myPrincipal1 = new GenericPrincipal(myUser1, myUser1Roles);  //依使用者和角色名稱陣列，建立實體

//判斷目前的原則，是否包含指定的角色
Console.WriteLine("IsInRole : {0}", myPrincipal1.IsInRole("Users"));   //True
```


- [設計應用程式管理的授權](http://msdn.microsoft.com/zh-tw/library/ms954586.aspx)
- [以角色為基礎的安全性](http://msdn.microsoft.com/zh-tw/library/shz8h065.aspx)
- [Windows 驗證提供者](http://msdn.microsoft.com/zh-tw/library/907hb5w9.aspx)
- [表單驗證提供者](http://msdn.microsoft.com/zh-tw/library/9wff0kyh.aspx)
- 
- 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2Q1Ch0u6DdE7o3mJIqe9Q-tlW1K61LeLq7ZCdEAeAF3NC0R3zMj_CxYX_YlXVbOgBUmVX0U7tPO0ylNpH5PN3_JfocbUJ4-yQdOr_TB_dYYQ3UO5Fuf9CpKQVykMY9LOtKVOFiTiPWCA/s120/RelationPage.png)

- [認識網站驗證機制與ASP.NET執行時的身份識別](http://127.0.0.1:8080/VitoWeb/Documents/%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/IIS/WindowsAuthentication.aspx?DID=D0118)