---
title: 異動資料擷取
layout: default
parent: SSIS
nav_order: 7
description: "異動資料擷取"
date: 2014-01-01
tags: [SQL,SSIS]
postid: "3060811976928664699"
---
#### 什麼是「異動資料擷取」

在 DW 的設定過程中，有些來源資料表會隨著時間不停的在變更，例如訂單資料表。  若要將這些變更定期載入到 DW 中，不可能每次都 Copy 整個來源快照集，因為這樣會耗費較多時間和資源。  所以「遞增載入」（Incremental Load）的需求就此產生，也就是每次執行 ETL 時只會轉移新增或異動的資料。  

在處理這類問題時，你可以會遇到情況：

- 你可以區分出何謂新的資料，所以，你只要轉移新增或異動的資料即可。
- 你無法區分新舊的界線，但是你可以區分特定區間的資料，例如每次轉移特定月或特定天的資料。
- 沒有任何資訊可以讓你區分新舊資料的界線。

本章節將介紹幾種方法來解決「異動資料擷取」的問題。

# 使用動態 SQL 讀取資料

如果你的來源資料可以使用特定的欄位來區分新增的資料，例如 OrderDate 欄位，那麼你就可以在 SSIS 中定義一個 @IncrementalDate 變數，  透過動態 SQL 的方式，來轉移那些比 @IncrementalDate 還要之後的資料。  

在 SSIS 中，如何使用動態 SQL ，會因使用何種來源連接器類型而有所不同。  

## 在 OLEDB Source 中建立動態 SQL

若使用 [OLE DB Source](http://technet.microsoft.com/en-us/library/ms141696.aspx) Adapter ，有二種方式產生動態的 SQL 。

### 1) 在 SQL Command 中套用參數

這個方法是在 SQL Command 中，加入「?」，再將變數值當做參數套用進去。

先定義一個變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjh535HkyM6ZJb9caut9FbCxlJGNAIdzZ76TiqUmMWEOmYLa9S4FmIJIu_IcRqiei-l0gQ2QGUEqgCB95stcq8wJkL-bvOMEtahE6AyYz2KP51BaVjAQ62QQkjF6S33RCpyvObfImmucb8/s0/ssis-dynamic-sql-6.png)

設定 SQL Command Text 中，加入「?」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOMkJPZDt_yK4B8wGlStFez-cIOhop3w_2_3EpecS8U4YwP_l9teQsfoGeWwHSnFUfexXFmYyvLLSoSDlXfVhTR8xjHlF78F3V9DQncbtTWF2QwZXQTugo5lZbToPQ-ebfQsk47TWuwZ4/s0/ssis-dynamic-sql-1.png)

將變數對應到參數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNMiw2_u-9P5d0w99tjTnwtGrYXDlfLCC6sk9w4jQW4CwWMqadRiIoCWPhQVI9QGxu8MRQkDqzUGjIkwqilnOZvjpdFhQSLk0peW_jlXg06x5rcsYye3nknraAOWiCQ8o7Iw71P-qAqjc/s0/ssis-dynamic-sql-2.png)

### 2) 使用變數的 SQL Command

這個方法是整個 SQL Command 都由變數取得，所以你必須先定義一個類似 @SQLString 的字串變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9oSX7d0Cceek66yDjFhIWXlzrltBTL87Idk9GblvqZ5ctoNoRnKuTRXcfEuP2MFTa9zkjtTKSbbA99976bNji23LUGpbtwUcFUKVfuicie-fzRtkCKtsfgPzVQqqgNxpwB0C4KlO-mhA/s0/ssis-dynamic-sql-3.png)

點選上點中的「運算式」按鈕，開啟「運算式產生器」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjUeafAq-FmJcJj3gf6cQvrkhGXO5JpESz5wpabVi2fGFahZBx9usYaoBnmaOWA2jaKzYNkrt4SGjtjxLWmxwp4mufPZl7aCz806acClG710mdI9F1Lqhns3M0EfUPYBdZNbJMcHlkMOFc/s0/ssis-dynamic-sql-4.png)

