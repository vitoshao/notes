---
title: LINQ 入門(一)
layout: default
parent: LINQ
nav_order: 1
description: "LINQ 入門(一)"
date: 2012-05-15
tags: [DotNetFramework,LINQ]
postid: "5835563713084355010"
---
# LINQ 簡介

MS 在 .NET Framework 3.5 版中，引進一個新的功能，稱之為 LINQ (Language Integrated Query)。  它是一種針對**集合**元素進行查詢的語言，如此一來，不管你使用何種資料來源，只要該資料是實作 IEnumerable 介面的物件，都可以使用單一的 LINQ 語法去查詢處理。  

LINQ 使用類似 SQL 查詢的語法：  
from 子句指定查詢來源；  
where 子句設定篩選條件；  
order by 子句做排序；  
group by 子句對資料分組，  
select 子句回傳資料。  
而且，LINQ 查詢也支援複雜的 join 語法，這樣就可以在程式語言中，直接執行複雜的連結運算，以達到資料搜尋的目的。  

LINQ包含以下部份

- LINQ to Object：可查詢集合與陣列中的資料
- LINQ to DataSet：查詢DataSet/DataTable中的資料。
- LINQ to SQL：查詢或異動微軟 SQL Server 資料庫的資料。
- LINQ to XML：查詢XML節點。

#### LINQ to SQL

LINQ to SQL，可以利用類似 SQL 查詢的語法，在執行期間將關連式資料庫的資料庫視為物件，直接進行管理及查詢。  也就是在程式碼中直接使用類似 SQL 的直覺式語法，進行查詢物件的資料、篩選、排序資料等等。  

LINQ to SQL 內建了物件與關聯式資料兩者之間對應的架構，讓程式設計師能夠建立對應於資料庫結構的.NET 物件模型。  有了物件模型後，便可透過 LINQ 語法來查詢、或修改其中的資訊。  而建立對應到資料庫物件的物件模型這些繁雜的工作，還可以交由工具去幫你做。  

#### LINQ to DataSet

ADO.NET 提供的 DataSet 與 DataTable 具備有將資料暫存在用戶端電腦上的能力。  DataSet 就如離線的資料庫一般，可以存放一到多個資料表，每個資料表為一個 DataTable 物件。  DataSet 也可以描述資料表與資料表之間的關聯，以及一對一、一對多等特色。  雖然 DataSet 與 DataTable 提供一些方法，具備有篩選、搜尋資料的能力，但認真來說，這些功能稍嫌簡陋，缺乏彈性，若想要做一些複雜的查詢，往往不能靠 DataSet、DataTable 來達成。  

當你利用 DataAdapter 把資料載入 DataSet 之後，若需要查詢其中的資料，就可以使用 LINQ to DataSet。程式設計師可以針對 DataSet 來撰寫 LINQ 查詢。

# LINQ to SQL 可以做到的功能

#### 查詢

執行 LINQ 查詢來擷取結果即可。LINQ to SQL 會將所有必要的作業轉譯成熟悉的 SQL 作業。
```c#
NorthWindDataContext nw = new NorthWindDataContext(nw_conn);
string strCity = txtCity.Text; // "Taipei";

var query =
from cust in nw.Customers
where cust.City == strCity
select new {cust.City, cust.CompanyName, cust.ContactName};

foreach (var cust in query)
{
Console.WriteLine("{0}  {1}", cust.CompanyName, cust.CompanyName);
}
```

#### 新增

若要執行 SQL Insert，只要將物件加入至所建立的物件模型，並在 DataContext 上呼叫 SubmitChanges 即可。
```c#
NorthWindDataContext nw = new NorthWindDataContext(nw_conn);

Customers cust = new Customers();
cust.CompanyName = "vito";
cust.City = "Taipei";
cust.CustomerID = "10001";
cust.Phone = "02-3939889";

nw.Customers.InsertOnSubmit(cust);

nw.SubmitChanges();
```

#### 更新

若要更新 (Update) 資料庫項目，請先擷取該項目，然後直接在物件模型中編輯該項目。修改物件之後，再於 DataContext 上呼叫 SubmitChanges 以更新資料庫。
```c#
NorthWindDataContext nw = new NorthWindDataContext(nw_conn);
string strCity = txtCity.Text;

var query =
from cust in nw.Customers
where cust.City.Contains(strCity)
select cust;

foreach (Customers cust in query)
{
cust.City = "Taichun";
}

nw.SubmitChanges();
```

#### 刪除

若要刪除 (Delete) 項目，請從項目所屬的集合中移除該項目，然後在 DataContext 上呼叫 SubmitChanges，以認可變更。
```c#
NorthWindDataContext nw = new NorthWindDataContext(nw_conn);
string strCustomerID = "10001";

var query =
from cust in nw.Customers
where cust.CustomerID == strCustomerID
select cust;

if (query.Count() > 0)
{
nw.Customers.DeleteOnSubmit(query.First());
}
nw.SubmitChanges();
```


- [LINQ to SQL](http://msdn.microsoft.com/zh-tw/library/bb386976.aspx)
- [逐步解說：操作資料 (C#) (LINQ to SQL)](http://msdn.microsoft.com/zh-tw/library/bb386927)
-