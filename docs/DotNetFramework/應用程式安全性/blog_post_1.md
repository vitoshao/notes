---
title: 方法的安全性
layout: default
parent: 應用程式安全性
nav_order: 1
description: "方法的安全性"
date: 2012-03-15
tags: [DotNetFramework,應用程式安全性]
postid: "283021326845532535"
---
前一章節所描述的內容，是針對整個的組件(assembly)，進行 CAS 設定以保護組件的安全。  除此之外，也可以針對組件的方法 (method) 做各別的 CAS 設定以保護方法 的安全。  建立方法安全性時，有二種方式：宣告式 (Declaratively) 和命令式 (Imperatively) 。  使用宣告式時，編譯器會在程式碼執行前就做安全性檢查；  使用命令式時，則在程式碼執行中才做安全性檢查，並控制檢查失敗的處理作業。  

# 方法使用權限請求的種類

設定方法的使用權限請求，也是透過 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 列舉型別的設定，就可以請求特定的使用權限。  前一章節有介紹到，在 CAS 組件宣告時， [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 共有三種設定值，但是在 CAS 方法宣告中，則有六種不同的權限請求設定值：  

- [Demand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ( 要求 ) ：堆疊中的所有呼叫端都要有權限。
- [PermitOnly](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ：指明本方法中，僅可以執行的權限，用來降低權限。
- [Assert](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ( 判斷提示 ) ：告訴 runtime ，只要此呼叫端有權限即可，可以忽略再往上層的檢查。
- [Deny](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ( 拒絕 ) ：拒絕某個權限，用來降低權限。
- [LinkDemand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ( 連結要求 ) ：只要立即呼叫端(直接呼叫的那個呼叫端)有權限即可。
- [InheritanceDemand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ( 繼承要求 ) ：。

#### &lt;font color="red"&gt;注意１：&lt;/font&gt;

其中的 LinkDmand 和 [InheritanceDemand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 二項，只能用在 Declaratively ，不能用在 Imperatively 。

#### &lt;font color="red"&gt;注意２：&lt;/font&gt;

當某個方法的屬性值被設成 [Demand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 或 [LinkDemand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) ，CAS 並不是檢查目前這個方法的使用權限，而是檢查呼叫的那個方法的使用權限。  Demand 要求所有呼叫端都要檢查；LinkDemand 要求只要檢查直接呼叫端即可。  

#### 權限種類歸納：

1. **Demand** and        **LinkDemand** do not check the current method's permissions — they check the caller.
2. **Demand** causes the permission check to verify the access of all callers, whereas              **LinkDemand** verifies only the immediate caller.
3. **Deny** performs a similar function to **RequestRefuse**, whereas              **PermitOnly** is similar to **RequestOptional**.
4. **Assert** enable a method to vouch for all callers.
5. use the **SecurityAction** enumerations for **declarative security** and the       **CodeAccessPermission** methods for **imperative security**。

To understand each of these methods, consider a group of four guests who want to  enter an exclusive party. The host (your method) has hired a bouncer (the .NET  Framework runtime) to make sure that only guests (calling assemblies) with an invitation  (a CAS permission) are allowed to enter the party (call your method).    

If the host calls **InvitedGuests**.**LinkDemand**, the bouncer       **will check the invitation of the  first guest and then allow everyone else into the party**. This is quick, but it might let  people sneak into the party. If the host calls **InvitedGuests**.**Demand**, the bouncer       **will  check the invitation of every guest individually**. This process takes more time, but it  ensures that nobody can sneak in.    

To speed up the process of checking invitations, **the first invited guests might use**      **InvitedGuests**.**Assert** to       **assure the bouncer that all the guests in the group were  invited**—assuming that the bouncer **trusted the first guest enough**. This procedure  would also allow the first guest to bring guests who lacked invitations, which might  be a good thing if the host wanted to have a lot of people at the party but didn't want  to hand out too many invitations (that might fall into the wrong hands). However, it  might be a bad thing if a thief discovered that he could sneak into the party.    

If the host wanted to ensure that people danced at the party (and never did anything  else), the host would use **Dancing**.**PermitOnly** to       **instruct the bouncer to make sure  that guests stayed on the dance floor**. If the host wanted people to do anything but  dance, the host would use **Dancing**.**Deny** to       **prevent anyone from dancing**.

# 以宣告方式要求CAS使用權限 ( Demand CAS Permissions Declaratively )

以宣告方式建立方法(method)CAS，和建立組件CAS的方法類似。  

下面範例，我們想進行以下操作：ClassA.createFolder() -&gt; ClassB.createFolder() -&gt; ClassC.createFolder()  
透過指定不同的 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 列舉值，以瞭解不同權限請求設定值的特性與用法。  

## Demand：檢查所有呼叫端是否都有必要的使用權限

這個要求使用 [Demand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 設定值，所以必須每個 caller 都要有寫入 c:\temp 目錄的權限才會通過檢查。但是因為 ClassA.createFolder() 僅有讀取的權限，所以無法通過檢查。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggKi2OZ-9VZd8mh1cft8iBAP7SxcNLXiIsYWUK8M9ew4NZS_4HTjcY-nhc9TbtLeKZeFSHmcnHQe649Nf1LxkOsRZwPSGbaXqF4X7OiZQtNNqcH4xYZulEr0TxH-Cqu6hUpOdPVsAVlKw/s850/SecurityAction_Demand.png)

## LinkDemand：只檢查直接的呼叫端

這個要求使用 [LinkDemand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 設定值，所以只要 immediate caller 有寫入 c:\temp 目錄的權限，在其上層的 caller 就不用理會，所以可以通過檢查。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjGamDOBhcAlbIg3YEcjghYBR-U8mXsmob4TYrqQ_eiZMrjZLq1KiRx_A-GcPpYZTsGUHnWhaxLBTaJc1z6h1w4Vpv9ZaQUAIEScjX9VE0jrkyBeMOTCyvNuthgZ8FWasXn8fB1c1-T-Xg/s596/SecurityAction_LinkDemand.png)

## Assert：阻止要求檢查，允許權限不足的程式碼呼叫較高CAS使用權的方法。

Assert可以用來放寛使用權限以促進效能，如下面範列：在 ClassC.createFolder() 方法上，雖然這個要求使用 [Demand](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 設定值，但是，其中一個 caller 搬出了 [Assert](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 做擔保，在其上層的 caller 就不用理會，所以可以通過檢查。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjV49-8PBAbM5SO1Od_lkjei3620Sxo-P_6XlCjft2UhqLZ7ecVKU5XujeHEuyUKbLgZvtTR81X8-Cpx7Qdi7ZHrpl6cjMBi58zhvjjmuJNzDxXkT6y8UP471-o7BRcoEiP3X8eeqs4skI/s746/SecurityAction_Assert.png)

要使用 [Assert](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 選項，必須該組件具有「Assert Any Permission That Has Been Granted」權限，也就是 SecurityPermissionFlag.Assertion 權限。  雖然 [Assert](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction) 可以降低使用權限的檢查而增進效能，但是卻也因此讓缺少CAS使用權限的某些組件得以使用這些方法，為了避免被濫用及駭客的攻擊，應在try/finally區段呼叫 RevertAssert ，將CAS使用權限檢查回復到原始狀態。  
```c#
public class ClassY
{
public void createFolder()
{
FileIOPermission filePerm = new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp");
filePerm.Assert();
try
{
ClassZ class_z = new ClassZ();
class_z.createFolder();
}
finally
{
CodeAccessPermission.RevertAssert();
}
}
}
```

# 以命令方式要求CAS使用權限 ( Demand CAS Permissions Imperatively )

在上面，我們使用了宣告方式，藉由設定 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 的列舉值以指定 CAS 。  另外，我們也可以透過 [CodeAccessPermission](http://msdn.microsoft.com/zh-tw/library/system.security.codeaccesspermission.aspx) 類別，使用同 [SecurityAction](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.securityaction.aspx) 列舉值的方法名稱，以達到命令式的安全性。  

## 命令式Demand：檢查所有呼叫端是否有必要的使用權限

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0vu9r2XXanuK8H-al17lKK0uHD2VeOCoz3d84lV65Aux7c1uRYICpjk-aipqpRB7zD_YIet_df1JPX1LLHTwiJ7ibQd7p245bsMmW9ANz2FfmdE-AyQglPWiK8zFTpJ5qorqSaLvP2ik/s850/SecurityAction_Demand_Imperatively.png)

## 命令式Assert：阻止要求檢查，允許權限不足的程式碼呼叫較高CAS使用權的方法。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzFSoOcmgLkw2j291w4EAySSV6WeLXip_OYmeeMSESRu18yASLErL8hUBwDY1mIvOdaZDraYbd6fMwdFMUCjqqX6jd6O2-8crmYD2dHXqOgvcmlCQB0XrywUIrjRqaSct0jnYLGuJRuhA/s850/SecurityAction_Assert_Imperatively.png)

## SecurityManager.IsGranted：分析是否已授與使用權限。
```c#
FileIOPermission filePermissions = new FileIOPermission(FileIOPermissionAccess.Read, @"C:\Windows\");
if ( SecurityManager.IsGranted(filePermissions) == true )
Console.Write(@"Assembly cannot read the C:\\Windows directory");
else
Console.Write(@"Assembly cannot read the C:\\Windows directory");
```

## CodeAccessPermission.RevertPermitOnly：移除已授與的使用權限。
```c#
private void method4()
{
FileIOPermission ioPermission = new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp");
ioPermission.PermitOnly();
method5();
}

private void method5()
{
//授與Write使用權限
FileIOPermission ioPermission = new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp");
ioPermission.PermitOnly();

//移除已授與的使用權限
CodeAccessPermission.RevertPermitOnly();

//重新授與Read使用權限
ioPermission = new FileIOPermission(FileIOPermissionAccess.Read, @"C:\temp");
ioPermission.PermitOnly();
method6();
}

private void method6()
{
FileIOPermission ioPermission = new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp");
ioPermission.Demand();  
}
```

# How to Call Trusted Code from Partially Trusted Code

為了安全起見，部分信任的程式碼，預設是無法呼叫強式名稱組件。  但是經由加入 AllowPartiallyTrustedCallersAttribute 的組件層級自訂屬性，部分信任就可以呼叫強式名稱組件。  

[assembly:AllowPartiallyTrustedCallers]  

若組件沒有強式名稱，即使沒有這個屬性，部分信任程式碼也可以呼叫組件的公用方法。

# How to Use Permission Sets

當一個方法的請求中，得包含數個權限時，這時就必須用到權限集(Permission Set)。  使用 [System.Security.PermissionSet](http://msdn.microsoft.com/zh-tw/library/system.security.permissionset.aspx) 類別建立 Permission Set，並用 [AddPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissionset.addpermission.aspx) 方法指定要加入權限集的使用權限。  完成之後，便可以呼叫包括 Assert、Demand、Deny、PermitOnly 的標準使用權限方法。  

下面範例，在權限集中加入了三項權限，當要求對這些資源存取時，若缺少任何一個所列的使用權限，將引發執行階段的例外狀況。  
```c#
PermissionSet myPermSet = new PermissionSet(PermissionState.None);
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Read, @"C:\Windows"));
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Write, @"C:\Inetpub"));
myPermSet.AddPermission(new RegistryPermission(RegistryPermissionAccess.Write, @"HKEY_LOCAL_MACHINE\Software"));
myPermSet.Demand();
```
```c#
private void method7()
{
PermissionSet myPermSet = new PermissionSet(PermissionState.None);
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Read, @"C:\temp"));
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp"));
myPermSet.PermitOnly();
method5();
}

private void method8()
{
PermissionSet myPermSet = new PermissionSet(PermissionState.None);
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Read, @"C:\temp"));
myPermSet.AddPermission(new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp"));
myPermSet.PermitOnly();
method6();
}

private void method9()
{
FileIOPermission ioPermission = new FileIOPermission(FileIOPermissionAccess.Write, @"C:\temp");
ioPermission.Demand();
}
```

# 範例

## 案例一：如何限定某個組件，只能存取固定的IP+port

透過 [System.Net.SocketPermission](http://msdn.microsoft.com/zh-tw/library/system.net.socketpermission.aspx) 類別，限制這個函式只能存取 192.168.42.158 8040 port
```c#
[SocketPermission(SecurityAction.PermitOnly, Access = "Connect", Host = "192.168.42.158", Port = "8040", Transport = "All")]
private void button9_Click(object sender, EventArgs e)
{
try
{
string ip = txtIP.Text;
int port = int.Parse(txtPort.Text);
Socket socket = new Socket( AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
socket.Connect(new IPEndPoint(IPAddress.Parse(ip), port));
}
catch (Exception ex)
{
MessageBox.Show(ex.Message);
}
}
```

## 案例二：如何限定某個組件，只能存取固定的網址

透過 [System.Net.WebPermission](http://msdn.microsoft.com/zh-tw/library/system.net.webpermission.aspx) 類別，限制這個函式只能存取 http://tw.stock.yahoo.com/ 
```c#
[System.Net.WebPermission(SecurityAction.PermitOnly, ConnectPattern = @"http://tw.stock.yahoo.com/")]
private void button11_Click(object sender, EventArgs e)
{
try
{
string url = txtURL.Text;
System.Net.HttpWebRequest req = (System.Net.HttpWebRequest)System.Net.HttpWebRequest.Create(url);
}
catch (Exception ex)
{
MessageBox.Show(ex.Message);
}
}
// ConnectPattern 是一個 regular expression 
// 所以，也可以這麼設定：http://[.\*].yahoo.com/[.\*]
// 
```