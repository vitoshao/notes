---
title: Lab2：建立倉儲資料庫
layout: default
parent: Labs
nav_order: 2
description: "Lab2：建立倉儲資料庫"
date: 2013-12-11
tags: [SQL,Labs]
postid: "734449686822762108"
---
在以下的練習中，我們將建立一個 DW 資料庫，並使用 AdventureWorksDW2012 資料庫當做資料來源執行 ETL。  

# 建立倉儲資料庫

## 建立 Database

### create the database

###### Tip

- SIMPLE recovery mode
- reserve sufficient space for your data and log files
- prevent autoshrinking and autogrowing
```sql
USE [master]
GO

CREATE DATABASE [MyDW] ON  PRIMARY 
( 
NAME = N'MyDW', 
FILENAME = N'D:\Database\TestDB\MyDW.mdf' , 
SIZE = 300000KB , 
MAXSIZE = UNLIMITED, 
FILEGROWTH = 1000000KB )
LOG ON 
( 
NAME = N'MyDW_log', 
FILENAME = N'D:\Database\TestDB\MyDW_log.ldf' , 
SIZE = 100000KB , 
MAXSIZE = 100000KB ,
FILEGROWTH = 10%
)
GO

USE [MyDW]
GO

ALTER DATABASE [MyDW] SET RECOVERY SIMPLE WITH NO_WAIT;
GO
```

### create a sequence object

為了提供給將來 ETL 客戶資料時，能夠產生我們要的 surrogate key ，所以我們先建立一個 sequence object 備用。  
```sql
USE [MyDW]
GO

IF OBJECT_ID('dbo.SeqCustomerDwKey','SO') IS NOT NULL
DROP SEQUENCE dbo.SeqCustomerDwKey;
GO

CREATE SEQUENCE dbo.SeqCustomerDwKey AS INT
START WITH 1
INCREMENT BY 1;
GO
```

## 建立 Dimenstions

### Create the Customers dimension

Customers 的資料來源是 AdventureWorksDW2012.DimCustomer。  

Dimenstions 與 DimGeography 具有關連性，我們必須反正規化這層結構；  而且，為了支援 SCD Type 2 ，所以我們必須加入一個 Surrogate key 欄位。  
```sql
CREATE TABLE dbo.Customers(
CustomerDwKey	int				NOT NULL,
CustomerKey		int				NOT NULL,
FullName		nvarchar(150)	NULL,
EmailAddress	nvarchar(50)	NULL,
BirthDate		date			NULL,
MaritalStatus	nchar(1)		NULL,
Gender			nvarchar(1)		NULL,
Education		nvarchar(40)	NULL,
Occupation		nvarchar(100)	NULL,
City			nvarchar(30)	NULL,
StateProvince	nvarchar(50)	NULL,
CountryRegion	nvarchar(50)	NULL,
Age  AS (
case 
when datediff(year,BirthDate,getdate())<=(40) then 'Younger' 
when datediff(year,BirthDate,getdate())>(50) then 'Older' 
else 'Middle Age' 
end),
CurrentFlag		bit				NOT NULL DEFAULT 1,
CONSTRAINT PK_Customer PRIMARY KEY CLUSTERED 
(
CustomerDwKey ASC
)
)
```

### Create the Products dimension

Products 的資料來源是 AdventureWorksDW2012.DimProducts
```sql
CREATE TABLE dbo.Products(
ProductKey		int			NOT NULL,
ProductName		nvarchar(50) NULL,
Color			nvarchar(15) NULL,
Size			nvarchar(50) NULL,
SubcategoryName nvarchar(50) NULL,
CategoryName	nvarchar(50) NULL,
CONSTRAINT PK_Products PRIMARY KEY CLUSTERED (
ProductKey ASC
)
)
```

### Create the Dates dimension

Dates 的資料來源是 AdventureWorksDW2012.DimDate
```sql
CREATE TABLE dbo.Dates(
DateKey			int			 NOT NULL,
FullDate		date		 NOT NULL,
MonthNumberName nvarchar(15) NULL,
CalendarQuarter tinyint		 NULL,
CalendarYear	smallint	 NULL,
CONSTRAINT PK_Dates PRIMARY KEY CLUSTERED 
(
DateKey ASC
)
)
```

