---
title: 資料來源控制項(2)
layout: default
parent: 資料來源控制項
nav_order: 3
description: "資料來源控制項(2)"
date: 2013-04-09
tags: [Web,ASPNET,資料來源控制項]
postid: "4864477729129333"
---
# XmlDataSource

The [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) control provides a means to create a binding connection between controls on your page and an XML file.   The XML data source control is best used when you wish to bind to XML data that is represented as hierarchical (such as using in the [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) control).  

- [DataFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.datafile.aspx) ：定義要繫結至 [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 控制項的 XML 檔案名稱。
- [Data](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.data.aspx) ：這個屬性是 [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 控制項內部的一個區塊，是用來直接定義 XML 資料用的
- [TransformFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.transformfile.aspx) ：定義可延伸樣式表語言 (XSL) 檔案 (.xsl) 的名稱。

如果同時設定 [DataFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.datafile.aspx) 和 [Data](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.data.aspx) 屬性，則會以 [DataFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.datafile.aspx) 屬性優先，並且會使用 XML 檔案中的資料，而不是在 [Data](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.data.aspx) 屬性所指定的 XML 資料。  

如果您變更 [Data](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.data.aspx) 屬性值，則會引發 DataSourceChanged 事件。如果已啟用快取，並變更 [Data](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.data.aspx) 的值，則快取就會失效。  

## 使用 XmlDataSource 將 Xml 資料載入 GridView

### 載入 XML 資料

[GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 預設支援的 XML 格式如下：  
```xml
<?xml version="1.0" standalone="yes"?>
<Products>
<Product Category="Beverages" Name="Chai" QuantityPerUnit="10 boxes x 20 bags" UnitPrice="18.0000" />
<Product Category="Condiments" Name="Aniseed Syrup" QuantityPerUnit="10 boxes x 20 bags" UnitPrice="10.0000" />
</Products>
```

如果 XML 資料如上面格式，只要使用 [DataFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.datafile.aspx) 屬性指定檔案路徑即可。
```xml
<asp:XmlDataSource ID="XmlDataSource1" runat="server" DataFile="Products1.xml" >
</asp:XmlDataSource>
<asp:GridView ID="GridView4" runat="server" DataSourceID="XmlDataSource1">
</asp:GridView>
```

### 轉換 XML 資料

如果 XML 資料如下面格式，則必須藉由 XSLT 檔來轉換格式。
```xml
<?xml version="1.0" standalone="yes"?>
<Products>
<Product>
<Category>Beverages</Category>
<Name>Chai</Name>
<QuantityPerUnit>10 boxes x 20 bags</QuantityPerUnit>
<UnitPrice>18.0000</UnitPrice>
</Product>
<Product>
<Category>Condiments</Category>
<Name>Aniseed Syrup</Name>
<QuantityPerUnit>12 - 550 ml bottles</QuantityPerUnit>
<UnitPrice>10.0000</UnitPrice>
</Product>
</Products>
```

XSLT (Extensible Stylesheet Language Transformations) is an XML-based language used for the transformation of XML documents into other XML or “human-readable” documents.
```xml
<?xml version="1.0"?>
<xsl:stylesheet
version="1.0"
xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns:msxsl="urn:schemas-microsoft-com:xslt">
<xsl:strip-space elements="\*"/>
<xsl:output method="xml"
omit-xml-declaration="yes"
indent="yes"
standalone="yes" />
<xsl:template match="/">
<xsl:for-each select="Products">
<xsl:element name="Products">
<xsl:for-each select="Product">
<xsl:element name="Product">
<xsl:attribute name="Category">
<xsl:value-of select="Category"/>
</xsl:attribute>
<xsl:attribute name="Name">
<xsl:value-of select="Name"/>
</xsl:attribute>
<xsl:attribute name="QuantityPerUnit">
<xsl:value-of select="QuantityPerUnit"/>
</xsl:attribute>
<xsl:attribute name="UnitPrice">
<xsl:value-of select="UnitPrice"/>
</xsl:attribute>
</xsl:element>
</xsl:for-each>
</xsl:element>
</xsl:for-each>
</xsl:template>
</xsl:stylesheet>
```

要使用這樣格式的 XML 資料，除了使用 [DataFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.datafile.aspx) 屬性指定 XML 檔案，  還必須將 XSLT 檔案指定給 [TransformFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.transformfile.aspx) 屬性才可以。
```xml
<asp:XmlDataSource ID="XmlDataSource2" runat="server"
DataFile="Products2.xml" 
TransformFile="Products2.xsl" >
</asp:XmlDataSource>
<asp:GridView ID="GridView5" runat="server" DataSourceID="XmlDataSource2">
</asp:GridView>
```

載入畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiiKi0A74Gx7_-L0hsczqTsYC1D8RYn6-PdUth3mAZt-sbZUuaKAqNuc3HJRXB8a2FfwIRwjiXZuMgGAacJT-cGwOgj5Rcx_Q48zA2_Zwg6NoeF3WXrjtxMqQ7oYkVC4ITTqAsE4nF-R1U/s518/xml-datasource-gridview.png)

## 使用 XmlDataSource 將 Xml 資料載入 TreeView
```xml
<?xml version="1.0" standalone="yes"?>
<Products>
<Product>
<Category>Beverages</Category>
<Name>Chai</Name>
<QuantityPerUnit>10 boxes x 20 bags</QuantityPerUnit>
<UnitPrice>18.0000</UnitPrice>
</Product>
<Product>
<Category>Condiments</Category>
<Name>Aniseed Syrup</Name>
<QuantityPerUnit>12 - 550 ml bottles</QuantityPerUnit>
<UnitPrice>10.0000</UnitPrice>
</Product>
</Products>
```
```xml
<xsl:stylesheet version="1.0"
xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:template match="Products">
<Products>
<xsl:for-each select="Product">
<xsl:sort select="Name" order="ascending" />
<Product>
<Name>
<xsl:value-of select="Name"/>
</Name>
<Category>
<xsl:text>Category: </xsl:text>
<xsl:value-of select="Category"/>
</Category>
<QuantityPerUnit>
<xsl:text>Quantity: </xsl:text>
<xsl:value-of select="QuantityPerUnit"/>
</QuantityPerUnit>
<UnitPrice>
<xsl:text>Price: </xsl:text>
<xsl:value-of select="UnitPrice"/>
</UnitPrice>
</Product>
</xsl:for-each>
</Products>
</xsl:template>
</xsl:stylesheet>
```
```xml
<asp:XmlDataSource ID="XmlDataSource3" runat="server"
DataFile="Products3.xml" 
TransformFile="Products3.xsl" >
</asp:XmlDataSource>
<asp:TreeView id="TreeView1" runat="server" DataSourceID="XmlDataSource3">
<DataBindings>
<asp:TreeNodeBinding DataMember="Name" TextField="#InnerText" />
<asp:TreeNodeBinding DataMember="Category" TextField="#InnerText" />
<asp:TreeNodeBinding DataMember="QuantityPerUnit" TextField="#InnerText" />
<asp:TreeNodeBinding DataMember="UnitPrice" TextField="#InnerText" />
</DataBindings>
</asp:TreeView>
```

載入畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKWU6ROScpTCjjZlHPgiwZpV87seyx-v0kzzTfkSz-342oKehxvC-HUDkCi5Asy8a8GvuSXEQVBX5n69WaKF7VJi_V2_7K3hB4W16ADJyny4ess95YIat8rgfAr_gZeLKUUgaSHcGvHuE/s368/xml-datasource-treeview.png)

## Filtering XML with the XmlDataSource Control

### 例一

[XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 的提供 [XPath](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.xpath.aspx) 屬性，可以用來設定資料來源的篩選條件。  

針對上面範例，我們可以使用以下的 [XPath](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.xpath.aspx) 表示法來篩選資料。
```xml
<asp:XmlDataSource ID="XmlDataSource2" runat="server"
DataFile="Products2.xml" 
TransformFile="Products2.xsl" 
XPath="/Products/Product[@Category='Beverages']" >
</asp:XmlDataSource>
```

### 例二

XPath 的使用方法，會因為資料格式的不同而有所不同，底下範例示範如何篩選一個 XML 資料，並繫結到 TreeView 控制項。
```xml
<?xml version="1.0" standalone="yes"?>
<Products>
<Product>
<Category>Beverages</Category>
<Name>Chai</Name>
<QuantityPerUnit>10 boxes x 20 bags</QuantityPerUnit>
<UnitPrice>18.0000</UnitPrice>
</Product>
<Product>
<Category>Condiments</Category>
<Name>Aniseed Syrup</Name>
<QuantityPerUnit>12 - 550 ml bottles</QuantityPerUnit>
<UnitPrice>10.0000</UnitPrice>
</Product>
<Product>
<Category>Condiments</Category>
<Name>Chef Anton's Cajun Seasoning</Name>
<QuantityPerUnit>48 - 6 oz jars</QuantityPerUnit>
<UnitPrice>22.0000</UnitPrice>
</Product>
<Product>
<Category>Produce</Category>
<Name>Uncle Bob's Organic Dried Pears</Name>
<QuantityPerUnit>12 - 1 lb pkgs.</QuantityPerUnit>
<UnitPrice>30.0000</UnitPrice>
</Product>
<Product>
<Category>Beverages</Category>
<Name>Guaraná Fantástica</Name>
<QuantityPerUnit>12 - 355 ml cans</QuantityPerUnit>
<UnitPrice>4.5000</UnitPrice>
</Product>
<Product>
<Category>Beverages</Category>
<Name>Sasquatch Ale</Name>
<QuantityPerUnit>24 - 12 oz bottles</QuantityPerUnit>
<UnitPrice>14.0000</UnitPrice>
</Product>
<Product>
<Category>Beverages</Category>
<Name>Steeleye Stout</Name>
<QuantityPerUnit>24 - 12 oz bottles</QuantityPerUnit>
<UnitPrice>18.0000</UnitPrice>
</Product>
</Products>
```
```xml
<xsl:stylesheet version="1.0"
xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:template match="Products">
<Products>
<xsl:for-each select="Product">
<xsl:sort select="Name" order="ascending" />
<Product>
<Name>
<xsl:value-of select="Name"/>
</Name>
<Category>
<xsl:text>Category: </xsl:text>
<xsl:value-of select="Category"/>
</Category>
<QuantityPerUnit>
<xsl:text>Quantity: </xsl:text>
<xsl:value-of select="QuantityPerUnit"/>
</QuantityPerUnit>
<UnitPrice>
<xsl:text>Price: </xsl:text>
<xsl:value-of select="UnitPrice"/>
</UnitPrice>
</Product>
</xsl:for-each>
</Products>
</xsl:template>
</xsl:stylesheet>
```
```xml
<asp:XmlDataSource ID="XmlDataSource3" runat="server"
DataFile="Products3.xml" 
TransformFile="Products3.xsl"
XPath="/Products/Product[Category='Category: Beverages']"  >
</asp:XmlDataSource>
```

#### NOTE inserting, deleting, modifying, and saving XML data

The XmlDataSource control is typically used when reading XML data.   Unlike many of the other data source controls, it does not provide automatic attributes for inserting, deleting, updating, and saving XML data.   Instead, you have to write your own custom code if you require this behavior.   Please see Chapter 7 for more information on working with XML data in this manner.

# SiteMapDataSource

## Connecting to Site Navigation Data with SiteMapDataSource

這個 [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) 控制項可用來提供(階層式)網站導覽用的資料，例如：TreeView、Menu、[DropDownList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.dropdownlist.aspx) 控制項。  另外， [SiteMapPath](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemappath.aspx) 這個控制項是專門的網站巡覽控制項，它有它特定的使用方法，所以不須要使用到 [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) 控制項。  

The data for this control is defined in a special XML file called a **web.sitemap**.   You can define one sitemap file in the **root** of your Web application.   The file includes information about the pages in your site and their hierarchy.   It also includes **page names**, **navigational information**, and a **description** of the page.  

#### Adding a web.sitemap file

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEja-Kg4DWlRI2sBijgKSeoubk25w9-mbjJeX8uZ3XoxqgAoA8C5-zgOozoPT7vBHNWougOXifsUJibfn-15aerPWrECebIgFvRsUb9UJsvreX6PZhPwGx5orIx6KnL850Ip5cRy6vM4HQM/s545/web-site-map.png)

#### web.sitemap sample
```xml
<?xml version="1.0" encoding="utf-8" ?>
<siteMap xmlns="http://schemas.microsoft.com/AspNet/SiteMap-File-1.0" >
<siteMapNode url="Home.aspx" title="Home" description="">
<siteMapNode url="products.aspx" title="Products" description="">
<siteMapNode url="productDetails.aspx" title="Product Details" description="" />
</siteMapNode>
<siteMapNode url="services.aspx" title="Services" description="" />
<siteMapNode url="locations.aspx" title="Locations" description="" />
<siteMapNode url="about.aspx" title="About Us" description="" />
</siteMapNode>
</siteMap>
```

The following code also demonstrates binding to a [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) control:
```xml
<asp:SiteMapDataSource ID="SiteMapDataSource1" runat="server" />
<asp:Menu ID="Menu1" runat="server" DataSourceID="SiteMapDataSource1">
</asp:Menu>
```

載入畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJaKQa7wF7X80qp-HqATpDMg-o-Kkpus0kAUfs-tJ0ETQFv2b1PsGF0QyFPhqIU23I8pD_4BZGdnmBjKFeFCmuzq7zsWv-LNIwXTp0nYpQnFhMqsmyonk5GuY9oxCTtrquO5aqAv_2LN4/s235/web-site-map-result.png)

## Filtering the Data Shown in the SiteMapDataSource

Sometimes, you might wish to show only a portion of the data in your sitemap data file.   The [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control provides a couple attributes you can use to control the data that is shown.  

- [StartingNodeUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.startingnodeurl.aspx) ：指定開始節點，根據預設，開始節點是階層架構的根節點。
- [ShowStartingNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.showstartingnode.aspx) ：是否顯示 [StartingNodeUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.startingnodeurl.aspx) 屬性所指的節點。
- [StartFromCurrentNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.startfromcurrentnode.aspx) ：是否使用目前頁面的節點當做開始節點。
- [StartingNodeOffset](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.startingnodeoffset.aspx) ：this attribute is used to move the starting node up or down the sitemap data tree
```xml
<asp:SiteMapDataSource ID="SiteMapDataSource1" runat="server"
ShowStartingNode="false" 
StartingNodeUrl="WebForm2.aspx"
/>
<asp:Menu ID="Menu1" runat="server" DataSourceID="SiteMapDataSource1">
</asp:Menu>
```