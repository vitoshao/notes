---
title: 資料倉儲簡介
layout: default
parent: Data Warehouse
nav_order: 2
description: "資料倉儲簡介"
date: 2013-12-02
tags: [SQL,Data Warehouse]
postid: "3893904031281805824"
---
#### Terminology

- LOB：line-of-business
- OLTP：online transational processing
- OLAP：online analytical processing
- SSAS：SQL Server Analysis Service
- SSIS：SQL Server Integration Service
- SCD：Slowly Changing Dimension
- CDC：Change Data Capture
- DQS：Data Quality Services
- MDS：Master Data Service
- BISM：Business Intelligence Semantic Model
- DAX：Data Anaysis Expression, The query in the BISM tabular model
- MDX：Mulit-Dimensional Expression, The query for the SSAS dimensional model

#### Tools

- SSIS：SQL Server Integration Services
- BIDS：Business Intelligence Development Studio (support VS2008 , SQL2008)
- SSDT：SQL Server Data Tools (support VS2010)
- DTS：Data Transformation Services (SQL2000)

# 倉儲的資料模型

「資料倉儲」簡單講就是用一種特殊結構的儲存方式，轉換原本正規化的關連式資料庫。  這樣子的儲存方式，可以提供快速的分析能力，以便在龐大的資料中產生有價值的決策。  

更詳盡的說明，可參考：[http://zh.wikipedia.org/wiki/資料倉儲](http://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E5%80%89%E5%84%B2)

## Star & Snowflake Schema

### Star Schema

- There is a single central table, called a **fact table**, surrounded by multiple tables called **dimensions**.
- One **Star schema** covers one business area. In this case, the schema covers Internet sales.
- An enterprise data warehouse covers multiple business areas and consists of multiple Star (and/or Snowflake) schemas.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQFX-noBW196c942WvQ_vrxU1g9Yp8-Y1hYf9dWPl2qFOc1V4TyAgIQrvPQjJJ1rRgGk6n9cMKi8SEQzdASOldMPJt9WR-QJSgAlpudNslwZWQJReWhjVfKZIm-cELdQ4kUziifP8Iktw/s0/dw-star-schema.png)
**小明**於**5/20號**在**微風店**買了一支**手錶**，共花費**２萬元**。

### Snowflake Schema

- 若定義維度時有來自多個資料表的資訊，有時候就會需要較複雜的結構，星狀結構就會演化成雪花式結構。
- In snowflake structure, each dimension is based on attributes from columns in multiple tables linked to each other and ultimately to the fact table by primary key - foreign key relationships.
- 
- If you do not use OLAP cubes and your reports query your data warehouse directly, then using a Star instead of a Snowflake schema might speed up the reports, because your reporting queries involve fewer joins.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDi-PApkBYftASSB93IJdEk971fLzxJQpmodmJJoA-w-zhjRn0PyMzbUWYA_5lmy2yjmFX3NRpZmAmMefBtUhcl4AH5toFjY8W-cQrAhH_PTRkO4V32ZyErgcRPc-T7vBwdsmEd64vU0g/s0/dw-snowflake-schema.png)

## Fact Table & Dimensions

### Fact Table

下圖中位於中央的資料表即稱為 Fact Table

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQFX-noBW196c942WvQ_vrxU1g9Yp8-Y1hYf9dWPl2qFOc1V4TyAgIQrvPQjJJ1rRgGk6n9cMKi8SEQzdASOldMPJt9WR-QJSgAlpudNslwZWQJReWhjVfKZIm-cELdQ4kUziifP8Iktw/s0/dw-star-schema.png)

### Dimensions （維度）

環繞在 Fact Table 周圍的即為 Dimensions

The **fact table** is connected to all the **dimensions** with foreign keys.   Usually, all foreign keys taken together uniquely identify each row in the fact table, and thus collectively form a unique key,   so you can use all the foreign keys as a composite primary key of the fact table. You can also add a simpler key.  

If you were to form a proposition from a row in a fact table, you might express it with a sentence such as,   “Customer A purchased product B on date C in quantity D for amount E.”  This proposition is a fact; this is how the fact table got its name.  

