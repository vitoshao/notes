---
title: 安裝與維護 Data Quality Servies (DQS)
layout: default
parent: DQS
nav_order: 1
description: "安裝與維護 Data Quality Servies (DQS)"
date: 2014-01-22
tags: [SQL,Data Quality Service]
postid: "2026166854299285696"
---
#### 企業資訊管理解決方案 (EIM) 

SQL Server 2012 在單一產品中提供了有效企業資訊管理 (EIM) 解決方案所需的所有元件。  可幫助您建置 EIM 解決方案的主要 SQL Server 2012 元件包括： 

- SSIS : SQL Server Integration Services  
提供強大的 ETL 功能，整合各種不同的來源資料。
- MDS : SQL Server Master Data Services  
該服務可用來維護一個精確且集中式的資料集，確保主要資料在不同應用程式之間的完整性和一致性。
- DQS : SQL Server Data Quality Services  
該服務可讓您清理、比對、標準化及充實資料，籍此維護資料的品質，並確保資料適合其商業用途。

#### 什麼是 Data Quality Services (DQS)

- DQS 是一種利用**知識驅動**(knowledge-driven)以提升資料品質的服務。
- DQS 可讓您建立**知識庫**，然後使用該知識庫執行各種重要的資料品質工作，包括更正(correction)、充實(enrichment)、標準化(standardization)及刪除重複(de-duplication)資料。
- 你也可以在 DQS 中使用**雲端參考資料**當作**參考資料提供者**，以更執行**資料清理**。
- DQS 也提供您整合資料品質工作(data-quality tasks)，讓您分析資料的完整性。

DQS 包含 Data Quality Server 和 Data Quality Client，兩者都會安裝成 SQL Server 2012 的一部分。  

- Data Quality Server：一個 SQL Server 執行個體功能。其中包含具有資料品質功能與儲存的三個 SQL Server 目錄。
- Data Quality Client：一個 SQL Server 共用功能。  操作者（商務使用者、資訊工作者以及 IT 專業人員）可以使用該功能來執行電腦輔助的資料品質分析（自動），或者以互動方式來管理資料品質（人工）。

# Data Quality Problems and Roles

Master Data (主檔資料) 指的是企業內部最主要的資料檔，例如客戶資料、產品資料、員工資料等等。  所以談到 Data Quality 就會關連到 Master Data Management (MDM) ，因為 MDM 方案最主要目的就是要提升 Master Data 的品質。  

Before you define your data quality activities, you must decide for which aspects you will measure and improve the quality of your data.       ***Data quality dimensions*** capture a specific aspect of general data quality.       ***Measuring data quality***, also known as ***data profiling***, should be an integral part of the implementation of an MDM solution.  

## Data Quality Dimensions

**Data quality dimensions** can refer to data **values** or to their **schema**.  You can measure some data quality dimensions with tools such as Transact-SQL queries.  Measurable dimensions are called **hard dimensions**.  In contrast, some dimensions depend on the perception of the users of the data; these are called **soft dimensions**.  

You cannot measure soft dimensions directly; you can measure them indirectly through interviews with data users or through any other kind of communication with users.  This lesson describes hard dimensions in detail and takes only a quick overview of soft and schema dimensions.  

### Data Quality Hard Dimensions

下面幾個 Dimemsions 都屬於 Hard Dimemsions

#### Completeness（完整性）

- Completeness is one of the easiest dimensions to measure. You can start measuring it on a population level.（母體）
- In an open-world assumption, you can get a reference relation that contains the entire population to state the population completeness.
- In a closed-world assumption, You could measure the following completeness to semi-contructioned columns (xml data type columns).
- attribute completeness：the number of NULLs in a specific attribute
- tuple completeness：the number of unknown values of the attributes in a tuple.
- relation completeness：the number of tuples with unknown attribute values in the relation.
- value completeness：makes sense for complex.

#### Accuracy（精確性）

Accuracy is a complicated dimension. First, you have to determine what is inaccurate.

