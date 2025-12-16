---
title: 抽象、介面、委派、事件
layout: default
parent: Foundation
nav_order: 17
description: "抽象、介面、委派、事件"
date: 2014-08-27
tags: [DotNetFramework,Foundation]
postid: "7863929131191885847"
---
要使用 C# 設計物件導向，不得不搞懂以下幾個技巧：  

- 抽象類別（abstract class）：
- 介面（interface）：
- 委派（delegate）：
- 事件（event）：

# 抽象類別

如果要設計一個系統，其中會包含數個相似的類別，且這些類別具有許多共通的特性。  這時你就可以建立一個類別，用來定義這些共通的特性，並做為這些類別的父類別，這就是「基底類別」。  有時候在基底類別中，我們僅定義了某些方法名稱，並未實作其中的邏輯，希望由各個延申類別再去實作，這時就可以將此類別設計成「抽象的基底類別」。  

抽象類別有以下幾個特性：

- 抽象類別不能被 new 出實體。
- 抽象類別一定要被繼承後，才可以使用到它內部的功能。
- 若抽象類別定義了抽象方法，繼承它的子類別一定要實作這個方法。
- 抽象類別並沒有要求所有成員都要是抽象的，但是若類別中有任何一個抽象成員，則該類別必須為抽象類別。

抽象方法有以下幾個特性：

- 抽象方法只能定義在抽象類別中。
- 抽象方法不能使用 private 存取修飾詞。
- 若繼承抽象類別的子類別沒有實作全部的抽象方法，那麼該子類別就必須宣告為抽象類別。

### 範例練習

下面這個例子，因為台灣人、日本人、美國人這三種類別，都有共同的屬性和方法，所以我們使用 Human 這個抽象類別，把這些共同的屬性和方法獨立出來。  另外，因為 SayHello 隨不同類別，必須有各自不同的執行內容，所以就將它定義成一個抽象方法，讓各個類別繼承後，各自去實作。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmLgvlE61V0BaZlb3CZFYkEMJrSKq6o7RmcpdFu-nk9MhmgItKhugl_aUDs1aN3sUI2HzFziq_vPZBapY1XSRfgI2wwVCJckQB09vusdfBaKc2NEV169J0-_tFnlnkxaSoAy5vwtWM1ak/s516/abstract-class-inherience.png)
```c#
public abstract class Human
{
private string name;
private DateTime birthday;

public string Name { get; set; }
public DateTime Birthday { get; set; }

public void Sleep() { Console.WriteLine("zZＺ"); }
public abstract void SayHello();
}

public class Taiwanese : Human
{
public override void SayHello()
{
Console.WriteLine("你好");
}
}

public class Japanese : Human
{
public override void SayHello()
{
Console.WriteLine("こんにちは");
}
}

public class American : Human
{
public override void SayHello()
{
Console.WriteLine("Hello");
}
}
```

### 繼承修飾詞

在 C# 中，與類別繼承相關的修飾詞 (Modifier) 共有三個：

- abstract ：**抽象類別**只是當做其他類別的基底類別。
- sealed ：**密封類別**可防止其他類別繼承該類別。
- static ：**靜態類別**不能具現化 (Instantiated)，也不能被繼承。
- 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghKUMJrsdOi7zfjAvGQRJm5Jx_jZVoG43TCvdzEdHeEhlNuiX2Zw119PWOQm0eycnQymvkq7BhczZEJm5IE2keSxTs7BvNB5vvwrquQashl3NFDDTQShJg92WIhY5H0Wp2qVM4zhPVKXY/s295/inherience-modifier-class.png)

而方法的修飾詞則有四個：

- abstract ：**抽象方法**必須在子類別中被實作。
- virtual ：**虛擬方法**允許在子類別中被覆寫。
- override ：宣告這個方法是覆寫父類別中的抽象方法或虛擬方法。
- static ：**靜態方法**不屬於任何執行個體，必須由類別名稱直接取得。
- 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiHTthfWkuWYXaYeZ7TY2G-m15dzjBbS8xDrl9ZcRfC_aQqXe8vi2HM1G6l4Z38K18H0Ny2SVPPTxvDUHqdQi5nYRx7SrbrFFT-V2NfUGlDK6mNUoWY3vhK71nbwbgI_uz6EeUthYM0R9Q/s253/inherience-modifier-method.png)

