---
title: Lab14：資料比對
layout: default
parent: Labs
nav_order: 14
description: "Lab14：資料比對"
date: 2014-02-19
tags: [SQL,Labs]
postid: "2498531106531251685"
---
這個 Lab 將練習如何使用 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) ，以及如何使用 **DQS 比對原則知識庫**進行重複資料的清除。  

# 建立測試資料

首先我們要為 identity mapping 和 de-duplication 建立一個測試資料。這份資料包含二個版本：淨版和髒版。  

### Create CustomersClean

我們由 AdventureWorksDW2012.dbo.DimCustomer 中取得一份淨版的測試資料。
```sql
--create table
CREATE TABLE dbo.CustomersClean
(
CustomerKey		INT NOT NULL PRIMARY KEY,
FullName		NVARCHAR(200) NULL,
StreetAddress	NVARCHAR(200) NULL
);

--populate stage data
INSERT INTO dbo.CustomersClean(CustomerKey, FullName, StreetAddress)
SELECT 
CustomerKey,
FirstName + ' ' + LastName AS FullName,
AddressLine1 AS StreetAddress
FROM AdventureWorksDW2012.dbo.DimCustomer
WHERE CustomerKey % 10 = 0;
```

### Create CustomersDirty

另外也建立一個髒版的測試資料，它與淨版的測試資料，只是多個二個欄位，且欄位值暫時都是空的。

- **Updated** field will be used by the query that makes the data dirty.
- **CleanCustomerKey** field will be used with the customer key from the clean table after identity mapping.
```sql
--create table
CREATE TABLE dbo.CustomersDirty
(
CustomerKey		 INT NOT NULL PRIMARY KEY,
FullName		 NVARCHAR(200) NULL,
StreetAddress	 NVARCHAR(200) NULL,
Updated			 INT NULL,
CleanCustomerKey INT NULL
);

--populate stage data
INSERT INTO dbo.CustomersDirty(CustomerKey, FullName, StreetAddress, Updated)
SELECT 
CustomerKey \* (-1) AS CustomerKey,
FirstName + ' ' + LastName AS FullName,
AddressLine1 AS StreetAddress,
0 AS Updated
FROM AdventureWorksDW2012.dbo.DimCustomer
WHERE CustomerKey % 10 = 0;
```

CustomersClean：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEjLSX8v9NV60Zz5-T2zPnXwmYXd9KC35SrI3mZwN7EVTJlwIlx8rb7QAR_eQFYmbiN8IJ2XzFwY-NdIIqgvKNodjnARzvylgis4MrRsIRoJJ9o6bfdXDWjdEhegnD-Axhjfb3c0h7n_E/s0/ssis-lab14-matching-10.png)

CustomersDirty：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhekyshi-332rgrubxWV4Eib11M5VIG3Vs7BAiMONldzDbjJHfy-nP5e-9NRyhatyxOkjSOtXPuHcmpnOePRzk1_UpXbG74o-ALinhW7zcRadKqZqbgv39dHUuiZ5kN04qudLJXNpuzrtg/s0/ssis-lab14-matching-11.png)

### Make the data dirty

