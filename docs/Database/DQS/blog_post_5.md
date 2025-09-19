---
title: 資料重覆問題
layout: default
parent: DQS
nav_order: 5
description: "資料重覆問題"
date: 2014-01-29
tags: [SQL,Data Quality Service]
postid: "7227469397552355650"
---
在企業中，主要資料（ Master Data）的來源常常會經由不同系統匯入，此時最大的挑戰就是「**識別對應**（identity mapping）」和「**資料重複**（de-duplication）」問題。  

例如：來自不同系統的客戶資料，他們使用不同的識別碼，但卻表示同一位客戶，在建置主資料時，你該如何識別它們，以剔除這樣子的重複性。  另一種狀況是像名字或地址這類資料，常常因為輸入打字的原故，彼此之間有些微的不同，但是卻又代表相同的資料，在建置主資料時，也應該要剔除這樣子的重複性。  

# 識別對應與資料重複問題

## 問題在哪裏

如果要比對二個不同系統的資料，若沒有共同的識別欄位，要如何比對呢？  這時大概只能採用資料的相似度（similarity）來判別，例如由姓名、地址、Email等屬性著手去判別出相同的資料。  

要比對二個資料來源，就會有以下問題：

#### 識別對應問題

下圖的上半部，表示二個資料來源，它們分別使用不同的鍵值，不同的資料。而下方則是二個資料表的 CROSS JOIN 結果。  這個合併後的表格，主要用來表達任意二筆資料的相似度。  

你可以依相似度值高低來判斷他們是否有匹配。有了這層關係就可以用來進行資料的更正(cleansing)。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0qkGXKsWSNsbdCx7_wgsjl8yY0CfZe8SaUddrojFBXwMHpYPJXIPq96WokAN4P9qGb_3Xca-BhMpe0kxEs91sR_DQRzHftQy55d4d_wfxF7DWMZK2-5JihB9JnadjBctJGJY_6O2ngMA/s0/ssis-identity-mapping-problem.png)

#### 資料重複問題

另一個與識別對應相似的問題就是刪除資料重複的問題(de-duplication)。  它其實和上一個問題差不多，只不過比對的對象是來自同一個資料來源。

## 如何解決問題

要進行這類相似性比對，自然會需要用到相關的演算法。在這裡當然不會討論這些演算法，只要知道 SSIS Fuzzy Transformation 是使用微軟自行研發且非公開的演算法進行相似比對。  

