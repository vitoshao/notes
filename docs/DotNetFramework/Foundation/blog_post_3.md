---
title: 參考型別
layout: default
parent: Foundation
nav_order: 3
description: "參考型別"
date: 2011-12-16
tags: [DotNetFramework,Foundation]
postid: "3439732641247884167"
---
# 什麼是參考型別

參考型別是將**變數位址** (ponter) 存在 Stack 中，但該變數位圵則指到一塊稱為「堆積(Heap)」 的記憶體區塊，該區塊才是真正資料儲存的地方。  

若某個變數是參考型別，當複製該變數時，實際上複製的是另外一份參考，而這個參考會指向 Heap 中同一個記憶體位置。底下範例可看出二者實際上的差異：  
```c#
struct sNum
{
public int val;
public sNum(int _val) { val = _val; }
}

class cNum
{
public int val;
public cNum(int _val){val = _val;}
}

private void button2_Click(object sender, EventArgs e)
{
sNum s1 = new sNum(5);
sNum s2 = s1;
s2.val = 3;
Console.WriteLine("s1={0} , s2={1}", s1.val, s2.val);  //s1=5 , s2=3

cNum c1 = new cNum(5);
cNum c2 = c1;
c1.val = 3;
Console.WriteLine("c1={0} , c2={1}", c1.val, c2.val);  //c1=3 , c2=3

int i = 100;
object obj = i;
}
```