下面程式碼，比較「方法」、「虛擬方法」、「抽象方法」在繼承關係中的不同：
```c#
//抽象類別
abstract class AbsClass
{
public void pubMethod() { }
public virtual void virMethod() { }     //virtual： 虛擬方法
public abstract void abcMethod();       //abstract： 抽象方法 -> 只能定義,不能有內容
}

//一般類別
class GenClass
{
public void pubMethod() { }
public virtual void virMethod() { }
//public abstract void abcMethod();     //抽象方法只能在抽象類別中定義
}

//繼承一般類別
class MyClass1 : GenClass
{
//1. 在繼承一般類別時，一般的方法不可以被覆寫
//public override void pubMethod()｛ ｝

//2. 但是，可以用 new 修飾詞來隱藏父類別中的這個方法，
public new void pubMethod()｛ ｝

//3. 父類別中的虛擬方法才可以被覆寫
public override void virMethod()｛ ｝
}

//繼承抽象類別
class MyClass2 : AbsClass
{
//4. 繼承抽象類別, 就一定要實作它的抽象方法, 同樣是以覆寫的方式來做.
public override void abcMethod() { }
}
```

那麼 override 和 new 最大的差異點在哪？請看以下程式碼：
```c#
public class BaseForm
{
public void Query()
{
GetData();
PrintData();
}
public virtual void GetData()
{
Console.WriteLine("BaseForm GetData");
}
public virtual void PrintData()
{
Console.WriteLine("BaseForm PrintData");
}
}

public class OrderForm : BaseForm
{
//使用 override 
public override void GetData()
{
Console.WriteLine("OrderForm GetData");
}
//使用 new 
public new void PrintData()
{
Console.WriteLine("OrderForm PrintData");
}
}

static void Main(string[] args)
{
OrderForm form1 = new OrderForm();
form1.Query();
Console.ReadLine();
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-Kqw5rZ0kt9a6psVWKKjZD3vuP0KIb23bQgVOT10KxizONhGzH1jc-qhGbmYo8i3o-PH8BPRw3z0OD5RuDqdJGUfRQhOeyRLP5zL5ZDDASa7ltWqLs9lRRlOYMPgSHnOZi6ARRuHw86E/s216/override-and-new.png)

在上面例子中叫用了 Query() 方法，該方法會先後叫用 GetData() 與 PrintData() 方法。  因為 OrderForm.GetData() 覆寫了 BaseForm.GetData()，所以其輸出結果不意外。  但是 OrderForm.PrintData() 在定義時使用了 new 關鍵字，所以只會隱藏 BaseForm.PrintData()，  當程式執行時，會叫用離最近的方法，所以執行的便是 BaseForm.PrintData() 。  另外，如果想在子類別中叫用父類別中的方法，則可以使用 base.GetData() 的方式，去叫用父類別中的函式。  

在 Vb.NET 中，同樣也可以實作覆寫與隱藏（遮蔽 Shadowing）功能，程式碼如下：
Class BaseForm

Sub Query()
GetData()
End Sub

Overridable Sub GetData()
Console.WriteLine("BaseForm GetData")
End Sub

Overridable Sub PrintData()
Console.WriteLine("BaseForm PrintData")
End Sub

End Class

Class OrderForm
Inherits BaseForm

'使用 OverRides
Overrides Sub GetData()
Console.WriteLine("OrderForm GetData")
End Sub

'使用 OverLoads
Overloads Sub PrintData()
Console.WriteLine("OrderForm PrintData")
End Sub

End Class

Sub Main()
Dim form1 = New OrderForm
form1.Query()
Console.ReadLine()
End Sub

# 介面

如果要設計一個系統，其中包含多個不同的類別，但是這些類別具有部份相同名稱的功能。  這時你就可以建立一個介面，用來定義這些相同名稱的功能。  這麼設計的好處是，只要有實作這個介面的類別，都會具有這些功能。  

介面有以下幾個特性：

- 可視為沒有 construct 的自訂類別。
- 類別中僅定義 member 名稱，沒有內容。
- 實作這個介面的類別，必需支援該介面的所有成員。

介面與抽象類別有下列幾點不同：

- 介面可包含方法、屬性、事件、索引子；不能包含常數、欄位、運算子、執行個體建構函式。
- 介面裡的method必須全部都定義成 abstract 。
- 介面裡的method必須全部都定義成 public 。  
在 c# 中，method 的預設修飾詞就是使用 public abstract ，所以可以省略。

### 範例練習

下面這個例子，我們定義了一個 IMessage 介面，介面中包含了 Creating 和 Sending 二個成員。  因為手機、即時通、電子郵件這三個類別都實作這個介面，所以它們都必須實作這二個方法。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-LFT-6P-1lwBk7N_FL0RUfwdXNOajSBFadxnKmh2LJCHYx9SJSzinSC15OoCmgz8ce1O_O0zmwt0m26U9DGXX3pcnSVLxFpDc_s9tHyfLB7m6Avk_Z-pW_XaDea3PmoQglHBBTMq3nwE/s549/interface-inherience.png)
```c#
public interface IMessage
{
void Creating();
void Sending();
}
```
```c#
public class Email : IMessage
{
public void Creating()
{
Console.WriteLine("Creating an Email Message");
}
public void Sending() 
{
Console.WriteLine("Sending an Email Message");
}
}

