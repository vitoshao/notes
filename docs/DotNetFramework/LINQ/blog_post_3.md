---
title: LINQ 入門(二)
layout: default
parent: LINQ
nav_order: 3
description: "LINQ 入門(二)"
date: 2012-11-15
tags: [DotNetFramework,LINQ]
postid: "6174900600944279071"
---
Language-Integrated Query (LINQ) provides a consistent model for querying data no matter where that data comes from.   This allows you to write .NET Framework code (in lieu of SQL) when working with data.   LINQ gives you compile-time syntax checking against your data, static typing, and IntelliSense in the code editor.   All of these features make programming data easier and more consistent, regardless of the origin of the data.  

LINQ is an extension to the development languages of C# and Visual Basic.   For more information on the LINQ language extensions, you can review the MSDN topic, [Getting Started with LINQ in C#](http://msdn.microsoft.com/zh-tw/library/bb397933.aspx)  

# LINQ 簡介

早先，我們查詢關聯式資料必須使用 SQL ，查詢 XML 文件必須使用 XQuery 。  對開發人員而言，他們必須使用不同的查詢語言以應付不同格式的資料類型。  LINQ 改變了查詢資料了做法，它提供了一個共同的模型，將查詢轉換成真正的程式碼，可以用來直接用來存取各種不同的資料格式，以簡化這種情況。  

在 LINQ 查詢中，您所處理的一定是物件。  不論您要查詢的資料是存在 XML 文件、SQL 資料庫、ADO.NET 資料集，還是 .NET 集合，以及其他任何 LINQ 提供者 (Provider) 的格式中，都是使用相同的基本程式碼撰寫模式。  這帶來了二個主要的優點：  

1. 編譯時期即可檢查型別，檢少錯誤。
2. 任何資料型態的查詢，都使用一致性的架構。

## LINQ and IEnumerable

若要使用 LINQ 查詢，該資料物件必須是「可查詢型別」，也就是必須支援 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 或 [IEnumerable&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/9eekhta0.aspx) 之衍生介面的型別，例如 Array、DataSet、List&lt;T&gt; 。  

要進行 LINQ 查詢作業，大都包含以下三個步驟：

1. 取得資料來源：定義一個用來查詢資料用的資料來源，資料來源必須為「可查詢型別」 (eg.集合物件、XML文件、EDO模型物件)。
2. 建立查詢：建立強型別的 LINQ 查詢表達示 ( strongly typed query expression)。
3. 執行查詢：執行 LINQ 查詢，以取得查詢結果。

