---
title: .NET 中使用 COM 物件
layout: default
parent: Interoperation with COM
nav_order: 3
description: ".NET 中使用 COM 物件"
date: 2012-05-18
tags: [DotNetFramework,Interoperation with COM]
postid: "1604031695611036716"
---
# Interoperation

#### Managed vs Unmanaged

- Managed 程式碼：在執行階段(CLR)控制之下執行的程式碼，如 C#/VB.NET 等語言所開發的程式碼。
- Unmanaged 程式碼：如 VB6/C/C++ 等語言所開發的程式碼。或者 COM 元件、ActiveX 介面及 Win32 API 函式，都是 Unmanaged 程式碼。

**Interoperation** 泛指由 Managed 程式碼與 Unmanaged 程式碼的互動。  

#### COM vs COM+

雖然 .NET Framework 已經包裝了大部份的 Windows APIs，但是還是有部份重要的 APIs 只能透過 COM 物件模型存取。 所以，倒霉的工程師還是得學習如何由 .NET 中存取 COM 物件。  

- COM：遵守某些規則(interface,GUID,iUNKNOW,query....)做出的元件，具有跨平台，可攜性...等優點。
- COM+：又叫 DCOM 它是 COM 的加強版，主要可遠端執行。

#### COM Object vs Type Library

- COM ：一種 unmanaged object-oriented programming model, 包含 classes, methods, and types. 其中的型別資料就定義在 *Type Library* 之中。
- Type Library：型別函式庫，內容包含型別資訊，物件的描述和其他參考的資料，可以獨立在 .tlb 檔案中，也可以被嵌在 .dll, .exe, .ocx 裡頭。

#### COM 元件的製作：

- (1) 使用由 VB6/C/C++ 開發的 unmanaged 元件，又稱 ActiveX DLL。使用 regsvr32.exe 註冊。
- (2) 使用 .NET 開發的類別庫專案，又稱 Managed DLL。使用 regasm.exe 註冊。

#### 如何公開 .NET 類別庫給 COM

使用 C#/VB.NET 來開發類別庫專案，這是屬於 managed 程式碼，通常是提供 .NET 的其他專案參考使用。   若是要提供給 VB6/ASP 等系統呼叫使用，則該專案的「 Register for COM interop 」屬性必須勾選，這個屬性可在 Build 頁籤中找到。  同時類別在定義的時候，也必須加上 [ComVisible(true)] 的屬性，或者勾選組件資訊中的「 COM-Visible 」項目。  另外，其註冊方法也不同於 ActiveX DLL。必須先部署到 GAC 之中，再使用 regasm.exe 註冊。  
詳細事項，可參考以下文章內容：  

