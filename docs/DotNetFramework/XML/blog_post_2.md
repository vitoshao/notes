---
title: 編輯與讀取 XML 文件資料
layout: default
parent: XML
nav_order: 2
description: "編輯與讀取 XML 文件資料"
date: 2012-09-16
tags: [DotNetFramework,XML]
postid: "1101756146071118285"
---
# XML 文件物件模型

## 什麼是 XML 文件物件模型 (Document Object Model, DOM)

DOM 簡單講就是一套用來隨機存取、瀏灠、修改 XML 文件的共同標準。

- 任何 XML 文件都必須(且僅有)有一個根節點(root)。
- 標籤名稱區分大小寫
- 每個元素(Element)由一組封閉的標籤構成，例如下例中的 Style, IconStyle, scale 等等都是一個元素。
- 每個屬性(Attributes)必須包在元素的標籤範圍內，其值必須使用單引號或雙引號圈住，例如下例中的 id 屬性。
```xml
<Style id="point">
<IconStyle>
<scale>0.9</scale>
<Icon>
<href>http://maps.google.com/mapfiles/kml/shapes/placemark_circle.png</href>
</Icon>
</IconStyle>
</Style>
```

## XML 相關物件

在 .NET Framework 中，要操作 XML 文件，會使用到的物件大至上有以下幾個：

#### XML Namespace