- duplicate values are inaccurate
- you can extract data that is only potentially inaccurate.
- A value with very low frequency is probably incorrect.
- For strings, you cans earch for **string length** distribution
- For strings, you can also find patterns and then create **pattern** distribution.
- For continuous attributes, you can use **descriptive statistics**.

#### Information（資訊）

Information Theory, defines entropy as the quantification(定量) of information in a system.  You can measure　entropy on a column and table level.  

Information is not a direct data quality dimension; however, it can tell you whether your data is suitable for analysis or not.

#### Consistency（一致性）

Consistency measures the equivalence of information stored in various databases.   No matter how you find inconsistencies, you can flag them and then measure the level of inconsistency on the column or table level.

### Data Quality Soft Dimensions

The following list includes some typical soft dimensions:

- **Timeliness** :
- **Ease of use** :
- **Intention** :
- **Trust** :
- **Presentation quality** :

### Data Quality Schema Dimensions

The following list are the most important schema dimensions:

- **Schema completeness** :
- **Schema correctness** :
- **Documentation** :
- **Compliance with theoretical models** :
- **Minimalization** :

## Data Quality Activities and Roles

...

# Installing Data Quality Services

DQS 服務提供的**資料品質方案**可讓資料管理人或 IT 專業人員維護其資料的品質，並確保資料適合其商業用途。    DQS 是知識驅動方案，可同時提供電腦輔助和互動式方法來管理資料來源的完整性和品質。   DQS 可讓您探索(discover)、建立(build)以及管理(manage)資料的相關知識。   然後您可以使用該知識執行資料清理(cleansing)、比對(matching)和分析(profiling)。   您也可以在 DQS 資料品質專案中使用參考資料提供者的雲端式服務。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBFfWP04HTzNO4u6QSS2BztMPNUYl-PQabjw7etP3znOghC_J5qdIk8axMc6QIDuHTtLJrM2xF11EzXtmDv4GAJf_swVWCyt_4f1X2eNRQMMb9xtfcnFp4jb00jkmlQzp-nfbCurXVUEU/s0/ssis-dqs-process.gif)

## DQS Architecture

在安裝前，必須先知道一下 DQS 的架構，它包括 **Data Quality Server** 和 **Data Quality Client** 二部份。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcLBIj1IhIkyOBqGHxxHTmZeDjJ90neSRwOm0Y11oMVCnYJlQwdPT6hjBIwpLdjktgEyUr5JP6LrHxuriWZaUsSuw0rO_38fsNjD20Gi1Q4GXBD023yt4Hy_au8GaVm1UCFnt32UuXfYk/s0/ssis-dqs-01.png)

#### Data Quality Server

Data Quality Server 由三個資料庫實作，您可以在 SSMS 中管理並監控這些資料庫。

- **DQS\_Main**：包含 DQS 預存程序、DQS 引擎和已發行的知識庫。
- **DQS\_Projects**：包含知識庫管理和 DQS 專案活動所需的資料。
- **DQS\_Staging\_Data**：此為中繼暫存資料庫。您可以將來源資料先複製到中繼暫存資料庫以執行 DQS 作業，然後再將已處理的資料匯出。

#### Data Quality Client

Data Quality Client 是一個獨立應用程式，可讓您執行知識管理、資料品質專案，以及一個使用者介面的管理工作。  它是獨立的可執行檔，會執行**知識探索**、**定義域管理**、**比對原則建立**、**資料清理**、比對、分析、監控和伺服器管理。   Data Quality Client 可以與 Data Quality Server 在同一部電腦上安裝及執行，也可以在另一部電腦上遠端安裝及執行。

## DQS Installation

### 安裝前準備工作

#### 安裝 Data Quality Server

Data Quality Server 是 SQL Swever 2012 的新功能，  若要安裝 DQS ，必須使用 SQL Server 2012 的 Database Engine，且記憶體最低需求為 2GB ，建議 4GB。  

#### 安裝 Data Quality Client

若要安裝 Data Quality Client，系統中必須有 **.Net Framework 4.0** (若沒有，會在安裝 Data Quality Client 期間加以安裝) 。  而且瀏灠器至少 **Internet Explorer 6.0 SP1** 。  

