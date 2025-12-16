---
title: 認識反射
layout: default
parent: 反射(Reflection)
nav_order: 5
description: "認識反射"
date: 2012-03-21
tags: [DotNetFramework,反射(Reflection)]
postid: "861995750562902093"
---
[Reflection](http://msdn.microsoft.com/zh-tw/library/f7ykdhsy.aspx) ，一般稱為「反映」或「反射」，反射系統提供了瀏覽和檢測資料型別的功能。  通常，我們在使用型別資料的時候，順序大都是先取得型別參考，然後建立instance，再叫用其已知的屬性或方法。  Reflection 則可以動態載入組件，再檢測該型別中有哪些的屬性或方法，再進而呼叫使用。  

例如，一些外掛(plug-in)的組件，系統開發階段根本不知道會遇到什麼外掛組件，那要如何存取型別？  通常就是透過 [Reflection](http://msdn.microsoft.com/zh-tw/library/f7ykdhsy.aspx) 動態載入組件，再將組件中的型別繫結給已知的物件，進而叫用其方法(method)或存取其屬性(property)。  

反射 (Reflection) 的幾個重要功能：

- 1. 動態載入組件。
- 2. 反射組件中的屬性 ( Reflecting Attributes )。
- 3. 反射組件中的型別 ( Reflecting Types )。
- 4. 執行動態載入組件中的程式碼。
- 5. 在執行階段建立組件程式碼。

# Assemblies and Modules

一個組件不僅僅只有程式碼而已，它實際上包含了CLR 在執行時所需要的不同型態的資料，如：  

1. **組件的中繼資料 (Assembly metadata)：**  
組件的中繼資料又稱**組件資訊清單 (Assembly manifest)**，由一些用來描述組件的元素組成。  
例如：組件名稱、版本、強式名稱(strong name)、文化特性(culture)等資訊。  
使用 [Assembly.GetCustomAttributes](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getcustomattributes) 可以取得組件的屬性。
2. **型別的中繼資料 (Type metadata)：**  
此部份指的是與資料型別相關的資訊。  
例如：命名空間、型別名稱(類別名稱)、方法、屬性、建構子、事件、委派、介面等等。  
使用 [Assembly.GetType](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.gettype) 可以取得組件的型別。
3. **程式碼 (MSIL code)：**  
此部份包含 MSIL 程式碼，可以在 .NET Framework 的 runtime 執行。
4. **資料 (Application resource)：**  
指的是程式碼會使用到的物件，如文字、圖像或檔案。

通常這些資料都會編譯成一個檔案，但並沒有規定組件一定要編輯在單一個檔案中。  也可以將部分的型別資料、程式碼、資源編譯在其他檔案當作參考使用。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWH7fkg-g0dsF6hWsAPNHlcCyo2nLEaItj0GJdY-ABLsgASzAIVD9sjFnGjPwQA1lDqubmvPlmaYgEQqL0ZB5fw842RosmRvZ7OTM1Bk5gIwjj2da_F7kWdKRUnFSrcrFKHCaXr3mQJBI/s175/assembly_single_file.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj84qHpuzSbAiTQ_p0eXn7aMw5_ds3xzx7wuF6mJZxCq8tQ5kLtkhrmJrTm7VjWfb0yPXz0WDk36lrDZKufAnYNhx8ArXBSqasdv0QPMyDccO-lIRprIZqZtbUVkOHXM6bNOffb3M7VwAA/s349/assembly_multi_file.png)

##  Assembly 類別

下表是組件類別( [Assembly](http://msdn.microsoft.com/zh-tw/library/xbe1wdx9.aspx) )中用來建立執行個體的一些靜態方法：

| 名稱 | 說明 |
| --- | --- |
| [GetAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getassembly.aspx) | 回傳一個含有特殊型別的組件。 |
| [GetCallingAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getcallingassembly.aspx) | 回傳一個呼叫現在方法的組件。（父層組件）。 |
| [GetEntryAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getentryassembly.aspx) | 回傳一個啟動目前程序(process)的組件。（最起始的組件）。 |
| [GetExecutingAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getexecutingassembly.aspx) | 回傳一個正在執行的組件。（目前組件）。 |
| [Load](http://msdn.microsoft.com/zh-tw/library/x4cw969y.aspx) | 依據 AssemblyName 載入一個組件。常用於載入 GAC 中的組件。 |
| [LoadFile](http://msdn.microsoft.com/zh-tw/library/b61s44e8.aspx) | 載入指定路徑上組件檔案的內容。 |
| [LoadFrom](http://msdn.microsoft.com/zh-tw/library/1009fa28.aspx) | 依據特定檔案載入一個組件到目前的 AppDomain 。 |
| [ReflectionOnlyLoad](http://msdn.microsoft.com/zh-tw/library/0et80c7k.aspx) | 依據 AssemblyName 載入一個組件，只用於檢測而不能執行。常用於載入 GAC 中的組件。 |
| [ReflectionOnlyLoadFrom](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.reflectiononlyloadfrom.aspx) | 依據特定檔案載入一個組件，只用於檢測而不能執行。 |

####  [LoadFrom](http://msdn.microsoft.com/zh-tw/library/1009fa28.aspx) vs [LoadFile](http://msdn.microsoft.com/zh-tw/library/b61s44e8.aspx) 

這二個方法最主要的不同處在於載入不同檔案的相同組件時，會有不一樣的效果，請看以下說明：
```c#
// path1 and path2 point to different copies of the same assembly on disk: 

Assembly assembly1 = Assembly.LoadFrom(path1); 
Assembly assembly2 = Assembly.LoadFrom(path2); 

// These both point to the assembly from path1, so this is true 
Console.WriteLine(string.Compare(assembly1.CodeBase, assembly2.CodeBase) == 0);     //true

assembly1 = Assembly.LoadFile(path1); 
assembly2 = Assembly.LoadFile(path2); 

// These point to different assemblies now, so this is false 
Console.WriteLine(string.Compare(assembly1.CodeBase, assembly2.CodeBase) == 0);     //false
```

#### 認識 CallingAssembly、EntryAssembly、ExecutingAssembly 

下圖中，Test組件載入CL1組件，CL1組件載入CL2組件，它們三者的關係：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMF33g8fcxbM8uSigIPYH-2Bl9WUS8mctUsDsKMQYqJvoJ98HPRWzgTM51Lb0YU4vOTVjKJ-1Au0raGXx79kAZnd1W_2SK6amxtRV-Q3llKuEbQ9pKo4hLWSBeyZgyk8GqQG78rfqxfBM/s529/asm_call_entry_execute.png)

要操控組件，就要先載入組件，才可以檢測組件的屬性。例如：

- 使用 [GetExecutingAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getexecutingassembly.aspx) 方法就可以取得執行中的組件。
- 使用 [Assembly.LoadFile](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.loadfile.aspx) 可以載入磁碟中某個組件。

下面範例中，示範一小部分組件的屬性或方法，[詳情請參閱MSDN](http://msdn.microsoft.com/zh-tw/library/xbe1wdx9.aspx)。
```c#
Assembly asm = Assembly.GetExecutingAssembly();

Console.WriteLine("Full Name： {0}", asm.FullName);                  //取得組件名稱
Console.WriteLine("Path： {0}", asm.Location);                       //取得組件路徑
Console.WriteLine("Only Reflection?： {0}", asm.ReflectionOnly);     //判斷組件是否由 ReflectionOnlyLoad 載入
Console.WriteLine("From GAC ? {0}", asm.GlobalAssemblyCache);        //指出組件是否從全域組件快取載入
Console.WriteLine("CLR Version： {0}", asm.ImageRuntimeVersion);     //取得 CLR 版本
Console.WriteLine("Version： {0}.{1}.{2}.{3}", 
asm.GetName().Version.Major, asm.GetName().Version.Minor,
asm.GetName().Version.Build, asm.GetName().Version.Revision);    //取得 版本編號 

//Full Name：Practice, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
//Path ：D:\MCTS\MCTS2011\Practice\PracticeMCTS\bin\Debug\Practice.exe
//Only Reflection?： False
//From GAC ? False
//CLR Version： v4.0.30319
//Version： 1.0.0.9

Assembly asm1 = Assembly.Load("Practice, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null");
Assembly asm2 = Assembly.LoadFile(@"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll");
Assembly asm3 = Assembly.LoadFrom("http://.../..");
```

下圖顯示一個組件包含的屬性  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnXkfxK0xF-pf020kN_SjPqHgADQJ2PvPtRHr8uNiVbiPn43Xx2N1SOC8FjOk-iCV2GIS1Ugpl9EqTxFNEj53qW4DUOj_wllcVRLMl6M5Tx5hK_PmEBQ_9giqWT_1L_ovP3l2gMW-NW0Y/s850/assembly_class.png)

#### 反射不可執行的組件

有時候我們只是想透過反射機制，以取得組件的相關資訊，並沒有真正要叫用組件的方法時，我們可以利用以下方法較有效率的讀取組件資訊。

- [ReflectionOnlyLoad](http://msdn.microsoft.com/zh-tw/library/0et80c7k.aspx)
- [ReflectionOnlyLoadFrom](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.reflectiononlyloadfrom.aspx)

使用上面方法建立的組件，只可以取得組件相關資訊，無法建立 Instance
```c#
string fullName = "System.Transactions, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089";

//經由 Load 方法建立的組件，可以取得組件相關資訊，可以建立 Instance

Assembly asm1 = Assembly.Load(fullName);
Console.WriteLine("Location： {0}", asm1.Location);
object obj1 = asm1.CreateInstance("System.Transactions.TransactionScope");  

//經由 ReflectionOnlyLoad 方法建立的組件，只可以取得組件相關資訊，無法建立 Instance

Assembly asm2 = Assembly.ReflectionOnlyLoad(fullName);
Console.WriteLine("Location： {0}", asm2.Location);
object obj2 = asm2.CreateInstance("System.Transactions.TransactionScope");  //＜－－ 這一行會 throw an exception
```

##  Module 類別

模組（ [Module](http://msdn.microsoft.com/zh-tw/library/5yhwh18w.aspx) ）是諸如 type.dll 或 application.exe 等類型可移植的執行檔，包含一個或多個類別 (Class) 和介面。  單一模組中可能包含多個命名空間，而且一個命名空間也可以擴展成多個模組。  已部署為單位的一個或多個模組會組成組件。  

一個組件中可以包含一個或多個模組，模組中則包含了型別資訊。透過組件的 [GetModules](http://msdn.microsoft.com/zh-tw/library/cbfy6dbb.aspx) 方法可以取得所有屬於這個組件的模組。  
```c#
// Show ModuleInfo
private void ShowModuleInfo(Assembly asm)
{
Module[] mods = asm.GetModules();
foreach (Module mod in mods)
{
Console.WriteLine("Module Assembly: {0}", mod.Assembly);
Console.WriteLine("Module FullyQualifiedName: {0}", mod.FullyQualifiedName);
Console.WriteLine("Module Name: {0}", mod.Name);
}
}

private void btnShowModuleInfo_Click(object sender, EventArgs e)
{
// ex1:
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm1 = Assembly.ReflectionOnlyLoadFrom(dll_file);
ShowModuleInfo(asm1);
//Module Assembly: ClassLibrary1, Version=1.2.4463.28304, Culture=neutral, PublicKeyToken=14fca5731fbee0da
//Module FullyQualifiedName: D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll
//Module Name: ClassLibrary1.dll

//ex2:
string fullName = "System.Transactions, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089";
Assembly asm2 = Assembly.ReflectionOnlyLoad(fullName);
ShowModuleInfo(asm2);
//Module Assembly: System.Transactions, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
//Module FullyQualifiedName: C:\Windows\assembly\GAC_32\System.Transactions\2.0.0.0__b77a5c561934e089\System.Transactions.dll
//Module Name: System.Transactions.dll
}
```

# 利用 Reflection 的技術，反向取得物件

某些時候，需動態產生 From，但是卻僅有 FormName 的狀況下，這時候可以利用 Reflection 的技術，反向取得物件
```c#
string FormName =  "命名空間" + "." + "表單名稱" ; 
((Form)Assembly.GetExecutingAssembly().CreateInstance(FormName)).Show(); 
```

可以這麼做是因為，自訂表單都是繼承自同一個父類別 Form

類似的技巧，若是僅有類別名稱，如何生成？
```c#
string ClassFullName =  "AssemblyName" + "." + "ClassName" ;
DBObject = (IDBObject)Assembly.Load(AssemblyName).CreateInstance(ClassFullNamee);
```

前提是，必需將這些類別都設計成由同一個基底類別而來，就可以用這個方法實作。