---
title: 型別轉換
layout: default
parent: Foundation
nav_order: 1
description: "型別轉換"
date: 2011-12-16
tags: [DotNetFramework,Foundation]
postid: "1589871349677134864"
---
# 何謂型別轉換

資料在進行型別轉換時，可分成明確轉換或隱含轉換二種。

- 明確轉換(explicit conversion)：明確指定轉換的型別。
- 隱含轉換(implicit conversion)：由系統自動進行不同型別資料的轉換。
```c#
//明確轉型 
long a = System.Convert.ToInt64(10000);

//明確轉型
byte c = (byte) a;   //long 轉 byte 可能會造成資料丟失 

//隱含轉型
a = c;              //byte 轉 long
```

隱含轉換的過程，可能造成下面二種情況：

- 擴展轉換(widening conversion)：隱含轉換時，目的型別精準度大於原始型別。
- 縮小轉換(narrowing conversion)：隱含轉換時，目的型別精準度小於原始型別。

VB預設二種情況都是允許的，C#則禁止縮小轉換。  VB若要關閉隱含轉換，可在程式碼檔案最前面加上 Option Strict On。  或者在[專案]-&gt;[屬性]-&gt;[編譯]，將整個專案的 Option Strict 設為 On。  
```c#
int i = 10;
double d = 5.5;

d = i;          // 擴展轉換
//i = d;        // 縮小轉換: C#不允許縮小轉換，所以會產生 double 不能隱含轉換為 int 的錯誤.
i = (int)5.5;   // explicit conversion , may lost some information
```

# 何謂 Boxing 與 Unboxing

- Boxing : Value Type to Reference Type, implicit convertion.
- Unboxing : Reference Type to Value Type, explicit convertion.
```c#
int i = 123;
object obj = i;     //boxing會自動隱含轉換
```
```c#
object obj = 123;
int i = (int)obj;   //unboxing 動作必須明確宣告轉換型別，無法自動隱含轉換
int j = obj;        //這一行會錯誤。
```

下面這個例子，第 4 和第 8 行會發生轉換錯誤。原因？
```c#
int no1 = 10;
object obj1 = no1;
int no2 = (int)obj1;
short no3 = (short)obj1;

short no4 = 10;
object obj2 = no4;
int no5 = (int)obj2;
short no6 = (short)obj2;
```

- 行２：obj1 boxing 一個 int 的資料。
- 行３：no2 將 obj1 中的資料 unboxing 回 int.
- 行４：obj1 中的資料為一個 int 型別資料，無法 unboxing 成 short 型別。
- 行８：obj2 中的資料為一個 short 型別資料，無法 unboxing 成 int 型別。

# 如何實作自訂型別的轉換 (Conversion)

若要設計自訂型別的轉型，通常必須根據想要轉換的型別使用不同的技巧。例如以下幾種方式：

