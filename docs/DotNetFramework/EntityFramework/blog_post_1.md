---
title: Entity Framework 4.1
layout: default
parent: EntityFramework
nav_order: 1
description: "Entity Framework 4.1"
date: 2016-01-28
tags: [DotNetFramework,EntityFramework]
postid: "5958875953407573606"
---
[Entity Framework 4.1](https://www.microsoft.com/en-us/download/details.aspx?id=26825) 非 EF 4 的升級版，它只是擴充 EF4 的功能，其中包含二個主要功能：  

- DbContext API  
EF4.1 中的 [DbContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Entity.DbContext.aspx) 只是將 EF4 中的 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 包裝起來，簡化原本 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 的操作。  所以說它算是較輕量化的 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) ，僅提供程式設計時需要用到的功能。
- Code First  
EF4.1 支援 Code First 機制，也就是允許你在還沒有建立實體資料庫前，就可以先行建立 entity object 撰寫程式，以達到 SOC (Separation of concern) 概念，讓開發人員可以更專注於商務邏輯的開發。

[![](https://lh3.googleusercontent.com/-NPPEr0Q_wQY/VqoCnnhsOzI/AAAAAAAAebc/LpwZiVDJCFY/s800/ef-41-install.png)](https://lh3.googleusercontent.com/-NPPEr0Q_wQY/VqoCnnhsOzI/AAAAAAAAebc/LpwZiVDJCFY/s0/ef-41-install.png)

# 如何建立 DBContext 的 EDM

要建立 DBContext 的 EDM ，必須先安裝 EF 4.1 之後，安裝完成之後，再透過「ADO.NET DBContext Generator」工具，將原先使用 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 建立的 EDM ，改成 DBContext 的 EDM 。  

在 VS2010 中，當你建立「ADO.NET Entity Data Model」項目時，既使已安裝了 EF 4.1 ，它還是會自動建立以 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 為主的 EDM 。  要轉換成 DBContext 的 EDM，首先你必須先該 EDM 檔案的屬性視窗中，將[自訂工具]屬性清除，它就會移除原先預設的 enity 和 context 物件。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqtQ2rlXaSCZvQun0UfwbZ-BuRAskSQtABo58Rr6w9PCRjj1gXodNmihWtpm3hfzASJQlmjtLa034ozlPDWr1q2_pI1WB4CBnGfHebuOsIKe-Dr48VmLaVzgaejyI_oXFkZq7jNNn7p_U/s0/ef41-dbcontext-01.png)

接著開啟 EDM 的 Designer 視窗，從右鍵選單中執行＂Add Code Generation Item..＂，並點選＂ADO.NET DBContext Generator＂。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5SD1kH1vHFHAiBn651HsDPhFt25by5ksWCe6eIvAnqm9E1ROz_N4TPcst-AxVrbrThP_rPMfLxVWceYWSiQTbaLqwpWBT_yNKxdYqSRKgHyJz7_ZTFWnjTA5fu8_6CIl73KB638aGpdM/s0/ef41-dbcontext-02.png)

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhw-osh04R4PMqoBGkjesu-9EzwCxdgE9uEeTosqEMeVPUYdp-vIfTTTlsuFjIgEhvu9uZL7oMk3KROF7Q5988_vc8hRpczVm4Eyxwci6eiHVObzUsR7Te_KW3k-dq-6dOaMQ9qNaK_U-w/s800/ef41-dbcontext-03.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhw-osh04R4PMqoBGkjesu-9EzwCxdgE9uEeTosqEMeVPUYdp-vIfTTTlsuFjIgEhvu9uZL7oMk3KROF7Q5988_vc8hRpczVm4Eyxwci6eiHVObzUsR7Te_KW3k-dq-6dOaMQ9qNaK_U-w/s0/ef41-dbcontext-03.png)

