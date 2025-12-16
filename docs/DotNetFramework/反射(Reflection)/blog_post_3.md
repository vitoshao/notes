---
title: 反射組件的型別
layout: default
parent: 反射(Reflection)
nav_order: 3
description: "反射組件的型別"
date: 2012-03-21
tags: [DotNetFramework,反射(Reflection)]
postid: "278416352573053897"
---
前面章節，已經說明了組件與模組的用法，接下來要說明的是型別的反射。

# 取得型别 (Type)

要取得一個型別物件 ( [Type](http://msdn.microsoft.com/zh-tw/library/system.type) )，可透過以下幾個方式：

- 由組件 ( [Assembly](http://msdn.microsoft.com/zh-tw/library/xbe1wdx9.aspx) ) 類別中的 [Assembly.GetType](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.gettype) 方法取得。
- 由模組 ( [Module](http://msdn.microsoft.com/zh-tw/library/5yhwh18w.aspx) ) 類別中的 [Module.GetType](http://msdn.microsoft.com/zh-tw/library/system.reflection.module.gettype) 方法取得。
- 由物件 ( Object ) 執行個體的 Object.GetType 方法取得。
- C# 的 [typeof](http://msdn.microsoft.com/zh-tw/library/58918ffs.aspx) 運算子與 Visual Basic 的 [GetType](http://msdn.microsoft.com/zh-tw/library/tay4kywk.aspx) 運算子，都可取得型別的 [Type](http://msdn.microsoft.com/zh-tw/library/system.type) 物件。

底下示範各種取得型別物件的方法，並透過型別的執行個體取得型別的屬性：
```c#
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm = Assembly.LoadFrom(dll_file);

//=====================================
// 由 Assembly 取得型別
//=====================================
Type empType = asm.GetType("ClassLibrary1.Employee");       //取得某一個型別
Type[] types1 = asm.GetTypes();                             //取得組件中所有型別
//ClassLibrary1.TestClass
//ClassLibrary1.Employee
//ClassLibrary1.Class2
//ClassLibrary1.Class1
//ClassLibrary1.ClassB
//ClassLibrary1.ClassA

//=====================================
// 由 Module 取得型別
//=====================================
Module[] mods = asm.GetModules();
Module m = mods[0];
Type[] types2 = m.GetTypes();

//=====================================
// 由 Object執行個體 取得型別
//=====================================
object o = new object();
Type type3 = o.GetType();

//=====================================
// 由 typeof 取得型別
//=====================================
Type types4 = typeof(Int32);                                //System.Int32
Type type5 = typeof(ClassLibrary1.ClassA);                  //ClassLibrary1.ClassA

// 透過型別的執行個體取得型別的屬性
Type t = typeof(String);
Console.WriteLine("Type: {0}", t.Name);                     //String
Console.WriteLine(" Namespace : {0}", t.Namespace);         //System
Console.WriteLine(" FullName : {0}", t.FullName);           //System.String
Console.WriteLine(" Is ValueType?: {0}", t.IsValueType);    //False
Console.WriteLine(" Is Sealed? : {0}", t.IsSealed);         //True
Console.WriteLine(" Is Abstract? : {0}", t.IsAbstract);     //False
Console.WriteLine(" Is Public? : {0}", t.IsPublic);         //True
Console.WriteLine(" Is Class? : {0}", t.IsClass);           //True
Console.WriteLine(" Is IsEnum? : {0}", t.IsEnum);           //False
```

# 列舉型别的成員

##  MemberInfo 

型别的成員包含**欄位**、**方法**、**屬性**、**事件**、**介面**等等的資訊。  在取得某型別物件後，可以透過 [Type](http://msdn.microsoft.com/zh-tw/library/42892f65.aspx) 類別所提供的方法和屬性，進一步取得這些相關的成員資訊。  

透過 [GetMembers](http://msdn.microsoft.com/zh-tw/library/system.type.getmembers.aspx) 方法，可以取得所有的成員資訊。  若只想取得特定類型的成員資訊則可以叫用相對應的方法，例如：  

- [GetField](http://msdn.microsoft.com/zh-tw/library/system.type.getfield.aspx) ：取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的特定欄位。
- [GetFields](http://msdn.microsoft.com/zh-tw/library/system.type.getfields.aspx) ：傳回目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的所有公用欄位。 [FieldInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.fieldinfo) 。
- [GetEvent](http://msdn.microsoft.com/zh-tw/library/system.type.getevent.aspx) ：取得由目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 所宣告或繼承的特定事件。
- [GetEvents](http://msdn.microsoft.com/zh-tw/library/system.type.getevensts.aspx) ：取得由目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 所宣告或繼承的所有公用事件。 [EventInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.eventinfo) 。
- [GetMethod](http://msdn.microsoft.com/zh-tw/library/system.type.getmethod.aspx) ：取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的特定方法。
- [GetMethods](http://msdn.microsoft.com/zh-tw/library/system.type.getmethods.aspx) ：傳回目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的所有公用成員。 [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.aspx) 。
- [GetProperty](http://msdn.microsoft.com/zh-tw/library/system.type.getproperty.aspx) ：取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的特定屬性。
- [GetProperties](http://msdn.microsoft.com/zh-tw/library/system.type.getproperties.aspx) ：傳回目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的所有公用屬性。 [PropertyInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.propertyinfo.aspx) 。
- [GetConstructor](http://msdn.microsoft.com/zh-tw/library/system.type.getconstructor.aspx) ：取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的特定建構函式。
- [GetConstructors](http://msdn.microsoft.com/zh-tw/library/system.type.getconstructors.aspx) ：取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 的所有建構函式。 [ConstructorInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo.aspx) 。
- [GetInterface](http://msdn.microsoft.com/zh-tw/library/system.type.getinterface.aspx) ：取得由目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 所實作或繼承的特定介面。
- [GetInterfaces](http://msdn.microsoft.com/zh-tw/library/system.type.getinterfaces.aspx) ：可以取得目前 [Type](http://msdn.microsoft.com/zh-tw/library/system.type.aspx) 所實作的所有介面。

透過這些方法回傳的型别成員都是由抽象類別 [MemberInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.memberinfo) 中繼承而來。  且其類別名稱都帶有 Info 結尾，例如：  

- [FieldInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.fieldinfo) ：表示欄位資訊的類別
- [EventInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.eventinfo) ：表示事件資訊的類別
- [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo) ：表示方法資訊的類別
- [PropertyInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.propertyinfo) ：表示屬性資訊的類別
- [ConstructorInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo) ：表示類別建構函式資訊的類別
- [LocalVariableInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.localvariableinfo) ：表示區域變數的類別
- [MethodBase](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbase) ：提供有關方法和建構函式的資訊。
- [Type](http://msdn.microsoft.com/zh-tw/library/system.type) ：代表型別宣告：類別型別、介面型別、陣列型別、值型別、列舉型別、型別參數、泛型型別定義，以及開放式或封閉式的建構泛型型別。
```c#
// 由 Assembly 取得型別
string asm_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm = Assembly.LoadFrom(asm_file);
Type t = asm.GetType("ClassLibrary1.Employee");

//顯示 Constructor 資訊  
foreach (ConstructorInfo constructor in t.GetConstructors())
{
Console.WriteLine("[{0}] {1}", constructor.MemberType, constructor.Name);
}

//顯示 Method 資訊  
BindingFlags flags = BindingFlags.DeclaredOnly;
foreach (MethodInfo method in t.GetMethods())
{
Console.WriteLine("[{0}] {1}", method.MemberType, method.Name);
}

//顯示 Field 資訊  
foreach (FieldInfo field in t.GetFields())
{
Console.WriteLine("[{0}] {1}", field.MemberType, field.Name);
}

//顯示 Property 資訊  
foreach (PropertyInfo prop in t.GetProperties())
{
Console.WriteLine("[{0}] {1}", prop.MemberType, prop.Name);
}

//顯示 Event 資訊  
foreach (EventInfo even in t.GetEvents())
{
Console.WriteLine("[{0}] {1}", even.MemberType, even.Name);
}

//顯示 Interface 資訊  
foreach ( Type type in t.GetInterfaces())
{
Console.WriteLine("[{0}] {1}", type.MemberType, type.Name);
}

//顯示 所有成員 資訊  
foreach (MemberInfo member in t.GetMembers())
{
Console.WriteLine("[{0}] {1}", member.MemberType, member.Name);
}

//[Method] get_EmpNum
//[Method] set_EmpNum
//[Method] set_EmpName
//[Method] get_EmpName
//[Method] get_Age
//[Method] set_Age
//[Method] ToString
//[Method] Equals
//[Method] GetHashCode
//[Method] GetType

//[Constructor] .ctor
//[Constructor] .ctor

//[Property] EmpNum
//[Property] EmpName
//[Property] Age
```

##  MethodBody 

以上的說明方法，僅僅取得型別的結構，並沒有取得程式碼部分，這部分就必須透過 [MethodBody](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.aspx) 類別來取得。   [MethodBody](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.aspx) 是一個特殊的物件，裝載著**區域變數**和**IL指令碼**。  要取得 [MethodBody](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.aspx) 可以透過 [MethodBase](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbase) ( [ConstructorInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo) , [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.aspx) 等類別 ) 的執行個體，透過呼叫 [**GetMethodBody**](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbase.getmethodbody) 來取得。  

####  [MethodBody](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.aspx) 類別中，幾個重要的屬性與方法：

- [MaxStackSize](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.maxstacksize.aspx) ：取得執行此方法時，運算元堆疊上的最大項目數。
- [&lt;font color="red"&gt;LocalVariables&lt;/font&gt;](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.localvariables.aspx) ：取得方法主體中所宣告之區域變數的清單。
- [&lt;font color="red"&gt;GetILAsByteArray&lt;/font&gt;](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodbody.getilasbytearray.aspx) ：傳回方法主體的 MSIL，當做位元組陣列。
```c#
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm = Assembly.LoadFile(dll_file);
Type classA = asm.GetType("ClassLibrary1.Employee");

//1)先取得想要追縱的方法
MethodInfo method = classA.GetMethod("ShowData");

//2)再由方法的執行個體，透過 GetMethodBody 取得 MethodBody
MethodBody body = method.GetMethodBody();

//取得 堆疊大小
Console.WriteLine(" MaxStack: {0}", body.MaxStackSize);

//列舉 變數
foreach (LocalVariableInfo local in body.LocalVariables)
{
Console.WriteLine("[{0}] Var({1})", local.LocalType, local.LocalIndex);
}
//[System.String] Var(0)
//[System.String] Var(1)
//[System.String] Var(2)
//[System.Int32] Var(3)
//[System.String[]] Var(4)

//列舉 IL 
foreach (Byte b in body.GetILAsByteArray())
{
Console.Write("{0:x2} ", b);
}
//00 02 28 16 00 00 06 0d 12 03 ...... 00 0a 0b 07 0c 2b 00 08 2a 
```

# BindingFlags

[BindingFlags](http://msdn.microsoft.com/zh-tw/library/system.reflection.bindingflags.aspx) 是一個列舉型別，  用來控制反射的繫結旗標，這個旗標是用來指定 [GetMembers](http://msdn.microsoft.com/zh-tw/library/system.type.getmembers.aspx) 方法的搜尋方式。  

- IgnoreCase：不考慮成員名稱的大小寫。
- DeclaredOnly：只考慮型別本身的成員。不考慮繼承成員。
- Instance：執行個體成員
- Static：靜態成員
- Public：公用成員
- NonPublic：非公用成員
- FlattenHierarchy：

例如前面一個例子，因為沒有指明 [BindingFlags](http://msdn.microsoft.com/zh-tw/library/system.reflection.bindingflags.aspx) ，所以 [GetMembers](http://msdn.microsoft.com/zh-tw/library/system.type.getmembers.aspx) 僅會回傳公用的成員，下面這個例子，則會回傳私用的成員：  
```c#
//取得組件型別
Assembly asm = Assembly.LoadFrom(@"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll");
Type empType = asm.GetType("ClassLibrary1.Employee");

//依指定的 BindingFlags , 列舉所有成員
BindingFlags flags = BindingFlags.NonPublic | BindingFlags.Instance;
foreach (MemberInfo member in empType.GetMembers(flags))
{
Console.WriteLine("[{0}] {1}", member.MemberType, member.Name);
}
//[Method] MyNumB
//[Method] Finalize
//[Method] MemberwiseClone
//[Field] EmpNumB
//[Field] <EmpName&gl;k__BackingField
//[Field] <Age&gl;k__BackingField
```

#### 備註：

指定 [BindingFlags](http://msdn.microsoft.com/zh-tw/library/system.reflection.bindingflags.aspx) 時，必須指定 Instance 或 Static 與 Public 或 NonPublic，否則不會傳回成員。