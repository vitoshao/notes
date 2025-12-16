---
title: 事件 (Event)
layout: default
parent: Foundation
nav_order: 9
description: "事件 (Event)"
date: 2012-03-14
tags: [DotNetFramework,Foundation]
postid: "2624251376561733832"
---
# 事件說明

#### 什麼是事件

事件是物件的一種機制，當物件在某種條件狀況下，該事件就會被引發。若客戶端程式有訂閱這個事件，該物件就會送出事件引發訊息。   
傳送事件的類別稱為發行者（Publisher）-&gt; Raise Event  
接收事件的類別稱為訂閱者（Subscriber）-&gt; Handle Event  

事件是物件的一種機制，比較學術的說法則是，事件是一種特殊的委派方法。通常可由二個方面來看事件：發行者和訂閱者。  

- 發行者（Publisher）：引發事件的類別 (Raise Event Class)
- 訂閱者（Subscriber）：接收事件的類別 (Handle Event Class)

#### 委派與事件的關係

當設計一個事件時，我們並不知道訂閱者會用哪一段程式碼來處理事件。  所以在設計事件時，就必須透過委派的協助，利用委派幫我們指向事件要處理的方法(函式)。  所以：  

- 發行者在宣告事件的時候，其回傳型別就必須是一個委派型別。
- 訂閱者在訂閱事件的時候，其處理函式就必須與該委派具有相同的簽名(Signature：也就是要有相同的**回傳值**和**參數列**)。

#### 事件的宣告
```c#
[存取修飾詞] delegate 回傳型別 委派名稱 ([參數列]);
[存取修飾詞] event 　 委派名稱 事件名稱;
```
```c#
//宣告一個委派
public delegate void SampleEventHandler(object sender, SampleEventArgs e); 

//宣告一個事件
public event SampleEventHandler SampleEvent;        //指明以 SampleEventHandler 這個委派型別為基礎
```

#### 觸發事件

觸發事件是發行者要做的事，要注意的是，當程式碼要引發事件的時候，必須先判斷該事件值是否為 null ，因為訂閱者不見得有訂閱這個事件，所以必須用 null 判斷。
```c#
if (SampleEventHandler != null)
SampleEventHandler (this, e);    
```

#### 訂閱事件

訂閱事件是訂閱者要做的事，主要可分成二個部份：

#### Step1：撰寫事件處理函式：這個函式的簽章 (Signature) 必須與事件的委派簽章相符 (相同的回傳值，相同的參數列)。
```c#
void HandleSampleEvent(object sender, CustomEventArgs a)
{
// Do something useful here.
}
```

#### Step2：訂閱事件：在適當的地方，撰寫訂閱事件程式碼。
```c#
// １) 訂閱事件
// 使用加法指派運算子 (+=) 將事件處理常式附加到事件上
publisher.SampleEvent += HandleSampleEvent;

// 2) 取消訂閱事件
// 使用減法指派運算子 (-=) 移除附加在事件上的事件處理常式
publisher.SampleEvent -= HandleSampleEvent;
```

# 實例演練一

下面範例我們定義了一個類別，這個類別有一個事件 MyWarningEvent ，一個屬性值 Count 。  透過 Increment 和 Decrement 方法可以增減 Count 值。  當 Count 值大於 10 或小於 0 時，則引發 MyWarningEvent 事件。

#### 傳送事件的類別（Publisher）
```c#
public class MyEventArgs : EventArgs
{
public int Count = 0;
public MyEventArgs(int _count)
{
Count = _count;
}
}

public class MyClass
{
private int Count = 0;

public delegate void MyEventHandle(object sender, MyEventArgs e);
public event MyEventHandle MyWarningEvent;

//====================================================================
// 將事件引發包裹在一個 protected virtual method 中
// 這樣子它的衍生類別才能覆寫這個方法中的行為
// 否則像下方的 Decrement 方法中，直接在該方法中將事件引發即可
//====================================================================
protected virtual void RaiseMyEvent(int _count)
{
if (MyWarningEvent != null)
{
MyWarningEvent.Invoke(this, new MyEventArgs(Count));
}
}

public void Increment()
{
Count++;
if ((Count > 10) || (Count < 0))         //引發事件
{
RaiseMyEvent(Count);
}
}

public void Decrement()
{
Count--;

if ((Count > 10) || (Count < 0))         //引發事件
{
if (MyWarningEvent != null)
{
MyWarningEvent.Invoke(this, new MyEventArgs(Count));
}
}
}
}
```

