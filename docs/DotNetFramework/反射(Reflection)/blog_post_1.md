---
title: 動態建立組件
layout: default
parent: 反射(Reflection)
nav_order: 1
description: "動態建立組件"
date: 2012-03-21
tags: [DotNetFramework,反射(Reflection)]
postid: "2643764941153914260"
---
反射不僅可以檢測已經存在的組件的型別資訊，也可以在執行階段，自行定義組件資訊，並回存到磁碟以重複使用。

# 建立自訂組件程式碼

在反射系統中，有一個子命名空間叫做 [Emit](http://msdn.microsoft.com/zh-tw/library/b0fx175t.aspx) ( [System.Reflection.Emit](http://msdn.microsoft.com/en-us/library/xd5fw18y.aspx) )。  在 [Emit](http://msdn.microsoft.com/zh-tw/library/b0fx175t.aspx) 中有一組 **builder** 類別，是用來建立組件、型別、方法等物件。  在執行時期要產生程式碼，必須循序漸進，先建立組件，再建立組件中的模組，最後是模組中的型別。  每一個builder類別都是由它本身的info類別部分繼承而來。  例如， [AssebmlyBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilder.aspx) 類別是繼承 [AssemblyInfo](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.assemblyinfo.aspx) ， [MethodBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.methodbuilder.aspx) 類別是繼承 [MethodInfo](http://msdn.microsoft.com/zh-tw/library/system.reflection.methodinfo.aspx) 。  

#### 建立一個組件的步驟：

就是一步一步來：

- 1. 建立 AssemblyBuilder ：透過 AppDomain 類別的 [DefineDynamicAssembly](http://msdn.microsoft.com/zh-tw/library/b4fs3t0e.aspx) 方法，建立動態組件 ( [AssemblyBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilder.aspx) )。
- 2. 建立 ModuleBuilder ：利用建立的 [AssemblyBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilder.aspx) ，產生模組物件( [ModuleBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.modulebuilder.aspx) )。
- 3. 建立 TypeBuilder ：利用建立的 [ModuleBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.modulebuilder.aspx) 物件中的 [DefineType](http://msdn.microsoft.com/zh-tw/library/23e9bwy1.aspx) 方法，來建立動態型別 ( [TypeBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.typebuilder.aspx) )。
- 4. 建立其他成員Builder：使用 [TypeBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.typebuilder.aspx) 提供的方法，以建立各類的成員。如： [ConstructorBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.constructorbuilder) 、 [MethodBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.methodbuilder.aspx) ...。
- 5. 建立程式碼：由 [ConstructorBuilder.GetILGenerator](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.constructorbuilder.getilgenerator.aspx) 建立一個 [ILGenerator](http://msdn.microsoft.com/zh-tw/library/wz52k528.aspx) 物件來撰寫動態程式碼。

####  [AppDomain.DefineDynamicAssembly](http://msdn.microsoft.com/zh-tw/library/system.appdomain.definedynamicassembly.aspx) 多載方法
```c#
//使用[指定的名稱]、[存取模式]來定義動態組件。
public AssemblyBuilder DefineDynamicAssembly(AssemblyName name, AssemblyBuilderAccess access);

//使用[指定的名稱]、[存取模式]和[儲存目錄]來定義動態組件。
public AssemblyBuilder DefineDynamicAssembly(AssemblyName name, AssemblyBuilderAccess access, string dir);
...
```

####  [AssemblyName](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyname.aspx) 建構子
```c#
public AssemblyName();
public AssemblyName(string assemblyName);
```

####  [AssemblyBuilderAccess](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilderaccess.aspx) 列舉

在建立動態組件時，必須使用 [AssemblyBuilderAccess](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilderaccess.aspx) 列舉，指明組件的存取模式。

- Run：動態組件可以執行，但不能儲存。
- Save：動態組件可以儲存，但不能執行。
- RunAndSave：動態組件可以執行及儲存。
- ReflectionOnly：動態組件已載入至僅反射的內容中，而且無法執行。
- RunAndCollect：動態組件可以卸載而且其記憶體可以回收

####  [AssemblyBuilder](http://msdn.microsoft.com/zh-tw/library/system.reflection.emit.assemblybuilder.aspx) 類別的方法
```c#
AssemblyBuilder.Save(string assemblyFileName);
```

**注意：**

上面這一行是儲存組件時叫用的方法，不過它的參數值內容，會受到 DefineDynamicAssembly 建構子的引響：

- 若要將組件儲存到指定的目錄，則必須使用有指定儲存目錄的 DefineDynamicAssembly 建構子。
- 若使用沒有指定路徑的 DefineDynamicAssembly 建構子，則此處 assemblyFileName 參數就不能含有磁碟機或目錄名稱。

下列程式碼示範，如何建立組件
```c#
//================================================================================================
//建立 Dynamic Assembly  （使用AssemblyBuilderAccess指定組件的存取模式。）
//================================================================================================

AssemblyBuilder myAsmBuilder = AppDomain.CurrentDomain.DefineDynamicAssembly(
new AssemblyName("DemoAssembly"), 
AssemblyBuilderAccess.RunAndSave
);

//================================
//建立 Module
//================================

ModuleBuilder myModuleBuilder = myAsmBuilder.DefineDynamicModule("DemoModule", "DemoAssembly.dll");

//================================
//建立 Type               （使用TypeAttributes指定型別屬性。）
//================================

TypeBuilder myTypeBuilder = myModuleBuilder.DefineType("DemoClass", TypeAttributes.Public | TypeAttributes.Class);

//================================
//建立 Field
//================================

//DefineField(para1,para2,para3) => (欄位名稱,欄位型別,欄位的屬性標籤)
// private int _count;
FieldBuilder myField = myTypeBuilder.DefineField("_count", typeof(int), FieldAttributes.Private);

//================================
// 建立 constructor 
// DefineConstructor：用來"定義建構式"，
// GetConstructor：用來取得"可實際被呼叫的建構式方法"
//================================

//建立一個具有個int參數的建構子
Type[] args = { typeof(int) };
ConstructorBuilder myConstructorBuilder =  myTypeBuilder.DefineConstructor(MethodAttributes.Public, CallingConventions.Standard, args);

//產生 MSIL 指令碼.
ILGenerator IL_generator = myConstructorBuilder.GetILGenerator();

//================================================================================================
// 以上完成動態組件的建立，但是不含程式碼。必須透過 ILGenerator.Emit() 方法，將 IL Code 放置進去
//================================================================================================

// 放置指令到指令資料流中
// 以下指令相當於建立C#程式碼 :  _count = x
IL_generator.Emit(OpCodes.Ldarg_0);          // load arg0
IL_generator.Emit(OpCodes.Ldarg_1);          // load arg1
IL_generator.Emit(OpCodes.Stfld, myField);   // 以新值 arg1 取代指定的參考物件 myField ( _count)
IL_generator.Emit(OpCodes.Ret);              // 回傳 IL 程式碼

//================================
//建立 Method 程式碼
//================================

// 放置指令到指令資料流中 
// 以下指令相當於建立C#程式碼 :  _count = _count + x
Type[] AddArgs = { typeof(int) };
MethodBuilder myMethodBuilder = myTypeBuilder.DefineMethod("Add", MethodAttributes.Public, null, AddArgs);
ILGenerator ilAdd = myMethodBuilder.GetILGenerator();
ilAdd.Emit(OpCodes.Ldarg_0); //
ilAdd.Emit(OpCodes.Dup);
ilAdd.Emit(OpCodes.Ldfld, myField);
ilAdd.Emit(OpCodes.Ldarg_1);
ilAdd.Emit(OpCodes.Add);
ilAdd.Emit(OpCodes.Stfld, myField);
ilAdd.Emit(OpCodes.Ret);

//================================
//建立 Property 
//1.建立屬性◦
//2.建立屬性的存取欄位。
//3.建立屬性存取欄位方法。
//================================

//(para1,para2,para3,para4) => (屬性名稱,屬性標籤,傳回型別,參數列型別)
// int Count
PropertyBuilder myProperty = myTypeBuilder.DefineProperty("Count", PropertyAttributes.None, typeof(int), new Type[] { typeof(int) });

//================================
//建立 Property 的存取方法 
//================================

//定義存取方法的屬性，其中要做為類別屬性的存取方法，必需要有特別的屬性，亦即要有MethodAttributes.SpecialName及MethodAttributes.HideSig
MethodAttributes getsetAttributes = MethodAttributes.Public | MethodAttributes.SpecialName | MethodAttributes.HideBySig;

// GET 

//定義取得方法，(para1,para2,para3,para4) => (方法名稱,描述方法的屬性,回傳的型別,傳入參數列型別)
MethodBuilder mbGetCount = myTypeBuilder.DefineMethod("get_Count", getsetAttributes, typeof(int), null);

//定義取得方法的中介語言，第二行是代表將我們先前定義的FieldBuilder欄位值傳回。
ILGenerator ilGetCount = mbGetCount.GetILGenerator();
ilGetCount.Emit(OpCodes.Ldarg_0);       // ldarg.0 holds the "this" reference 
ilGetCount.Emit(OpCodes.Ldfld, myField);
ilGetCount.Emit(OpCodes.Ret);

// SET 

//定義設定方法，(para1,para2,para3,para4) => (方法名稱,描述方法的屬性,回傳的型別,傳入參數列型別)
MethodBuilder mbSetCount = myTypeBuilder.DefineMethod("set_Count", getsetAttributes, null, new Type[] { typeof(int) });

//定義設定方法的中介語言，第三行是將傳入值設定到先前定義的FieldBuilder欄位。
ILGenerator ilSetCount = mbSetCount.GetILGenerator();
ilSetCount.Emit(OpCodes.Ldarg_0);
ilSetCount.Emit(OpCodes.Ldarg_1);
ilSetCount.Emit(OpCodes.Stfld, myField);
ilSetCount.Emit(OpCodes.Ret);

//在定義好存取及設定方法後，接下來將方法加入到屬性當中。
myProperty.SetGetMethod(mbGetCount);
myProperty.SetSetMethod(mbSetCount);

//================================
//建立型別
//================================

myTypeBuilder.CreateType();

//================================
//儲存組件
//================================
myAsmBuilder.Save(@"DemoAssembly.dll");

//=========================================================
//若不儲存組件資訊，我們也可以直接使用目前建立的型別
//=========================================================
//取得型別，建立 Constructor 
Type myType = myTypeBuilder.CreateType();
ConstructorInfo creater = myType.GetConstructor(new Type[]{typeof(int)}); 
object TypeInstance = creater.Invoke(new object[] { 20 });

//取得 GetCount 方法
MethodInfo mGetCount = myType.GetMethod("get_Count");

//查看目前的值
Console.WriteLine(((int)mGetCount.Invoke(TypeInstance, null)).ToString()); //20

//取得 SetCount 方法，並且 Invoke
MethodInfo mSetCount = myType.GetMethod("set_Count");
mSetCount.Invoke(TypeInstance, new object[] { 10 });

//查看目前的值
Console.WriteLine(((int) mGetCount.Invoke(TypeInstance,null)).ToString());  //10

//取得 Add 方法，並且 Invoke
MethodInfo mAdd = myType.GetMethod("Add");
mAdd.Invoke(TypeInstance, new object[] { 25 });

//查看目前的值
Console.WriteLine(((int)mGetCount.Invoke(TypeInstance, null)).ToString());  //35
```

下圖：我們用ILDasm這個工具來反組譯上面程式碼所產生的Dll。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj225XJ3JYJgBvh8pMUBs8AiLlnM5anh7DcoVoc3CON1Ow5aWTRAmfsDOnaGbJGqptGq6BH3eDc74g5ki29Q6Ps6BgUgIGz0dRWc_VOTLcAq7raYw6HKai3bJWFXBYOvIjSNvmRORFshvA/s498/ILDASM_All.png)

下圖：IL程式碼中 Constructor 的定義。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEholoeS760VhQ0sLAKvxuwePw07vKpVqO1Q3Hv688JLkUC_xOaeFofdIpfe-yMtzwNVr0Yxrz_gweVCiR86GUecesT3gBpFMb50zIjI7HlrnCNsEtIJjDfw5xc10mruFrnTBj4z949VlQQ/s529/ILDASM_Constructor.png)

下圖：IL程式碼中 Add 方法的定義。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYZEhnZzV7h0YgSb6W7DB2UbwiIsCQjgESPTPEUx-zyG1-UbtAJpHNNLp_YhKQfSTgE0qD7opN61cTKpqJMTIXRB4OuNY6mlK7GxLczI3KXCbLjwpMPG_tNNknoBz-ZOz6WJIxUTaqslc/s528/ILDASM_Add.png)

下圖：我們用 Telerik Decompiler 這個反組譯工具，來看看 Reflectoin 後的程式碼。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvJvVvL-E8I1lcdwiTwdlqNN9gfjgirncH6UD4oqr7oH0Ne1TvHkwnO9HEfHI4PqLCtFlsvWDJn6fBRHbac1_laxIlVGK8Oj02xkaiXcSD0tIuL251zT6fxe99KY7oT7OemdCt8XgFRzk/s811/ILDASM_Telerik.png)

將上面程式碼建立的組件加入參考後，我們寫一段code來驗證一下這個組件型別是否可以正常使用：
```c#
DemoClass demo = new DemoClass(15);
Console.WriteLine(demo.get_Count());  //15

demo.set_Count(25);
Console.WriteLine(demo.get_Count());  //25

demo.Add(10);
Console.WriteLine(demo.get_Count());  //35
```


- [NET中間語言（IL）](http://msdn.microsoft.com/zh-tw/library/dd229210.aspx)
- [OpCodes 類別](http://msdn.microsoft.com/zh-tw/library/x2ebty98.aspx)
- [NET Emit技術初探](https://sites.google.com/a/tsima.org.tw/www/wiki/dian-zi-qi-kan/2010nian2yue-hao/-net-emit-ji-shu-chu-tan-shang)