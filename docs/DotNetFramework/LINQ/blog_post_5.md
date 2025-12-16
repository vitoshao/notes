---
title: LINQ to XML
layout: default
parent: LINQ
nav_order: 5
description: "LINQ to XML"
date: 2013-04-09
tags: [DotNetFramework,LINQ]
postid: "6827428727817373354"
---
要查詢 XML 文件，除了使用 XQuery ，另一種方法就是使用 [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) 類別(們)。  這些類別都包含在 [System.Xml.Linq](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.aspx) 命名空間中，  除了提供簡單快速的方法可以用來查詢 XML 文件，且都使用與先前相同的查詢模組。  

# The LINQ to XML Classes

與 [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) 相關的類別很多，且大都以 X 開頭，用以提供維護管理 XML 文件內容所需的功能，例如： [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 、 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 以及 [XNode](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xnode.aspx) 等等。  然而最主要還是以 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 類別為主，它表示一個 XML 項目，透過這個類別我們可以執行各種 XML 樹狀目錄的元素操作。  

下圖是以 XObject 為基礎所衍生的 [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) 相關類別架構

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8hRM8OvlEdggnPpALp55AIhdEDTAjfuSEIWSDB_7ptbxqclPGZW1z1w4eW7Q45t4oW5uz76Lflv42jRxJMJZJywd9hHfzROtPhBABi5Vx5zGSSC-ycAxYKnfAgwgbP9i4ZufZycUPYu8/s830/xobjects.png)

# Loading an XML Tree

在使用 [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) 之前，必須先將 XML 載入到記憶體中，  你可以使用 [XElement.Load](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.load.aspx) 或 [XElement.Parse](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.parse.aspx) 方法載入 **file**, **string**, or **XmlReader** 等格式的 XML 文件。  

## XDocument

[XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 類別代表一個 XML 文件。  你可以建立一個空的 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 實體，也可以透過指定特定的物件參數，建立包含此內容的 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 實體，其建構式如下：  
```c#
public XDocument();
public XDocument(params object[] content);
```

由 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 建構式可以得知，它接受一個非特定型別的物件當作參數傳入，以建立 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 實體，被用來當作參數的物件，通常包含XElement、XComment或是XDeclaration等等。  最簡單的XDocument沒有任何內容，換句話說，你可以透過預設建構式建立一個空白內容的 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 物件，然後經由相關方法的引用，逐步建立其中的XML元素，或是直接以所要建立的XML文件架構為基礎，透過上述的建構式，憑空建立一份文件。  

### 手工 DIY 建立 XDocument 物件
```c#
XDocument doc = new XDocument();

XElement xmlTree = new XElement("node_1",
new XElement("node_2.1","AAA"),
new XElement("node_2.2",
new XElement("node_2.2.1", "BBB"),
new XElement("node_2.2.2", "CCC"),
new XElement("node_2.2.3", "DDD")
),
new XElement("node_2.3", "EEE")
);
```

### 載入 XML 資料到 XDocument 物件
```c#
XElement xmlTree = XElement.Load(MapPath(@"CarList.xml"));
XDocument xdoc = new XDocument(xmlTree);
XNamespace ns = "http://vito.tw/";

//透過 Elements 取得項目
var query1 = from doc1 in xdoc.Root.Elements(ns + "Car")
select doc1;
foreach (XElement car in query1)
{
Console.WriteLine(car);
}

//透過 Descendants 取得項目
var query2 = from doc2 in xdoc.Descendants(ns + "Car")
select doc2;
foreach (XElement car in query2)
{
Console.WriteLine(car);
}
```

### XDocument 組成要素

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhlmfs62l3tKw0mBSw09vQIHzQQeLcfmth_iifE7PNGH5ny4uUhpmOBS5hBfziy9_gynwtguJjTLagK-zqM4WSsmYzgFUAmvVIvFleMbqtBv_j3kLGh0Z0URU9c5Z8xGWiC2pHlpv4YKP8/s516/XElement.png)

