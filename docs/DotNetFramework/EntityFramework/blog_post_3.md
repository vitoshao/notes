---
title: 使用 EF 操作關聯性資料
layout: default
parent: EntityFramework
nav_order: 3
description: "使用 EF 操作關聯性資料"
date: 2016-02-26
tags: [DotNetFramework,EntityFramework]
postid: "1901588007382371823"
---
透過 ORM 物件來操作資料庫最大的好處就是方便，不用再去理會 table 與 column 的問題，也不需要去撰寫 Insert Update Delete 等等的 SQL 語法，這些 EF 都會自動幫我們處理。  之所以能夠這麼做，是因為 EF 會透過變更追蹤器(change tracker)持續追蹤你所進行的操作，直到你呼叫 SaveChanges 方法時，才會將這些變更，依據你的資料庫種類轉譯成適合的 SQL 語法，並執行更新。  

這一篇主要是探討關聯性(relationship)資料的操作時應該注意的事項。操作對象是北風資料庫，為了操作說明，將 Order.CustomerID 由原本是允許 Null 欄位，更改成必須欄位。  資料庫中， Order.OrderID, Employee.EmployeeID, Product.ProductID 都是自動編號。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixWv0REiSYNqpJl3eVyRAN2FIsUVeRIZuOHzHXirLF-353Z6T4GZNWtb1EpbEA4Nx2sNe9c0Ihqou_ZDKQxA8-gaLk2CYxViZIARGoM2VWFIm3Tni04RS_w9t1F9imU4_vHLZQvh8L-CI/s0/ef-changing-01.png)

# CRUD with EF

## 簡單的 CRUD

複習一下單獨新增一個 entity 或者對已存在物件進行修改與刪除。  
```c#
// add new entity
var p1 = new Product();
p1.ProductName = "";
context.Products.Add(p1);
int r1 = context.SaveChanges();
Console.WriteLine("{0} updated", r1);

int productid = p1.ProductID;
Console.WriteLine(productid);

// update entity
var p2 = context.Products
.Where(p => p.ProductID == 1)
.FirstOrDefault();
if (p2 != null)
{
p2.ProductName = "chai chai";
int r2 = context.SaveChanges();
Console.WriteLine("{0} updated", r2);
}

// delete existing entity
var p3 = context.Products
.Where(p => p.ProductID == productid)
.FirstOrDefault();
if (p3 != null)
{
context.Products.Remove(p3);
int r3 = context.SaveChanges();
Console.WriteLine("{0} updated", r3);
}
```

## 不載入刪除

要修改資料必須先載入資料，這個必須還可以另人接受，但是上面的方法，在刪除資料時，也必須事先載入資料，對於這個做法一定會讓人不高興。  這是因為叫用 Remove 方法刪除實體時，它的必要條件就是刪除對象必須是已經列入追蹤中實體，所以在刪除前一定得先載入該物件實體。  不過底下內容就是要來探討幾種不用事先載入直接刪除的方法。  

### 方法一：利用 stub 物件刪除

如果你知道你要刪除物件的鍵值，就可以利用一個 stub 物件來進行刪除，而不用事先載入該實體物件。  這個 stub 物件只含必要的鍵值欄位即可。  
```c#
// delete without loading
var p4 = new Product();  // create a stub object
p4.ProductID = 83;    
context.Products.Attach(p4);
context.Products.Remove(p4);
int r4 = context.SaveChanges();
Console.WriteLine("{0} updated", r4);
```

上面方法中，建立一個 stub 物件，透過 Attach 方法將該物件變成已載入追蹤，再叫用 Remove 方法註冊刪除。  不過這裡要注意的是，若是該 entity 原先就已經在載入追蹤裡頭，此時 Attach 方法將導至鍵值已存在的錯誤。

### 方法二：直接註冊刪除

在上面我們利用一個 stub 物件，再叫用 Attach 及 Remove 方法來完成刪除要求，該 Remove 方法做的就只是註冊一個刪除物件。  其實我們也可以直接變更物件狀態來達到這個功能。  
```c#
var p5 = new Product();
p5.ProductID = 84;
context.Entry(p5).State = EntityState.Deleted;
int r5 = context.SaveChanges();
Console.WriteLine("{0} updated", r5);
```

