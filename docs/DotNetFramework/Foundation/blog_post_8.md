---
title: 委派 (Delegate)
layout: default
parent: Foundation
nav_order: 8
description: "委派 (Delegate)"
date: 2012-03-14
tags: [DotNetFramework,Foundation]
postid: "5557767064855991675"
---
# 什麼是委派

## 話說委派

在設計一個類別的時候，我們可能必需在某些情況下去執行某些事情，但又不想／無法在類別裡寫死處理的方法，這時就要用到委派。  我們僅在類別裡定義一個特定的簽名（signature），指出要執行的方法的樣子（樣子指的就參數列與回傳值的型別），至於該方法叫什麼名字，內容如何，則由呼叫端於呼叫時自行決定。  

舉個例好了：  
假設你在設計一個警報器，你在這個類別上加上了一個＂警鈴響了＂事件，事件發生時，這個類別啟動自動處理模式，例如，撥打119或撥打110或通知老闆...。  但是你在設計時，根本不知道使用者要如何處理，所以這部分就可以留待使用者自行撰寫處理方法。  

也就是說，在設計類別時，可能會碰到某個方法在執行時需要額外處理，但是你無法將這部分的處理寫死在類別裡，此時就得將這個部分「外包」給呼叫端自行處理。  而客戶端必須事先提供一個處理函式，等到類別的方法要執行的時候，就會回頭去呼叫（callback）那個客戶端事先指定的處理函式。這個處理函式就稱為「委派方法」。  

從類別設計者的角度來說，這種設計方式可將那些變化不定的繁瑣細節從類別中移出去，使類別保持乾淨、穩定。  從呼叫端的角度來看，當你在使用某個類別時，該類別已經設計好一種模式，在你呼叫某個方法之前，它會要求你先提供一個符合特定簽名（signature；即參數與傳回值）的方法，才能達成你想要執行的工作。  

這樣子的模式就有點類似C/C++ 的函式指標（function pointer），程式碼回到呼叫端的處理函式執行後，再回到自已的程序。  與C/C++ 函式指標不同的是，delegate 具備 type-safe、secure managed 的特性，以確保 delegate 指向的函式必定存在。若在 delegate 尚未存放任何方法即進行呼叫，則 CLR 會拋出一個例外狀況。  

更精準來說：delegate 是 C# 的特殊型別，用來定義方法（method/function）的 signature，delegate 的實體（instance）可以存放一或多個符合該 signature 的方法。  

講白一點：delegate 是設計一個機制，這個機制要讓程式碼可以去執行某個方法，但是方法名稱還無法得知，只知道方法的參數型別，和回傳參數的型別。

## 委派的一些特性

- delegate 是一種參考型別，它可以 encapsulate 一個或多個方法。
- delegate 的宣告和方法簽章類似。它有回傳值和參數。
- delegate 在 CLR 中，扮演事件的底層機制。(function pointer)
- delegate 可用來封裝具名方法或匿名方法。
- delegate 大致類似 C++ 的函式指標，但是，delegate 是型別安全的。
- 將委派與具名方法或匿名方法建立關聯，即可實體化 (Instantiated) 委派。
- 實體化委派的那個方法，必須和委派的宣告有相同的簽名，也就是相同回傳型別和相同的參數列。
- 若要搭配匿名方法使用，就要同時宣告委派以及其相關聯的程式碼。

# 委派的宣告方法

宣告委派和定義 method 的樣式很相似，它包含一個回傳值和任意數量的參數。  所以委派宣告最主要工作就是要明確定義**回傳型別**和**參數型別**。  
```c#
[modifier] delegate 回傳值型別 delegateName ( [參數列] )
```
```c#
public delegate void Testdelegate3(object sender, EventArgs e);
public delegate void Testdelegate1(string message);
public delegate int  Testdelegate2(MyType m, long num);
public delegate void Testdelegate3(object sender, EventArgs e);
```

# 委派的使用方法

要使用委派，要先實體化一個委派（instantiated），通常會使用到要封裝的方法名稱，要不就是使用匿名方法建構。  
只要叫用（invoke）委派執行個體執行，委派就會呼叫封裝的方法。  
若呼叫端有傳遞參數給委派，委派也會 pass 參數給封裝的方法。  
若封裝的方法有回傳值，委派也會將回傳值送給呼叫端。  

