---
title: LINQ to Entity
layout: default
parent: LINQ
nav_order: 4
description: "LINQ to Entity"
date: 2013-04-09
tags: [DotNetFramework,LINQ]
postid: "6026717977242065070"
---
[LINQ to Entities](http://msdn.microsoft.com/zh-tw/library/bb386964.aspx) works in a manner that is similar to that of [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) .   You define a model to represent your application domain.   You then create a map between this model and your actual data source.   You can then load data into the model and query against it by using LINQ.  However, [LINQ to Entities](http://msdn.microsoft.com/zh-tw/library/bb386964.aspx) uses the [ADO.NET Entity Framework](http://msdn.microsoft.com/zh-tw/library/bb399572.aspx) as a basis for the models.   These models can be created for any data source (not just for SQL Server).  

The [ADO.NET Entity Framework](http://msdn.microsoft.com/zh-tw/library/bb399572.aspx) allows you to create a **conceptual model** of your application domain.   This ensures that you write application code against the conceptual domain objects rather than directly against a relational database.   This allows your model and database to evolve independently.   If changes need to be made to either, you only need to change the mappings.   In this way, you can even change your storage schemas without having to change your actual application code.   You would just change the mapping.  

# Entity Framework

**Entity Framework**（也稱做 ADO.NET Entity Framework），是微軟出的 O/R Mapping 解決方法，可減少資料庫系統開發時的程式碼撰寫。  較早期的版本，如 V3.5 和 V4 都隨附在 .NET Framework 中，版本號碼與其所在之架構的版本一致，之後的版本則開始獨立發行。  

- EF 5  
此版本可在 VS2010 和 VS2012 中使用，撰寫以 .NET 4.0 和 .NET 4.5 為目標的應用程式。  不過，在 在 VS2010 （.Net 4.0）中安裝  EF 5　中有效，如
- EF 6  
Entity Framewok 6 以上版本都可以直接由 NuGet 上取得更新。  它可在 VS2012 & S2013 中正常使用，若在 VS2010 中，只可以使用它的 runtime ，無法使用它的開發工具，也就是說不支援 ModelFirst/DatabaseFirst ，僅支援 CodeFirst。

詳細的版本歷程，可參考：[Entity Framework 版本歷程記錄](https://msdn.microsoft.com/zh-tw/data/jj574253.aspx)

## 架構圖

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi97KVahrWgEbgYGO1SXGxXBLe79EUuu9dVTdagY_jCEtSJNbtlF0RQRYskXw0Y_RIkSzgSpoSCcHqTPu7vV5T3xOCNsu1qKNSuFFSQ89y-qh22uWkbYrh7_IhH7o8SLFVH5O4XCQAhR5c/s0/ADO_NET_Entity_Framework_Architecture.png)

# Creating an Entity Model

You create an [Entity Framework](http://msdn.microsoft.com/zh-tw/library/bb399567.aspx) model by using the **ADO.NET Entity Data Model template** from the Add New Item dialog box in Visual Studio.   This file is an **.edmx** file. Like a [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) DBML file, this file has an XML representation of the schema  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEheye1LKv5HW0eEXH_c0fjA-HmarmjEg9wwR_j7R8eO_BbTBN6IEcKCf3K093r99V5x_PpYhqOxgMUNFGB8-QLBRpevJD3lLCFsJcTUIymYSQN2we4BMQq1DdEI0WbXOtzrXmSFUBX1U7w/s562/EntityToSql-add-template.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipqwfvvedeqTx6KpnO_slps774ERIcYINCBQ3lIu_wqJ7fJccZTTY2uk0O9w1CEQBzdKUqe-owEljGD1dzg-kNhoSZSoZs2l9vIpTW9Xq6yn__z6BI7qASoSgYvpA1dTx7WHvYqE6fy9o/s619/EntityToSql-designer.png)

#### 實體資料模型內容 (Entity Data Model , \*.edmx)

底下是一個精簡後的實體資料模型內容
```c#
using System.Data.Objects;
using System.Data.Objects.DataClasses;

namespace Northwind_EDMX
{
public partial class NorthwindEntities : ObjectContext
{
#region AddTo 方法
public void AddToOrder_Details(Order_Details order_Details)
{
base.AddObject("Order_Details", order_Details);
}
public void AddToOrders(Orders orders)
{
base.AddObject("Orders", orders);
}
public void AddToProducts(Products products)
{
base.AddObject("Products", products);
}
#endregion
}

public partial class Customers : EntityObject
{    }
public partial class Employees : EntityObject
{    }
public partial class Order_Details : EntityObject
{    }
public partial class Orders : EntityObject
{    }
}
```

#### Note

這個工具還有其他的功能，例如：由模型反向建立資料庫、與資料來源比對驗證模型的對應資訊、或者建立 stored procedure 對應等。  不過部份功能僅含在 [Visual Studio Feature Pack 2](http://msdn.microsoft.com/zh-tw/library/vstudio/gg269474%28v=vs.100%29.aspx) 之中。  

## 如何在 ADO.NET Entity Data Model 中執行 Stored Procedure

當使用 .Net Framework 4 裡，透過 winzard 建立 ADO.NET Entity Model 時，  系統只會在概念模型（conceptual model）中建立資料表的實體物件，  對於 Store Procedure 或 function 物件，系統只會建立在儲存模型（storage model）中。  若要將預存程序加入到概念模型，必須先執行【加入函式匯入（Function Import）】。  

### 加入函式匯入

加入函式匯入

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjAmIRjDi0QZlpz6ZWFAgaRABdYZsyNBpNH7ZFn1CZB9LP-_Vkqts4sEwnrduKB8yH57qwyROd8I2U0siofoBOzNOy4RW3_wlmz0D4FujJaJ2VrBL7ft08M1pNh48rvwJz52OTkGsbVeNE/s462/function-import-01.png)

Function Import 可以傳回簡單型別、EntityType、ComplexType，或者不傳回值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0BqWvXCfaZBBw4ox7RUvI5bHlgMhhNDedGGg2Jun0Elf0UMdZ1XS6MOBVSkmfnEtAdWAa8_-43__5aXymnls4fzQydkNxGVoAcLJYieQlALTvRlyjhnYkEJRqt26kpOKWVej_1wLyfdo/s515/function-import-02.png)

建立完成後，就可以在概念模型的 /Entity Container/Function Imports 中看到預儲程序的實體物件。  並且在複雜型別中建立該預儲程序的回傳型別。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgRLtCNq2vAkPinQSxXzkMvJNc_IJ4Aqx6Z3uAXsl948Z7cjcsiDSAmBUk53HE0RiL1VccvEKl4DYfD7tq4nsmXIR9N07qgxVXtehWRkFbj6V2bdvPLoAfMUdH3kTZv6QHChwWkydwWeTw/s330/function-import-03.png)

若使用 .Net Framework 5 ，在它的精靈的畫面中，已經包含自動建立 Store Procedure 或 function 到實體模型中的選項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZkqh37sKaSLdJItqPZIeBXIg7XrLoE6lBo8kLTfEu6ggU0t8tkbxgxEvZ5DVkKh-TxgihnAIHfkdScyApMv0UE7F4A2sd3Llbbif6izYPshGU2u8DIsYZBegSaWk0I7sxRyBN6krHwgI/s647/function-import-04.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNYslrIXt25678c6i077i1gazRQBMM1lOFzY8gn1gXa3msJhnZKujM0yCQnSX8okmN1rnfWyV6YcH0IKM6qcf_6Nle_KlQ2GB6BrxcSLAykjpl62MpnQb7AI0dO7HRxDxuhVGUucvsieg/s375/function-import-05.png)

