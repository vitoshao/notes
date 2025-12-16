---
title: Lab8：異動資料擷取
layout: default
parent: Labs
nav_order: 8
description: "Lab8：異動資料擷取"
date: 2014-01-02
tags: [SQL,Labs]
postid: "4404464797770412310"
---
設計「異動資料擷取」時，通常會將封裝分成二部份：「初始載入」和「遞增載入」封裝。

「**初始載入**」封裝通常只會執行一次，  它和一般的載入封裝沒什麼差別，只不過在 Control Flow 中的最開始和結尾處，各加入一個 [CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx) ，用以記錄相關 CDC 狀態。  而資料流程的內容則和一般的 ETL 流程沒什麼不同。  

「**遞增載入**」封裝，也是在 Control Flow 中的最開始和結尾處，各加入一個 [CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx) ，用以記錄相關 CDC 狀態。  不過，在資料流程中，必須透過 [CDC Source](http://technet.microsoft.com/en-us/library/hh758686.aspx) 來讀取 CDC 資料，  並利用 [CDC Splitter](http://technet.microsoft.com/zh-tw/library/hh758656.aspx) 將新增、修改、刪除的資料分割開來處理。  

# 一、建立「初始載入」封裝

之前的 Lab 都是 以AdvancetureWork2012 為資料來源，為了便於觀察與練習，這個 Lab 另外建立一個資料庫來操作。

我們將下面的 CDC\_DB 假設成 LOB 資料庫，而 MyDW 則是 DW 資料。  我們將練習如何使用 SSIS 的「異動資料擷取」功能，達到「遞增載入」的目的。  

## 建立測試資料庫

### Source DB
```sql
--------------------------------------------------------------------
--建立測試資料庫
--------------------------------------------------------------------
CREATE DATABASE CDC_DB
ON PRIMARY
(NAME = N'CDC_DB', FILENAME = N'D:\Database\testdb\CDC_DB.mdf',　SIZE = 307200KB , FILEGROWTH = 10240KB )
LOG ON
(NAME = N'CDC_DB_log', FILENAME = N'D:\Database\testdb\CDC_DB_log.ldf',　SIZE = 51200KB , FILEGROWTH = 10%);
GO
```
```sql
--------------------------------------------------------------------
--建立測試資料
--------------------------------------------------------------------
USE CDC_DB;
GO

CREATE TABLE SalesOrderHeader
(
SalesOrderID INT NOT NULL PRIMARY KEY,
OrderDate DATETIME NULL,
SalesOrderNumber NVARCHAR(50),
CustomerID INT NULL,
SalesPersonID INT NULL,
TerritoryID INT NULL,
SubTotal money NULL,
TaxAmt money NULL,
Freight money NULL
);
GO

INSERT INTO SalesOrderHeader (
SalesOrderID, OrderDate, SalesOrderNumber, CustomerID,
SalesPersonID, TerritoryID, SubTotal, TaxAmt, Freight
)
SELECT
SalesOrderID, OrderDate, SalesOrderNumber, CustomerID,
SalesPersonID, TerritoryID, SubTotal, TaxAmt, Freight
FROM AdventureWorks2012.Sales.SalesOrderHeader;
```
```sql
--------------------------------------------------------------------
--啟用 CDC
--------------------------------------------------------------------
CREATE ROLE cdc_role;

--設定資料庫啟用 CDC
EXEC sys.sp_cdc_enable_db;

--設定 stg.CDCSalesOrderHeader 啟用 CDC
EXEC sys.sp_cdc_enable_table
@source_schema = N'dbo',
@source_name = N'SalesOrderHeader',
@role_name = N'cdc_role',
@supports_net_changes = 1;
```

### Data WareHouse DB
```sql
USE MyDW;
GO

CREATE TABLE SalesOrderHeader
(
SalesOrderID INT NULL,
OrderDate DATETIME NULL,
SalesOrderNumber NVARCHAR(50),
CustomerID INT NULL,
SalesPersonID INT NULL,
TerritoryID INT NULL,
SubTotal money NULL,
TaxAmt money NULL,
Freight money NULL
);
GO
```

## 「初始載入」封裝的 Control Flow 設計

「初始載入」封裝通常只會執行一次，  它和一般的載入封裝沒什麼差別，只不過在 Control Flow 中的最開始和結尾處，各加入一個 [CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx) ，用以記錄相關 CDC 狀態。  而資料流程的內容則和一般的 ETL 流程沒什麼不同。  

### 1. 建立控制流程

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZNgUFiyI_YKlYTJh3GzArcSaH6DgYiVRC0lBBFwqoyJSaFJD8ULMgjBbrF2uJvI-3WxESX3K6JSiJYSSxG29tpLKv2c_KORysbUTwwFhyphenhyphenBuqYa5nyJlBkRP4FggdwrH-KWrq2jMBw36I/s0/lab8-cdc-01.png)