#### 其他注意事項

- Data Quality Server 和 Data Quality Client 可以安裝在同一部電腦或不同的電腦上。
- DQS 無法搭配舊版的 SQL Server 使用。
- SQL Server 2012 累計更新 1 (CU1) 版本已開始支援 DQS 做為叢集 SQL Server 2012 安裝的一部分。

### 安裝工作

DQS 元件包含在 SQL Server 2012 的安裝程式中，你可以在安裝步驟中的[特徵選取]（Features Selection）頁面中選取以下功能：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6CP61dJ_8xtvgevm0TfigT1-6kv_-0BjrTXNC05QOZjTvEr-Tsxyuz_k0m58f8PaVR0a7mu6v9yvrn7vWgtZZNV0ocakaPPlZd310p464tvA9nePKT4pzgRsf_VqAQEX_mGBD09wrY7w/s0/ssis-dqs-02.png)

### 安裝後工作

如果只需要 DQS Client ，那麼安裝結束就可以啟用了。  如果是要安裝 DQS Server ，那麼在安裝後，還必須建立 DQS Database 才算完成。  在 DQS 的安裝過程中，安裝程式會在 SQL Server 執行個體的目錄中安裝一個 **DQSInstaller.exe** 檔案，  你必須在 SQL Server 安裝精靈結束之後，執行 **DQSInstaller.exe** 這個檔案，才算完成 DQS 資料庫的建制。  而這個 DQS Installer 會完成以下內容的建置：

1. **建立 DQS 資料庫**：  
安裝檔會在資料庫中建立下列三個資料:DQS\_MAIN、DQS\_PROJECTS、DQS\_STAGING\_DATA。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5_G3qk1kjigqd6snH89oMluL0g6fAt6PaQcysqSQDSPpJZ31eluNgkAeaOaz1cJ75eT3ibzZSVPZ8_j2barAhrhv2_5CFnrUQC83cz4wmbEx-9zZBOHmczx44kJuPTSLk1_B0ZoTGYss/s0/ssis-dqs-10.png)
2. **建立 DQS 登入**：  
建立 DQ Server 需要用到的２個登入：##MS\_dqs\_db\_owner\_login##、##MS\_dqs\_service\_login##  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsNFgmok3ksTMwX04FhERvHOfBcHfdX7gHlqjzZXWXMv3-1g1dwf-0NYrrgQRx2qOmgPrTWZH08CFdtBo0bN95Fi0O_2vlrzA_DXBYBYfuxL7JAk5tMkq-O2jTVVvKP1nayDRRlkvELVM/s0/ssis-dqs-11.png)
3. **建立 DQS 角色**：  
在 DQS\_MAIN 中，建立３個不同管理權限的角色：dqs\_administrator、dqs\_kb\_editor、and dqs\_kb\_operator.  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi43Bv_snulRax9XV3eYUoOe61lRBGSGGZWvBAFhyphenhyphenpMsbYKb7MPKOMoy9VTy1lt9oersOaNEtez3WS1eToFPwSoqm4bANzArr6b8AjojNNnaK1jla8FJr8bxlpr1X1Zeq1j8NKHm08saf0/s0/ssis-dqs-12.png)
4. **建立 DQInitDQS\_MAIN 預存程序**：  
安裝檔會在 master 資料庫中建立 DQInitDQS\_MAIN 預存程序，用來執行 DQS 資料庫的初始化工作。
5. **建立 MDS 使用者**：  
如果 Data Quality Server 安裝的 SQL Server 執行個體包含 Master Data Services ，  則安裝程式會自動建立對應到 MDS 登入的使用者，並授與該使用者 DQS\_MAIN 資料庫的 dqs\_administrator 角色權限。

### DQSInstaller.exe

你可以由以下二個地方來執行 DQSInstaller 。

