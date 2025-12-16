---
title: 裝飾者模式
layout: default
parent: DesignPattern
nav_order: 4
description: "裝飾者模式"
date: 2016-04-06
tags: [DotNetFramework,DesignPattern]
postid: "5665318175421406915"
---
如果要對一個特定類別擴充新的功能，在不修改原始類別的前題下，通常我們會選擇使用繼承方式來建立一個新的類別。  例如一個類別叫做漢堡，價格 20 ，若是要製做漢堡蛋，就可以繼承漢堡，然後變更售價的屬性。  但這樣子的做法，若有多種添加副食，且允許使用者做任意選擇組合，就會變的相當難以維護。  另外一種方式就是透過**裝飾者模式（Decorate Pattern）**來處理，它比繼承方法的優勢就在於彈性，而且符合[「開放-封閉原則」（Open/Closed Principle, OCP）](https://en.wikipedia.org/wiki/Open/closed_principle)的程式架構。  
```c#
//主食(被裝飾者)
public abstract class Breakfast
{
protected string description;
public abstract string Description();
public abstract int Cost();
}

//主食1
public class Toast : Breakfast
{
public override string Description()
{
return "土司";
}
public override int Cost()
{
return 15;
}
}

//主食2
public class FanTuan : Breakfast
{
public override string Description()
{
return "飯團";
}
public override int Cost()
{
return 25;
}
}

//配料(裝飾者)  裝飾者類別必須繼承自被裝飾者,且亦為抽象類別
public abstract class SubFoodDecorator : Breakfast
{
//有必要做異動的功能, 必須設定為 abstract override , 以便在子類別中重新實作
public abstract override string Description();
public abstract override int Cost();
}

public class Cheese : SubFoodDecorator  //起司是一個裝飾者，所以繼承自抽象的裝飾者類別
{
private Breakfast _breadfast;

public Cheese(Breakfast b)
{
_breadfast = b;
}

public override string Description()
{
return "起士" + _breadfast.Description();
}

public override int Cost()
{
return 10 + _breadfast.Cost();
}
}

public class Pork : SubFoodDecorator  //起司是一個裝飾者，所以繼承自抽象的裝飾者類別
{
private Breakfast _breadfast;

public Pork(Breakfast b)
{
_breadfast = b;
}

public override string Description()
{
return "肉片" + _breadfast.Description();
}

public override int Cost()
{
return 30 + _breadfast.Cost();
}
}

public class Egg : SubFoodDecorator  //火腿是一個裝飾者，所以繼承自抽象的裝飾者類別
{
Breakfast _breadfast;

public Egg(Breakfast b)
{
_breadfast = b;
}

public override string Description()
{
return "蛋" + _breadfast.Description();
}

public override int Cost()
{
return 5 + _breadfast.Cost();
}
}
```

使用時，就可以參考以下範例，這個方法的好處是，若有新增加的主食或副食，只要新增該項類別，無須調整其他的程式碼。  它的擴增功能是動態加入的，而非事先定義在該類別中。  
```c#
Breakfast breadfast1 = new FanTuan();
breadfast1 = new Egg(breadfast1);
Console.WriteLine("{0} {1}元", breadfast1.Description(), breadfast1.Cost());
//蛋飯團 30元。

Breakfast breadfast2 = new Toast();
breadfast2 = new Pork(breadfast2);
breadfast2 = new Egg(breadfast2);
breadfast2 = new Cheese(breadfast2);
Console.WriteLine("{0} {1}元", breadfast2.Description(), breadfast2.Cost());
//起士蛋肉片土司 60元
```
## 參考資料  

- 
- 
-