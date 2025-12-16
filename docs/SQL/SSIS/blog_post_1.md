---
title: Data Flow
layout: default
parent: SSIS
nav_order: 1
description: "Data Flow"
date: 2013-12-16
tags: [SQL,SSIS]
postid: "1944544543004495487"
---
# 什麼是 Data Flow Task

When creating an ETL project, you will spend most of the time building various kinds of data flow tasks.  Each package can have zero or more data flow tasks.  There are three types of data flow task components in the **SSIS Toolbox**：  

- Data flow **source adapters**
- Data flow **transformations**
- Data flow **destination adapters**

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0hXj8HwX2y32REMr8XCmxBxtrIMGALC9iGNa2KBHEkddkwgLWKYNg6SmT3Judh6X9BAgTN_uKnljRozDezwG0fZKvWMkWU4yBc53JxfF0MLcFfYb44olfXIhXMsw3dqAhEnZVx7IqLXI/s850/ssis-what-is-data-flow.png)

# 定義 Data Sources 和 Data Destinations

Data Flow Adapters 可分成來源和目的地二大類，你必須針對不同的資料來源類型，使用不同的 Data Flow Adapters 。

## Data Source Adapters

| ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyEcTfNyDxv_rWFFiX8qm_VSTZEQLNa-3CQ2vqQfTZa7uE2OICd2px9YW1H3PVwULBcm5Gx1WKtqmd2PXMPMVRG-HHTLsoqUSeDHCb-DvQ_cEcbSwYxPsY5YKJfyqIE6_Q9xFOf1Sf6Io/s0/ssis-data-sources.png) | &lt;ul&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms137897.aspx"&gt;ADO NET Source&lt;/a&gt;  &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/hh758686.aspx"&gt;CDC Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141683.aspx"&gt;Excel Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/hh758687.aspx"&gt;ODBC Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141696.aspx"&gt;OLE DB Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms140277.aspx"&gt;XML Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms139941.aspx"&gt;Flat File Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms140169.aspx"&gt;Raw File Source&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/dn584122.aspx"&gt;OData Source&lt;/a&gt; &lt;/li&gt;  &lt;/ul&gt; |
| --- | --- |

### Data Sources 資料存取模式

OLE DB 來源提供四種不同資料存取模式用來擷取資料(extracting data)：

- A table or view
- A table or view specified in a variable.
- The results of an SQL statement
- The results of an SQL statement stored in a variable.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMZ0oqWTul_euTXyeQtD7yXsVO95lYubYFhCa6hK6hpriSX26y8iMA6MmLcacFEZJKqutzQdGRRfIC8XbtIG6UgSL7M0OeYpYGWg5tmAzRxE8-oKJhzkZqC0jRRqK_4ao9_YD3dg1C37Q/s0/ssis-data-source-access-type.png)

### 來源小幫手（Source Assistant）

[來源小幫手](http://msdn.microsoft.com/zh-tw/library/ff929138.aspx)元件可以用來協助建立來源元件和連線管理員。

## Data Destination Adapters

| ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsPvxtbUv3hliQtpbaMD8hO0P7H55FRljgi-KFu7HdFHFVlc0m3XqE63V160dHvFTlfKQXYJW3dMja0tIagHCyeNELC7_7q9rW6RWYstQDwuYM8walwaTLKlM4vqomt5DWX0rMKuvPMx0/s0/ssis-data-destinations.png) | &lt;ul&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/bb895291.aspx"&gt;ADO NET Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms140291.aspx"&gt;DataReader Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms137643.aspx"&gt;Excel Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/hh758691.aspx"&gt;ODBC Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141237.aspx"&gt;OLE DB Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms140269.aspx"&gt;SQL Server Compact Edition Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141095.aspx"&gt;SQL Server Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141668.aspx"&gt;Flat File Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms141661.aspx"&gt;Raw File Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms140144.aspx"&gt;Dimension Processing Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms137992.aspx"&gt;Partition Processing Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/en-us/library/ms139761.aspx"&gt;Data Mining Model Training Destination&lt;/a&gt; &lt;/li&gt;  &lt;li&gt;&lt;a target="_blank" href="http://technet.microsoft.com/zh-tw/library/cc879316.aspx"&gt;Recordset Destination&lt;/a&gt; &lt;/li&gt;    &lt;/ul&gt; |
| --- | --- |

