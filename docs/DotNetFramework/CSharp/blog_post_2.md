---
title: 傳遞參數
layout: default
parent: CSharp
nav_order: 2
description: "傳遞參數"
date: 2012-05-11
tags: [DotNetFramework,CSharp]
postid: "3239958762762023380"
---
# 使用 ref 和 out 傳遞參數

## ref vs out

[ref](http://msdn.microsoft.com/zh-tw/library/14akc2c7) 和 [out](http://msdn.microsoft.com/zh-tw/library/ee332485)  這二個關鍵字都是用來宣告使用傳址 (By Reference) 方式傳遞參數。使用上也都必須明確使用 ref 或 out 關鍵字。  

二者之間最主要的差異在於：

- 使用 ref 修飾詞的參數，其參數**傳送前**必須要有值。
- 使用 out 修飾詞的參數，其參數**回傳前**必須要有值。
```c#
private void button1_Click(object sender, EventArgs e)
{
int x;
x = 5;              //以ref方式傳送的引數,傳送前要先指派值              
Ref_Method(ref x);

int y;
//y = 5;            //以out方式傳送的引數,傳送前可以不用指派值  
Out_Method(out y);
}
private void Ref_Method(ref int a)
{
a = a + 1;          //ref參數,使用前不用再指派值
}
private void Out_Method(out int a)
{
a = 0;              //out參數,使用前要先指派值
a = a + 1;
}
```

## out 參數的應用

乍看之下，除了上面的限制外，功能面上好像沒什麼差別。其實使用 out 參數修飾詞，它的另外一面是希望，不去管呼叫端傳入的值，主要是想由被叫端取得值；或者說，你需求一個具多個回傳值的方法時，也可以利用 out 修飾詞。例如：  
```c#
static void Method(out int i, out string s1, out string s2)
{
i = 20120601;
s1 = "Windows Server 2012 RC ";
s2 = "Windows 8 Release Preview ";
}
private void button1_Click(object sender, EventArgs e)
{
int day;
string s1, s2;
Method(out day, out s1, out s2);
Console.WriteLine("{0} {1} {2}", day, s1, s2);
}
```

底下例子是日期的轉換函式 DateTime.TryParse 方法，它的功能是將一個字串轉換成對等的日期型別。  如果轉換成功，則 out 參數就是對等的日期型別資料，若轉換失敗，out 參數就是 0001/1/1 12:00:00 日期型別資料。  如此確保不管轉換成功或失敗，這個變數都會是一個有效的日期型別資料。  
```c#
DateTime dtime;
if (DateTime.TryParse("2011/13/25 15:59:58", out dtime))
Console.WriteLine(dtime.ToString());                // 2011/8/25 下午 03:59:58
else
Console.WriteLine(dtime.ToString());                // 0001/1/1 上午 12:00:00
```

# 使用可變長度參數（參數陣列）

[params](http://msdn.microsoft.com/zh-tw/library/w5zay9db.aspx) 關鍵字可用來宣告參數陣列，也就是宣告一個不固定數量的參數列。

使用上注意事項：

- 必須是清單中最後的參數。
- 必須為一維陣列型別。
- 不可與 ref 和 out 修飾詞合併使用。
```c#
public int Average(string className, params int[] grades)
{
int total = 0;
foreach (int grade in grades)
total += grade;
return total;
}
private void button10_Click(object sender, EventArgs e)
{
int total = Average("math", 100, 80, 40, 90);
}
```

# 具名引數

C# 2010 支援具名引數（Named Argument），使用上有幾點要注意，

- 若沒有所有引數都具名，要把所有具名引數全放在後面。
- 具名引數可以不分順序。
- 非具名引數要依照參數順序。
```c#
public void PrintData1(string UserName, string Tel, int Age)
{
Console.WriteLine(UserName + Tel + Age);
}
private void button11_Click(object sender, EventArgs e)
{
PrintData1("vito", "3939889", 10);

PrintData1(Age: 10, UserName: "vito", Tel: "3939889");

PrintData1("vito", Age: 10, Tel: "3939889");

PrintData1("vito", "3939889", Age: 10);
}
```

在 C# 程式設計中，建立物件時也可以使用具名引數，在 MSDN 中它稱為的「[集合初始設定式](http://msdn.microsoft.com/zh-tw/library/bb384062.aspx)」。  
```c#
class Cat
{
public int Age { get; set; }
public string Name { get; set; }
}
static void Main()
{
Cat cat = new Cat { Age = 10, Name = "Fluffy" };

List<Cat> cats = new List<Cat>
{
new Cat(){ Name = "Sylvester", Age=8 },
new Cat(){ Name = "Whiskers", Age=2 },
new Cat(){ Name = "Sasha", Age=14 }
};
｝
```

# 選擇性參數

C# 2010 支援選擇性參數（Optional Parameter），使用上有幾點要注意，

- 選擇性參數要位於參數列中必要參數的後面。
- 每個選擇性參數都要給預設值。
```c#
public void PrintData2(string UserName, string Tel = "3939889", int Age = 10)
{
Console.WriteLine(UserName + Tel + Age);
}
private void button12_Click(object sender, EventArgs e)
{
PrintData2("vito");
PrintData2("vito", Age: 20);  //搭配具名參數
}
```


- [傳遞參數 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/0f66670z)
- [使用 ref 和 out 傳遞陣列 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/szasx730)
- [參數陣列](http://msdn.microsoft.com/zh-tw/library/aa645765%28v=VS.71%29.aspx)