### 方法三：叫用 ExecuteSqlCommand 刪除

另外也可以直接叫用 ExecuteSqlCommand 方法，直接進行刪除。
```c#
context.Database.ExecuteSqlCommand(
"Delete Products Where ProductID = {0}", productid);
```

## If not Exists insert

透過 SQL 進行操作時有時候必須用到「if not Exists insert ...」來新增一筆資料。例如：  
```sql
if not exists( select 1 from Customers where CustomerID='apple')
BEGIN
insert Customers(CustomerID,CompanyName) values ('apple','apple inc');
END
```

那麼這樣子的需求在 EF 中該如何表示呢？很簡單就利用 Find 方法來協助即可。
```c#
var customerid = "apple";
var customer = context.Customers
.Find(customerid)
?? context.Customers.Add(new Customer
{
CustomerID = "apple",
CompanyName = "apple inc"
});
int r6 = context.SaveChanges();
```

# 關聯性資料 - 新增

要建立資料的關聯性資料和新增一般資料沒什麼不同，只不過若是 Key 值是自動編號，那麼 Parent 資料要先 Save 以便取得 Key 值。  
```c#
using (var context = new NorthwindEntities())
{
var emp = new Employee
{
FirstName = "a",
LastName = "b"
};
context.Employees.Add(emp);

var order = new Order()
{
EmployeeID = emp.EmployeeID,
CustomerID = "apple"
};
context.Orders.Add(order);
context.SaveChanges();
}
```

其實直接透過**導覽屬性**來操作比較符合 ORM 的精神。  
```c#
using (var context = new NorthwindEntities())
{
var emp = new Employee
{
FirstName = "a",
LastName = "b"
};
context.Employees.Add(emp);

var order = new Order()
{
Employee = emp,   // 差別只有這一行
CustomerID = "apple"
};
context.Orders.Add(order);
context.SaveChanges();
}
```

若是**集合導覽屬性**也是類似方法的操作。  
```c#
using (var context = new NorthwindEntities())
{
var order = new Order()
{
CustomerID = "apple"
};
context.Orders.Add(order);
context.SaveChanges();

var detail1 = new OrderDetail
{
OrderID = order.OrderID,
ProductID = 11,
Quantity = 1
};
order.OrderDetails.Add(detail1);

var detail2 = new OrderDetail
{
OrderID = order.OrderID,
ProductID = 12,
Quantity = 1
};
order.OrderDetails.Add(detail2);

int r = context.SaveChanges();
Console.WriteLine("{0} updated", r);
}
```

# 關聯性資料 - 刪除

若要刪除一筆資料，但是該筆資料和其他資料具有關聯性，這時必須考慮這個關聯性欄位是否是必要欄位。  

- 選擇性關聯(Optional relation)：若資料庫欄位設定為允許Null，則稱為非必要欄位。
- 必要關聯(Required relation)：若資料庫欄位設定為不允許Null，則稱為必要欄位。

例如 Catagory 和 Product 關係，要刪除一個分類，只要將相關的產品變更分類，或者將分類設定成空白即可。  但是如果是 Order 和 OrderDetail 的關係，通常若要刪除一筆 Order ，與該訂單相關的 OrderDetail 都必須連同刪除才有意義。  所以我們可以說 Product.CatagoryID 是選擇性關聯欄位，OrderDetail.OrderID 則是必要關聯欄位。  
```c#
public partial class Order
{
public int OrderID { get; set; }
public string CustomerID { get; set; }
public Nullable<int> EmployeeID { get; set; }

public virtual Customer Customer { get; set; }
public virtual Employee Employee { get; set; }
public virtual ICollection<OrderDetail> OrderDetails { get; set; }
｝
```

## 選擇性關聯

