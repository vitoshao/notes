---
title: 委派與反射
layout: default
parent: Foundation
nav_order: 10
description: "委派與反射"
date: 2012-03-21
tags: [DotNetFramework,Foundation]
postid: "8522733778372616599"
---
委派就是要委託某個執行方法，但是，若是這個執行方法必須要在 RunTime 才能決定的話，那要如何設計這個委派呢？  

#### 問題

假設存在底下二個類別，我們要委派執行 MethodB ，但是，必須在 RunTime 時才能決定對象到底是 Class1.MethodB 或 Class2.MethodB
```c#
public class Class1
{
public int MethodB(int a)
{
return a + a;
}
}

public class Class2
{
public int MethodB(int a)
{
return a \* a;
}
}
```

#### 一般做法

底下是常用的委派使用方法，在實體化委派時，就同時指明 invoke 的方法名稱，如下列程式碼：
```c#
public delegate int DelegateMethod(int x);

private void btnGeneralInvoke_Click(object sender, EventArgs e)
{
Class1 c1 = new Class1();
Class2 c2 = new Class2();

//實體化委派
DelegateMethod theDelegate = new DelegateMethod(c1.MethodB);

//Invoke
int a = theDelegate(3);
MessageBox.Show(a.ToString());
}
```

#### 使用 Reflection + DynamicInvoke

要達到上述問題的要求，可以使用 [Reflection](http://msdn.microsoft.com/zh-tw/library/f7ykdhsy.aspx) 方法搭配委派的動態呼叫來做。其簡單步驟就是：  

- 使用 [Reflection](http://msdn.microsoft.com/zh-tw/library/f7ykdhsy.aspx) 方法取得組件的方法資訊。
- 使用靜態的 [Delegate.CreateDelegate](http://msdn.microsoft.com/zh-tw/library/system.delegate.createdelegate) 方法，建立委派執行個體。
- 叫用該委派個體的 [DynamicInvoke](http://msdn.microsoft.com/zh-tw/library/system.delegate.dynamicinvoke) 方法，動態 Invoke 指定的方法。

[CreateDelegate](http://msdn.microsoft.com/zh-tw/library/system.delegate.createdelegate) 有多個多載方法，底下是其中幾個：
```c#
public static Delegate CreateDelegate(Type type, MethodInfo method)
public static Delegate CreateDelegate(Type type, object firstArgument, MethodInfo method);
public static Delegate CreateDelegate(Type type, Type target, string method);
```
```c#
private void btnReflectionInvoke_Click(object sender, EventArgs e)
{
//======================================================================================
// 使用 Reflection 方法，動態載入組件，並取得指定的執行方法物件 MethodInfo
//======================================================================================

//動態載入組件
string dll_file = @"D:\MCTS\MCTS2011\Practice\ClassLibrary3\bin\Debug\dll";
Assembly asm = Assembly.LoadFile(dll_file);

//由 UI 決定執行哪一個類別的方法
string className = txtClass.Text;

//由組件取得型別物件
Type MyType = asm.GetType(className);

//由型別物件取得方法物件
MethodInfo MyMethod = MyType.GetMethod("MethodB", new Type[]{typeof(int)});

//======================================================================================
// 委派叫用
//======================================================================================

// 建立指定型別的委派
Delegate theDelegate = Delegate.CreateDelegate(typeof(DelegateMethod), null, MyMethod);

// DynamicInvoke 動態叫用委派的方法
int a = (int) theDelegate.DynamicInvoke(3);
MessageBox.Show(a.ToString());
}
```