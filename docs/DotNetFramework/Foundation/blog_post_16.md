---
title: 泛型委派
layout: default
parent: Foundation
nav_order: 16
description: "泛型委派"
date: 2013-04-17
tags: [DotNetFramework,Foundation]
postid: "861320728067286178"
---
# 自訂委派

若要透過委派執行某個方法，通常就必須先宣告一個委派結構。我們先來回顧一下，如何叫用自訂委派  

## 具名委派
```c#
//宣告一個委派結構
public delegate bool CustomDelegate(Employee emp);

private Boolean IsAdult(Employee emp) 
{ 
return emp.Age >= 20; 
}

private void button1_Click(object sender, EventArgs e)
{
List<Employee> Employees = GetEmployeeList();

//自訂具名委派
CustomDelegate cusDelegate1 = IsAdult;
foreach (Employee emp in Employees)
Console.WriteLine(cusDelegate1(emp));
}
```

## 匿名委派

上面例子中，當建立委派的執行個體時，也就是委派在具現化(Instantiated)時，會與要叫用的方法建立關聯，這做法就是具名委派。  C# 也允許在具現化時，直接使用 Lambda 運算式或匿名方法，將要執行的程式碼區塊直接連接在宣告定義中，這做法就是匿名委派。  所以上面例子，可以改寫成使用以下的匿名委派做法。  
```c#
//宣告一個委派結構
public delegate bool CustomDelegate(Employee emp);

private void button1_Click(object sender, EventArgs e)
{
List<Employee> Employees = GetEmployeeList();

//自訂匿名委派
CustomDelegate cusDelegate2 = emp => emp.Age >= 20;
foreach (Employee emp in Employees)
Console.WriteLine(cusDelegate2(emp));
}
```

# 自訂泛型委派

我們也可以將以上用法，改成使用泛型宣告。  

## 具名委派
```c#
//宣告一個委派結構
public delegate bool CustomGenDelegate<T>(T item);

private bool IsAdult(Employee emp) 
{ 
return emp.Age >= 20; 
}

private void button1_Click(object sender, EventArgs e)
{
List<Employee> Employees = GetEmployeeList();

//自訂泛型具名委派
CustomGenDelegate<Employee> cusgenDelegate1 = IsAdult;
foreach (Employee emp in Employees)
Console.WriteLine(cusgenDelegate1(emp));
}
```

## 匿名委派

上面例子，我們可以改寫成使用匿名委派。
```c#
//宣告一個委派結構
public delegate bool CustomGenDelegate<T>(T item);

private void button1_Click(object sender, EventArgs e)
{
List<Employee> Employees = GetEmployeeList();

//自訂泛型匿名委派
CustomGenDelegate <Employee> cusgenDelegate2 = emp => emp.Age >= 20;
foreach (Employee emp in Employees)
Console.WriteLine(cusgenDelegate2(emp));
}
```

我們再上面例子改寫成連委派的回傳值都是泛型。
```c#
宣告一個泛型委派結構
public delegate R CustomGenericDelegate<in T, out R>(T item);

private void button2_Click(object sender, EventArgs e)
{
List<Employee> Employees = GetEmployeeList();

CustomGenericDelegate<Employee, bool> cusgenDelegate4 = emp => emp.Age >= 20;
foreach (Employee emp in Employees)
Console.WriteLine(cusgenDelegate4(emp));
}
```

# Func委派、Action委派、Predicate委派

.NET 底下的 Func&lt;T&gt; 和 Action&lt;T&gt; 到底是什麼？我們先看看它的宣告：  
```c#
public delegate TResult Func<out TResult>();
public delegate TResult Func<in T, out TResult>(T arg)
public delegate TResult Func<in T1, in T2, out TResult>(T1 arg1, T2 arg2)
public delegate TResult Func<in T1, in T2, in T3, out TResult>(T1 arg1, T2 arg2, T3 arg3)
...
public delegate void Action<in T>(T obj);
public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2);
public delegate void Action<in T1, in T2, in T3>(T1 arg1, T2 arg2, T2 arg3);
...
public delegate bool Predicate<in T>(T obj)
```

比較一下上面宣告中的第二行的 Func 宣告，是不是和前一個例子的自訂泛型委派是一樣的。  沒錯，上面這些長的怪怪的宣告，只不過是 .NET 系統內建的泛型委派結構罷了，沒什麼特別的，以後不要再害怕看到它了。  補充幾點：  

- Func 和 Action 的差異只在於是否有回傳值。
- Predicate 只是 Func 中的一個特例，就是相當於 Func&lt;T, bool&gt; ，也就是只帶一個參數，並回傳 bool 型態。
- .NET 2.0只支援0~1個參數的泛型委派。
- .NET 3.5支援0~4個參數的泛型委派。
- .NET 4.0支援0~16個參數的泛型委派。
- 