### 2. 設定「啟始CDC控制工作」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6MSrP5sXlXqWfX7J1ueE_CS6Oq-L1g9Pnvon0H5xtSF4L35Y7N1eKDACQNRYpPp2ESiz2kZZIIZH44DYynSAp1sfu3yU21oEBuc1AVVuPGiF0PMTORtnz4pr5STG_GAma_ksUMTnknpU/s0/lab8-cdc-02.png)

2.1 設定 ADO.NET 連接，連接到 CDC 資料來源。

2.2 將CDC控制作業設為「標示初始載入開頭」。（Mark Initail Load Start）

2.3 新增一個存取 CDC 狀態的變數。

2.4 設定 CDC 狀態的儲存資料庫的「連接」

2.5 設定 CDC 狀態的儲存資料庫的「資料表」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDi1lQ0dXeJiiuKdjap5DonLkHo1E3bAwy1u01kxUmFJmtAO_TE89Ialz_DU70fA8-yINQ7g_DasZ3gVvgMrV2xk5pLKu3yBzTYgh0jBLty4m9J2fsBd7FdzpaG5XZ9i04QxlMsD3aG-M/s0/lab8-cdc-03.png)

若是首次設定 CDC 控制工作，你可以按右邊的「建立」按鈕，會跳出現成的 TSQL 來建立這個儲存狀態的資料表
```sql
CREATE TABLE [dbo].[cdc_states] 
([name] [nvarchar](256) NOT NULL, 
[state] [nvarchar](256) NOT NULL) ON [PRIMARY]
GO
CREATE UNIQUE NONCLUSTERED INDEX [cdc_states_name] ON 
[dbo].[cdc_states] 
( [name] ASC ) 
WITH (PAD_INDEX  = OFF) ON [PRIMARY]
GO
```

2.6 設定 CDC 狀態的儲存資料庫的「狀態名稱」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUJwycQ-mOMspd18vRpR4kZpiP81Si1Afu4n_9OG-IsT4sbeRtd7kzPKltla2ZE4izzZX7wLJetPMdLVN5Up20gnxRjExnqReuU06siB_SWJafF8sAkCseJjNTltmlaPwf9ZG2BnZtLaQ/s0/ssis-dynamic-sql-18.png)

### 3. 設定「結尾CDC控制工作」

「結尾CDC控制工作」設定同「啟始CDC控制工作」，差別只在於將CDC控制作業設為「標示初始載入結尾」。（Mark Initail Load End）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmKVjUHR7EjjgVOTy59u5sBgiCS1x9PQNhCKjtMPfYNwkzgKefl1tKl44bm4_QQqbkno1YC3QWZlOOhAMsEJ3mIEyGQTJXt6VFCgGcRT1JI9NQ5rHU_iXUtKbEGAllJo3kvWaWi8NNZms/s0/lab8-cdc-04.png)

## Data Flow

在 Data Flow 中沒什麼特別的，就是將 CDC\_DB 中的 dbo.SalesOrderHeader 轉移至 MyDW 中的 stg.SalesOrderHeader 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHJ9GYXXWJ7BrBRdDvf_2_UhUBgTLYVo7eLCiJySAGzcNk_aZxI1U3mahusVOBdOvDiZGPT81be_q47wJtjKOpya_Or_JI2TkvnwA_UW05jdfpPgWAiwe6ulJ-sn80BEN0AqGHY2YSn2g/s0/lab8-cdc-05.png)

