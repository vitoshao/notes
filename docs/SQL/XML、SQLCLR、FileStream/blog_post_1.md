---
title: 在資料庫中使用 XML 欄位型別
layout: default
parent: XML、SQLCLR、FileStream
nav_order: 1
description: "在資料庫中使用 XML 欄位型別"
date: 2013-06-05
tags: [SQL,XML、SQLCLR、FileStream]
postid: "139131609847316037"
---
早期的資料庫管理系統並不支援XML格式的欄位，因此資料庫管理系統本身無法對XML資料做最佳化的儲存與管理，更無法利用XML的查詢功能，例如：XPath語法直接查閱。  現今新版本的資料庫管理系統幾乎都支援XML的資料格式，並且增加特殊指令來處理XML資料，同時亦可直接將關聯式資料轉換成XML階層式的資料做輸出，軟體系統將不必再做資料轉換的動作，即可將關聯式資料以XML格式做處理。  

SQL Server 支援 XML doucments 和 XML fragments (非完整的 XML document, 僅含部份 XML 文件內容)，如下範例：  
```xml
<Order Id="43860" OrderDate="2001-08-01T00:00:00" ShipDate="2001-08-08T00:00:00" />
<Order Id="44501" OrderDate="2001-11-01T00:00:00" ShipDate="2001-11-08T00:00:00" />
<Order Id="45283" OrderDate="2002-02-01T00:00:00" ShipDate="2002-02-08T00:00:00" />
```
```xml
<Customers>
<Customer CustomerID="BERGS" Name="Berglunds snabbköp">
<Orders>
<Order OrderID="10278" OrderDate="1996-08-12T00:00:00" ShipAddress="Berguvsvägen  8" />
<Order OrderID="10280" OrderDate="1996-08-14T00:00:00" ShipAddress="Berguvsvägen  8" />
</Orders>
</Customer>
<Customer CustomerID="WHITC" Name="White Clover Markets">
<Orders>
<Order OrderID="10269" OrderDate="1996-07-31T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10344" OrderDate="1996-11-01T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10469" OrderDate="1997-03-10T00:00:00" ShipAddress="1029 - 12th Ave. S." />
</Orders>
</Customer>
</Customers>
```

# 如何將查詢結果轉成 XML

XML與tabular資料格式最大的差異點就是XML是階層式的資料。例如上面範例，該 XML 文件裡就同時包含了客戶資枓和其訂購記錄。  在不使用 XML 的情況下，你可能有以下二種做法以取得類似的結果。  
```sql
SELECT CustomerID, CompanyName 
From Customers
WHERE CustomerID in ('BERGS','WHITC')

SELECT OrderID, OrderDate, ShipAddress 
From Orders
WHERE CustomerID in ('BERGS','WHITC')
```
```sql
SELECT C.CustomerID, C.CompanyName,
O.OrderID, O.OrderDate, O.ShipAddress 
From Customers C
INNER JOIN Orders O ON C.CustomerID=O.CustomerID
WHERE C.CustomerID in ('BERGS','WHITC')
```

以上二種做法都是tabular資料格式，還得靠用戶端程式做許多處理才能得到相要的階層式資料。這時如果使用 XML 解決方案就簡單多了。  

只要使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) ... [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) 陳述式就可以將查詢結果轉成 XML 資料輸出。   [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) 子句支援四種模式：  