在北風範例資料庫中，Order.EmployeeID 是一個選擇性關聯欄位，如果我們直接刪除了某位 Employee ，而沒有處理相關 Order 的話，那麼與該員工相關的 Order 將導至 Foreign Key 衝突。  要解決這個問題則必須在刪除 Employee 之前，先將所有相關的 Order 的 EmployeeID 欄位變更成 Null ，再刪除 Employee 才不會產生錯誤。  
```c#
using (var context = new NorthwindEntities())
{
var employee = context.Employees
.Where(emp => emp.FirstName == "vito")
.FirstOrDefault();
if (employee != null)
{
foreach (var order in employee.Orders)
{
order.Employee = null;
}
context.Employees.Remove(employee);    //若沒有先移除相關 Order 的 Employee 屬性，呼叫 SaveChanges 時 將導至 FK 參考錯誤。
context.SaveChanges();
}
}
```

上面我們使用 loop 將 Child 資料中相關聯的欄位設成 null ，其實這一連串的動作，EF 都可以自動幫我們完成，只是有一點必須注意，**你必須先載入相關的 Child 資料**。  
```c#
using (var context = new NorthwindEntities())
{
var employee = context.Employees
.Where(emp => emp.FirstName == "vito")
.FirstOrDefault();

if (employee != null)
{
context.Orders
.Where(o => o.EmployeeID == employee.EmployeeID)
.Load();     // loading relational data

context.Employees.Remove(employee); 
context.SaveChanges();
}
}
```

上面程式碼，我們透過 Load 來載入相關的 Child 資料，接著就可以叫用 Remove 方法直接刪除 Parent 實體，而 Child 資料的關聯欄位就會被更新成 null 。  如果透過 SQL Profile 的觀查，你會發現叫用 SaveChanges 時， EF 實際對資料庫送出了以下的指令：  
```sql
update order set employeeid=null where orderid = ...
update order set employeeid=null where orderid = ...
...
delete employee where employeeid = ...
```

#### 叫用 Clear 方法

我們也可以叫用 Clear 方法來清除相關聯的 Child 資料，針對選擇性關聯欄位，它就會自動將欄位值更新成 null 。  不過叫用 Clear 方法時，觀查 SQL Profile 上的事件，你可以發現 EF 實際上是對資料庫執行 Select Child 的動作，而真正的 update child 和 delete parent 也是在叫用 SaveChanges 方法時才會送出。  
```c#
int orderid = int.Parse(tbOrderID.Text);

using (var context = new NorthwindEntities())
{
var order = context.Orders
.Where(o => o.OrderID == orderid)
.FirstOrDefault();

if (order != null)
{
order.OrderDetails.Clear();
context.Orders.Remove(order);
context.SaveChanges();
}
}
```

## 必要關聯

在北風範例資料庫中，如果我們直接刪除了某個 Order 而沒有處理 OrderDetail 的話，那麼與該訂單相關的 Detail 將導至 Foreign Key 衝突。  而且因為 OrderDetail 的 OrderID 欄位不允許 Null ，若要刪除該筆 Order ，就必須先將相關的 OrderDetail 刪除，再刪除 Order 才不會產生錯誤。  

### 手動處理

解決這個問題最直接的方法就是手動處理，也就是先刪除相關聯的 Child 資料，再刪除 Parent 資料。如：  
```c#
using (var context = new MyTripDBEntities())
{
var order = context.MyOrders
.Where(a => a.OrderID == orderid)
.FirstOrDefault();

if (order != null)
{
var details = order.MyOrderDetails.ToList();
foreach (var detail in details)
{
context.MyOrderDetails.Remove(detail);
}
context.MyOrders.Remove(order);
context.SaveChanges();
}
}
```

### 串聯刪除（cascade delete）

「**串聯刪除**」（cascade delete）是指，你只要刪除 Parent 資料，相關連的 Child 資料就由系統自動處理。  串聯刪除有二種處理方式：  

#### 在 ORM 的 Relationship 中設定串聯刪除