## 建立 Fact Tables

### create fact tables

InternetSales 的資料來源是 AdventureWorksDW2012.FactInternetSales。

雖然我們可以同原始資料中，使用 SalesOrderNumber + SalesOrderLineNumber 當做 PK ，不過，在這裡我們要練習自動編號，所以利用 IDENTITY 欄位來當做 surrogate key。   
```sql
USE [MyDW]
GO

CREATE TABLE dbo.InternetSales(
InternetSalesKey	int		 NOT NULL IDENTITY(1,1) ,
CustomerDwKey		int		 NOT NULL,
ProductKey			int		 NOT NULL,
DateKey				int		 NOT NULL,
OrderQuantity		smallint NOT NULL DEFAULT 0,
SalesAmount			money	 NOT NULL DEFAULT 0,
UnitPrice			money	 NOT NULL DEFAULT 0,
DiscountAmount		float	 NOT NULL DEFAULT 0,
CONSTRAINT PK_InternetSales
PRIMARY KEY (InternetSalesKey)
)
GO
```

### setting foreign keys
```sql
ALTER TABLE dbo.InternetSales 
ADD CONSTRAINT FK_InternetSales_Customers 
FOREIGN KEY(CustomerDwKey)
REFERENCES dbo.Customers (CustomerDwKey);
ALTER TABLE dbo.InternetSales 
ADD CONSTRAINT FK_InternetSales_Products 
FOREIGN KEY(ProductKey)
REFERENCES dbo.Products (ProductKey);
ALTER TABLE dbo.InternetSales 
ADD CONSTRAINT FK_InternetSales_Dates 
FOREIGN KEY(DateKey)
REFERENCES dbo.Dates (DateKey);
GO
```

### database diagram

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4y7T3tq1Mod0WUGCwsmVYaz0q3Jmxt38as6h2lz8zVE48aEdNJYlCcHl58A3NlE1X9EIqk16pqDaPsY2qswT5VaFGbmzheShf1BNRQ7KjIpOhUXjNBG4YbVz0ctEQh7isBEUa3gjeRlo/s0/dw-mydw-database-diagram.png)

# 轉換資料

在倉儲資料庫建立完成之後，我們練習使用 TSQL 進行 ETL 工作。

## Load Your Data Warehouse

下面練習，我們要將 AdventureWorksDW2012.dbo.FactInternetSales 中的資料 ETL 到我們 DW 中的 InternetSales 資料表。
```sql
USE [MyDW]

--------------------------------------------------------
-- Load Customers
--------------------------------------------------------

INSERT INTO dbo.Customers
(
CustomerDwKey, CustomerKey, FullName, 
EmailAddress, 
Birthdate, 
MaritalStatus,
Gender, Education, 
Occupation, 
City, 
StateProvince, 
CountryRegion
)
SELECT 
NEXT VALUE FOR dbo.SeqCustomerDwKey AS CustomerDwKey,
C.CustomerKey,
C.FirstName + ' ' + C.LastName AS FullName,
C.EmailAddress, 
C.BirthDate, 
C.MaritalStatus,
C.Gender, 
C.EnglishEducation, 
C.EnglishOccupation,
G.City, 
G.StateProvinceName, 
G.EnglishCountryRegionName
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G
ON C.GeographyKey = G.GeographyKey;
GO

--------------------------------------------------------
-- Load Products
--------------------------------------------------------
INSERT INTO dbo.Products
(
ProductKey, ProductName, Color,
Size, SubcategoryName, CategoryName
)
SELECT 
P.ProductKey, P.EnglishProductName, P.Color,
P.Size, S.EnglishProductSubcategoryName, C.EnglishProductCategoryName
FROM AdventureWorksDW2012.dbo.DimProduct AS P
INNER JOIN AdventureWorksDW2012.dbo.DimProductSubcategory AS S ON P.ProductSubcategoryKey = S.ProductSubcategoryKey
INNER JOIN AdventureWorksDW2012.dbo.DimProductCategory AS C ON S.ProductCategoryKey = C.ProductCategoryKey;
GO

--------------------------------------------------------
-- Load Dates
--------------------------------------------------------
INSERT INTO dbo.Dates
(
DateKey, FullDate, 
MonthNumberName,
CalendarQuarter, CalendarYear
)
SELECT 
DateKey, FullDateAlternateKey,
SUBSTRING(CONVERT(CHAR(8), FullDateAlternateKey, 112), 5, 2) + ' ' + EnglishMonthName,
CalendarQuarter, CalendarYear
FROM AdventureWorksDW2012.dbo.DimDate;
GO

--------------------------------------------------------
-- Load InternetSales
--------------------------------------------------------
INSERT INTO dbo.InternetSales
(
CustomerDwKey, 
ProductKey, 
DateKey,
OrderQuantity, 
SalesAmount,
UnitPrice, 
DiscountAmount
)
SELECT 
C.CustomerDwKey,
FIS.ProductKey, 
FIS.OrderDateKey,
FIS.OrderQuantity, 
FIS.SalesAmount,
FIS.UnitPrice, 
FIS.DiscountAmount
FROM AdventureWorksDW2012.dbo.FactInternetSales AS FIS
INNER JOIN dbo.Customers AS C
ON FIS.CustomerKey = C.CustomerKey;
GO
```