使用以下 TSQL 語法去修改 CustomersDirty 中的資料，這些程式碼只會隨機且輕微的變更資料。  目的是要模擬真實世界中，資料彼此間的小小差異。  
```sql
DECLARE @i AS INT = 0, @j AS INT = 0;

WHILE (@i < 3) -- loop more times for more changes
BEGIN
SET @i += 1;
SET @j = @i - 2; -- control here in which step you want to update

-- only already updated rows

WITH RandomNumbersCTE AS
(
SELECT CustomerKey
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber1
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber2
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber3
,FullName
,StreetAddress
,Updated
FROM dbo.CustomersDirty
)
UPDATE RandomNumbersCTE SET
FullName =	STUFF(FullName,	CAST(CEILING(RandomNumber1 \* LEN(FullName)) AS INT), 1, CHAR(CEILING(RandomNumber2 \* 26) + 96)),
StreetAddress = STUFF(StreetAddress, CAST(CEILING(RandomNumber1 \* LEN(StreetAddress)) AS INT), 2, ''),
Updated = Updated + 1
WHERE RAND(CHECKSUM(NEWID()) % 1000000000 - CustomerKey) < 0.17 AND Updated > @j;

WITH RandomNumbersCTE AS
(
SELECT CustomerKey
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber1
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber2
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber3
,FullName
,StreetAddress
,Updated
FROM dbo.CustomersDirty
)
UPDATE RandomNumbersCTE SET
FullName =STUFF(FullName, CAST(CEILING(RandomNumber1 \* LEN(FullName)) AS INT), 0, CHAR(CEILING(RandomNumber2 \* 26) + 96)),
StreetAddress = STUFF(StreetAddress, CAST(CEILING(RandomNumber1 \* LEN(StreetAddress)) AS INT), 2, CHAR(CEILING(RandomNumber2 \* 26) + 96) + CHAR(CEILING(RandomNumber3 \* 26) + 96)),
Updated = Updated + 1
WHERE RAND(CHECKSUM(NEWID()) % 1000000000 - CustomerKey) < 0.17 AND Updated > @j;

WITH RandomNumbersCTE AS
(
SELECT CustomerKey
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber1
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber2
,RAND(CHECKSUM(NEWID()) % 1000000000 + CustomerKey) AS RandomNumber3
,FullName
,StreetAddress
,Updated
FROM dbo.CustomersDirty
)
UPDATE RandomNumbersCTE SET
FullName =STUFF(FullName, CAST(CEILING(RandomNumber1 \* LEN(FullName)) AS INT), 1, ''),
StreetAddress = STUFF(StreetAddress, CAST(CEILING(RandomNumber1 \* LEN(StreetAddress)) AS INT), 0, CHAR(CEILING(RandomNumber2 \* 26) + 96) + CHAR(CEILING(RandomNumber3 \* 26) + 96)),
Updated = Updated + 1
WHERE RAND(CHECKSUM(NEWID()) % 1000000000 - CustomerKey) < 0.16 AND Updated > @j;

END;
```

經由以上的算法，將隨機更新 FullName 或 StreetAddress 欄位，且每更新一次， Updated 欄位值會加１.  

以下二圖是隨機選取３筆記錄，分別是更新前與更新後的比較：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEjLSX8v9NV60Zz5-T2zPnXwmYXd9KC35SrI3mZwN7EVTJlwIlx8rb7QAR_eQFYmbiN8IJ2XzFwY-NdIIqgvKNodjnARzvylgis4MrRsIRoJJ9o6bfdXDWjdEhegnD-Axhjfb3c0h7n_E/s0/ssis-lab14-matching-10.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEir_IZImaEGA7x6Ldu7OdAr33Jwog7ONKpjZdkCL8LpuLx5A9cABusVh9wkZ1TnZN5xaE7Opz7yrXfbhw7bkqIyzlP4q1K61lrwUjDxJJhwSeLLzt9QguqabrCrpOhWraQXyI_Ig0TCtYQ/s0/ssis-lab14-matching-12.png)

### Check the dirty data

你可以使用以下 TSQL 檢查 CustomersDirty 中的資料。
```sql
SELECT  1.0 \* (SELECT Count(1) FROM CustomersDirty WHERE Updated>0) / (SELECT Count(1) FROM CustomersDirty) 

SELECT Top 10 C.CustomerKey, C.FullName ,D.FullName, C.StreetAddress, D.StreetAddress, D.Updated, CleanCustomerKey
FROM dbo.CustomersClean AS C
INNER JOIN dbo.CustomersDirty AS D ON C.CustomerKey = D.CustomerKey \* (-1)
```

你會發覺，在 CustomersDirty 資料表中，總共大約有 40% 的資料被更改過。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9r2lvYnsPyB4c7P2tp3lH5vVEuUu_nz8TIGWMjIeF3JfuGnbX7tVtcw2hvuGnuA4pdGiWz-JELGEB-ORcl39M5m0MEDz_jAt-aI_Fyx1MB0wX1mJcKaJ76qdqrPOrUDPr7uKTjEOn8Tg/s0/ssis-lab14-matching-01.png)

### Setting a row to be corrected