你可以在 Entity Model 中，將物件的關聯性設定成 cascade ，如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjW3tzynH9oTjhtHAyJ39gg6_-oA2DZV-_yW61yx1MrVbbAXxond6AIlkSUczAJnlqS7RefOkUvmaMpi0JhF8L6MGfRbscWLovu-79W1k_2rb4XbbPFCnX9jp61bgcJ7-tza6cDnCuZDYw/s0/ef-changing-03.png)

然後使用**預先載入**(eager loading)將關聯資料載入到記憶體，再刪除 Parent 資料即可。
```c#
using (var context = new MyTripDBEntities())
{
var order = context.MyOrders
.Where(a => a.OrderID == orderid)
.FirstOrDefault();

if (order != null)
{
// eager loading relational data
context.Entry(order)
.Collection(o => o.MyOrderDetails)
.Load();

context.MyOrders.Remove(order);
context.SaveChanges();
}
}
```

#### 在資料庫中設定串聯刪除

透過 foreign key 建立資料庫關聯時，資料庫本身就有管理這個**串聯刪除**的設定，如下圖所示。  在 SQL Server 中，它的預設值是「沒有動作」（No Action）。  若你將刪除規則設定成「串聯式」（cascade, SQL Server 翻譯成「重疊顯示」），那麼當 Parent 被刪除時，系統本身就會自動刪除相關的 Child 。  這時候程式中就不需要 Load 相關的 Child ，直接刪除 Parent 即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwIytodQY4sX_NHCneU1h7zMf2l3YY_WVXSAiHSNeshBkpNQa5DFwpatwyMY5aSUDhl4DMZss6T26ZgK0492TTOmeKN2t1LzW4pDRDh0Dwbl1rR0ioomM087PkVn1_SycvpkARv_Wy1Jw/s0/ef-changing-02.png)
```c#
using (var context = new MyTripDBEntities())
{
var order = context.MyOrders
.Where(a => a.OrderID == orderid)
.FirstOrDefault();

if (order != null)
{
context.MyOrders.Remove(order);
context.SaveChanges();
}
}
```

關於刪除規則

若我們在 SQL Server 中建立關連性，也就是設定外部鍵(FK)，若沒有特別指定該關連性的刪除規則，則其預設值為「沒有動作」。  可是如果在 EF 中，透過 Code First 模型建立資料庫時，若模型中含有關聯性欄位，而且是**必要關聯**性的話，那麼該當資料庫建立時，該關連性的刪除規則會自動設定成「串聯刪除」。  若是**非必要關聯**，才會設定成「沒有動作」。

# 關聯性資料 - 修改

有了新增和刪除，修改就不用寫了，因為修改等於先刪除再新增...喂～～  

不過底下這個例子可以清楚看出，當我們變更了某個關聯欄位的屬性值之後，對應的集合屬性也會自動變更。  
```c#
using (var context = new NorthwindEntities())
{
var EmpA = context.Employees.Find(18);
var EmpB = context.Employees.Find(19);

var orderEmpA = context.Orders.Where(o => o.EmployeeID == EmpA.EmployeeID);
var orderEmpB = context.Orders.Where(o => o.EmployeeID == EmpB.EmployeeID);

foreach (var order in orderEmpA)
{
Console.WriteLine(order.OrderID);
}
//11086  原本與 EmpA 相關的 Order 有2筆
//11087

foreach (var order in orderEmpB)
{
Console.WriteLine(order.OrderID);
}
//10248  原本與 EmpB 相關的 Order 只有1筆

var orderA1 = orderEmpA.FirstOrDefault();
orderA1.EmployeeID = EmpB.EmployeeID;    //將 EmpA 的第一筆 Order 變更 EmpB
context.SaveChanges();

foreach (var order in orderEmpA)
{
Console.WriteLine(order.OrderID);
}
//11087 orderEmpA 集合只剩1筆

foreach (var order in orderEmpB)
{
Console.WriteLine(order.OrderID);
}
//10248  orderEmpB 自動變成2筆了
//11086
}
```

不過，如果要變更的屬性是 key 值欄位，因為 key 值欄位是 EF 用來識別實體的欄位，所以不允許變更，最好的方法還是先執行刪除，再新增一筆新的資料。