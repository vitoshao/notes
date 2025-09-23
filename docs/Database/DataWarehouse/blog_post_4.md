---
title: Lab4：資料流程設計
layout: default
parent: Labs
nav_order: 4
description: "Lab4：資料流程設計"
date: 2013-12-19
tags: [SQL,Labs]
postid: "4674820807634099304"
---
在建立倉儲資料庫時，常會需要使用到 Stage 資料表，所以底下的 Lab 一開始會使用三個簡單的方法，來練習(1)如何在 Data Flow 將來源資料 ETL 到 Stage 資料表。  (2)再使用較進階的方法將 Stage 資料匯整成倉儲資料庫所要的資料。  (3)最後再練習如何在 Data Flow 中，判斷一筆資料是要新增或更新到倉儲資料庫。  

# Staging

在建立倉儲資料庫時，常會需要使用到 Stage 資料表，所以底下這個練習，我們先由三個簡單的 Data Flows 方法，來幫助我們建立 Stage 資料表。  這個小節將同時練如何讀取不同類型的資料來源。  

## 關於 AdventureWork2012 

以下練習會使用到 AdventureWork2012 中幾個資料表，簡單敍述一下：

- 「銷售員」和「客戶」資料都是以「Person」為基底資料表。
- 每筆「交易記錄」都包含著「銷售員」和「客戶」和「銷售地區」。
- 「交易記錄」、「銷售員」和「客戶」都有「銷售地區」欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8w4JlywbijmxpECiLXutkwPjtYzCnnMUrID3PIJvG2EEAJhrHg0VIuQtMik45BO2drhws6j2uBCoIA9O-GAaeFl4Q_v5Sckbc-c_lJYv10XYhBIouEHHknlOSLIxHBdk88jZQna-ehJQ/s0/ssis-lab4-01.png)

另外再準備一個客戶其他資訊的檔案(.csv)，練習檔案資料來源的讀取。（[下載 CustomerInformation.txt](https://sites.google.com/site/sharedvito/blogfile/CustomerInformation.txt?attredirects=0&d=1)）  
完成後，我們將得到下圖結構的 Stage 資料表以及客戶資料的維度資料表：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMN8wNEvEHzxncoApiu1x8e0IsT8-SURO6nRWFJTvZMy8xEXm2Gw5K1c9rOZ-QzxT2rTbgG8CDUEXmkdoreC652USLlO9Nn3C6sNNNOqMBx_IBylgYG39-cMyFB8MPZXohjVcra3a1U_8/s0/ssis-lab4-04.png)

## Stage the Person.Person Table ( Using OLE DB Source )

這個範例，練習使用 OLE DB Connection 將 AdventureWorks2012 中的 Person 資料 ETL 到 MyDW.stg.Person 。

### 1. 準備工作

通常 Stage Table 是不會對外公開的，所以你可以另外使用一個 schema 來區分 stage 和 dw 資料表的權限。  
```sql
-- 建立DW資料庫
USE master;
GO
IF DB_ID('MyDW') IS NOT NULL
DROP DATABASE MyDW;
GO

CREATE DATABASE MyDW
ON PRIMARY
(NAME = N'MyDW', FILENAME = N'D:\Database\testdb\MyDW.mdf',
SIZE = 307200KB , FILEGROWTH = 10240KB )
LOG ON
(NAME = N'MyDW_log', FILENAME = N'D:\Database\testdb\MyDW_log.ldf',
SIZE = 51200KB , FILEGROWTH = 10%);
GO
ALTER DATABASE MyDW SET RECOVERY SIMPLE WITH NO_WAIT;
GO

-- 建立SCHEMA
USE MyDW;
Go
CREATE SCHEMA stg AUTHORIZATION dbo;
Go

-- 建立stage資料表
CREATE TABLE stg.Person
(
BusinessEntityID INT NULL,
PersonType NCHAR(2) NULL,
Title NVARCHAR(8) NULL,
FirstName NVARCHAR(50) NULL,
MiddleName NVARCHAR(50) NULL,
LastName NVARCHAR(50) NULL,
Suffix NVARCHAR(10) NULL,
ModifiedDate DATETIME NULL
);
```

### 2. 建立 Connection

在連接管理員中，加入二個 OLE DB Connection ，一個連上 AdventureWorks2012 ，一個連上倉儲資料庫 MyDW。  

