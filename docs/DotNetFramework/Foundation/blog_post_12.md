---
title: 委派的演變
layout: default
parent: Foundation
nav_order: 12
description: "委派的演變"
date: 2012-05-18
tags: [DotNetFramework,Foundation]
postid: "301316201271123360"
---
委派是一種定義方法簽章的型別，隨者 C# 版本的演變，它的使用方法也有些許的變化。  例如：具名方法，匿名方法，Lambda表示法。  

## 委派宣告

不同的 C# 版本，委派的宣告方法還是一樣的，不同的是委派的使用方法。
```c#
存取型態 delegate 回傳值型別 delegateName ( [參數列] )
```

## 使用具名委派
```c#
// 定義一個委派
public delegate bool MyDelegate(Employee e);

// 定義一個方法，找出大於40歲的人
private Boolean IsElder40(Employee emp)
{
return emp.Age > 40;
}

public void button2_Click(object sender, EventArgs e)
{
List<Employee> empList =  new List<Employee>(){
new Employee{Name="apple21",Age=21},
new Employee{Name="dean51", Age=51},
new Employee{Name="carry41",Age=41},
new Employee{Name="book31", Age=31}
};

//實體化一個委派，並具名的指出要執行的方法
MyDelegate myDelegate = new MyDelegate(IsElder40);

//上一行也可以這麼寫
//MyDelegate myDelegate = IsElder40;

//==================================================================
//將List中的每一個成員，都送給委派所指定的方法去執行，以驗證結果
//==================================================================
foreach (Employee emp in empList)                   
{
//Invoke 委派
bool result = myDelegate.Invoke(emp);

//上一行也可以這麼寫
//bool result = myDelegate(emp);

Console.WriteLine(result);      // F T T F
}

//==================================================================
//List.FindAll(Predicate<T>)
//FindAll的參數是一個系統內部定義的泛型委派 Predicate<T> ，
//Predicate<T> 要求傳入一個物件，會回傳一個 bool，剛好我們的 IsElder40 符合這個要求。
//==================================================================
List<Employee> empFindResult = empList.FindAll(IsElder40);
foreach (Employee emp in empFindResult)             // dean51 carry41
Console.WriteLine(emp.Name);
}
```

上面這個例子，List&lt;T&gt; 的 FindAll 這個方法，接受一個 [Predicate&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/bfcke1bz.aspx) 的泛型委派，所以可以傳入我們定義的 IsElder40 這個方法。

## 委派的演變
```c#
//定義一個委派
public delegate bool MyDelegate(Employee e);

Employee emp1 = new Employee("vito", 25);

//==================================================================================
// 1.0) 具名委派
//==================================================================================
MyDelegate d1 = new MyDelegate(IsElder40);
d1(emp1);

//==================================================================================
// 2.0) 匿名委派
//==================================================================================
MyDelegate d2 = delegate(Employee emp)
{
return emp.Age > 40;
};
d2(emp1);

//==================================================================================
// 3.0) 匿名委派 轉 Lambda
//==================================================================================
MyDelegate d3 = (Employee emp) =>
{
return emp.Age > 40;
};
d3(emp1);

// 因為只有一行，可以去掉大括弧和 return
MyDelegate d4 = (Employee emp) =>
emp.Age > 40;
d4(emp1);

// 符合泛型委派 Predicate<T> 的定義，所以就用 Predicate<T> 
Predicate<Employee> d5 = (Employee emp) => 
emp.Age > 40;
d5(emp1);

// 因為參數列只有一個，編譯器可從委派定義得知型別，所以可以把小括弧和型別名稱都去掉
Predicate<Employee> d6 = emp =>
emp.Age > 40;
d6(emp1);

// 最後再精簡一下變數符號，就成底下這樣了
// 所以 p 就是一個泛型委派的實體
Predicate<Employee> p = e => e.Age > 40;
```

## 從 Lambda 表示式到 LINQ
```c#
List<Employee> empList = new List<Employee>(){
new Employee{Name="apple21",Age=21},
new Employee{Name="dean51",Age=51},
new Employee{Name="carry41",Age=41},
new Employee{Name="book31",Age=31}
};

//使用 Lambda 表示式，定義一個泛型委派
Predicate<Employee> p1 = e => e.Age > 40;

//找第一個符合委派方法的項目
Employee emp1 = empList.Find(p1);

//若只要叫用一次，換成這樣寫，可以比較彈性了
Employee emp2 = empList.Find(e => e.Age > 40);

//也可以變換判斷規則
Employee emp3 = empList.Find(e => e.Age > 50);                      //回傳第一個大於50歲的人 
Employee emp4 = empList.Find(e => e.Name.Contains("a"));            //回傳第一個名字中含a的人

//==================================================================================
//使用 List<T> 的 FindAll 方法查詢所有符合條件的資料，回傳型別是 List<T>，接受參數是泛型委派 Predicate<T> 
//==================================================================================
List<Employee> result1 = empList.FindAll(e => e.Name.Contains("a"));

//==================================================================================
//改用 List<T> 的 Where 這個擴充方法查詢資料，回傳型別是 IEnumerable<T>，但接受參數是另一個泛型委派 Func<T, R> 
//==================================================================================
IEnumerable<Employee> result2 = empList.Where<Employee>(e => e.Name.Contains("a"));

//==================================================================================
//搭配 OrderBy , Select 等擴充方法，這二個方法也都接 Func<T, R> 型別的參數，最後 Select 回傳型別是 IEnumerable<string>
//==================================================================================
IEnumerable<string> result3 = empList
.Where<Employee>(e => e.Name.Contains("a"))
.OrderBy<Employee,int>(e => e.Age)
.Select<Employee, string>(e => e.Name);

//改用 LINQ 表示式來對照，

IEnumerable<string> result4 = from e in empList
where e.Name.Contains("a")
orderby e.Age
select e.Name;

//為方便起見，回傳型別宣告成 var 隱含型別。

var names = from e in empList
where e.Name.Contains("a")
orderby e.Age
select e.Name;
```


- [C# 筆記：重訪委派－從 C# 1.0 到 2.0 到 3.0](http://huan-lin.blogspot.com/2009/01/delegate-revisited-csharp-1-to-2-to-3.html)
- [C# 筆記：從 Lambda 表示式到 LINQ](http://huan-lin.blogspot.com/2009/01/from-lambda-to-linq.html)
- [擴充方法 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/bb383977.aspx)
- [Enumerable 方法](http://msdn.microsoft.com/zh-tw/library/bb341635)
- [泛型委派](http://msdn.microsoft.com/zh-tw/library/sx2bwtw7)
- [Func&lt;t, tresult&gt; 委派&lt;/t,&gt;](http://msdn.microsoft.com/zh-tw/library/bb549151)