要使用 XML 類別必須先加入 System.Xml.dll 參考，並用引用 [System.Xml](http://msdn.microsoft.com/zh-tw/library/system.xml.aspx) 命名空間。

#### XML Objects

- [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) ：表示 XML 文件。
- [XmlDataDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldatadocument.aspx) ：允許透過關聯式 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 存放、擷取及管理結構化資料。(已過時)
- [XmlConvert](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlconvert.aspx) ：編碼和解碼 XML 名稱，並提供 CLR 與 XSD (XML Schema Definition, 結構描述定義語言) 資料型別的轉換。
- [XmlReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.aspx) ：表示提供快速、非快取、順向存取 XML 資料的讀取器 (Reader)。
- [XmlNodeReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnodereader.aspx) ：表示提供快速、 非快取、 順向存取 XML 資料到 XmlNode。
- [XmlTextReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.aspx) ：表示提供快速、 非快取、 順向存取 XML 資料讀取器
- [XmlTextWriter](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.aspx) ：表示提供快速、 非快取、 順向的方式來產生資料流或XML
- [XslTransform](http://msdn.microsoft.com/zh-tw/library/system.xml.xsl.xsltransform.aspx) ：將使用可延伸樣式表語言轉換 (XSLT) 樣式表的 XML 資料轉換。(已過時)
- [XPathDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathdocument.aspx) ：使用 XPath 資料模型，以提供 XML 文件的快速、 唯讀、 於記憶體中表示。
- [XPathNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathnavigator.aspx) ：提供游標模型來巡覽及編輯 XML 資料。

#### XmlDocument and XmlDataDocument

- The [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) and [XmlDataDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldatadocument.aspx) objects are in-memory representations of XML that use the DOM Level 1 and Level 2. These classes can be used to navigate and edit the XML nodes
- The [XmlDataDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldatadocument.aspx) inherits from the [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) and also represents relational data.
- The [XmlDataDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldatadocument.aspx) can expose its data as a [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) to provide relational and nonrelationalviews of the data.
- The [XmlDataDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldatadocument.aspx) is in the System.Data.dll assembly.

#### XPathDocument

The [XPathDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathdocument.aspx) provides a cached read-only [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) that can be used for performing quick XPath queries. The constructor for this class requires a stream object to create an instance of this object. The only useful method that this class exposes is the [CreateNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createnavigator.aspx) method.

#### XmlConvert

The [XmlConvert](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlconvert.aspx) class has many static methods for converting between XSD data types and common language runtime (CLR) data types.You can use the [XmlConvert.DecodeName](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlconvert.decodename.aspx) method to encode the invalid XML names in the data sources.

#### XmlReader

The [XmlReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.aspx) provides an object for reading and validating against DTD, XML Schema Reduced (XDR), or XSD. The constructor expects a reader or a string as the source of the XML to be validated.

這個類別能夠讀取 XML DOM 樹狀子目錄，但不支援文件類型定義 (DTD) 或結構描述驗證。若要執行資料驗證，請使用 [Validating [XmlReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.aspx) Object]。這個類別有12個同名的靜態方法 [Create](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.create.aspx) ，可以用來建立新的 System.Xml.XmlReader 執行個體。
```c#
public static XmlReader Create(Stream input);
public static XmlReader Create(string inputUri);
public static XmlReader Create(TextReader input);
public static XmlReader Create(Stream input, XmlReaderSettings settings);
public static XmlReader Create(XmlReader reader, XmlReaderSettings settings);
...
```

#### XmlNodeReader

The [XmlNodeReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnodereader.aspx) provides forward-only access to data in an [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) or XmlDataDocument. It provides the ability to start at a given node in the [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) and sequentially read each node.

#### XmlTextReader

The [XmlTextReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.aspx) provides noncached, forward-only access to XML data. It parses XML tokens but makes no attempt to represent the XML document as a DOM. The [XmlTextReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.aspx) does not perform document validation, but it checks the XML data to ensure that it is well formed.

#### XmlTextWriter

The [XmlTextWriter](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.aspx) provides noncached, forward-only writing of XML data to a stream of files, ensuring that the data conforms to the W3C XML 1.0 standard. The [XmlTextWriter](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.aspx) contains logic for working with namespaces and resolving namespace conflicts.

## 建立 XML 文件

[XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) 這個類別可以在記憶體中建立樹狀結構的 XML 文件模型，並且具有瀏覽和編輯這份文件的功能。底下為幾個常用的方法：

- [CreateCDataSection](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createcdatasection.aspx) ：建立 XmlCDataSection 包含指定的資料。
- [CreateXmlDeclaration](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createxmldeclaration.aspx) ：建立 XmlDeclaration 節點。
- [CreateElement](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createelement.aspx) ：建立XmlElement。
- [CreateAttribute](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createattribute.aspx) ：建立XmlAttribute與指定的名稱。
```c#
protected void btnCreateXmlDocument_Click(object sender, EventArgs e)
{
//建立新的XML文件
XmlDocument xmlDoc = new XmlDocument();
XmlElement rootNode;
int childCounter;
int grandChildCounter;

//建立 Declaration
XmlDeclaration declNode;
declNode = xmlDoc.CreateXmlDeclaration("1.0", "utf-8", null);
xmlDoc.AppendChild(declNode);

//建立 CData
XmlCDataSection CData;
CData = doc.CreateCDataSection("All Jane Austen novels 25% off starting 3/23!");
xmlDoc.AppendChild(rootNode);

//建立 root node 
rootNode = xmlDoc.CreateElement("RootNode");
xmlDoc.AppendChild(rootNode);

//建立 child node 
for (childCounter = 1; childCounter <= 2; childCounter++)
{
XmlElement childNode;
XmlAttribute childAttribute;

//指定 child node 的 ID 和 Attribute
childNode = xmlDoc.CreateElement("ChildNode");
childAttribute= xmlDoc.CreateAttribute("NodeID");
childAttribute.Value= "Node" + childCounter.ToString();
childNode.Attributes.Append(childAttribute);

//Append ChildNode into the RootNode
rootNode.AppendChild(childNode);

//建立 grandchildren node
for (grandChildCounter = 1; grandChildCounter <= 3; grandChildCounter++)
{
//Create grandchildren 
XmlElement grandNode = xmlDoc.CreateElement("GrandNode");
grandNode.InnerText = "I am grand child";
childNode.AppendChild(grandNode);
}
}

//儲存檔案
xmlDoc.Save(MapPath("XmlDocumentTest.xml"));
lbl.Text = "XmlDocumentTest.xml Created";
}
```
```xml
<?xml version="1.0" encoding="utf-8" ?>
<rootnode>
<![CDATA[All Jane Austen novels 25% off starting 3/23!]]>
<ChildNode NodeID="Node1">
<GrandNode>I am grand child 1</GrandNode>
<GrandNode>I am grand child 2</GrandNode>
<GrandNode>I am grand child 3</GrandNode>
</ChildNode>
<ChildNode NodeID="Node2">
<GrandNode>I am grand child 1</GrandNode>
<GrandNode>I am grand child 2</GrandNode>
<GrandNode>I am grand child 3</GrandNode>
</ChildNode>
</rootnode>
```

## 修改 Xml 文件

載入 [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) 物件之後，就可以簡單的加入或移除節點：

- [CreateNode](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createnode.aspx) ：建立XmlNode。
- [RemoveChild](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnode.removechild.aspx) ：移除指定的子節點。
- [InsertAfter](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnode.insertafter.aspx) ：在指定的參考節點之後，插入指定的節點。
- [InsertBefore](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnode.insertbefore.aspx) ：在指定的參考節點之前，插入指定的節點。
```c#
protected void btnModifyXmlDoc_Click(object sender, EventArgs e)
{
//載入文件
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(MapPath("XmlDocumentSample.xml"));

//刪除節點
XmlNode node;
node = xmlDoc.SelectSingleNode("//ChildNode[@NodeID='Node3']");
node.ParentNode.RemoveChild(node);

//加入節點
XmlElement newElement = xmlDoc.CreateElement("myNewElement");
node = xmlDoc.SelectSingleNode("//ChildNode[@NodeID='Node1']");
node.ParentNode.InsertAfter(newElement, node);

xmlDoc.Save(MapPath("XmlDocumentSample.xml"));
Response.Redirect("XmlDocumentSample.xml");
}
```
```xml
<?xml version="1.0" encoding="utf-8" ?>
<rootnode>
<![CDATA[All Jane Austen novels 25% off starting 3/23!]]>
<ChildNode NodeID="Node1">
<GrandNode>I am grand child 2</GrandNode>
<GrandNode>I am grand child 3</GrandNode>
</ChildNode>
<ChildNode NodeID="Node2">
<GrandNode>I am grand child 1</GrandNode>
<GrandNode>I am grand child 2</GrandNode>
<GrandNode>I am grand child 3</GrandNode>
<GrandNode>I am new Grand</GrandNode>
</ChildNode>
</rootnode>
```

## 驗證 XML 文件

DTD 是一種用來定義 XML 文件結構的描述，簡單講就是文件型別的定義描述。它透過一系列的陳述式來定義 XML 文件的結構。（這部份可參考 MSDN : [DTD 陳述式](http://msdn.microsoft.com/zh-tw/library/ms256469.aspx)）。所以說，透過 DTD 的描述，就可以用來驗證一個 XML 文件的完整性。

[XmlReaderSettings](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreadersettings.aspx) 是用來設定驗證 XML 文件的類別，它有幾個屬性用來控制驗證方法：

- [ValidationType](http://msdn.microsoft.com/zh-tw/library/system.xml.validationtype.aspx)：指定要執行驗證的類型。
- None ：不執行驗證。
- DTD ：根據 DTD 資訊驗證。
- Schema ：根據 XML 結構描述定義語言 (XSD) 結構描述進行驗證
- Auto ：若文件包含 DTD 資訊則驗證。(已過時)
- XDR ：依據 XDR 資訊驗證。(已過時)
- [DtdProcessing](http://msdn.microsoft.com/zh-tw/library/system.xml.dtdprocessing.aspx)：指定的選項來處理 Dtd。
- Prohibit ：(=0) 若遇到 DTD 時擲回 XmlException 並顯示訊息，說明禁止使用 DTD。這是預設行為。
- Ignore ：(=1) 忽略DOCTYPE 項目，不發生 DTD 處理。
- Parse ：(=2) 用於剖析 DTD。

以指定的 [XmlReaderSettings](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreadersettings.aspx) 為參數，透過 [XmlReader.Create](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.create.aspx) 方法，即可驗證文件的完整性。
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE RootNode[
<!ELEMENT RootNode ANY> 
<!ELEMENT ChildNode ANY> 
<!ELEMENT GrandNode ANY>
<!ATTLIST ChildNode NodeID ID #REQUIRED>
<!ATTLIST GrandNode NodeID ID #REQUIRED>
]>
<RootNode>
<ChildNode NodeID="Node1">
<GrandNode NodeID="Node4">I am grand child</GrandNode>
<GrandNode NodeID="Node5">I am grand child</GrandNode>
<GrandNode NodeID="Node6">I am grand child</GrandNode>
</ChildNode>
<ChildNode NodeID="Node2">
<GrandNode NodeID="Node7">I am grand child</GrandNode>
<GrandNode NodeID="Node8">I am grand child</GrandNode>
</ChildNode>
<ChildNode NodeID="Node3">
<GrandNode NodeID="Node9">I am grand child</GrandNode>
</ChildNode>
</RootNode>
```
```c#
private bool ValidateDocument(string fileName)
{
XmlReaderSettings xSetting = new XmlReaderSettings(); 
xSetting.ValidationType = ValidationType.DTD; //根據 DTD 驗證。
xSetting.DtdProcessing = DtdProcessing.Parse; //若 XML 文件不符合 DTD 的描述，則丟出 Exception
XmlReader xReader = XmlReader.Create(fileName, xSetting); 

XmlDocument xDoc = new XmlDocument(); 
try
{
xDoc.Load(xReader);
return true;
}
catch (Exception ex)
{
lbl.Text += ex.Message + "<br />";
return false;
}
finally
{
xReader.Close();
}
}
```

# 巡覽 XML 文件

## 使用 DOM 巡覽 XML 文件

DOM 是一個樹狀的架構，可以使用遞迴方式 parsing 整個 XML 文件

- [XmlDocument.DocumentElement](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.documentelement.aspx) ：取得文件的根 System.Xml.XmlElement。
```c#
protected void btnParsingXmlDocument_Click(object sender, EventArgs e)
{
XmlDocument xmlDoc = new XmlDocument(); 
xmlDoc.Load(MapPath("XmlDocumentTest.xml"));
RecurseNodes(xmlDoc.DocumentElement);//取得文件的根
}
public void RecurseNodes(XmlNode node)
{
//start recursive loop with level 0
RecurseNodes(node, 0);
}

public void RecurseNodes(XmlNode node, int level)
{
string s; 
s = string.Format("{0} <b>Type:</b>{1} <b>Name:</b>{2} <b>Attr:</b> ", new string('-', level), node.NodeType, node.Name);

if (node.Attributes != null)
{
foreach (XmlAttribute attr in node.Attributes)
{
s += string.Format("{0}={1} ", attr.Name, attr.Value);
}
}
lbl.Text += s + "<br>";
foreach (XmlNode n in node.ChildNodes)
{
RecurseNodes(n, level + 1);
}
}
```

## 使用 XPathNavigator 解析 XML 文件

[XPathNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathnavigator.aspx) 是另一種遞迴解析 XML 文件的方式，簡單說就是使用 XPath 資料模型處理 XML 資料。
```c#
protected void btnUsingXPathNavigator_Click(object sender, EventArgs e)
{
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(MapPath("XmlDocumentTest.xml"));

XPathNavigator xpathNav = xmlDoc.CreateNavigator();
xpathNav.MoveToRoot();
RecurseNavNodes(xpathNav);
}
public void RecurseNavNodes(XPathNavigator node)
{
//start recursive loop with level 0
RecurseNavNodes(node, 0);
}

public void RecurseNavNodes(XPathNavigator node, int level)
{
string s = null;
s = string.Format("{0} <b>Type:</b>{1} <b>Name:</b>{2} <b>Attr:</b> ", new string('-', level), node.NodeType, node.Name);
if (node.HasAttributes)
{
node.MoveToFirstAttribute();
do
{
s += string.Format("{0}={1} ", node.Name, node.Value);
} while (node.MoveToNextAttribute()); node.MoveToParent();
}
lbl.Text += s + "<br>";
if (node.HasChildren)
{
node.MoveToFirstChild();
do
{
RecurseNavNodes(node, level + 1);
} while (node.MoveToNext()); node.MoveToParent();
}
}
```

# 搜尋 XML 文件

## 使用 DOM 的 GetElementById 方法，查詢 XML 文件中的節點

這個方法只能使用在具有型別定義的文件 (Document Type Definition , DTD) ，在 DTD 中明確定義這個 Id 是哪個屬性，而不是在節點中，命名一個叫做 "Id" 屬性就可以。
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE RootNode[
<!ELEMENT RootNode ANY> 
<!ELEMENT ChildNode ANY> 
<!ELEMENT GrandNode ANY>
<!ATTLIST ChildNode NodeID ID #REQUIRED><!--定義 NodeID 屬性為ChildNode節點的 ID -->
<!ATTLIST GrandNode NodeID ID #REQUIRED> <!--定義 NodeID 屬性為GrandNode節點的 ID -->
]>
<RootNode>
<ChildNode NodeID="Node1">
<GrandNode NodeID="Node4">I am grand child</GrandNode>
<GrandNode NodeID="Node5">I am grand child</GrandNode>
<GrandNode NodeID="Node6">I am grand child</GrandNode>
</ChildNode>
<ChildNode NodeID="Node2">
<GrandNode NodeID="Node7">I am grand child</GrandNode>
<GrandNode NodeID="Node8">I am grand child</GrandNode>
</ChildNode>
<ChildNode NodeID="Node3">
<GrandNode NodeID="Node9">I am grand child</GrandNode>
</ChildNode>
</RootNode>
```
```c#
protected void btnSearchingXmlDocument_Click(object sender, EventArgs e)
{
XmlDocument xmlDoc = new XmlDocument(); 
xmlDoc.Load(MapPath("XmlDocumentSample.xml"));
XmlNode node;
node = xmlDoc.GetElementById("Node5");

if (node != null)
{
StringBuilder sb = new StringBuilder();

sb.AppendLine(string.Format("Type:{0}",node.NodeType));
sb.AppendLine(string.Format("Name:{0}", node.Name));
foreach (XmlAttribute attr in node.Attributes)
{
sb.AppendLine(string.Format("{0}={1} ", attr.Name, attr.Value));
}
sb.AppendLine(string.Format("InnerText:{0}", node.InnerText));

lbl.Text = sb.ToString().Replace(System.Environment.NewLine, "<br>");
}
}
```

## 使用 XPathNavigator 的 SelectSingleNode 方法，查詢 XML 文件中的節點

使用 [SelectSingleNode](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.selectsinglenode.aspx) 這個方法不需要在 DTD 定義 ID 的資料類型。它需要傳送一個 XPath 查詢給 [SelectSingleNode](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.selectsinglenode.aspx) 以進行搜尋，並回傳符合 XPath 運算式的第一個 XmlNode。
```c#
protected void btnSelectSingleNode_Click(object sender, EventArgs e)
{
//Declare and create new XmlDocument
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(MapPath("XmlDocumentSample.xml"));
XmlNode node;
node = xmlDoc.SelectSingleNode("//ChildNode[@NodeID='Node3']");

if (node != null)
{
//列舉節點的Attributes
foreach (XmlAttribute attr in node.Attributes)
{
myDebug.WriteLine("{0}={1} ", attr.Name, attr.Value);
}

//取得節點內容
myDebug.WriteLine("InnerText={0}", node.InnerText);
}
}
```

## 使用 DOM 的 GetElementsByTagName 方法，查詢 XML 文件中的節點

使用 [GetElementsByTagName](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.getelementsbytagname.aspx) 這個方法會依據 TagName 來搜尋文件，回傳的是 [XmlNodeList](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnodelist.aspx) 型態的項目集合。
```c#
protected void btnGetElementByTagName_Click(object sender, EventArgs e)
{
//Declare and create new XmlDocument
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(MapPath("XmlDocumentSample.xml"));
XmlNodeList nodeList = xmlDoc.GetElementsByTagName("ChildNode");

StringBuilder sb = new StringBuilder();
foreach (XmlNode node in nodeList) 
{
sb.AppendLine(string.Format("<b>Type</b>:{0}", node.NodeType));
sb.AppendLine(string.Format("<b>Name</b>:{0}", node.Name));
sb.AppendLine(string.Format("<b>InnerText</b>:{0}", node.InnerText));
}
lbl.Text = sb.ToString().Replace(System.Environment.NewLine, "<br>");
}
```

## 使用 DOM 的 SelectNodes 方法，查詢 XML 文件中的節點

使用 [SelectNodes](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.selectnodes.aspx) 方法也可以找出 [XmlNodeList](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlnodelist.aspx) 清單。它是以 XPath 運算式當作查詢條件。
```c#
protected void btnSelectNode_Click(object sender, EventArgs e)
{
//Declare and create new XmlDocument
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(MapPath("XmlDocumentSample.xml"));
XmlNodeList nodeList = xmlDoc.SelectNodes("//GrandNode");

StringBuilder sb = new StringBuilder();
foreach (XmlNode node in nodeList)
{
sb.AppendLine(string.Format("<b>Type</b>:{0}", node.NodeType));
sb.AppendLine(string.Format("<b>Name</b>:{0}", node.Name));
sb.AppendLine(string.Format("<b>InnerText</b>:{0}", node.InnerText));
}
lbl.Text = sb.ToString().Replace(System.Environment.NewLine, "<br>");
}
```

## 使用 XPathNavigator 搜尋 XPathDocument 文件中的節點

- [XPathNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathnavigator.aspx) 物件可以由 [XmlDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.aspx) 建立，也可以由 [XPathDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathdocument.aspx) 建立。若是由 [XmlDocument.CreateNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xmldocument.createnavigator.aspx) 建立，則是可編輯的；若是由 [XPathDocument.CreateNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathdocument.createnavigator.aspx) 建立，則是唯讀的。
- 使用 [XPathDocument](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathdocument.aspx) 建立的 [XPathNavigator](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathnavigator.aspx) ，消耗的資源較少，且有較好搜尋彈性。
- 若將 XPath 查詢字串，編譯成 [XPathExpression](http://msdn.microsoft.com/zh-tw/library/system.xml.xpath.xpathexpression.aspx) 物件後，再叫用 AddSort 方法，可以指定搜尋結果的排序方法。
```c#
protected void btnXPathNavigator_Click(object sender, EventArgs e)
{
// 回傳的是 XPathDocument , 比 XmlDocument 使用較少的資源
XPathDocument xmlPathDoc = new XPathDocument(MapPath("XmlDocumentSample.xml")); 
XPathNavigator xpathNav = xmlPathDoc.CreateNavigator();
XPathExpression xPathQuery = xpathNav.Compile("//ChildNode");//編譯表示 XPath 運算式的字串

//Sort the selected node by NodeID.
xPathQuery.AddSort("@NodeID",
XmlSortOrder.Descending,
XmlCaseOrder.None, "",
XmlDataType.Text);

XPathNodeIterator iterator = xpathNav.Select(xPathQuery);
XPathNavigator navResult = iterator.Current;

StringBuilder sb = new StringBuilder();
while (iterator.MoveNext())
{
sb.AppendLine(string.Format("<b>Type</b>:{0}", navResult.NodeType));
sb.AppendLine(string.Format("<b>Name</b>:{0}", navResult.Name));

if (navResult.HasAttributes)
{
navResult.MoveToFirstAttribute();
do
{
sb.AppendLine(string.Format("{0}={1} ", navResult.Name, navResult.Value));
} while (navResult.MoveToNextAttribute());
}
}
lbl.Text = sb.ToString().Replace(System.Environment.NewLine, "<br>");
}
```

# 編輯 XML 資料

## 使用 XmlTextWriter 寫入檔案

[XmlTextWriter](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.aspx) 這個類別可以用來在檔案或資料流中快速的產生 XML 資料。底下為幾個常用的方法：

- [WriteComment](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.writecomment.aspx) ：寫入註解 &lt;!-]-&gt; 包含指定的文字。
- WriteStartElement ：寫入指定的開頭標記。
- [WriteEndElement](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.writestartelement.aspx) ：寫入對應的結尾標記。
- [WriteAttributeString](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.writeattributestring.aspx) ：寫入指定的屬性資訊。
- [WriteElementString](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextwriter.writeelementstring.aspx) ：寫入項目的文字資訊。
```c#
protected void btnXmlTextWriter_Click(object sender, EventArgs e)
{
XmlTextWriter xmlWriter = new XmlTextWriter(MapPath("EmployeeList.xml"), System.Text.Encoding.UTF8);
xmlWriter.Formatting = Formatting.Indented; //設定縮排
xmlWriter.Indentation = 4;//縮排大小

xmlWriter.WriteStartDocument(); 
xmlWriter.WriteComment("XmlTextWriter Test Date: " + DateTime.Now.ToShortDateString());

xmlWriter.WriteStartElement("EmployeeList");

//New Employee 1
xmlWriter.WriteStartElement("Employee");
xmlWriter.WriteAttributeString("EmpID", "1");
xmlWriter.WriteElementString("Name", "Joe");
xmlWriter.WriteElementString("Salary", XmlConvert.ToString(50000));

//Address
xmlWriter.WriteStartElement("Address");
xmlWriter.WriteElementString("Street", "123 MyStreet");
xmlWriter.WriteElementString("City", "MyCity");
xmlWriter.WriteElementString("State", "OH");
xmlWriter.WriteElementString("ZipCode", "12345");
xmlWriter.WriteEndElement();

//Employee
xmlWriter.WriteEndElement();

//New Employee 2
xmlWriter.WriteStartElement("Employee");
xmlWriter.WriteAttributeString("EmpID", "2");
xmlWriter.WriteElementString("Name", "Mary");
xmlWriter.WriteElementString("Salary", XmlConvert.ToString(40000));

//Address
xmlWriter.WriteStartElement("Address"); 
xmlWriter.WriteElementString("Street", "234 MyStreet"); 
xmlWriter.WriteElementString("City", "MyCity"); 
xmlWriter.WriteElementString("State", "OH"); 
xmlWriter.WriteElementString("ZipCode", "23456");
xmlWriter.WriteEndElement();

xmlWriter.WriteEndElement();//Employee 2

xmlWriter.WriteEndElement();//EmployeeList
xmlWriter.Close();
Response.Redirect("EmployeeList.xml");
}
```
```xml
<?xml version="1.0" encoding="utf-8"?>
<!--XmlTextWriter Test Date: 2012/9/13-->
<EmployeeList>
<Employee EmpID="1">
<Name>Joe</Name>
<Salary>50000</Salary>
<Address>
<Street>123 MyStreet</Street>
<City>MyCity</City>
<State>OH</State>
<ZipCode>12345</ZipCode>
</Address>
</Employee>
<Employee EmpID="2">
<Name>Mary</Name>
<Salary>40000</Salary>
<Address>
<Street>234 MyStreet</Street>
<City>MyCity</City>
<State>OH</State>
<ZipCode>23456</ZipCode>
</Address>
</Employee>
</EmployeeList>
```

## 使用 XmlTextReader 讀取檔案

[XmlTextReader](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.aspx) 這個類別可以用來讀取檔案或資料流中的 XML 資料。底下為幾個常用的方法：

- [Read](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.read.aspx) ：從資料流讀取下一個節點。
- [GetAttribute](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.getattribute.aspx) ：取得屬性的值。
- [HasAttributes](http://msdn.microsoft.com/zh-tw/library/system.xml.xmlreader.hasattributes.aspx) ：判斷目前節點是否具有屬性。
```c#
protected void btnXmlTextReader_Click(object sender, EventArgs e)
{
XmlTextReader xmlReader = new XmlTextReader(MapPath("EmployeeList.xml"));
while (xmlReader.Read())
{
switch( xmlReader.NodeType)
{
case XmlNodeType.XmlDeclaration:
case XmlNodeType.Element:
case XmlNodeType.Comment:
{
string s;
s = String.Format("{0}: {1} = {2}<br />", xmlReader.NodeType, xmlReader.Name, xmlReader.Value);
lbl.Text += s;
break; 
} 
case XmlNodeType.Text:
{
string s;
s = String.Format(" - Value: {0}<br />", xmlReader.Value);
lbl.Text += s;
break;
}
}
if(xmlReader.HasAttributes)
{ 
while (xmlReader.MoveToNextAttribute()) 
{
string s;
s = String.Format(" - Attribute: {0} = {1}<br />", xmlReader.Name, xmlReader.Value);
lbl.Text += s;
}
}
}
xmlReader.Close();
}
```

# 其它讀取 XML 資料的方法

## 將 XML 資料還原序列化
```c#
protected void btnStreamReader_Click(object sender, EventArgs e)
{
string xml = @"
<Product>
<Price>35</Price>
</Product>";

StringReader xmlStream = new StringReader(xml);

XmlSerializer xs = new XmlSerializer(typeof(Product));
Product produce = xs.Deserialize(xmlStream) as Product;
string price = produce.Price.ToString();
}
public class Product
{
public decimal Price { get; set; }
}
```

## 將 XML 資料讀進 DataTable

這個方法的 XML 資料，必須要有根節點。而且 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 物件必須是已經結構化的。
```c#
protected void btnDataTable_Click(object sender, EventArgs e)
{
string xml2 = @"
<Products>
<Product>
<Price>35</Price>
</Product>
</Products>";
StringReader xmlStream2 = new StringReader(xml2);
DataTable dt = CreateProductTable();
dt.ReadXml(xmlStream2);
string price = dt.Rows[0]["Price"].ToString();
}
private DataTable CreateProductTable()
{
DataTable dt = new DataTable();
dt.Columns.Add(new DataColumn("Price", typeof(decimal)));
dt.TableName = "Product";
return dt;
}
```

## 使用 XmlReader 讀取 XML 資料
```c#
protected void btnXmlReader_Click(object sender, EventArgs e)
{
string xml = @"
<Product>
<Price>35</Price>
</Product>";

XmlReader xmlReader = XmlReader.Create(new StringReader(xml));
while (xmlReader.Read())
{
switch (xmlReader.NodeType)
{
case XmlNodeType.XmlDeclaration:
case XmlNodeType.Element:
case XmlNodeType.Comment:
case XmlNodeType.Text:
{
myDebug.ResponseBR("[{0}] {1} = {2}", xmlReader.NodeType, xmlReader.Name, xmlReader.Value);
break;
}
}
}
}
```

# 命名空間前置詞

## 什麼是命名空間前置詞

XML命名空間主要功用是在解決XML元素或屬性名稱的衝突問題。

當元素名稱或屬性名稱中包含冒號(:)，那麼在冒號之前的字串就是命名空間的**前置字串**，而冒號後的字串為**區域名稱**。

### 定義命名空間

- Namespace的參引僅能用於宣告該Namespace的元素及其子(孫)元素。
- 一個元素中可以同時宣告多個命名空間前置詞
- 使用「預設名稱空間」，可以不必以前置字串來指定名稱空間
- 在宣告Namespace時，前置字串不可以xml為字首來命名

下面這個例子定義了三個名稱空間前置詞，其中第一個是預設名稱空間。
```xml
<gpx 
xmlns="http://www.topografix.com/GPX/1/1" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://www.topografix.com/GPX/1/1 http://www.topografix.com/GPX/1/1/gpx.xsd">
</gpx>
```

### 使用命名空間

下面例子中，沒有指定前置詞的元素表示使用「預設名稱空間」。另外在第4行又增加定義了一個 gpxx 名稱空間前置詞，且這個前置詞的引用，只能套用在本身或子孫元素。
```xml
<trk>
<name>Track LOG A</name>
<extensions>
<gpxx:TrackExtension xmlns:gpxx="http://www.garmin.com/xmlschemas/GpxExtensions/v3">
<gpxx:DisplayColor>DarkGreen</gpxx:DisplayColor>
</gpxx:TrackExtension>
</extensions>
</trk>
```

#### 
```xml
```

#### 

#### 
```xml
```
上面例子中，定

## 讀取含命名空間前置詞的XML文件

透過 SelectNodes 或 SelectSingleNode 方法可以快速的在 XML 文件中尋找節點，但是若一個 XML 文件中含有命名空間前置詞，則 XPath 運算式中也必須包含命名空間前置詞。你可以利用 XmlNamespaceManager 物件來設定必須的命名空間前置詞。
```xml
<gpx 	xmlns="http://www.topografix.com/GPX/1/1" 	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 	xsi:schemaLocation="http://www.topografix.com/GPX/1/1 http://www.topografix.com/GPX/1/1/gpx.xsd"><trk>
<name>Track LOG A</name>
<extensions>
<gpxx:TrackExtension xmlns:gpxx="http://www.garmin.com/xmlschemas/GpxExtensions/v3">
<gpxx:DisplayColor>DarkGreen</gpxx:DisplayColor>
</gpxx:TrackExtension>
</extensions>
</trk>
<rte><name>Route LOG A</name><extensions><gpxx:RouteExtension xmlns:gpxx="http://www.garmin.com/xmlschemas/GpxExtensions/v3"><gpxx:IsAutoNamed>false</gpxx:IsAutoNamed><gpxx:DisplayColor>Yellow</gpxx:DisplayColor></gpxx:RouteExtension></extensions></rte></gpx>```

上面例子中，定義了預設命名空間，如果想透過 SelectSingleNode 尋找 trk 元表，若設定 XPath = "//trk" 將會查無資料，你可以使用任意一個名稱來表示預設的命名空間前置詞，並且將該字串用於 XPath 表示式之中。
```c#
XmlDocument xmlDoc = new XmlDocument();XmlNamespaceManager namespaces = new XmlNamespaceManager(xmlDoc.NameTable);//載入文件xmlDoc.Load(MapPath("map.xml"));//加入命名空間前置詞namespaces.AddNamespace("ns", "http://www.topografix.com/GPX/1/1");//預設的空白前置詞,可以任意自訂一個名稱namespaces.AddNamespace("gpxx", "http://www.garmin.com/xmlschemas/GpxExtensions/v3");XmlNode trk = xmlDoc.SelectSingleNode("//ns:trk", namespaces);if (trk != null){lbl.Text = trk["name"].InnerText;}//若XML文件項目中沒有特別指定前置詞,就是使用預設的前置詞XmlNode color = trk.SelectSingleNode("//ns:extensions//gpxx:RouteExtension//gpxx:DisplayColor", namespaces);if (color != null){lbl.Text = color.InnerText;}```
## 參考資料

- [使用 XPath 巡覽選取節點](https://msdn.microsoft.com/zh-tw/library/d271ytdx%28v=vs.100%29.aspx)
- 
-