1. 由功能選單執行

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-yWsTTbcVOMMVDnKxGygApYks2yROldrNqgNqKz5PBxo504PYisSfMhIdZsLyAHQvU7cJ9Un5a7ShTLqPBnppqwsZ3wST55ruL9fq2ufxPlouBY1asuE33ITQbgbOlyJKcv9vKovHo_A/s0/ssis-dqs-03.png)

2. 由檔案系統執行

檔案存放在執行個體安裝目錄：C:\Program Files\Microsoft SQL Server\MSSQL11.＜instance\_name＞\MSSQL\Binn

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgU61nhPFkDI7reo8B37DmqwhUhm9zpbT2j9x3IcGD8NaPcLXmDZpTwKDxbBnCXIps2xlDa59D8VUfm0LNYPVFkAQn7tT9Ktpi3gkMpR5R_eQgafagrfHnJ_vZ7ZV3i4HZil48CaB-HGmM/s0/ssis-dqs-04.png)

### 驗證連線

整個 DQS 安裝完成後，你可以執行 DQ Client ，看看是否可以連上 DQ Server 。

1. 執行 DQ Client

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhsc75PU4TgEBY3cSPbrC8daYGIPnh1mj-0BVQ56ivif04EjqN82M4RYvgz4wWUQgCPB_9ir3pVcn2rehFMXx66XCZao3b6tR84q2psSozikcA7mapzwm9skV21W878OUeNYDuKSPEbz70/s0/ssis-dqs-05.png)

2. 連接到 DQ Server

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVMZ2_P1l1iAfS6fAKk1BJdVevlmpKUxwi3I5Lb4lvja9C-QjYkJ0XPFN8VH0uFequBpE6wgLNjvg2o0-YpjBPPUkjyTaCmR4oFNMlxWp7Uo_CFWswomnDNYzJkQYfdhXOfGwdARuzfjM/s0/ssis-dqs-06.png)

3. DQ Client 主視窗

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxu71K7B6QDgcsEcYCkDb4aFi15ChCNTOOsWmFi6V4LCUtA_5HF94EiuCff1WT28uQHvS1t7x7dwU8c6380_HGdit-g93BDIaMUD_h1RcOR-DY410lBA_jtp6Ln7P4DpD4BHYRWDMh__Q/s850/ssis-dqs-07.png)

4. 查看 DQ Client 版本

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEheB5EzGVtvtO3ffRywXz5ye9DWUzNByNDUGAaTiypZVyByX4_c3Xh4kjBh37eT9LtHcAFWuppq14N_zA9yF7Z6mSMbCUTOgbXaB7Ki-RZO5DL1k6SGPySZOM7hlqDrHrYjWdVlt4JkhwY/s0/ssis-dqs-08.png)

5. 顯示 DQ Client 版本

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJuE1ZjrDQQLReiHTLya8Cpgcb6UsNm0EMqjgGGQMomU4LbZjezSvjziwqSY5Kw0lKtccQSpV0IvuGeZlBbqz6U7BpU9gV7gS9seuTfCVBHH9S98oJRBJKho7ycjv6_0Y_gC8WsHSxBkc/s0/ssis-dqs-09.png)

# Maintaining Data Quality Services

DQS 的管理工作包括：

- DQS 安全性維護
- DQS 資料庫備份
- 組態設定
- 監控 DQS 活動
- DQS 記錄
- 設定清理與比對的臨界值

下面內容將介紹如何使用 Data Quality Client 或其他工具來操作這些管理工作。

## Using Data Quality Client

管理 DQS 最簡單的方法就是使用 Data Quality Client 這個工具，它提供了大部份管理 DQS 的功能，例如：

- 監控 DQ Server 上的各種 DQS 活動
- 設定 Reference Data Service (例如設定與 Windows Azure Marketplace 的連線，或 3rd Party 的參考資料服務提供者。)
- 設定清理（cleansing）與比對（matching）活動的臨界值。
- 啟用/停用 Data Quality Client 中的通知。
- 依事件的嚴重性層級（severity level）設定記錄（log）

### 參考資料

