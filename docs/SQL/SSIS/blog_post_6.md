---
title: 緩時變維度
layout: default
parent: SSIS
nav_order: 6
description: "緩時變維度"
date: 2013-12-25
tags: [SQL,SSIS]
postid: "139484822111396833"
---
#### 緩時變維度（Slowly Changing Dimensions, SCD）

當 OLTP 資料庫中的資料發生變更時，如何在 DW 中適當的反應出這個資訊，這就是緩時變維度要解決的問題。  例如：因為每個業務員有其所屬的銷售區域，所以我們可以由銷售員的角度，來檢視各區的報表。可是如果這個銷售員轉換了銷售區域，那該時何來處理這樣子的變化，以反應出正確的報表結果。  

「緩時變維度」問題是設計 DW 時很常遇到的狀況，在 SSIS 中，你除了可以在 Control Flow 和 Data Flow 中**自訂**解決方法外，  最簡單的方法，就是直接使用 [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) 來簡化處理緩時變問題。  

# 屬性類別（Attribute Types）

## SCD 問題

當一個維度的屬性值被變更時，該用什麼方法來保留這個歷吏記錄，有人就歸納出 Type0 ~ Type6 等幾種不同將況，詳情可參考「[What are Slowly Changing Dimensions?](http://datawarehouse4u.info/SCD-Slowly-Changing-Dimensions.html)」。  不過，其中比較常見的解決方法就只有以下三種：  

- Type0：Retain Original。
- Type1（overwrite）：   直接使用新資料取代舊資料。
- Type2（add a row）：   使用一個新的資料列來表示新的資料。
- Type3（add a column）：加入一個額外的欄位，用來區分新值與舊值。

### Type1（overwrite）

變更前：

| SalesID | SalesName | SalesRegionID |
| --- | --- | --- |
| 1 | Vito | TP |

變更後：

| SalesID | SalesName | SalesRegionID |
| --- | --- | --- |
| 1 | Vito | KS |

### Type2（add a row）

變更前：

| SalesID | SalesName | SalesRegionID | Start\_Date | End\_Date | Current\_Flag |
| --- | --- | --- | --- | --- | --- |
| 1 | Vito | TP | 2013/01/01 | 9999/12/31 (null) | Y |

變更後：

| SalesID | SalesName | SalesRegionID | Start\_Date | End\_Date | Current\_Flag |
| --- | --- | --- | --- | --- | --- |
| 1 | Vito | TP | 2013/01/01 | 2014/12/31 | N |
| 2 | Vito | KS | 2014/01/01 | 9999/12/31 (null) | Y |

### Type3（add a column）

變更前：

| SalesID | SalesName | SalesRegionID | SalesRegionID\_last |
| --- | --- | --- | --- |
| １ | Vito | TP | TP |

變更後：

| SalesID | SalesName | SalesRegionID | SalesRegionID\_last |
| --- | --- | --- | --- |
| １ | Vito | KS | TP |

## 定義屬性類別（Defining Attribute Types）

為了能夠保留歷吏記錄，所以在開始執行 ETL 之前，你就必須先定義，哪些屬性需要要做歷吏記錄，要使用什麼類型的方式做歷吏記錄。  

通常可以將維度屬性分成以下幾種類別：

- Business Key：表示該欄位為商務索引鍵，執行「緩時變維度」轉換時至少需要一個商務索引鍵資料行。
- Fixed：表示該欄位為不可變更欄位。
- Type 1 SCD：可以覆蓋的欄位。
- Type 2 SCD：必須保留歷史記錄的欄位。

## 「緩時變維度轉換」的屬性設定

在使用 [緩時變維度轉換](http://technet.microsoft.com/en-us/library/ms141715.aspx) 元件時有以下二個限制必須先知道的：

- 只可以處理 SCD Type1 Type2 的需求。
- 「緩時變維度精靈」只支援與 SQL Server 的連接。

### 商務索引鍵

使用「緩時變維度」轉換至少需要一個商務索引鍵資料行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhsbSiB4IYWJed4OU7AqokLgp7W8JTEEyaw0TbtGqzIWvrsMw7_a4xaZ7UTdU0PFwRgDdLiQyIIat0R55W8eS1SCfuU9NKfWgt6JxvY0sUKFt5Q7HXlN1hj_ZxxuRXDoVddwG58wJaO-Gc/s0/ssis-lab7-01.png)

### 資料行的「變更類型」

針對**非索引鍵資料行**，「緩時變維度」支援以下四種資料行的變更方法：

- 固定屬性（fixed attribute）：指示資料行的值不得變更。
- 變更屬性（changing attribute）：指示使用覆寫方式記錄變更。相當於「Type 1」變更。
- 歷程記錄屬性（historical attribute）：指示使用新資料列方式記錄變更。相當於「Type 2」變更。
- 推斷的成員（ inferred member）：指示資料行為推斷的成員。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBTqcirHbUbfmSu2UDl5OWVa5d7KlTW0-ro5yLMn7G0e7d6Ak7s-2mSoQqOe_hiKYy_L2cKQ_Yodlq1z1jve4riPKF2jDW5YvNNSykjyq_r2V3WlItN-K9TCCh7ZLlFxHjYmewIfLLo9w/s0/ssis-lab7-02.png)

