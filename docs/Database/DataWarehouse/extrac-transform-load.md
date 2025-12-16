---
title: 資料倉儲設計
layout: default
parent: Data Warehouse
nav_order: 3
description: "資料倉儲設計"
date: 2013-12-05
tags: [SQL,Data Warehouse]
postid: "6162385213325902905"
---
建構資料倉儲，就是要完成 [ETL](http://zh.wikipedia.org/wiki/ETL) (extrac-transform-load)。  也就將資料從來源端經過萃取（extract）、轉置（transform）、載入（load）至目的端的過程。  而 SSIS 就是用來協助完成這項作業的工具。  

## Creating a Data Warehouse Database

在建立 DW 資料庫時，可以參考以下幾點建議：

### Using Simple Recovery Mode

A DW contains a transformed copy of line-of-business (LOB) data and it is not online, real-time data.   You do not need to back up the transaction log for your data warehouse, as you would in an LOB database.   Therefore, the recovery model for your data warehouse should be **Simple**.  

### Turn off Autoshrinking and Autogrowing

Numerous small-growth operations and shrinking can fragment the data.  Therefore, you should prevent autoshrinking and autogrowing.  Though you can't prevent the database from growing, you should reserve sufficient space for your data and log files initially to prevent autogrowth.  

### An appropriate size for your log

Although the transaction log does not grow in the Simple recovery model,   you should still set it to be large enough to accommodate the biggest transaction.  

### Using Table Partition

In your data warehouse, large fact tables typically occupy most of the space.   You can optimize querying and managing large fact tables through partitioning.   Table partitioning has management advantages and provides performance benefits.  

### Using Filegroups

For most DW scenarios, having one filegroup for each partition is the most appropriate.  For the number of files in a filegroup, you should consider your disk storage.   Generally, you should create one file per physical disk.  

### Using Staging Tables

You can implement [staging tables](http://blogs.msdn.com/b/managingsql/archive/2008/02/22/the-wizard-s-wand-to-partitioning-creating-staging-tables-adroitly-simple.aspx) in your DW to mitigate the complexity of transfer data from source system.  You use staging tables to temporarily store source data before cleansing it or merging it with data from other sources.   Staging tables are never exposed to end users. You can store them in a different schema and assign deny permission for end users.  The part of a DW containing staging tables is called the **data staging area** (DSA).

## Implementing Dimensions

設計 Dimension 的注意事項：  

### surrogate key

In addition to business keys, you should add a surrogate key to all dimensions that need Type 2　Slowly Changing Dimension (SCD) management.   You should also add a column that flags the current row or two date columns that mark the validity period of a row when you implement Type 2 SCD management for a dimension.  

- You can use simple sequential integers ( [IDENTITY COLUMN](http://msdn.microsoft.com/zh-tw/library/ms186775.aspx) ) for surrogate keys.
- You can also use [sequences object](http://msdn.microsoft.com/zh-tw/library/ff878058.aspx) for identifiers in SQL Server 2012 .

### computed columns

The value of the [computed column](http://technet.microsoft.com/zh-tw/library/ms189292.aspx) is determined by an expression.   It can make you simplify the queries and also help with performance by persist and index a computed column  

### Discretizing

You can use computed columns to discretize continuous values in source columns.   Computed columns are especially useful for column values that are constantly changing.  

### lineage columns

if you need lineage information, you should include lineage columns in your dimensions as well.

## Implementing Fact Tables

You should always implement fact tables after you implement your dimensions.   A fact table is on the "many" side of a relationship witha dimension,   so the parent side must exist if you want to create a foreign key constraint.  

### inferred member

A row in a dimension added during fact table load is called an inferred member.  Except for the key values, all other column values in a dimension are unknown.  

例如你要新增一筆交易，它會關連到某個客戶，若此為新客戶，你可能還沒有建立客戶資料，但是交易又必須即時建立。  此時就要在維度中添加一筆只含客戶索引鍵的替代記錄，直到取得完整的資料來源以執行完整更新為止。  這筆新記錄就稱為「推斷成員 (Inferred Member)」。  

### late-arriving dimensions

[Late arriving dimensions](http://queforum.com/data-warehouse/82-what-late-arriving-dimension-how-handle-data-warehouse.html) are the dimensions where the fact (measurable quantities) table records come early when compared to the dimension table records.

這其實跟 inferred member 指的是相同的事情，也可稱為 early-arriving facts。  當 Fact Table 資料建立時，維度中的該筆推斷成員，除了 key 值外，其他欄位都應該被填入 NULL 值。  

### computed columns and lineage columns

Like dimensions, fact tables can also contain computed columns and lineage columns to simplify querys and enhance performance .

## 參考資料  

- 
- 
-