## 執行封裝

這個封裝執行後，在 CDC\_DB 中的 dbo.SalesOrderHeader 所有資料都會轉移至 MyDW 中的 stg.SalesOrderHeader 。  並且 CDC state 也會被標示為 Initial ，並記錄在 dbo.cdc\_states 資料表中。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUJwycQ-mOMspd18vRpR4kZpiP81Si1Afu4n_9OG-IsT4sbeRtd7kzPKltla2ZE4izzZX7wLJetPMdLVN5Up20gnxRjExnqReuU06siB_SWJafF8sAkCseJjNTltmlaPwf9ZG2BnZtLaQ/s0/ssis-dynamic-sql-18.png)

# 二、建立「遞增載入」封裝

接下來要設計「遞增載入」封裝。我們將利用到 SSIS 中的 CDC 元件（[CDC Splitter](http://technet.microsoft.com/zh-tw/library/hh758656.aspx)、[CDC Source](http://technet.microsoft.com/en-us/library/hh758686.aspx)），  來幫助我們自動擷取變更的資料，然後再載入到倉儲資料庫中。  

## 建立刪除和修改資料的中繼資料表

[CDC Splitter](http://technet.microsoft.com/zh-tw/library/hh758656.aspx) 元件會將 CDC 來源資料，自動分割成三類：新增、修改、刪除。  若是新增的資料，就直接送到目標資料表；若是修改或刪除的資料，我們先將它輸出到中繼續資料表，再利用 Set-based 方法來進行更新，以增進執行效能。  所以在此，我們先建立以下二個中繼資料表。  
```sql
CREATE TABLE stg.tmpUpdateSalesOrderHeader
(
SalesOrderID INT NULL,
OrderDate DATETIME NULL,
SalesOrderNumber NVARCHAR(50) NULL,
CustomerID INT NULL,
SalesPersonID INT NULL,
TerritoryID INT NULL,
SubTotal money NULL,
TaxAmt money NULL,
Freight money NULL
);
GO
CREATE TABLE stg.tmpDeleteSalesOrderHeader
(
SalesOrderID INT NULL,
OrderDate DATETIME NULL,
SalesOrderNumber NVARCHAR(50)NULL,
CustomerID INT NULL,
SalesPersonID INT NULL,
TerritoryID INT NULL,
SubTotal money NULL,
TaxAmt money NULL,
Freight money NULL
);
GO
```

## Control Flow

1. 建立控制流程

在控制流程中，頭尾一樣各罷放一個 CDC 控制工作，用來管理 CDC 狀態。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnB0li1CdnQ63OmNsaE3-WMDk06U94j5jh4jZ_60s2zAztDSCyj2xHUhAlneoqhw612ajLaDQ97ISWVuadwGfqEwh7mgbJ31BgBKImXAaozVg5n2xf6ZdBvepEkUayDEyjnlDoGqXJueQ/s0/lab8-cdc-06.png)

2. 設定「啟始CDC控制工作」

此處的 CDC 控制作業要設定為「取得處理中的範圍」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqiky4tkaVgn7fAsSXN7b4kkhRLkBFLQXAXqULoTOK9x7TRygvfnb7F7VxVKFhV526TH-gPNvg0IdY-DfEFW7JLa0kMS3VE8F22S6Si0gmpLTnRJYf2Q1WLJkb_eunWdkw-IQFwhZJmzU/s0/lab8-cdc-07.png)

3. 設定「更新」SQL工作
```sql
UPDATE S
SET
OrderDate = U.OrderDate,
SalesOrderNumber = U.SalesOrderNumber,
CustomerID = U.CustomerID,
SalesPersonID = U.SalesPersonID,
TerritoryID = U.TerritoryID,
SubTotal = U.SubTotal,
TaxAmt = U.TaxAmt,
Freight = U.Freight
FROM stg.SalesOrderHeader AS S
INNER JOIN stg.tmpUpdateSalesOrderHeader AS U ON S.SalesOrderID = U.SalesOrderID;
```

