---
title: 反射組件的屬性
layout: default
parent: 反射(Reflection)
nav_order: 4
description: "反射組件的屬性"
date: 2012-03-21
tags: [DotNetFramework,反射(Reflection)]
postid: "2245827145702112127"
---
當我們設計一個組件時，除了類別的定義之外，還有一些額外的資訊是不包含在類別之中的，例如：版本編號，作者，公司資訊等等。  這些資訊統稱組件屬性，也就是前一節介紹的組件中繼資料。  欲編輯這些資訊可以在專案中開啟 [AssemblyInfo](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.assemblyinfo.aspx) 這個檔案，它就是用來儲存組件屬性的設定檔。(C#的檔案名稱為 AssemblyInfo.cs；VB的檔案名稱為 AssemblyInfo.vb)  

在預設情況下，組件資訊檔會包含以下資訊：
```c#
[assembly: AssemblyCompany("")]
[assembly: AssemblyCopyright("Copyright ©  2012")]
[assembly: AssemblyCulture("")]
[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]
```

在前置詞 assembly 後面的關鍵字代表組件各個不同的屬性，例如：AssemblyCompany 表示公司資訊、AssemblyVersion 表示版本編號...。  這些屬性名稱都有一個組件的屬性類別跟它互相對應，用來存取組件的屬性。  

叫用 [**Assembly.GetCustomAttributes**](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getcustomattributes) 可以取得組件的屬性陣列。  
```c#
//本例示範如何取得特定組件屬性

Assembly asm = Assembly.GetExecutingAssembly();

//取得AssemblyVersion屬性
object[] asmAttrs = asm.GetCustomAttributes(typeof(AssemblyVersionAttribute), false);
if (asmAttrs.Length > 0)
{
AssemblyVersionAttribute ver = (AssemblyVersionAttribute)asmAttrs[0];
Console.WriteLine("AssemblyVersion : {0}", ver.Version);
}

//取得AssemblyDescription屬性
object[] versionAttrs = asm.GetCustomAttributes(typeof(AssemblyDescriptionAttribute), false);
if (versionAttrs.Length > 0)
{
AssemblyDescriptionAttribute desc = (AssemblyDescriptionAttribute)versionAttrs[0];
Console.WriteLine(desc.Description);
}
```
```c#
//本例示範如何列舉組件的所有屬性

Assembly asm = Assembly.GetExecutingAssembly();
foreach (Attribute attr in asm.GetCustomAttributes(false))
{
string AttrName = attr.GetType().Name;
Console.WriteLine(AttrName);

if (attr.GetType() == typeof(AssemblyVersionAttribute))
{
Console.WriteLine("AssemblyVersion：{0}", ((AssemblyVersionAttribute)attr).Version);
}
else if (attr.GetType() == typeof(AssemblyDescriptionAttribute))
{
Console.WriteLine("AssemblyDescription：{0}", ((AssemblyDescriptionAttribute)attr).Description);
}
//...
}
```

[AssemblyVersion](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyversionattribute.aspx)   

上面例子中，使用 [GetCustomAttributes](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getcustomattributes.aspx) (typeof(AssemblyVersionAttribute), false) 一直取不到 [AssemblyVersion](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyversionattribute.aspx) 屬性。  原來它不是 Custom Attribute ，沒有被加進組件中。  它與編譯相關，可以用自動產生的序號更新組件編號 (build number) 和修正編號 (revision)。  若要取得這些值，可以使用 [Assembly.GetExecutingAssembly().GetName().Version. ToString()](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyname.version.aspx) 取得。  另外也可以使用 AssemblyFileVersion 這個屬性來做版本判別，它就與編譯無關，屬於 Custom Attribute 屬性。  

AssemblyVersionAttribute 由四組數字組成：  
&lt;major version&gt;.&lt;minor version&gt;.&lt;build number&gt;&lt;revision&gt;  

- major version：主要版號。通常用於大改版。
- minor version：次要版號。通常用於小改版。
- build number：組件編號。通常用於區分哪一天建置的。
- revision：修訂編號

若將 [AssemblyVersion](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyversionattribute.aspx) 屬性設定為 (1.0.\*) ，也就是以星號(asterisk)取式組件編號和修訂編號，則每次編譯時，系統會自動產生這二個編號。  其中 build number 是一個隨日期遞增的編號，（由 2000/01/01 開始算的天數）。而 revision 則是一個隨機產生的號碼。  

若將 [AssemblyVersion](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyversionattribute.aspx) 屬性設定為 (1.0.3.\*) ，則表示 build number 固定。而 revision 則隨機產生。  

若將 [AssemblyVersion](http://msdn.microsoft.com/zh-tw/library/system.reflection.assemblyversionattribute.aspx) 屬性設定為 (1.0.\*.2) ，不允許這樣子的設定。