# 效能調校

## 使用 Data Compression

壓縮前
```sql
EXEC sp_spaceused N'dbo.InternetSales', @updateusage = N'TRUE';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWqXs_gvCtgdVqBlCHCua1jh1j79XxAdFSairUBeV2mMeJqYUdr72KAI6ZwIdOOhQR0jfu0zMrh4ixwhX40qGJTMooGS2xsuclQt32XUgq82xbTerSLD_oF8EZG3K4t9pCVx89TjmOKh8/s0/dw-lab1-01.png)

壓縮後
```sql
ALTER TABLE dbo.InternetSales
REBUILD WITH (DATA_COMPRESSION = PAGE);
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEger3bjUDjpkkAv5Nznh3o7cFA0iGjEBTGyz6pdbQF6DtlESREXIaxlkU0NDbX4Ms5gcK_fO893k1HqnxzLgYRH6ZHyjFo-O7pUisMglqWqWCvuKkj0JZU-9Mp5ntK5rCUuEXcIIktE2kw/s0/dw-lab1-02.png)

## 使用 Columnstore Index

為了提升統計查詢的效能，我們在 InternetSales 建立 Columnstore Index 。
```sql
CREATE COLUMNSTORE INDEX CSI_InternetSales
ON dbo.InternetSales
(
InternetSalesKey, CustomerDwKey, ProductKey, DateKey,
OrderQuantity, SalesAmount,UnitPrice, DiscountAmount
);
GO
```

不過，由於這個測試資料，資料量不夠大，看不出使用 COLUMNSTORE INDEX 的效果，不過，你可以透過 execution plan 檢查看看到它是否有使用 COLUMNSTORE INDEX 進行查詢。

建立 Columnstore Index 前

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVqquxSHq6QYlJcZge0QMfTxZi0D0rbkNw2lyCxRap2gEuynCeKvomZTJFlgcOf7WmkIKfHHYrkal_CUonhBQkPa6541x6AYZVD1iYRo_o572cw7oSF5xI0gG6L7dR1B-q1TUR6wqLjeQ/s850/dw-lab1-03.png)

建立 Columnstore Index 後

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiT8S4dNCqGdA2XI0RSJ3z9Zaf-LXAKmVZ2Go29HrxwMeMKJ7LIA33FxYQWwNPMW1htXKd-0DbrGSQN-xbtGlv9C5fcP1DtxzId6VQk2NlQXqwmgf6M_SP3qkrGgLJXu3p2mum0iHKk4vs/s850/dw-lab1-04.png)