- [RAW](http://msdn.microsoft.com/zh-tw/library/ms175140.aspx)：一個 &lt;row&gt; 元素表示一筆資料
- [AUTO](http://msdn.microsoft.com/zh-tw/library/ms188273.aspx)：根據 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式中的資訊，以巢狀的 XML 結構來表示一筆查詢結果。
- [EXPLICIT](http://msdn.microsoft.com/zh-tw/library/ms189068.aspx)：以自訂的屬性和元素來表示資料
- [PATH](http://msdn.microsoft.com/zh-tw/library/ms189885.aspx)：以自訂的屬性和元素來表示資料

## FOR XML RAW

RAW 模式會將查詢結果集的每一個資料列轉換成具有泛用識別碼 &lt;row&gt; 的 XML 元素，或以指定的識別碼當做元素名稱。  

#### 預設樣式
```sql
SELECT CustomerID, CompanyName 
FROM Customers
WHERE CustomerID in ('BERGS','WHITC')
FOR XML RAW;
```
```xml
<row CustomerID="BERGS" CompanyName="Berglunds snabbköp" />
<row CustomerID="WHITC" CompanyName="White Clover Markets" />
```

#### 自訂樣式

- 可以使用 as 別名，來指定 XML 屬性名稱。
- 可以使用 ROOT 宣示詞加入一個根節點。
- 可以在 RAW 或 ROOT 加入參數，來自訂元素名稱
```sql
SELECT CustomerID as Id, CompanyName as Name
From Customers
WHERE CustomerID in ('BERGS','WHITC')
FOR XML RAW('Customer'), ROOT('Customers');
```
```xml
<Customers>
<Customer Id="BERGS" Name="Berglunds snabbköp" />
<Customer Id="WHITC" Name="White Clover Markets" />
</Customers>
```

#### 使用XML元素(Element)樣式

前面的例子，查詢後的資料，都會被當做 XML 的屬性(Attribute)來表示，如果希望以 XML 元素(Element)來表示，只要加上 **ELEMENTS** 關鍵字即可。
```sql
SELECT CustomerID, CompanyName 
FROM Customers
WHERE CustomerID in ('BERGS','WHITC')
FOR XML RAW('Customer'), Root('Customers'), Elements
```
```xml
<Customers>
<Customer>
<CustomerID>BERGS</CustomerID>
<CompanyName>Berglunds snabbkop</CompanyName>
</Customer>
<Customer>
<CustomerID>WHITC</CustomerID>
<CompanyName>White Clover Markets</CompanyName>
</Customer>
</Customers>
```

#### 處理 NULL 欄位值

在將查詢結果轉換成 XML 型別時，系統預設會將NULL欄位值忽略，也就是不會出現在屬性之中。如底下例子：
```sql
SELECT ProductID, ProductName, Color
FROM Products
WHERE ProductID < 3
FOR XML RAW, ELEMENTS;
```
```xml
<row>
<ProductID>1</ProductID>
<ProductName>Chai Chai</ProductName>
<Color>Red</Color>
</row>
<row>
<ProductID>2</ProductID>
<ProductName>Chang</ProductName>
</row>
```

如果因為需求，你也可以使用 **XSINIL** 關鍵字，告訴 SQL Server 要保留 NULL 欄位，這時候在轉出的資料中，該欄位就會多出一個 nil = "true" 的屬性設定。  
```sql
SELECT ProductID, ProductName, Color
FROM Products
WHERE ProductID < 3
FOR XML RAW, ELEMENTS XSINIL;
```
```xml
<row xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<ProductID>1</ProductID>
<ProductName>Chai Chai</ProductName>
<Color>Red</Color>
</row>
<row xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<ProductID>2</ProductID>
<ProductName>Chang</ProductName>
<Color xsi:nil="true" />
</row>
```

在上面的結果中，在根節點中都有參考到 http://www.w3.org/2001/XMLSchema-instance ，這個是 NIL 屬性的命名空間。

## FOR XML AUTO

AUTO 模式與 RAW 模式若只針對單一TABLE進行查詢，其查詢結果幾乎會完全相同。  二者最大的不同在於JOIN多個TABLE的時候，RAW 模式只能以單層路徑(one path)來表示查詢結果，而 AUTO 模式則會使用多層路徑(multi path)來表示查詢結果。  多層路徑是指在查詢陳述式中用到的 table 名稱都會獨立使用一個 element 項目來描述。  

AUTO Mode 的欄位資料預設還是以屬性方式表示，同樣的，若想要以元素方式表示，只要加上 Element 選項即可。  
```sql
SELECT Customers.CustomerID ID, Customers.CompanyName as Name,
Orders.OrderID, Orders.OrderDate,
OrderItem.ProductID, OrderItem.Quantity, OrderItem.UnitPrice
From Customers
LEFT JOIN Orders as Orders ON Customers.CustomerID=Orders.CustomerID
LEFT JOIN OrderDetails as OrderItem ON OrderItem.OrderID=Orders.OrderID
WHERE Customers.CustomerID in ('GROSR','LAZYK')
FOR XML AUTO, ROOT('Customers');
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjO-bRJl-TcjzjxI2VxmsanrOT2k0S0tN6ZhsxBHQuLMRltlcxyCWzUhOLKUzvYi_ojEcyYMBZqdK0Va1KabBJeiCIz2uXQqIvp8THhShGGTukQ9yxbgJxfvtM0Funp66cISorGVjbVI0E/s0/sql-for-xml-auto-1.png)
```xml
<Customers>
<Customers ID="GROSR" Name="GROSELLA-Restaurante">
<Orders OrderID="10268" OrderDate="1996-07-30T00:00:00">
<OrderItem ProductID="29" Quantity="10" UnitPrice="99.0000" />
<OrderItem ProductID="72" Quantity="4" UnitPrice="27.8000" />
</Orders>
<Orders OrderID="10785" OrderDate="1997-12-18T00:00:00">
<OrderItem ProductID="10" Quantity="10" UnitPrice="31.0000" />
<OrderItem ProductID="75" Quantity="10" UnitPrice="7.7500" />
</Orders>
</Customers>
<Customers ID="LAZYK" Name="Lazy K Kountry Store">
<Orders OrderID="10482" OrderDate="1997-03-21T00:00:00">
<OrderItem ProductID="40" Quantity="10" UnitPrice="14.7000" />
</Orders>
<Orders OrderID="10545" OrderDate="1997-05-22T00:00:00">
<OrderItem ProductID="11" Quantity="10" UnitPrice="21.0000" />
</Orders>
</Customers>
</Customers>
```

使用 AUTO 模式要特別注意 ORDER BY 子句的使用，因為這個模式的轉換會以 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 後面出現的 table 順序當作階層順序，如果 ORDER BY 用錯了，很可能結果就不是預期的樣子。

## FOR XML EXPLICIT

當 PATH 模式都不能滿足需求時，才得使用 EXPLICIT 模式，這模式可以建立各種形式的 XML 結構，但用起來卻棘手。  

你必須使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 語法加上特定的欄位命名以產生一個特定格式的結果集，EXPLICIT 模式會將查詢所產生的結果集轉換為 XML 文件。  而這個特定的結果集必須包含 **Tag** 和 **Parent** 二個欄位，用來提供階層資訊。  

- Tag：整數值，表示元素的標記編號
- Parent：父元素的標記編號，如果沒有父層元素則用 Null 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjbzkflkiiizR-azMsppxi8YHpBohW-y45TN-4xg22jzZg1Ogstr4B-KUdp0BCzFryeeAlxveeo3FqKt64TMHl0XckzlNKSnfe3p1VpAhthdkCLR313Wph6VvwdNu-kM79QcRHoaQ7f9aE/s0/sql-xml-explicit-1.png)

下面語法，若不考慮 [FOR XML EXPLICIT](http://msdn.microsoft.com/zh-tw/library/ms189068.aspx) 子句，所得到的結果集就是上圖中的結果，  也就是如果要使用 EXPLICIT 模式，只要想辨法使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 語法取得這樣子的一個結果即可。  
```sql
SELECT
1 AS Tag
,NULL AS Parent
,NULL AS "Customers!1!!element"
,NULL AS "Customer!2!CustomerId"
,NULL AS "Customer!2!CompanyName"
,NULL AS "Order!3!OrderId"
,NULL AS "Order!3!OrderDate"
UNION ALL
SELECT
2 AS Tag
,1 AS Parent
,NULL AS "Customers!1!!element"
,c.CustomerID  AS "Customer!2!CustomerId"
,c.CompanyName AS "Customer!2!CompanyName"
,NULL		   AS "Order!3!OrderId"
,NULL		   AS "Order!3!OrderDate"
FROM Customers AS c
WHERE c.CustomerID in ('GROSR','LAZYK')
UNION ALL
SELECT
3 AS Tag
,2 AS Parent
,NULL AS "Customers!1!!element"
,O.CustomerID AS "Customer!2!CustomerId"
,NULL         AS "Customer!2!CompanyName"
,O.OrderID    AS "Order!3!OrderID"
,O.OrderDate  AS "Order!3!OrderDate"
FROM Orders AS O 
WHERE O.CustomerID IN ('GROSR','LAZYK')
ORDER BY "Customer!2!CustomerId", Tag
FOR XML EXPLICIT;
```

得到的 XML 結果如下：  
```xml
<Customers>
<Customer CustomerId="GROSR" CompanyName="GROSELLA-Restaurante">
<Order OrderId="10268" OrderDate="1996-07-30T00:00:00" />
<Order OrderId="10785" OrderDate="1997-12-18T00:00:00" />
</Customer>
<Customer CustomerId="LAZYK" CompanyName="Lazy K Kountry Store">
<Order OrderId="10482" OrderDate="1997-03-21T00:00:00" />
<Order OrderId="10545" OrderDate="1997-05-22T00:00:00" />
</Customer>
</Customers>
```

若想將資料以元素形式表示，只要在資料行名稱中增加 ELEMENT 指示詞即可，例如：
```sql
SELECT
1 AS Tag
,NULL AS Parent
,NULL AS "Customers!1!!element"
,NULL AS "Customer!2!CustomerId"
,NULL AS "Customer!2!CompanyName"
,NULL AS "Order!3!OrderId!element"
,NULL AS "Order!3!OrderDate!element"
UNION ALL
SELECT
2 AS Tag
,1 AS Parent
,NULL AS "Customers!1!!element"
,c.CustomerID  AS "Customer!2!CustomerId"
,c.CompanyName AS "Customer!2!CompanyName"
,NULL		   AS "Order!3!OrderId"
,NULL		   AS "Order!3!OrderDate"
FROM Customers AS c
WHERE c.CustomerID in ('GROSR','LAZYK')
UNION ALL
SELECT
3 AS Tag
,2 AS Parent
,NULL AS "Customers!1!!element"
,O.CustomerID AS "Customer!2!CustomerId"
,NULL         AS "Customer!2!CompanyName"
,O.OrderID    AS "Order!3!OrderID"
,O.OrderDate  AS "Order!3!OrderDate"
FROM Orders AS O 
WHERE O.CustomerID IN ('GROSR','LAZYK')
ORDER BY "Customer!2!CustomerId", Tag
FOR XML EXPLICIT;
```
```xml
<Customers>
<Customer CustomerId="GROSR" CompanyName="GROSELLA-Restaurante">
<Order>
<OrderId>10268</OrderId>
<OrderDate>1996-07-30T00:00:00</OrderDate>
</Order>
<Order>
<OrderId>10785</OrderId>
<OrderDate>1997-12-18T00:00:00</OrderDate>
</Order>
</Customer>
<Customer CustomerId="LAZYK" CompanyName="Lazy K Kountry Store">
<Order>
<OrderId>10482</OrderId>
<OrderDate>1997-03-21T00:00:00</OrderDate>
</Order>
<Order>
<OrderId>10545</OrderId>
<OrderDate>1997-05-22T00:00:00</OrderDate>
</Order>
</Customer>
</Customers>
```

## FOR XML PATH

FOR XML PATH mode is the best choice of the different [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) modes for most solutions.  PATH mode allows for the easy creation of different XML structures by simply interpreting column names specified using an XPath-like expression when generating the XML result.  

FOR XML PATH 模式有以下幾個用法：  

- **將欄位名稱轉XML元素**：這是預設項
- **將欄位名稱轉XML屬性**：在欄位別名中加上 @ 符號。
- **將欄位值當做特定元素中的屬性值**：使用 /項目名稱/@colName 當做欄位別名
- **將欄位值當做特定元素中的文字**：使用 /項目名稱/text() 當做欄位別名
- **註解文字**：使用 comment() 當做欄位別名
- **插入自訂項目**：使用 node() 當做欄位別名
- 
```sql
SELECT 
C.CustomerID as "@ID", 　　　　　　　　 --將 CustomerID 以 ID 屬性表示
C.CompanyName as "Name",                --將 CompanyName 以 Name 元素表示
P.ProductID as "Product/@ID",           --將 ProductID 以 Product 項目的 ID 屬性表示
P.ProductName as "Product/text()",      --將 ProductName 以 Product 項目的 InnerText 表示
O.OrderDate, 
P.UnitPrice AS "comment()",             --將 P.UnitPrice 當成【註解文字】
CAST('<Test/>' AS XML) AS "node()"   --插入一個自訂的項目
From Customers C
LEFT JOIN Orders as O ON C.CustomerID = O.CustomerID
LEFT JOIN OrderDetails as OD ON OD.OrderID = O.OrderID
LEFT JOIN Products as P ON P.ProductID=OD.ProductID
WHERE C.CustomerID in ('GROSR','LAZYK')
FOR XML PATH('Customer'), ROOT('Customers')
```
```xml
<Customers>
<Customer ID="GROSR">
<Name>GROSELLA-Restaurante</Name>
<Product ID="29">Thuringer Rostbratwurst</Product>
<OrderDate>1996-07-30T00:00:00</OrderDate>
<!--123.7900-->
<Test />
</Customer>
<Customer ID="GROSR">
<Name>GROSELLA-Restaurante</Name>
<Product ID="72">Mozzarella di Giovanni</Product>
<OrderDate>1996-07-30T00:00:00</OrderDate>
<!--34.8000-->
<Test />
</Customer>
<Customer ID="GROSR">
<Name>GROSELLA-Restaurante</Name>
<Product ID="10">Ikura</Product>
<OrderDate>1997-12-18T00:00:00</OrderDate>
<!--31.0000-->
<Test />
</Customer>
<Customer ID="GROSR">
<Name>GROSELLA-Restaurante</Name>
<Product ID="75">Rhonbrau Klosterbier</Product>
<OrderDate>1997-12-18T00:00:00</OrderDate>
<!--7.7500-->
<Test />
</Customer>
<Customer ID="LAZYK">
<Name>Lazy K Kountry Store</Name>
<Product ID="40">Boston Crab Meat</Product>
<OrderDate>1997-03-21T00:00:00</OrderDate>
<!--18.4000-->
<Test />
</Customer>
<Customer ID="LAZYK">
<Name>Lazy K Kountry Store</Name>
<Product ID="11">Queso Cabrales</Product>
<OrderDate>1997-05-22T00:00:00</OrderDate>
<!--21.0000-->
<Test />
</Customer>
</Customers>
```

### 使用 FOR XML PATH , 將多筆資料合為一筆

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdquNWzV-LVXgYR-n_YBBpdIbkUCDEdj3Keq373FNRYK5zDhCpJBgL0JtQeXh7h4Jjc7d4MVqZ4GTrWGkFsTBn9T5tA9_7SiiufD9-ShhcqaXtJRcrww4VglDfp489e3PztVoqN_n6ifM/s0/sql_merge_rows.png)
```sql
--使用 FOR XML PATH , 將多筆資料合為一筆
--查詢一樹狀結構資料中, 將每一個節點的子節點合併顯示
SELECT CID as ParentCID,
STUFF((
SELECT ',' + CID  
FROM tblCatalog as C2 
WHERE C2.ParentCID = C1.CID 
FOR XML PATH('')
),1,1,'') as ChildCID
FROM tblCatalog C1

-- STUFF 函數會將字串插入另一個字串。
-- 它會在第一個字串的指定開始位置刪除指定長度的字元，然後將第二個字串插入第一個字串的指定開始位置。

SELECT STUFF('abcdef', 3, 2, '123');   --ab123ef  在第一個字串的第3個字元開始,刪除2個字元,再插入第二個字串

-- FOR XML PATH 練習
SELECT  ', ' + CID FROM tblCatalog WHERE ParentCID = 'C0000' FOR XML PATH('') ;   --, C0001, C0002, C0003, C0059
```

## Nesting FOR XML Queries

All [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) mode queries can be nested to produce a hierarchy.   This means that you can place a [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) query as a subquery in another [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) query to produce a complete XML document.  
```sql
SELECT 
C.CustomerID as "@ID", 
C.CompanyName as "@Name", 
(
SELECT 
O.OrderID as "@ID", O.OrderDate as "@OrderDate",
(
SELECT 
P.ProductID as "@ID",
P.ProductName,
OD.Quantity, 
OD.UnitPrice
FROM 
OrderDetails as OD 
LEFT JOIN Products as P ON P.ProductID=OD.ProductID
WHERE OD.OrderID = O.OrderID
FOR XML PATH('Product'), TYPE
) as OrderDetails
FROM
Orders as O
WHERE O.CustomerID = C.CustomerID
FOR XML PATH('Orders'), TYPE	
)
From Customers C
WHERE C.CustomerID in ('GROSR','LAZYK')
FOR XML PATH('Customer'), ROOT('Customers');
```
```xml
<Customers>
<Customer ID="GROSR" Name="GROSELLA-Restaurante">
<Orders ID="10268" OrderDate="1996-07-30T00:00:00">
<OrderDetails>
<Product ID="29">
<ProductName>Thüringer Rostbratwurst</ProductName>
<Quantity>10</Quantity>
<UnitPrice>99.0000</UnitPrice>
</Product>
<Product ID="72">
<ProductName>Mozzarella di Giovanni</ProductName>
<Quantity>4</Quantity>
<UnitPrice>27.8000</UnitPrice>
</Product>
</OrderDetails>
</Orders>
<Orders ID="10785" OrderDate="1997-12-18T00:00:00">
<OrderDetails>
<Product ID="10">
<ProductName>Ikura</ProductName>
<Quantity>10</Quantity>
<UnitPrice>31.0000</UnitPrice>
</Product>
<Product ID="75">
<ProductName>Rhönbräu Klosterbier</ProductName>
<Quantity>10</Quantity>
<UnitPrice>7.7500</UnitPrice>
</Product>
</OrderDetails>
</Orders>
</Customer>
...
</Customers>
```

#### TYPE option

Note that the TYPE option is required in each subquery.   Without the TYPE option, SQL Server interprets the result of the subquery as an XML-encoded string.  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFKlBPSAiZsFZqxC9lpNC9wtGaNRcfmnuyA-xVrSQG0-CA6lZch7Lotp4_3DLAs8xzjl9Jvpmm3T7JlUfc6SMzKCOx8Td3L0Hesx5vunuqhxuLR6nrhW5eCD-p4AMp7beIilZh1PG4UC8/s800/sql-xml-nested-type.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFKlBPSAiZsFZqxC9lpNC9wtGaNRcfmnuyA-xVrSQG0-CA6lZch7Lotp4_3DLAs8xzjl9Jvpmm3T7JlUfc6SMzKCOx8Td3L0Hesx5vunuqhxuLR6nrhW5eCD-p4AMp7beIilZh1PG4UC8/s0/sql-xml-nested-type.png)

## WITH XMLNAMESPACES

若需要在 XML 中加入命名空間，則可以使用 [WITH XMLNAMESPACES](http://msdn.microsoft.com/zh-tw/library/ms177607.aspx) 陳述式以宣告一或多個 XML 命名空間。   
```sql
WITH XMLNAMESPACES(  	DEFAULT 'http://www.vito.com/CustomerOrders'  )SELECT CustomerID as Id, CompanyName as Name
From Customers
WHERE CustomerID in ('BERGS','WHITC')
FOR XML RAW('Customer'), ROOT('Customers');
```
```xml
<Customers xmlns="http://www.contoso.com/CustomerSchema">
<Customer Id="BERGS" Name="Berglunds snabbköp" />
<Customer Id="WHITC" Name="White Clover Markets" />
</Customers>
```

# 如何在程式中讀取 XML

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 陳述式會以資料列集(rowset)的型式回傳，如果希望以 XML 格式回傳，只要指定 [FOR XML](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx) 子句即可。  
```c#
using (SqlConnection conn = CreateTestConnection())
{
SqlCommand cmd = conn.CreateCommand();
cmd.CommandText = @"
Select C.CustomerID as ""@CustomerID"", CompanyName as ""@Name"", (
SELECT OrderID as ""@OrderID"", 
OrderDate as ""@OrderDate"", 
ShipAddress as ""@ShipAddress""
From Orders
WHERE CustomerID = C.CustomerID
FOR XML PATH('Order'), TYPE
) as Orders
From Customers C
WHERE C.CustomerID in ('BERGS','WHITC')
FOR XML PATH('Customer'), ROOT('Customers')
";

conn.Open();
XmlReader reader = cmd.ExecuteXmlReader();
XmlDocument doc = new XmlDocument();
doc.Load(reader);

foreach (XmlElement customer in doc.SelectNodes("/Customers/Customer"))
{
myDebug.WriteLine("Customer: {0}", customer.Attributes["CustomerID"].Value);
foreach (XmlElement order in customer.SelectNodes("Orders/Order"))
{
myDebug.WriteLine("\tOrder: {0}", order.Attributes["OrderID"].Value);
}
}
}
```

# XML 型別資料

當 XML 資料在儲存時，實際上是以 varbinary(max) 型別儲存。也就是說 XML 是一個二進位資料，而不是字串資料。  此外，也可以選擇性地將 XML 結構描述的集合與 xml 類型的變數、參數和資料行建立關聯。   在此情況下，此 xml 資料類型的執行個體即稱為「具類型」(Typed)。 非此種情況下的 XML 執行個體則稱為「不具類型」(Untyped)。   

## 如何建立具型別的 XML 資料

XML 資料型別可分成「**具類型**」（typed）和「**不具類型**」（untyped）。  簡單講，具類型就是使用了 XML 結構描述來限定資料類型。  

在 SQL Server 中新增了 XML Schema 的集合物件，透過 [CREATE XML SCHEMA COLLECTION](http://technet.microsoft.com/zh-tw/library/ms176009.aspx) 方法將自訂 XML SCHEMA 新增到集合物件中，其語法如下：  
```sql
CREATE XML SCHEMA COLLECTION collectionName 
AS 
--Schema內容
```

建立完 XML SCHEMA COLLECTION 物件後，以 CREATE() 語法新增資料表時，可以將該 XML SCHEMA COLLECTION 名稱指派到 xml 資料型別之後，將來新增或更新資料到該欄位時，會取出 XML SCHEMA 內容做判斷，其語法如下：  
```sql
CREATE TABLE TableName
(
ColName xml([XML_SCHEMA_Collection_Name])
) 
```

### 範例演練
```sql
CREATE XML SCHEMA COLLECTION BookSchema
AS
N'<?xml version="1.0"?>
<xsd:schema 
xmlns:xsd = "http://www.w3.org/2001/XMLSchema"
targetNamespace = "http://BookSchema" >
<xsd:element name="Book">
<xsd:complexType>
<xsd:attribute name="ISBN" type="xsd:string"/>
<xsd:attribute name="Title" type="xsd:string"/>
<xsd:attribute name="Price" type="xsd:int"/>
</xsd:complexType>
</xsd:element>
</xsd:schema>';

CREATE TABLE MyBook
(
BookID int IDENTITY(1,1) NOT NULL,
BookData XML(BookSchema)
);
```
```sql
INSERT MyBook (BookData) VALUES (N'
<Book xmlns="http://BookSchema"
ISBN  ="9789781234567"
Title ="MS SQL Server 2008"
Price ="600" >
</Book>');

--以下DML會失敗，因為欄位型態不符規定。
INSERT MyBook (BookData) VALUES (N'
<Book xmlns="http://BookSchema"
ISBN  ="9789781234567"
Title ="MS SQL Server 2008"
Price ="六百" >
</Book>');
```

使用具型別XML時有一點要注意：若是在資料建立後才要變換 Schema ，則必須先將該XML欄位改成 untyped ，再刪除 Schema 後再重建 Schema 。

### 刪除與檢查 XML SCHEMA
```sql
--檢查 XML SCHEMA
SELECT \* FROM sys.xml_schema_collections 

--刪除 XML SCHEMA
DROP XML SCHEMA COLLECTION BookSchema
```

## 如何查詢 XML 型別資料

- [XQuery](http://technet.microsoft.com/zh-tw/library/ms189075.aspx) 是一項可以透過 XML 結構特性來查詢 XML 文件的敘述式。
- [XQuery](http://technet.microsoft.com/zh-tw/library/ms189075.aspx) 敘述式由一組 [FLWOR](http://en.wikipedia.org/wiki/FLWOR) (發音同"flower") 組成，也就是由 For 、 Let 、 Where 、 Order By 、 Return 子句等結構而成。

SQL Server 2005針對 xml 資料類型提供五個方法，用來進行 XML 資料的存取與維護：

- [query()](http://technet.microsoft.com/zh-tw/library/ms191474.aspx)：從 XML 欄位執行指定的 [XQuery](http://technet.microsoft.com/zh-tw/library/ms189075.aspx) ，以取出 XML 類型的資料
- [value()](http://technet.microsoft.com/zh-tw/library/ms178030.aspx)：針對 XML 欄位取出一個純量值
- [exist()](http://technet.microsoft.com/zh-tw/library/ms189869.aspx)：判斷 XML 內某個 Node 是否存在，有則回傳 1 沒有就回傳 0
- [modify()](http://technet.microsoft.com/zh-tw/library/ms187093.aspx)：修改 XML 欄位內的資料
- [nodes()](http://technet.microsoft.com/zh-tw/library/ms188282.aspx)：將 XML Node 切分成多筆 Row

建立下面範例要用到的測試資料
```sql
CREATE TABLE MyProducts
(
ID int IDENTITY(1,1) NOT NULL,
ProductsData XML
);
```
```xml
INSERT MyProducts Values('<Products ProductID="1" ProductName="Chai Chai" SupplierID="1" UnitPrice="18.0000" />')
INSERT MyProducts Values('<Products ProductID="2" ProductName="Chang" SupplierID="1" />')
INSERT MyProducts Values('<Products ProductID="3" ProductName="Aniseed Syrup" SupplierID="1" UnitPrice="10.0000" />')
INSERT MyProducts Values('<Products ProductID="4" ProductName="Chef Anton''s Cajun Seasoning" SupplierID="2" UnitPrice="22.0000" />')
INSERT MyProducts Values('<Products ProductID="5" ProductName="Chef Anton''s Gumbo Mix" SupplierID="2" UnitPrice="21.3500" />')
INSERT MyProducts Values('<Products ProductID="6" ProductName="Grandma''s Boysenberry Spread" SupplierID="3" UnitPrice="25.0000" />')
INSERT MyProducts Values('<Products ProductID="7" ProductName="Uncle Bob''s Organic Dried Pears" SupplierID="3" UnitPrice="30.0000" />')
INSERT MyProducts Values('<Products ProductID="8" ProductName="Northwoods Cranberry Sauce" SupplierID="3" UnitPrice="40.0000" />')
INSERT MyProducts Values('<Products ProductID="9" ProductName="Mishi Kobe Niku" SupplierID="4" UnitPrice="97.0000" />')
INSERT MyProducts Values('<Products ProductID="10" ProductName="Ikura" SupplierID="4" UnitPrice="31.0000" />')
```

### The exist() Method

[exist()](http://technet.microsoft.com/zh-tw/library/ms189869.aspx) 方法是用來驗證 XPath ，若指定的 XPath 存在則回傳 1 。  
```sql
--查詢 ProductsData 欄位值中，存在 SupplierID = 2 的資料
SELECT \*
FROM MyProducts
WHERE ProductsData.exist('/Products[xs:integer(@SupplierID) = 2]') = 1;

SELECT \*
FROM MyProducts
WHERE ProductsData.exist('/Products[@SupplierID=2]') = 1;
```

### The value() Method

[value()](http://technet.microsoft.com/zh-tw/library/ms178030.aspx) 方法會使用 TSQL [CONVERT](http://technet.microsoft.com/zh-tw/library/ms187928.aspx) 運算子，隱含地將 [XQuery](http://technet.microsoft.com/zh-tw/library/ms189075.aspx) 轉換成指定的型別。

**語法**
```sql
value (XQuery, SQLType)
```

#### 範例１
```sql
SELECT 
ProductsData.value('(/Products/@ProductID)[1]', 'INT') AS ProductID,
ProductsData.value('(/Products/@ProductName)[1]', 'NVARCHAR(50)') AS ProductName
FROM MyProducts
WHERE ProductsData.exist('/Products[xs:integer(@SupplierID) = 2]') = 1;
```

上面範例中，[1]表示第一個找到的位置

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1ReJDcac1nEGEHpSkLnSBjEwpNFbDLzO2SvbQndNMAKNS1V6MC9UfmL_JVJQyiaBEjF0I0mqNJ-GPF16nsECJHm4BWxsTathE-bGKdWnToDyP8XKgAdmQrPer_Xs6-QtHwzfUn2hTjwo/s0/sql-xquery-1.png)

#### 範例２
```sql
--取出所有商品的售價，再求平均值
SELECT Avg(ProductsData.value('(/Products/@UnitPrice)[1]', 'Money')) AS AvgPrice
FROM MyProducts;

--取出 XML 資料中，含有 Products 項目的個數。再求總數。
SELECT Sum(ProductsData.value('count(/Products)', 'INT')) AS ItemCount
FROM MyProducts;
```

#### 使用 [exist()](http://technet.microsoft.com/zh-tw/library/ms189869.aspx) 方法而非 [value()](http://technet.microsoft.com/zh-tw/library/ms178030.aspx) 方法

基於效能的考量，不要在述詞中使用 [value()](http://technet.microsoft.com/zh-tw/library/ms178030.aspx) 方法來與關聯式值做比較，而是使用 [exist()](http://technet.microsoft.com/zh-tw/library/ms189869.aspx) 搭配 sql:column() 進行比較。例如：
```sql
--找出 ProductID=5 的資料
SELECT \* FROM MyProducts
WHERE ProductsData.value('(/Products/@ProductID)[1]', 'INT') = 5
```

這可由下列方式來撰寫：
```sql
SELECT \* FROM MyProducts
WHERE ProductsData.exist( '/Products[@ProductID=5]') = 1
```

### The nodes() Method

[nodes()](http://technet.microsoft.com/zh-tw/library/ms188282.aspx) 方法可以將 XML 格式中的每個 Element 轉換成 Tabular 的一筆 Row 。  這樣的特性也可以用來將 XML 資料類型轉換成 Table 形式。

**語法**
```sql
nodes (XQuery) as Table(Column)
```

#### 範例１

將 XML Node 切分成多筆 Row
```sql
DECLARE @Products XML;
SET @Products= (
SELECT ProductID, ProductName, SupplierID, UnitPrice
FROM Products WHERE ProductID<5
FOR XML AUTO
)

SELECT
tab.col.query('.') AS result
FROM @Products.nodes('/Products') AS tab(col);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiL-Q-NI4Pr_PM9BSrqx7v58767M7-oRXbO2SexqWNfeWJXeZrmMQXfTiPdKxA1Qe4HOM1wIfuScMOI_OOsa6PaJWYuSWx1tTud_lpNCtuqTrikELCNtjz9yizurRj5sP1gGW2C_vTY6Sw/s0/sql-xml-nodes.png)

#### 範例２

將 XML 轉成 Tabular
```sql
DECLARE @Products XML;
SET @Products= (
SELECT ProductID, ProductName, SupplierID, UnitPrice
FROM Products WHERE ProductID<5
FOR XML AUTO
)

SELECT
tab.col.value('@ProductID', 'INT') AS ProductID
,tab.col.value('@ProductName', 'NVARCHAR(50)') AS ProductName
,tab.col.value('@SupplierID', 'INT') AS SupplierID
,tab.col.value('@UnitPrice', 'Money') AS UnitPrice
FROM @Products.nodes('/Products') AS tab(col);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQloOpt-VwuqqBOeNW8-tULvaoAVaVnhjnK3JnnGLsYas8huNPHHHWUwPzwL_c1uvVnBaHP083B0DNUjhPqfrDvIjVaYks-W6pJOeLyCNcuxCPy0-yK9gP_ud_0sYeBB2AQTZqS1Z-dKM/s0/sql-xquery-2.png)

#### 範例３：將 XML 資料與 TABLE 資料做 JOIN 
```sql
DECLARE @OrderData XML;
SET @OrderData= (
SELECT OrderID, ProductID, UnitPrice
FROM OrderDetails WHERE OrderID = 10248
FOR XML AUTO
)
SELECT 
T.c.value('@ProductID','int') as ProductID, 
P.ProductName as ProductName 
FROM @OrderData.nodes('/OrderDetails') as T(c)
INNER JOIN Products P ON P.ProductID = T.c.value('@ProductID','int')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkTS0yTvzqc5ZqyOxRk2kTbONwu-BhJ2JXQk2GA48VYnBfwfMcBwcAaBNzrR4EEZuNJN7BePNcyoryLprhUOYbJOI5uwornRqKn3A1HcZ2SVnmvGJ0D1iYs1Lil5AXjEvf1NtiRmQzRmc/s0/sql-xquery-3.png)

### The query() Method

[query()](http://technet.microsoft.com/zh-tw/library/ms191474.aspx) 方法可以用來查詢 xml 資料，回傳亦為 xml 類型。 該方法會傳回不具類型的 XML 執行個體。

**語法**
```sql
query ('XQuery')
```

#### 範例１：查詢 xml 欄位
```sql
SELECT ID, ProductsData.query('<Products ProductID="{Products[1]/@ProductID}" />') as Result
FROM MyProducts
WHERE ProductsData.exist('/Products[@SupplierID=2]') = 1
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIBejr5NQWWfeP92pdoydSQKUYQam6R5zEdFMsMADJ90gG-6KWHiRMeyyReX8fkU43N2SV89JWS8ew1rq90FLpP017SxRYfFYpVv_xlgumrQdbDUJvYVqrmsvElcGyQEqSoxa9tDPw2xU/s0/sql-xquery-4.png)

#### 範例２：查詢 xml 資料
```sql
declare @myDoc xml
set @myDoc = '
<Customers>
<Customer CustomerID="BERGS" Name="Berglunds snabbköp">
<Orders>
<Order OrderID="10278" OrderDate="1996-08-12T00:00:00" ShipAddress="Berguvsvägen  8" />
<Order OrderID="10280" OrderDate="1996-08-14T00:00:00" ShipAddress="Berguvsvägen  8" />
</Orders>
</Customer>
<Customer CustomerID="WHITC" Name="White Clover Markets">
<Orders>
<Order OrderID="10269" OrderDate="1996-07-31T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10344" OrderDate="1996-11-01T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10469" OrderDate="1997-03-10T00:00:00" ShipAddress="1029 - 12th Ave. S." />
</Orders>
</Customer>
</Customers>';
SELECT @myDoc.query('/Customers/Customer[2]/Orders[1]')
```
```xml
<Orders>
<Order OrderID="10269" OrderDate="1996-07-31T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10344" OrderDate="1996-11-01T00:00:00" ShipAddress="1029 - 12th Ave. S." />
<Order OrderID="10469" OrderDate="1997-03-10T00:00:00" ShipAddress="1029 - 12th Ave. S." />
</Orders>
```

#### 範例３：使用 [XQuery](http://technet.microsoft.com/zh-tw/library/ms189075.aspx) 查詢資料
```sql
DECLARE @Products XML;
SET @Products= (
SELECT ProductID, ProductName, SupplierID, UnitPrice
FROM Products WHERE ProductID<10
FOR XML AUTO
)
SELECT @Products.query('
for $P in /Products
where xs:integer($P/@ProductID) < 5
return 
<Products>
{ $P/@ProductID } 
{ $P/@ProductName } 
</Products>
')
```
```xml
<Product ProductID="1" ProductName="Chai Chai" />
<Product ProductID="2" ProductName="Chang" />
<Product ProductID="3" ProductName="Aniseed Syrup" />
<Product ProductID="4" ProductName="Chef Anton's Cajun Seasoning" />
```

## 如何修改 XML 型別資料

- [modify()](http://technet.microsoft.com/zh-tw/library/ms187093.aspx) 方法可以透過 XML [DML](http://msdn.microsoft.com/zh-tw/library/ff848766.aspx) 陳述式來插入、更新或刪除 XML 資料的節點。
- [modify()](http://technet.microsoft.com/zh-tw/library/ms187093.aspx) 方法適用於 xml 類型變數或資料行的內容
```sql
modify (XML_DML)
```

參數 XML\_DML 是 XML 資料操作語言 (DML) 的字串。 XML 文件會根據此運算式來更新。

### replace value of (XML DML)

[replace value of()](http://technet.microsoft.com/zh-tw/library/ms190675.aspx) 可用來更新文件中的節點值。

**語法**
```sql
replace value of 
Expression1 
with
Expression2
```

#### 範例１：變更 xml 資料的節點值
```sql
DECLARE @Products XML;
SET @Products= (
SELECT ProductID, ProductName, SupplierID, UnitPrice
FROM Products WHERE ProductID<10
FOR XML AUTO
)
--將資料中的第一個 UnitPrice 節點值變更為 18.5
SET @Products.modify('
replace value of (/Products/@UnitPrice)[1]
with "18.5"
')
```

#### 範例２：變更 xml 欄位中的節點值
```sql
SELECT \* FROM MyProducts 
WHERE ProductsData.exist( '/Products[@ProductID=5]') = 1

--變更前
--<Products ProductID="5" ProductName="Chef Anton Gumbo Mix" SupplierID="2" UnitPrice="21.3500" />

UPDATE MyProducts
SET ProductsData.modify('
replace value of (/Products/@UnitPrice)[1]
with "20.0"
')
WHERE ProductsData.exist( '/Products[@ProductID=5]') = 1

--變更後
--<Products ProductID="5" ProductName="Chef Anton Gumbo Mix" SupplierID="2" UnitPrice="20.0" />
```

### insert (XML DML)

[insert()](http://technet.microsoft.com/zh-tw/library/ms175466.aspx) 可用來更新文件中的節點值。

**語法**
```sql
insert 
Expression1 
(
{as first | as last} into | after | before
Expression2
)
```

### delete (XML DML)

[delete()](http://technet.microsoft.com/zh-tw/library/ms190254.aspx) 可用來刪除文件中的節點。

**語法**
```sql
delete Expression
```

## XPath 語法小複習

- **name**
- **[n]**
- **\***
- **/**
- **..**
- **.**
- **//**
- **[@key='value']**
- **name[@key='value']**
- **[text()='value']**
- **name[text()='value']**
- **@name**
- **@\***
## 參考資料  

- [xml Data Type(Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms187339.aspx)
- [FOR XML (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms178107.aspx)
- [使用巢狀 FOR XML 查詢](http://msdn.microsoft.com/zh-tw/library/ms188276.aspx)
- [CREATE XML SCHEMA COLLECTION (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms176009.aspx)
- [FFLWOR 陳述式與反覆運算 (XQuery)](http://msdn.microsoft.com/en-us/library/ms190945.aspx)
- [XPath, XQuery, and XSLT Functions](http://www.w3schools.com/xpath/xpath_functions.asp)
- [Xquery 語言參考 (SQL Server)](http://technet.microsoft.com/zh-tw/library/ms189075.aspx)
- [XML 資料類型方法](http://msdn.microsoft.com/zh-tw/library/ms178030.aspx)
- [XML 資料修改語言 (XML DML)](http://msdn.microsoft.com/zh-tw/library/ms177454.aspx)