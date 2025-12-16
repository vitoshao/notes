---
title: 資料品質專案
layout: default
parent: DQS
nav_order: 4
description: "資料品質專案"
date: 2014-01-28
tags: [SQL,Data Quality Service]
postid: "2034176871922913468"
---
在安裝 [Data Quality Services](http://technet.microsoft.com/zh-tw/library/ff877925.aspx) 之後，你就可以開始使用 DQS 提升資料品質。  DQS 中的「**資料品質專案**（Data Quality Project）」是一種改善來源資料品質的一種專案，  它會利用**知識庫**（KB）中的資訊，執行「**資料比對（matching）**」和「**資料清理（cleansing）**」活動（Activity），然後將執行結果匯出到 SQL Server 資料庫或 .csv 檔案。   

下表先簡單整理解決「資料清理（cleansing）」「資料比對（matching）」問題，可用的工具：

- 資料清理  
DQS cleansing project 、 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 、 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 。
- 資料比對  
DQS matching project 、 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 、 [Fuzzy Grouping Transformation](http://technet.microsoft.com/en-us/library/ms141764.aspx) 。

# 建立 DQS 知識庫

#### 知識庫與定義域

- DQS 知識庫是資料管理人或 IT 專業人員所建立的中繼資料儲存機制，它儲存了與資料相關的知識，可用於資料清理和資料比對程序上，以提升資料品質。
- DQS 知識管理包括以**電腦輔助**方式和**互動方式**建立並管理知識庫所使用的處理序。
- DQS 知識庫是由**定義域**所組成，每個**定義域**則對應到資料來源的資料欄位。

## 建立知識庫（Knowledge Base）

您可以同時使用電腦輔助和互動式程序來建立、建置和更新 DQS 知識庫。  底下是建置 DQS 知識庫所包含的幾個程序與元件：   

- **知識探索**（Knowledge Discovery ）  
知識探索是一種電腦輔助程序，它會分析你所提供的樣本資料，以便建立該資料的知識。  有了分析結果，你可以驗證並強化知識，再將其套用，以便執行資料清理、比對和分析。
- **定義域管理**（Domain Management）  
定義域管理處理序是一種互動式程序，可讓資料管理人驗證並修改知識庫定義域中的知識。
- **Reference Data Services**  
你可以透過參考資料提供者（reference data provider）來驗證資料，以維護並保證的資料的正確性。   您可以使用 Windows Azure Marketplace 的服務來連接到參考資料提供者，也可以使用提供者的直接連接。
- **比對原則** （Matching Policy）  
比對原則會包含用來執行刪除重複資料作業的比對規則。   比對原則處理序可讓您建立比對規則、根據比對結果和分析資料進行微調，以及將原則加入至知識庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5S97csXGdRXJPp3BNe8gLeBfXTKWmxAX920GK-Ak5-LEawZGi8_LNeRNH3xoQAcBFB_0v0Z8w2GNkg8jwOBPr8OLG17aOIAr3bSa0zfZPLC967EwfNacXXegYrMiLcxTrlDJ9zlEqMcE/s0/ssis-kb-and-domain.gif)

## 定義域管理（Domain Management）

定義域都對特定欄位設定了各自的分析語法。

# 建立資料品質專案

## 資料品質專案

在有了**知識庫**之後，你就可以使用**資料品質專案**，去驗證或者清理資料。  **資料品質專案**並不能直接修改來源資料，而只會將執行結果匯出到 SQL Server 資料庫或 .csv 檔案。  你可以利用匯出的資料，直接使用 TSQL 自行更新資料來源。  

**資料品質專案**包含二個專案類型：「清理」和「比對」。

- **清理活動（cleansing activity）**：找出不正確的資料  
資料品質清理專案可讓您根據知識庫來清理來源資料。  你可以使用**電腦輔助清理程序**或者**互動式清理程序**來找出資料中不完整或不正確的資料，然後進行更正以得到正確有效的資料。
- **比對活動（matching activity）**：找出重複的資料  
資料品質比對專案可讓您根據知識庫中的比對原則來執行比對活動，藉由識別完全相符和大致相符來避免資料重複，藉此讓您移除重複的資料。  通常執行 DQS 作業，會先執行資料清理活動，然後再執行資料比對活動。

## 資料清理

資料清理會在四個階段執行： 對應階段、電腦輔助清理階段 、互動式清理階段 、最後的匯出階段 。  

以上每一個程序都是在清理活動精靈的個別頁面中執行，好讓您來回移到不同的頁面、重新執行此程序，並退出特定清理程序，然後返回該程序的相同階段。   DQS 會提供有關來源資料和清理結果的統計資料，好讓您做出有關資料清理的明智決定。   

- 對應階段：此階段用來設定欲清理的資料與知識庫定義域的對應。
- 電腦輔助清理階段：將要清理的資料執行自動清理程序。該程序會依知識庫定義域，執行資料變更或提供建議的變更。
- 互動式清理階段：在互動式清理階段中，您可以查看 DQS 所建議的變更，然後決定是否要核准或拒絕變更來實作變更。
- 匯出階段：此階段可讓您匯出已清理的資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizKhmqsJ_Z93EQJWXSn6-M4YR9tigeUS2_a0oLqT8kelzUtzpALQBsq1tt-zg4_Z5AkzLHi31DFt8Q8GNC_mttRyMcSaGBXkyn9P9L4SAqF2lWQWeVSQ9qsr3AQYuePhZgspkKqmIyNAA/s850/ssis-data-cleanse-1.png)