我們額外更新一筆特定的資料，這筆資料列將在後面的練習中，使用 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 來修正。  
```sql
UPDATE dbo.CustomersDirty
SET 
FullName = N'Jacquelyn Suarez', 
StreetAddress = N'7800 Corrinne Ct.',
Updated=1
WHERE CustomerKey = -11010;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqBJMS0BSS2Ve0as7tFWpa7vlXmSQccft7kVYaGiheFnRwdSi3JYb0eNbN4dh8kTHBIAjsnxqw6yeQ0fTsfvnxuAropR2-nZo1PQwF1SFhf8rypj57VuWyzhD4v3LwjJNcxfwqltxNpIg/s0/ssis-lab14-matching-02.png)

# Use the DQS Cleansing Transformation

在進行比對前我們必須對資料先進行清理，這樣子才能提升比對的品質。本節將使用 SSIS 封裝，透過 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 來清理資料。  

## 建立封裝，執行資料清理

我們將在這個封裝中，先使用 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 對 CustomerDirty 執行資料清理，以便產生一正較為正確的清單。  再將清理結果透過 Lookup Transfermation 與 CustomerClean 做明確比對。  

清理的意思是說，只要符合知識庫中的原則，仍然視為相同的資料，以免被判定成不相同資料。  例如我們在知識庫中的 StreetAddress 定義域，已經設定了一個「以詞彙為主的關聯」，會將「Ct.」轉換成「Court」。  也就是說 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 會先將來源資料依據知識庫原則轉換後再輸出。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjyKoJ59w5o0rM3ZFIpS91n7JaSLHaZTzn29Q5B8MvQAzPqkI_Uyd3J4SmeiNQcNyDsU67632IWbQC0G5lDtdxrAbz4s4bH0PxObXY9oBUl1wm_shCfNn73HYAidhThWRE7bX-OXb0hb0E/s0/ssis-lab14-matching-04.png)

### 新增專案與設定連接管理員

Drag a [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) .

New an OLEDB Connection, configure the connection to DQS\_STAGING\_DATA .

New an DQS Connection, configure the connection to Data Quqlity Server .

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoSg1OkF159tiPqoXmaola8aikpbcLU79Arkaef4bx4eeMQ8OCqS_HBp7tlhmb53VzyAF50f_D55Ir46NZEaMGJs81fgZBbmF1KLUuqoKSmucvCOYPnJLV9FZdqpucHJBYWTIMlKpzq6I/s0/ssis-lab14-matching-03.png)

### 設定資料來源

Add an OLEDB Source . (named **CustomersDirty**)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3fbgyTy89ajPrOMfGJ20rLfuINbz4PEsKWdOeORfhL7uRXtRAUs-svbpAW48aAIhpnKfckRP0EeiVoj9KS34LgNxZWm69fjR0mMIbU_S7fpB1FhvmBASkmQGUztoHGMmkD0x-HQahb3Y/s0/ssis-lab14-matching-14.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5CDLjqO3iHSdh_fV_1h0FbWSeO_z4pe6CfN7sY5k0kmMc1TPTdakwRSDZjknktNqcXoKgjeqrRGReP-dhhFIigBDs3pML2cJAkkHP3LWh8FfzBEE30-t8QEVnDCMKkHqyS7_yggghICI/s0/ssis-lab14-matching-15.png)

### 設定 DQS Cleansing Transformation

Drag a [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) . (named **Cleanse StreetAddress**)

Connect it to the CustomersDirty data source with the normal data flow (gray arrow).

In the [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) Editor, configure the following setting.

設定 DQS 及 KB 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcgXXmn_hLgCwAvc9k3WqqNpJSS5kkQQ7eGS6L-os_jnPcFOPcCvpmYqTjtm5q47jpGG8XpQ3NMSo60s3NsTMOEYZPqfbKBMY9cos28FKTgy6V3kaP06ZfYWTl9P_TLQyFlCotNnHb5DY/s0/ssis-lab14-matching-05.png)

將 StreetAddress 欄位對應到 StreetAddress 定義域。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXuUMPWcHXsCt97fHHQA7_ruMI-9t_OHV9cEbnzmWqgslYQ5d1q32lJgX2VvjMbsjh93ba49B-q49n7I2xgJEr9mC1Lo3CqoFLL0PIGlJU-v6TroHLgh05AzStx8WBUBkBcpt9Ba-_lSM/s850/ssis-lab14-matching-06.png)

在［進階］頁籤中，勾選[信賴]和[原因]選項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEijzDuGE4RLaB1xC61RXzqaR0awyGRLJ-oAaK5D6XG2uf4TBGS6pnHCG_5TUnr1hY9oJy33q7AbbD62zw1v-drI4l90PmsPHzBju_Q2I5wNThcXRG5XSlsrzqvxAduiJeS7IY6wP1ZRVdg/s0/ssis-lab14-matching-07.png)

### 設定 Lookup Transformation

Drag a [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) , (named **Exact Matches**)

- 這個查閱轉換會將來源資料（CustomersDirty）與 CustomersClean 做**明確比對**。
- 在編輯器的在[連接]視窗中，設定資料來源的連接，連接到 DQS\_STAGING\_DATA.dbo.CustomersClean  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqk2Dr_oyMfIKPMcj4fmirMbcsvNbAFZeyG458yTL5UDmBdAcn9DCJoQAddPGLIbQvfHHgCI4RY61GaphZv74rBtrX8hlAeATDN9aOpBQWmbZqHnpQY7rKI_DWmzY8jDPSMaUCgR3qs-Q/s0/ssis-lab14-matching-08.png)
- 在[資料行]視窗中，設定輸入資料與查閱資料要比對的欄位，並勾選 CustomerKey 當做回傳欄位。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixtfhWecA0GFj0czuHOG0zo2NVIOFhIBE_GNbmRatmRo2LqS_wBxr8B_pQTDN6g0v8ebAYYJOo6axvLZri1xcGsz9tfstYitd9rXOodKL6-VqHpP0okJCRNX_My0AZx75loCwwqRMqr0k/s0/ssis-lab14-matching-09.png)
- 在[查閱作業]欄位中，選擇[取代'CleanCustomerKey']，並將[輸出]命名為**CleanCustomerKey**。
- 在[一般]視窗中，將查閱不到的資料導向[無相符合結果輸出]。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9uxgxuqi15SMk49y1rqgQV_HEPz8FsG3EubnAFo5sY6_uC9AXRIWYn2RSl6d2IsJqN0R20QdwxoE5hSV8ApgZROJImxA-dhgsEGD5QZbdEGIatQpFbklrtH8-rQXxHNXKtbfzK3JDDnM/s0/ssis-lab14-matching-13.png)

### 設定 Multicast Transformation

放置二個 [Multicast Transformation](http://technet.microsoft.com/en-us/library/ms137701.aspx) ，分別用來接收查詢比對的結果。

### 儲存比對結果

1. 先在 DQS\_STAGING\_DATA 資料庫中，建立二個資料表。二者結構相同，將用來儲存比對結果。
```sql
CREATE TABLE dbo.CustomersDirtyMatch
(
CustomerKey INT NOT NULL PRIMARY KEY,
FullName NVARCHAR(200) NULL,
StreetAddress_Source NVARCHAR(200) NULL,
StreetAddress NVARCHAR(200) NULL,
StreetAddress_Status NVARCHAR(100) NULL,
StreetAddress_Confidence NVARCHAR(100) NULL,
StreetAddress_Reason NVARCHAR(4000) NULL,
Updated INT NULL,
CleanCustomerKey INT NULL,
Record_Status NVARCHAR(100) NULL
);