因此，若要使用系統內建的泛型委派，上面例子可以改寫如下：  
```c#
List<Employee> Employees = GetEmployeeList();

//使用 Func<T> 泛型委派 ＋ 具名方法
Func<Employee, bool> genDelegate1 = IsAdult;
foreach (Employee emp in Employees)
Console.WriteLine(genDelegate1(emp));

//使用 Func<T> 泛型委派 + 匿名方法
Func<Employee, bool> genDelegate2 = delegate(Employee emp)
{
return emp.Age >= 20;
};

//使用 Func<T> 泛型委派 ＋ 匿名方法 
Func<Employee, bool> genDelegate2 = emp => emp.Age >= 20;
foreach (Employee emp in Employees)
Console.WriteLine(genDelegate2(emp));

//使用 Predicate<T> 泛型委派 ＋ 具名方法
Predicate<Employee> genDelegate3 = IsAdult;
foreach (Employee emp in Employees)
Console.WriteLine(genDelegate3(emp));

//使用 Predicate<T> 泛型委派 ＋ 匿名方法 
Predicate<Employee> genDelegate4 = emp => emp.Age >= 20;
foreach (Employee emp in Employees)
Console.WriteLine(genDelegate4(emp));
```

# 泛型方法

**泛型方法**：帶有泛型參數的方法，如下所示：。  
```c#
public void SwapGen<T>(ref T x, ref T y)
{
T tmp;
tmp = x;
x = y;
y = tmp;
}
```

叫用泛型方法：  
```c#
string x = "12";
string y = "34";
SwapGen<string>(ref x, ref y);
SwapGen(ref x, ref y);              //可省略型別引數，編譯器會自行推斷
```

類別中的泛型方法：  
```c#
public class MyClassA
{
public void SwapGen<T>(ref T x, ref T y)
{
T tmp;
tmp = x;
x = y;
y = tmp;
}
}
```

如果把泛型參數定義在類別上，非泛型方法也可以存取該類別層級中的泛型參數，如下所示：  
```c#
public class MyClassA<T>
{
public void SwapGen(ref T x, ref T y)
{
T tmp;
tmp = x;
x = y;
y = tmp;
}
}
```

# 泛型類別

**泛型類別**：類別定義中，包含非特定型別的封裝。  
```c#
public class MyClassA<T>
{
//T 類別中會使用到的某個型別，這個型別由程式碼在具現化時才決定。
}

MyClassA<string> class1 = new MyClassA<string>();
MyClassA<int> class2 = new MyClassA<int>();
```

泛型類別常應用於與集合（Collection）相關的物件中，例如：stacks, queues, lists 等等。  這類物件的操作方法基本上都是相同的，只是資料項目的型別不同罷了。  

### Dictionary&lt;TKey, TValue&gt; 類別

表示索引鍵和值的集合。
```c#
Dictionary<TKey, TValue>()                          //這個建構子會使用索引鍵型別的預設相等比較子。
Dictionary<TKey, TValue>(Int32)                     //建立具有指定初始容量的集合，這個建構子也是使用索引鍵型別的預設相等比較子。
Dictionary<TKey, TValue>(IEqualityComparer<TKey>)   //這個建構子會使用指定的 IEqualityComparer<T> 當做比較子。
```

### Task 類別 & Task&lt;TResult&gt; 類別

Task&lt;TResult&gt; 表示一個非同步的作業，並且會回傳值。
```c#
1) Task(Action)：使用指定的 Action，初始化新的 Task。 
2) Task(Action<Object>, Object)：使用指定的Action和State，初始化新的 Task。
3) Task(Action, CancellationToken)：使用指定的Action和CancellationToken(取消憑證)，初始化新的 Task。
```
```c#
1) Task<TResult>(Func<TResult>)                        //使用指定的函式，初始化新的 Task<TResult>。
2) Task<TResult>(Func<TResult>, CancellationToken)     //使用指定的函式和取消作業參數，初始化新的 Task<TResult>。
3) Task<TResult>(Func<Object, TResult>, Object)        //使用指定的函式和建立選項，初始化新的 Task<TResult>。
...
```

#### Task&lt;TResult&gt;(Func&lt;TResult&gt;) 什麼意思

- 類別中會使用到一個非特定型別（TResult）。
- 具現化時，必須使用函式名稱當做參數。
- 該函式名稱必須回傳（TResult）型別的值。
```c#
private AtomEntry UploadFile()
{
return UploadFileAsync(gsAlubmId, @"01.jpg"); //return AtomEntry;
}
private void bnTaskResult_Click(object sender, EventArgs e)
{
// Task<TResult>(Func<TResult>) 
Task<AtomEntry> task1 = new Task<AtomEntry>(UploadFile1);
}
```

#### Task&lt;TResult&gt;(Func&lt;Object, TResult&gt;, Object) 什麼意思

- 類別中會使用到一個非特定型別（TResult）。
- 具現化時，必須使用函式名稱當做參數。
- 該函式名稱必須傳入一個 Object 型別參數，並回傳（TResult）型別的值。
```c#
private AtomEntry UploadFile2(Object filename)
{
return UploadFileAsync(gsAlubmId, filename.ToString()); //return AtomEntry;
}
private void bnTaskResult_Click(object sender, EventArgs e)
{
// Task<TResult>(Func<Object, TResult>, Object)
Task<AtomEntry> task2 = new Task<AtomEntry>(UploadFile2, filename);
}
```
## 參考資料  

- [泛型委派 (C# 程式設計手冊)](https://msdn.microsoft.com/zh-tw/library/sx2bwtw7%28v=vs.100%29.aspx)
- [泛型方法 (C# 程式設計手冊)](https://msdn.microsoft.com/zh-tw/library/twcad0zb%28v=vs.100%29.aspx)
- [泛型類別 (C# 程式設計手冊)](https://msdn.microsoft.com/zh-tw/library/sz6zd40f%28v=vs.100%29.aspx)