When you search for a problem in sales data, you use a divide-and-conquer technique: slicing the cube over different categories of customers, products, or time.   In other words, you slice the cube over its dimensions.   Therefore, customers, products, and time represent the three dimensions in the conceptual model of the sales cube.   Dimension tables (dimensions) got their name from this conceptual model.

### Conformed Dimension (Shared Dimemsion)

To represent the business correctly, you must be able to connect the multiple Star schemas in your data warehouse.   The connection is simple – you use the same dimensions for each Star schema.   In fact, the dimensions should be shared among multiple Star schemas.  Dimensions with connections to multiple fact tables are called **「shared or conformed dimensions」。**.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzqHchqNcy-kRHcN_y7UHQ3ngPT0BjFTSIEdJ4Dmtarfk32j0Rm3dfqJO5eoU6Ka3L0ncBwKWPfYBBFHReylBEr-piJWjY6I1jY38KtxKYAz5yOWhg02Boi9EDOyPHcmLP_cL2INWTfBo/s0/dw-shared-dimensions.png)

## Audition and Lineage

#### Audition Table

In additional, to audit every update, you should   You can add a auditing table or tables to log   **who** made the update,   **when** it was made,   and **how many rows** were transferred to each dimension and fact table in your DW.  

#### Lineage Column

If you want to know where each row in a dimension or fact table came from and when it was added,   you must add appropriate columns to the dimension and fact tables.  Such detailed auditing information is also called lineage in DW terminology.

## Granularity Level

The number of dimensions connected with a fact table defines the level of granularity of analysis you can get.

For example, if no products dimension is connected to a sales fact table, you cannot get a report at the product level—you could get a report for sales for all products  only.

## SSIS 和 SSAS 有什麼不同

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQJp1KvMqmP82DsalBVrpPEmTNbcc6Rh1R9FjFtDxqn_pggEDzO-Sl4VTs1F9UQ_CHHyNu3dUxXVXs3WEqGjr4T0jBUHZ58T7XVa42cIgqiae8VPiQDquS6sbIXjBWdgoeadT8k9DiCiw/s850/sql-ssis-ssas-ssrs.png)

### SSIS

SSIS 是 SQL 中用來建置 DW 的服務器。  你必須設計好 SSIS package ，並將它部署到 SQL 中，SSIS 服務器就會自動幫你做好 ETL 的工作，

### SSAS

SSAS 是多維度的分析工具，具有 data mining 的能力，它可以透過 OLAP 技術去分析儲存在特定結構中的大量資料。  你可以使用熟悉的工具建立連線以取得後端的資料，如 Excel 或 SharePoint 等應用程式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdpUlxYnemTS7JivLGnw6TuEU01O85svLifhlyCiz411zuFurEK24Y6FH6cJCltSHtUDVFHySpiQjt7mK4U4KOEtyIQTE-YKX87UsXXPPNRTlUpBBXUmhdKvuuSJfSlLeSWxBx1bIV77E/s0/sql-ssis-ssas-ssrs-2.png)

# Designing Dimensions

## 維度資料行的種類（Dimension Column Types）

我們通常可以將維度資料表中的資料行，分成以下幾種類別：  

- **Attributes**：樞紐分析用的欄位
- **Keys columns**：identify entities
- **Name columns**：naming entities
- **Member properties**：用於報表標題的欄位
- **Lineage columns**：稽核或記錄歷程用的欄位，不會給user看的資訊。

### Attributes

維度資料表中的資料是用來提供量測用的資料，而且其中的資料大都是經由轉置（pivot）後的結果。  一個 column 轉置後可能在 dimension 中產生一個或多個 column ，在 DW 或 OLAP 中。  這些轉置後的資料行，通常就是用來分析的資料行，在 DW 和 OLAP 領域中，一般就稱之為「**Attribute**」。  

Discretizing

在設計 Attribute 時，要考量 Discretizing 問題。Discretizing 的意思就是分組，也就是將資料分成不連續的組別。  例如員工資料表中的生日欄位，你可能會依照月份，切出一個欄位.  如果你想切出一個年齡欄位，你就得考量切割的方式。  因為年齡數據是個連續的資料，如果你依照所有的數字分類，那就沒什麼意義。  所以，此時你可以考量以10歲當成一個級距進行分類。

### Keys columns

就像 LOB 資料庫中的資料一樣，你必須要有一個鍵值欄位，可用來識別每一筆資料。

### Name columns