你可以在主視窗的[管理]窗格中點擊[組態]按扭，以開啟管理功能介面。預設頁籤是[參考資料]，如果你要使用**線上 DataMarket 參考資料**，可以輸入一組帳戶識別碼。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiY5DsJHSCkvRuJFsNdFsaVWf1BXzzNANCFf9lEbw0RQlxSBV8MQPrAfokr1BHQtm-cKGVwCGc6GR5MbVTLbhNTUdOdJNyHYfwdpaYsekMSVlEnKc_wkbBE3T0rWBlOu4UG7BTSW_l53po/s850/ssis-dqs-14.png)

如果還沒有帳戶識別碼，可點選[建立DataMarket帳戶識別碼]連結，註冊登記。之後你就可以在 Datamarket 上訂閱你要的參考資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLMXhjxhkH-2zggsDPMTCWj03zfktlDxcQ3Zr3xKhu4sqHBJM61nCmnYYY-kiTKEQK4j2qOLPcpS8akDVvlLvlUBhPmMOZdetFXCtXcapnB5JzjgObVfRVTss_ecL4NzvlLz9hov6Y9nM/s850/ssis-dqs-15.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9R7qVbH1yLtPgwECRY4i84a2nRQQVQrjvCIugYQ0HvmDBWek144Elo0_5r1xXBJs4I2q7ao97NO-SyALHbVG2NKflK8sZ0nn5NEsaqn8cmIlVaHEmgqL_9di6lyi1PN1qt52wbDwhm_w/s850/ssis-dqs-16.png)

### 一般設定

你可以在[一般設定]頁籤中，指定資料清理和資料比對的臨界值，以及是否在 Data Quality Client 中啟用分析所使用的通知。    這些臨界值（threshold value ）會使用在執行資料品質專案的時候，讓 DQS 在執行清理和比對時參考。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdLkkgWbrUjwGAvwDfAGDFbGmET6XY1VkgA0B58umlB3_jLkic5b5-EbqDbQUTiyhvfgnUN-v8QXTqM4AEowFMn3Yw0P7oyII1O0rRQSqNQJuRRM-hbFECRTgznUxGtC81nU6xw7l2JPo/s0/ssis-dqs-17.png)

- **建議最低分數（Min Score For Suggestions）**：  
設定 DQS 在執行**清理程序**時，提供建議的信賴等級。該值比須小於等於[自動更正的最低分數]。預設值是 0.7 。  
例如，輸入 0.75（75%），表示若比對資料時，其信賴度大於 0.75，則應該使用[建議]。
- **自動更正最低分數（Min Score For Auto Corrections）**：  設定 DQS 在執行**清理程序**時，提供自動更正的信賴等級。  
例如，輸入 0.9（90%）表示若比對資料時，其信賴度大於 0.9，則使用[自動更正]。預設值是 0.8 。
- **Min Record Score**：  
設定 DQS **比對程序**的臨界值。  這個值表示一筆資料與另一筆資料 match 的最底程度。預設值是 80%。

### 記錄檔設定

DQS 中的記錄檔會記錄在 DQS 中執行的活動，您可以篩選您要針對各種 DQS 功能記錄的訊息，也可以根據事件的嚴重性層級篩選 DQS 模組。  

Data Quality Server log file 預設位置：C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Log\DQServerLog.DQS\_MAIN.log

Data Quality Client log file 預設位置：%APPDATA%\SSDQS\Log folder\DQSSSISLog.log

#### features

- 定義域管理（Domain Management）
- 知識探索（Knowledge Discovery）
- 清理專案（Cleansing Project）
- 比對原則和比對專案（Matching Policy And Matching Project）
- RDS（Reference Data Services）

#### severity level

- 嚴重錯誤（Fatal）
- 錯誤（Error）
- 警告（Warn）
- 資訊（Info）
- 偵錯（Debug）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMrW1b5-nLLdBIfVGHSYo7BztjE4aKFwE_x2ZiY_23-8o2NVu6EA9_BkWyegjlklqIooZD8ebBgg-FS8h9yWgfI0yXHmmZvmEZdxEGonLMkY7_af9l7-tsYZBabW2EBFDGc93jakLqTFE/s0/ssis-dqs-13.png)