- [如何開發 COM 元件給 ASP 使用](http://www.dotblogs.com.tw/boei/archive/2010/07/05/16384.aspx)
- [用 .NET 寫 COM 元件的注意事項](http://blog.miniasp.com/post/2008/07/15/How-to-write-COM-component-using-NET.aspx)
- [使用 Visual C++ 製作 COM 元件與使用教學](http://mir.cs.nthu.edu.tw/users/hanyin/code/myWork/otherApplication/COM/COM_method/COM_method.html)

# 如何在 .NET 專案中引用 COM 元件

#### 將 COM 元件的型別轉成 .NET 型別

#### 1.登錄

首先要知道的是，COM 元件和 .NET 元件在使用上有所不同，COM 元件必須先登錄（registered）後才可使用。  
透過命令工具 Regsvr32.exe 可以註冊 COM 元件。例如：  
```c#
regsvr32 person.dll
```

#### 2.型別轉換

COM 元件中的型別必須轉換成 CLR 認得的型別，這樣子才可以在 .NET 專案中引用 COM 元件。  
透過命令工具 [TlbImp.exe (型別程式庫匯入工具)](http://msdn.microsoft.com/zh-tw/library/tt0cf3sx.aspx)，就可以將 COM 型別程式庫中的型別定義轉換為 CLR 組件中的等效定義。  例如：  
```c#
tlbimp ComLib.dll /out: NetLib.dll
```

#### 3.加入參考

將轉換後的 .NET 組件加入參考後，它會出現在［.NET 頁籤］中的清單。

#### 直接在 .NET 專案中加入 COM 元件參考

要存取 COM 元件，另一個簡單的方法就是直接在 Visual Studio 裡頭，直接加入該 COM 元件的參考。   因為加入參考時， Visual Studio 就會自動將 COM 元件中的型別定義轉換為 CLR 組件中的等效定義，同時登錄 COM 元件。  

將 COM 元件加入參考後，它會出現在［COM 頁籤］中的清單。

#### .NET 還提供一些工具，供 COM Interop 使用：

- TlbImp.exe：Imports a new .NET assembly based on the COM component。
- TlbExp.exe：Creates a COM type library that can be consumed by a .NET application。
- Regasm.exe：Enables you to add .NET assemblies to and remove .NET assemblies from the system registration database.。
- Regedit.exe：Allows you to search for and manage existing registry entries.
- Ildasm.exe：To view a visual representation of the Intermediate Language (IL).。

# 如何在 .NET 程式碼中使用 COM 元件

在匯入 COM 元件的型別程式庫之後，使用程式庫中的物件，就跟直接使用 .NET 建立的物件一樣。  例如 MS Speech Object Library 是一套可用來將文字轉語音的 COM 元件，當我們匯入這個 COM 元件之後，就可以輕輕鬆鬆利用這個元件來叫電腦說話。如下程式碼範例：  
```c#
using SpeechLib;

private void button3_Click(object sender, EventArgs e)
{
SpVoice voice = new SpVoice();
voice.Voice = voice.GetVoices().Item(1);
voice.Speak("台北站到了.", SpeechVoiceSpeakFlags.SVSFDefault);
}
```

#  COM Interop 的錯誤處理

雖然 [System.Exception](http://msdn.microsoft.com/zh-tw/library/system.exception) 是所有 [Exception](http://msdn.microsoft.com/zh-tw/library/system.exception.aspx) 物件的最上層類別，但是在 .NET 2.0 之前，它是無法攔截 COM 元件所引發的錯誤的。  因為 [System.Exception](http://msdn.microsoft.com/zh-tw/library/system.exception) 只能補捉 CLS 相容的錯誤 ( CLS - compliant exception , Common Language Specification )。 而 COM 元件所引發的錯誤是 non-CLS-compliant exceptions 的。  

有些語言 (例如 C++) 可讓您擲回任何型別的例外狀況。其他語言 (例如 Microsoft C# 和 Visual Basic) 則要求每個擲回的例外狀況都必須衍生自 [Exception](http://msdn.microsoft.com/zh-tw/library/system.exception.aspx) 類別。  為了維護語言之間的相容性，Common Language Runtime (CLR) 會將不是衍生自 [Exception](http://msdn.microsoft.com/zh-tw/library/system.exception.aspx) 的物件包裝在 [RuntimeWrappedException](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices.runtimewrappedexception.aspx) 物件中。  

但是從 .NET 2.0 開始， [System.Runtime.CompilerServices](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices) 命名空間增加了 [RuntimeWrappedException](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices.runtimewrappedexception) 類別，這個類別繼承自 [System.Exception](http://msdn.microsoft.com/zh-tw/library/system.exception) ，可用來包裝不是衍生自 [System.Exception](http://msdn.microsoft.com/zh-tw/library/system.exception) 類別的例外狀況。  [RuntimeWrappedException](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices.runtimewrappedexception) 類別中的 [WrappedException](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices.runtimewrappedexception.wrappedexception) 屬性就是用來包裝產生例外的物件。  

RuntimeCompatibilityAttribute 可用來指定是否使用 [RuntimeWrappedException](http://msdn.microsoft.com/zh-tw/library/system.runtime.compilerservices.runtimewrappedexception.aspx) 物件來包裝不是衍生自 [Exception](http://msdn.microsoft.com/zh-tw/library/system.exception.aspx) 類別的例外狀況。

# COM Interop 的限制

- **Static/shared members**：COM 物件**不支援** static/shared 成員。
- **Parameterized constructors**：COM 物件**不允許**傳參數給 constructor。
- **Inheritance**：**無法辨識**基底類別中的遮蔽成員 ( shadow members )，繼承能力上有所限制。
- **Portability**：COM 物件**必須被註冊後才可使用**，非 Windows 的其他系統沒有登入資料庫 (registry)，所以無法使用在其他平台。
## 參考資料  

- [COM Interop 第 1 部份：C# 用戶端教學課程](http://msdn.microsoft.com/zh-tw/library/aa288455%28v=vs.71%29.aspx)
- [型別程式庫匯入工具(TlbImp.exe)](http://msdn.microsoft.com/zh-tw/library/tt0cf3sx%28v=vs.80%29.aspx)