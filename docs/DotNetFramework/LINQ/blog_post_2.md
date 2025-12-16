---
title: LINQ 入門(三)關連性查詢
layout: default
parent: LINQ
nav_order: 2
description: "LINQ 入門(三)關連性查詢"
date: 2012-05-16
tags: [DotNetFramework,LINQ]
postid: "5663970696949537765"
---
# 跨 Table 的關聯性查詢

## 直接使用物件模型中的關連

如果在資料庫中 Catalog 和 Documents 之間已經建立了關連性(外部索引鍵)，在 linq 語法中，此時不需要在 Catalog 和 Documents 之間使用「聯結」(Join)，  就可以直接從 Catalog 物件存取 Documents 物件。  
```c#
// 取得 tblUser , 以執行查詢
Table<tblCatalog> Catalog = db.GetTable<tblCatalog>();

var catalogQuery =
from catalog in Catalog
where catalog.tblDocuments.Any()
select catalog;

foreach (var catalog in catalogQuery)
{
if (catalog.tblDocuments.Count > 0)
{
foreach (var doc in catalog.tblDocuments)
{
Console.WriteLine("  CatalogName={0}, DocumentID={1}, Title={2}", catalog.CName, doc.DID, doc.Title);
}
}
else
{
Console.WriteLine("CatalogName={0}, without documents ...", catalog.CName);
}
}
```
```sql
SELECT [t0].[CID], [t0].[CName], [t0].[ParentCID], [t0].[SortNum], [t0].[CreatedTime], [t0].[CreatedUser], [t0].[RevisedTime], [t0].[RevisedUser]
FROM [tblCatalog] AS [t0]
WHERE EXISTS(
SELECT NULL AS [EMPTY]
FROM [tblDocuments] AS [t1]
WHERE [t1].[CID] = [t0].[CID]
)
```

上面的 LINQ ，會自動使用延遲載入 (deferred loading) 的功能，也就是預設狀況下只會取得要求的第一層資料，相關連的資料並不會自動取得，  除非後來有要需要使用到第二層的資料，才去資料庫抓取。

##  Inner Join 

從分類中找文章。
```c#
Table<tblCatalog> Catalog = db.GetTable<tblCatalog>();
Table<tblDocuments> Documents = db.GetTable<tblDocuments>();

var innerJoin =
from cata in Catalog
join doc in Documents on cata.CID equals doc.CID
where cata.CID == "C0088" || cata.CID == "C0000"
select new { CName = cata.CName, DID = doc.DID, Title = doc.Title };

foreach (var obj in innerJoin)
{
Console.WriteLine("CatalogName={0}, DocumentID={1}, Title={2}", obj.CName, obj.DID, obj.Title);
}

//CatalogName=CSharp, DocumentID=D0048, Title=Properties
//CatalogName=CSharp, DocumentID=D0110, Title=is 和 as
//CatalogName=CSharp, DocumentID=D0124, Title=傳遞參數1
```
```sql
SELECT [t0].[CName], [t1].[DID], [t1].[Title]
FROM [tblCatalog] AS [t0]
INNER JOIN [tblDocuments] AS [t1] ON [t0].[CID] = [t1].[CID]
WHERE ([t0].[CID] = @p0) OR ([t0].[CID] = @p1)
```

##  Left Join 

從分類中找文章，沒有文章的分類也要列出。
```c#
var leftJoin =
from cata in Catalog
join doc in Documents on cata.CID equals doc.CID into k
from doc in k.DefaultIfEmpty()
where cata.CID == "C0088" || cata.CID == "C0000"
select new { CName = cata.CName,
DID = doc == default(tblDocuments) ? "null" : doc.DID,
Title = doc.Title ?? "null"
};

foreach (var obj in leftJoin)
{
Console.WriteLine("CatalogName={0}, DocumentID={1}, Title={2}", obj.CName, obj.DID, obj.Title);
}

//CatalogName=Documents, DocumentID=null, Title=null
//CatalogName=CSharp, DocumentID=D0048, Title=Properties
//CatalogName=CSharp, DocumentID=D0110, Title=is 和 as
//CatalogName=CSharp, DocumentID=D0124, Title=傳遞參數1
```
```sql
SELECT [t0].[CName], 
(CASE 
WHEN [t2].[test] IS NULL THEN CONVERT(NVarChar(5),@p2)
ELSE CONVERT(NVarChar(5),[t2].[DID])
END) AS [DID], COALESCE([t2].[Title],@p3) AS [Title]
FROM [tblCatalog] AS [t0]
LEFT OUTER JOIN (
SELECT 1 AS [test], [t1].[DID], [t1].[CID], [t1].[Title]
FROM [tblDocuments] AS [t1]
) AS [t2] ON [t0].[CID] = [t2].[CID]
WHERE ([t0].[CID] = @p0) OR ([t0].[CID] = @p1)
```