設定 [OLE DB Source](http://technet.microsoft.com/en-us/library/ms141696.aspx) 的存取模式為「來自變數的 SQL 命令」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIZqKhuQFjw4dOUfJn_Y-EbAKSHQ6_F4BoFUc2nVWWGydxLYceidYQVQAcEFPBrb2QqL54hiW4FDHN8-DXy6vqhoCjeaPy8-CP0sMumqglb_DTzXSH5tmcg2B_RYs2VlImRoxLsSmGhgk/s0/ssis-dynamic-sql-5.png)

### 在 ODBC Source 中建立動態

若是使用 ODBC 或 ADO.NET 資料來源，其動態 SQL 的設定方法必須在 Control Flow 中 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) 的 expression 屬性上。

如果你開啟 [ODBC Source](http://technet.microsoft.com/en-us/library/hh758687.aspx) 的編輯，你會發覺它不像 OLEDB Source 有個「參數」或者「來自變數的SQL命令」，可供設定動態 SQL 的方法。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPt7-cLo01HkJkQF0mzhcZKqREHDgAFdUyCOwundsn4YyDajbh8-AnAKkQ80zZsurjFBbWlZuQon0YL1Ri8UkgUPSEd9fcVpqY3ke4hffgxhfsXnsCwvyXkxYrgM9s-eXQ_B4Vb0kRfoM/s0/ssis-dynamic-sql-7.png)

1. 你可以在 Control Flow 中點一下 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) ，然後開啟屬性視窗中的 expression 屬性。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjyetnLLOIHxqAnJ_FhERd-w6bwbMTRxMJk0Cs56IjpzGhwcS5ZY-TTypA-AQQs8TkA_kizJSY5BJSs85DA0eMeznbW-vA-YHl-OmNmeMgG7Ao2KwuPF1sfEPxa4c4Wac7oAXg9QfP4qx4/s0/ssis-dynamic-sql-8.png)

2. 在 expression editor 中，選擇 [your odbc source].SqlCommand 屬性，並設定運算式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPiVKxaQN6xlxEXtdfBJRR3ut-2LmOe0gAPAonILdUtq2VpLYtbUiYCDKCl63iF3yTX2UMvCP3eg1PjfBg3tCBg4n8oy0NCLEnL1uLXqvOpx9M-_-ynDzZcWqna_jb5_MQmcTFIr6NfTs/s0/ssis-dynamic-sql-9.png)

3. 在 expression builder 中，輸入以下運算式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMu52hf9Fin7lxrAOcTyyURwotaYisFgrjK77eM1rss_Q7VNEB0h4A-mX9Z9kTp7zMoRtKkteEQpgRzGVeRM7pRFdFbGQ89mn9hiyTisUy_Un0FY37PpdtyxdVW_kX0IVMSo09U3MyjAU/s0/ssis-dynamic-sql-10.png)
"SELECT SalesOrderID, OrderDate, CustomerID, TaxAmt, SubTotal 
FROM Sales.SalesOrderHeader
WHERE OrderDate &gt;=  '"  +  (DT_WSTR, 50)(DT_DBTIMESTAMP)  @[User::IncOrderDate] + "'"  
4. 執行

SSIS 會將這個 expression 當做 ODBC 資料來源的 SQL Command 執行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjw5jbUFSO6dzrw_0CYB62YsmqTsPEdN3UY-IDKdH_sChuHi64dIAvpcMk6yr36Wo7nxkRcEqpLpGgYwyrdAtcCYkljMS26L32A_4lRts9EzJHtmhPgcPx-GWO_iY4qT6TOUWlDiGJlwzY/s0/ssis-dynamic-sql-14.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjw-tjtZHYshWgGDPRDzKjkJ3pcqk8obL3ifzuI-2gKboWxVv9KiTQ96OhMSl_EZ_cTEv2p0-mbP322Gy5DHuyT3JERiScQzz7_iacrspDJmGSG5URTb9xTTAY1uHNWFWEFoOEzQzQmytU/s0/ssis-dynamic-sql-11.png)