public class Mobile : IMessage
{
public void Sending()
{
Console.WriteLine("Sending a Mobile Message");
}
public void Creating()
{
Console.WriteLine("Creating a Mobile Message");
}
}

public class Skype : IMessage
{
public void Sending()
{
Console.WriteLine("Sending a Skype Message");
}
public void Creating()
{
Console.WriteLine("Creating a Skype Message");
}
}
```

### 多重繼承

在 C# 中，當使用類別的繼承功能時，一次只能繼承一個類別，可以當實作介面時，一次可以實作多個介面。  
```c#
public interface IContact       {          void SaveContact();          void FindContact();      }  public interface IMessage
{
void Creating();
void Sending();
}
public class Email : IMessage, IContact      {          public void Creating()          {              Console.WriteLine("Creating an Email Message");          }          public void Sending()           {              Console.WriteLine("Sending an Email Message");          }          public void SaveContact()          {              throw new NotImplementedException();          }          public void FindContact()          {              throw new NotImplementedException();          }      }  ```

### 介面重構

既然 interface 是幾個不同物件的共同介面，如果你已經設計好了一個類別，才發覺這個類別中的幾個方法，必須獨立定義到 interface 中，因為會有好幾個不同的類別也都必須實作這些方法。  這時候你就可以使用 VS 2010 內建的「[重構](http://msdn.microsoft.com/zh-tw/library/719exd8s.aspx)」工具。「重構」工具包含以下功能：  

- 重新命名：對某個方法重新命名。
- 擷取方法：將某段程式碼獨立到副程式中。
- 擷取介面：將某些方法名稱，獨立到介面中。
- 重排參數列：重新排列方法中的參數。
- 移除參數：移除方法中的參數。
- 封裝欄位：將 field 封裝到 property 中。

下面示範擷取介面：

1. 標記程式碼。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGItuO93OjT1GxS3Xb0WaQIwlSD27L-RSTpXLXxmjDbPzmQCuSRbKWGlB5ClcRpBSRrCQ_InTKUKuQiHVxVPpj4AJA2Op6m1E_iF64QrXj4p7QXF_HWJ0_ln2biKWH2e_du4H5UFIqCi4/s515/refactor-1.png)

2. 在［功能選單］或［右鍵選單］中，選擇［重構］-&gt;［擷取介面］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZlOOIVNJQwjdX4A0vxgnLSHnMlyQiIFs5E7gdyV4xFG_nKbh3tGCCHhv2jDlO47IIDtLtJ3AnBbEyUYcbV481UkwTujuVC4gRO8nGBMDg3x12LgjEN_Wp9objeCiy2LhhORzCqxC7IBo/s307/refactor-2.png)

3. 設定介面的相關資訊。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvuDY88EoubKcTWPumitXeL7vI_uFMgLXd1eE9RiFY3sj-VyDPiX9wTORtPBJVECljeSbYLPFulBbzy3SLnfhmLoH4RyczOkdZo2hNVo1Nnwcq8F5EMzydbN0-2R7wkz7EwW233qEdyC4/s496/refactor-3.png)

# 委派

通常我們想要執行某個方法，只要直接叫用那個方法的名稱即可。  可是如果你要執行的方法，卻還不知道名稱，這是就可以使用委派。  例如，你要設計一個元件，當元件中某個事件發生時，應該去執行某個方法，但你並不知道以後使用這個元件的人會使用什麼方法名稱，這時你就可以利用委派來處理。  

不管怎樣，只要記住：委派是一個沒有內容的方法，其功能就是要當一個中介者，專門用來協調處理要執行的方法。  

## 宣告委派

即然說，委派是一個沒有內容的方法，那麼它就和方法的宣告方法有點類似，只是沒有內容。

語法：
```c#
[存取修飾詞] delegate 回傳值型別 委派名稱 ( [參數列] )
```

範例：
```c#
public delegate void MyHandler(int x, int y);
```

即然說，委派是專門用來協調處理要執行的方法，但也不是所有方法都來者不拒，必須和他的簽名相同才行。  如上例，該方法就必須帶有二個 int 型別的參數。例如：  
```c#
public void DoWorkA(int a, int b)
{
Console.WriteLine(a + b);
}
public void DoWorkB(int a, int b)
{
Console.WriteLine(a \* b);
}
```

## 使用委派

1. 要使用委派，必須先實體化一個委派，再叫用（invoke）委派，委派就會呼叫封裝的方法。
2. 如果你要執行的方法不止一個，就可以利用「+=」加法指派運算子，將新的方法位置參考加入委派物件。
3. 最後叫用委派，也就是執行委派物件的 Invoke 方法。
```c#
MyHandler handler = new MyHandler(DoWorkA);     //實體化一個委派