#### 接收事件的類別（Subscriber）
```c#
MyClass obj = new MyClass();

//事件處理方法
private void On_MyEvent(object sender, MyEventArgs e)
{
Console.WriteLine(e.Count);
}

//訂閱事件
private void button1_Click(object sender, EventArgs e)
{
obj.MyWarningEvent += On_MyEvent;
}

//取消訂閱事件
private void button2_Click(object sender, EventArgs e)
{
obj.MyWarningEvent -= On_MyEvent;
}

private void btnIncrement_Click(object sender, EventArgs e)
{
obj.Increment();
}

private void btnDecrement_Click(object sender, EventArgs e)
{
obj.Decrement();
}
```

# 實例演練二

下面範例我們設計了一個使用者自訂控制項，它具有二個事件：小偷入侵、發生火災。同時我們用二個按鈕，分別來摸擬當這二個事件的發生。  

#### 傳送事件的類別（Publisher）
```c#
public delegate void Alarm_Handler();               //宣告事件的 Delegate 型別

public partial class ucAlarm : UserControl
{
public event Alarm_Handler ThiefBreakInto;      //宣告事件 ThiefBreakInto 小偷入侵    
public event Alarm_Handler Fire;                //宣告事件 FireOn 火燒

public ucAlarm()
{
InitializeComponent();

btnThief.Click += new System.EventHandler(this.On_ThiefBreakInto);  //摸擬發生小偷入侵事件        
btnFire.Click += new System.EventHandler(this.On_Fire);             //摸擬發生火災事件
}

private void On_Fire(object sender, EventArgs e)
{
if (Fire != null)                           //當事件發生時,觸發"火災"事件            
Fire.Invoke();
}

private void On_ThiefBreakInto(object sender, EventArgs e)
{
if (ThiefBreakInto != null)                 //當事件發生時,觸發"小偷入侵"事件            
ThiefBreakInto.Invoke();
}
}
```

#### 接收事件的類別（Subscriber）

訂閱者使用加法指派運算子 (+=) 訂閱事件，一個事件是可以同時委派多個處理方法的。  
```c#
public partial class Form2 : Form
{
public Form2()
{
InitializeComponent();

//1.1) 訂閱ThiefBreakInto事件 
alarm1.ThiefBreakInto += alarm1_ThiefBreakInto; //使用加法指派運算子 (+=) 將事件處理常式附加到事件上

//1.2) 訂閱Fire事件                                                     
alarm1.Fire += alarm1_Fire;                     

// 再訂閱Fire事件，執行 Call_Boss　(一個事件是可以委派多個方法的)
alarm1.Fire += Call_Boss;
}

//定義事件處理常式方法，其簽章 (Signature) 與事件的委派簽章相符。 
private void alarm1_ThiefBreakInto(DateTime dt)
{
Console.WriteLine("Call 110 : " + System.DateTime.Now.ToString());
}

private void alarm1_Fire(DateTime dt)
{
Console.WriteLine("Call 119 : " + System.DateTime.Now.ToString());
}

private void Call_Boss(DateTime dt)
{
Console.WriteLine("Call Boss : " + System.DateTime.Now.ToString());
}
}
//結果顯示
//Call 110 : 2012/8/8 上午 09:51:12
//Call 119 : 2012/8/8 上午 09:51:15
//Call Boss : 2012/8/8 上午 09:51:15
```

#### 上例補充說明：

我們在上面例子中，針對 ThiefBreakInto 這個事件，總共加入了２個處理方法。  
當委派的方法被 invoke 時，會依方法加入的先後順序執行，如上例的結果顯示。  
如果被呼叫的方法有回傳值的話，那麼上面的做法就只能取到最後一個方法的回傳值。  
若需求狀況有必要取得所有方法的回傳值的話，就必須利用 [GetInvocationList](http://msdn.microsoft.com/zh-tw/library/system.delegate.getinvocationlist.aspx) 方法，取得委派的引動清單後，再分開 invoke 每一個方法，這樣就可以取得每一次的回傳值。  
```c#
//利用 GetInvocationList ，分開invoke
private void On_ThiefBreakInto(object sender, EventArgs e)
{
if (ThiefBreakInto != null)
{
foreach (Alarm_Handler handler in ThiefBreakInto.GetInvocationList())
{
handler();
}
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaNqJjVhTbFz7DKMaTLpTsqVBf78fxZp_GJyZeTQX-cMYTbDt3nLUmNlpLg7jDeJCT5MJHKSLmZWydMPzBfPzT9eJMSa-jd7FZpabw9NbN19Ao_ASA01Vcq_FB-1GPGBhMatydQpITOCg/s545/delegate_GetInvocationList.png)