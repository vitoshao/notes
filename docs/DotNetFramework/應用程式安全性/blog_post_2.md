---
title: 組件的安全性
layout: default
parent: 應用程式安全性
nav_order: 2
description: "組件的安全性"
date: 2012-03-15
tags: [DotNetFramework,應用程式安全性]
postid: "5324831370161011035"
---
CAS 是一種用來限制資源被存取的機制，包括檔案、目錄、印表機、網路存取...。  .NET Framework 對每個可以被保護資源各提供一類別，定義在 [System.Security.Permissions](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.aspx) 命名空間裡。  每個類別都有各自的特定成員，藉以控制相關資源的使用權限。例如：  

- [GacIdentityPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.gacidentitypermission.aspx) ：defines the identity permission for files originating in the global assembly cache.
- [PublisherIdentityPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.publisheridentitypermission.aspx) ：represents the identity of a software publisher.
- [DataProtectionPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.dataprotectionpermission.aspx) ：controls the ability to access encrypted data and memory.
- [StrongNameIdentityPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.strongnameidentitypermission.aspx) ：defines the identity permission for strong names
- [FileIOPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.fileiopermission.aspx) ：
- [IsolatedStoragePermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.isolatedstoragepermission.aspx) ：
- [ReflectionPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.reflectionpermission.aspx) ：
- [RegistryPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.registrypermission.aspx) ：
- [UIPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.uipermission.aspx) ：
- [WebBrowserPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.webbrowserpermission.aspx) ：
- [ZoneIdentityPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.zoneidentitypermission.aspx) ：
- ...

雖然各個權限類別有其特殊的屬性設定值，不過這些類別都繼承自 [CodeAccessSecurity](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.codeaccesssecurityattribute.aspx) 類別，所以只須熟悉二種標準的屬性即可：  

- **Action**：指定執行哪個安全性動作，可使用 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 列舉值來設定此屬性。
- **Unrestricted**：若要表示 full permission，則值設成 true ，此時會完全忽略 CAS 的設定。反之則 false 。

#### **Action** 屬性，共有下列三種列舉成員：

1. [RequestMinimum (M)](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ：授與指定的使用權限。
2. [RequestOptional (O)](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ：僅授與指定的使用權限。這個要求隱含地拒絕所有其他未授與的使用權限。
3. [RequestRefuse (R)](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ：拒絕使用權限。

&lt;!-- more --&gt;  
這三種設定值若重複指定，其結果判斷規則如下：  
若某一組件，其原先的 Security Policy (也就是Enterprise, Machine, User三者的交集) 為 P，  經過 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 設定後，最終的權限為：  
（Ｐ 交（（Ｍ 聯 Ｏ) － Ｒ））  

# 如何建立組件宣告

底下程式碼示範，組件必須具備 C:\boot.ini 的讀取權限，才可以執行。
```c#
[assembly:FileIOPermissionAttribute(SecurityAction.RequestMinimum, Read=@"C:\boot.ini")]
namespace DeclarativeExample
{
class Program
{
static void Main(string[] args)
{
Console.WriteLine("Hello, World!");
}
}
}
```

## 範例１

以下範列假設使用管理員帳號與 Everything 的使用權限，說明各種狀況下的權限變化。  
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestMinimum, Read = @"C:\Log.txt")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\Log.txt | yes | yes | yes |
| C:\Log2.txt | yes | yes | yes |

## 範例２
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Read = @"C:\Log.txt")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\Log.txt | yes | no | no |
| C:\Log2.txt | no | no | no |

## 範例３
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Read = @"C:\Log.txt", Write = @"C:\Log.txt")]
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Append = @"C:\Log.txt")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\Log.txt | yes | yes | yes |
| C:\Log2.txt | no | no | no |

## 範例4
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Read = @"C:\temp")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\temp\Log.txt | yes | no | no |
| C:\temp\Log2.txt | yes | no | no |
| C:\temp2\Log.txt | no | no | no |
| C:\Log.txt | no | no | no |

## 範例５
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestMinimum, Read = @"C:\temp")]
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Append = @"C:\temp2")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\temp\Log.txt | yes | no | no |
| C:\temp2\log.txt | no | no | yes |
| C:\Log.txt | no | no | no |

