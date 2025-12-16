---
title: 動態載入組件
layout: default
parent: 反射(Reflection)
nav_order: 2
description: "動態載入組件"
date: 2012-03-21
tags: [DotNetFramework,反射(Reflection)]
postid: "6955829823012148842"
---
在瞭解組件的相關資訊後，本章節接著要探討的是，一個組件在沒有事先被參考的前題下，如何被動態載入與執行。

# 載入組件與叫用成員

動態載入組件，並執行方法的步驟：

1. 載入組件，取得型別 (Type)。
2. 取得建構函式：透過型別的 [GetConstructor](http://msdn.microsoft.com/zh-tw/library/hy6h38d5.aspx) 方法取得建構函式資訊 ( [ConstructorInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo) )。
3. 建立執行個體：透過建構函式叫用 [ConstructorInfo.Invoke](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo.invoke) 建立物件的執行個體。
4. 取得方法：透過型別的 [GetMethod](http://msdn.microsoft.com/zh-tw/library/system.type.getmethod.aspx) 方法取得要執行的方法資訊 ( [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.aspx) )。
5. 叫用方法資訊 [MethodInfo.Invoke](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.invoke.aspx) 以執行程式碼。

#### 叫用建構函式 (ConstructorInfo.Invoke) 的時候，必須傳入相對應的建構函式的[參數列]。
```c#
public object Invoke(object[] parameters);
public abstract object Invoke(BindingFlags invokeAttr, Binder binder, object[] parameters, CultureInfo culture);
```

#### 叫用方法 (MethodInfo.Invoke) 的時候，必須傳入２個參數。其中，第一個參數是[執行個體]，第二個參數是要傳給該方法的[參數列]。
```c#
public object Invoke(object obj, object[] parameters);
public abstract object Invoke(object obj, BindingFlags invokeAttr, Binder binder, object[] parameters, CultureInfo culture);
```

#### 也可以使用 (Type.InvokeMember) 叫用方法。
```c#
public object InvokeMember(string name, BindingFlags invokeAttr, Binder binder, object target, object[] args);
public object InvokeMember(string name, BindingFlags invokeAttr, Binder binder, object target, object[] args, CultureInfo culture);
...
```

## 例一：使用沒有參數的建構子建立物件，並叫用方法
```c#
//=======================================================
// 1) 載入組件，取得型別
//=======================================================
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm = Assembly.LoadFile(dll_file);
Type employee = asm.GetType("ClassLibrary1.Employee");

//=======================================================
// 2) 取得建構函式資訊
//=======================================================

// 傳入空的陣列參數列，建立一個 Constructor
Type[] args = new Type[] { };
ConstructorInfo ContInfo = employee.GetConstructor(args);

//=======================================================
// 3) 建立執行個體
//=======================================================

// 產生 ctor 的 instance
object obj = ContInfo.Invoke(null);

//=======================================================
// 4) 取得方法資訊
//=======================================================
MethodInfo miShowData = employee.GetMethod("ShowData");

//=======================================================
// 5) Invoke 方法
//=======================================================
string sEmpData = (string) miShowData.Invoke(obj, null);
Console.WriteLine(sEmpData);      //I am Unknow, 0 years old.
```

## 例二：叫用靜態成員。

若呼叫的是一個靜態方法，則不用建立執行個體，所以第一個參數可以用 null 取代。  若不用傳參數給方法，第二個參數也可以用 null 取代。  
```c#
Assembly asm = Assembly.LoadFile(@"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll");
Type type = asm.GetType("ClassLibrary1.Employee");

//不傳參數呼叫靜態方法
string return1 = (string)type.GetMethod("SayHello").Invoke(null, null);
Console.WriteLine(return1);      //hello.

//傳參數呼叫靜態方法
string return2 = (string)type.GetMethod("MyName").Invoke(null, new object[] { "vito" });
Console.WriteLine(return2);     //I am vito
```

## 例三：使用具參數的建構子建立物件，並叫用方法
```c#
//get the instance of controler
Type[] args2 = Type.EmptyTypes;
ConstructorInfo ctor2 = emp.GetConstructor(args2);
object obj2 = ctor2.Invoke(null);

//invoke method
sEmpData = (string)miShowData.Invoke(obj2, null);

Console.WriteLine(sEmpData);                    //I am Unknow, 0 years old.
```

## 例四：精簡一下寫法

建立執行個體：**GetConstructor**.Invoke(**建構子的參數陣列**);  
執行方法：**GetMethod**.Invoke(**物件執行個體**, **方法的參數陣列**);  
```c#
// 載入組件，取得型別
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Type emp = Assembly.LoadFile(dll_file).GetType("ClassLibrary1.Employee");

// 建立型別的執行個體
// invoke 的參數是建構子的參數列
Type[] args = new Type[] { typeof(string), typeof(string), typeof(int) }; ;
object obj = emp.GetConstructor(args).Invoke(new object[] { "1234", "vito", 20 });    //指明以哪一個 Constructor 建立執行個體

// 叫用型別的方法
// invoke 的第一個參數是執行個體，第二個參數是要傳給方法的參數列
string sEmpData = (string)emp.GetMethod("ShowData").Invoke(obj, null);
Console.WriteLine(sEmpData);                    //I am vito, 20 years old.
```

除了上面例子中提到的 [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.aspx) 類別，其他 Info 類別 (FieldInfo,EventInfo...) ，也都是使用類似的方法來呼叫程式碼。

## Binder 類別

在處理動態程式碼時，可以建立自已的 [Binder](http://msdn.microsoft.com/zh-tw/library/y135dh1e.aspx) 類別，以決定如何處理型別的轉換。

## Activator 類別

使用 [Activator](http://msdn.microsoft.com/zh-tw/library/b4wc81dc.aspx) 類別的 [CreateInstance](http://msdn.microsoft.com/zh-tw/library/d49ss92b.aspx) 方法，可以用來**建立指定型別的執行個體**。  這樣就不必再透過 [ConstructorInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.constructorinfo) ，  它會叫用最符合指定引數的建構函式，建立型別的執行個體。  
```c#
// 載入組件，取得型別
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Type emp = Assembly.LoadFile(dll_file).GetType("ClassLibrary1.Employee");

// 建立型別(無參數）的執行個體
object instance1 = Activator.CreateInstance(emp);
string sEmpData = (string)emp.GetMethod("ShowData").Invoke(instance1, null);
Console.WriteLine(sEmpData);

// 建立型別(有參數）的執行個體
object instance2 = Activator.CreateInstance(emp, new object[] { "1234", "vito", 20 });
string sEmpData2 = (string)emp.GetMethod("ShowData").Invoke(instance2, null);
Console.WriteLine(sEmpData);   
```

# 範例

## 例六：下面範例示範，如何載入 System.Web 組件，並使用 HttpUtility 類別進行文字的編碼。
```c#
string path = @"C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\System.Web.dll";

// 根據指定的檔案建立組件
Assembly webAssembly = Assembly.LoadFile(path);

// 取得型別 HttpUtility 
Type utilType = webAssembly.GetType("System.Web.HttpUtility");

// 取得靜態方法 HtmlEncode 和 HtmlDecode
MethodInfo miEncode = utilType.GetMethod("HtmlEncode", new Type[] { typeof(string) });
MethodInfo miDecode = utilType.GetMethod("HtmlDecode", new Type[] { typeof(string) });

// Create a string to be encoded
string strOriginal = "This is Sally & Jack's Anniversary";
Console.WriteLine(strOriginal);

// 叫用 HtmlEncode 方法
string strEncoded = (string)miEncode.Invoke(null, new object[] { strOriginal });
Console.WriteLine(strEncoded);

// 叫用 HtmlDecode 方法
string strDecoded = (string)miDecode.Invoke(null, new object[] { strEncoded });
Console.WriteLine(strDecoded);
```

## 例七：下面是一個完整例子，示範反射的相關用法。

#### 組件原始內容
```c#
namespace ClassLibrary1
{
public class Employee
{
private string EmpNum;
public string EmpName { get; set; }
public int Age { get; set; }

public Employee()
{
EmpNum = "F123456789";
EmpName = "Unknow";
Age = 0;
}

public Employee(string pEmpNum, string pEmpName, int pAge)
{
EmpNum = pEmpNum;
EmpName = pEmpName;
Age = pAge;
}

public string ShowData()
{
string sAge = Age.ToString();
string msg = "I am " + EmpName + ", " + sAge + " years old.";
return msg;
}

public void SetAge(int pAge)
{
Age = pAge;
}

public static string MyName(string name)
{
return "I am " + name;
}

private string MyNum()
{
return EmpNum;
}
}
}
```

#### 反射上面組件的內容
```c#
//載入組件，
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll";
Assembly asm = Assembly.LoadFile(dll_file);

//反射型別
Type empType = asm.GetType("ClassLibrary1.Employee");

//建立實體
object employee = Activator.CreateInstance(empType, new object[] { "F222222222", "vito", 20 });

//=========================================================================
// 使過 MethodInfo.Invoke 叫用方法
//=========================================================================

//叫用 ShowData 方法：(1)使用 MethodInfo.Invoke
MethodInfo miShowData = empType.GetMethod("ShowData");
string sShowData = (string) miShowData.Invoke(employee, null);
Console.WriteLine(sShowData);

//取得屬性
PropertyInfo piEmpName = empType.GetProperty("EmpName");
string EmpName = (string) piEmpName.GetValue(employee, null);

//設定屬性
PropertyInfo piAge = empType.GetProperty("Age");
piAge.SetValue(employee, 30, null);

//=========================================================================
// 使過 Type.InvokeMember 叫用方法
//=========================================================================

//【叫用 Public 方法】 ShowData ：(2)使用 Type.InvokeMember
sShowData = (string)empType.InvokeMember(
"ShowData"
, BindingFlags.Public | BindingFlags.InvokeMethod | BindingFlags.Instance
, null, employee, null);
Console.WriteLine(sShowData);

//【叫用 Private 方法】 MyNum
string sEmpNum = (string)empType.InvokeMember(
"MyNum"
, BindingFlags.NonPublic | BindingFlags.InvokeMethod | BindingFlags.Instance
, null, employee, null);

//【叫用帶參數的公開方法】
empType.InvokeMember(
"SetAge"
, BindingFlags.Public | BindingFlags.InvokeMethod | BindingFlags.Instance
, null, employee, new object[]{25});

//【叫用 Static 方法】 (不須使用到 instance)
string sMyName = (string)empType.InvokeMember(
"MyName"
, BindingFlags.Static | BindingFlags.InvokeMethod | BindingFlags.Public
, null, null, new object[] { "test" });

//【取得 Private 欄位】  
sEmpNum = (string)empType.InvokeMember(
"EmpNum"
, BindingFlags.NonPublic | BindingFlags.GetField | BindingFlags.Instance
, null, employee, null);
```

## 例八：動態載入組件與執行方法的完整例子。
```c#
// 1)取得組件
Assembly asm = Assembly.LoadFrom(@"D:\MCTS\MCTS2011\Practice\ClassLibrary1\bin\Debug\ClassLibrary1.dll");

// 2)取得型別
Type clsEmploye = asm.GetType("ClassLibrary1.Employee");

// 3)取得方法
MethodInfo method = clsEmploye.GetMethod("ShowData");

// 4)叫用 ShowData 方法

// 4.1)建立constructor
ConstructorInfo ctor = clsEmploye.GetConstructor(new Type[] { typeof(string), typeof(string), typeof(int) });
object obj = ctor.Invoke(new object[] { "1234", "vito", 20 });

// 4.2)invoke method
string sEmpData = (string)method.Invoke(obj, new object[] { });

Console.WriteLine(sEmpData);

// MethodInfo 類別底下的幾個重要屬性

Console.WriteLine("IsAbstract：{0}", method.IsAbstract); //判斷 method 是否為 abstract ：
Console.WriteLine("IsPrivate：{0}", method.IsPrivate);   //判斷 method 是否為 private ：
Console.WriteLine("IsPublic：{0}", method.IsPublic);     //判斷 method 是否為 public ：
Console.WriteLine("IsFamily：{0}", method.IsFamily);     //判斷 method 是否為 protected ：只能在其類別和衍生類別內看見該方法或建構函式。
Console.WriteLine("IsAssembly：{0}", method.IsAssembly); //判斷 method 是否為 internal ：只有相同組件中的其他型別可以看見該方法或建構函式，組件外部的衍生型別則看不見它們。 

Module module = method.Module;                            //反向取得Module
Type type = method.ReflectedType;                         //反向取得Type，回傳結果等同上面的clsEmploye
```
**備註：**  
.NET DLL 組件一旦載入，就會一直留在記憶體中，直到載入它的主程式結束為止。  若要更靈活運用記憶體，就必須使用把 DLL 組件載入到不同的 app domain。