CREATE TABLE dbo.CustomersDirtyNoMatch
(
CustomerKey INT NOT NULL PRIMARY KEY,
FullName NVARCHAR(200) NULL,
StreetAddress_Source NVARCHAR(200) NULL,
StreetAddress NVARCHAR(200) NULL,
StreetAddress_Status NVARCHAR(100) NULL,
StreetAddress_Confidence NVARCHAR(100) NULL,
StreetAddress_Reason NVARCHAR(4000) NULL,
Updated INT NULL,
CleanCustomerKey INT NULL,
Record_Status NVARCHAR(100) NULL
);
```

2. 加入 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) , (named **CustomerDirtyMatch**)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3D2amf2W2515zD7UtwhzTQJ6pA-vbeiPEApVeK9Sf-CJrrDTtfiGMQfz3VsEgzAKKMWQyG4W0zO6lctkQHZ35jTVpU3QYtEI4C8ZvrzdeMUZwDwYGQ8_kgHRWRP2Q23-J9lB77ppKkQs/s0/ssis-lab14-matching-16.png)

3. 加入 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) , (named **CustomerDirtyNoMatch**)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3D2amf2W2515zD7UtwhzTQJ6pA-vbeiPEApVeK9Sf-CJrrDTtfiGMQfz3VsEgzAKKMWQyG4W0zO6lctkQHZ35jTVpU3QYtEI4C8ZvrzdeMUZwDwYGQ8_kgHRWRP2Q23-J9lB77ppKkQs/s0/ssis-lab14-matching-16.png)

### 執行 SSIS 封裝

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikTFAYuTCBxTbkDxhGbC4WkT7Q2YLHUty7JEUDcCXeh3l0QrheeE5_VqWfp0tBh2kQLq5SfKdEfXkjiyXqEen0SyIRBTNvLrmlDfh4pdDjuCUHuaQnGHOk00o2r7rIhylJ2zHiQjCJEZw/s0/ssis-lab14-matching-17.png)

檢查 CustomersDirtyMatch 和 CustomersDirtyNoMatch 中的資料。

你可以在 CustomersDirtyMatch 中找到 CustomerKey=-11010 這筆資料，且 StreetAddress 也已修正成「7800 Corrinne Court」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3P8T2iWN5IBLMWWzsMjTNCEXljwmpC8vqtm55Q5w9gpoZfnUWTk4JO6TAPYqP5fZ8rpn_dNmphjYtL8Xacx9QyE99OIyT1FG8mFOvbAXJVFyZmoDlXuQryGcNn1Pd5gra_CNJRXS0arw/s850/ssis-lab14-matching-18.png)

# Use DQS Matching

現在我們要使用 DQS 來進行資料比對，也就是要進行**重複資料刪除**。  

## 建立比對資料集

完成以上練習，你還記得以下的資料表嗎？ 

- CustomerClean：原始的淨版資料。（records:1849）
- CustomerNoMatch：經由 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 沒有被比對到的結果。（records:784）

我們建一個 View 來 Union 這二個資料。（records:2633）
```sql
CREATE VIEW dbo.vCustomersDQSMatch
AS
SELECT CustomerKey, FullName, StreetAddress FROM dbo.CustomersClean
UNION
SELECT CustomerKey, FullName, StreetAddress FROM dbo.CustomersDirtyNoMatch;
```

所以這個 View 共有 2633 筆資料，其中會有 784 筆是重複的，但是這些重複的資料與淨版資料實際上又存在些微的差異。  底下 Lab 就是要練習，如何使用 DQS 來刪除重複資料。

## 建立 KB

### 新增知識庫

新增一個知識庫：KB for Customers Matching

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAPG2LCr4_VJxJDXNXW96n64SRlB9pTqgacTnUhx_E2URvfOSSC7dLfsK1wc3iSGlWnYeE2U7EXj9nGsWsA_qeeiA62MwobHOCV52ZvtFcY5mnd8DjgU2BFIZwJoWA543RspOFQpjVAqw/s850/ssis-lab14-matching-19.png)

### 設定 data source

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjGOs5WHwY62MeYTdGvQ9wM8DoGhyyOLYJ9tkuLZvtLEJg2eDW6KynWXinLUYlnUSjnW4aFIEERQ1CgzYvF3S5E2aVAvRi0aWiMLux06XwoPSnOJ7rs6M0T7bh_LOsfWfu-JvmQ5mbvBHY/s0/ssis-lab14-matching-20.png)

### 設定 domain

| 定義域名稱 | 資料類型 | 使用前置值 | 字串標準化 | 啟用拼字檢查 | 停用語法錯誤演算法 | 輸出格式 | 語言 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FullName | String | Ｖ | Ｖ |  | Ｖ | 無 | 英文 |
| StreetAddress | String | Ｖ | Ｖ |  | Ｖ | 無 | 英文 |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlghbunyWyWnBNVedv444Dr3OcoHpXlddJM9csrfPF34N_RIxwaQhnqKVF1wFrsl-8iPiefTBqqwJDOgmDUfXnhdKr1dJSoW3_dGUIWX3gFK-AJsAtDBmGelJh92ZHpzJ8b1ViChGJvQ8/s0/ssis-lab14-matching-21.png)

### 設定 composite domain

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKuuuIhxik9lOMJJzGbONaG2HV_hwUAYAfoDbeJ9hheLM1K1Me_Luf52GWumHcyuWsY2fEEBoM8Iohyphenhyphen5DeAV-02qRU2txqvaKxnJqgq7d4THHA44guvtBa8yUmhQJ4ijAvQZJSJrlSkCQ/s0/ssis-lab14-matching-22.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOCsYaXSHuwFZVAiGPMJJy0Hre5xz_wy5H_nSG-OSi93d4vdjjsTreffBzgn7TUqfPYDNUb9955hOROUhC9cmCyH7eI98x7w7gimzx5IEIfgbmMILtB2I2owH90oGxHSJ6EVgO_Xdteow/s0/ssis-lab14-matching-23.png)

### 建立比對原則

加入比對原則（Matching Rule）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSQArQssjpmW4ifF6g2cZBta5uMS04F2FA8akrjUPvZgThIjSDRlPm6m1PUm-iG4ncEjAcC8B4qEBDlJgTesh9a49iQikYFK1yvfQMk86Kk1jzSS4xUtLL5AfHkDOAxl51W_sh0JxriFQ/s0/ssis-lab14-matching-24.png)

加入新的定義域項目（Domain Element）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTFUDKI_tiRXfAldifQkcO_IfGaMdj8Vtpzuo2NdgUj3XrQ3WXgZ3qTtzyEysaEFjLMghCaofmRr8L1NS0Zk8eipb9HhgTWFYy8GxLIDXQCzWfJQMivUEX5lxVmx4dvNU6qmuFFWjhows/s850/ssis-lab14-matching-27.png)

設定加權值（Weight）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVH0J0eDXWfAQTDmJYRm38iMFh6w92eyJfdkhKw7ZYi5fqTnNJNw8XZ4CjZg5NSFM98Zpst4DCgE4-awCltSjDbcjUTZnThe1finmEPSs1PTWmIRU-7m2JTXvvZ2VSPynXmgwl_dOmaPA/s850/ssis-lab14-matching-28.png)

啟對比對

在比對規則執行完成後，你可以在下方看到比對結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpLE2wV7WfIv15H8HEu7JTLOV2iEJlzhsueUkXb7OwtDlS9VYRb-3cvVk5i3qDN11HN6lRjil8Te8wQ1oZxaB3M6SAkh3-7I_JuOx-yJ7fMakFUeins1RwSOpB9NFpptIhvs7QBkkNN8o/s850/ssis-lab14-matching-25.png)

### 建立比對原則２

你可以建立其他的比對原則，以便對資料進行更多的比對測試。  例如下面這個例子對 FullName 定義域設定 40% 權重，StreetAddress 定義域設定 60% 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0dw8UBE-FahNUPrixWYdrhaaHBJJOiHYEEFVI1mKisnyWoLkBib6jN4dIilrDKpLnhiH3yUJNNKwB7XncW3sPH_R7cqa9OnThWu1s5ylrbCAqFeJgA0tRlGM0k8Dnj6Sul7tSsexqEjE/s850/ssis-lab14-matching-26.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHNZqPd9QSL6Dp6H1Pkk02bhLwiTglUAHXElmhlXWM5teusAE8U-sy46Y4gQiOEjDY7ohJ0vaXa4UbYyhTtRrFN4i8M1qRBRVe0_wPqif9jzlZfSHyk6hp0wD1m4DdY65GrVP43NWIZGI/s0/ssis-lab14-matching-29.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTaes4F1ur93qiynKlpHk_H9RiPjTuPpsckyzETu2Fs0cVgiku9KbLSSNTyjkvkL7BXXtkobmFRcCuJek-eepfe1bbLH-c91gS-HSLui52l5EEHea3cFu2wJ7aC6P-kz3WOAXnMovxrx8/s0/ssis-lab14-matching-30.png)

### 啟動比對與檢視比對結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhk_VJJX6Xt5NscnsJR8w-QmyBrc5-_Eib2I0sL3XofSpg7TBGjwAFEJjGx7pFFg7WhXMieWo4zDdb9VU7llwt4qW2MCC0Q7FyuJRCJiV71LKeE-5rTMdayYxzW33pXU_YbhJTblhMqcnQ/s850/ssis-lab14-matching-31.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJPUiMi5a79jv2Awk41LyHK_YY3KxBCN4XETliKiE0Ln8sGzN8rYlzk1GvuZ0j2tgBsknc-lNDaKTLR7e2WBsv9CjizYP4EXhcUz1hTGgFK20Bvo8RfgmGW8vJfIF3cyEp0T9hZDvtClE/s850/ssis-lab14-matching-32.png)

### 發行知識庫

按下[完成]

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4nInpJJ20lK3rhXgM48UHl5F4r0x-Z6HeJ_hTzoOcCnvJ9ODO23cAkSMlEr7Psk9hkF3QKPiqJWwDfIxF3j8DlDLECn_dRz3LhtQ-SxJSJKxUboooU3OKcwoM8LCOWlnZ5xCmW6yNSAE/s0/ssis-lab14-matching-34.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOyjxcJUtk1kHaLA8M_-18k8lQRIcjWao2_SLNNJh-1ZMy-u78H6f9Q21ikMVrU8hC0fLjeRvoHpbtlvmGEBYuouYd7aXJxp0KV-ETQeMVxOG-z_6giL6847VyqRHjxyVbqj7i6lReFpo/s0/ssis-lab14-matching-33.png)

## 建立 Matching Project

### 新增 DQS 專案

新增一個 DQS 專案：DQS Matching Project，其活動選項必須選擇「比對」。

- 知識庫：使用前面發行的KB for Customers Matching。
- 活動選項：必須選擇「比對」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWCx6cdU_EESYeDqMKakdo79CYQFpsoD-tEEUBF8tuywKtJSqMsRFVzTK9drvWa2JPLXpz0k17PpXUQR4daYBgPN7f_kdsFOKhHEyK-VvD9FtiwarUeSR82OSSDT1CxR3hGr2xMiQJ-Hs/s850/ssis-lab14-matching-35.png)

### 設定比對

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4xICwB9otM-vFWyvXGPEhA-Xe8KL6IJu5K0uhBnpzCkHXbEdq1Y2detULJy24rZABrRUSp8bmPwLUvCjgSwGjtMbMmlYOtu5yedZfXgx3pKYh9EC3w0saY9HvF7EdrlGSRLJfz7mOIYk/s850/ssis-lab14-matching-36.png)

### 執行比對

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-3I3jvjUQdRKJqeTG-tsoQyWPkST0ilrHHdKdwQvfQpXy2vQuU0J3zKvzvVhGrrPqn25zktLXs1cRikkVtL17vKNk4fsJBovW8qnHCpcPWY3-kNvpw3qasAdl20MtahQ6NF_9AQ8Q5VI/s850/ssis-lab14-matching-37.png)

### 匯出比對結果

在匯出的內容中，包含二個結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjy9MPqD8KqLeJ3gE6twQ6kCgmOzXj1r_bXLlo8rtlHJGIMy4M7ldgumJwrduoGEHb-MN8fFnbZ9LvQ0Js7NyOYlxUzCDdOFsVH9eZ4yATHD_9lTK8d-Cb0QNoElKdWbrPCEVaArLFOm5U/s850/ssis-lab14-matching-38.png)

- 比對結果：用來記錄比對的結果。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgthHEbmeaTt0gfo6fzh1LF2jzHFB-kPS-o9YEM_XeLHMvEF16O0X4S0WeqHurp1RnUl7jHBrbapNDxI7FOvLOeJwHnqco1wfHiS0c3wbm_o0kA2P0I2d80t6OCOshu4u6_r7o2m3nbd3E/s850/ssis-lab14-matching-39.png)
- 生存結果：用來記錄刪除重複記錄後的清單。（Survivorship Result）  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzLoaDdqyASaW-CtnQHc6TJS1SZZTKju89ysy1h2MZ4gW78lULwrE8VJysNOFMq2gWsxL4DN6DfnOEXcyLTth2RyLId4Vkomi9xHa2Pew1QjuFINRbyUFkAgMDPNT9K7crAzgiGq4HFgU/s0/ssis-lab14-matching-40.png)