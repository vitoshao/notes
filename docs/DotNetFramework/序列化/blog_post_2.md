---
title: XML 序列化
layout: default
parent: 序列化
nav_order: 2
description: "XML 序列化"
date: 2012-01-26
tags: [DotNetFramework,序列化]
postid: "8441384206609844263"
---
#### 什麼是 XML 

- XML 是一種以文字為主的標準化格式，可用來儲存應用程式的資訊，且具可讀性。
- XML 可以儲存任何型別的資料，包括文件、圖片、音樂、二進位檔案等資訊。

#### 為什麼使用 XML 

- Greater interoperability：因為 XML 是一個以文字為主的檔案標準(file standard)，所以大部分的開發環境都有支援。
- More administrator-friendly：可用文字編輯器進行檢視與編輯，在管理與問題排除上較為方便。
- Better forward-compatibility：XML 序列化的物件具有自我描述與容易處理特點，可讓新版的程式較輕易地處理物件。

#### 什麼是 XML [序列化](http://msdn.microsoft.com/zh-tw/library/7ay27kt9) 

- XML [序列化](http://msdn.microsoft.com/zh-tw/library/7ay27kt9) (Serialization) 是將物件的公用屬性和欄位轉換為 XML 格式的程序。
- 還原序列化 (Deserialization) 是從 XML 輸出將物件重新建立成它的原始狀態。

#### XML 序列化使用上的限制：

- 只能序列化公用資料，無法序列化私用資料。
- 不能序列化物件圖形([object graph](http://stackoverflow.com/questions/877157/what-is-an-object-graph-and-how-do-i-serialize-one))；只能序列化物件(object）。

# 如何使用 XML 序列化及還原序列化物件

##  XmlSerializer 類別：

####  [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.aspx) 建構子：
```c#
public XmlSerializer(Type type);
public XmlSerializer(Type type, string defaultNamespace);
public XmlSerializer(XmlTypeMapping xmlTypeMapping);
public XmlSerializer(Type type, Type[] extraTypes);
...
```

###  [Serialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.serialize.aspx) and [Deserialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.deserialize.aspx) 方法：  ```c#
protected virtual void Serialize(object o, XmlSerializationWriter writer);
public void Serialize(Stream serializationStream, object graph, Header[] headers);
public void Serialize(Stream stream, object o);
public void Serialize(TextWriter textWriter, object o);
public void Serialize(XmlWriter xmlWriter, object o);
public void Serialize(Stream stream, object o, XmlSerializerNamespaces namespaces);
....

public object Deserialize(Stream stream);
public object Deserialize(TextReader textReader);
public object Deserialize(XmlReader xmlReader);
protected virtual object Deserialize(XmlSerializationReader reader);
...
```

##  XML 序列化物件的步驟：

1. Create a **Stream**, **TextWriter**, or **XmlWriter** object to hold the serialized output.
2. Create an [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer) object by passing it the type of object you plan to serialize.
3. Call the [Serialize](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.serialize) method to serialize the object and output the results to the stream.
```c#
// 建立儲存的檔案
FileStream fs = new FileStream("DateTime.XML", FileMode.Create);

// 以序列化物件的型別為參數，建立 XmlSerializer 物件
XmlSerializer xSerializer = new XmlSerializer(typeof(DateTime));

// 執行 XmlSerializer.Serialize
xSerializer.Serialize(fs, System.DateTime.Now);

fs.Close();
```
```xml
<?xml version="1.0"?>
<dateTime>2012-06-29T17:43:53.0514221+08:00</dateTime> 
```

##  XML 還原序列化物件的步驟：

1. Create a stream, TextReader, or XmlReader object to read the serialized input.
2. Create an [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer) object by passing it the type of object you plan to deserialize.
3. Call the [Deserialize](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.deserialize) method to deserialize the object, and cast it to the correct type.
```c#
// 建立 FileStream 開啟序列化資料
FileStream fs = new FileStream("DateTime.XML", FileMode.Open);

// 以序列化物件的型別為參數，建立 XmlSerializer 物件
XmlSerializer xSerializer = new XmlSerializer(typeof(DateTime));

// 執行 XmlSerializer.Deserialize
DateTime previousTime = (DateTime)xSerializer.Deserialize(fs);

fs.Close();
Console.WriteLine("Day: " + previousTime.DayOfWeek + ", Time: " + previousTime.TimeOfDay.ToString());
// Day: Friday, Time: 17:43:53.0514221
```

# 如何建立可被 XML 序列化的類別：

使用 XML 序列化對類別的處理上與標準序列化的需求有些許不同。  XML 序列化處理類別時，**不一定**要有 [Serializable](http://msdn.microsoft.com/zh-tw/library/bcfsa90a) 屬性。  而且序列化過程中會忽略私有或保護的成員(private or protected member)。  所以，要建立一個可以被 XML 序列化的物件，必須符合以下規則：  

1. public class：該類別必須是公開類別。
2. public properties and fields ：只可以序列化公用屬性和欄位。若必須序列化非公用資料，請使用 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.aspx) 類別。
3. default constructor：該類別必須包含一個不帶參數的建構函式。

範例如下：
```c#
//類別必須是 public 的，才允許被序列化
public class Grade
{
public string name;     //設定成 public 的成員才會被序列化
public int math;
public int english;
public int chinese;
private int total;

//必須包含一個沒有參數的建構函式
public Grade()
{
}

public Grade(string _name, int _math, int _english, int _chinese)
{
name = _name;
math = _math;
english = _english;
chinese = _chinese;
total = _math + _english + _chinese;
}

public string result()
{
if ((total) > 180)
return "pass";
else
return "fail";
}
}
```

# 如何控制 XML 序列化

## XML 的項目(element)與屬性(attribute)

上述範例，因為我們執行序列化時，沒有額外特別控制序列化的屬性，系統會使用預設的設定，將每一個成員都序列化成一個項目(element)，如下所示：  
```xml
<?xml version="1.0"?>
<Student">
<name>vito</name>
<id>S0001</id>
<math>80</math>
<english>90</english>
<chinese>30</chinese>
<total>200</total>
</Student>
```

但是，有時候為了符合某些特定需求，在序列化建立 XML 文件時，必須產出符合特定結構描述的 XML 文件。  如下面這個文件所示，一個項目 (element) 包含了其他項目，而且項目也使用屬性 (attribute) 來加以描述。  
```xml
// name, id 都是用來描述項目的屬性(attributes)，
// math, english, chinese 都是一個項目(elements)
// StudentGrade 是一個項目, 而且包含３個其他項目

<?xml version="1.0"?>
<StudentGrade  name="vito" id="S0001">       
<math>80</math>                           
<english>90</english>                     
<chinese>30</chinese>
</StudentGrade>                             
```

像這樣子的一份文件，是不是更具閱讀性呢？那要如何做才能產出符合這樣結構描述的 XML 文件呢？有二種方式可以達到這個目的：  

- 透過 XML 序列化屬性的設定。(請參考： [控制 XML 序列化的屬性](http://msdn.microsoft.com/zh-tw/library/83y7df3e) )
- 撰寫自訂 XML 序列化的程式碼。

## 設定 XML 序列化屬性

下表是幾個常用的控制 XML 序列化屬性，其他詳細的屬性可參考MSDN：[控制 XML 序列化的屬性](http://msdn.microsoft.com/zh-tw/library/83y7df3e)

- [XmlAnyAttribute](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlanyattributeattribute) ：
- [XmlAnyElement](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlanyelementattribute) ：
- [XmlArray](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlarrayattribute) ：
- [XmlArrayItem](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlarrayitemattribute) ：控制陣列中可插入的型別。
- [XmlAttribute](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlattributeattribute) ：將成員序列化為 attribute。
- [XmlElement](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlelementattribute) ：將成員序列化成一個 item。也可指定序列化後的屬性名稱。
- [XmlEnum](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlenumattribute) ：
- [XmlIgnore](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlignoreattribute) ：控制不要序列化的成員。
- [XmlInclude](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlincludeattribute) ：當 [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.aspx) 序列化同時包含基底類別與衍生類別的物件時，套用 XmlIncludeAttribute ，讓 [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.aspx) 在對物件進行序列化或還原序列化時，能夠辨識型別。
- [XmlRoot](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlrootattribute) ：控制 root item 序列化後的項目名稱或命名空間。
- [XmlText](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmltextattribute) ：將成員序列化成 XML 文字。
- [XmlType](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmltypeattribute) ：

### 範例１

底下這個範例，我們透過XML序列化屬性，指定部分欄位的序列化屬性，以建立一個符合特定 XML 需求的序列化類別。例如我們的需求為：  

- 將類別名稱由 Student 變更成為 StudentGrade。
- 將項目名稱由 name 轉換成 StudentName
- 讓 id 由項目 (element) 變成屬性 (attribute)。
- 讓 total 欄位，不要納入序列化文件中。

依照需求，修改後的類別如下：  
```c#
//可被 XLM 序列化的類別
[XmlRoot ("StudentGrade")]              //將 Student 變更成 StudentGrade
public class Student
{
[XmlElement(ElementName = "StudentName")]
public string name;             //指明序列化後，將項目名稱 name 轉換成 StudentName
[XmlAttribute]
public string id;               //讓 id 成為 attribute，而不是 element
public int math;
public int english;
public int chinese;

[XmlIgnore]
public int total;        //不要將total納入序列化文件

public Student()｝       //必須指定一個沒有參數的 constructor

public Student(string _name, int _math, int _english, int _chinese)
{
name = _name;
math = _math;
english = _english;
chinese = _chinese;
total = _math + _english + _chinese;
}
}
```

底下就是上面自訂類別，經 XML 序列化後輸出的內容。  
```xml
<?xml version="1.0"?>
<StudentName>vito</StudentName>
<math>80</math>
<english>90</english>
<chinese>30</chinese>
</StudentGrade>
```

### 範例２
```c#
public class Address
{
[XmlAttribute]
public int AddressType;
public string Line1;
public string Line2;
[XmlIgnore]
public string Line3;
[XmlElement]
public string City;
[XmlElement("ZipCode")]
public string ZipPostalCode;
}
```
```c#
Address address = new Address
{
AddressType= 2,
Line1 = "250 Race Court",
Line3 = "No 306",
City = "Chicago",
ZipPostalCode = "60603"
};

XmlSerializer xSerializer = new XmlSerializer(typeof(Address));
FileStream fs = new FileStream("Address.XML", FileMode.Create);

xSerializer.Serialize(fs, address);

fs.Close();

// 序列化結果
// 1.null 欄位不會顯示
// 2.標記 XmlAttribute 的欄位，會以屬性方式顯示
// 3.沒有任何標記的欄位，都會當做 XmlElement
// 4.標記 XmlElement 的欄位，可以變更顯示的項目名稱
// 5.標記 XmlIgnore 的欄位，會略過序列化

//<Address AddressType="2">
//  <Line1>250 Race Court</Line1>
//  <City>Chicago</City>
//  <ZipCode>60603</ZipCode>
//</Address>
```

## 自訂 XML 序列化

使用上面介紹的序列化屬性設定，一般就可以滿足大部份的需求，不過，也可以完全 DIY 來完整控制 XML 序列化的動作，這時候就必須實作 [IXmlSerializable](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.ixmlserializable) 介面。  實作這個介面必須實作該介面的 [ReadXml](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.ixmlserializable.readxml) 和 [WriteXml](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.ixmlserializable.writexml) 方法。也就是透過 XmlReader 和 XmlWrite 自行讀寫 XML 文件。

## UnknownNode 事件

當執行還原序列化時，遇到未知型別的 XML 節點時，通常該節點會被忽略掉，如果想記錄這些資料，就可以使用 [UnknownNode](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.unknownnode.aspx) 事件。  

利用上面例子，我們在序列化後的 XML 資料中隨意插入一個項目資料，再進行還原序列化。並且在還原序列化的步驟中訂閱 [UnknownNode](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.unknownnode.aspx) 事件。  
```c#
//進行還原序列化
private void button8_Click(object sender, EventArgs e)
{
// Create an XmlSerializer object
FileStream fs = new FileStream("Grade.XML", FileMode.Open);
XmlSerializer xSerializer = new XmlSerializer(typeof(Grade));

xSerializer.UnknownNode += UnknownNodeHandle;  // 訂閱 UnknownNode 事件

// Deserialize
Grade grade = (Grade)xSerializer.Deserialize(fs);

// Close the file
fs.Close();
｝

//未知節點處理函式
public void UnknownNodeHandle(object sender, XmlNodeEventArgs e)
{
Console.WriteLine("未知節點行號: {0}", e.LineNumber);  
Console.WriteLine("未知節點名稱: {0}", e.Name);        
Console.WriteLine("未知節點型態: {0}", e.NodeType);    

//未知節點行號: 8
//未知節點名稱: test
//未知節點型態: Element
}
```
```xml
<?xml version="1.0"?>
<Grade>
<StudentName>vito</StudentName>
<math>80</math>
<english>90</english>
<chinese>30</chinese>
<test>30</test>
</Grade>
```

# 如何遵循 XML 的結構描述

一般要設計一個可供不同系統交換資料用的 XML 檔案時，開發人員必須先建立一個用來描述 XML 文件的 XML 結構描述檔 (.xsd , Xml Schema Definition)。  不過，如果已經先存在一個 XML 文件，那要如何設計一個可序列化的類別來還原序列化該 XML 文件呢？  

這時候就可以使用 MS 提供的 XML 結構描述定義工具 ([Xsd.exe](https://msdn.microsoft.com/zh-tw/library/vstudio/x6c1kb0s%28v=vs.100%29.aspx)) ，來幫我們建立這個結構描述檔。  這個工具還可以將結構描述檔再轉換成可序列化的類別檔。轉操作步驟可參考下面示範：（.xml =&gt; .xsd =&gt; .cs ）。  
```c#
xsd.exe C:\test.xml                //=>  利用文件檔(.xml)，產生結構描述檔(.xsd)
xsd.exe C:\test.xsd /classes       //=>  利用結構描述檔(.xsd)，產生類別檔(.cs)
```

之後，再將產生的類別，加進專案中即可

# 如何序列化 DataSet

除了公用類別(public class)可以序列化之外，像 DataSet 這種物件的 instant 也可以序列化。如下列範例所示：
```c#
DBService db = DBService.CreateService();
DataSet ds = db.DBObject.GetDataSet("SELECT \* FROM tblBlogSpot");

TextWriter writer = new StreamWriter("DataSet.XML");
XmlSerializer xSerializer = new XmlSerializer(typeof(DataSet));
xSerializer.Serialize(writer, ds);
writer.Close();
```
```c#
FileStream fs = new FileStream("DataSet.XML", FileMode.Open);
XmlSerializer xSerializer = new XmlSerializer(typeof(DataSet));
DataSet ds = (DataSet) xSerializer.Deserialize(fs);
fs.Close();
```

雖然我們同樣可以直接使用 XML [序列化](http://msdn.microsoft.com/zh-tw/library/7ay27kt9.aspx) Dataset 物件，不過，我們無法使用像自訂類別那樣的控制樣式。替代方式就是使用 DataSet 物件的 [WriteXml](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.writexml.aspx) 、 [ReadXml](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.readxml.aspx) 、 [GetXml](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.getxml.aspx) 等方法：  
```c#
DBService db = DBService.CreateService();
DataSet ds = db.DBObject.GetDataSet("SELECT \* FROM tblBlogSpot");

ds.WriteXml("DataSet_WriteXml.XML");
```
```c#
FileStream fs = new FileStream("DataSet_WriteXml.XML", FileMode.Open);
DataSet ds = new DataSet();
ds.ReadXml(fs);
fs.Close();
```

# 如何序列化階層式的類別 (hierarchy class)
```c#
public class Group
{
// 若序列化的資料包含陣列，比須使用 XmlArrayItemAttribute 這個屬性，告知陣列元素的型別

[XmlArrayItem(Type=typeof(Employee))]
[XmlArrayItem(Type=typeof(Manager))]
public Employee[] Employees;
public Group(){}
}

public class Employee
{
public string Name;
public Employee() { }
}

public class Manager : Employee
{
public int Level;
public Manager(){}
}
```
```c#
Group group = new Group();

Employee emp1 = new Employee();
emp1.Name = "vito";

Employee emp2 = new Employee();
emp2.Name = "shao";

Manager manager = new Manager();
manager.Name = "peter";
manager.Level = 1;

group.Employees = new Employee[3] { emp1, emp2, manager };

FileStream fs = new FileStream("Group.XML", FileMode.Create);
XmlSerializer xSerializer = new XmlSerializer(typeof(Group));
xSerializer.Serialize(fs, group);
fs.Close();
```
```c#
<?xml version="1.0"?>
<Group xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Employees>
<Employee>
<Name>vito</Name>
</Employee>
<Employee>
<Name>shao</Name>
</Employee>
<Manager>
<Name>peter</Name>
<Level>1</Level>
</Manager>
</Employees>
</Group>
```
```c#
Group group = new Group();

Employee emp1 = new Employee();
emp1.Name = "vito";

Employee emp2 = new Employee();
emp2.Name = "shao";

Manager manager = new Manager();
manager.Name = "peter";
manager.Level = 1;

group.Employees = new Employee[3] { emp1, emp2, manager };

FileStream fs = new FileStream("Group.XML", FileMode.Create);
XmlSerializer xSerializer = new XmlSerializer(typeof(Group));
xSerializer.Serialize(fs, group);
fs.Close();
```

# 如何序列化陣列

#### 序列化
```c#
public class People
{
public string name;
public int age;
public People() { }

public People(string _name, int _age)
{
name = _name;
age = _age;
}
}

private void button17_Click(object sender, EventArgs e)
{
People[] peoples = new People[3];

peoples[0] = new People("tony", 40);
peoples[1] = new People("wu", 35);
peoples[2] = new People("vicky", 30);

FileStream fs = new FileStream("array_people.XML", FileMode.Create);
XmlSerializer xSerializer = new XmlSerializer(typeof(People[]));
xSerializer.Serialize(fs, peoples);
fs.Close();
}
```

底下是序列化的結果。
```xml
<?xml version="1.0"?>
<ArrayOfPeople xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<People>
<name>tony</name>
<age>40</age>
</People>
<People>
<name>wu</name>
<age>35</age>
</People>
<People>
<name>vicky</name>
<age>30</age>
</People>
</ArrayOfPeople>
```

#### 還原序列化

當進行還原序列化時，若遇到未知的節點內容，通常都會忽略過去。若想要欄截記錄這些資訊，可以訂閱 [UnknownNode](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer.unknownnode.aspx) 事件即可。  

例如，我們可以將上面序列化的資料中先加入如下的節點資料，再進行還原序列化。
```xml
<Pen>
<name>pencil</name>
<price>35</price>
</Pen>
```
```c#
//未知節點處理函式
public void UnknownNodeHandle(object sender, XmlNodeEventArgs e)
{
Console.WriteLine("未知節點行號: {0}", e.LineNumber);  
Console.WriteLine("未知節點名稱: {0}", e.Name);        
Console.WriteLine("未知節點型態: {0}", e.NodeType);    

//未知節點行號: 8
//未知節點名稱: test
//未知節點型態: Element
}

private void button18_Click(object sender, EventArgs e)
{
// Open file
FileStream fs = new FileStream("array_people.XML", FileMode.Open);

// Create an XmlSerializer object
XmlSerializer xSerializer = new XmlSerializer(typeof(People[]));

// 訂閱 UnknownNode Event
xSerializer.UnknownNode += new XmlNodeEventHandler(UnknownNodeHandle);

// 進行還原序列化
People[] peoples = (People[])xSerializer.Deserialize(fs);

// Close the file
fs.Close();

foreach(People people in peoples)
{
Console.WriteLine(people.name); 
}
//tony
//wu
//vicky
}
```

# 如何序列化 Object 型態的欄位或屬性

從以上的說明我們知道，只要有型別的公開欄位或屬性，這個欄位或屬性都會自動被序列化。  可是如果欄位或屬性的型別是 Object 時怎麼辨呢?  
```c#
public abstract class Product
{
public int Price { get; set; }
}

public class Book : Product
{
public string BookNo { get; set; }
public string BookName { get; set; }
}
public class Pen : Product
{
public string PenNo { get; set; }
public string PenName { get; set; }
}
public class Order
{
public string OrderNo;
public DateTime OrderDate;
public bool Error;
[XmlIgnore]
public int Total
{
get
{
int total = 0;
foreach (Product product in Products)
{
total += product.Price;
}
return total;
}
}
public string MemberID { get; set; }

[XmlElement(ElementName = "Product")]
public object[] Products;
}
```
```c#
MemoryStream memStrem ;
private void button24_Click(object sender, EventArgs e)
{
Order order = new Order();
order.OrderNo = "ON201205";
order.OrderDate = new DateTime(2010, 12, 25);
order.MemberID = "M0009";

Book book1 = new Book("B0001", "CSharp", 100);
Book book2 = new Book("B0002", "VB.NET", 200);
Pen pen1 = new Pen("P0001", "PILOT", 50);

object[] product = new object[3] {book1, book2, pen1};
order.Products = product;
Console.WriteLine("Total = {0}", order.Total.ToString());

// 建立型別陣列
Type[] extraTypes = new Type[2] { typeof(Book), typeof(Pen) };

XmlSerializer xSerializer = new XmlSerializer(typeof(Order), extraTypes);   //指定允許放置陣列中的型別
memStrem = new MemoryStream();
xSerializer.Serialize(memStrem, order);
memStrem.Flush();

FileStream fileStream = new FileStream("Order.XML", FileMode.Create);
xSerializer.Serialize(fileStream, order);
fileStream.Flush();
fileStream.Close();
}
```
```c#
private void button23_Click(object sender, EventArgs e)
{
Type[] extraTypes = new Type[2];
extraTypes[0] = typeof(Book);
extraTypes[1] = typeof(Pen);

memStrem.Seek(0, SeekOrigin.Begin);
XmlSerializer xSerializer = new XmlSerializer(typeof(Order), extraTypes);
Order order = (Order)xSerializer.Deserialize(memStrem);

Console.WriteLine("Total = {0}", order.Total.ToString());
}
```
```xml
<?xml version="1.0"?>
<Order xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<OrderNo>ON201205</OrderNo>
<OrderDate>2010-12-25T00:00:00</OrderDate>
<Error>false</Error>
<Product xsi:type="Book">
<Price>100</Price>
<BookNo>B0001</BookNo>
<BookName>CSharp</BookName>
</Product>
<Product xsi:type="Book">
<Price>200</Price>
<BookNo>B0002</BookNo>
<BookName>VB.NET</BookName>
</Product>
<Product xsi:type="Pen">
<Price>50</Price>
<PenNo>P0001</PenNo>
<PenName>PILOT</PenName>
</Product>
<MemberID>M0009</MemberID>
</Order>
```

# Datatable vs XML

Datatable 和 Dataset 都有提供 ReadXml 、 WriteXml 方法，可以快速地將資料轉譯成 XML 文件。以下為程式碼範例

#### Datatable 轉 XML 文件
```c#
DBService db = DBService.CreateService();
DataTable dtUser = db.DBObject.GetDataTable("SELECT Top 10 UserID,UserName,Birthday FROM tblUser ORDER BY UserID", "User");
dtUser.WriteXml(@"User.xml"); 
```

#### XML 文件轉 Datatable
```c#
DataTable table = new DataTable("User");
try
{
//open the file using a Stream 
using (Stream stream = new FileStream(@"User.xml", FileMode.Open, FileAccess.Read))
{
//create the table with the appropriate column names 
table.Columns.Add("UserID", typeof(string));
table.Columns.Add("UserName", typeof(string));
table.Columns.Add("Birthday", typeof(DateTime));

//use ReadXml to read the XML stream 
table.ReadXml(stream);
}
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
} 
```

#### 使用還原序列化將 XML 文件轉物件類別

#### 製作對應的物件類別

如果說，當你拿到一個 XML 文件，必須將它轉成自訂類別物件時，這時候就必須依據該 XML 文件的格式，製作一個符合該格式且可被序列化的自訂類別，才能使用還原序列化的方法，將資料還原成類別物件。

除了自已動手設計該類別外，.NET 也有提供一個 xsd.exe 工具，可較快速的依照 XML 文件內容，製作出相對應的可序列化類別，其製作步驟如下，之後，再將產生的類別，加進專案中即可使用。
```c#
xsd.exe C:\User.xml                //=>  利用文件檔(.xml)，產生結構描述檔(.xsd)
xsd.exe C:\User.xsd /classes       //=>  利用結構描述檔(.xsd)，產生類別檔(.cs)
```

下面文件即 xsd.exe 工具產生的類別檔
```c#
//------------------------------------------------------------------------------
// <auto-generated>
//     這段程式碼是由工具產生的。
//     執行階段版本:4.0.30319.269
//
//     對這個檔案所做的變更可能會造成錯誤的行為，而且如果重新產生程式碼，
//     變更將會遺失。
// </auto-generated>
//------------------------------------------------------------------------------

using System.Xml.Serialization;

// 
// 此原始程式碼由 xsd 版本=4.0.30319.1 自動產生。
// 

/// <remarks/>
[System.CodeDom.Compiler.GeneratedCodeAttribute("xsd", "4.0.30319.1")]
[System.SerializableAttribute()]
[System.Diagnostics.DebuggerStepThroughAttribute()]
[System.ComponentModel.DesignerCategoryAttribute("code")]
[System.Xml.Serialization.XmlTypeAttribute(AnonymousType=true)]
[System.Xml.Serialization.XmlRootAttribute(Namespace="", IsNullable=false)]
public partial class DocumentElement {

private DocumentElementUser[] itemsField;

/// <remarks/>
[System.Xml.Serialization.XmlElementAttribute("User", Form=System.Xml.Schema.XmlSchemaForm.Unqualified)]
public DocumentElementUser[] Items {
get {
return this.itemsField;
}
set {
this.itemsField = value;
}
}
}

/// <remarks/>
[System.CodeDom.Compiler.GeneratedCodeAttribute("xsd", "4.0.30319.1")]
[System.SerializableAttribute()]
[System.Diagnostics.DebuggerStepThroughAttribute()]
[System.ComponentModel.DesignerCategoryAttribute("code")]
[System.Xml.Serialization.XmlTypeAttribute(AnonymousType=true)]
public partial class DocumentElementUser {

private string userIDField;

private string userNameField;

private string birthdayField;

/// <remarks/>
[System.Xml.Serialization.XmlElementAttribute(Form=System.Xml.Schema.XmlSchemaForm.Unqualified)]
public string UserID {
get {
return this.userIDField;
}
set {
this.userIDField = value;
}
}

/// <remarks/>
[System.Xml.Serialization.XmlElementAttribute(Form=System.Xml.Schema.XmlSchemaForm.Unqualified)]
public string UserName {
get {
return this.userNameField;
}
set {
this.userNameField = value;
}
}

/// <remarks/>
[System.Xml.Serialization.XmlElementAttribute(Form=System.Xml.Schema.XmlSchemaForm.Unqualified)]
public string Birthday {
get {
return this.birthdayField;
}
set {
this.birthdayField = value;
}
}
}
```

#### 還原序列化

只要利用上面的類別，就可以直接進行還原序列化的操作
```c#
FileStream fs = new FileStream(@"User.XML", FileMode.Open, FileAccess.Read);
XmlSerializer xmls = new XmlSerializer(typeof(DocumentElement));
DocumentElement user = (DocumentElement)xmls.Deserialize(fs);
fs.Close();
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [XML 序列化簡介](http://msdn.microsoft.com/zh-tw/library/182eeyhh%28v=vs.100%29.aspx)
- [序列化物件](http://msdn.microsoft.com/zh-tw/library/szzyf24s)
- [還原序列化物件](http://msdn.microsoft.com/zh-tw/library/fa420a9y)