handler += DoWorkB;                             //在委派物件中，加入一個新的方法參考

handler.Invoke(2, 3);                           //叫用委派
```

上面這個例子，當叫用委派後，就會將自動去執行 DoWorkA 和 DoWorkB 二個方法，並帶入參數值。

# 事件

「[事件](http://msdn.microsoft.com/zh-tw/library/awbftdfh.aspx)」是什麼，大家應該都很清楚，就是當某件事情發生時，你要程式可以自動去執行某件事。  問題是，事件應該如何定義？還有事件發生時，該如何讓相關的執行工作動起來？  

要定義一個事件其實很簡單，只要透過 event 關鍵字即可。  只是，當事件發生時，我們想要指派一個工作給它，  可是這個工作通常是由訂閱者決定的，也就是必須在具有這個事件的物件在具體化之後才會被決定。  那麼在這個類別中，該如何來定義事件發生時，所要執行的工作？這時候就得利用上面介紹的委派來幫我們做好中介者的角色。  所以定義事件的時候，也要指名委派的名稱。  

## 宣告事件

語法：
```c#
[存取修飾詞] delegate 回傳值型別 委派名稱 ( [參數列] )
[存取修飾詞] event 委派名稱 事件名稱；  ```

範例：
```c#
public delegate void WorkHandler(string msg);
public event WorkHandler WorkComplete;
```

上面例子，定義了一個委派，並且宣告了一個事件，當 WorkComplete 事件發生時，WorkHandler 會幫我們去啟動要執行的工作。

## 使用事件

### 例一：簡單的事件範例
```c#
// 宣告事件public delegate void WorkHandler(string msg);
public event WorkHandler WorkComplete;
// 待處理方法      public void Do_WorkComplete(string msg)      {          Console.WriteLine(msg);      }        // 訂閱事件      private void btnEventSubscribe_Click(object sender, EventArgs e)      {          this.WorkComplete += new WorkHandler(Do_WorkComplete);      }        // 引發事件      private void btnEventPublish_Click(object sender, EventArgs e)      {          this.WorkComplete.Invoke("abc");      }  ```

### 例二：在類別中設計事件

在設計事件時，通常可由二個方面來看事件：發行者和訂閱者。   

- 發行者（Publisher）：引發事件的類別 (Raise Event Class)
- 訂閱者（Subscriber）：接收事件的類別 (Handle Event Class)

#### 觸發事件

觸發事件是發行者要做的事，要注意的是，當程式碼要引發事件的時候，必須先判斷該事件值是否為 null ，因為訂閱者不見得有訂閱這個事件，所以必須用 null 判斷。

#### 訂閱事件

訂閱事件是訂閱者要做的事，主要可分成二個部份：

- Step1：撰寫事件處理函式：這個函式的簽章 (Signature) 必須與事件的委派簽章相符 (相同的回傳值，相同的參數列)。
- Step2：訂閱事件：在適當的地方，撰寫訂閱事件程式碼。

底下這個類別，具有一個 Fire 事件。  它會使用 FireHandler 這個委派來處理事件。  而 FireHandler 委派的簽名是沒有參數，也沒有回傳值。  
```c#
public class FireAlarm
{
public delegate void FireHandler();
public event FireHandler Fire;

//觸發事件
public void Raise_Fire()
{
if (Fire != null)
Fire(); 
}
}
```
```c#
// 1. 撰寫事件處理函式
public void On_Fire()
{
Console.WriteLine("Fire Fire Fire");
}

private void btnEvent_Click(object sender, EventArgs e)
{
// 2. 訂閱事件
alarm.Fire += On_Fire;
}

private void btnFireEvent_Click(object sender, EventArgs e)
{
// 產生一個事件
alarm.Raise_Fire();
}
```