- DB1.AdventureWorks2012 ： connect to AdventureWorks2012
- DB1.MyDW ： connect to MyDW

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTwa8ArXikx0yfJXFILnhvd8Qiw9AMSXCuqS8_7W7Z2j-qjEUiyH_X7zSRX51IBo7RxMiHvu-hyKRtwZQTTw7s-5aZffu6jeRvl_hcpsJROKK-5ZzAXt_KNW0jhnm9vDmJQ3rH5Exn4wc/s850/ssis-lab4-05.png)

### 3. 建構 Control Flow

Drag two Tasks

- [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) ： truncate table MyDW.str.Person (using DB1.MyDW connection)
- [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) ： move data from AdventureWorks2012 to stg.Person

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNMiQ0dLGTGVeqET4gdZU7GNwbNyzLtaU0y1g8PLv8TRMHPPmiDEbqfwiIYRgzBumSwGpSzqnhmFjJF8M3LICa-gZPCDouR_D9hGSCQZe7uegcgSnq1M-ef67GNrxMRfPU0CWiOVqZvz4/s0/ssis-lab4-08.png)

### 4. 編輯 Data Flow

Drag two Data Adaptor Components：

- [OLE DB Source](http://technet.microsoft.com/en-us/library/ms141696.aspx) ： using DB1.AdventureWorks2012 connection
- [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) ： using DB1.MyDW connection

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiF33w4XKP1gf4HGU19NyniQKKtl_5yGQs-2qULECozjuY6nDBozuVXHK4_qM62gsxjdZNTL4S12s7gurg85ekWkXiggR11cbe8DdqOtn8RL51B6NQcyi1aH3mXkgspsCqSKssmuPpBaQ4/s0/ssis-lab4-13.png)

Setting：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZKh2tbRYwkcOlkFL4A3-g-1Q07Ck7On5Y_6B06wSd1xiVHVRQmBJhPdV7ksD-_IXc3YBVIKbl2kNLDM3HVdV-Ee6KpUhHluvHzTmgSfaFX9YXk0h8YCOnTl6Pb-ufY_Snkz-mvxQAtjA/s0/ssis-lab4-11.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEib-Q5uVsP5wrys5AevRpXpbRvbY_iXhPfcge7YV0pneXhCyskoEwVsXTmPvex2OefTSARAetvcNLPs3hNJRKWjo-2gEnP8D0y99tOnNlg2XJBvYANGhlTU4Hm773Nr48UXXoCAW4dW6as/s0/ssis-lab4-12.png)

## Stage the Sales.Customer Table ( Using ODBC Source )

這個範例，練習使用 ODBC Connection 將 AdventureWorks2012 中的 Customer 資料 ETL 到 MyDW.stg.Customer 。

