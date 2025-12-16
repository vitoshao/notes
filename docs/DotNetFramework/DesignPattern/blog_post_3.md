---
title: 倉儲模式
layout: default
parent: DesignPattern
nav_order: 3
description: "倉儲模式"
date: 2016-02-15
tags: [DotNetFramework,DesignPattern]
postid: "3839843203316456135"
---
Repository Pattern (倉儲模式)最主要是定義如何切割 BLL 層跟 DAL 層之間的相依性，讓 BLL 層不用依賴於 DAL 層的實做。並且在有需要更換 DAL 目標的時候，可以有抽換 DAL 層的能力。  

# 相依性的問題

底下類別圖用來描述密碼驗證過程中三個相關連的物件。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRFwCCUcUg41LDN181B3rFGyo74NGL69aIHauvD2VPe2uRl8Sh4LDv5fGKBrBlUDztQrbLVuvVeLd39QrGwj8_zy6mQGx0RO0oOmXrSO2Wu8e4dASiag8wvX0y4BVOujjoIKqbOp3kBFo/s0/repository-01.png)
```c#
public class Validation
{
public bool CheckAuthentication(string id, string password)
{
// 根據 UI 輸入的 ID 取得資料庫中已加密的密碼
Account account = new Account();
string pwd_db = account.GetPassword(id);
// 將 client 端傳進來的密碼執行加密運算
Hash hash = new Hash();
string pwd_ui = hash.Encrypt(password);
// 比對以上二個步驟的結果
return pwd_db == pwd_ui;
}
}
public class Account
{
internal string GetPassword(string id)
{
//連接DB
throw new NotImplementedException();
}
}
public class Hash
{
internal string Encrypt(string password)
{
//MD5碼編
throw new NotImplementedException();
}
}
```

上面這個例子，用戶端要使用時只需這麼叫用：  
```c#
Validation validation = new Validation();
string id = "";
string password = "";
var result = validation.CheckAuthentication(id, password);
```

在 CheckAuthentication 方法裡頭，它的邏輯很簡單，取得密碼，取得 Hash 結果，比對二者是否相同。  程式碼本身符合**單一職責原則**，將不同的職責，交由不同的物件負責，再透過物件之間的互動來滿足使用者需求。  不過在這段程式碼裡頭， CheckAuthentication 方法本身其實根本無須在乎 Account 以及 Hash 物件，因為那不屬於它的商業邏輯。  如果你要執行或測試 CheckAuthentication 方法，就勢必連同 Account 以及 Hash 物件都必須一起完成測試，這帶表它們之間存在著緊密的相依性。

# 隔離物件之間的相依性

要隔離物件之間的相依性，主要原則就是不要在目標物件中直接初始化相依物件。  也就是不要直接使用 new 建立一個實例，而是將相依物件抽離出來，使用**介面**來取代。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggj-5mKloBfgh_THRCM5fvAvyJlWlA-zcUGU63Z3gvYfzoo5-tljHHoIC8SGx0HfkhDFq5bThEAy2RmDdDrQjGLLABDj72ngC_ixNuh3W0S7RZWrQligc9Y4Y-BzYoIqfcyrZKN52hk3g/s0/repository-02.png)
```c#
public interface IAccount
{
string GetPassword(string id);
}
public interface IHash
{
string Encrypt(string id);
}
public class ValidationRepository
{
public bool CheckAuthentication(string id, string password)
{
//取得 user 加密後密碼
IAccount account = new Account();
string pwd_db = account.GetPassword(id);
//將 client 端傳進來的密碼進行 md5 編碼
IHash hash = new Hash();
string pwd_ui = hash.Encrypt(password);
// 比對hash後的密碼，與資料中的密碼是否吻合
return pwd_db == pwd_ui;
}
}
public class Account : IAccount
{
public string GetPassword(string id)
{
throw new NotImplementedException();
}
}
public class Hash : IHash
{
public string Encrypt(string id)
{
throw new NotImplementedException();
}
}
```

上面這個方法，就是就是所謂的「依賴注入」（Dependency Injection），  它將相依性由原本目標物件內，轉移到目標物件之外，所以也稱作「控制反轉」，也就是 IoC （Inversion of Control）。  如此一來，目標物件就可以專注於自身的商業邏輯，而不直接相依於任何實體物件，僅相依於介面。而這也是目標物件的擴充點，或是接縫，提供了未來實作新的物件，來進行擴充或抽換相依物件模組，而不必修改到目標物件的 context 內容。

# 用 Repository Pattern 抽離對 Entity Framework 的依賴

## 參考資料  

- [Architecture Pattern – Repository 實作查詢功能](https://msdn.microsoft.com/zh-tw/library/mt179396.aspx)
- [\[Xian's 學習筆記\]Repostitory Pattern](http://blog.developer.idv.tw/2014/05/repostitory-pattern.html)
- [ASP.NET MVC 專案分層架構 Part.1 初學者的起手式](http://kevintsengtw.blogspot.tw/2012/10/aspnet-mvc-part1.html)
- [用Repository Pattern抽離對Entity Framework的依賴](http://ithelp.ithome.com.tw/question/10157484)
- [\[昏睡領域\]\[Architecture Pattern\] Repository](https://dotblogs.com.tw/clark/2012/04/29/71883)
- [\[阿源哥哥\]3-4 Repository Pattern](http://mvc.keigen.net/2013/12/3-4-repository-pattern.html)
- [\[30天快速上手TDD\]\[Day 5\]如何隔離相依性 - 基本的可測試性](https://dotblogs.com.tw/hatelove/2012/11/13/learning-tdd-in-30-days-day5-how-to-isolate-dependency-the-basic-testability)