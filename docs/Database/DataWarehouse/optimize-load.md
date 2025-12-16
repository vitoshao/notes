---
title: 資料倉儲資料載入
layout: default
parent: Data Warehouse
nav_order: 5
description: "資料倉儲資料載入"
date: 2013-12-16
tags: [SQL,Data Warehouse]
postid: "6522296234231900754"
---
Loading large fact tables can be a problem. You have only a limited time window in which to do the load, so you need to optimize the load operation. In addition, you might be required to track the loads.  

# Using Partitions

Loading even very large fact tables is not a problem if you can perform incremental loads.  However, this means that data in the source should never be updated or deleted; data should be inserted only.  

There is always a possibility that something might go wrong in the source system,   which means that you will have to reload historical data.   This reloading will require you to delete part of the data from your data warehouse.  

Deleting large portions of fact tables might consume too much time, unless you perform TRUNCATE TABLE statement.  However, this command deletes all the data from a table and deleting all the data is usually not acceptable.  More commonly, you need to delete only portions of the data.  

Inserting huge amounts of data could consume too much time as well.   You can do a minimally logged insert, but as you already know, minimally logged inserts have some limitations.  Among other limitations, a table must either be empty, have no indexes, or use a clustered index only on an ever-increasing (or ever-decreasing) key,   so that all inserts occur on one end of the index.  

You can resolve all of these problems by **partitioning a table**.  Please ref : [效能調校-資料表分割](../../70461/C06_Tuning_Performance/tuning-5-partition-table.aspx)

# Data Lineage

Auditing by adding data lineage information for your data loads is quite simple.   You add appropriate columns to your dimensions and/or fact tables, and then you insert or update the values of these columns with each load.   If you are using SSIS as your ETL tool, you can use many of the SSIS system variables to add lineage information to your data flow.  

The following query uses system functions that are very useful for capturing lineage information.

- APP_NAME() ：ApplicationName
- DATABASE_PRINCIPAL_ID ：DatabasePrincipalId
- USER_NAME ：DatabasePrincipalName
- SUSER_ID ：ServerPrincipalId
- SUSER_SID ：ServerPrincipalSID
- SUSER_SNAME ：ServerPrincipalName
- CONNECTIONPROPERTY ：TransportProtocol
- CONNECTIONPROPERTY ：ClientNetAddress
- CURRENT_TIMESTAMP ：CurrentDateTime
- @@ROWCOUNT ：RowsProcessedByLastCommand

```sql
SELECT
APP_NAME() AS ApplicationName,
DATABASE_PRINCIPAL_ID() AS DatabasePrincipalId,
USER_NAME() AS DatabasePrincipalName,
SUSER_ID() AS ServerPrincipalId,
SUSER_SID() AS ServerPrincipalSID,
SUSER_SNAME() AS ServerPrincipalName,
CONNECTIONPROPERTY('net_transport') AS TransportProtocol,
CONNECTIONPROPERTY('client_net_address') AS ClientNetAddress,
CURRENT_TIMESTAMP AS CurrentDateTime,
@@ROWCOUNT AS RowsProcessedByLastCommand;
GO

--APP_NAME() ：Microsoft SQL Server Management Studio - 查詢
--DATABASE_PRINCIPAL_ID ：1
--USER_NAME ：dbo
--SUSER_ID ：259
--SUSER_SID ：0x0105000000000005150000008A6704A261C979CE3F291499F4010000
--SUSER_SNAME ：VITO-2011W7\Administrator
--CONNECTIONPROPERTY('net_transport') ：Shared memory
--CONNECTIONPROPERTY('client_net_address') ：＜local machine＞
--CURRENT_TIMESTAMP ：2013-12-16 11:36:54.223
--@@ROWCOUNT 
```

Besides lineage information, you might also want to log additional information about the whole load,  You can create a custom logging table and insert this information at the start and end of the ETL process.

# 練習

下面練習，我們要將 AdventureWorksDW2012.dbo.FactInternetSales 中的資料 ETL 到我們 DW 中的 InternetSales 資料表。  而 InternetSales 資料表要滿足以下要求：  

- 依據 OrderDateKey 中的年度資料，將 InternetSales 進行  Partition Table 切割。
- InternetSales 資料表必須建立一個  COLUMNSTORE INDEX。
- 使用 INSERT INTO ，將 FactInternetSales 中 2005, 2006, 2007 的資料 ETL 到 InternetSales
- 使用 ALTER TABLE ... SWITCH TO ，將 FactInternetSales 中 2008 的資料 ETL 到 InternetSales

詳情請參考：[Lab2：建立倉儲資料庫](http://vito-note.blogspot.com/2013/12/lab2.html)
## 參考資料  

- 
- 
-