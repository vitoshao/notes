---
title: 工廠模式
layout: default
parent: DesignPattern
nav_order: 1
description: "工廠模式"
date: 2015-12-09
tags: [DotNetFramework,DesignPattern]
postid: "8517974581384193481"
---
##### 工廠模式

當系統中有多個類似的物件，而你隨時可能要建立這些物件的實體，但是到底要 new 哪一個物件，卻必須等到執行階段才能知道，這時候就適合使用工廠模式。  這個模式利用一個稱為 Factory 的元素來執行實際建立實體的工作，同時也讓程式架構符合[「開放-封閉原則」（Open/Closed Principle, OCP）](https://en.wikipedia.org/wiki/Open/closed_principle)。  

# 一般情況

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDHTfNwynZ6OXs9xtewpnBGG1umYwpJU3JZJle4DTlvViXR2W_J9ZKQeQ1QFto-Z8uQIeO2AUKwAeqkeMpqBO6XyEHdKtwTiJkjUvKuC6QgWjwvBtEitAcgMyunYCIz4cj8WtQiJXwnEI/s0/NonFactoryPattern.png)

在一般情況下，我們要建立物件實體，只要透過"new"就可以了，如下面程式碼範例。  這一段程程式碼建立一個 DailyTrade 物件，可以自網路上下載收盤交易資訊；同時也建立一個 BigTrade 物件，可以自網路上下載巨額交易資訊。  你可能設計了許多類似的物件，可以用來下載不同類型資訊，因此你都必須先 new 該物件的實體才能進行動作，可是如果需求必須等到執行階段才決定要 new 哪一個物件實體的話，那麼這種做法將會變的很難管理。  
```c#
public interface ITradeProvider
{
void DownloadData();
void ImportData();
}

public class DailyTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download DailyTrade");
}

public void ImportData()
{
Console.WriteLine("Import DailyTrade");
}
}

public class BigTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download BigTrade");
}

public void ImportData()
{
Console.WriteLine("Import BigTrade");
}
}
```
```c#
ITradeProvider trade = null;

trade = new DailyTrade();
trade.DownloadData();
trade.ImportData();

trade = new BigTrade();
trade.DownloadData();
trade.ImportData();
```

# 簡單工廠模式（Simple Factory Pattern）

簡單工廠模式將類別的實體化動作封裝在 Factory 內，讓程式可以在執行時才決定要實體化哪一個類別，如下圖所示。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIj70FRogBTgbvn3GG50s4DAxBguQA8MYWK4uVyauW473tZQTtuNq1AhlRYVYNKW9i1bGpSaRpZk3curSJaqH268R4ysHWruondHbc9zPtKTFDs43z9jq4TdKXM1U-h7ssf9u7pBuWpTw/s0/SimpleFactoryPattern.png)
```c#
public interface ITradeProvider
{
void DownloadData();
void ImportData();
}

public class DailyTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download DailyTrade");
}

public void ImportData()
{
Console.WriteLine("Import DailyTrade");
}
}

public class BigTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download BigTrade");
}

public void ImportData()
{
Console.WriteLine("Import BigTrade");
}
}
public class Fctory      {          public static ITradeProvider CreateProduct(string TypeName)          {              ITradeProvider obj = null;              switch (TypeName)              {                  case "DailyTrade":                      obj = new DailyTrade();                      break;                  case "BigTrade":                      obj = new BigTrade();                      break;                  default:                      throw new Exception("type undefined..");              }              return obj;          }      }  ```
```c#
ITradeProvider trade = null;

trade = Fctory.CreateProduct("DailyTrade");
trade.DownloadData(); 
trade.ImportData(); 

trade = Fctory.CreateProduct("BigTrade");
trade.DownloadData(); 
trade.ImportData(); 
```

上面程式碼可以看的出來，當 Client 在建立物件實體時，可以利用一個字串變數，就可以在執行階段決定要實體化的類別。

### 使用 Reflection 設定 Factory

上述的 Fctory 類別中，使用 switch case 用來判斷要建立的實體類型，如果系統新增一個新的物件，那麼這段程式碼也就必須加以修改，這還是違反了&lt;u&gt;開放-封閉原則&lt;/u&gt;。  這時候可以利用 [Reflection](http://vito-note.blogspot.com/2012/03/blog-post_21.html) 機制，直接依物件名稱來建立物件實體，底下示範如何使用 Reflection 建立實體。  
```c#
public class Fctory2
{
private static readonly string AssemblyName = "DesignPatterns";
private static readonly string Namespace = "FactoryPattern.SimpleFacoryPattern";
public static ITradeProvider CreateProduct(string TypeName)
{
string className = AssemblyName + "." + Namespace + "." + TypeName;
return (ITradeProvider)Assembly.Load(AssemblyName).CreateInstance(className);
}
}
```

# 工廠方法模式（Factory Method Pattern）

雖然在上面的簡單工廠模式中，利用 Reflection 機制來修改 Factory 以達到 OCP 原則，但是這個做法僅限程式語言有 Reflection 機制才行。  

所以，如果要滿足程式架構的 OCP 原則，就要利用「工廠方法模式」來解決，它的做法是將 Factory 類別抽象化，讓每個 Product 子類別都有屬於自己的工廠類別。  它的優點是讓程式可以容易擴充新的功能，但是不用去修改到舊有的程式碼。如下圖所示。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbwutKLcfnozHbPx0bpt7xtCQvvPjCDHD752PgmdgHT0YX-WRv__D7IT-ZIfHFcNzUhbN6Qz2z_nJzvxPF6ODpk3QNjHNwvUOefYlsGvhWWY_I8Vd1I_mk9lBmrp0pfTf_PZOhLyGGpPQ/s0/FactoryMethodPattern.png)
```c#
public interface ITradeProvider
{
void DownloadData();
void ImportData();
}

public class DailyTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download DailyTrade");
}

public void ImportData()
{
Console.WriteLine("Import DailyTrade");
}
}

public class BigTrade : ITradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download BigTrade");
}

public void ImportData()
{
Console.WriteLine("Import BigTrade");
}
}
public interface IFctory      {          ITradeProvider CreateProduct();      }        public class DailyTradeFctory : IFctory      {          public ITradeProvider CreateProduct()          {              return new DailyTrade();                  }      }        public class BigTradeFctory : IFctory      {          public ITradeProvider CreateProduct()          {              return new BigTrade();          }      }  ```
```c#
ITradeProvider trade = null;

IFctory factory1 = new DailyTradeFctory();
trade = factory1.CreateProduct();
trade.DownloadData();
trade.ImportData();

IFctory factory2 = new BigTradeFctory();
trade = factory2.CreateProduct();
trade.DownloadData();
trade.ImportData();
```