## 實體化委派
```c#
delegateName var = new delegateName( FunctionName ) ;       //C#1.0 用法
delegateName var = FunctionName ;                           //C#2.0 用法
```
```c#
// Define a delegate
public delegate void Mydelegate(string message);

// Instantiate the delegate.
Mydelegate handler1 = new Mydelegate(DoWork);   //實體化一個委派，這是C#1.0的寫法
Mydelegate handler2 = DoWork;　　　             //這是C#2.0精簡化的寫法，意思與上一行一模一樣

// Call the delegate.
handler1.Invoke("Hello World");      //看似是對委派呼叫，但實際上，委派會去呼叫 DoWork 方法，並 pass 參數"Hello World"給該方法
handler1("Hello World");　　　　     //這是C#2.0精簡化的寫法,同上
```

## 使用具名方法呼叫

前面提到過，委派方法要和委派具有相同的簽名。此外，使用具名方法呼叫還可以做到：

- 可以同時封裝多個方法（[Multicastdelegate](http://msdn.microsoft.com/zh-tw/library/system.multicastdelegate.aspx)）。
- 可以封裝**執行個體的方式**或是**靜態的方法**。

#### 範例說明：

1.範例(1)-(3)都是封裝執行個體的方式  
2.範例(4)是封裝靜態的方式  
3.範例(3)：多重委派 (Multidelegate)，委派封裝的方法可以多個，也可以重複。  
```c#
delegate void Mydelegate(int x, int y);     // 定義委派型別

class Program
{
static void Main(string[] args)
{
MathClass1 mc1 = new MathClass1();
MathClass2 mc2 = new MathClass2();

//===========================================
//ex:1) Invoke the delegate object (   MapTo: mc1.NumberAdd )
//===========================================

Mydelegate myDlegate1 = new Mydelegate(mc1.NumberAdd);      //步驟2: 建立委派物件
for (int i = 1; i <= 3; i++)
{
myDlegate1.Invoke(i, i);                                // callback NumberAdd(i, i);
}
// output : 2 4 6

//===========================================
//ex:2) Invoke the delegate object (  MapTo: mc2.NumberAdd )
//===========================================

Mydelegate myDlegate2 = mc2.NumberAdd;                      //步驟2: 建立委派物件  (這是C#2.0精簡的寫法)
for (int i = 1; i <= 3; i++)
{
myDlegate2(i, i);                                       //C#2.0精簡寫法，省略了 .Invoke 
}
// output : 4 8 12

//===========================================
//ex:3) Invoke the delegate object  (  MapTo: mc1.NumberAdd  and mc2.NumberAdd )
//===========================================

Mydelegate myDlegate3 = null;    // 委派變數在使用前必須先初始化　
myDlegate3 += mc1.NumberAdd;     // 委派可以封裝一個或多個方法
myDlegate3 += mc2.NumberAdd;     // 委派封裝的方法，是不用管它所參考的物件類別，只要該方法的 引數型別 和 傳回型別 與委派的型別相符即可。(就是簽名signature相同）
myDlegate3 += mc1.NumberAdd;     // 若委派的引動過程清單中包含一個以上的項目，稱為多點傳送的委派(Multicastdelegate)。
for (int i = 1; i <= 3; i++)
{
myDlegate3.Invoke(i, i);   
}
// output : 2 4 2   4 8 4   6 12 6

//===========================================
//ex:4) Invoke the delegate object  (  MapTo STATIC method :  MathClass1.NumberMinus )
//===========================================

Mydelegate myDlegate4 = MathClass1.NumberMinus;   
for (int i = 1; i <= 3; i++)
{
myDlegate4(i, i);
}
// output : 0 0 0

}
}

public class MathClass1
{
public void NumberAdd(int m, int n)
{
int result = (m + n) \* 1;
Console.Write(result.ToString() + " ");
}

public static void NumberMinus(int m, int n)
{
int result = (m - n) \* 1;
Console.Write(result.ToString() + " ");
}

}

public class MathClass2
{
public void NumberAdd(int m, int n)
{
int result = (m + n) \* 2;
Console.Write(result.ToString() + " ");
}
}
```

## 使用匿名方法呼叫

在 C# 2.0 以前的版本中，宣告委派的唯一方式是使用具名方法。  C# 2.0 引進了匿名方法 (Anonymous Method)，在 C# 3.0 (含) 以後版本，則以 Lambda 運算式取代匿名方法來做為撰寫內嵌 (Inline) 程式碼的慣用方式。   匿名方法可以讓您省略參數清單，而這表示匿名方法可以轉換為具有各種簽章的委派。  

在使用匿名方法時，就不需要定義那個方法名稱，所以就不須撰寫實體化這一段程式碼，直接把那個方法要做的事情內嵌到委派宣告的下方。  
```c#
//===========================================
//ex:5) Invoke the delegate object  (  使用匿名方法 )
//===========================================
//使用匿名方法時，將原本要執行的程式碼內嵌進來，就不需像上面例子還得建立一個獨立的執行方法
//注意{}結尾有一個；
Mydelegate myDlegate5 = delegate(int m, int n)
{   
int result = (m + n) \* 1;
Console.Write(result.ToString() + " ");
};  
for (int i = 1; i <= 3; i++)
{
myDlegate5(i, i);
}
// output : 2 4 6
```

## 具名方法 VS. 匿名方法
```c#
//這個範例示範，分別使用具名方法與匿名方法，撰寫 button1.click 要執行的程式碼
public Form1()
{
InitializeComponent();

//使用具名方法
button1.Click += new System.EventHandler(button1_Click);

//使用匿名方法，將要執行的程式碼，內嵌進來，就不需要像上面例子還得建立個別的執行方法
button2.Click += delegate(object o, EventArgs e)
{
MessageBox.Show("hello");
};
}

private void button1_Click(object sender, EventArgs e)
{
MessageBox.Show("hello");
}
```
```c#
//================================
//當需要建立似乎不太有必要的方法時，就可以指定程式碼區塊來替代委派 (匿名方法)
//啟動新執行緒時就是個好例子
//================================
private void button3_Click(object sender, EventArgs e)
{
//方法1) 透過具名方法，讓委派啟動新執緒要做的事
ThreadStart thread_start = new ThreadStart(SimpleWork);  //ThreadStart
Thread thread1 = new Thread(thread_start);
thread1.Start();

//方法2) 透過匿名方法，讓委派啟動新執緒要做的事
Thread thread2 = new Thread(
delegate()
{
Console.WriteLine("Thread: {0} Start: {1}", Thread.CurrentThread.ManagedThreadId, System.DateTime.Now.ToString());
Thread.Sleep(3000);
}
);
thread2.Start();
}

static void SimpleWork()
{
Console.WriteLine("Thread: {0} Start: {1}", Thread.CurrentThread.ManagedThreadId, System.DateTime.Now.ToString());
Thread.Sleep(3000);
}
```

## C# 3.0 的寫法

[「Lambda 運算式」(Lambda Expression)](http://msdn.microsoft.com/zh-tw/library/bb397687.aspx) 是一種匿名函式，它可以包含運算式和陳述式 (Statement)，而且可以用來建立委派 (delegate) 或運算式樹狀架構型別。

Lambda 運算子「 =&gt; 」，這個符號的意思是「移至(goes to)」；或者可以解讀成：「把左邊的參數傳入右邊的匿名方法」
```c#
//===========================================
//ex:6) Invoke the delegate object (  使用 C# 3.0 新增加的寫法 )
//===========================================

Mydelegate myDlegate6 = (int m, int n) =>
{
int result = (m + n) \* 3;
Console.Write(result.ToString() + " ");
};
for (int i = 1; i <= 3; i++)
{
myDlegate6(i, i);
}
// output : 6 12 18   
```
```c#
//================================
//delegate各種不同寫法的例子
//下面四種程式碼寫法,執行結果都相同
//================================
delegate int Mydelegate(int i);
private void button4_Click(object sender, EventArgs e)
{
//C#1.0 寫法
Mydelegate mydelegate1 = new Mydelegate(DoWork);
int y1 = mydelegate1.Invoke(5);

//C#2.0 具名寫法
Mydelegate mydelegate2 = DoWork;
int y2 = mydelegate1(5);

//C#2.0 匿名寫法
Mydelegate mydelegate3 = delegate(int x) { return x \* x; };
int y3 = mydelegate1(5);  

//C#3.0 Lambda 寫法
Mydelegate mydelegate4 = x => x \* x;   
int y4 = mydelegate4(5);  
}

static int DoWork(int x)
{
return x \* x;
}
```

## 搞懂了沒：委派
```c#
delegate double DE(double i);
private double DoWork(double i, DE de)
{
return de(i + i);
}
private double Square(double i)
{
return i \* i;
}
private void button1_Click(object sender, EventArgs e)
{
DE de = i => i \* 2;
int x = (int) DoWork(de(25), Square);
int y = (int) DoWork(de(25), Math.Log10);
}

//x ?= 50 100 625 2500 10000(\*)
//y ?= 2
```

# 委派的進階使用方法

## Multicastdelegate

[Multicastdelegate](http://msdn.microsoft.com/zh-tw/library/1kswf507)  ，委派可以同時封裝多個方法，也就是它的引動過程清單中可以包含一個以上的項目。  所以，若我們 Invoke 一個含有多個項目的引動清單，則委派就依引動清單的順序，執行委派方法。如上面例子所示範的。  

該例子因為委派方法沒有回傳值，Invoke 之後，就會依序執行所有的委派方法。  但是如果委派方法有回傳值，也如此直接 Invoke 的話，就只能取得最後一次的回傳值。如下面這個範例：  
```c#
public class MathClass
{
public int Add(int m, int n)
{
return (m + n);
}
public int Sum(int m, int n)
{
return (m - n);
}
public int Mul(int m, int n)
{
return (m \* n);
}
public int Div(int m, int n)
{
return (m / n);
}
}

delegate int MathHandler(int i, int j);

private void button12_Click(object sender, EventArgs e)
{
MathClass mathclass = new MathClass();
MathHandler handler = null;

handler += mathclass.Add;
handler += mathclass.Sum;
handler += mathclass.Mul;

int result = 0;
result = result + handler.Invoke(4, 5);  //直接 invoke ，只會取得最後一次委派方法的值

Console.Write(result); //20
}
```

所以，如果委派方法有回傳值，就必須利用 [GetInvocationList](http://msdn.microsoft.com/zh-tw/library/system.delegate.getinvocationlist.aspx) 方法，取得委派的引動清單後，再分開 invoke 每一個方法，這樣才可以取得每一次的回傳值。如下面這個範例：  
```c#
private void button13_Click(object sender, EventArgs e)
{
MathClass mathclass = new MathClass();
MathHandler handler = null;

handler += mathclass.Add;
handler += mathclass.Sum;
handler += mathclass.Mul;

int result = 0;
foreach (MathHandler math in handler.GetInvocationList())
{
result = result + math.Invoke(4, 5);
}
Console.Write(result); //28
}
```

## delegate.DynamicInvoke

[DynamicInvoke](http://msdn.microsoft.com/zh-tw/library/system.delegate.dynamicinvoke) 是動態叫用委派的方法，它是屬於晚期繫結 (late-bound) 的一種做法。  例如，委派的方法必須在執行階段才知道的話，就可以透過 reflection 物件取得組件的執行方法，  再使用靜態的 [delegate.Createdelegate](http://msdn.microsoft.com/zh-tw/library/system.delegate.createdelegate) 方法，建立委派執行個體。  再使用 [DynamicInvoke](http://msdn.microsoft.com/zh-tw/library/system.delegate.dynamicinvoke) 以叫用委派方法。  
```c#
private void button14_Click(object sender, EventArgs e)
{
//======================================================================================
// 使用 Reflection 方法，動態載入組件，並取得指定的執行方法物件 MethodInfo
//======================================================================================

//透過 reflection 取得組件的執行方法
string className = "PraceticeBlog.DotNet.MathClass";
string methodName = (rbMul.Checked ? "Mul" : "Div");
int n1 = int.Parse(txtN1.Text);
int n2 = int.Parse(txtN2.Text);

//取得組件
Assembly asm = Assembly.GetExecutingAssembly();
//由組件取得型別物件
Type MyType = asm.GetType(className);
//由型別取得方法物件
MethodInfo method = MyType.GetMethod(methodName, new Type[] { typeof(int), typeof(int) });

//======================================================================================
// 委派叫用
//======================================================================================

// 建立指定型別的委派
delegate thedelegate = delegate.Createdelegate(typeof(MathHandler),null, method);

// DynamicInvoke 動態叫用委派的方法
int result = (int)thedelegate.DynamicInvoke(n1, n2);
Console.Write(result);          
}
```


- [使用具名和匿名方法委派的比較 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/98dc08ac.aspx)
- [匿名方法 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/0yw3tz5k.aspx)
- [Lambda 運算式 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/bb397687.aspx)