4. 設定「刪除」SQL工作
```sql
DELETE stg.SalesOrderHeader
WHERE SalesOrderID IN 
(SELECT SalesOrderID FROM stg.tmpDeleteSalesOrderHeader);
```

5. 設定「結尾CDC控制工作」

此處的 CDC 控制作業要設定為「標示已處理的範圍」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitSxh5X4Q2eU4pmKdFwKYCU09aO9fCrvhcrI1fl_nCb07UNhvmoFWHqY7ZpTBSy18hEQ41ZMRgs3bwEKRQH4o4Ph3IIXtaK9ZV45jpsxfDxkiHp9M8TvRNXp1cpf4hPR3c8sXDGlqEyNk/s0/lab8-cdc-08.png)

## Data Flow

1. 加入「CDC 來源」和「CDC 分隔器」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdyXOeQ9TvJeOPIAEdfL8uvmsdAVsCQN-x6wq0w9lu2S6A8oPy-vztbLmzFmyOgGSFd-39cKzXZidYv_jT5kNIBO2nsFlEbu_dPrcD5hkCg5eOn9ueZW8YWW2pqqn1lNwLLOBnXY57rCk/s0/lab8-cdc-11.png)

2. 設定「CDC 來源」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEwHnM7w_fSFo2h5ZR1YkTVn1Pc4Hj4C0WolJMngAU64QS-qCpFg5eOH8i0KpnfvHpoCfkTEvIrI0bECA6y7hz08cgjF13XqQp2SBfzc9hs7i34TeUOiYzZ_kX1g2nSj490Qlloah3wu4/s0/lab8-cdc-10.png)

3. 設定「CDC 分隔器」。

CDC 分隔器會將 CDC 來源資料流程中變更資料列的單一流程分割為插入、更新和刪除作業的不同資料流程。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCDN46i87lXFnz8c9aMGevKYKR9YS56mhbX_NYUSfAdLBZrDhJTwipyUzu2hDXxVdxSY267SWf82eQ8ksQK5KvUT6T0z1JrgMiIK49sm2-Hl4ZGLoqau-X1Xrd-JfBY1BZLNwyxhDTquc/s0/lab8-cdc-09.png)

## 執行封裝

這個封裝執行時，每次只會將 CDC\_DB.dbo.SalesOrderHeader 上的變更部份，轉移至 MyDW 中的 stg.SalesOrderHeader 。  
```sql
UPDATE SalesOrderHeader SET TerritoryID = 6 WHERE SalesOrderID = 43659;
UPDATE SalesOrderHeader SET TerritoryID = 7 WHERE SalesOrderID = 43659;
INSERT dbo.SalesOrderHeader values ('75124','2005-7-2','SO75124','29825','279','7',0,0,0)
INSERT dbo.SalesOrderHeader values ('75125','2005-7-3','SO75124','29825','279','2',0,0,0)
DELETE dbo.SalesOrderHeader where SalesOrderID = 75123
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5xnU73eXfLDqFpjoNh7zas08YSf60IOmnNn6UI1IY_tVr5IYd29R2QycnT3G0aEswf3Aun5TMw-15fIQRoBQfXp02xtpH3aPyFKtYI2dn7SpNCrl3PmuwyBSrrN6qmkVG9RQIOAHh6TM/s0/lab8-cdc-12.png)
```sql
UPDATE SalesOrderHeader SET TerritoryID = 4 WHERE SalesOrderID = 43659;
DELETE dbo.SalesOrderHeader where SalesOrderID = 75124
DELETE dbo.SalesOrderHeader where SalesOrderID = 75125
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQRM341_VktbzDXBESLXYG0iN6i0vdQao74-iD3RyZ27BQXnkrU6fW0i-ZjcxS5dKoyqaVLHcFD1tlNZgIiFSyYmlt0BlPMy5xiLDLuG5yutQDpcGOipi6XXg67qW8wy3hyaEuSOW4NQA/s0/lab8-cdc-13.png)