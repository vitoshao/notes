---
title: 單例模式
layout: default
parent: DesignPattern
nav_order: 2
description: "單例模式"
date: 2016-01-28
tags: [DotNetFramework,DesignPattern]
postid: "3184404040276279060"
---
單例模式（Singleton Pattern）主要的精神是希望某個類別在程式的生命週期期間，被建立的實體只會有一個。  也就是程式不管在什麼地方都可以建立該物件的實體，但是如果該物件的實體已經存在，那麼就直接回傳已經存在的那個實體。  所以這個類別必須提供一個公開的方法讓其他人取得自已的實體。  例如像 ThreadPool、Cache、Registry 等等的這類物件就常會使用到單例模式來設計。  另外像一些管理類型的 Class ，在程式運作時只需要一個，也很適合使用單例模式設計。  

單例模式有點類似靜態物件，主要的差別在於，靜態物件是在程式載入時就會同時被建立起來，而單例模式是必須等到該物件首次被使用到時才會載入（Lazy Instantiaze）。  除此次外，靜態類別無法被繼承，而單例模式可以，也就表示它可以被繼承後再擴充其他的方法。  
```c#
public class MyClass
{
public int test = 1;
private static MyClass _myclass = new MyClass();
// 為了不被外界直接 new 出實體，所以將建構子設定為 private , 
private MyClass()
{
}
// 外界只能使用這個靜態方法取得實例
public static MyClass GetInstance()
{
return _myclass;
}
public void DoSomething()
{
Console.WriteLine(test++);
}
}
static void Main()
{
MyClass mgr1 = MyClass.GetInstance();
mgr1.DoSomething();     //1
mgr1.DoSomething();     //2
MyClass mgr2 = MyClass.GetInstance();
mgr2.DoSomething();     //3
mgr2.DoSomething();     //4
}
```