方法大至同上，改成使用 ODBC Connection 即可。
```sql
CREATE TABLE stg.Customer
(
CustomerID INT NULL,
PersonID INT NULL,
StoreID INT NULL,
TerritoryID INT NULL,
AccountNumber NVARCHAR(20) NULL,
ModifiedDate DATETIME NULL,
);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIcKWADRgZI8pJBZrhACuo21rYlHYLp55W2KZcoTFwiklsAQo6k1hQTrs5qqc9_jvaPL6-Dwj6NVzP8DyaPLxPMnQRkAB6Jy9PHIQdyi728HMZIoAEFpoAPuCr36Kb-DjKiOWKswPm-AM/s850/ssis-lab4-07.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwBP4eodina5Q6t62viVsl1_3ZnQKY6xwY7XcptUh4wDSxPqDUYWmLCRWH-kAkOTy19RitNc0HRR18jYU-1-dKj4gSa2dzJxGTYEIzDEPxNLZuObdDSjkr37mzwreK3xiGY8lOCKBb6_I/s0/ssis-lab4-09.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9-9HvpMkhnjNSmMaHWAE18tSdJ40G8xJfKRbdAwgGH-B3lmXyzF_b5WBULDqEX7NvBZIO9e9r2Ki-nEdwm8u8nwEQxuzFm5a8stxi-gF6HvQFhokIgj7_RmAipRFZRWTLlx7K8rFYx2M/s0/ssis-lab4-14.png)

## Stage the CustomerInformation Table ( Using Flat Files )

這個範例，練習使用 Flat Data Connection 將檔案 CustomerInformation.txt 中的資料 ETL 到 MyDW.stg.CustomerInformation 。

### 1. 準備工作
```sql
CREATE TABLE stg.CustomerInformation
(
PersonID INT NULL,
EnglishEducation NVARCHAR(30) NULL,
EnglishOccupation NVARCHAR(50) NULL,
BirthDate DATE NULL,
Gender NCHAR(3) NULL,
MaritalStatus NCHAR(3) NULL,
EmailAddress NVARCHAR(50) NULL
);
```

### 2. 建立 Flat Data Connection

2.1 在連接管理員中，加入一個「一般檔案連接（Flat File Connection）」，連上 CustomerInformation.txt。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg76fT-K-GYiR4e8nHCrMwcrPUMGifJNhIfp9PNb50_H3oNVxJ_M0eNCi0M_BQvnsFfPhcFIm1okvkVuycDgPUq75kpm59pQgMmLurerqIxawmh9bcobGPmvzFHRFvGCLBDD3XQdQA-mig/s850/ssis-lab4-06.png)

2.2 編輯該連線的設定：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIuxRlmwgeRRJshQTvRDdt62RHcGb9iVVGFsAslg0o-Z0m7ExKMQ0l6k_vo2fx2IqXwot8dFdPvKmrU3CBibbSxywFM59iD2IV-Sr7QYfQuT_UrZixparojJgZClcXKJjLoIqKrGURbvg/s0/ssis-lab4-15.png)

2.3 切換到資料行頁簽，檢視資料行資訊：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBam7krGFyMF47_uOEL1cTudwuAudybzWFyTgsZSUDJ8ymSREAMoHk4QkXtmSk2e5YKqj0t5TD84mYs34JJ0IGG_J3q5tEf4aln7uK-2r-YMIzsx3tDdWBGX0QVW0fJK48eV4q0kLYHv8/s0/ssis-lab4-16.png)

2.4 設定採樣資料數：

在執行這個步驟前，你可以切換到[進階頁簽]中，檢查目前系統付與各個欄位的欄位型別。  它很可能全部都是字串型別，這是由於 Flat File 這種資料來源本身不帶資料型別，所以這種連線會預先讀取些許的資料來猜測資料的型別。  你可以在進階頁簽中的[建議類型]功能中，增加採樣的資料列數目，讓一般檔案資料來源，可以更精準的預測型別。  當然你也可以自行將 DataType 屬性，修改成你要的型別。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCAjIVTYtv2J1d-m5LHD0IwoA8Xyg6Q2BiurOzN3OwEXJTLoaBEt6_mLcsWNxtppsKFUN4rxPNZjt2waxkiziw-C-fnkGM5KW0fslP21wdQBstk3NdRocAEDUTTstkqcOYJA2JjSg5mOk/s0/ssis-lab4-18.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwA88HpEqrlLPulVsYH-XGaLzjfEuBtnFd_bCUQ-JckSIA1QwoJaUwsftfQ7o_u-_Y1kCNFtUMgsAkDBU-Bj0boXsGj8QsRgY5u6db2omEsyt4czveDmqu5FLrpdQXDSIBHamRchEaYEE/s0/ssis-lab4-19.png)

### 3. 建構 Control Flow

Drag two Tasks

- Execute SQL Task ： truncate table MyDW.str.CustomerInformation (using DB1.MyDW connection)
- Data Flow Task ： move data from File to stg.CustomerInformation

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiApd_PD3veqLKEN-pFkHeeGUkt-QtigXkswBmm260wIlQ73PaSMK0FYmUvsOhAhGTISzpn2vZd_5UL-0ffwxL901a_AJgLkanHzuTgCCxnvmEcDBGo_7bc7gvt7EbhAUCLrdC_-d7a9gs/s0/ssis-lab4-10.png)

### 4. 編輯 Data Flow

Drag two Data Flow Components：

- [Flat File Source](http://technet.microsoft.com/en-us/library/ms139941.aspx) ： using Flat Data Connection
- [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) ： using DB1.MyDW connection

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjD0B6W1esCKPgEpjIMgPJ9Z9r9GB8i24Tbz6Dbo23sWLXzQ5HRt7m8oBiStQAwSVD1FyjXxyoIYb82Vx2u-dj6iN06OrifKmi5kx9FLKJTZq5YzgJMONMOZQaboi6mXuGGaVrsnN8FMk/s0/ssis-lab4-20.png)

Setting Flat File Source：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNarPPnaaznBGqnvB23ciinNgtGO1-KnKnYh0sXOKd7DcZFEO2jXc4PKYAu0Qxul_LlRBG9OUpU0MRM29tu6-LxqIt1nT-J2KZOHhqw4Uhxhk9S5vy8ykxcfZz4_IFvRyGd6ywTLL1haU/s0/ssis-lab4-23.png)

Setting OLE DB Destination：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnrfIiTwjcirJ0LlCEcjEOyUlSWw7CnEXlquZkK7zXSBuVTPdVtLL52K5JK7BuWLH8tKtaSkl4neMb23rRRhJcgE1TlSnNxdDTTVoA41zs9Uuuk8BA1z553keJUgQrCIs5m0BgOTcdx58/s0/ssis-lab4-21.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQ9nYWu6zi5eJPVhfhD82IJBeUSVskHBicZx7cGLCdwJBFAk7x1QLnbd6ZuCLE35yZOltAHiwMJF6UresnfKR24z8gXNcvuhIPr5RiLhVabNuBKs6zIEt_dfGbRAByGixUR-3XHTTU6TI/s0/ssis-lab4-22.png)

### 5. 執行封裝

如果沒有錯誤，你將可以看到以下執行結果畫面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYLRi_w1mdV8So_hyphenhyphen0I9aCVV31dFTQbwOtH28W-3zzAXK9MTWch3gjY-wO3NWlivu_AxzicvPpvY6OEwSwrRxgmk3JCBbVrpB1s-OJDE7r2f3QtCNlVQFJWxwC3DOZ5HrHRUd2NYP1KaM/s0/ssis-lab4-24.png)

# ETL to Dimention Table

前一個練習，我們使用三個簡單的轉換方法，建立了Stage資料表。  接下來，我們將利用較進階的轉換方法，將Stage資料匯整出倉儲資料庫要用的客戶資料。  這個轉換，將會同時讀取Stage資料和線上交易資料庫中的資料，以便匯整出完整的客戶資料維度。  

## Prepare Customers Dimension Table

### 1. 準備工作

1.1 create **DimCustomer** table in MyDW database
```sql
USE MyDW;
GO

