---
title: is 和 as
layout: default
parent: CSharp
nav_order: 1
description: "is 和 as"
date: 2012-03-23
tags: [DotNetFramework,CSharp]
postid: "2410615849573445190"
---
由於物件都是多型的，因此可以使用某個基底類別 (Base Class) 型別的變數來存放衍生型別 (Derived Type)。  
若要存取衍生型別的方法，您必須將值轉型回衍生型別。  
不過，在這種情況下嘗試進行簡單轉型卻會有擲回 InvalidCastException 的風險。  
這就是 C# 提供 is 和 as 運算子的原因。  
您可以使用這兩個運算子測試轉型能否成功，而不會擲回例外狀況 (Exception)。  

as 運算子通常比較有效率，因為如果轉型能夠成功，它實際會傳回轉型值。  
is 運算子卻只會傳回布林值，因此它可以用在只是要確定物件的型別而不必實際轉型的情況。  

is 和 as 都是 C# 中的運算子，其功能如下:

- is ：用來檢查物件是否與指定的型別相容。
- as ：用來檢查物件是否可以轉換成特定類型。

# is 運算子

若可以將型別轉換成提供的型別，is 運算式就會評估為 true。  
若對象為Null，則返回值永遠為false。  
```c#
class anamal{}
class monkey : anamal { } 
class rose { }

private void button12_Click(object sender, EventArgs e)
{
anamal a = new anamal();
monkey m = new monkey();
rose r = new rose();

test(a);  //its an anamal.
test(m);  //its an anamal. its a monkey.
test(r);  //its a rose.
}

void test(object o)
{
if (o is anamal)
Console.WriteLine("its an anamal.");

if (o is monkey)
Console.WriteLine("its a monkey.");

if (o is rose)
Console.WriteLine("its a rose.");
}
```

# as 運算子

as 運算子就像是轉型作業。 不過，如果不能進行轉換，as 便會傳回 null，而不會引發例外狀況 (Exception)。 
```c#
//as 的用法
expression as type

//上面的運算式相當於下列運算式
expression is type ? (type)expression : (type)null
```
```c#
void test_anamal(object obj)
{
anamal a = obj as anamal;
if (a != null)
Console.WriteLine("its an anamal");
else
Console.WriteLine("its not an anamal");
}

private void button13_Click(object sender, EventArgs e)
{
anamal a = new anamal();
monkey m = new monkey();
rose r = new rose();

test_anamal(a);   //its an anamal.
test_anamal(m);   //its an anamal.
test_anamal(r);   //its not an anamal
}
```


- [使用 as 和 is 運算子進行安全轉型 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/cc488006)
- 
-