### Data Destination 資料存取模式

OLE DB 目的地提供五種不同資料存取模式用於載入資料(loading data)：

- A table or view
- A table or view using fast-load
- A table or view specified in a variable.
- A table or view specified in a variable using fast-load options
- The results of an SQL statement

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiseOtseVaEtxWY-M78upX-9Wxdw3rFGiTnOC98Tma0KGnaHuIT4oXWfGeOxXsOHgPySJMrUyBpEwaybcDm156A6poIz3TKRKdBC6hNCuTlMwEmVaP5t4gklOg9b6nCIN1-X8Y1PmGPDZc/s0/ssis-data-destination-access-type.png)

### 快速載入選項

如果 OLE DB 目的地使用快速載入（Fast Load）資料存取模式，則您可以為目的地指定下列**快速載入選項**： 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiekdBYdqeX8y8Ing7W2AbQncfTPheY-3ISIHuAiSI_qSwP4TQOrNA8DXJ0EYyTutvape-7axWjK0JRFUiC5j5EEOeixfO34rHpAIaHX9JGM5qHavlfKv-OEPmeEXrliH649cp4_eb0zgM/s0/ssis-data-destination-fast-load-options.png)

- 保留識別    ：保留匯入資料檔中的識別值或者使用 SQL Server 指派的唯一值。
- 保留Null    ：大量載入作業期間，保留 Null 值。
- 資料表鎖定  ：大量載入作業期間，需要使用資料表層級鎖定。
- 檢查條件約束：大量匯入作業期間，檢查目的端的 table 或 view 的條件約束。
- 每批次的資料列：指定批次處理的資料列數目（batch size）。
- 插入認可的上限：指定認可的資料列數目（commit size）。

## SSIS Data Types

當建立資料來源時，由於資料來源可能是 SQL、Oracle 或者 Flat File ，  所以 SSIS 會自動依據資料來源中的內容，自動決定一個SSIS的資料型別。  你可以針對實際上的需求自行調整資料的型別設定，以免資料在執行 Transformation 時發生型別錯誤。  

你可以在［進行編輯］功能中，找到SSIS為每個欄位資料設定的輸出型別：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhV6VyxwaaxH65pWy2YNEFB5WKyjFhyphenhyphenzVVfkwLZtNMi5BlmEkEio0PCERwuyWHjFF736zOo4Xbp-9LkZOj8A3_fGc3TbcOaNnuFNYXy_BCzbmrP5YvSmammuxVwHLT5-TRlF6J3Q0umzt8/s0/ssis-data-type.png)

## 使用「快速剖析（Fast Parse）」

[Fast Parse](http://technet.microsoft.com/zh-tw/library/ms139833.aspx) is a set of operations within SSIS that can be used for very fast loading of flat file data.  Fast Parse will not parse any locally sensitive data so it can load data very quickly.  

Fast Parse has limited functionality, because it works only for specific data types.   But if you have a lot of columns of **date**, **time**, or **integer** data types or very large files,   Fast Parse is the fastest method for importing data files.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJilbO3BWrjb95cez84OrvsV9Dmiddg_F4yLRvyl7T0pMlyp9kselNa1Ulj9SFURPl7OP85LXmULeOd8XlQ-TIxTw5nzss8gwolAhz57PSM6D8WQINiyiRO9fAB3muqwPdweJq4wFi0DQ/s0/ssis-fast-parse.png)

## Data Flow 元件常用的屬性

下列列表是幾個 Data Flow 元件常用的屬性：

- CommandTimeout：設定元件Timeout的時間。
- ValidateExternalMetadata：是否在 design time 時執行外部資料的驗證。
- 
-

# Working with Transformations

## 轉換元件（Transformations）

轉換（Transformations）是SSIS資料流程中的元件。  專門用來處理彙總、合併、散發和修改資料等作業；也可用來執行查閱作業，以並產生資料集範本。  下面依造功能分類，列出 SSIS 中的轉換元件。  

### 商業智慧轉換（Business Intelligence Transformations）

下列轉換會用來執行與商業智慧相關的作業 (例如，清除資料、採礦文字及執行資料採礦預測查詢)。 