#### Advanced section

[記錄檔設定] 索引標籤中的 [進階] 區段可讓您在模組層級（modular level）設定記錄嚴重性設定  模組表示某項功能(feature)中的各種不同功能，也就是你可以更細部的設定各個模組功能的嚴重性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi19nF-kGrlhoAc9FlYEvOg9Mn_G8DHW-IheDXvDCxUlp38M7D09FPVHf8KodHtVcPRL8NU2x-ZpMcAAbInW90Ig3ah6lLH0HnvyFEwT5lx20lTEqZZBrufIvK6F7HxCX5C5rejBi3ZLTI/s0/ssis-dqs-18.png)

## DQS的安全性設定與備份

### 安全性設定

DQS 有四個角色。  其中一個是資料庫管理員 (DBA)，他主要負責處理產品安裝、資料庫維護和使用者管理。  這個角色主要是使用 SSMS，而不是 Data Quality Client 應用程式。  

另外三個在 DQS\_MAIN 資料庫中事先定義好的角色分別是：  

- **DQS 系統管理員（DQS Administrator ,dqs\_admininstrator 角色）**：  
可以在產品的範圍內執行任何工作。  
例如編輯和執行專案、建立和編輯知識庫、終止活動、停止活動內的程序，也可以變更組態和參考資料服務設定。  但是 DQS 管理員無法安裝伺服器或新增使用者。 這必須由資料庫管理員來執行。
- **DQS KB 編輯者（DQS KB Editor ,dqs\_kb\_editor 角色）**：  
可以執行管理以外的所有 DQS 活動。  
例如編輯和執行專案以及建立和編輯知識庫。   他們也可以看到活動監控資料，但不能終止或停止活動或是履行管理職責。
- **DQS KB 操作員（DQS KB Operator ,dqs\_kb\_operator 角色）**：  
可以編輯和執行專案。  
他們不能執行任何種類的知識管理，也不能建立或變更知識庫。   他們可以看到活動監控資料，但不能終止活動或履行管理職責。

Permissions for the roles are cumulative.  The dqs\_kb\_editor and dqs\_administrator roles are members of the dqs\_kb\_operator role,  The dqs\_administrator role is also a member of the dqs\_kb\_editor role.  You can manage DQS security by adding users to appropriate DQS roles.

### DQS 資料庫備份

你可以使用一般的備份還原功能來備份 DQS 資料庫，  其中 DQS\_MAIN 和 DQS\_PROJECTS 是必要的，而 DQS\_STAGING\_DATA 就看有沒有使用到暫存資料。
## 參考資料  

- [Data Quality Services 簡介](http://technet.microsoft.com/zh-tw/library/ff877917.aspx)
- [Data Quality Services 概念](http://technet.microsoft.com/zh-tw/library/hh213015.aspx)
- [SQL Server 2012 資料品質服務實戰簡介](http://fecbob.pixnet.net/blog/post/38990545-sql-server-2012-%E8%B3%87%E6%96%99%E5%93%81%E8%B3%AA%E6%9C%8D%E5%8B%99%E5%AF%A6%E6%88%B0%E7%B0%A1%E4%BB%8B%EF%BC%88data-qualit)
- [安裝 Data Quality Services](http://technet.microsoft.com/zh-tw/library/gg492277.aspx)
- [Configure Severity Levels for DQS Log Files](http://technet.microsoft.com/en-us/library/hh510405.aspx)
- [DQS 安全性](http://technet.microsoft.com/zh-tw/library/hh213045.aspx)
- [設定清理和比對的臨界值](http://technet.microsoft.com/zh-tw/library/hh510415.aspx)
- [DQS 管理](http://technet.microsoft.com/zh-tw/library/gg524797.aspx)
- [備份及還原 DQS 資料庫](http://technet.microsoft.com/zh-tw/library/hh213068.aspx)
- [卸離和附加 DQS 資料庫](http://technet.microsoft.com/zh-tw/library/jj572864.aspx)
- http://technet.microsoft.com/zh-tw/library/hh213040.aspx