## XElement

[XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 類別是用來表示 XML 項目。

### XElement Constructor
```c#
public XElement(XElement other);
public XElement(XName name);
public XElement(XStreamingElement other);
public XElement(XName name, object content);
public XElement(XName name, params object[] content);
```

在一般的情形下，通常我們不會使用 [XDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xdocument.aspx) 建立 XML 文件實體，取而代之的，是透過 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 進行各種與 XML 有關的元素操作。  XDocument 用來表示一個特定的文件，除非你有需要，否則針對 XML 內容架構的操作，直接建立代表特定元素的 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 物件是比較合適的選擇。

### XAttribute

XAttribute 類別是用來表示 XML 屬性。
```c#
public XAttribute();
public XAttribute(XName name, object content);
```

若你要建立以下 XML 項目如下：
```c#
<Employee id="003" tel="1234">Vito</Employee>
```

你可以使用以下程式碼：
```c#
new XElement("Employee", "vito", new XAttribute("id", "003"), new XAttribute("tel", "1234"))
```

### XElement Method

- [XElement.Load](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.load.aspx) ：Loads an [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) from a string , file or XReader 。
- [XElement.Parse](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.parse.aspx) ：Load an [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) from a string that contains XML 。
```c#
XElement xmlTree = XElement.Load(MapPath(@"CarList.xml"));

foreach (XElement car in xmlTree.Elements())
{
Console.WriteLine(car);
}
```

# Writing LINQ Queries Against XML Trees

The queries you write with [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) can be seen as similar to XPath and XQuery.   However, they use the standard LINQ notation.  This ensures a single, consistent query model across different types of data.  You write [LINQ to XML](http://msdn.microsoft.com/zh-tw/library/bb387098.aspx) queries in a similar way as querying against a DataSet.   This is because the XML data, like the DataSet, is an in-memory store.  
```c#
string xmlFile = MapPath("employee.xml");
XElement employees = XElement.Load(xmlFile);

var query = from emp in employees.Elements("Employee")
where (int)emp.Element("Salary") > 30
select emp;

foreach (XElement emp in query)
{
myDebug.ResponseBR("EmployeeId={0}, FirstName={1}, Salary={2}",
emp.Element("EmployeeId").ToString(),
emp.Element("FirstName").ToString(),
emp.Element("Salary").ToString()
);
}
//EmployeeId=001, FirstName=aaa, Salary=40
//EmployeeId=002, FirstName=bbb, Salary=50
```
```xml
<?xml version="1.0" standalone="yes"?>
<Employees>
<Employee>
<EmployeeId>001</EmployeeId>
<LastName>AAA</LastName>
<FirstName>aaa</FirstName>
<Salary>40</Salary>
</Employee>
<Employee>
<EmployeeId>002</EmployeeId>
<LastName>BBB</LastName>
<FirstName>bbb</FirstName>
<Salary>50</Salary>
</Employee>
<Employee>
<EmployeeId>003</EmployeeId>
<LastName>CCC</LastName>
<FirstName>ccc</FirstName>
<Salary>20</Salary>
</Employee>
</Employees>
```

# Creating an XML Tree with XElement

####  [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 建構函式
```c#
public XElement(XElement other);
public XElement(XName name);
public XElement(XStreamingElement other);
public XElement(XName name, object content);
public XElement(XName name, params object[] content);
```

以上是 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 的建構函式，其中最後一個建構函式可建立具有指定內容和屬性的項目。  它可以使用類似 XML 結構的方法，在參數陣列中，巢狀呼叫 [XElement](http://msdn.microsoft.com/zh-tw/library/system.xml.linq.xelement.aspx) 建構函式，以產生 XML Tree。  

## 範例

### sample 1
```c#
string xmlFile = MapPath("employee.xml");
XElement empFile = XElement.Load(xmlFile);

XElement employees =
new XElement("Employees",
new XElement("Employee",
new XElement("EmployeeId", "MFS52347M"),
new XElement("FirstName", "Martin"),
new XElement("LastName", "Sommer"),
new XElement("Salary", 35)
),
from emp in empFile.Elements()
select emp
);

foreach (XElement emp in employees.Elements())
{
myDebug.ResponseBR("EmployeeId={0}, FirstName={1}, Salary={2}",
emp.Element("EmployeeId").ToString(),
emp.Element("FirstName").ToString(),
emp.Element("Salary").ToString()
);
}
//EmployeeId=MFS52347M, FirstName=Martin, Salary=35
//EmployeeId=001, FirstName=aaa, Salary=40
//EmployeeId=002, FirstName=bbb, Salary=50
//EmployeeId=003, FirstName=ccc, Salary=20
```

### sample 2
```c#
XElement xmlTree = new XElement("node_1",
new XElement("node_2.1","AAA"),
new XElement("node_2.2",
new XElement("node_2.2.1", "BBB"),
new XElement("node_2.2.2", "CCC"),
new XElement("node_2.2.3", "DDD")
),
new XElement("node_2.3", "EEE")
);
myDebug.ResponseBR("{0}",xmlTree);

//<node_1>
//  <node_2.1>AAA</node_2.1>
//  <node_2.2>
//    <node_2.2.1>BBB</node_2.2.1>
//    <node_2.2.2>CCC</node_2.2.2>
//    <node_2.2.3>DDD</node_2.2.3>
//  </node_2.2>
//  <node_2.3>EEE</node_2.3>
//</node_1>
```

### sample 3
```c#
XElement xmlTree = new XElement("Department",
new XElement("Name", "資訊部"),
new XElement("Memeber",
new XElement("姓名", "vito", new XAttribute("id", "E01"), new XAttribute("tel", "1234")),
new XElement("姓名", "shao", new XAttribute("id", "E02"), new XAttribute("tel", "1233")),
new XElement("姓名", "steve", new XAttribute("id", "E03"), new XAttribute("tel", "1232"))
),
new XElement("Manager", "E01")
);
//<Department>
//  <Name>資訊部</Name>
//  <Memeber>
//    <姓名 id="E01" tel="1234">vito</姓名>
//    <姓名 id="E02" tel="1233">shao</姓名>
//    <姓名 id="E03" tel="1232">steve</姓名>
//  </Memeber>
//  <Manager>E01</Manager>
//</Department>
```

### sample 4
```c#
//建立根節點
XElement root = new XElement("Department");

//建立Department的子節點
XElement nodeName = new XElement("Name", "資訊部");
XElement nodeMmeber = new XElement("Memeber");
//建立Memeber的子節點及屬性
nodeMmeber.Add(new XElement("姓名", "vito", new XAttribute("id", "E01"), new XAttribute("tel", "1234")));
nodeMmeber.Add(new XElement("姓名", "shao", new XAttribute("id", "E02"), new XAttribute("tel", "1233")));
nodeMmeber.Add(new XElement("姓名", "steve", new XAttribute("id", "E03"), new XAttribute("tel", "1232")));
XElement nodeManager = new XElement("Manager", "E01");

//將子節點加入根節點
root.Add(nodeName,nodeMmeber,nodeManager);

//將 XML Ttee 存檔
XDocument doc = new XDocument(root);
doc.Save(MapPath("employee_linq.xml"));

//<Department>
//  <Name>資訊部</Name>
//  <Memeber>
//    <姓名 id="E01" tel="1234">vito</姓名>
//    <姓名 id="E02" tel="1233">shao</姓名>
//    <姓名 id="E03" tel="1232">steve</姓名>
//  </Memeber>
//  <Manager>E01</Manager>
//</Department>
```

### sample 5

下面範例示範，如何將現有類別物件轉成 XML   

假設程式中包含以下類別  
```c#
public class Employee
{
public string Name { get; set; }
public List<Address> Address { get; set; }

public Employee()
{
Address = new List<Address>();
}
}
public class Address
{
public string AddressType { get; set; }
public string AddressValue { get; set; }
}

// 建立測試資料
List<Employee> EmployeeList = new List<Employee> ();

Employee emp1 = new Employee() { Name = "vito" };
emp1.Address.Add(new Address() { AddressType = "Email1", AddressValue = "vito@gmail.com" });
emp1.Address.Add(new Address() { AddressType = "Email2", AddressValue = "vito@yahoo.com" });
emp1.Address.Add(new Address() { AddressType = "MSN", AddressValue = "vito@hotmail.com" });

Employee emp2 = new Employee() { Name = "shao" };
emp2.Address.Add(new Address() { AddressType = "Email1", AddressValue = "shao@gmail.com" });
emp2.Address.Add(new Address() { AddressType = "MSN", AddressValue = "shao@hotmail.com" });

EmployeeList.Add(emp1);
EmployeeList.Add(emp2);
```
```c#
// 將上述資料轉 XML

var xmlEmp = new XElement( 
"Employees", 
from emp in EmployeeList select ( 
new XElement(
"Employee", 
new XAttribute("Name", emp.Name) , 
from add in emp.Address select new XElement("Address", new XAttribute(add.AddressType, add.AddressValue))
))
);

// 輸出
//<Employees>
//  <Employee Name="vito">
//    <Address Email1="vito@gmail.com" />
//    <Address Email2="vito@yahoo.com" />
//    <Address MSN="vito@hotmail.com" />
//  </Employee>
//  <Employee Name="shao">
//    <Address Email1="shao@gmail.com" />
//    <Address MSN="shao@hotmail.com" />
//  </Employee>
//</Employees>
```

## 什麼是 BOM （位元組順序記號, Byte-Order-Mark）

因為 XML 文件常被拿來當做資料交換使用，所以它的檔案格式就格外顯的的重要，稍有不慎，不同的應用軟體之間或許就無法溝通。  這裡要說明的是 [BOM](http://en.wikipedia.org/wiki/Byte_order_mark) （Byte-Order-Mark）這個東西。  因為常常有些軟體，無法讀取含 BOM 的 XML 文件。  

&gt;   
&gt; ##### BOM 是什麼？
&gt;   
&gt;   在使用 Unicode 編碼的文件裡，前頭都會有一個位元組順序記號（BOM），用來定義該文件的編碼是高位在前（big-endian）還是低位在前（little-endian）。  例如：UTF16 中，若前二碼為 FE FF ，表示高位在前；若前二碼為 FF FE ，表示低位在前。  
&gt;   UTF-8 BOM: ef bb bf 
&gt;     UTF-16BE BOM: fe ff   
&gt;     UTF-16LE BOM: ff fe 
&gt;     UTF-32BE BOM: 00 00 fe ff 
&gt;     UTF-32LE BOM: ff fe 00 00  
&gt;   這資訊對於 UTF16, UTF32 格式是必要的，但是在 UTF8 中就沒有意義，所以有時會被拿掉，也就因此 UTF8 有區分有 BOM 和無 BOM 二個版本。  UTF-8 是使用 0xEF,0xBB,0xBF 三個位元來當做 BOM 。  
&gt;   
&gt; ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiE8WzuJqruSuzWZjmG437-F4bf1J9dJRDhQFloj2mJvFQMnoKu5QiiUPHETXTvJrmlSZlkpIf4sVOCGpeqcbzopXe0C7OH-2K8jp4ibKLjtVjlVgXNlDqYQGYOCloOZzxg8isGCi9kEpE/s617/xml-bom.png)
&gt;   

上面範例中，我們使用 XDocument.Save 方法將 XDocument 序列化為 TextWriter ，它會序列為含 BOM 的 UTF8 文件。  如果要輸出不含 BOM 的檔案，可以參考以下做法：