建立 Columnstore Index 後，你也可以用 sp\_spaceused 再檢查看看它使用掉多少空間。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSDQuUAhRM7ScpOKBbLVgkl-DkS1Qt_kFQRSgXm_XFQk-473zq5-4ozi-xqR9GWROoo0JKxifzbhSNSfTxDOXIbGdvPgqtDacWGuyFVGuKIjU-g0X-Yc2isxD2Enyd41AxIJeuGseDMuA/s0/dw-lab1-05.png)

## 使用 Partition Table

在轉換資料時，你可以使用以下技巧來最小化交易記錄。

下面練習，我們要將 AdventureWorksDW2012.dbo.FactInternetSales 中的資料 ETL 到我們 DW 中的 InternetSales 資料表。  而 InternetSales 資料表要滿足以下要求：  

- 依據年度值，將資料儲存在不同的 Partition 。
- 使用 INSERT INTO ，將 FactInternetSales 中 2008 年度以前的資料 ETL 到 InternetSales
- 使用 ALTER TABLE ... SWITCH TO ，將 FactInternetSales 中 2008 年度的資料 ETL 到 InternetSales

### 1. Create a partition function
```sql
CREATE PARTITION FUNCTION PF_SalesYear (smallint)
AS RANGE LEFT FOR VALUES (2001, 2002, 2003, 2004, 2005, 2006, 2007, 2008, 2009);
GO
```

### 2. Create a partition schema
```sql
CREATE PARTITION SCHEME PS_SalesYear
AS PARTITION PF_SalesYear
ALL TO ([PRIMARY]);
GO
```

### 3. Re-Create the InternetSales table

因為我們要加入 partition column ，所以必須重建 InternetSales 資料表。  為了不想刪除先前建立的 InternetSales 資料表，所以我們建立新的 InternetSalesNew 資料表。  
```sql
---------------------------------------------------------
-- re-create InternetSales table
---------------------------------------------------------
CREATE TABLE dbo.InternetSalesNew(
InternetSalesKey	int		 NOT NULL IDENTITY(1,1) ,
PC_SalesYear		smallint NOT NULL ,
CustomerDwKey		int		 NOT NULL,
ProductKey			int		 NOT NULL,
DateKey				int		 NOT NULL,
OrderQuantity		smallint NOT NULL DEFAULT 0,
SalesAmount			money	 NOT NULL DEFAULT 0,
UnitPrice			money	 NOT NULL DEFAULT 0,
DiscountAmount		float	 NOT NULL DEFAULT 0,
CONSTRAINT PK_InternetSalesNew
PRIMARY KEY (InternetSalesKey, PC_SalesYear)
)
ON PS_SalesYear(PC_SalesYear);
GO

---------------------------------------------------------
-- setting FOREIGN KEY contraint
---------------------------------------------------------
ALTER TABLE dbo.InternetSalesNew 
ADD CONSTRAINT FK_InternetSalesNew_Customers 
FOREIGN KEY(CustomerDwKey)
REFERENCES dbo.Customers (CustomerDwKey);
ALTER TABLE dbo.InternetSalesNew 
ADD CONSTRAINT FK_InternetSalesNew_Products 
FOREIGN KEY(ProductKey)
REFERENCES dbo.Products (ProductKey);
ALTER TABLE dbo.InternetSalesNew 
ADD CONSTRAINT FK_InternetSalesNew_Dates 
FOREIGN KEY(DateKey)
REFERENCES dbo.Dates (DateKey);
GO

---------------------------------------------------------
-- setting PAGE DATA_COMPRESSION
---------------------------------------------------------
ALTER TABLE dbo.InternetSalesNew
REBUILD WITH (DATA_COMPRESSION = PAGE);
GO
```

### 4. ETL Data using INSERT INTO