# Writing LINQ to Entities Queries

實體模型(entity model)是由 System.Data.Objects.[ObjectContext](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.aspx) 類別繼承而來的。  它提供以物件的方法來查詢或使用實體資料。  

底下範例示範如何使用 [LINQ to Entities](http://msdn.microsoft.com/zh-tw/library/bb386964.aspx) 執行 CRUD 。

## 查詢
```c#
NorthwindEntities db = new NorthwindEntities();

var query = from emp in db.Employees
where emp.FirstName.Length > 5
select emp;

gv1.DataSource = query;
gv1.DataBind();
```

## 新增
```c#
NorthwindEntities db = new NorthwindEntities();

var emp = new NorthwindDB.Employees();
emp.FirstName = "vito";
emp.LastName = "shao";
emp.Age = 40;

db.Employees.AddObject(emp);
db.SaveChanges();
```

## 修改

當變更模型中資料，實際上變更的是記憶體中的資料，若要保存(Persists)這些變更，必須呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 方法，才會將操作其間所做的異動回存至資料來源。  

實體物件有一個 [EntityState](http://msdn.microsoft.com/zh-tw/library/system.data.objects.dataclasses.entityobject.entitystate.aspx) 屬性，就是用來記錄目前的狀態，回存動作就是依這個狀態值來判斷該回存哪些實體物件。  EntityState 是由 [EntityState](http://msdn.microsoft.com/zh-tw/library/system.data.entitystate.aspx) 列舉值所構成，它包含以下列舉值：  

- Detached(1)   ：當該物件被加入至Context之前。
- Unchanged(2)　：當該物件被載入Context中，或從上一次呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 之後都沒有修改過。
- Added(3)      ：當透過呼叫 [AddObject](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.addobject.aspx) 將物件加入至Contex中。
- Deleted(4)    ：當透過呼叫 [DeleteObject](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.deleteobject.aspx) 將物件由Contex中移除。
- Modified(16)  ：當此物件已變更，但是尚未呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 方法。

當叫用 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 方法時，只有處於 Added、Modified 或 Deleted 狀態的物件會被更新。  

要回存物件中的資料時，可能有以下幾種狀況，必須使用不同的方法處理：

### 連接情況下

連接情況指的是在同一個 Context 之中，修改由資料庫中檢索出來的實體。  這種狀況只要在修改屬性值之後，再叫用 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 方法即可。  
```c#
using (AspNetDB_ObjectContext context = new AspNetDB_ObjectContext())
{
var query = from user in context.Users
where user.UserId >= 2 && user.UserId <= 5
select user;
foreach (var user in query)
{
if ((user.UserId % 2) == 0)
user.LastActivityDate = DateTime.Now;
}
context.SaveChanges();
}
```

上面這個例子，假如符合條件的資料共有四筆，當呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 更新時，只會更新二筆。  而且，因為我們只更動LastActivityDate這個欄位，所以只有這個欄位的狀態是已變更，最後它對資料庫的操作也只會更新這個欄位，其他欄位都不會處理。

### 斷開連接情況下

斷開連接情況指的是要處理的物件不在該 Context 之中。這種狀況有以下幾種處理方法：  

#### 使用 AttachTo 方法

[AttachTo](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.attachto.aspx) 方法會將具有實體索引鍵的物件附加至物件內容，之後若有任何資料變更，必須呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 回存。

注意，叫用 [AttachTo](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.attachto.aspx) 的時機很重要，因為當叫用 [AttachTo](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.attachto.aspx) 時，之前的任何變更，都不會列入追縱的，只有在叫用方法後的變更，才會列入追縱的。  所以下面例子中的 FirstName 都不會被寫入。  
```c#
using (ExtendedAspNetDB context = new ExtendedAspNetDB())
{
EntityUsers user = new EntityUsers();
user.UserId = 2;
user.UserName = "vito";
user.LastActivityDate = DateTime.Now;

context.AttachTo("Users", user);    //在這之前所設定的值，都不會被寫入

user.UserName = "vito2";
context.SaveChanges();
｝
```

上面程式碼，最後只執行以下操作：
```sql
update [Users] set [UserName] = 'vito2' where UserId=2
```

#### 使用 Attach 方法

[Attach](http://msdn.microsoft.com/zh-tw/library/ff422174.aspx) 方法會將具有實體索引鍵的物件附加至物件內容，再利用 [ChangeObjectState](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectstatemanager.changeobjectstate.aspx) 方法變更狀態，然後呼叫 [SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.savechanges.aspx) 回存。
```c#
using (ExtendedAspNetDB context = new ExtendedAspNetDB())
{
EntityUsers user = new EntityUsers();
user.UserId = 2;
user.UserName = "vito";
user.LastActivityDate = DateTime.Now;

context.Users.Attach(user);
context.ObjectStateManager.ChangeObjectState(user, EntityState.Modified);

context.SaveChanges();
｝
```

上面程式碼，最後執行了以下操作：
```sql
update Users set UserName='vito',LastActivityDate='2013-02-22 12:59:28.457' where UserId=2
```

這方法有個很大的問題要注意，在呼叫 [ChangeObjectState](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectstatemanager.changeobjectstate.aspx) 方法時，是所有欄位都會當做是已變動欄位。  也就是，若部份欄位你都沒有設定到，也就是沒給定值，資料庫就可能就被更新成空白。  

#### 使用 ApplyCurrentValues 方法

使用這個方法，必須先查詢出資料庫中要修改的實體，然後呼叫 [ApplyCurrentValues](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectstateentry.applycurrentvalues.aspx) 方法修改實體的值。  
```c#
using (ExtendedAspNetDB context = new ExtendedAspNetDB())
{
EntityUsers user = new EntityUsers();
user.UserId = 2;
user.UserName = "vito2";

context.Users.OfType<EntityUsers>().First(u => u.UserId == user.UserId);
context.Users.ApplyCurrentValues(user);

context.SaveChanges();
｝
```

上面程式碼，最後執行了以下操作。
```sql
update Users set LastActivityDate='' where UserId=2
```

因為它會將參數中的實體和查詢出來的實體做欄位值的比對，然後產生最佳化的SQL，也就是只會更新不同值的欄位。  這個例子，因為資料庫中的 UserName 原本就是 vito2 ，所以不會再被 Update ，  資料庫中的 LastActivityDate 欄位原本是有值的，但是參數中的實體的LastActivityDate卻是空的，所以會被 Update。  

#### 使用 SQL 指令批次更新

更新多筆資料時，除了可以使用迴圈逐筆更新之外。  
如果想要一次更新多筆，也可以叫用 [ExecuteStoreCommand](http://msdn.microsoft.com/zh-tw/library/system.data.objects.objectcontext.executestorecommand.aspx) 方法，直接透過 SQK 指令更新。
```c#
NorthwindEntities context = new NorthwindEntities();
context.ExecuteStoreCommand("Update Employees Set City='New Taipei' Where City='Taipei'");
```

## 刪除

### 連接情況下
```c#
NorthwindEntities db = new NorthwindEntities();

var oneEmp = db.Employees.First(emp => emp.FirstName == "vito" && emp.LastName == "shao");

db.Employees.DeleteObject(oneEmp);
db.SaveChanges();
```

### 斷開連接情況下
```c#
NorthwindEntities db = new NorthwindEntities();

NEmployees emp = new NEmployees();
emp.EmployeeID = 11;

db.AttachTo("Employees", emp);
db.DeleteObject(emp);
db.SaveChanges();
```

# 如何更新 .edml 檔案

當資料庫結構做過變更後，可以使用「更新模型精靈」（Update Model Wizard）對 .edmx 檔案進行更新。  

你可以在模型圖表中的右鍵選單，找到如下圖的更新更能，或由 Model Browser 中的右鍵選單也有相同功能。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1ADYu28dXzggmPfdBPDVCoZq4KsWH5AtZwDV_sjdC0Id6YcOM2BkbTCISHV0Da2WB1_8hWuMrMCdCy4AbkGPPzbDUbmzW84wUX1jShounvCXBI6I3C5_5FC8VxnvbufFxExE11QkrGFQ/s0/update-edml-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBhKKn5pGEDh2710sYRt8WX8yMoV4ClcJ5BJup7urK1UNRSn7YRZsqV2SHMkIUL9q4fqATwYqwB9cla3Q72iNDq6jTzZBxxz4mMlmYVekdY-fWOArI-UuYETkDYBxzzHbIo-5HM7IZX30/s0/update-edml-2.png)
## 參考資料  

- [執行 Entity Framework 的「從資料庫更新模型」為何不更新](http://blog.miniasp.com/post/2011/05/02/Entity-Framework-Update-Model-from-Database.aspx)
- [HOW TO：在資料庫變更時更新 EDM](https://msdn.microsoft.com/zh-tw/library/vstudio/cc716697%28v=vs.100%29.aspx)
-