變更一下 expression 變數中的值，再次執行就可以得到不同的結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFw3bTrXtal7UzTKZ4wOi9AGWk-aRMTTWtNmWPzF4PlwKoP7pKBQFVQfu92TnJD-WTp2DbR-CgJz2qSBHXzudbIT5sf-15UBwrG-i7vSAQMplW7UHyrNxZH19isvxiD-E3F96pzCEtF48/s0/ssis-dynamic-sql-13.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8wM55jr2ZhPETonB1gSVIyLyxGz488T5w8b37b56U-xtQ3RWBfbtH5GaSiLbnUfeI-2BJK4mVMRu8MysFSfTlBeM4puO4SmcL2R-Hw2-d8FEOELTmMOQbvML91j6HrKqT8xIbRxstRoU/s0/ssis-dynamic-sql-12.png)

# 使用 SSIS 的 CDC 功能

上面說明例子中，剛好有個 OrderDate 欄位可用來識別變更的資料，所以可以做到累加式載入。  如果來源資料缺少這樣子的欄位，執行 ETL 時就必須整個資料集載入，或者自行設計「**異動資料擷取（Change Data Capture）**」。  

「異動資料擷取」，簡單講就是當資料發生新增、修改、刪除操作時，將資料記錄下來。  通常可以透過 Trigger 來設計規劃。  但是自行設計的「異動資料擷取」功能，通常不僅繁鎖且耗系統資源。  所幸自 SQL 2008 開始，微軟已在 SQL Server 中加入了這個新功能，可以輕鬆做到「異動資料擷取」。  

## Enabling CDC on the Database

使用「異動資料擷取」功能，必須對資料庫設定啟用 CDC ，還必需針對每個你要擷取的 Table 設定啟用。  同時必須啟用 SQL Server Agent ，擷取處理序才會將交易記錄寫入變更資料表。  

### 啟用 CDC 功能

啟用範例：
```sql
--using MyDW

CREATE ROLE cdc_role;

--設定資料庫啟用 CDC
EXEC sys.sp_cdc_enable_db;

--設定 stg.CDCSalesOrderHeader 啟用 CDC
EXEC sys.sp_cdc_enable_table
@source_schema = N'stg',
@source_name = N'CDCSalesOrderHeader',
@role_name = N'cdc_role',
@supports_net_changes = 1;

--PS.

--資料庫停用 CDC
EXEC sys.sp_cdc_disable_db
GO

--資料表停用 CDC
EXEC sys.sp_cdc_disable_table
@source_schema = N'dbo',
@source_name   = N'MyTable',
@capture_instance = N'dbo_MyTable'
GO
```

### CDC 系統資料表

在啟用 CDC 後，系統會自動建立以下幾個系統資料表：