使用鍵值欄位較不具備可讀性，通常在 DW 的維度中，你也會使用到一個或多個命名欄位，該資料較容易閱讀。

### Member properties

有些欄位資料，通常不會用來進行分析，如地址、電話。  但是這類資料又常使用在報表之中，隨然你可以連接到 LOB 取得，但為了方便性與查詢效能，所以這些欄位也會被放在維度資料表中。  

若在進行資料分析時，會使用到不同的語系，你也可以針對 Name Coloumns 和 Member Properties 建立不同的語言欄位。  SSAS 就會根據你提供的資訊自動轉換。

### Lineage columns

有時為了記錄資料的歷程，就會使用到 Lineage 欄位，這欄位資訊通常是不會給user看的。

## Hierarchies

The DimCustomer dimension.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjOr6Ttv8HbhbVaNH0iMfYdkcQ-P1O5hWDNC6XyDeiVcJhyphenhyphen6rU58fX-rOY1ZZ4_E8SrXuXkhQnCoDWDKc7k1rkE78gMGTvzyDptnvoMlnRNBM6Ahfrtg_5Xh5X6VjxN-DOoEOgIHYbHSI4/s0/dw-customer-dimention.png)

In the figure, the following columns are attributes (columns used for pivoting):

- BirthDate (after calculating age and discretizing the age)
- MaritalStatus
- Gender
- YearlyIncome (after discretizing)
- TotalChildren
- NumberChildrenAtHome
- EnglishEducation
- EnglishOccupation
- HouseOwnerFlag
- NumberCarsOwned
- CommuteDistance

All these attributes are unrelated.   Pivoting on MaritalStatus, for example, is unrelated to　pivoting on YearlyIncome.       **None** of these columns have any **functional dependency** between them, and there is       **no** natural **drill-down path** through these attributes  

The DimDate dimension.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEic7rq1Rv05maOThEUUa7aCsBcZwPxZna7neItC8DM7QqjsmDqngGQX6mLxvxBV0C44GBGjl8j-eKDtt2445MtFe4XQOKLRt5U40cb0nUy-lHkxRfA7ovpG_bxADxrdFcMriIR0ghNG6Ig/s0/dw-date-dimention.png)

Some attributes of the DimDate edimension include the following

- FullDateAlternateKey
- EnglishMonthName
- CalendarQuarter
- CalendarYear
- CalendarSemester

You will immediately notice that these attributes are connected. There is a **functional dependency** among them.  

They break third normal form and form a hierarchy.   Hierarchies are particularly useful for pivoting and OLAP analyses  They provide a natural drill-down path. You perform divide-and-conquer analyses through hierarchies.  

Hierarchies have levels. When drilling down path in the DimDate dimension goes through the following levels:   
CalendarYear ➝ CalendarSemester ➝ CalendarQuarter ➝ EnglishMonthName ➝ FullDateAlternateKey.

## Slowly Changing Dimensions (SCD)

The "Slowly Changing Dimension" problem is a common one particular to data warehousing,  it means the data in the dimension varies over time .  This is usually not a problem in an OLTP application; when a piece of data changes, you just update it.   However, in a DW, you have to maintain history. The question that arises is **how to maintain** it.  

There are three different types of SCD.

- 1) Type 1 : The new record replaces the original record. Only one record exist in database – current data.
- 2) Type 2 : A new record is added into the customer dimension table. Two records exist in database – current data and previous history data.
- 3) Type 3 : The original data is modified to include new data. One record exist in database – new information are attached with old information in same row.

# Designing Fact Tables

- Fact tables, like dimensions, have specific types of columns that limit the actions that can be taken with them.
- Queries from a DW aggregate data; depending on the particular type of column, there are some limitations on which aggregate functions you can use.
- Many-to-many relationships in a DW can be implemented differently than in a normalized relational schema.

## Fact Table Column Types

##### 量值（Measures）

- Fact tables are collections of measurements associated with a specific business process.
- The measurements stored in columns is called a 「**measure**」.
- Measures are the essence of a fact table, usually **numeric** and can be **aggregated**.
- They store values that are of interest to the business, such as sales amount, order quantity, and discount amount.

#### Fact Table 的資料行種類

我們通常可以將Fact資料表中的資料行，分成以下幾種類別：  