| Transformations | 轉換 | 說明 | Block |
| --- | --- | --- | --- |
| [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx) | 緩時變維度轉換 | configures the updating of a slowly changing dimension. |  |
| [Fuzzy Grouping Transformation](http://technet.microsoft.com/en-us/library/ms141764.aspx) | 模糊群組轉換 | standardizes values in column data. |  |
| [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) | 模糊查閱轉換 | 使用模糊比對 |  |
| [Term Extraction Transformation](http://technet.microsoft.com/en-us/library/ms141809.aspx) | 詞彙擷取轉換 | extracts terms from text. |  |
| [Term Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137850.aspx) | 詞彙查閱轉換 | looks up terms in a reference table. |  |
| [Data Mining Query Transformation](http://technet.microsoft.com/en-us/library/ms140358.aspx) | 資料採礦查詢轉換 | 執行資料採礦預測查詢 |  |
| [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) | DQS 清理轉換 | 在來源資料中建立規則以更正來源資料 |  |

### 資料列轉換（Row Transformations）

這類轉換元件會針對輸入中的每一個資料列，進行資料行的更新，然後再以建立新的資料行輸出。  

| Transformations | 轉換 | 說明 | Block |
| --- | --- | --- | --- |
| [Character Map Transformation](http://technet.microsoft.com/en-us/library/ms141694.aspx) | 字元對應轉換 | applies string functions to character data. |  |
| [Copy Column Transformation](http://technet.microsoft.com/en-us/library/ms137741.aspx) | 複製資料行轉換 | adds copies of input columns to the transformation output. |  |
| [Data Conversion Transformation](http://technet.microsoft.com/en-us/library/ms141706.aspx) | 資料轉換 | 資料型別的轉換 |  |
| [Derived Column Transformation](http://technet.microsoft.com/en-us/library/ms141069.aspx) | 衍生的資料行轉換 | 「衍生的資料行」轉換會將運算式套用至轉換輸入資料行，藉此建立新的資料行值。 |  |
| [Import Column Transformation](http://technet.microsoft.com/en-us/library/ms141262.aspx) | 匯入資料行轉換 | inserts data from a data flow into a file. |  |
| [Export Column Transformation](http://technet.microsoft.com/en-us/library/ms139818.aspx) | 匯出資料行轉換 | that reads data from a file and adds it to a data flow. |  |
| [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) | 指令碼元件 | uses script to extract, transform, or load data. |  |
| [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) | OLE DB 命令轉換 | runs SQL commands for each row |  |

Derived Column Transformation

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRMSQ_vbnBRVm1C-2-c6aRCYePdtZMEN1U4jWEf7G4s3c4PLIpuYskWofVrn5akU9fGwScH5f4VWBFXHpX8yj4SkjmLm74JymFeQ1bv7eil19Qa0Cz9ztqB4OJ7QB8R95ZL_SjMEjnB7g/s0/ssis-devired-column-transformation.png)

### 資料列集轉換（Rowset Transformations）

下列轉換元件會產生新的資料列集輸出。

| Transformations | 轉換 | 說明 | Block |
| --- | --- | --- | --- |
| [Aggregate Transformation](http://technet.microsoft.com/en-us/library/ms138031.aspx) | 彙總轉換 | 執行彙總 (例如 AVERAGE、SUM 及 COUNT) 的轉換。 |  |
| [Sort Transformation](http://technet.microsoft.com/en-us/library/ms140182.aspx) | 排序轉換 | 排序資料的轉換。 |  |
| [Percentage Sampling Transformation](http://technet.microsoft.com/en-us/library/ms139864.aspx) | 百分比取樣轉換 | 透過使用百分比指定範例大小來建立樣本資料集的轉換。 |  |
| [Row Sampling Transformation](http://technet.microsoft.com/en-us/library/ms141200.aspx) | 資料列取樣轉換 | 透過指定樣本中的資料列數目來建立樣本資料集的轉換。 |  |
| [Pivot Transformation](http://technet.microsoft.com/en-us/library/ms140308.aspx) | 樞紐轉換 | creates a less normalized version of a normalized table. |  |
| [Unpivot Transformation](http://technet.microsoft.com/en-us/library/ms141723.aspx) | 取消樞紐轉換 | creates a more normalized version of a nonnormalized table. |  |

### 分割與合併轉換（Split and Join Transformations）

下列轉換元件會將一個輸入散發成多個輸出，或者合併多個輸入到一個輸出。

| Transformations | 轉換 | 說明 | Block |
| --- | --- | --- | --- |
| [Conditional Split Transformation](http://technet.microsoft.com/en-us/library/ms137886.aspx) | 條件式分割轉換 | routes data rows to different outputs. |  |
| [Multicast Transformation](http://technet.microsoft.com/en-us/library/ms137701.aspx) | 多點傳送轉換 | distributes its input to one or more outputs. |  |
| [Union All Transformation](http://technet.microsoft.com/en-us/library/ms141020.aspx) | 聯集全部轉換 | 合併多個資料集 |  |
| [Merge Transformation](http://technet.microsoft.com/en-us/library/ms141703.aspx) | 合併轉換 | 合併兩個已排序資料集 |  |
| [Merge Join Transformation](http://technet.microsoft.com/en-us/library/ms141775.aspx) | 合併聯結轉換 | 使用 FULL、LEFT 或 INNER 來 JOIN 兩個資料集 |  |
| [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) | 查閱轉換 | The transformation that looks up values in a reference table using an exact match. |  |
| [Cache Transform](http://technet.microsoft.com/en-us/library/bb895264.aspx) | 快取轉換 | The transformation that writes data from a connected data source in the data flow to a Cache connection manager that saves the data to a cache file. |  |

#### **Merge** vs **Merge Join** vs **Union All**

- [Merge Transformation](http://technet.microsoft.com/en-us/library/ms141703.aspx) 可以用來將多個已排序的輸入資料，合併成一個輸出資料，且合併後的整個資料也會排序。
- [Merge Join Transformation](http://technet.microsoft.com/en-us/library/ms141775.aspx) 與 [Merge Transformation](http://technet.microsoft.com/en-us/library/ms141703.aspx) 不同之處，在於其可以自行設定輸出的資料列，而不是全部。
- [Union All Transformation](http://technet.microsoft.com/en-us/library/ms141020.aspx) 與 [Merge Transformation](http://technet.microsoft.com/en-us/library/ms141703.aspx) 不同之處，在於其輸入不需要已排序，且其輸出也不會排序。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikgryHf1jdpBibNRKlgm_XCQnDMBWdDGyl2iGAlIbghQ1uX6gcYqVeISKc1ypibZdmgabvAV3GOiPL4oaB4DTw4ealSRk0KUXjuLnY7UasMf8-s_atvvANohOuwxv15jjakTY0dXKICk8/s850/ssis-merge-transformations.png)

#### **Conditional Split** vs **Multicast**

這兩種轉換都會將一個輸入導向多個輸出， 兩者的差異在於：  「多點傳送」轉換會將每個資料列導向每個輸出，也就是每個輸出都與原來的輸入大小相同；而  「條件式分割」會將一個資料列導向單一輸出。也就是每個輸出內容都不會相同。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivCzcSoXsyqTtKMjTLkwFoLvMQLrvqvde286-mAmQ6bGsq9xBA0TenrD9LTnndsF0K5LAZvc52TYc34NWt4RV9uYOCmdH3_mlsGwibYzCwSmp6i6o_AlGA2_rLejnbu8VIi6w8v1WFBHA/s850/ssis-multicast-vs-condition-split.png)

### 稽核轉換（Auditing Transformations）

| Transformations | 轉換 | 說明 | Block |
| --- | --- | --- | --- |
| [Audit Transformation](http://technet.microsoft.com/en-us/library/ms141150.aspx) | 稽核轉換 |  |  |
| [Row Count Transformation](http://technet.microsoft.com/en-us/library/ms141136.aspx) | 資料列計數轉換 | 資料列通過資料流程時計算其數目，並將最後計數儲存到變數中 |  |

### 自訂轉換（Custom Transformations）

撰寫自訂轉換。

## Using Transformations

要使用何種 Transformations ，就將元件拖拉到 SSIS Designer 之中即可。詳細步驟請參考 Lab4 和 Lab5 。

### Resolving Column References

如果在資料流程建立之後，資料來源中的資料表才被加入或刪除的欄位，這可能引發轉換元件參考到錯誤欄位。  過去，你必須一個一個欄位修改對應，現在可以使用［解析參考］快速解決對應問題。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOtFCslZmFK7aRVW9HZsXnYG3pxRmK4sXRzkFhKqJlbfmkDyuOlXSQ_m1uJxqeRKgbn-MjnE8DQJL25hI7xU-_e3jSXV-OwF5Z9Uz0a7cPqTyZrDzFrXPgCdNak0X4EvgFaG0fm6xUsGg/s0/ssis-resolving-column-reference.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6g-_whzBzrGtA-h-LFzOgXzTiqU2PN4FR8BHmK_au91p5nWYkOlj5rQz1wtMcE3T6raa4l9r1mPBN1VLTJqWGl8Lfj4EuqYXwsYqCkCz1ZOQ-DQsiqcnuQy3USOxKguVUZWI7HuyUGJc/s0/ssis-resolving-column-reference-2.png)

# Determining Appropriate ETL Strategy

## ETL Strategy

## 查閱轉換（Lookup Transformation）

Lookup Transformation 元件是一個在 ETL 過程中很實用的元件，它可以用來查詢特定的 dataset 是否有符可條件的資料。  在使用這個元件時，有幾點要注意的：  

### 區分大小寫

這個 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 元件是區分大小寫的(case sensitive)。  你可以先加入 [Character Map Transformation](http://technet.microsoft.com/en-us/library/ms141694.aspx) 元件，並利用 UPPER 或 LOWER 函式進行大小寫的轉換。

### 如何處理比對後的輸出

使用 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 元件時，每次比對，都只會回傳第一筆符合條件的資料，這也是為什麼在比對前要先排序的原因。  它與 Merge Transformations 元件最大的不同處在於 [Merge Transformation](http://technet.microsoft.com/en-us/library/ms141703.aspx) 回傳的是所有符合條件的資料。  

比對後，會得到下列二個結果輸出：

1. 查閱比對輸出（Match output）：符合比對的結果集。
2. 查閱無相符比對輸出（No Match output）：不符合比對的結果集。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKA4-6tabultju7Tt2vBdw7Gha_mUkJLSC52frinOuLxt7tu4fPrnv_w0GxwFWKKxG82I5AhB-X9Nalnl3Gg2lKK8Zis9tiU1c-gTQIQkUz2I_yfHEscDDGWvWlPtGOehyCMgkejphx-8/s475/ssis-no-match-output-3.png)

在 Lookup Transformation 元件的編輯功能中，你可以設定下列選項，針對「不符合的項目」來進行處理：  

1. **忽略失敗**：結果相當於使用LeftJoin，No Match資料會使用 Null 填塞，由 Match 端輸出。
2. **將資料列重新導向錯誤輸出**：No Match資料會導向「錯誤輸出」，由 Error 端輸出。
3. **失敗元件**：發生元件錯誤，產生 Error ，不輸出。
4. **將資料列重新導向無相符合結果輸出**：No Match 資料會導向「無相符合結果」輸出。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIX3It55koycK9wWYYNvdBS-6UOVluUAXarQQS4bn8GUv2Sv8hegFtpeGFBGaAUhO9A7dX67GKz90w9K5JvD7mVOpiQ4tP4dcOk7Ec79No956rOqHMS1M8-EbNMEIeXU2PqqLNbxNC8xw/s0/ssis-no-match-output.png)

#### 處理不相符資料

如果選擇「將資料列重新導向無相符合結果輸出」，則 No Match 的資料將由「查閱無相符合結果」輸出。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKA4-6tabultju7Tt2vBdw7Gha_mUkJLSC52frinOuLxt7tu4fPrnv_w0GxwFWKKxG82I5AhB-X9Nalnl3Gg2lKK8Zis9tiU1c-gTQIQkUz2I_yfHEscDDGWvWlPtGOehyCMgkejphx-8/s475/ssis-no-match-output-3.png)

2. 如果選擇**忽略失敗**，則 No Match 資料會與 Match 資料，都一起由「查閱比對結果」輸出。

如果二個結果你都想要保留，你可以使用 [Union All Transformation](http://technet.microsoft.com/en-us/library/ms141020.aspx) 來合併二個結果。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEij8-Ri06knsk-cKw2wGNYyxHQJ17zCXtKTBcBFY_zEAkI9eAkW1Zg0GST3VypGmSpJFFP7MLkso29xvqgvBOnof8d_y_ICmQxm6uRcJxgC4-lHZlcfxc_XbsK5HqDmXA8Ydy9p7yAJ10U/s0/ssis-no-match-output-2.png)

除了以上做法，你也可以使用另外一個方法，而且效率會更好。  你可以在 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 中將 No Match output 設定成**忽略失敗**，那麼不符合的比對的資料，其結果欄位就會被塞NULL值。  然後你可以在輸出結果下面再加入一個 [Derived Column Transformation](http://technet.microsoft.com/en-us/library/ms141069.aspx) ，透過類似以下方法，來處理這些NULL欄位。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjGgkJb0b5XLE7T9CJ_6xSjWSON0Zznh3vOXLcjn-cFDvxEckZzYkHgHGr3BjB9IQnjcNVvSVPaVwNdM0vp5YaB9YbItaAYhp3vm4vBayc3hURHj3MsfWei5905-KYEoJhwDppUuSRDyG4/s488/ssis-no-match-output-4.png)

### 設定快取選項

- 完整快取：在查閱轉換執行之前，產生參考資料集，並將其載入快取。
- 部分快取：在查閱轉換執行期間產生參考資料集。  將參考資料集中具有相符項目的資料列，以及在資料集中沒有相符項目的資料列載入到快取。
- 沒有快取：在查閱轉換執行期間產生參考資料集。  沒有資料會載入到快取。
-

### 搭配 Cache Transform Transformation 使用，以提升效能

- It allows you to reuse the cache to reduce database load.
- It allows you to share the cache between lookups to reduce memory usage.
- You can do lookups against other (non OLE-DB) sources.

## Sorting the Data

當使用 [Merge Join Transformation](http://technet.microsoft.com/en-us/library/ms141775.aspx) 時，其輸入必須是已排序的資料，所以如果你是使用 TSQL 取得來源資料，你可以直接使用 ORDER BY 子句排序資料，另外你也可以使用 [Sort Transformation](http://technet.microsoft.com/en-us/library/ms140182.aspx) 。

你必需將「排序轉換」插入到「合併」或「合併聯結」轉換之前。

你可以在[輸入與輸出屬性]的[排序輸出]中，找到 **SortKeyPosition** 屬性設定，如下圖位置：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjF4g1cHyTHlNJAZEqr6j36Wxf35YnWU70vJH_wjMPWt_IamAuhXZEJtQTJ0yc3M6Jak0ZekEEAHYVjOnUrkq8IH4smm3qeOJhiujGBariqwHxrHJaFiW91KK0f7-9iIRbnw190QnBS47Y/s0/ssis-sort-transformation-sortkey.png)

- 0：預設值，不使用該欄位排序。
- 1：該欄位當做第１個排序欄位，且使用升冪。
- -1：該欄位當做第１個排序欄位，且使用降冪。
- 2：該欄位當做第２個排序欄位，且使用升冪。
- -2：該欄位當做第２個排序欄位，但使用降冪。
- ...

## Set-Based Updates

Set-Based Updates 是什麼意思，簡單講就是針對一個資料的集合運作，白話一點就是一整批更新，而不是一筆一筆更新。  你可以參考網路這篇文章：[Understanding “Set based” and “Procedural” approaches in SQL](http://www.codeproject.com/Articles/34142/Understanding-Set-based-and-Procedural-approaches)  

在 SSIS 中，若你使用 [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) 來執行 SQL 以進行資料更新，  它會依據資料流中的資料，逐筆執行，所以僅適合資料筆數較小的案例，這方式也稱為 **Procedural Updates**。  

如果資料筆數較大，通常會將這些要更新的資料先輸出到一個暫時資料表，然後搭配 MERGE T-SQL 類型的陳述式來達到 **Set-Based Updates**。  
```sql
UPDATE C
SET 
C.FullName = CU.FullName,
C.EmailAddress = CU.EmailAddress,
C.BirthDate = CU.BirthDate,
C.MaritalStatus = CU.MaritalStatus,
FROM dbo.DimCustomers C
INNER JOIN stg.DimCustomersUpdate CU ON C.CustomerDwKey = CU.CustomerKey
```
## 參考資料  

- [Integration Services 轉換](http://technet.microsoft.com/zh-tw/library/ms141713.aspx)
- 
-