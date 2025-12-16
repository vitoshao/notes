---
title: 類別
layout: default
parent: Foundation
nav_order: 2
description: "類別"
date: 2011-12-16
tags: [DotNetFramework,Foundation]
postid: "8088638491339682656"
---
在物件導向語言中，大部分的工作都是透過物件來執行。  即使是一個最簡單的應用程式也需要建立一個或多個自訂類別，  然後透過這些類別的屬性(peoperty)和方法(method)，去執行與該物件相關的工作。  

物件導向有三個主要特性：封裝、繼承、多型。（encapsulation, inheritance, polymorphism）

- 封裝：類別可以封裝隱藏物件的屬性，必須透過該類別的執行個體及其公開的方法，外界才能存取這些屬性。
- 繼承：該父層類別公開屬性及方法可以延續到子層類別。
- 多型：因為繼承關係，讓不同的物件可以對特定的方法重新定義，就導至相同功能，在不同的物件中會有不同的表現。

# 繼承 (Inheritance)

[繼承](http://msdn.microsoft.com/zh-tw/library/aa645623.aspx) 簡單講就利用現有類別建立新的類別，讓新的類別能夠延用現有類別中的公開屬性與方法。  

繼承有幾個特性，假設 ABC 為爺父孫三層類別，那麼：

1. C 將繼承 B 的成員，也將繼承 A 的成員。
2. 繼承會繼承所有成員，除了**建構函式**、**解構函式**和**靜態建構函式**以外。
3. 私有成員也會被繼承，只是無法存取。
4. 已繼承的成員不能移除。不過，子類別可以籍由使用一個相同名稱的成員，來隱藏父層的成員。
5. 衍生類別可以視為一個基底類別，所以 C 可以說是一個 B ，也可以說是一個 A 。
```c#
public class cPerson
{
public string name;
private int age;

public int Age { get; set; }

public cPerson(){}
public cPerson(string _name, int _age)
{
name = _name;
age = _age;
}

public void Growing() { }
public void Learning() {
Console.Write("Person Learning");
}
private void Political() { }
public virtual void Marital() {
Console.Write("Person Marital");
}
}
public class cStudent : cPerson
{
public cStudent(string _name, int _age)
{
name = _name;
Age = _age;
}
// 覆寫 Object 類別的 ToString 
public override string ToString(){
return name.ToString() + " age:" + Age.ToString();
}
// 隱藏父類別的 Learning 方法
public new void Learning(){
Console.Write("Student Learning");
}
// 覆寫父類別的 Marital 方法
public override void Marital(){
Console.Write("Student Marital");
}
}

private void button1_Click(object sender, EventArgs e)
{
cPerson p1 = new cPerson("vito", 30);
cStudent s1 = new cStudent("vito", 31);

cPerson p2 = new cStudent("vito", 31);
//cStudent s2 = new cPerson("vito", 32); 不允許

p1.Learning();      //Person Learning
p1.Marital();       //Person Marital

// new 和 override 在此看起來結果很像,但意義是不同的.
// Student 類別中的 Learning 方法，是使用一個相同的名稱來隱藏父類別的方法
// Student 類別中的 Marital 方法，是覆寫父類別的方法
s1.Learning();      //Student Learning
s1.Marital();       //Student Marital

// p2 雖然由 new Student 產生，但是其實會「隱含轉換」成 Person 類別
// 但是 Person.Learning() 和 Student.Learning() 本來就是不相同的方法，所以叫用 p2.Learning() 輸出的是 Person Learning
// 而 Student Marital() 會覆寫 Person.Marital() ，所以 p2.Marital() 輸出的是 Student Learning
p2.Learning();      //Person Learning
p2.Marital();       //Student Marital
}
```

## 繼承修飾詞

以下是類別的繼承修飾詞：

| VB.NET | C# | 說明 |
| --- | --- | --- |
| Inherits | public | 可繼承類別 |
| NotInheritable | [sealed](http://msdn.microsoft.com/zh-tw/library/88c54tsw.aspx) | 不可繼承類別 |
| MustInherit | [abstract](http://msdn.microsoft.com/zh-tw/library/sf985hc5.aspx) | 必須繼承類別 |

- [abstract](http://msdn.microsoft.com/zh-tw/library/sf985hc5.aspx) 關鍵字可讓您建立類別和類別成員，這些類別和成員並不完整，因此必須在衍生類別 (Derived Class) 中實作。
- [sealed](http://msdn.microsoft.com/zh-tw/library/88c54tsw.aspx) 關鍵字可讓您避免繼承先前標記為 [virtual](http://msdn.microsoft.com/zh-tw/library/9fkccyh4.aspx) 的類別或特定類別成員。

## 抽象基底類別

如果不希望類別可以使用 new 關鍵字被具現化 (Instantiation)，則必須將類別宣告為抽象類別。   

抽象類別沒有要求成員一定要是抽象的，但是若類別中有抽象成員，則類別本身必須宣告為抽象。

# Interface

介面就像一紙合約(contracts)，給將來要實作該介面的類別先定義好方法名稱。

一個類別可以同時實作多個介面。

若設計好一個類別後，才想到還有其他類別也須要有相同的成員，可以利用VS提供的重構功能，將方法名稱抽離到一個獨立的介面。

- 可視為沒有construct的自訂類別。
- 類別中僅定義member名稱，沒有內容。
- 實作這個介面的類別，必需支援該介面的所有成員。
```c#
interface IMessage
{
string Message { get; set; }
string Address { get; set; }

bool Send();
}

class EmailMessage : IMessage
{
public string Message { get; set; }
public string Address { get; set; }

public bool Send()
{
throw new NotImplementedException();
}
}
```

# Partial Classes

將一個類別定義，分散到不同檔案，如此可以讓衍生類別只著重在一些比較重要的部分。

#### 補充：

|  | class | sealed class | abstract class | static class |
| --- | --- | --- | --- | --- |
| 可否被繼承 | Ｖ |  | Ｖ |  |
| 可否實體化 | Ｖ | Ｖ |  |  |

# Generics

## 泛型是什麼：

#### 簡單說明：

[泛型(Generics)](http://msdn.microsoft.com/zh-tw/library/512aeb7t.aspx)一種型別參數化的自訂型別，也就是定義類別時，不需指定參數型別，當程式碼要使用該類別時才指定型別。

#### 使用時機：

當程式中需要對不同的資料型別執行相同的功能時。

- 重複使用：不須為每一種型別定義一個類別。
- 減少錯誤(Reduced run-time errors)：編輯時期就可以發現資料型別上的錯誤。
- 增加效率(Improved performance)：減少不必要的Boxing/Unboxing問題。

#### 泛型宣告：
```c#
Class GenClass<T,U>
{
T V1;
U V2;
Public GenClass(T _V1, U _V2)
{
V1=_V1;
V2=_V2;
}
}
```

#### 範例一：自訂泛型類別
```c#
class genClass<T,U>
{
public T first;
public U second;

public genClass(T _a1, U _a2)
{
first = _a1;
second = _a2;
}
}

genClass<string, string> g1 = new genClass<string, string>("vito", "shao");
Console.Write(g1.first + g1.second);

genClass<int, int> g2 = new genClass<int, int>(3, 4);
Console.Write(g2.first + g2.second);
```

#### 範例二：自訂泛型堆疊
```c#
public class GenStack<T>    //T is the generic type parameter (or type parameter) 
{
object[] m_Items;
int iPointer = 0;  //stack pointer
int m_Size = 0;    //max size

public GenStack() : this(100) { }
public GenStack(int size)
{
m_Size = size;
m_Items = new object[m_Size];
}

public void Push(T item)
{
if (iPointer >= m_Size)
throw new StackOverflowException();
else
m_Items[iPointer++] = item;
}
public object Pop()
{
if (iPointer > 0)
{
return m_Items[--iPointer];
}
else
{
throw new InvalidOperationException("Cannot pop an empty stack");
}
}
}

//利用Generic可充份解決
//1:效率,因為不必執行boxing,unboxing的操作
//2:安全,編譯時,即可檢驗型別上的錯誤。
GenStack<int> gStack = new GenStack<int>();
gStack.Push(1);
gStack.Push(2);
```

## 型別參數的條件約束 （Constrains）

參考[http://msdn.microsoft.com/zh-tw/library/d5x73970.aspx](http://msdn.microsoft.com/zh-tw/library/d5x73970.aspx)

由上面的介紹，我們已經知道，泛型的設計，可以該用戶端在使用泛型類別時才自行決定型別。  但是，若我們設計了一個泛型，但是卻必須限制用戶端可使用的型別，這就是泛型的條件約束。  

之所以要這麼做的目的是因為，在類別設計之中，可能會使用一些比較方法或邏輯運算，  但是這些判斷式，並不一定適用於所有型別，所以利用條件約束加以限制。  泛型支援六種條件約束：  

1. **where T : struct**：型別引數必須是「實值型別」。
2. **where T : class**：型別引數必須是「參考型別」。
3. **where T : new()**：型別引數必須具有無參數的建構函式。
4. **where T : &lt;interface name&gt;**：型別引數必須有實作指定介面。
5. **where T : &lt;base class name&gt;**：型別引數必須是指定類別，或該類別的衍生類別。
6. **where T : U**：型別引數必須是 (或衍生自) 提供給 U 的引數。

#### 1. 使用實值型別條件約束

使用 **struct** 條件約束，表示傳遞的型別參數必須是「實值型別」。
```c#
public struct Nullable<T> where T : struct
```

#### 2. 使用參考型別條件約束

使用 **class** 條件約束，表示傳遞的型別參數必須是「參考型別」。
```c#
class MyClass<T, U>
where T : class
where U : struct
{}
```

#### 3. 使用 new() 條件約束

- 使用 new() 條件約束指定在泛用類別宣告中的任何型別參數，都**必須具有**公用的**無參數建構函式**。若要使用 new 條件約束，則型別**不可為抽象**。
- 若將 new() 條件約束與其他條件約束一起使用時，它必須擺放在最後一個。
```c#
class ItemFactory<T>
where T : new()             //new() : 限制使用的型別，必須具有無參數且公開的建構函式">
{
public T GetNewItem()
{
return new T();
}
}

public class TestA
{
public TestA()
{
}
public TestA(int a)
{
}
}

private void button4_Click(object sender, EventArgs e)
{
ItemFactory<TestA> a = new ItemFactory<TestA>();
}
```

#### 4. 使用 Interface 條件約束

下面這個例子，因為 CompGen 類別裡面，要使用到 [CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 這個方法，所以我們必須使用 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 這個介面當做約束條件，以確保 T 的型別，都有實作 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 。  
```c#
public class CompGen<T> where T : IComparable
{
public T t1;
public T t2;
public CompGen(T _t1, T _t2)
{
t1 = _t1;
t2 = _t2;
}
public T Max()
{
if (t2.CompareTo(t1) < 0)
return t1;
else
return t2;
}
}

private void button15_Click(object sender, EventArgs e)
{
//由於 int 型別有實作 IComparable ，所以沒問題
CompGen<int> a = new CompGen<int>(3,5);
Console.Write(a.Max());     //5

//由於 People 型別繼承了 IComparable 介面，卻沒有實作 CompareTo 方法，所以會編譯失敗
CompGen<People> emp = new CompGen<People>(new People("vito", 1), new People("shao", 2));
People result = emp.Max();
Console.Write(result.Name);
}

public class People
{
public string Name { get; set; }
public int ID { get; set; }

public People(string _name, int _id)
{
Name = _name;
ID = _id;
}
}
```

由於 People 型別繼承了 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 介面，卻沒有實作 [CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 方法，所以程式碼第26行會編譯失敗。  若要解決這個問題，必須在 People 類別中，實作 [CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 方法。如下：  
```c#
public class People : IComparable
{
public string Name { get; set; }
public int ID { get; set; }

public People(string _name, int _id)
{
Name = _name;
ID = _id;
}

public int CompareTo(object obj)
{
People emp2 = obj as People;

if (emp2!=null)
return this.ID.CompareTo(emp2.ID);
else
throw new ArgumentException("Object is not a People");
}
}
```

[CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 是用來表示目前的執行個體應該排序在比較物件的前面、後面或相同位置。其回傳值意義如下：

- 負值： 執行個體排序在 obj 前面。
- 零值： 執行個體與 obj 排序在相同位置。
- 正值： 執行個體排序在 obj 後面。

#### 5. 使用 Base Class 條件約束
```c#
//限制Ｔ的型別僅可以是Employee
//若限制Ｔ的型別僅可以是People,則其衍生類別 Student,Employee 就都可以使用
public class GenericList<T> where T : Employee
{
public void AddHead(T t)
{
Node n = new Node(t);
n.Next = head;
head = n;
}        
｝

GenericList<Employee> myList = new GenericList<Employee>();
myList.AddHead(new Employee("test1", 1));
myList.AddHead(new Employee("test2", 2));

public class Student : People
{
public Student() : base() { }
public Student(string _name, int _id) : base(_name, _id) { } 
}

public class Employee : People
{
public Employee() : base() { }
public Employee(string _name, int _id) : base(_name, _id) { } 
}

public abstract class People
{
public string Name{get;set;}
public int ID{get;set;}
public People(){}
public People(string _name, int _id)
{
Name = _name;
ID = _id;
}
}
```

## 未繫結的型別參數

沒有條件約束的型別參數 (例如 SampleClass&lt;T&gt;{} 中的 T) 稱為未繫結的型別參數。未繫結的型別參數有下列規則：

- 無法使用 != 和 == 運算子，因為不能確定實體的型別引數是否會支援這些運算子。
- 這些參數可與 System.Object 相互轉換或明確轉換成任何介面型別。
- 您可以與 null 比較。如果將未繫結的型別參數與 null 比較，那麼當型別引數為實值型別時，一定會傳回 false。
```c#
public class SampleClass<T,U>
{
public void SampleClass(T a, U b)
{
if (a == b)  //compile error ＝＝＞ T,U都是未繫結的型別參數，不支援比較運算子
{
Console.WriteLine("a==b");
}
}
}
```

# Events

參考[http://msdn.microsoft.com/zh-tw/library/awbftdfh.aspx](http://msdn.microsoft.com/zh-tw/library/awbftdfh.aspx)

「事件 (Events)」是指某物件送出訊息，用以告知其他類別或物件某個動作的發生。  傳送 (或「引發」(Raise)) 事件的類別稱為「發行者」(Publisher)，而接收 (或「處理」(Handle)) 事件的類別則稱為「訂閱者」(Subscriber)。

在事件溝通時，事件發送著類別並不知道它所引發的事件是由哪個物件或方法來接收處理，所以必須透過一個中介者的機制，提供這個機制的特殊型別，稱為委派(Delegate)。  

- 事件 (Events)：要處理的事情
- 事件處理函式 (Events Handler)：事情的處理方法
- 委派 (Delegate)：指明哪一個事情，由哪一個方法去處理。

#### 宣告方法
```c#
public delegate 回傳型別 委派名稱 ([參數列]);

public event    委派名稱 事件名稱; 
```

## 如何設計事件

下面範例中，我們設計一個警報器控制項，並且定義了二個事件：(1)小偷入侵 (2)房子起火。  當第一個按鈕被按下時會引發小偷入侵事件，當第二個按鈕被按下時會引發房子起火事件。  
```c#
public delegate void Alarm_Handler(DateTime dt);    //宣告事件的 Delegate 型別

public partial class ucAlarm : UserControl
{
//定義事件
public event Alarm_Handler ThiefBreakInto;      //宣告事件 ThiefBreakInto 小偷入侵    
public event Alarm_Handler Fire;                //宣告事件 FireOn 火燒

public ucAlarm()
{
InitializeComponent();

btnThief.Click += new System.EventHandler(this.On_ThiefBreakInto);  //摸擬發生小偷入侵事件        
btnFire.Click += new System.EventHandler(this.On_Fire);             //摸擬發生火災事件
}

//觸發"火災"事件   
private void On_Fire(object sender, EventArgs e)
{
if (Fire != null)                           //當事件發生時,觸發"火災"事件            
Fire.Invoke(DateTime.Now);
}

//觸發"小偷入侵"事件   
private void On_ThiefBreakInto(object sender, EventArgs e)
{
if (ThiefBreakInto != null)                 //當事件發生時,觸發"小偷入侵"事件            
ThiefBreakInto.Invoke(DateTime.Now);
}
}
```

## 如何訂閱事件

要訂閱發行者所引發的事件，必須先撰寫事件的處理方法，再透過委派指明事件的處理方法。  
```c#
//訂閱事件
private void btnStartAlarm_Click(object sender, EventArgs e)
{
//透過委派指明事件的處理方法
ucAlarm1.ThiefBreakInto += new Alarm_Handler(ucAlarm1_ThiefBreakInto);
ucAlarm1.Fire += new Alarm_Handler(ucAlarm1_Fire); 
}

//取消訂閱事件
private void btnStopAlarm_Click(object sender, EventArgs e)
{
ucAlarm1.ThiefBreakInto -= new Alarm_Handler(ucAlarm1_ThiefBreakInto); 
ucAlarm1.Fire -= new Alarm_Handler(ucAlarm1_Fire); 
}

//小偷事件的處理方法
private void ucAlarm1_ThiefBreakInto(DateTime dt)
{
Console.WriteLine("Call 110 ..." + dt.ToString());
}
//火災事件的處理方法
private void ucAlarm1_Fire(DateTime dt)
{
Console.WriteLine("Call 119 ..." + dt.ToString());
}
```

## EventHandler

EventHandler 是一個系統內預定的委派，其定義如下：
```c#
public delegate void EventHandler(
Object sender,
EventArgs e
)
```

.NET 2.0 新增 [EventHandler 的泛型版本](http://msdn.microsoft.com/zh-tw/library/db0etb8x)，其定義如下：
```c#
[SerializableAttribute]
public delegate void EventHandler<TEventArgs>(
Object sender,
TEventArgs e
)
where TEventArgs : EventArgs
```

# Attributes

這裡要提的屬性，不是類別的property，而是在類別宣告時，寫在類別上方的那一些怪怪的文字描述，稱為[屬性(Attributes)](http://msdn.microsoft.com/zh-tw/library/system.attribute.aspx)

屬性通常用來：

- 指定某個類別所需的安全權限。Specify which security privileges a class requires
- 指定拒絕的安全權限，以降低安全風險。Specify security privileges to refuse to reduce security risk
- 宣告能力，例如是否支援序列化。Declare capabilities, such as supporting serialization
- 以提供標題、說明以及著作權注意事項來描述組件。Describe the assembly by providing a title, description, and copyright notice
```c#
[assembly: AssemblyTitle("Practice")]
[assembly: AssemblyDescription("")]
[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]

[Serializable] //宣告成可序列化
public class Grade : IDeserializationCallback
{
...
｝
```

# Type Forwarding

Type forwarding allows you to move a type from one assembly (assembly A) into another assembly (assembly B), such that, it is not necessary to recompile clients that consume assembly A.

參考[http://msdn.microsoft.com/zh-tw/library/ms177220.aspx](http://msdn.microsoft.com/zh-tw/library/ms177220.aspx)

| ```c#
namespace ClassLibrary<br>{
public class Class2
{
public string Name()
{
return "Class2 under Dll_2";
}
}<br>
public class AnotherClass
{<br>
}<br>}<br>``` | ```c#
namespace ClassLibrary<br>{
public class Class1
{
public string Name()
{
return "Class1 under Dll_1";
}
}<br>}<br>``` |
| --- | --- |
| ```c#
private void button11_Click(object sender, EventArgs e)<br>{
ClassLibrary.Class2 c2 = new ClassLibrary.Class2();
Console.WriteLine(c2.Name());<br>}<br>//Output:<br>Class2 under Dll_2<br>``` |

若某個應用程式原先使用到組件２的Class2類別，但因為某些原因，該類別要移動到組件１裡面，  一般的做法是變更應用程式的參考，甚至要改變程式碼以符合變更。  不過利用Type Forwarding技術，可以不用重新編譯應用程式即可正常運作。將二個類別調整如下，re-build即可

| ```c#
using System.Runtime.CompilerServices;<br>[assembly: TypeForwardedTo(typeof(ClassLibrary.Class2))]<br><br>namespace ClassLibrary<br>{
//public class Class2
//{
//    public string Name()
//    {
//        return "Class2 under Dll_2";
//    }
//}<br>
public class AnotherClass
{<br>
}<br>}<br>``` | ```c#
namespace ClassLibrary<br>{
public class Class1
{
public string Name()
{
return "Class1 under Dll_1";
}
}<br>
public class Class2
{
public string Name()
{
return "Class2 under Dll_1";
}
}<br>}<br>``` |
| --- | --- |
| ```c#
private void button11_Click(object sender, EventArgs e)<br>{
ClassLibrary.Class2 c2 = new ClassLibrary.Class2();
Console.WriteLine(c2.Name());<br>}<br>//Output:<br>Class2 under Dll_1<br>``` |

# 建構函式 (Constructor)

## 執行個體建構函式（Instance Constructors ）

- 建構函式通常用來初始化資料成員 (Data Member)。
- **不使用任何參數**的建構函式稱為「**預設建構函式**」(Default Constructor)。
- **若公開類別中沒有任何建構函式，C# 編譯器都會自動指定一個公用的預設建構函式**，以執行類別執行個體化。
- **若公開類別中已有其他建構函式，就不會再自動提供預設建構函式**。
- 建構函式可標示為 public、private、protected、internal 或 protectedinternal。這些存取修飾詞 (Modifier) 將定義類別使用者如何建構類別。  
如果未指定類別的存取修飾詞，則預設值為**internal**

### 例一：預設建構函式
```c#
public class Person
{
string Name { get; set; }
}

private void button2_Click(object sender, EventArgs e)
{
Person person = new Person(); 
}
```

### 例二：呼叫基底類別的建構函式
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
```c#
//如果未指定類別的存取修飾詞，則預設值為 internal
class Point
{
private int _x, _y;

public Point(int x, int y)
{
_x = x;
_y = y;
}
}
private void button18_Click(object sender, EventArgs e)
{
Point point1 = new Point(3, 4);
}
```

## 靜態建構函式

- 「靜態建構函式」可以用來初始化任何「靜態資料」，而且只會執行一次。
- 靜態建構函式不使用存取修飾詞，也不能使用參數。
```c#
class SimpleClass
{
// 靜態變數必須在執行階段被初始化。
static readonly long baseline;

// 不管具現化幾個實體，靜態建構函式只會被呼叫一次
static SimpleClass()
{
baseline = DateTime.Now.Ticks;
}

public long GetBaseline()
{
return baseline;
}
}

private void button16_Click(object sender, EventArgs e)
{
SimpleClass c1 = new SimpleClass();
Console.WriteLine(c1.GetBaseline());    //634794289047137456

SimpleClass c2 = new SimpleClass();
Console.WriteLine(c2.GetBaseline());    //結果同上634794289047137456
}
```

## 私用建構函式

「私用建構函式」(private) 是一種特殊的執行個體建構函式。  它通常是用在只有包含靜態成員的類別中，讓其他類別不能建立這個類別的執行個體。例如：   
```c#
public class Counter
{
//私用建構函通常用在只有靜態成員的類別中
private Counter() { }
public static int currentCount;
public static int IncrementCount()
{
return ++currentCount;
}
}

private void button17_Click(object sender, EventArgs e)
{
// Counter counter = new Counter();  //因為 Counter 為 private constructor，所以無法具現化

Counter.currentCount = 100;
Counter.IncrementCount();
}
```

注意，若定義建構函式時，沒有使用存取修飾詞 (Modifier) ，則預設會是 private (私用)。

# 靜態類別

靜態類別就像一個容器，專門用來收集一些好用的方法，在靜態類別中的 method、property、field 都是靜態的，  你無法具現化一個靜態類別，所以自然不需要在靜態類別中使用 Set 或 Get 來設定執行個體的欄位值。  

靜態類別的主要特色：

- 不能具體化。
- 不能包含建構函式。
- 只能有靜態成員。
- 是密封的 (sealed)，就是不能被繼承。

它與一般類別的差異在於，當程式執行時，一般類別只有在具現化時才會被載入記憶體，當使用完畢就可以被釋放，從記憶體中移除。  而靜態類別會在程式一開始執行時就被載入，且在程式的生命週期中，都會一直保持在記憶體中。  
```c#
public static class myDebug
{
public static void Write(string msg)
{
Debug.Write(msg);
}
public static void WriteLine(string msg)
{
Debug.WriteLine(msg);
}
public static void Write(string format, params object[] args)
{
Debug.Write(string.Format(format, args));
}
public static void WriteLine(string format, params object[] args)
{
Debug.WriteLine(string.Format(format, args));
}
｝
```

# 靜態成員

- 不管是欄位、屬性、或是方法，只要設定成**靜態成員**，那麼它就不屬於任何一個物件實體。
- 你可以直接存取靜態成員，不需也不能透過該物件的實體去存耶。
- 非靜態類別也可以包含靜態成員，但無論該非靜態類別建立多少個執行個體，該靜態成員還是只會有一份覆本存在。
- 靜態方法由於屬於類別而不屬於類別的任何執行個體，因此能多載但不能覆寫。
```c#
public class Test
{
private int pvt_Var = 1;
public int pub_Var = 2;
private void pvt_Func() { }
public void pub_Func() { }

//靜態成員
private static int pvt_stc_Var = 1;
public static int pub_stc_Var = 2;
private static void pvt_stc_Func() { //不允許存取實體成員 }
public static void pub_stc_Func() { //不允許存取實體成員 }
}
```

由物件實體，無法存取靜態成員。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9AxKCv8z4cFNB98o-MN15PfQCuLSn67PmaZu3pqJefgg-XUsqiM0RNqGVmFf7SMYILGuVBL4XbsGdz82CrrQX9GhMQgyyp5S5aBHYOvXAX5jYMxSwp6dJP2SrdtwCHcOcddvhaxI1KK4/s438/static-member-2.png)

要存取靜態成員，可以直接透過類別名稱來存取。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipq9NS02E5jFCQ3eJO_PIhpc4eZEpR_ZlF7kldWuf3ebNocU-jCgdC8-l5nryu93fXIMzAvwbUKFNhscRooIfqPLi9SkkGpUmVysNPskclXMLx5qYKxj4yT_36qRMtQsFTPVmOMdG9BWg/s437/static-member-1.png)

# 存取修飾詞 (Modifier)

存取修飾詞是用來指定型別或成員的存取範圍。共有下列四種存取修飾詞：

| 名稱 | C# | VB.NET | 說明 |
| --- | --- | --- | --- |
| 公用 | [public](http://msdn.microsoft.com/zh-tw/library/yzh058ae.aspx) | [Public](http://msdn.microsoft.com/zh-tw/library/9dc6we3z.aspx) | 沒有限制 |
| 私用 | [private](http://msdn.microsoft.com/zh-tw/library/st6sy9xe.aspx) | [Private](http://msdn.microsoft.com/zh-tw/library/wx059ey1.aspx) | 最嚴格的存取層級，只能在宣告他們的類別主體或結構主體內存取 |
| 保護 | [protected](http://msdn.microsoft.com/zh-tw/library/bcd5672a.aspx) | [Protected](http://msdn.microsoft.com/zh-tw/library/8050kawf.aspx) | 允許由其衍生類別的執行個體存取。 |
| 內部 | [Internal](http://msdn.microsoft.com/zh-tw/library/7c5ka91b.aspx) | [Friend](http://msdn.microsoft.com/zh-tw/library/08w05ey2.aspx) | 只允許在相同組件中的檔案內存取。 |

透過存取修飾詞可以使用下列五種存取範圍層級：

- **public**：存取沒有限制。
- **protected**：存取只限包含型別或其衍生類別的型別。
- **Internal**：存取只限於目前的組件。
- **protected Internal**：存取只限於目前的組件或衍生自包含類別的型別。
- **private**：存取只限於包含型別。

## 「繼承」與「存取修飾詞」

下面這個例子簡單舉例各種存取修飾詞的關係.
```c#
class Program
{

class Shape
{
protected int x = 100;
protected int y = 150;
}
class Square : Shape
{
public int area()
{
x = 200;   //因為 x,y 是 protected 屬性，所以可以在衍生類別中使用
y = 200;
return x\*y;
}
private void privateF() { }
protected void protectedF() { }
internal void internalF() { }
}

static void Main(string[] args)
{
Shape shape = new Shape();
Square square = new Square();

//shape.x = 200;   //x 是 protected 屬性，只能在類別中使用
//square.x = 200;　//x 是繼承自基底類別的 protected 屬性，所以只能在該衍生類別中使用
square.area();
square.internalF();
//square.protectedF();  //protected，只能在該類別中使用
//square.privateF();    //private，只能在該類別中使用
}
}
```

# 自訂擴充方法

- 擴充方法是指直接對現有的型別「加入」新的方法，而不需要建立新的衍生型別 (Derived Type)、或是修改原始型別。
- 擴充方法是一種特殊的靜態方法，建立好之後，你可以由它們原來型別的執行個體直接叫用。
- 定義擴充方法時，第一個引數必須是「this」。
```c#
public class MyType
{
public string MethodA()
{
return "MethodA";
}
}

//Extension methods must be defined in a static class
public static class MyTypeExtension
{
// This is the extension method.
// The first parameter takes the "this" modifier
// and specifies the type for which the method is defined.
public static string MethodB(this MyType mytype)
{
return "MethodB";
}
}

//Using extension method
private void btnExtension_Click(object sender, EventArgs e)
{
string str = "";

MyType mytype = new MyType();
str= mytype.MethodA();
str= mytype.MethodB();
}
```


- [C# 程式設計手冊](http://msdn.microsoft.com/zh-tw/library/67ef8sbd)
- [繼承基本概念](http://msdn.microsoft.com/zh-tw/library/c8shwxa5.aspx)
- [繼承 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/ms173149%28v=vs.100%29.aspx)
- [執行個體建構函式 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/k6sa6h87)
- [使用建構函式 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/ms173115.aspx)
- [靜態類別和靜態類別成員 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/79b3xss3.aspx)
- [靜態建構函式 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/k9x6w0hc.aspx)
- [私用建構函式 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/kcfb85a6.aspx)
- [\[C#\] 泛型的條件約束 Constraints](http://www.dotblogs.com.tw/atowngit/archive/2011/03/09/21758.aspx)
- [new 條件約束 (C# 參考)](http://msdn.microsoft.com/zh-tw/library/sd2w2ew5.aspx)
- [擴充方法 (C# 程式設計手冊)](https://msdn.microsoft.com/zh-tw/library/bb383977.aspx)