1. Measures  
在 Fact Table 中的資料，大都是要被用來分析用的資訊，這類資料行就稱為「Measures」。
2. Foreign keys  
關聯到 Dimemdion Table 的欄位。
3. Lineage columns (optional)
4. surrogate key column (optional)   

- This key is shorter and consists of one or two columns only.
- This key is usually the business key from the table that was used as the primary source for the fact table.
- eg.  

Order Head  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEheWKnBv-UgSdd74t012Itw1diRNPyfUwDvpz_keM0NkVSuJTibtCvoigUQBztf2v6QFXwi8g69fS0jZQka5u-4UcWgXTInKVweyaDN7dN703rMhZj2uSnb9qgGiJAdzjD_goaEe5SSooU/s0/ssis-surrogate-key-1.png)

Order Detail  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0WgjqzUHnUs3YCd7birx-ygbnKs96QrOc8aGm7pp1T9IMMnwON3SqCKG3vZj8UCIIliDq0Qtbexs8pe8rXqTQ8ocN4bKSAYByFu6dMVs63rsZZd1qYUi3BmRlBPdZOAHVVOLXzHMTGIE/s0/ssis-surrogate-key-2.png)

The Sales Fact Table  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhyHKq0Dp8rgoKwf9G2DFbzKRJkE809YAVqBqqI1mGYGenDa4kzZmxbDoUbbWRlbAeaZ7yqo-RfksJnSwYzVQ_sz21b_hLl05WGNxEzA5JjeYDfR0yXe6W5PEBOeJLCrXhvFSSDqVdJDTM/s0/ssis-surrogate-key-3.png)

- The Order Details table was the primary source for this fact table.
- The ProductId column is replaced with a surrogate DW ProductKey column.
- The CustomerId and OrderDate columns are replaced with the surrogate DW keys CustomerKey and OrderDateKey.

## Additivity of Measures in SSAS

#### Measures Types

You should consider which aggregate functions you will use when designing a data warehouse.  The additivity of an aggregation function determines how the measure is aggregated across all the dimensions in the cube.   Aggregation functions fall into three levels of additivity:  

- **additive measures**  
An additive measure, also called a fully additive measure, can be aggregated along all the dimensions
- **semi-additive measures**  
A semiadditive measure can be aggregated along some, but not all, dimensions that are included in the measure group that contains the measure.
- **non-additive measures**  
A nonadditive measure cannot be aggregated along any dimension in the measure group that contains the measure. Instead, the measure must be individually calculated for each cell in the cube that represents the measure.

#### Aggregation Functions

By default, measures are summed along each dimension. However, the AggregateFunction property lets you modify this behavior.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEnhMyXQ9FPrjLngVCgpFsLZi-5tEwmjpjWrkIACa5EjUfWAoW_OOtoeK1baZTn-Kjyv2P1RNkXjdCz92kTha6IasjnfY2ybR5due9S-M0aQRrcsBgFCTktd3kvlRrzqJKI1cAk4i9ANk/s0/ssis-additivity-of-measure-in-ssas.png)

## Many-to-Many Relationships in SSAS

在RDB中，若二個資料表存在多對多的關係，通常我們會使用一個中間資料表來描述二者的關連，如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjX_7rnAWl7ZILuH9uTOUXvYKJlnPbv8G30Tr70Jzckn_oOKzzu5QWgxv2sqah66-a3yzc4tLaD1c_Gn15-7oFsKPnOgqYtVP3b9ZSkcBQVziT5vtydOEiMlsjpYyQuoT0LfyDJ9zG3QzY/s0/dw-many-to-many.png)

但是，這在 SSAS 之中是不被支援的，通常你必須在二個 Fact Table 間建立一個**中間維度**(intermediate dimension)。如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOTtf_OpA8auX0M44V9RS-oQ30-3IqcZ1FB7GXMWcSw_mgaHNjjqRawYXFpTkUKHCMXYA9FdrE-ANcDzusw131jdh2iOpgQ5slf3f9TZk7Q0KFL7TGuHrylMoLAYPt5NSOJcyIF62Hncc/s0/dw-many-to-many-2.png)
## 參考資料  

- 
- 
- 
- 
- [多維度模型中的量值和量值群組](http://technet.microsoft.com/zh-tw/library/ms365347.aspx)
- 
-