- 覆寫 ToString：提供將其他型別轉換為字串的功能。
- 覆寫 Parse：提供將字串轉換為數字型別的功能。
- 定義[轉換運算子](http://msdn.microsoft.com/zh-tw/library/85w54y0a.aspx)：可執行數值之間的轉換功能。
- 加入 [System.IConvertible](http://msdn.microsoft.com/zh-tw/library/tyz9cd4z) 介面：在自訂型別中，實作各種型別的轉換方法。

## ToString & Parse

- ToString：這個方法是覆寫自 Object 的 ToString 方法，可用來將數值型態的執行個體，轉換成對等的字串。
- Parse：每個數值型別都具有靜態的 Parse 方法，可用來將字串轉換成本身的數值型別。
```c#
int iNum = 10;
double dNum = 5.5;
string str;

str = iNum.ToString();       //將int轉換為字串。
dNum = double.Parse(str);    //將字串轉換為double。
iNum = int.Parse(str);       //將字串轉換為int。
```

無參數的 ToString 方法是覆寫自 Object.ToString 。  通常.NET 內建的型別中，也會自訂其它多載的 ToString 方法，以應付不同需求狀況。例如底下為 DateTime 型別４個多載方法的樣式：
```c#
public override string ToString();
public string ToString(IFormatProvider provider);
public string ToString(string format);
public string ToString(string format, IFormatProvider provider);
```
```c#
DateTime theDate = new DateTime(2012, 7, 20);
Console.WriteLine(theDate.ToString());          // 2012/7/20 上午 12:00:00
Console.WriteLine(theDate.ToString("d"));       // 2012/7/20

//使用委內瑞拉的 CultureInfo 來顯示日期
CultureInfo esVE = new CultureInfo("es-VE");
Console.WriteLine(theDate.ToString("d", esVE)); // 20/07/2012

//使用克羅埃西亞的 DateTimeFormatInfo 來顯示日期
DateTimeFormatInfo fmt = new CultureInfo("hr-HR").DateTimeFormat;
Console.WriteLine(theDate.ToString("d", fmt));  // 20.7.2012
Console.WriteLine(theDate.ToString(fmt.ShortDatePattern));  // 20.7.2012
```
```c#
int num = 4567;
Console.WriteLine(num.ToString());          // 4567
Console.WriteLine(num.ToString("d"));       // 4567
Console.WriteLine(num.ToString("d6"));      // 004567
Console.WriteLine(num.ToString("C"));       // NT$4,567.00
Console.WriteLine(num.ToString("N"));       // 4,567.00

//使用大陸的 CultureInfo
CultureInfo zhCN = new CultureInfo("zh-CN");
Console.WriteLine(num.ToString("C", zhCN)); // ￥4,567.00

//使用克羅埃西亞的 NumberFormatInfo
NumberFormatInfo fmt = new CultureInfo("hr-HR").NumberFormat;
Console.WriteLine(num.ToString("C", fmt));  // 4.567,00 kn
```

## 轉換運算子 (Conversion operator)

轉換運算子就是在自訂類別中直接宣告型別的轉換，該自訂型別可與其他型別或.NET型別互相轉換。  
轉換運算子有二種屬性， **explicit** 或 **implicit**。詳情請參考：[使用轉換運算子 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/85w54y0a.aspx)  

#### 隱含轉換 vs 明確轉換

- 宣告為隱含的轉換運算子(implicit)，會在必要時自動發生，也就是客戶端不需要動作就會自行轉換。  
此種轉換使用在資料不會失去精準度時使用，例如 int =&gt; double。
- 宣告為明確的轉換運算子(explicit)，需要呼叫轉型，也就是客戶端得使用強制轉型才會進行轉換動作。  
此種轉換使用在容易失去精準度時使用，例如 double =&gt; int。

#### 使用轉換運算子時，要注意以下幾點︰

- 所有轉換必須宣告為 **static**。
- 轉換運算子的宣告方法就和運算子一樣，並且以轉換成的型別來命名。
- 轉換運算子的宣告，轉換的結果或傳入的參數，其中一個必須是轉換型別。

#### 使用 operator 關鍵字來建立使用者定義轉換的語法如下：
```c#
//目標型別或來源型別，其中一個必須是轉換型別
public static implicit operator 目標型別 ( 來源型別名稱 )
public static explicit operator 目標型別 ( 來源型別名稱 )
```
```c#
public static implicit operator float (myType _type)        // 定義隱含轉換運算子,允許 myType 型別隱含轉換成 float 型別
public static explicit operator int (myType _type)          // 定義明確轉換運算子, myType 型別必須明確轉換成 int 型別
public static explicit operator myType (int _value)    
public static implicit operator myType (float _value)  
```

下面這個例子中的CTemp、FTemp是二個自訂型別，程式碼示範如何利用轉換運算子做型別轉換。若我們需求程式可以達到：  
1. 二者都可以和 float 型別做隱含轉換。  
2. FTemp 可隱含轉換成 CTemp ； CTemp 須明確轉換成 FTemp。  
```c#
public abstract class Temperature
{
public float Temp { get; set; }
public Temperature(float temp)
{
this.Temp = temp;
}
public override string ToString()
{
return Temp.ToString("0.00"); 
}
}
public class CTemp : Temperature
{
//將建構子設為 private，代表無法用 new 關鍵字new出 CTemp 型別
private CTemp(float temp) : base(temp) { }

public static implicit operator float(CTemp c)      //隱含轉換 (將CTemp轉成float)
{
return c.Temp;
}

public static implicit operator CTemp(float temp)   //隱含轉換 (將float轉成CTemp)
{
return new CTemp(temp);
}

public static implicit operator CTemp(FTemp f)      //隱含轉換 (將FTemp轉成CTemp)
{
return (((f.Temp - 32) \* 5) / 9);
}

public static explicit operator FTemp(CTemp c)      //明確轉換 (將CTemp轉成FTemp)
{
return (((c.Temp \* 9) / 5) + 32);
}
}

public class FTemp : Temperature
{
private FTemp(float temp) : base(temp) { }

public static implicit operator FTemp(float temp)
{
return new FTemp(temp);
}
public static implicit operator float(FTemp f)
{
return f.Temp;
}
}

CTemp tempC1, tempC2, tempC3;
FTemp tempF = 95;

tempC1 = 28.563f;            // float 允許隱含轉換成 CTemp
tempC2 = 25;                 // int 允許隱含轉換成 CTemp
tempC3 = tempF;              // FTemp 允許隱含轉換成 CTemp

Console.WriteLine(tempC1.ToString());   // 28.56
Console.WriteLine(tempC2.ToString());   // 25.00
Console.WriteLine(tempC3.ToString());   // 35.00

//tempF = tempC3;             // CTemp 不能隱含轉換成 FTemp
tempF = (FTemp)tempC3;        // 必須明確轉換成 FTemp

Console.WriteLine(tempF.ToString());   // 95.00
```

## 實作 System.IConvertible 介面

[System.IConvertible](http://msdn.microsoft.com/zh-tw/library/system.iconvertible.aspx) 介面是用來定義自訂型別轉換成具有等值的 CLR 型別 (如 Boolean、Byte、Int16 ...) 。  
若要實作 [System.IConvertible](http://msdn.microsoft.com/zh-tw/library/system.iconvertible.aspx) 介面，可以將 [IConvertible](http://msdn.microsoft.com/zh-tw/library/system.iconvertible.aspx) 介面加入到型別定義中，然後實作該介面。
```c#
TypeA a = 68;
string s = a.ToString();
byte be = Convert.ToByte(a);
bool bl = Convert.ToBoolean(a);
int i = Convert.ToInt16(a);

class TypeA : IConvertible
{
protected int Value;

public static implicit operator TypeA(int arg)
{
TypeA res = new TypeA();
res.Value = arg;
return res;
}

public override string ToString()
{
return this.Value.ToString();
}

public bool ToBoolean(IFormatProvider provider)
{
if (Value > 0)
return true;
else
return false;
}

public byte ToByte(IFormatProvider provider)
{
if (Value < 255)
return (byte)Value;
else
return 0;
}
public short ToInt16(IFormatProvider provider)
{
if (Value < 32767)
return (short)Value;
else
return 0;
}
}
```

## 實作 System.IFormattable 介面

[IFormattable](http://msdn.microsoft.com/zh-tw/library/system.iformattable.aspx) 介面是用來**自訂型別的字串顯示樣式**，它會根據「格式字串」和「格式提供者」這二個參數，將物件轉換成其字串表示。   

#### 格式字串：通常用來定義外觀。例如，.NET Framework 定義了下列幾種格式字串：

- [列舉型別格式字串](http://msdn.microsoft.com/zh-tw/library/c3s1ez6e)
- [標準數值格式字串](http://msdn.microsoft.com/zh-tw/library/dwhawy9k)
- [標準日期和時間格式字串](http://msdn.microsoft.com/zh-tw/library/az4se3k1)

#### 格式提供者：通常用來定義字串使用的符號。例如，.NET Framework 會定義三種格式提供者：

- [System.Globalization.CultureInfo 類別](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo)：文化特性類別，其中包含了 NumberFormatInfo 或 DateTimeFormatInfo 。
- [System.Globalization.NumberFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo)：這個類別用來表示某個文化特性下的數字格式。
- [System.Globalization.DateTimeFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo)：這個類別用來表示某個文化特性下的日期和時間格式

下面例子，我們簡單設計一個用來表示攝氏溫度的型別，並繼承 IFormattable 介面。  繼承 IFormattable 介面就必須實作 ToString 方法，我們在這個方法中，依不同的參數值顯示不同的溫度格式。   
```c#
public class MyTemperature : IFormattable
{
private float m_Temp;
public MyTemperature(float temperature)
{
this.m_Temp = temperature;
}

public string ToString(string format, IFormatProvider formatProvider)
{
if (String.IsNullOrEmpty(format)) format = "C";

switch (format)
{
case "F":
return (((m_Temp \* 9) / 5) + 32) + " °F";
case "C":
return m_Temp.ToString() + " °C";
default:
throw new FormatException("Invalid format string");
}

}
}

private void button11_Click(object sender, EventArgs e)
{
MyTemperature temp = new MyTemperature(32.6f);
Console.WriteLine(temp.ToString());             //PracticeMCTS.Chapter01.Lesson4+MyTemperature
Console.WriteLine(temp.ToString("C", null));    //32.6 °C
Console.WriteLine(temp.ToString("F", null));    //90.68 °F
}
```

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [Boxing 和 Unboxing (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/yz2be5wk)
- [使用轉換運算子 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/85w54y0a.aspx)
- [轉換運算子 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/09479473.aspx)