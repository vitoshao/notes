---
title: COM 中使用.NET 元件
layout: default
parent: Interoperation with COM
nav_order: 2
description: "COM 中使用.NET 元件"
date: 2012-05-18
tags: [DotNetFramework,Interoperation with COM]
postid: "9162631696474635578"
---
上一篇介紹如何在 .NET 中使用 COM ，這一篇將討論如何公開 .NET 元件給 COM 使用。      

# 建立可供 COM 使用的 .NET 元件

當 COM 要使用 .NET 元件時，其實中間是透過一個稱為 CCW ( COM Callable Wrapper ) 的代理程式來封送處理的。 不過若要設計              .NET 類別庫給 COM 使用，有幾點必須要注意：          

#### 1.在專案屬性的建置頁籤中，必須勾選設定「 Register for COM interop 」項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgippYk5NUCNmxyK4t_IQvybhqUlGDpx1vKI82jiVWQ1O1zRnR729YQa2sxpmoigsq58NbuTnZupla89iicZA16VTv5DJ1JDnKU6uw1AnOJQmGgd2uRrB8Hn0kjEtnjtYdtcgOyPtcamF0/s793/Register-for-COM-interop.png)

#### 2.必須要引入 [System.Runtime.InteropServices](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices) 命名空間。

#### 3.讓 .NET 類別在 COM 可見。

- 若希望組件的預設值就是 COM 可見，可以針對組件屬性設定。  ```c#
[assembly: ComVisible(true)]
```
- 若沒有做上一個步驟，也可以在想要公開的類別或成員上，加 [ComVisible(true)] 屬性。針對不想公開的成員，則可以設成 false 。  ```c#
using System.Runtime.InteropServices;

namespace ClassLibrary_For_COM
{
[ComVisible(true)]
class ComVisiblePerson
{
[ComVisible(false)]
public string FirstName { set; get; }
[ComVisible(false)]
public string LastName { set; get; }

[ComVisible(true)]
public string FullName() 
{
return string.Concat(FirstName, " ", LastName);
}
}
}
```
&lt;!-- more --&gt;        
# 部署可供 COM 使用的 .NET 元件

雖然建立了 COM 可見的組件，但是 MSDN 列出了幾點方針以確保建立的組件可以正確部署：          

- All classes must use a **default constructor** with no parameters.
- Any **type** that is to be exposed must be **public**.
- Any **member** that is to be exposed must be **public**.
- Abstract classes will not be able to be consumed.

組件建立之後，可以使用命令工具 [TlbExp.exe](http://msdn.microsoft.com/zh-tw/library/hfzzah2c) 匯出型別程式庫，以便取得 .tlb 檔。例如：
```c#
TlbExp ClassLibrary_For_COM.dll /out:ClassLibrary_For_COM.tlb
```

在 COM 中，元件必須註冊後才可以使用，以下２種方法都可以用來註冊.NET 元件。

#### 使用 visual studio 註冊組件

在Visual Studio開發環境中，若專案屬性的建置頁籤中，有勾選「 Register for COM interop 」項目，則建置(build)後，該組件就會自動註冊在該電腦之中。

#### 手動方式註冊組件

使用命令工具 Regasm.exe ，可以手動將 .NET 元件註冊到電腦中。但是必須以系統管理員身份才可執行。例如：
```c#
regasm myAssembly.dll
```
## 參考資料  

- [.NET 2.0 筆記 - 建立 COM+ 元件](http://www.dotblogs.com.tw/huanlin/archive/2008/04/23/3196.aspx)
- 
-