下面例子，我們只轉換 2008 年以前的資料。而且，轉換完成後，同樣的也必須建立 COLUMNSTORE INDEX 。
```sql
---------------------------------------------------------
-- Transfer data to InternetSalesNew table
---------------------------------------------------------
INSERT INTO dbo.InternetSalesNew
(
PC_SalesYear, 
CustomerDwKey,
ProductKey, DateKey,
OrderQuantity, SalesAmount,
UnitPrice, DiscountAmount
)
SELECT
CAST(SUBSTRING(CAST(FIS.OrderDateKey AS CHAR(8)), 1, 4) AS smallint) AS PC_SalesYear,
C.CustomerDwKey,
FIS.ProductKey, FIS.OrderDateKey,
FIS.OrderQuantity, FIS.SalesAmount,
FIS.UnitPrice, FIS.DiscountAmount
FROM AdventureWorksDW2012.dbo.FactInternetSales AS FIS
INNER JOIN dbo.Customers AS C ON FIS.CustomerKey = C.CustomerKey
WHERE
FIS.OrderDateKey < 20080101
GO

---------------------------------------------------------
-- Re-create COLUMNSTORE INDEX
---------------------------------------------------------
CREATE COLUMNSTORE INDEX CSI_InternetSalesNew ON dbo.InternetSalesNew
(
InternetSalesKey, 
PC_SalesYear,
CustomerDwKey, ProductKey, DateKey,
OrderQuantity, SalesAmount,
UnitPrice, DiscountAmount
);
GO
```

