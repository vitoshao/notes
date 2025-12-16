---
title: LINQ 資料格式轉換
layout: default
parent: LINQ
nav_order: 9
description: "LINQ 資料格式轉換"
date: 2013-04-10
tags: [DotNetFramework,LINQ]
postid: "9178646886530044733"
---
LINQ 不僅僅可以用來查詢資料，  LINQ 也允許我們將來源資料轉換成其他格式的資料。例如轉成 XML 或 JSON 格式，亦或是自訂型別都可以。  

# Mapping to Another Type or Anonymous Object

LINQ allows you to use the Select keyword to push your results to an instance of a strongly typed object or even a new, anonymous type.  

Using **select new** to transform to another type
```c#
private class MyUser
{
public string Name { get; set; }
public string ID { get; set; }
}
protected void ToAnonymousObject_Click(object sender, EventArgs e)
{
var employees = TestData.GetEmployeeList();

// Transform to Custom Type
var empQuery = from emp in employees
select new MyUser
{
Name = emp.FirstName + " " + emp.LastName,
ID = emp.EmployeeId
};

// Transform to Anonymous Type
var empQuery2 = from emp in employees
select new
{
Name = emp.FirstName + " " + emp.LastName,
ID = emp.EmployeeId
};
}
```

# Merging Multiple Data Sources

LINQ also lets you merge two similar datasets by using the **Concat** method from the list object.
```c#
ConnectionStringSettings connString = ConfigurationManager.ConnectionStrings["NorthwindConnectionString"];
NorthwindContext nwdb = new NorthwindContext(connString.ConnectionString);

Table<Nothtwind_Employees> Employees = nwdb.GetTable<Nothtwind_Employees>();

var empQuery = (
from emp in Employees
where emp.City == "Seattle"
select emp
)
.Concat
(
from emp in Employees
where emp.City == "London"
select emp
);
```

# Performing Operations on Results

You can perform mathematical and string operations on your result sets.  
```c#
var employees = TestData.GetEmployeeList();

var empQuery = from emp in employees
select new
{
Description = 
string.Format("I'm {0} {1}, {2} years old.", emp.FirstName, emp.LastName, emp.Age)
};

foreach (var user in empQuery)
{
myDebug.WriteLine(user.Description);
}
//I'm Andrew Fuller, 40 years old.
//I'm Apple Chen, 10 years old.
//I'm Banana Shao, 18 years old.
//I'm Melon Lai, 30 years old.
```

# Transforming Results into XML

You can use [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) to transform data from a LINQ query into an XML structure.   You do so by using the [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) class and the LINQ Select keyword to create new elements nested inside one another like an XML tree.  
```c#
var employees = TestData.GetEmployeeList();

var empXml = new 
XElement("Employees", from emp in employees
select new XElement("Employee",
new XElement("Id", emp.EmployeeId),
new XElement("Name", emp.FirstName + " " + emp.LastName),
new XElement("Age", emp.Age)
)
);

//<Employees>
//  <Employee>
//    <Id>987654321X</Id>
//    <Name>Andrew Fuller</Name>
//    <Age>40</Age>
//  </Employee>
//  <Employee>
//    <Id>1234567890</Id>
//    <Name>Apple Chen</Name>
//    <Age>10</Age>
//  </Employee>
//  <Employee>
//    <Id>1234567892</Id>
//    <Name>Banana Shao</Name>
//    <Age>18</Age>
//  </Employee>
//  <Employee>
//    <Id>1234567891</Id>
//    <Name>Melon Lai</Name>
//    <Age>30</Age>
//  </Employee>
//</Employees>
```

# Transforming Results into JSON

Given the increased use of JQuery and AJAX,   you might need to transform your LINQ query results into JSON format for working with these client-side libraries.  

This serializer can be found in the [System.Runtime.Serialization.Json](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.aspx) namespace.   The actual class name of the serializer is [DataContractJsonSerializer](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.datacontractjsonserializer.aspx) .  It can be used to serialize types and lists of types that are marked as [DataContract](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datacontractattribute.aspx) .  
```c#
[DataContract]
public class TestEmployee
{
[DataMember(Order = 0)]
public string EmployeeId { get; set; }
[DataMember(Order = 1)]
public string FirstName { get; set; }
[DataMember(Order = 2)]
public string LastName { get; set; }
[DataMember(Order = 3)]
public int Age { get; set; }
public TestEmployee(string id, string firstname, string lastname, int age)
{
EmployeeId = id;
FirstName = firstname;
LastName = lastname;
Age = age;
}
}
```

Using the [WriteObject](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.datacontractjsonserializer.writeobject.aspx) method of [DataContractJsonSerializer](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.datacontractjsonserializer.aspx) class to write the query results to a       **MemoryStream**.
```c#
var employees = TestData.GetEmployeeList();

var empJson = from emp in employees
where emp.Age > 30
select emp;

DataContractJsonSerializer ser =
new DataContractJsonSerializer(typeof(IEnumerable<TestEmployee>));

MemoryStream ms = new MemoryStream();

ser.WriteObject(ms, empJson);
string json = Encoding.Default.GetString(ms.ToArray());
ms.Close();
Response.Write(json);

//[{"EmployeeId":"987654321X","FirstName":"Andrew","LastName":"Fuller","Age":40}] 
```
&lt;!--相關文章--&gt;  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2Q1Ch0u6DdE7o3mJIqe9Q-tlW1K61LeLq7ZCdEAeAF3NC0R3zMj_CxYX_YlXVbOgBUmVX0U7tPO0ylNpH5PN3_JfocbUJ4-yQdOr_TB_dYYQ3UO5Fuf9CpKQVykMY9LOtKVOFiTiPWCA/s120/RelationPage.png)

- [Data Transformations with LINQ (C#)](http://msdn.microsoft.com/en-us/library/vstudio/bb397914.aspx)
- 
-