- [cdc.change_tables](http://technet.microsoft.com/zh-tw/library/bb522506.aspx) ：記錄哪些 tables 啟用 CDC 。
- [cdc.captured_columns](http://technet.microsoft.com/zh-tw/library/bb500243.aspx) ：記錄哪些 columns 啟用 CDC 。
- [cdc.ddl_history](http://technet.microsoft.com/zh-tw/library/bb510681.aspx) ：History of all of the data definition (DDL) changes since CDC enablement.
- [cdc.index_columns](http://technet.microsoft.com/zh-tw/library/bb522474.aspx) ：Indexes associated with CDC tables.
- [cdc.lsn_time_mapping](http://technet.microsoft.com/zh-tw/library/bb510494.aspx) ：Used to map between log sequence number (LSN) commit　values and the time the transaction was committed.
- [cdc.＜CDC_Table＞_CT](http://technet.microsoft.com/zh-tw/library/bb500305.aspx) ：這個資料表是針對啟用CDC的來源資料表，所建立的系統資料表，用來記錄插入和刪除作業的資料。如果是更新作業則記錄兩個資料列(刪除+插入)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgW16mqdmIuVBwKD_dsT7HoYKQPMcARCMYEqA2TBQ2ELZ5CLtWG7NydBAwDb9KCBIWSftoefjvvvGpL2oobbE5VDa3uDkh7lbERpqlmKbEK_7Nr2X7JvBiROjKdgeBJST6BaTx0obSz7jQ/s0/ssis-dynamic-sql-16.png)

### 啟用 SQL Server Agent 作業

在啟用 CDC 時，系統也會在 SQL Server Agent 中建立二個作業，所以要使用 CDC ， SQL Server Agent 也必須要啟動執行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjL6JRzxFrdRbB5egdQzGcbPZln0PGjBUJoyt38Ht2QT3sE4aGQnUzs4fUO8VNsexKOKcJtxTCDTzKkXNtOWI3gXJldjKTwwTRB0KBWAMVnbDGpo6cmBQpGYnTqKtvHn-2casLKjZXJm4g/s0/ssis-dynamic-sql-15.png)

### 取得淨變更項目

完成以上設定後，你可以針對你設定的 CDC 資料表進行變更或新增資料，觀查一下 CDC 功能是如何在 cdc.＜CDC\_Table＞\_CT 資料表記錄變化。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwmPkn-8EAr80ZGEvZipR2VzNvUSfybpwwbWha6EUNjsHkn1uUYD3d1ctQe3_nUXmaPVHI1iH_RmkSdI3eDpnfaELqsRAu-WrKrbtyLdQyZAbVvu1WqNDea6YRQZCqWDYSP9V7TzAV7fg/s850/ssis-dynamic-sql-17.png)

雖然由 cdc.＜CDC\_Table＞\_CT 資料表可以取得變更的記錄，但是你也可以透過以下 CDC 函式取得變更。

- [cdc.fn_cdc_get_all_changes_＜capture_instance＞](http://msdn.microsoft.com/zh-tw/library/bb510627.aspx)：傳回在定義間隔內發生的所有變更。
- [cdc.fn_cdc_get_net_changes_＜capture_instance＞](http://msdn.microsoft.com/zh-tw/library/bb522511.aspx)：傳回在定義間隔內發生的淨變更。
```sql
DECLARE @begin_time datetime, @end_time datetime;
DECLARE @from_lsn binary(10), @to_lsn binary(10);

SET @begin_time = GETDATE() -1;
UPDATE SalesOrderHeader SET TerritoryID = 6 WHERE SalesOrderID = 43659;
UPDATE SalesOrderHeader SET TerritoryID = 7 WHERE SalesOrderID = 43659;
INSERT dbo.SalesOrderHeader values ('75124','2005-7-2','SO75124','29825','279','7',0,0,0)
SET @end_time = GETDATE();

SET @from_lsn = sys.fn_cdc_map_time_to_lsn('smallest greater than or equal', @begin_time);
SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', @end_time);
```

所有變更
```sql
select \* from cdc.fn_cdc_get_all_changes_dbo_SalesOrderHeader (@from_lsn, @to_lsn, 'all')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeB_6IUFRRGsFiYkM2IpHeSUs7Dii6tjq9ntr1Ix_5KEelwZbmvUX-0OX83hTKVN9l6Nh7cCsu9opxzdGKgE8bPL0MLrAC9rULRpheb4bpqsWlkERN8zSUpblffK-uFZD3Ohs8RBOyjAk/s850/ssis-cdc-all-change.png)

淨變更
```sql
select \* from cdc.fn_cdc_get_net_changes_dbo_SalesOrderHeader (@from_lsn, @to_lsn, 'all')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj15EhTSxSlQ6nNgtUXgcZsQaM4gwOygD7B_h-jwjBxtRJPw6g5nRrq-kj-VqDvpI_Azg07wdkcC8kWThUpKJYGJj7H7HNB1YWj_oVr4Bas9M7f8m7h54xLA-eKL9EcFF40ICsmYd3D11Q/s850/ssis-cdc-net-change.png)

## SSIS CDC Components

為了讓 SSIS 在設計 CDC 時可以更加容易，SQL Server 2012 加入了幾個新元件：

- [CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx)
- [CDC Source Adapter](http://technet.microsoft.com/en-us/library/hh758686.aspx)
- [CDC Splitter](http://technet.microsoft.com/zh-tw/library/hh758656.aspx)

### CDC Control Task

「[CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx)」主要用來控制 CDC 封裝的生命週期。  它會管理 CDC 封裝在執行時候的記錄序號 (Log Sequence Number) ，也會維護 CDC 封裝中的狀態變數，並將它保存在資料庫資料表中。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUJwycQ-mOMspd18vRpR4kZpiP81Si1Afu4n_9OG-IsT4sbeRtd7kzPKltla2ZE4izzZX7wLJetPMdLVN5Up20gnxRjExnqReuU06siB_SWJafF8sAkCseJjNTltmlaPwf9ZG2BnZtLaQ/s0/ssis-dynamic-sql-18.png)

#### CDC 控制作業

- 標示初始載入開頭（Mark Initail Load Start）： 初始載入封裝**開始讀取資料**之前。
- 標示初始載入結尾（Mark Initail Load End）：初始載入封裝**結束讀取資料**之後。
- 標記 CDC 開始（Mark CDC start）：
- 取得處理範圍（Get processing range）：遞增載入封裝**開始讀取資料**之前
- 標示已處理的範圍（Mark processed range）：遞增載入封裝**結束讀取資料**之後
- 重設 CDC 狀態（Reset CDC state）：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKLXCgWiQ2xGC1krpAMujAVW6nkOdCid5BsZ3prHfVxlUtL5qE9fBdy1YTYLsDJ25BSDl_9mhsm5ZYXFCf8wlr5cmmqJCa3R82WzxjYrzz0Xp8SWQsjZxWFjA775sJTa-AIAZR8igVEC8/s0/ssis-cdc-control-operations.png)

### CDC Source Adapter

[CDC Source](http://technet.microsoft.com/en-us/library/hh758686.aspx)會從 SQL Server 2012 變更資料表中讀取變更資料的範圍，並將這些變更向下游傳遞至其他 SSIS 元件。

#### CDC 處理模式

使用 [CDC Source Adapter](http://technet.microsoft.com/en-us/library/hh758686.aspx) 時，它提供 5 種取得變更資料的方式：

- **全部**（All）：傳回目前 CDC 範圍中的變更，不含舊值（更新之前的值）。
- **全部（含舊值）**（All with old values）：傳回目前 CDC 處理範圍中的變更，包括舊值（更新之前的值）。每個更新作業都有兩個資料列：一個包含更新之前的值，另一個則包含更新之後的值。
- **淨值**（Net）：針對目前 CDC 範圍中的變更，每個資料列只傳回一筆結合變更後的資料列。
- **淨值（含更新遮罩）**（Net with update mask）：這種模式與一般的淨值模式很相似，但是它還加入了名稱模式為 \_\_$＜column-name＞\_\_Changed 的布林資料行，表示目前變更資料列中的變更資料行。
- **淨值（含合併）**（Net with merge）：這種模式與一般的淨模式很相似，但是插入和更新作業會合併成單一合併作業 (UPSERT)。

### CDC Splitter

[CDC Splitter](http://technet.microsoft.com/zh-tw/library/hh758656.aspx) 是用來將來自於 [CDC Source](http://technet.microsoft.com/en-us/library/hh758686.aspx) 中的資料，依插入、刪除、更新等作業類型，分割成三個獨立的輸出。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhniFpnXIsFXHgbq4NIUYln24Qu-2EPFksPMiEevoc-e61gpvbijfMTUNxZ8SuUFLQ-58MULSKjKFvcjQh05n_nhj-fVelFnaz_-tfNO1vWSIMey06f5Ff1o_JmTiDuKE_cR750J2fWt08/s0/ssis-cdc-splitter.png)

# ETL Strategy for Incrementally Loading Fact Tables

The following lists some general guidelines for loading fact tables:

- Partition your fact tables.
- Incremental data should be on one partition, so that you can easily remove this data without a delete operation by using partition switching.
- Use this loading strategy:
- Load incremental data to a table that has the same structure as the destination fact table, without compression or indexes.
- Apply the necessary indexes and compression.
- Switch the loaded table with the partition in the destination fact table.
- Use fully cached lookups to get appropriate surrogate keys.
## 參考資料  

- [異動資料擷取的概觀](http://technet.microsoft.com/zh-tw/library/cc627397.aspx)
- [異動資料擷取 (SSIS)](http://technet.microsoft.com/zh-tw/library/bb895315%28v=sql.120%29.aspx)
- 
-