執行結束後，就可以看見 POCO Entities 和 DBContext 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2_3lAPcL9CFI5fnlN0XnsV9DHhSr1V2u5k5pKUaCfgE7wzJdE43KYJw8KFTnf14czTCqxjmCE8T1FMPmmiXaBL5F-KWyd2vYYKMVYN9ggBRKUhofssy26nvNp8ECvFrfLha4eqSDIYbA/s0/ef41-dbcontext-t4.png)

什麼是「T4 文字範本」

上圖中的 .tt 檔案就是一種 T4 文字範本，T4 來自於 Text Template Transformation Toolkit ，它就是在一個文字檔案中，利用 &lt;# #&gt; 標籤插入一些使用 c# 或 Vb.net 設計的控制邏輯，用以輸出所需要的文件內容。  就像 asp 檔案一樣，內含文字與程式邏輯，最後輸出一個網頁內容。詳細的介紹，可參考 MSDN : [「程式碼產生和 T4 文字範本」](https://msdn.microsoft.com/zh-tw/library/bb126445.aspx).  

什麼是「POCO Entities」

上圖中 Model1.tt 底下的每一個 .cs 檔案中的類別就是 POCO Entity ，（"Plain Old C# Object" 或 "Plain Old CLR Object"），意思就是**簡單物件**。  這些物件都只有定義屬性，而沒有定義其他的方法或事件，它的好處是讓開發人員可專注在商務邏輯開發或者進行單元測試的規劃。  底下程式碼列出北風資料庫的部份實體內容。  
```c#
public class NorthwindEntities : DbContext
{
public DbSet<Customer> Customers { get; set; }
public DbSet<Employee> Employees { get; set; }
public DbSet<OrderDetail> OrderDetails { get; set; }
public DbSet<Order> Orders { get; set; }

public virtual ObjectResult<CustOrderHist_Result> CustOrderHist(string customerID)
{
//...
}

public virtual ObjectResult<CustOrdersDetail_Result> CustOrdersDetail(Nullable<int> orderID)
{
//...
}

public virtual ObjectResult<CustOrdersOrders_Result> CustOrdersOrders(string customerID)
{
//...
}
}
```

前面提過 [DbContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Entity.DbContext.aspx) 只是包裝了 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) , 你也可以透過以下程式碼透過 [DbContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Entity.DbContext.aspx) 取得 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 。
```c#
ObjectContext objCtx = (myDbContext as IObjectContextAdapter).ObjectContext;
```

# Entity Framework 4.1 中的擴充功能