------------------------------------------------------------
-- 建一個 SEQUENCE ，用以產生 DimCustomer 的 surrogate key 
------------------------------------------------------------
IF OBJECT_ID('dbo.SeqCustomerDwKey','SO') IS NOT NULL
DROP SEQUENCE dbo.SeqCustomerDwKey;
GO
CREATE SEQUENCE dbo.SeqCustomerDwKey AS INT
START WITH 1
INCREMENT BY 1;
GO

------------------------------------------------------------
-- 建立 DimCustomers
------------------------------------------------------------
CREATE TABLE dbo.DimCustomers
(
CustomerDwKey INT NOT NULL,
CustomerKey INT NOT NULL,
FullName NVARCHAR(150) NULL,
EmailAddress NVARCHAR(50) NULL,
BirthDate DATE NULL,
MaritalStatus NVARCHAR(3) NULL,
Gender NVARCHAR(3) NULL,
Education NVARCHAR(40) NULL,
Occupation NVARCHAR(100) NULL,
City NVARCHAR(30) NULL,
StateProvince NVARCHAR(50) NULL,
CountryRegion NVARCHAR(50) NULL,
Age AS
CASE
WHEN DATEDIFF(yy, BirthDate, CURRENT_TIMESTAMP) <= 40 THEN 'Younger'
WHEN DATEDIFF(yy, BirthDate, CURRENT_TIMESTAMP) > 50 THEN 'Older'
ELSE 'Middle Age'
END,
CurrentFlag BIT NOT NULL DEFAULT 1,
CONSTRAINT PK_DimCustomers PRIMARY KEY (CustomerDwKey)
);