你可以在[一般設定]頁籤中，指定資料清理和資料比對的臨界值：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_RvLvVDQKtTYjoo7mGz0t-1NkcBD4CBhvNKs0CB7gt2SO_LKNzI8VmMg6TtQDGU038Z09tztYzeWvZhdbotxEcdcOA9oTM13zkQaL1MINhna2AyTtSAtnsFqNKoPAg2Aks61EaUX17B4/s0/ssis-data-cleanse-3.png)

DQS 清理程序會依據這個信賴等級門檻，將資料分成五個狀態：

- 更正（Corrected）：該值的信賴等級高於**自動更正臨界值**，所以在自動清理程序期間會自動更正定義域值。
- 建議（Suggesion）：該值的信賴等級高於**自動建議臨界值**，但低於**自動更正臨界值**。
- 新增（New）：DQS 沒有足夠的資訊，因此無法對應至其他任何索引標籤。
- 無效（Invalid）：不符合定義域規則的值。
- 正確（Correct）：顯示已發現正確的定義域值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeXP6DbTj0ZxZfAPI_fEGxlBu0P6ByDGpDepwAfQCF-I9hfH98NwyAUzqUVDkF0pkcLJ6bN1nV17-UGBZDj607gpGB2gCdsFgRX2-9N-1RS8XNVzZMTYV_wf1D5NUVWxHDaEiUmuHhIw0/s850/ssis-data-cleanse-2.png)

## 資料比對

請看下一篇。

# 分析資料與提升資料品質

上面提到的「資料品質專案」，它是利用知識庫來協助分析資料，以達到資料品質的目的。  

其實你也可以使用任何 SQL 所提供工具程式來分析資料，進而改善資料品質。  例如使用 T-SQL 查詢，或者在 SSIS 中，使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 來分析資料。  

## Using Queries

Transact-SQL is a very powerful language. You can use it to find incomplete and inaccurate data.

## Using Data Profiling Task

[Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 是 SSIS 封裝中的一個簡單的分析元件，它可以將分析結果儲存成 XML 格式檔案的變數，接著你可以使用 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 與這個分析結果來自訂程式碼。  另外，你也可以使用「資料設定檔檢視器」（Data Profile Viewer）這個工具程來檢視這個設定檔。  

- The [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) is a task that you use to configure the **profiles** that you want to compute.
- You then run the package that contains the [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) to compute the **profiles**.
- **A data profile** is a collection of aggregate statistics about data that might include the following.
- The task saves the **profile** output in XML format to a file or a package variable.

#### 設定檔類型（Profile Type）

你可以使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 進行以下八種分析工作：

1. **資料行長度散發設定檔（Column Length Distribution Profile Request）**：  
報告該欄位中的資料各種長度的數量與佔比。
2. **資料行 Null 比例設定檔（Column Null Ratio Profile Request）**：  
報告該欄位中的資料NULL值的佔比。
3. **資料行模式設定檔（Column Pattern Profile Request）**：  
報告該欄位中的資料包含那幾種正規表示式。
4. **資料行統計設定檔（Column Statistics Profile Request）**：  
對數值欄位，可以報告最小值、最大值、平均和標準差；對日期欄位，可以報告最小值和最大值。
5. **資料行值散發設定檔（Column Value Distribution Profile Request）**：  
報告該欄位的所有相異值，以及該資料表中每個值所代表之資料列的百分比。
6. **候選索引鍵設定檔（Candidate Key Profile Request）**：  
報告資料行或資料行集合是否為選取之資料表的索引鍵或近似索引鍵。
7. **功能相依性設定檔（Functional Dependency Profile Request）**：  
報告某個資料行 (相依資料行) 中的值相依於另一個資料行或資料行集合 (行列式資料行) 中之值的程度。
8. **值包含設定檔（Value Inclusion Profile Request）**：  
計算兩個資料行或資料行集合之間值的重疊。

PS. 前五種是針對個別資料行，後三種會分析多個資料行或資料行與資料表間的關係。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjO0gVyW8EfU2N7VWazHt1t8I_2fm9hyphenhyphenSS-huNlLSwxdY8oDRG8tzpFGqFkQTOL354ivk7LKSziVUckKX-dmol-068tB15xEndriTn8R2hSCOFEsE8fY823VQm8MwovoustxvxpLunRT4/s0/ssis-data-profiling-task-profile-type.png)
## 參考資料  

- [資料品質專案 (DQS)](http://technet.microsoft.com/zh-tw/library/hh213052.aspx)
- [建立資料品質專案](http://technet.microsoft.com/zh-tw/library/hh510393.aspx)
- [DQS 知識庫與定義域](http://technet.microsoft.com/zh-tw/library/gg524799.aspx)
- [資料清理](http://technet.microsoft.com/zh-tw/library/gg524800.aspx)
- [資料比對](http://technet.microsoft.com/zh-tw/library/hh213071.aspx)
- [DQS 中的 Reference Data Services](http://technet.microsoft.com/zh-tw/library/hh213066.aspx)
- [建立定義域規則](http://technet.microsoft.com/zh-tw/library/hh510397.aspx)
-