「工廠方法模式」解決了「簡單工廠模式」的擴充功能，當有新的物件類型要增加時（例如新的 FBTrade），只需要加入一個 FBTrade 和 FBTradeFactory 類別即可，而不用去修改舊有的程式碼。

# 抽象工廠模式（Abstract Factory Pattern）

假設我們系統需求要設計一個交易資訊下載，包含**上市股票**的&lt;u&gt;每日交易資訊&lt;/u&gt;，&lt;u&gt;巨額交易&lt;/u&gt;，&lt;u&gt;融資融券&lt;/u&gt;，&lt;u&gt;法人交易&lt;/u&gt;。  如果現在又多了另一組資料來源，叫做**上櫃股票**，同樣包含這些資訊，而且可能日後還有另外的資料來源，那麼這時候就可能使用「抽象工廠模式」來設計這樣子的需求。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjws0NCPjnoFvhB5374Q0mUm7RNPKMcjpubZYEy5ssjv3IH3gNRQX8vTQdDb9N8qkxpvgfIZqn4VTsbUYkngcYM52T0Zfn3qbqHzOo3NNbEFbTwNkWPxmY0LSpwr578uQV4vPSd_-boBV4/s0/AbstractFactoryPattern.png)
```c#
public interface IDailyTradeProvider
{
void DownloadData();
void ImportData();
}

public interface IBigTradeProvider
{
void DownloadData();
void ImportData();
}

public interface IAbstractFactory
{
IDailyTradeProvider getDailyTrade();
IBigTradeProvider getBigTrade();
}

// 第一組

public class TweDailyTrade : IDailyTradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download Twe DailyTrade");
}

public void ImportData()
{
Console.WriteLine("Import Twe DailyTrade");
}
}

public class TweBigTrade : IBigTradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download Twe BigTrade");
}

public void ImportData()
{
Console.WriteLine("Import Twe BigTrade");
}
}

public class TweTradeFactory : IAbstractFactory
{
public IDailyTradeProvider getDailyTrade()
{
return new TweDailyTrade();
}

public IBigTradeProvider getBigTrade()
{
return new TweBigTrade();
}
}

// 第二組

public class OtcDailyTrade : IDailyTradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download Otc DailyTrade");
}

public void ImportData()
{
Console.WriteLine("Import Otc DailyTrade");
}
}

public class OtcBigTrade : IBigTradeProvider
{
public void DownloadData()
{
Console.WriteLine("Download Otc BigTrade");
}

public void ImportData()
{
Console.WriteLine("Import Otc BigTrade");
}
}

public class OtcTradeFactory : IAbstractFactory
{
public IDailyTradeProvider getDailyTrade()
{
return new OtcDailyTrade();
}

public IBigTradeProvider getBigTrade()
{
return new OtcBigTrade();
}
}
```
```c#
IAbstractFactory     factoy;
IDailyTradeProvider tradeA;
IBigTradeProvider   tradeB;

factoy = new TweTradeFactory();

tradeA = factoy.getDailyTrade();
tradeA.DownloadData();
tradeA.ImportData();

tradeB = factoy.getBigTrade();
tradeB.DownloadData();
tradeB.ImportData();

factoy = new OtcTradeFactory();

// 底下這段程式和上面一段一模一樣，但是得到結果是完全不一樣的，
// 這個模式很方便將整組物件一起抽換的情境。
tradeA = factoy.getDailyTrade();
tradeA.DownloadData();
tradeA.ImportData();

tradeB = factoy.getBigTrade();
tradeB.DownloadData();
tradeB.ImportData();
```
## 參考資料  

- [Factory pattern 工廠模式](http://blog.amowu.com/2009/08/factory-pattern.html)
- [設計模式-Abstract Factory抽象工廠模式](http://hycleo.logdown.com/posts/257304--abstract-factory-design-patterns-abstract-factory-pattern)
- [設計模式：抽象工廠模式 (Abstract Factory Pattern)](http://xyz.cinc.biz/2013/07/abstract-factory-pattern.html)
- [\[Design Pattern\] 工廠方法模式 (Factory Method Pattern) 各自的飲料工廠](https://www.dotblogs.com.tw/joysdw12/2013/09/12/design-pattern-factory-method-pattern)
- 
-