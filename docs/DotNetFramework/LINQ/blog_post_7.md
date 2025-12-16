---
title: LINQ to DataSet
layout: default
parent: LINQ
nav_order: 7
description: "LINQ to DataSet"
date: 2013-04-09
tags: [DotNetFramework,LINQ]
postid: "8620655388038429522"
---
# Defining the DataSet Schema

# Populating the DataSet

### Adding Data Programmatically

### Using the DataAdapter

# Saving Changes to the Database in Batches

# Creating Typed DataSets

A [Typed DataSet](http://msdn.microsoft.com/zh-tw/library/esbykkzb.aspx) is a [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) that is based on strongly typed objects that exist at design time.   [Typed DataSets](http://msdn.microsoft.com/zh-tw/library/esbykkzb.aspx) allow you to program against the actual table and field schemas for your database instead of relying on strings.  

For example,
```c#
//Using DataSet
DataTable companyTable = salesData.Tables["Company"];

//Using Typed DataSet
DataTable companyTable = vendorData.Company;
```

You can provide an XML Schema Definition (XSD) file to generate a typed [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) class.   You can also use the [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) Editor to graphically create and modify an XSD file.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVZXmHwYxLx1ATB2Se2UE1fQMmAplvtOKBtX0fihIr13hbwceilcMkEEsZpi7MeICHxYakU1f9Oma8P-Kj3i1ah_vSdPEhNZ33wj5Zfowi_Fg6sO1dFuaIViAlRhoXePro7QTB8xjzbsA/s543/typed-dataset-new.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbK-ZurCsVxiM97OJd-PYcOLl9j5ZVHs5EcEWb-A7umgx-r2RqENtT15XiWAPls4eehAghbl7pD6h3t5o-HrAsqTfde4ZYv34CDmqgRpn44zCYfLECsaYKXAf2RofNQHHV1yWjqqXQGjI/s678/typed-dataset-design.png)
```c#
Nothtwind dsNothtwind = new Nothtwind();    //Order is a Typed Dataset
DataTable orders = dsNothtwind.Orders;      //get the table in order dataset
DataTable order_detail = dsNothtwind.Order_Details;
```

# Querying with LINQ to DataSet

The [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) object already exposes [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) objects as [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) collections of data.   Therefore, writing LINQ queries against a [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) or [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) are not much different than writing against other collections of data.  
```c#
var employees = TestData.GetCompanyEmployeeDataSet();
var query = from emp in employees.Tables["Employee"].AsEnumerable()
where emp.Field<decimal>("Salary") > 50
select emp;

foreach (var emp in query)
{
myDebug.WriteLine("FirstName={0}, LastName={1}, Salary={2}",
emp.Field<String>("FirstName"),
emp.Field<String>("LastName"),
emp.Field<Decimal>("Salary"));
}
```

# Querying with Typed DataSet Objects

Before using LINQ query , you can load this typed [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) by passing it to a DataAdapter instance.   And then you can use LINQ to [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) to write a strongly typed LINQ query against the cached results.  
```c#
public SqlDataAdapter();
public SqlDataAdapter(SqlCommand selectCommand);
public SqlDataAdapter(string selectCommandText, SqlConnection selectConnection);
public SqlDataAdapter(string selectCommandText, string selectConnectionString);

public override int Fill(DataSet dataSet);
public int Fill(DataTable dataTable);
public int Fill(DataSet dataSet, string srcTable);
...
```

使用 LINQ 查詢 Typed DataSet
```c#
ConnectionStringSettings conn = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
Nothtwind dsNothtwind = new Nothtwind();
SqlDataAdapter adp = new SqlDataAdapter("Select \* From Orders;", conn.ConnectionString);
adp.Fill(dsNothtwind, "Orders");

var query = from o in dsNothtwind.Orders
where o.CustomerID == "VINET"
orderby o.OrderID
select o;

foreach (var o in query)
myDebug.WriteLine("{0} {1}", o.OrderID, o.OrderDate.ToString());
```

# Cross-Table LINQ to DataSet Queries

下面範例，使用 DataApapter 的 Fill 方法先將資料載入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 。  再使用 LINQ 進行 join 查詢，最後將結果放進一個匿名型別裡。  
```c#
Nothtwind dsNothtwind = new Nothtwind();
var orders = dsNothtwind.Orders;
var details = dsNothtwind.Order_Details;

OrdersTableAdapter adp_order = new OrdersTableAdapter();
adp_order.Fill(orders);

Order_DetailsTableAdapter adp_detail = new Order_DetailsTableAdapter();
adp_detail.Fill(details);

var query = from order in orders
join detail in details on order.OrderID equals detail.OrderID 
where order.OrderID == 10330
orderby detail.ProductID
select new
{
OrderID = order.OrderID,
ShipAddress = order.ShipAddress,
ProductID = detail.ProductID,
Quantity = detail.Quantity
};

gv1.DataSource = query;
gv1.DataBind();
```

# Comparing Data in DataSet Objects

You can use LINQ features to compare data contained in one or more DataTables.   These features include the following operators:  

- [Distinct](http://msdn.microsoft.com/zh-tw/library/system.linq.enumerable.distinct%28v=vs.100%29.aspx) ：產生不重複項目的序列。
- [Union](http://msdn.microsoft.com/zh-tw/library/bb341731.aspx) ：產生兩個序列的聯集(∪)
- [Intersect](http://msdn.microsoft.com/zh-tw/library/system.linq.enumerable.intersect%28v=vs.100%29.aspx) ：產生兩個序列的交集(∩)
- [Except](http://msdn.microsoft.com/zh-tw/library/bb300779.aspx) ：產生兩個序列的差集
```c#
Nothtwind dsNothtwind = new Nothtwind();
var orders = dsNothtwind.Orders;

OrdersTableAdapter adp_order = new OrdersTableAdapter();
adp_order.Fill(orders);

var FirstSet = from order in dsNothtwind.Orders
where order.OrderDate.Year==1996
select order.CustomerID;

var SecondSet = from order in dsNothtwind.Orders
where order.OrderDate.Year == 1998
select order.CustomerID;

//SELECT Distinct CustomerID FROM Orders Where year(OrderDate) = 1996

var Customer1996 = FirstSet.Distinct();

//SELECT CustomerID FROM Orders Where year(OrderDate) = 1996
//INTERSECT 
//SELECT CustomerID FROM Orders Where year(OrderDate) = 1998

IEnumerable<string> IntersectSet = FirstSet.Intersect(SecondSet);

//SELECT CustomerID FROM Orders Where year(OrderDate) = 1996
//EXCEPT
//SELECT CustomerID FROM Orders Where year(OrderDate) = 1998

IEnumerable<string> ExceptSet = FirstSet.Except(SecondSet);

//SELECT CustomerID FROM Orders Where year(OrderDate) = 1996
//UNION
//SELECT CustomerID FROM Orders Where year(OrderDate) = 1998

IEnumerable<string> UnionSet = FirstSet.Union(SecondSet);

myDebug.WriteLine(Customer1996.Count().ToString());     //67
myDebug.WriteLine(IntersectSet.Count().ToString());     //61
myDebug.WriteLine(ExceptSet.Count().ToString());        //6
myDebug.WriteLine(UnionSet.Count().ToString());         //87

//相異的部份
var DiffSet = FirstSet.Except(SecondSet).Union(SecondSet.Except(FirstSet));
myDebug.WriteLine(DiffSet.Count().ToString());          //26
```