### 緩時變維度轉換的「輸出」

「緩時變維度」只接受一個**輸入**，依據以上設定，在經過轉換處理後，會產生至多六種**輸出**，讓你可以對不同類型輸出做不同的處理。

1. **不變更輸出（Unchanged output）**：無需更新的資料列。
2. **新輸出（New output）**：新資料列。
3. **推斷的成員更新輸出（Inferred Member Updates output）**：此輸出用於推斷的成員資料列。（流程接：UPDATE 陳述式更新記錄）
4. **歷程記錄屬性插入輸出（Historical Attributes Inserts output）**：符合 Type 2 SCD 資料列。（流程接：先 UPDATE 目前記錄的 ValidTo，再插入新資料列）
5. **變更屬性更新輸出（Changing Attributes Updates output）**：符合 Type 1 SCD 資料列。（流程接：UPDATE 陳述式更新記錄）
6. **固定屬性輸出（Fixed Attribute output）**：此輸出表示設定為不可變更的資料列，但是卻被變更資料了。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhnxWXQMRNo1aj7cfIoKMZ-srXJZA70fuuhJpOMQ9hLQ2tIV0f6AdM-NmO_fpBbiW2GPidCxIuKRAV36mvpzpK8pWMv97TevbwCeG-dDrEV0l9KcdDMDImxxtQOHqT2V9W88fh8671H7I0/s0/ssis-scd-transformation-output.png)

# 推斷成員（Inferred Dimemsion Member）

In data warehouse scenarios, it is possible for fact records to arrive with a source business key that has not yet been loaded in the dimension table.   This problem is known in DW jargon as **late-arriving dimensions** or **early-arriving facts**.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjz7QldmKe8p7-SCpE9D26B2HmZ6xVudXgdqIXOEtAogoah_HoqdoQRbAlCUilNyObLVXA6BNB74BfYVaBLCTlx4gJl6ZLDA-jJ8EBmht6-iiuj2ujPpbrpO8-ZIRVf1jgCaI7bnoQffs/s0/ssis-inferred-dimention-prob.gif)

typical solution is to create **inferred members** in a dimension table :

1. Insert a row in the dimension table by using the source system key, and assign a surrogate key to this member.   Mark this record as an inferred member by adding a InferredMemrber column (T/F).
2. Use the newly created surrogate key and assign it to the fact record.
3. Change the loading process for the dimension to check whether a record was populated as an inferred member.   If this is the case, you must treat all the attributes as Type 1 SCD regardless of your specification, except for the surrogate key and the business key.

# Using the Slowly Changing Dimension Task

SSIS has a predefined [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) that guides you through multiple steps for creating the complete logic for inserting and updating data for a dimension table.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVKMecHwXIoA4pjyBMe2w1mcm77r8QP0YYk4s_KvO2641GL0KW5iqWROqN75O664kGvXxD-LcaMN-lZw4-2GAHeCIFxCxWUvLyEeyBLngETgdj2QPXg4FhuZzDpdiCOc4KgaPGzlMcMTw/s0/ssis-scd-transformation.png)

這個 [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) 轉換元件支援 Type1 和 Type2 。  如果要達到 Type2 支援，這個元件提供二種法：  

- 使用一個識別欄位（如：CurrentFlag）來識別該筆資料是否為最新的值，或是過期的資料。
- 使用二個欄位 ValidFrom 和 ValidTo 來區分該筆資料是最新的值還是歷史資料。

在設定好「資料行變更類型」及「是否啟用推斷成員」之後，這個 [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) 會產生六種不同的輸出結果：  

1. **Unchanged output**：無需更新的資料列。
2. **New output**：新資料列。
3. **Inferred Member Updates output**：此輸出用於推斷的成員資料列。（流程接：UPDATE 陳述式更新記錄）
4. **Historical Attributes Inserts output**：符合 Type 2 SCD 資料列。（流程接：先 UPDATE 目前記錄的 ValidTo，再插入新資料列）
5. **Changing Attributes Updates output**：符合 Type 1 SCD 資料列。（流程接：UPDATE 陳述式更新記錄）
6. **Fixed Attribute output**：此輸出表示設定為不可變更的資料列被變更資料了。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhnxWXQMRNo1aj7cfIoKMZ-srXJZA70fuuhJpOMQ9hLQ2tIV0f6AdM-NmO_fpBbiW2GPidCxIuKRAV36mvpzpK8pWMv97TevbwCeG-dDrEV0l9KcdDMDImxxtQOHqT2V9W88fh8671H7I0/s0/ssis-scd-transformation-output.png)

# Effectively Updating Dimensions

要注意的是，雖然 [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) 可簡單快速的產生輸出結果，但是，如果你直接使用 [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) 元件去承接以進行資料更新，那麼它會使用效率較差的逐筆更新方式。
## 參考資料  

- 
- 
-