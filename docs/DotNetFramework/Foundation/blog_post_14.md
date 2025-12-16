---
title: base 用法
layout: default
parent: Foundation
nav_order: 14
description: "base 用法"
date: 2012-11-20
tags: [DotNetFramework,Foundation]
postid: "2433422080217092043"
---
# : base(...)

[base](http://msdn.microsoft.com/zh-tw/library/hfw7t1ce.aspx) 關鍵字是衍生類別用來存取父類別的成員，它有以下二個情況會使用到：  

## 1. 呼叫基底類別中的方法

在衍生類別中，若要執行基底類別中的方法，就要使用 base 關鍵字去叫用基底類別的方法。  
```c#
public class Person
{
protected string ssn = "444-55-6666";
protected string name = "John L. Malgraine";

public virtual void GetInfo()
{
Console.WriteLine("Name: {0}", name);
Console.WriteLine("SSN: {0}", ssn);
}
}
class Employee : Person
{
public string id = "ABC567EFG";
public override void GetInfo()
{
// 呼叫父類別的 GetInfo 方法:
base.GetInfo();
Console.WriteLine("Employee ID: {0}", id);
}
}

private void frmBase_Load(object sender, EventArgs e)
{
Employee emp = new Employee();
emp.GetInfo();
//Name: John L. Malgraine
//SSN: 444-55-6666
//Employee ID: ABC567EFG
}
```

## 2. 呼叫基底類別的建構函式

另一種狀況是，若 derived-class 的 constructor 的參數，實際上是 base-clas s的資料，這時候就可以使用 base 關鍵字去呼叫 base-class 的 constructor。  

因為在物件的繼承行為中，建構函式是不會被繼承的，所以若想在衍生類別的建構函式中，直接利用基底類別建構函式中的程式碼，就會遇到這種狀況。  
```c#
public class Person
{
public Person(string name)
{
Console.WriteLine("My name is " + name);
}
}
public class Student : Person
{
public Student(string name, int age) 
: base(name)
{
Console.WriteLine("I am " + age.ToString() + " years old");
}
}
```

# : this(...)

若一個類別中有多個 constructor ，如果這些建構函式中有些程式可以共用，你就可以利用 : this() 語法。  也是說在同一個類別中叫用另一個建構函式。  
```c#
public class Person
{
public Person(string name)
{
Console.WriteLine("My name is " + name);
}
public Person(string name, int age) : this(name)
{
Console.WriteLine("I am " + age.ToString());
}
public Person(string name, int age, string city)
: this(name, age)
{
Console.WriteLine("I live in " + city);
}
}
private void button1_Click(object sender, EventArgs e)
{
Person person = new Person("vito", 20, "taipei");
}
```

在過去 C#3.5 還不支援選則性參數(optional parameter)時，就常使用這個方法來模擬選則性參數的功能。  
```c#
public class Person
{
public Person(string name)
: this(name, 20, "taipei")
{
Console.WriteLine("My name is " + name);
}
public Person(string name, int age) 
: this(name, age, "taipei")
{
Console.WriteLine("I am " + age.ToString());
}
public Person(string name, int age, string city)
{
Console.WriteLine("I live in " + city);
}
}
```