- 在 DQS 中，你可以直接執行刪除重複資料的作業
- 在 SSIS 中，  你可以使用 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 解決「識別對應問題」；  使用 [Fuzzy Grouping Transformation](http://technet.microsoft.com/en-us/library/ms141764.aspx) 解決「資料重複問題」。

# 「DQS 」與「DQS 清理轉換」

想要減少資料重複的問題，就必須要使用近似比對（approximate matching）。以下工具都可以達到這個效果：  

- 在 SSIS 的封裝中，你可以使用 [DQS Cleansing Transformation（DQS 清理轉換）](http://technet.microsoft.com/zh-tw/library/ee677619.aspx) 來識別資料中可能重複的資料列。
- 在 DQS 的專案中，利用比對原則知識庫來執行刪除重複資料作業。

## DQS 清理轉換（DQS Cleansing Transformation）

在 SSIS 封裝的 Data Flow 中，我們可以使用 [DQS Cleansing Transformation](http://technet.microsoft.com/zh-tw/library/ee677619.aspx) 來找出可能重複的資料列。  [DQS Cleansing Transformation](http://technet.microsoft.com/zh-tw/library/ee677619.aspx) 會使用 [Data Quality Services](http://technet.microsoft.com/zh-tw/library/ff877925.aspx) (DQS) 的知識庫來更正資料，所以你必須預先建立好 DQS 知識庫。  

#### 進階選項設定

這些設定都是用來指定輸出資料的格式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiT-fd4bo_74lA6rwH60jWAdf8pLsnZQQapB_8_xAPXJZ7wN86aoEVTWfaCBoYoFpAuqDjMr19P9DEhRUMjjqvJueMGC68qSXxxQO46RG9WHOf_Ake0wjVJJn3FvcDGeRre74llTM1Wuqc/s0/ssis-dqs-cleansing-adv-options.png)

- **標準化輸出（Standardize output）**  
指出是否要根據定義域所定義的輸出格式，以標準化格式輸出資料。
- **信賴（Confidence）**  
指出是否要在更正資料中包含信賴層級的欄位。信賴層級表示 DQS 對更正或建議的確定程度。
- **原因（Reason）**  
指出是否要在更正資料中包含更正原因。
- **附加的資料（Appended data）**  
指出是否要輸出從現有參考資料提供者收到的其他資料。
- **附加的資料結構描述（Appended data schema）**  
指出是否要輸出資料結構描述。

## DQS Matching

如同 DQS 中的其他資料品質程序，執行比對必須先建立知識庫，並使用以下步驟在資料品質專案中執行比對活動（matching activity）：

1. 在知識庫中建立比對原則(matching policy)。
2. 在資料品質比對專案中執行刪除重複作業程序。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQQFtuA5B6VPCGdGX59EAo4HiyPrwXOUeUckgwGNiG4Fz9J-sTbg5VyTi37Kyn2mEdZwjtfEw9R-g3Jv6nrPtwNzHIEXwljHJsQXfTjSj8JJMx3yVGXknX8TysiKctYB_kKl329ge2xvs/s0/ssis-dqs-matching.png)

### 建立比對原則(matching policy)

知識庫中的**比對原則**是 DQS 用來進行比對的依據。  比對原則是由一個或多個比對規則所組成，這些規則會在 DQS 評估某一筆記錄與另一筆記錄的相符程度時識別將使用哪些定義域，並指定每一個定義域值在比對評估中所佔的加權比重。

### 執行比對專案

# Implementing SSIS Fuzzy Transformations

使用 DQS 比對時，你會發現資料量會是一個迪卡兒乘積，當資料量較大時，這比對效能將會下降的非常快速。  而且該過程並不提供互動式的步驟來處理比對結果。  所以，若有這樣子的需求，就可以考量使用 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 和 [Fuzzy Grouping Transformation](http://technet.microsoft.com/en-us/library/ms141764.aspx) 來處理較大資料量的比對。  例如資料量超過千筆以上時就適合用這個轉換來處理，不過，這個轉換只能用在字串的比對，不像 DQS 還可以對數字或日期做比對。  

## Fuzzy Transformations Algorithm

模糊轉換元件，使用「[傑卡德相似係數(Jaccard similarity coefficient)](http://en.wikipedia.org/wiki/Jaccard_index)」演算法進行比對。  轉換進行時必需要連接到 SQL Server 的執行個體，用以建立轉換演算法所需的暫存 SQL Server 資料表。

## Versions of Fuzzy Transformations

模糊轉換元件共有三種：

- The SSIS [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) ：
- The SSIS [Fuzzy Grouping Transformation](http://technet.microsoft.com/en-us/library/ms141764.aspx) ：
- The Excel Fuzzy Lookup Add-in ：（[點此下載](http://www.microsoft.com/en-us/download/details.aspx?id=15011)）

### Fuzzy Lookup Transformation

「**模糊查閱轉換**」主要是在進行**識別對應**（identity mapping）工作。  也就是進行資料清理。例如：標準化資料、更正資料、提供遺漏值。  

- [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 與 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 用法類似，只不過它使用模糊比對的方法進行查閱。
- 在封裝資料流程中，「模糊查閱」轉換經常在「查閱」轉換之後。也就是先使用「查閱轉換」尋找完全相符項目，如果找不到，再使用「模糊查閱轉換」尋找相近似項目。
- 「模糊查閱」轉換包含三種用於自訂其所執行之查閱的功能：
- 每個輸入資料列傳回的相符項目上限。
- Token 分隔符號
- 相似度臨界值（similarity thresholds）          相似度門檻設的愈高，整個比對的過程效能會比較好些，因為模糊比對可以考量較少的比對對象。
- 「模糊查閱轉換」的輸出資料行會包含二個額外的欄位：
- \_Similarity：（相似度分數）描述輸入與參考資料行中值之間相似度的資料行。          　相似度分數由介於 0 與 1 之間。愈接近 1，表示二者愈相似。
- \_Confidence：（信心分數）描述相符品質的資料行。  
信心分數也是介於 0 與 1 之間的十進位值，它表示對相符的信心。

### Fuzzy Grouping Transformation

「**模糊群組轉換**」主要是在進行**消除重複**（de-duplicating）工作。

- 「模糊群組轉換」的輸出資料行會包含三個額外的欄位：
- \_key\_in：每個資料列的唯一識別資料行。
- \_key\_out：識別同一組重複資料列的資料行。
- score：介於 0 與 1 之間的值，指出輸入資料列與標準資料列的相似度。

### Excel Fuzzy Lookup Add-in

最後，微軟還有提供個 Excel 增益集，用來在 Excel 中處理模理比對的功能，這個增益集僅限 Excel 2000 使用，你可以在以下連結下載：  [Fuzzy Lookup Add-In for Excel](http://www.microsoft.com/en-us/download/details.aspx?id=15011)
## 參考資料  

- [資料比對（Data Matching）](http://technet.microsoft.com/zh-tw/library/hh213071.aspx)
- [建立比對原則](http://technet.microsoft.com/zh-tw/library/hh270290.aspx)
-