這二個功能都是 [Entity Framework 4.1](https://www.microsoft.com/en-us/download/details.aspx?id=26825) 中的擴充方法。

## DbSet

[DbSet](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.aspx) 是 EF 中的一個類別，用來表示 context 中的所有 entity 。   [DbSet](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.aspx) 類別提供許多既實用又簡單的方法，可以用來操作實體模型，例如： [DbSet.Add](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.add.aspx) 、 [DbSet.Create](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.create.aspx) 、 [DbSet.Remove](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.remove.aspx) 、 [DbSet.Find](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.find.aspx) 等等。  
```c#
using (var context = new NorthwindEntities())
{
DbSet<Order> orders = context.Orders;
IQueryable<Order> query = orders.Where(o => o.CustomerID == "VINET");
foreach (Order order in orders)
{
Console.WriteLine(order.OrderID);
}
}
```

### DbSet.Local

[DbSet](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.aspx) 的 [Local](https://msdn.microsoft.com/zh-tw/library/gg679246%28v=vs.113%29.aspx) 屬性是用來記錄已追蹤的實體，也就是載入到記憶中的資料，都可以在 [Local](https://msdn.microsoft.com/zh-tw/library/gg679246%28v=vs.113%29.aspx) 屬性中取得。  也因為這個屬性的存在，當你對資料庫進行第二次查詢時，若實體資料已經存在 [Local](https://msdn.microsoft.com/zh-tw/library/gg679246%28v=vs.113%29.aspx) 中，就不會重新由資料庫載入。  例如下面這個例子：行５回傳的數值是０，這是因為查詢尚未對資料庫提取資料，你可以透過 iteration 方式或叫用 Load 方法，將資料載入到記憶體。  
```c#
using (var context = new NorthwindEntities())
{
DbSet<Order> orders = context.Orders;
IQueryable<Order> query = orders.Where(o => o.CustomerID == "VINET");  //+5
Console.WriteLine(orders.Local.Count);  //0
query.Load();
Console.WriteLine(orders.Local.Count);  //5
query = orders.Where(o => o.CustomerID == "TOMSP");         //+6
query.Load();
Console.WriteLine(orders.Local.Count);  //11
}
```

### DbSet.Find

在對資料庫進行查詢時，我們很常透過索引鍵直來取得特定的一筆資料，  在 [ObjectContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.aspx) 中通常我們會這麼做：  
```c#
var trade1 = context.DailyTrades
.SingleOrDefault(t => t.StockID == "2303" && t.TradeDate == "20160104");
```

但是在 [DbContext](http://msdn.microsoft.com/zh-tw/library/System.Data.Entity.DbContext.aspx) 中，除了上述方法外，你也可以利用 [DbSet](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.aspx) 提供的 [Find](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.find.aspx) 方法，透過鍵值直接查詢。  這個方法有個重要的特性必須特別注意，它並不是每次都會向資料庫提取資料，而是先在 [Local](https://msdn.microsoft.com/zh-tw/library/gg679246%28v=vs.113%29.aspx) 中尋找已經由資料庫載入到記憶中的實體資料（包含已加入但尚未存回資料庫的實體），若找不到才會對資料庫進行搜尋。  
```c#
using (var context = new NorthwindEntities())
{
DbSet<Order> orders = context.Orders;
IQueryable<Order> query = orders.Where(o => o.CustomerID == "VINET");  //+5

Order order = null;
order = orders.Find(10248);     //非已追蹤物件，向資料庫提取
if (order != null)
{
Console.WriteLine(orders.Local.Count);      //1
Console.WriteLine(order.OrderID);           //10248
}

query.Load();
order = orders.Find(10248);     //10248 是已追蹤物件，直接由記憶體提取
if (order != null)
{
Console.WriteLine(orders.Local.Count);      //5
Console.WriteLine(order.OrderID);           //10248
}
}
```

若是複合主鍵也可以直接使用。
```c#
var orderdetail = context.OrderDetails.Find(10248, 72);
```

## EF 的異動偵測

上面提過 [DbSet.Local](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.local.aspx) 屬性可以用來取得已追蹤的實體。  由於這個屬性的回傳值是一個 ObservableCollection&lt;T&gt; 型別，這個型別不僅僅是一個集合類型的型別，最大的特色就是當這個集合內的物件有任何異動時可以提供告知(notifications)。  因此若要追蹤實體的異動，只要訂閱  CollectionChanged 事件即可。  
```c#
using (var context = new NorthwindEntities())
{
context.Orders.Where(o => o.CustomerID == "VINET").Load();
ObservableCollection<Order> localdata = context.Orders.Local;

localdata.CollectionChanged += (s, a) =>
{
if (a.NewItems != null)
{
foreach (Order item in a.NewItems)
{
Console.WriteLine(item.OrderID + " Added.");
}
}

if (a.OldItems != null)
{
foreach (Order item in a.OldItems)
{
Console.WriteLine(item.OrderID + " Removed.");
}
}
};
}
```

## No-tracking queries

使用 EF 對資料庫進行查詢，原則上每次查詢結果都會被記錄在快取記憶體中以便追縱，這些內容稱為**追蹤的實體**。  若之後再對資料庫做查詢，雖然查詢指令依然會送到資料庫端，但是資料若已在追蹤的實體中，則由追蹤的實體中回傳。  所以底下這個例子，第二次的查詢結果會與實際資料庫中的記錄不同，因為結果是來自於快取中的追蹤的實體，但是該資料已經被異動過。  
```c#
using (StockDBEntities context = new StockDBEntities())
{
var query1 = context.Catalogs;
foreach (var catalog1 in query1.ToList())
{
catalog1.CatalogName = catalog1.CatalogName + "0";
}

//在同一個 dbcontext 中，因為 Catalogs 實體已是快取追蹤物件，所以不會重新向資料庫取得
var query2 = context.Catalogs;
foreach (var catalog2 in query2.ToList())
{
Console.WriteLine(catalog2.CatalogName);
}
}
```

DbContext 提供 [AsNoTracking](https://msdn.microsoft.com/zh-tw/library/gg679352%28v=vs.103%29.aspx) 擴充方法來執行**不追蹤式查詢**。  當使用 [AsNoTracking](https://msdn.microsoft.com/zh-tw/library/gg679352%28v=vs.103%29.aspx) 進行查詢時，它不會理會快取中的追蹤實體，結果必定由資料庫中取得，而且回傳的結果也不會被放入快取成為追蹤實體。  
```c#
var catalogs1 = context.Catalogs;
foreach (var catalog1 in catalogs1)
{
catalog1.CatalogName = catalog1.CatalogName + "0";
}
//會由快取中取得資料
var catalogs2 = context.Catalogs.Where(c => c.TweOtc == 1);
foreach (var catalog2 in catalogs2)
{
Console.WriteLine("CatalogName[{0}]", catalog2.CatalogName);
}
//重新取得資料，不由快取中取得
var catalogs3 = context.Catalogs.AsNoTracking().Where(c => c.TweOtc == 1);
foreach (var catalog3 in catalogs3)
{
Console.WriteLine("CatalogName[{0}]", catalog3.CatalogName);
}
```

［注意１］

通常不追蹤式查詢，只適用於沒有異動需求的操作，因為當使用 No-tracking [Query](https://msdn.microsoft.com/zh-tw/library/gg696278%28v=vs.113%29.aspx) 時，對 context 而言，每個 entity 資料的狀態都是 [Detached] 。  也就是你無法在變動資料後，直接使用 context.SaveChanges() 將異動過的資料回存。  

［注意２］AsNoTracking v.s. Fine

前面提過 Find() 方法可以方便依主鍵值來方便查詢資料，它實際的做法是先判斷 context 中是否有相同主鍵值的資料，若有就直接回傳，若沒有才會對儲存區查詢，而且會將查詢到的資料放到 context 中追蹤。  所以 [Find](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.find.aspx) 與 [AsNoTracking](https://msdn.microsoft.com/zh-tw/library/gg679352%28v=vs.103%29.aspx) 是不可以同時使用的。

# CRUD in EF4.1

|  | ObjectContext | DbContext |
| --- | --- | --- |
| New | [AddObject](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.AddObject.aspx) | [Add](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.add.aspx) |
| Delete | [DeleteObject](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.DeleteObject.aspx) | [Remove](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbset.remove.aspx) |
| Save | [SaveChanges](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.SaveChanges.aspx) | [SaveChanges](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.SaveChanges.aspx) |
| Query SQL | [ExecuteStoreQuery](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.ExecuteStoreQuery.aspx) | [Database.SqlQuery](http://msdn.microsoft.com/zh-tw/library/system.data.entity.database.sqlquery.aspx) |
| Execute Command | [ExecuteStoreCommand](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.ExecuteStoreCommand.aspx) | [Database.ExecuteSqlCommand](http://msdn.microsoft.com/zh-tw/library/system.data.entity.database.executesqlcommand.aspx) |
| Execute Function | [ExecuteFunction](http://msdn.microsoft.com/zh-tw/library/System.Data.Objects.ObjectContext.ExecuteFunction.aspx) | X |
| EntityState | [EntityObject.EntityState](http://msdn.microsoft.com/zh-tw/library/system.data.objects.dataclasses.entityobject.entitystate.aspx) | [DbEntityEntry.State](http://msdn.microsoft.com/zh-tw/library/system.data.entity.infrastructure.dbentityentry.state.aspx) |
```c#
using (var context = new StockDBEntities())
{
// Create
var catalog = new Catalog();
catalog.CatalogID = "96";
catalog.CatalogName = "9696";
catalog.OrderNum = 100;
context.Catalogs.Add(catalog);
context.SaveChanges();
// Retrive and Update
var catalog2 = context.Catalogs.SingleOrDefault(c => c.CatalogID == "95");
if (catalog2 != null)
{
catalog2.CatalogName = "9595";
catalog2.OrderNum = 95;
Console.WriteLine("Entity State : {0}", context.Entry(catalog2).State);
context.SaveChanges();
}
// Delete 
var catalog3 = context.Catalogs.SingleOrDefault(c => c.CatalogID == "97");
if (catalog3 != null)
{
context.Catalogs.Remove(catalog3);
context.SaveChanges();
}
}
```

# Using Stored Procedures

##### Using Function Imports

以匯入函式的方式來操作預存程序是最方便簡單的，同時也可以在 EDM 中建立強型別的回傳實體。  
```c#
List<usp_MyTradeRecords_InStocks_Result> result1 = context.usp_MyTradeRecords_InStocks("6245").ToList();
```

##### Using Database.SqlQuery
```c#
var result12 =
context.Database.SqlQuery<MyTradeRecord>(
"exec usp_MyTradeRecords_InStocks @StockID",
new SqlParameter("StockID", "6245")).ToList();
```

##### Using Database.ExecuteSqlCommand
```c#
context.Database.ExecuteSqlCommand("exec usp_MyTradeRecords_InStocks @StockID", new SqlParameter("StockID", "6245"));
```

# Transation in EF4.1

在 EF4.1 中，當執行 [DbContext.SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbcontext.savechanges.aspx) 方法時，EF 就會開始轉換成 SQL 語法，對資料庫執行命令，它本身是批次作業，所以不需要手動叫用 Complete 方法。  如果要自已手動決定 Complete 時機，可以將它們包在一個 Transation 物件之中即可。 （PS. Transation 物件必須自行加入 System.Transation 參考）  
```c#
using (TransactionScope transaction = new TransactionScope())
{
context.SaveChanges();
transaction.Complete();
}
```

如果是叫用 [ExecuteSqlCommand](http://msdn.microsoft.com/zh-tw/library/system.data.entity.database.executesqlcommand.aspx) 這類指令不是批次的，你也可以透過 Transation 物件來達到批次交易的目的。
```c#
using (var context = new TestDB1Entities())
{
using (TransactionScope transaction = new TransactionScope())
{
var connection = ((IObjectContextAdapter)context).ObjectContext.Connection;
try
{
var product = context.Products.FirstOrDefault(p => p.ProductID == 4920);
product.ProductName = "....";
context.SaveChanges();
context.Database.ExecuteSqlCommand("Update Products Set ProductID=4920 Where ProductID=4921");  //error
transaction.Complete();
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}
finally
{
connection.Close();
}
}
}
```

另外 [DbContext.SaveChanges](http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbcontext.savechanges.aspx) 沒有其它的多載方法，它執行完成後會自動復原 [EntityState](http://msdn.microsoft.com/zh-tw/library/system.data.objects.dataclasses.entityobject.entitystate.aspx) ，所以也就不再提供 ObjectContext.AcceptAllChanges 方法。
## 參考資料  

- [Using DbContext in EF 4.1 Part 1: Introduction and Model](http://blogs.msdn.com/b/adonet/archive/2011/01/27/using-dbcontext-in-ef-feature-ctp5-part-1-introduction-and-model.aspx)
- [Create EDM and DBContext in EF 4.3](http://www.entityframeworktutorial.net/EntityFramework4.3/create-dbcontext.aspx)
- [Databinding with WinForms](https://msdn.microsoft.com/en-us/data/jj682076.aspx)
- [Local Data](https://msdn.microsoft.com/en-us/data/jj592872.aspx)
- [ObjectContext VS DBContext](http://www.c-sharpcorner.com/UploadFile/ff2f08/objectcontext-vs-dbcontext/)