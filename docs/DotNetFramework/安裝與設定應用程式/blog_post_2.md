---
title: .NET2.0組態工具
layout: default
parent: 安裝與設定應用程式
nav_order: 2
description: ".NET2.0組態工具"
date: 2012-02-08
tags: [DotNetFramework,安裝與設定應用程式]
postid: "8853573766569236236"
---
# 前言

若有一個應用程式，參考了一個類別庫(版號1.0.0.1)，當這個類別庫升級至(1.0.0.2)時，怎麼辨？  過去在win32時代，我們使用regsvr32，將dll註冊到system32目錄底下達到共享的目的，  但是，那時候的機制是不支援不同版本同時運行的，也就是，當新版本被註冊後，舊版本就自動被取代了，  而參考這個類別庫的應用程式，若對這個新的版本有不支援的狀況時，就會導至錯誤發生。  

.Net出現之後，這個情況被改善了，[全域組件快取](http://msdn.microsoft.com/zh-tw/library/yf1d93sz.aspx) ( Global Assembly Cache, GAC) 這個機制就是用來管理這些共用的組件。  它可以把同一組件的不同版本緩存在GAC中，所有引用它的程序，不管dll版本如何，都能不受影響的使用。  

為了要區別各個版本，所以，必須具有強式名稱(Strong Name)的組件，才可以註冊到GAC，而強式名稱就是每個組件的識別。  
參考：[如何建立強式名稱](http://vito-learning.blogspot.tw/2012/03/blog-post_07.html)

# 如何變更參考組件的版本

若原先應用程式讀取 1.0.0.6 的版本，若 GAC 之中，同時存在 1.0.0.7 的版本，底下示範如何將參考組件變更成 1.0.0.7 的版本。  

要變更組態的設定，必須使用「Microsoft .NET Framework 2.0 組態」管理工具，這個工具位於：控制台\系統及安全性\系統管理工具  

- 1.加入應用程式  
![](http://127.0.0.1:8080/_images/cs/change_ver_3.png)  

![](http://127.0.0.1:8080/_images/cs/change_ver_4.png)
- 2.設定組件  
![](http://127.0.0.1:8080/_images/cs/change_ver_5.png)  

![](http://127.0.0.1:8080/_images/cs/change_ver_1.png)
- 3.變更繫結原則  
![](http://127.0.0.1:8080/_images/cs/change_ver_2.png)

這時候系統會在應用程式組態檔加入以下資訊
```xml
<runtime>
<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
<dependentAssembly>
<assemblyIdentity name="ClassLibrary1" publicKeyToken="14fca5731fbee0da" />
<publisherPolicy apply="yes" />
<bindingRedirect oldVersion="1.0.0.6" newVersion="1.0.0.7" />
</dependentAssembly>
</assemblyBinding>
</runtime>
```

# 如何變更組件的位置

變更程式碼基底

![](http://127.0.0.1:8080/_images/cs/change_ver_6.png)

這時候系統會在應用程式組態檔加入以下資訊
```xml
<runtime>
<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
<dependentAssembly>
<assemblyIdentity name="ClassLibrary1" publicKeyToken="14fca5731fbee0da" />
<publisherPolicy apply="yes" />
<codeBase version="1.0.0.7" href="file:///C:/ClassLibrary1.dll" />
</dependentAssembly>
</assemblyBinding>
</runtime>
```
**注意：必須清除組件快取中的同一版本的註冊，否則該版本的優先權還是會大於這個版本。**    ```xml
<dependentAssembly>
<assemblyIdentity name="ClassLibrary1" publicKeyToken="14fca5731fbee0da" />
<publisherPolicy apply="yes" />
<bindingRedirect oldVersion="1.0.0.7" newVersion="1.0.0.8" />
<codeBase version="1.0.0.8" href="file:///C:/ClassLibrary1.dll" />
</dependentAssembly>
```

#### .NET Framework 2.0 Configuration Tool 的幾項功能

- 變更組件設定
- 變更安全設定
- 建立使用權限集合(Permission Sets)
- 建立程式碼群組(Code Group)
- 增加組件的信任層級(increasing an assembly's trust level)

#### 如何得知目前參考組件的版本

![](http://127.0.0.1:8080/_images/cs/assembly_load.jpg)