例：下面這個範例 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 的 [AsEnumerable](http://msdn.microsoft.com/zh-tw/library/system.data.datatableextensions.asenumerable.aspx) 方法會傳回實作泛型 [IEnumerable&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/9eekhta0.aspx) 介面的物件，這是一個「可查詢型別」，所以可以當做 LINQ Query 的資料來源。 
```c#
// 定義資料來源
DataTable dtEmp = TestData.GetEmployeeDataTable();

// 建立查詢
var query = from employee in dtEmp.AsEnumerable()
select employee;

// 執行查詢
foreach (DataRow row in query)
{
Response.Write(row.Field<String>("LastName") + ": ");
Response.Write(row.Field<Int16>("Age") + "<br />");
}
```

例：下面這個範例使用陣列當做資料來源。
```c#
string[] employees = { "Michael", "Hank", "Benjamin", "Gail" };

var query = From emp In employees
Where emp.Length > 5
Select emp;

foreach (var emp in query)
{
myDebug.WriteLine(emp); 
}
```

## LINQ Data Providers

The LINQ language extensions allow you to query data in a consistent manner.  LINQ also includes additions to the .NET Framework itself for working with specific types of data such as **relational databases**, **XML**, and **objects**.  These additions allow you to connect to this data, query against it, and insert, update, and delete items within the data all by using strongly typed objects.  

- [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) ： 透過 O/R map 物件模型連線資料庫，再使用 LINQ 存取資料。
- [LINQ to DataSet](http://msdn.microsoft.com/zh-tw/library/bb386977.aspx) ： 允許使用 LINQ 查詢 DataSet (in-memory) 中的資料。
- [LINQ to Entities](http://msdn.microsoft.com/zh-tw/library/bb386964.aspx) ： 允許使用 LINQ 查詢 [Entity Framework](http://msdn.microsoft.com/zh-tw/library/bb399567.aspx) (EF) 建立的資料模型。
- [LINQ to Objects](http://msdn.microsoft.com/zh-tw/library/bb397919.aspx) ： 這是基礎的 LINQ 用法。只要實作 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 或 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/9eekhta0.aspx) 介面的集合物件都允許使用 LINQ 查詢
- [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) ： 這個提供者可以用來 create XML, load XML file, query XML, manipulate the XML, and transform the XML。

# 定義資料來源

使用 LINQ 查詢，第一個步驟就是**取得資料來源**。

LINQ 查詢幾乎支援所有.NET 中的集合物件，例如，載入記憶體中的 XML 文件、EF 定義的模型、LINQ to SQL 模型、或者如簡單的陣列資料等等。  底下針對幾種不同的資料類型，舉列說明：  

## Array Data

底下這個例子我們使用 LINQ 來查詢 Array 資料。  

程式碼3~5行，我們定義了一個 LINQ 查詢，通常稱為「查詢運算式」(Query Expression)，  它是由識別資料來源的子句和查詢的反覆運算變數所組成。  

在 from 關鍵後後的 emp 變數，一般稱為「範圍變數」(Range Variable) 或「反覆運算變數」(Iteration Variable)。   它會依據資料來源中的型別自動取得型別。  

最後，可以使用 iterative 方式（如 foreach)，列舉集合中的資料。  
```c#
string[] employees = { "Michael", "Hank", "Benjamin", "Gail" };

var empQuery = from emp in employees
where emp.Length > 5
select emp;

//因為 employees 是字串陣列, emp 就自動為字串型別
foreach (var emp in empQuery)
{
myDebug.WriteLine(emp); 
}
```

## List Data

You can use LINQ to query against data stored in a collection class, such as the **List&lt;T&gt;** class
```c#
public class TestData
{
public static List<TestEmployee> GetEmployeeList()
{
List<TestEmployee> employees = new List<TestEmployee>()
{
new TestEmployee ("987654321X","Andrew","Fuller",40),
new TestEmployee ("1234567890","Apple","Chen",10),
new TestEmployee ("1234567892","Banana","Shao",18),
new TestEmployee ("1234567891","Melon","Lai",30)
};
return employees;
}
｝
```
```c#
var employees = TestData.GetEmployeeList();

var empQuery = from emp in employees
where emp.Age > 20
select emp;

foreach (var emp in empQuery)
{
myDebug.WriteLine("{0} {1} {2}",emp.FirstName, emp.LastName, emp.Age);
}
```

## XML Data

LINQ 也可以用來查詢 XML 資料來源，它會使用到 [System.Xml.Linq](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.aspx) 命名空間的 [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) 。  首先使用 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 類別載入 XML 文件，再透過 [Element](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xcontainer.element.aspx) 或 [Descendants](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xcontainer.descendants.aspx) 方法存取資料項目。  
```xml
<?xml version="1.0" standalone="yes"?>
<Employees>
<Employee>
<EmployeeId>987654321X</EmployeeId>
<LastName>Andrew</LastName>
<FirstName>Fuller</FirstName>
<Age>40</Age>
</Employee>
<Employee>
<EmployeeId>1234567890</EmployeeId>
<LastName>Apple</LastName>
<FirstName>Chen</FirstName>
<Age>10</Age>
</Employee>
<Employee>
<EmployeeId>1234567892</EmployeeId>
<LastName>Banana</LastName>
<FirstName>Shao</FirstName>
<Age>18</Age>
</Employee>
<Employee>
<EmployeeId>1234567891</EmployeeId>
<LastName>Melon</LastName>
<FirstName>Lai</FirstName>
<Age>30</Age>
</Employee>
</Employees>
```
```c#
public class TestData
{
public static XElement GetEmployeeXML(Page page)
{
string xmlFile = page.MapPath("~/TestData/employee.xml");
return XElement.Load(xmlFile);
}
｝
```
```c#
XElement employees = TestData.GetEmployeeXML(this);

IEnumerable<XElement> empQuery =
from emp in employees.Descendants("Employee")
where int.Parse(emp.Element("Age").Value) > 20
select emp;

foreach (var emp in empQuery)
{
myDebug.WriteLine("{0} {1} {2}", 
emp.Element("FirstName").Value, 
emp.Element("LastName").Value, 
emp.Element("Age").Value);
}
```

## Database Data

When using [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) (found in the [System.Data.Linq](http://msdn.microsoft.com/zh-tw/library/system.data.linq.aspx) namespace),   The first step is create an **object model** that maps to your data structures.   You can use Visual Studio's tool or code to create these structures.  

The [System.Data.Linq.Mapping](http://msdn.microsoft.com/zh-tw/library/system.data.linq.mapping.aspx) namespace provides attribute classes for defining objects that map your data structures. For example,   
```c#
using System.Data.Linq.Mapping;
using System.Data.Linq;

[Table(Name = "Employees")]
public class MyEmployees
{
private int _empID;

[Column(IsPrimaryKey = true, Name = "EmployeeId", Storage = "_empID")]
public int EmployeeId
{
get
{
return this._empID;
}
set
{
this._empID = value;
}
}

[Column(Name = "FirstName")]
public string FirstName { get; set; }

[Column(Name = "LastName")]
public string LastName { get; set; }

[Column(Name = "Age")]
public int Age { get; set; }

[Column(Name = "City")]
public string City { get; set; }
}
```

The next step is to define a connection to the database using [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) object.
```c#
public class MyNorthwind : DataContext
{
public Table<MyEmployees> employees;

// This constructor will call DataContext.DataContext()
public MyNorthwind(string connection)
: base(connection)
{
}
}
```

You can now set a reference to a table in the database by using the [Table&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/bb358844.aspx) object. (using [GetTable](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.gettable.aspx) method of [DataContext](http://msdn.microsoft.com/zh-tw/library/system.data.linq.datacontext.aspx) object).  
You can then run a LINQ query against this table and store the results in a list of Employee objects.   [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx) will take care of the mapping for you.  
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
DataContext nwdb = new NorthwindContext(connString.ConnectionString);

Table<Nothtwind_Employees> Employees = nwdb.GetTable<Nothtwind_Employees>();
IEnumerable<Nothtwind_Employees> empQuery = from emp in Employees
where emp.FirstName.Length > 5
select emp;

foreach (var emp in empQuery)
{
myDebug.WriteLine("{0} {1} {2}",
emp.FirstName, emp.LastName, emp.Age);
}
```

# 建立 LINQ 查詢

使用 LINQ 查詢，第二個步驟就是**建立 LINQ 查詢**。

LINQ 查詢語法分成二種寫法，一種是使用「LINQ 表示式」，另一種使用「Lambda 表示式」。  

### LINQ 表示式（LINQ Expression）

「[LINQ 表示式](http://msdn.microsoft.com/zh-tw/library/bb397676.aspx)」，必須以 from 子句開頭，並以 select 或 group 子句結尾。  中間則可以包含 where 、 orderby 、 join 、 let 等子句。  
```c#
query = from  <element> in <collection>
where ...
orderby ...
```

例：
```c#
//取得資料來源
string[] employees = { "Michael", "Hank", "Benjamin", "Gail" };

//建立 LINQ 查詢
IEnumerable<string> empQuery = 
from emp in employees
where emp.Length > 5
select emp;

//執行 LINQ 查詢
foreach (string emp in empQuery)
{
myDebug.WriteLine(emp);
}
```

### Lambda 表示式（Lambda Expression）

另一種 LINQ 查詢語法就是使用「[Lambda 表示式](http://msdn.microsoft.com/zh-tw/library/bb397687.aspx)」。  

例：
```c#
//取得資料來源
string[] employees = { "Michael", "Hank", "Benjamin", "Gail" };

//建立 λ 查詢
var empQuery = employees
.Where(emp => emp.Length > 5);

//執行 λ 查詢
foreach (string emp in empQuery)
{
myDebug.WriteLine(emp);
}
```

其他 LINQ 查詢子句的用法，將整理在以下文章中：

- [Select 、 Where 、 Distinct](http://vito-note.blogspot.com/2013/04/linq-1-selectwheredistince.html)
- [OrderBy 、 GroupBy 、 Into 、 Max 、 Min 、 Average 、 Sum 、 Count 、 Aggregate](http://vito-note.blogspot.com/2013/04/linq-2-orderby-groupby-into-max-min.html)
- [Join](http://vito-note.blogspot.com/2013/04/linq-3-join.html)
- [Exist、In、Any、All、Contains](http://vito-note.blogspot.com/2013/04/linq-4-existinanyallcontains.html)
- [Concat、Union、Intersect、Except](linq-expression-5.aspx)

# 執行 LINQ 查詢

在上面例子中，我們使用 empQuery 變數來儲存 LINQ 表示式，這個變數稱「查詢變數」（Query Variable）。  查詢變數只會儲存儲存「查詢」命令而已，而不會保存「查詢結果」。  至於這個查詢運算式何時才會真正執行？可分成以下二個狀況：  

## 延遲查詢 (deferred query)

真正執行 LINQ 查詢，是在 foreach 迴圈中反覆查看變數時，查詢運算式才會真正執行，這就稱為「延遲查詢」。  
```c#
//執行 LINQ 查詢
foreach (string emp in empQuery)
{
myDebug.WriteLine(emp);
}
```

## 立即查詢 (Immediate query)

#### 回傳單一值

若查詢子句中包括 Count、Max、Average 和 First 等關鍵字，則查詢子句會立即執行。  因為這類查詢必須計算過所有資料，才能產生要回傳的那一筆資料。  
```c#
int count = query.Count();
var FirstEmp = query.First();
double aveAge = query.Average(Emp => Emp.Field<Int16>("Age"));
double maxAge = query.Max(Emp2 => Emp2.Field<Int16>("Age"));
```

#### 強制執行

你也可以使用 ToList&lt;TSource&gt; 和 ToArray&lt;TSource&gt; 方法強制 query 立即執行以取得結果，而不需依賴 for-each 。  
```c#
// 將查詢結果轉 List
List<DataRow> list = query.ToList();

// 將查詢結果轉 Array
DataRow[] array = query.ToArray();
```
## 參考資料  

- [LINQ 關鍵字 (C# 參考)](http://msdn.microsoft.com/zh-tw/library/bb310804%28v=vs.100%29.aspx)
- [開始使用 C# 中的 LINQ](http://msdn.microsoft.com/zh-tw/library/bb397933.aspx)
- [從逐步解說學習 (LINQ to SQL)](http://msdn.microsoft.com/zh-tw/library/bb399349.aspx)
- [LINQ to SQL 查詢小技巧](http://www.dotblogs.com.tw/alonstar/archive/2010/06/24/16100.aspx)
- [建立XML文件 (LINQ to XML)](http://www.dotblogs.com.tw/yc421206/archive/2010/09/29/17981.aspx)
- [ADO.NET Entity Framework](http://msdn.microsoft.com/zh-tw/library/bb399572.aspx)
- [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx)
- [LINQ to DataSet](http://msdn.microsoft.com/zh-tw/library/bb386977.aspx)
- [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx)
- [LINQ to Entities](http://msdn.microsoft.com/zh-tw/library/bb399567.aspx)
- [LINQ to Objects](http://msdn.microsoft.com/zh-tw/library/bb397919.aspx)