## 範例６
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestMinimum, Read = @"C:\temp")]
[assembly: FileIOPermissionAttribute(SecurityAction.RequestRefuse, Read = @"C:\temp")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\temp\Log.txt | no | yes | yes |

## 範例７
```c#
[assembly: FileIOPermissionAttribute(SecurityAction.RequestOptional, Write = @"C:\temp")]
[assembly: FileIOPermissionAttribute(SecurityAction.RequestRefuse, Read = @"C:\temp")]
```

| 對象 | Read | Write | Append |
| --- | --- | --- | --- |
| C:\temp\Log.txt | no | yes | no |

# 如何在沙箱作業中，限定組件的權限

#### 例一：

下面這個例子，我們要在主應用程式中，使用沙箱作業，載入 C:\plugins\SandTest.exe 這個組件。  
我們使用 PermissionSet ，限定 SandTest 這個組件只能讀取 C:\temp\test1 目錄中的檔案，其他目錄則不允許存取。  
```c#
string pluginFolder = @"C:\plugins";
string plugInPath = @"C:\plugins\SandTest.exe");

PermissionSet pSet = new PermissionSet(PermissionState.None);
pSet.AddPermission(new SecurityPermission(SecurityPermissionFlag.Execution));
pSet.AddPermission(new UIPermission(PermissionState.Unrestricted));
pSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.PathDiscovery | FileIOPermissionAccess.Read, pluginFolder));
pSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Read, @"C:\temp\test1"));

AppDomainSetup setup = AppDomain.CurrentDomain.SetupInformation;
AppDomain sandbox = AppDomain.CreateDomain("sbox", null, setup, pSet);
sandbox.ExecuteAssembly(plugInPath);
AppDomain.Unload(sandbox);
```

#### 例二：

下面這個例子，我們透過 Evidence 來限定組件的執行權限。  

首先我們建立一個權限集合，如下圖所示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzemKYNxdEfxd4zCqcFBNJV1AYJ7wr4rCQdmW89co9-K8vZZXtjiZN-UvPAIrg5tWe8m4U17yis2YsYDkGXA99LhhVcwKgcctA1D5HhUmgKSlIMd8AwyKAluED_rawW6ckvuYlB3S4U-o/s850/assembly-security-permissionset.png)

再建立一個程式碼群組，如下圖所示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiO2fchogrxCh2SMcTzsPW1b6jmGK94u0XPApSMHx10FmJuWQpcAUgkreOwFioNzudiTrLT7WlRiFSj3ulzW8APW3m3lwzG1hyI_cH-nCwoUpQM6-0Mwxa0mscozNT0qygawTAwKY2iZZI/s850/assembly-security-codegroup.png)

程式碼部分如下，
```c#
private void button27_Click(object sender, EventArgs e)
{
string pluginFolder = @"D:\myBlog\TestProj\TestForAppDomain\TestForAppDomain\bin\Debug";
string plugInPath = Path.Combine(pluginFolder, "TestForAppDomain.exe");

Assembly asm = Assembly.LoadFile(plugInPath);

AppDomainSetup setup = new AppDomainSetup();
setup.ApplicationBase = pluginFolder;

//設定 sandbox 執行的權限
Evidence hostEvidence = new Evidence();
hostEvidence.AddHost(new Site("www.vito.com"));

PermissionSet pset = SecurityManager.ResolvePolicy(hostEvidence);
pset.AddPermission(new FileIOPermission(FileIOPermissionAccess.PathDiscovery | FileIOPermissionAccess.Read, pluginFolder));
pset.AddPermission(new SecurityPermission(SecurityPermissionFlag.Execution));
pset.AddPermission(new UIPermission(PermissionState.Unrestricted));

//建立 sandbox
AppDomain sandbox = AppDomain.CreateDomain("sbox", hostEvidence, setup, pset);  //指定 host 的 Evidence 和 權限集合

//建立 assebmlyEvidence
Evidence asmEvidence = new Evidence();
asmEvidence.AddAssembly(new Site("www.vito.com"));      //指定組件的 Evidence

//在 sandbox 執行組件
sandbox.ExecuteAssembly(plugInPath);

AppDomain.Unload(sandbox);
}
```