------------------------------------------------------------
-- 設定 DimCustomers.CustomerDwKey 來自於 SeqCustomerDwKey SEQUENCE 
------------------------------------------------------------
ALTER TABLE dbo.DimCustomers
ADD CONSTRAINT DFT_CustomerDwKey 
DEFAULT (NEXT VALUE FOR dbo.SeqCustomerDwKey)
FOR CustomerDwKey;
```

1.2 create a new package

1.3 將前一個 package 中的二個 OLE DB Connection 轉換成「專案連接」，以便在這個 package 中可以共同使用。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1wmoBiZyZNjN6XyqlUjSDYqw1kHVR4Iw2KHxYyG-SiVqbueJ1dc-39TWpas_iYPwWOqDV6sAaA1vv4VxR4AZQQktpLO2ipU3qvkJ7MOMbcReH5vv3hyphenhyphenelTfSlzKTo6GmUJ-fXzSIk3Ns/s0/ssis-lab4-25.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqbgUaj-eaoAZUf4VvNM1QQHoB59WoIEcyKglMSgEHkKNvxnRFqbP6cB5gZLMqp3d1mI5F0RfNegrIA2iOmEUuJqQkhyphenhyphenVjG1rMat5mJh9xmpnbDXN8FzBbiPwu-fd3VygjB-hNEofCDa4/s0/ssis-lab4-26.png)

### 2. 建構 Control Flow

這個 package 的 Control Flow 只要放入一個 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) 即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRoXlSlajVYAq2duTeAmdzMSWOvr_bjXsDLP_i9u__qZwlCAIsQho13uos-PrXEacyqoQ1TjJ6xv5tO61yz6M299GnE-qQp7z0MLQJPmMQ1oyJbNCFOL93_O2vTuod8bIYBTWmL_DoJyM/s0/ssis-lab4-27.png)

### 3. 編輯 Data Flow (Transformation)

依下圖建立資料流程：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhb9EG-MxrwR_B5NHtZdN0ejnt75wHTJl7rDN0cQCIamA3L_tv5WZcvQp1ChojxRfAvy7HW3PkBHULy7-fwHVpEbUQC4X-E-xv36hyLB8G2Pg_RcLUk4fJ7GOAA23rdEhv1PNJQWDKYVko/s0/ssis-lab4-03.png)

上圖說明如下：

3.1 加入三個 data source

- stgCustomer：using **MyDW\_OLEDB connection** and select **stg.Customer** table
- strPerson：using **MyDW\_OLEDB connection** and select **stg.Person** table
- Sales Territory：using **AdventureWorks2012\_OLEDB** connection and select **Sales.SalesTerritory** table

3.2 排序前二個資料來源

使用 [Sort Transformation](http://technet.microsoft.com/en-us/library/ms140182.aspx) 將來源資料排序：

排序1：指定 BusinessEntityID 當做排序欄位。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidXwyYq5k2pyt1MkPfbGUIfZ6LkizX6vFMxpSy82XPFu5pVTCra6DdStEgALR8xTZ2AqYbJxrIjy-mJrKr_zXiRYwXJHB_LPYmTBO2-Mgm6GbtaRLtUdymwjwPiCS7gphBNad3JTtupGs/s0/ssis-lab4-28.png)

排序2：指定 PersonID 當做排序欄位。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEa6cSzo2-FiskqVJKCY9py19QseCCGYHPpy05prdxU_BdbzauzcbQ-uG4zJHxidmxEBB_IfJIbSwoft_GA-GoZ7X15OQz-AMy5Jq2G44z6S7LVfP9Ixz80L638XwHBKxiLH_zTfsoqYg/s0/ssis-lab4-29.png)

3.3 加入合併聯結轉換（Merge Join Transformation）：

在這裡使用「合併聯結轉換（[Merge Join Transformation](http://technet.microsoft.com/en-us/library/ms141775.aspx)）」來合併上面二個排序後的資料來源。  此處的聯結類型，我們設定使用 Inner Join（內部聯結）；  同時勾選二個表的關聯鍵，並勾選要取得的欄位。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0sVHi12utFAOFzq_YbuN-eMI1KNk0f4OUto9v99-Zn5a_KmeIvuioDGdGEudQbRT0sqGrPHfSIASuymX7fF0tmRU5hFStUZsQfhUUwBma5KfEn69K9ftsVpgkIswEtw7-T9N928PZWQY/s0/ssis-lab4-31.png)

3.4 合併排序結果(排序3)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqzSrckG43uz31sH04aCPWfG5BUXlUl1GZ4HS2QBRxLzoULymc8NhCw78CEkRjvKuQGHU00fAOTd956abWAKm10_l_lfYEzd-WhNdtiPwhndVIIPnyvuFTDU96rVPLfZHSb7SC92RI0JY/s0/ssis-lab4-30.png)

3.5 排序第三個資料來源(排序4)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrQQsFzZRLg9lBUxrH-1dTSpJuvvtKI0GmS2AdRyMSqRGdnf5wgGNTBwjh7tHvmL1BhLe1N5oH97TpmGC5FNSHCpsvIEzugwZ4x628Xk9yBch9Ar4gh3cTi7ixpinoHTM_CMDM47ywdk8/s0/ssis-lab4-32.png)

3.6 合併排序結果

在這裡使用「左方外部聯結（Left Join）」的聯結類型來合併上面二個排序後的資料來源。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgR8_o3JpMvjYeyBKsdXUQNZs0bN-mLhTyJ5qeN-V5PGpaq6xYpY_gLDgMEDJ7gGieTsJA03kEZ1fU4xcRuO-FTVZg5NNx9phA6iOC3yhLuDmR-ZAWgWt4VEDIP8rOU3whPG7wfryz3_-g/s0/ssis-lab4-33.png)

## Loading to Customers Dimension Table

### 4. 編輯 Data Flow (Loading)

4.1 Lookup CustomerInformation

經過上面的操作，我們會得到一個結果集，接下來針對這個結果集，我們要透過 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 去查詢 CustomerInformation 中是否有相關資料。  

4.1.1 將查閱不到的客戶，導向「無相符結果輸出」  
當使用 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 查閱資料時，若該查詢沒有相符的結果，你可以有４種輸出方式。  在此我們選擇「導向無相符結果輸出」，接著對這個輸出給定預設的 CustomerInformation 資訊值。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSjC04EWLyk9lXw7ITYq16ZbECCz1XJN1M1Qn5FzF99DF54EeXDYXgz2zYKJIx2wcri7PKjv4BVqe1zvIV6mar7cKLQb_b10i3HgdmF5LwMpuv6q4gRTN4LhPzQU6AWgFiPqSCX_UgoP0/s0/ssis-lab4-34.png)

4.1.2 在 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 中設定查閱的資料表。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggqQkkqDRIi8O2ENx-aTw-JGb-JefzFbcgcV-kq79aA2NbOK2J8levDRwDgnecPz64ql63147xkdbk0G9OtXACqVejS-A4qXmDW6r4yL9HDmrcWqHE6Q3SonYgkIN8uLvphldtiwMXQtE/s0/ssis-lab4-35.png)

4.1.3 設定查閱資料表的關聯欄位；並勾選要回傳的欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjE_GahlmosQOXZBnb8Bf8XP-fcVigElv-YW3YDhDkW3wt8p6htc0dM_kWOrtRzRex38goDI8WiTiVDlK8P-kakJD60I5Czdbt7fVx1a72kx6d3Aup4E9crCfff-uicn56FGxk-HvhcFAs/s0/ssis-lab4-36.png)

4.2 設定預設值。

上一步的查閱轉換將原本的資料集分成了二個結果，一個是「查閱比對輸出」，另一個是「查閱無相符結果輸出」。  接下來透過一個 [Derived Column Transformation](http://technet.microsoft.com/en-us/library/ms141069.aspx) 來設定沒有相符查閱結果的預設值

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivQZ5NrTuHYkpl8fd6DY7cOtzkng7VI_NsQyZyAQjVHB8_s9-0-ljV9WoVZzIwDsVJf91BXC2JFwLn_T9qOgYAcC_fGd3ji_UScU2CqXcNQE1hFEU5L3ZDxuP1gC9lIDm1jvKkM6aDJy4/s0/ssis-lab4-37.png)

加入６個衍生欄位以便設定預設值

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYQDo_EH59pemILWYQMKKcKTWcxaB1byNTfdcb43FpOTJDBPeOuhdgeP6yBdbVzlReQ85zElo21Iu5XqixMq0laxztU7hojchyphenhyphentfgU8bnB0VcTY59D3DT6WZ5DtQv7TzWz2H2P0newYv4/s850/ssis-lab4-38.png)

4.3 聯集全部

上一步驟得到二個輸出結果集，一個是有比對相符，一個是查無相符的結果，現在我們透過 [Union All Transformation](http://technet.microsoft.com/en-us/library/ms141020.aspx) 把二個合併在一起。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjv5oggNRAjCFytEJq9FsutYW9qEW8bil9wbl1MpxX-1_72t1uDLjluOMVEHAcYikdcshSbULyrsVMDmQEuGknj4aueogQ_JboTpoIjMH5n5GEaqwMfdmbKRThoeeHpzy689wcPBUtUPJQ/s0/ssis-lab4-39.png)

4.4 Lookup DimCustomer

將上一步驟得到的結果，使用 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 對 DimCustomers 進行查閱。

- 4.4.1  「無相符項目的資料列」設定成「**忽略失敗**」  這個lab只要將不存在(新)的資料插入到 DimCustomers ，其實在這邊可以直接處理「無相符項目的資料列」即可。  但是為了多練習底下的條件式分割元件，所以這裡就使用「忽略失敗」，忽略失敗會塞NULL值給「無相符項目的資料列」的欄位。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1rZ6GkT0VQCi8gqro8Dtt2Q2mw4VKB1q3OHZQqYxfobElp9PQ0W350i0c9IHZA1ttDgJzjguNTvZpQBjRIW2rkrwdzK-eOtI50Ai1MKBT6unPvogXUP6noEgibDJjTK5y2ONmdM17j1E/s0/ssis-lab4-40.png)
- 4.4.2 這邊練習如何使用 SQL 查詢  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9tSKWhn1Ww354-HbGblLkJ1YOpKTG70ulHFh4wCH8ZdUGdQz1uxAySgWyskLfWJsBZe5mt7OTfgsBh_KVgxl3e8OgTZEsrAKbnFT7lEn0t2kJRoFpU4r4yDYA5aH46vX8_J0lJ4FL-Bs/s0/ssis-lab4-41.png)
- 4.4.3 設定Lookup資料表的關連欄位；並勾選要回傳的欄位。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjk5AU5XRzrEsb8kcJsa7VWLHW1GYgIg21o9kpCqeGI_lOH3RMMWNS3GZJw3C5YZobjYGn1nug-wZAlbl4-___JILEqb-l-ZfU3mx2XuFVGZ-HOlayKP4I9OlZePeNC11JCpqPfayO4SZ0/s0/ssis-lab4-42.png)

4.5 加入一個欄位

因為 DimCustomers 需要用到一個 FullName 欄位，所以這裡加入一個 [Derived Column Transformation](http://technet.microsoft.com/en-us/library/ms141069.aspx) 來取得這個欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg27-HJx-V78YLkxXD8bUut5Urpv-YTr5em8cDLaqVLB9FpyWwLy7lN6cq2QZGdJHZxpotayaxABOVCiY8jptgIvAulsTOvHoYo2MnDGVslU600RTwOuukhJpStaafk23Hba_hLTUz1Nv8/s850/ssis-lab4-43.png)

4.6 利用條件分割，判斷資料是否已存在

由於我們只需要將不存在的資料加入到 DimCustomers 維度表，所以可以利用 [Conditional Split Transformation](http://technet.microsoft.com/en-us/library/ms137886.aspx) 來分割結果集。

我們在「條件分割轉換」的編輯視窗中加入一個分割條件：只要符合 IsNull(CustomerDwKey) 條件的資料列，就會被輸出到 New record 這個結果集。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2Ycbe5Hzqme0ruPwN9U068FTFzMJ2kWCb8F3EXoumIkT4Lh826nHKx4_fcidvT3EmPYV7aMLxqpvsNAUTCUhh_Qhrg4zJGtQbYRHESysGavU1p_D4sTii-R7Gluoxc4clq-8Onf5Kk5M/s0/ssis-lab4-44.png)

4.7 輸出到目的地

最後將結果集輸出到 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) 元件，並設定輸出的對應欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHnu_i00MxQ1pm9CJ37X0rhkztAr_Tb6Hpuyke8s4jzIQrUypwBpd1HLcd6qvbgUsPQmz5_UgfOrvrlbQhyphenhyphenXXTJJb1As9rkahetYL5Q5uTqY7wcL_sZXCxUzPXkdb0T7FsOv1w_JrabPs/s0/ssis-lab4-45.png)

設定要輸出的欄位，不需要的欄位則設定忽略。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmuF-L9TPD0XukPIKMI27-7eX76CD7H1vk9hqgsyNcSgHdmLpB-kbkmlZCsu6yi5v4pg0wtOzTCQuPNKqKSfNlpFMEm1_B8ac3lgNmEMYWZAWL2MMNmvJYWyzpwlrLr_TmOeQ5zYav-IE/s0/ssis-lab4-46.png)

## Execute Package

執行封裝後，你可以由工作視窗看到轉換的資料數，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-6652hsr0xrEyy7MZwbMrtdcTjwaYbdY1gEkVSlHQu23JIKKl4vAkRp0QDjnNMVat4fQWoB-MC1lJEamt8gfqtyvAcILFV8Y_0DzSZQFo9yNrsgVrFfZ0KhIGztMCWX7xWCr05KjcNdE/s0/ssis-lab4-47.png)

如果你再執行一次，則沒有任何資料會被轉換，除非有新增的客戶資料，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigjk608qvcFVZ6m_ZqLkX7XUz0PI8pora29ssUiOW0XxjcPMjbnSm96IzUSurGmjGDlzR1gc4UfrwTtQY71vJFZY_-uZP9xCtOQt4JWiWoz5v8mzmMvnkZlM7YUEpWhlFiCWMfi2zMq30/s0/ssis-lab4-48.png)

# Implementing Update

前面完成的作業只會執行新增功能，接下來我們將在 Data Flow 中加入更新功能。  

## 加入 OLE DB Command 元件

5.1 加入 [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) 元件

「New Record」是我們在前一步驟中自行加進去的一個分割條件，若不符合該條件的資料，則會由流向「預設分割輸出」。  在此，我們使用一個「OLE DB 命令轉換（OLE DB Command Transformation）」元件在處理這些不符 New Record 條件的資料。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggT2P6pv-UuX_O7y3yVcPqc9Lh-hJC_VgmQI8zqZ_oGHPjWNjTFY-iQVaDxMJd1fDt_-D7S9my3zVucaPp3cyIjLsdvJ1r6wN-dVMkajEbIaTTh3OIWHmO5ionF3L_aO5p_0SyZ67lwwI/s0/ssis-lab4-51.png)

「OLE DB 命令轉換」元件會針對資料流程中的每個資料列執行 SQL 陳述式。  使用「OLE DB 命令轉換」元件時，有幾個可用的設定如下：    

- **SQL Statement**：Provide the SQL statement that the transformation runs for each row.
- **Time Out**：Specify the number of seconds before the SQL statement times out.
- **Code Page**：Specify the default code page

設定 SQL 陳述式

OLE DB 命令轉換的 SQL 陳述式通常包含參數。參數值由轉換輸入提供。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjavq3S3ci3HM5VPm_hVDfZY_uFG3WpoXBIwAOJh7B3M75nZ7V-DaLO-0OyhfQWc-6j8PdewUL04x_qXVJ-m1tByWZXPZx1jXPg92mkzRwPWDTFN0RDgsWBHfPrVCH-YB9NTb4_Qj9v0Pc/s0/ssis-lab4-49.png)

設定參數：

OLE DB 命令轉換提供了參數名稱，您無法對它們進行修改。 這些參數名稱為 Param\_0、Param\_1 等。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHuxC-GtdVGTJG5HMIdfyP3NkhU8eLpDmpADf6W5qaP1GQMfLlqlx6CareGb-wIMWgRaPP0mdnbHXOFTqXIkpRfvRXsErBa6hZ8JKNUH00i9lN9kFbZG6zIHzq5HjLnfv5LkhU2Wo_3DM/s0/ssis-lab4-50.png)

## 執行封裝

如果我們先執行以下 SQL ，加入了二筆客戶資料，再執行封裝，就會看到以下結果
```sql
insert stg.Person(BusinessEntityID, PersonType, FirstName, LastName, ModifiedDate) values ('20778','AD','Vito','Shao',GETDATE())
insert stg.Person(BusinessEntityID, PersonType, FirstName, LastName, ModifiedDate) values ('20779','AD','Peter','Yun',GETDATE())
insert stg.Customer(CustomerID, PersonID, ModifiedDate) Values ('30119','20778',GETDATE())
insert stg.Customer(CustomerID, PersonID, ModifiedDate) Values ('30120','20779',GETDATE())
```

二筆新增，其餘的會做更新。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNrlbduSt5lcEtQaymTpWlormcG1Fj3oNLyD9TroBI-5r6lqwLc7Td1BgnrNkm2q2HwoDQxvt4c-8Mz2qEa2rZqGnldFAabd8nT3y6tWYMGyItHqVUY6oilmhdayNM5n_W3EE2SMxU1p4/s0/ssis-lab4-52.png)