##  Group By 

從分類中找文章，沒有文章的分類也要列出。
```c#
var gooupby =
from cata in Catalog
join doc in Documents on cata.CID equals doc.CID into docs
where cata.CID == "C0088" || cata.CID == "C0000"
select new
{
CName = cata.CName,
Docs = docs
};

foreach (var obj in gooupby)
{
Console.WriteLine("CatalogName={0}, DocCount={1}", obj.CName, obj.Docs.Count());
}

//CatalogName=Documents, DocCount=0
//CatalogName=CSharp, DocCount=3
```
```sql
SELECT [t0].[CName], [t1].[DID], [t1].[CID], [t1].[Title], [t1].[FileName], [t1].[Url], [t1].[Target], [t1].[SortNum], [t1].[Visits], [t1].[CreatedTime], [t1].[CreatedUser], [t1].[RevisedTime], [t1].[RevisedUser], [t1].[SetTop], (
SELECT COUNT(\*)
FROM [tblDocuments] AS [t2]
WHERE [t0].[CID] = [t2].[CID]
) AS [value]
FROM [tblCatalog] AS [t0]
LEFT OUTER JOIN [tblDocuments] AS [t1] ON [t0].[CID] = [t1].[CID]
WHERE ([t0].[CID] = @p0) OR ([t0].[CID] = @p1)
ORDER BY [t0].[CID], [t1].[DID]
```

##  多欄位 Join 
```c#
from B in TblCodeBs
join C in TblCodeCs on new {B.CodeA , B.CodeB} equals new {C.CodeA, C.CodeB} into CodeC
from C in CodeC.DefaultIfEmpty()
select new 
{ 
B.CodeA, B.CodeB, CodeC = C.CodeC==null?0:C.CodeC, 
BName = B.CodeName, 
CName = C.CodeName ?? "NA"
}
```
```sql
-- Region Parameters
DECLARE @p0 Int = 0
DECLARE @p1 NVarChar(1000) = 'NA'
-- EndRegion
SELECT [t0].[CodeA], [t0].[CodeB], 
(CASE 
WHEN ([t1].[CodeC]) IS NULL THEN @p0
ELSE [t1].[CodeC]
END) AS [CodeC], [t0].[CodeName] AS [BName], COALESCE([t1].[CodeName],@p1) AS [CName]
FROM [tblCodeB] AS [t0]
LEFT OUTER JOIN [tblCodeC] AS [t1] ON ([t0].[CodeA] = [t1].[CodeA]) AND ([t0].[CodeB] = [t1].[CodeB])
```

##  Like 查詢
```c#
from cata in TblCatalogs
join doc in TblDocuments on cata.CID equals doc.CID into k
from doc in k.DefaultIfEmpty()
where cata.CID.Contains("C0088") || cata.CID.StartsWith("C0088") || cata.CID.EndsWith("C0088")
select new { CName = cata.CName,
DID = doc == default(TblDocuments) ? "NA" : doc.DID,
Title = doc.Title ?? "null"
}
```
```sql
DECLARE @p0 VarChar(1000) = '%C0088%'
DECLARE @p1 VarChar(1000) = 'C0088%'
DECLARE @p2 VarChar(1000) = '%C0088'
DECLARE @p3 NVarChar(1000) = 'NA'
DECLARE @p4 NVarChar(1000) = 'null'

SELECT [t0].[CName], 
(CASE 
WHEN [t2].[test] IS NULL THEN CONVERT(NVarChar(5),@p3)
ELSE CONVERT(NVarChar(5),[t2].[DID])
END) AS [DID], COALESCE([t2].[Title],@p4) AS [Title]
FROM [tblCatalog] AS [t0]
LEFT OUTER JOIN (
SELECT 1 AS [test], [t1].[DID], [t1].[CID], [t1].[Title]
FROM [tblDocuments] AS [t1]
) AS [t2] ON [t0].[CID] = [t2].[CID]
WHERE ([t0].[CID] LIKE @p0) OR ([t0].[CID] LIKE @p1) OR ([t0].[CID] LIKE @p2)
```


- [逐步解說：跨關聯性查詢 (C#) (LINQ to SQL)](http://msdn.microsoft.com/zh-tw/library/bb386951)
- [採用 ORM 技術需瞭解何謂查詢計畫 ( 以 LINQ to SQL 為例 )](http://blog.miniasp.com/post/2009/03/04/Using-ORM-must-know-Query-Plan-by-LINQ-to-SQL-example.aspx)
- [LINQ to SQL 其實不一定要下 join (deferred loading)](http://kelp.phate.org/2010/12/linq-to-sql-join-deferred-loading.html)
- 
-