![](http://127.0.0.1:8080/_images/cs/stack_heap.jpg)

#### 補充說明

堆疊 (Stack) 和堆積 (Heap) 是記憶體中，用來存放資料的兩種不同管理機制。

- Stack：當程式載入時，就配置好固定大小的記憶體空間；當變數生命周期結束時，就由記憶體中移除。
- Heap：當程式需要使用時，才透過記憶體配置方法，動態建立；當變數生命周期結束時，僅註明沒人使用，必須由程序釋放或由系統不定時的回收清空。
```c#
int i = 100;
object obj = i;
```

變數 i 是實值型別，資料存在 stack 裡  
變數 obj 是參考型別，資料存在 heap 裡  
當執行 obj = i 時，系統會將 100 從 stack 複製一份到 heap 之中，然後 obj 就指向這個位址，這就稱為 Boxing。  
反之，若將 heap 中的值，複製到stack之中，就稱為 Unboxing。  

將數值資料轉換成物件稱為 Boxing ，把物件轉成數值稱為 Unboxing 。

# 內建參考型別

Object、String、Array、Stream、Exception等等都是內建參考型別 (Built-in Reference Types)

### Strings and String Builders
```c#
string s;
s = "wombat"; // "wombat"
s += " kangaroo"; // "wombat kangaroo"
s += " wallaby"; // "wombat kangaroo wallaby"
s += " koala"; // "wombat kangaroo wallaby koala"
Console.WriteLine(s);

string[] ss = { "wombat", "kangaroo", "wallaby", "koala" };
string ss_join = string.Join(" ", ss);
Console.WriteLine(ss_join);             //wombat kangaroo wallaby koala

string ss_concat = string.Concat(ss);
Console.WriteLine(ss_concat);           //wombatkangaroowallabykoala

StringBuilder sb = new StringBuilder();
sb.Append("wombat"); 
sb.Append(" kangaroo");
sb.Append(" wallaby");
sb.Append(" koala");
Console.WriteLine(sb.ToString());       //wombat kangaroo wallaby koala
```

### Create and Sort Arrays
```c#
string[] ss = { "wombat", "kangaroo", "wallaby", "koala" };
Array.Sort(ss);
Console.WriteLine("{0}, {1}, {2}, {2}", ss[0], ss[1], ss[2], ss[3]);
//kangaroo, koala, wallaby, wallaby
```

### How to Use Streams
```c#
// Create and write to a text file
StreamWriter sw = new StreamWriter("text.txt");
sw.WriteLine("Hello, World!");
sw.Close();

// Read and display a text file
StreamReader sr = new StreamReader("text.txt");
Console.WriteLine(sr.ReadToEnd());
sr.Close();
```

### How to Throw and Catch Exceptions

底下是一個Exception的程式碼片段，有幾點值得注意一下的：

- 1.Catch區塊最好以最特定到最不特定的方式排序 (most specific =&gt; least specific)
- 2.Finally區塊，不管有無例外，這個區塊都會執行，所以可在這個區塊清除不必要的物件。
```c#
StreamReader sr = null;
try
{
sr = new StreamReader("test.txt");
Console.WriteLine(sr.ReadToEnd());
}

catch (System.IO.FileNotFoundException ex)
{
Console.WriteLine(ex.Message);
}
catch (System.UnauthorizedAccessException ex)
{
Console.WriteLine(ex.Message);
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
//找不到檔案 'D:\MCTS\MCTS2011\Practice\Practice\bin\Debug\test.txt'。

Console.WriteLine(ex.StackTrace);
//於 System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)
//於 System.IO.FileStream.Init(String path, FileMode mode, FileAccess access, Int32 rights, Boolean useRights, FileShare share, Int32 bufferSize, FileOptions options, SECURITY_ATTRIBUTES secAttrs, String msgPath, Boolean bFromProxy, Boolean useLongPath)
//於 System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options)
//於 System.IO.StreamReader..ctor(String path, Encoding encoding, Boolean detectEncodingFromByteOrderMarks, Int32 bufferSize)
//於 System.IO.StreamReader..ctor(String path)
//於 Practice.Chapter1.Lesson2.button3_Click(Object sender, EventArgs e) 於 ....Lesson2.cs: 行 73

Console.WriteLine(ex.Source);
//mscorlib

}
finally
{
if (sr!=null)
sr.Close();
}
```

### Object 型別

- 任何參考型別（字串、陣列、類別或介面）或實值型別（數字、Boolean、Char、Date、結構或列舉）都可以指派給 Object 變數。
- Object  的預設值為 Null。
- 你可以使用 [GetTypeCode](http://msdn.microsoft.com/zh-tw/library/system.type.gettypecode.aspx) 方法取得目前 Object 變數所參考的資料型別。
- Object  資料型別就是參考型別。但是，如果它參考的是實值型別的資料時，就會被視為實值型別。
- 無論它所參考的資料型別為何，Object 變數都不會包含資料值本身，而是值的指標。  它在電腦記憶體中所佔的空間為 4 個位元組，但這並不包括儲存表示變數值的資料。

### String 型別

字串物件是由 Char 物件所組成的一種物件，它是循序唯讀的集合。
```c#
string str = "hello";

// 字串變收是唯讀不可變動的
char tmp = str[2];

//str[2] = 'a';    <--這是不允許的
```

### 參數傳遞

下面這個例子，是使用委派，傳遞一個類別物件給委派指定的方法，看看參數值的變化。
```c#
testEmployee test;
Employee emp1 = new Employee{ Name="vito", Age=30 };
test = delegate(Employee arg)
{
arg.Name = "shao";
};
test(emp1);
Console.WriteLine(emp1.Name);  //shao

testEmployee test2;
test2 = delegate(Employee arg)
{
Employee emp2 = new Employee { Name = "peter", Age = 40 };
arg = emp2;                         
Console.WriteLine(arg.Name);
};
test2(emp1);
Console.WriteLine(emp1.Name);  //shao
```

上面這個例子的重點在第１４行，  當 emp1 被傳進方法中時，會複製一份指標位址給 arg，此時 arg 和 emp1 都指向相同的位址，但 arg 和 emp1 是不相同的。  arg 後來被改成指向 emp2，但當方法執行終了，生命週期也就跟著結束。  

下面這個例子，比較 int, object, string, class object 等型別，在當做參數傳遞時的差異。
```c#
private void test1(int x)
{
x = 5;
}
private void test2(ref int x)
{
x = 5;
}
private void test3(object x)
{
x = 5;
}
private void test4(StringBuilder x)
{
x.Replace('3', '5');
}
private void test5(Emp x)
{
x.salary = 5;
}
public class Emp
{
public Emp(int s) { salary = s; }
public int salary { get; set; }
}
private void button9_Click(object sender, EventArgs e)
{
int a = 3;
int b = 3;
object c = 3;
object d = "3";
StringBuilder sb = new StringBuilder("3");
Emp emp = new Emp(3);

test1(a);
test2(ref b);
test3(c);
test3(d);
test4(sb);
test5(emp);

Console.WriteLine(a);
Console.WriteLine(b);
Console.WriteLine(c);
Console.WriteLine(d);
Console.WriteLine(sb);
Console.WriteLine(emp.salary);

//3
//5
//3
//3
//5
//5
}
```

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [參考型別 (C# 參考)](http://msdn.microsoft.com/zh-tw/library/490f96s2)
-