你可以使用 [$PARTITION](http://technet.microsoft.com/zh-tw/library/ms188071.aspx) 來取得每一筆資料的儲存分割。

也可以直接由 [sys.partitions](http://msdn.microsoft.com/zh-tw/library/ms175012.aspx) 取得資料表分割的相關資訊。
```sql
---------------------------------------------------------
--統計不同 partition 的筆數
---------------------------------------------------------
SELECT 
$PARTITION.PF_SalesYear(PC_SalesYear) AS PartitionNumber,
COUNT(\*) AS NumberOfRows
FROM 
dbo.InternetSalesNew
GROUP BY 
$PARTITION.PF_SalesYear(PC_SalesYear);

---------------------------------------------------------
--取得資料表的 partition 資訊
---------------------------------------------------------
SELECT index_id, partition_number, rows
FROM sys.partitions 
WHERE object_id = OBJECT_ID('InternetSalesNew') 
ORDER BY index_id, partition_number;

---------------------------------------------------------
--實際統計每個年度的筆數
---------------------------------------------------------
SELECT PC_SalesYear, COUNT(\*) AS NumberOfRows
FROM dbo.InternetSalesNew
GROUP BY PC_SalesYear
GO
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigqT27ysWsJH9Irg1YzRaRvbJpqUQaBHi4NH5qQsWLoaJhnFrQjTB2I4UmL4e4IpxUpidEm9IavV1yq8NBKMaYvQNw1GtS-u8T0HAYAZpo2lKE365W2aIOgCD-nP4m3k0sInDvz4Gz0xg/s0/dw-lab1-06.png)

### 5. ETL Data using ALTER TABLE ... SWITCH TO

當原始資料有新增或者更新時，意謂者你也必須將資料更新到DW中。  要更新前，就必須刪除舊資料，刪除大量的舊資料是很費時的，最快的方法就是使用 TRUNCATE TABLE 。  可是 TRUNCATE TABLE 又只能刪除全部資料，所以我們可以利用一個 staging table 搭配 ALTER TABLE ... SWITCH TO 指令來完成這件事情。  

要建立一個 staging table ，必須和目標資料表的結構一樣。  同時也必須加入一個 Check 條件約束，讓這個 Check 條件約束的限制必須與 partition column 的限制對應。  
```sql
---------------------------------------------------------
-- 1. Create a staging table
---------------------------------------------------------
CREATE TABLE dbo.InternetSalesNew_Staging(
InternetSalesKey	int		 NOT NULL IDENTITY(1,1) ,
PC_SalesYear		smallint NOT NULL CHECK (PC_SalesYear = 2008),
CustomerDwKey		int		 NOT NULL,
ProductKey			int		 NOT NULL,
DateKey				int		 NOT NULL,
OrderQuantity		smallint NOT NULL DEFAULT 0,
SalesAmount			money	 NOT NULL DEFAULT 0,
UnitPrice			money	 NOT NULL DEFAULT 0,
DiscountAmount		float	 NOT NULL DEFAULT 0,
CONSTRAINT PK_InternetSalesNew_Staging
PRIMARY KEY (InternetSalesKey, PC_SalesYear)
);
GO

---------------------------------------------------------
-- 2. Setting FOREIGN KEY contraint
---------------------------------------------------------
ALTER TABLE dbo.InternetSalesNew_Staging 
ADD CONSTRAINT FK_InternetSalesNew_Staging_Customers 
FOREIGN KEY(CustomerDwKey)
REFERENCES dbo.Customers (CustomerDwKey);
ALTER TABLE dbo.InternetSalesNew_Staging 
ADD CONSTRAINT FK_InternetSalesNew_Staging_Products 
FOREIGN KEY(ProductKey)
REFERENCES dbo.Products (ProductKey);
ALTER TABLE dbo.InternetSalesNew_Staging 
ADD CONSTRAINT FK_InternetSalesNew_Staging_Dates 
FOREIGN KEY(DateKey)
REFERENCES dbo.Dates (DateKey);
GO

---------------------------------------------------------
-- 3. Setting PAGE COMPRESSION
---------------------------------------------------------
ALTER TABLE dbo.InternetSalesNew_Staging
REBUILD WITH (DATA_COMPRESSION = PAGE);
GO

---------------------------------------------------------
-- 4. Load the year 2008 to the InternetSalesNew_Staging table.
---------------------------------------------------------
INSERT INTO dbo.InternetSalesNew_Staging
(
PC_SalesYear, 
CustomerDwKey,
ProductKey, DateKey,
OrderQuantity, SalesAmount,
UnitPrice, DiscountAmount
)
SELECT
CAST(SUBSTRING(CAST(FIS.OrderDateKey AS CHAR(8)), 1, 4) AS smallint) AS PC_SalesYear,
C.CustomerDwKey,
FIS.ProductKey, FIS.OrderDateKey,
FIS.OrderQuantity, FIS.SalesAmount,
FIS.UnitPrice, FIS.DiscountAmount
FROM AdventureWorksDW2012.dbo.FactInternetSales AS FIS
INNER JOIN dbo.Customers AS C ON FIS.CustomerKey = C.CustomerKey
WHERE
CAST(SUBSTRING(CAST(FIS.OrderDateKey AS CHAR(8)), 1, 4) AS smallint) = 2008
GO

---------------------------------------------------------
-- 5. Re-create COLUMNSTORE INDEX
---------------------------------------------------------
CREATE COLUMNSTORE INDEX CSI_InternetSalesNew_Staging ON dbo.InternetSalesNew_Staging
(
InternetSalesKey, 
PC_SalesYear,
CustomerDwKey, ProductKey, DateKey,
OrderQuantity, SalesAmount,
UnitPrice, DiscountAmount
);
GO
```

當我們將資料都轉到 Staging table 之後，就可以使用 ALTER TABLE ... SWITCH TO 來執行 partition switching  

Partition Switch 可將資料從原本的 Table 轉移到另一個 Table 。因為這種作業只是變換系統目錄的指標，所以不管資料有多少筆，都是一瞬間就完成的事。  
```sql
---------------------------------------------------------
-- 6. Do the partition switching
---------------------------------------------------------
ALTER TABLE dbo.InternetSalesNew_Staging
SWITCH TO dbo.InternetSalesNew PARTITION 8;
GO
```

最後再檢查每個 partition 筆數。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1VT0D2Z8PA0eY2PA8Q29HGMPjtzX500igIGumOx0gE8B9QnddtbSfjtWWjiM9K_wCSGiN3_4lPk67arrxacB9XqjxAF6IjsxUDZL4lm7pPpFjqim2lUoGIgCFP1IxZL9nihf9GKfmgXo/s0/dw-lab1-07.png)

最後，如果要處理 2009 年資料，你可以依照上面步驟重做，但是要先刪除 InternetSalesNew\_Staging 資料表的 COLUMNSTORE INDEX ，  並且修正 Check 約束條件即可。