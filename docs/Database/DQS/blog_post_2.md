---
title: 設計 Master Data Services (MDS)
layout: default
parent: DQS
nav_order: 2
description: "設計 Master Data Services (MDS)"
date: 2014-01-22
tags: [SQL,Data Quality Service]
postid: "7493826134376559507"
---
#### Master Data Services

[Master Data Services](http://technet.microsoft.com/zh-tw/library/ee633763.aspx)（MDS）主要用途就是要用來執行**主要資料管理**（Master Data Management, MDM）。  **主要資料**指的是單位組織對其非交易之資料進行探索與定義，以便匯編成一份可維護的主要清單。  成功的 MDM 方案可產生可靠的集中式資料，以利資料分析，以便導出更好的商業性決策。   

也就是說，MDS 主要用來維護一個精確且集中式的資料（即主要資料），確保整個組織的所有應用程式都能夠共同參考這一份資料。  例如：ERP, HMS, CRM 等不同系統，他們都必參照到"人"的資料，就可以透過 MDS 建立一份人的主要資料，以便當做這些應用程式的中央資料單位。  或者像郵遞區號，通常也都是當做主要資料。  

MDM 專案一般包含內部商業程序的評估與重組，以及 MDM 技術的實作。  只要透過適當的訓練，大部分商務使用者應該都可以實作 Master Data Services 方案。    此外，您還可以使用 MDS 管理任何網域（domain)；它不是單指管理客戶、產品或帳戶的清單。  首次安裝 MDS 時，它不包含任何網域的結構，但您可以經由建立模型來定義所需要的網域。  

# Defining Master Data

## 什麼是主要資料

## 管理主要資料

## MDM Challenges

# Installing MDS

Master Data Services 包含在 SQL Server 之中，但僅支援 64-bit 版本。  請參考 MSDN：[SQL Server 2012 版本支援的功能](http://technet.microsoft.com/library/cc645993%28SQL.110%29.aspx#MDS)  

## MDS 架構

### MDS 的組成份子

在安裝 MDS 前，我們先瞭解一下 MDS 架構。該架構可為四個部分：

1. 主要資料（Master Data）：主要資料與MDS系統物件是分開儲存的。
2. MDS系統物件（MDS System Object）：MDS系統物件包含 system tables 、 programmatic objects ( such as system stored procedures and functions).
3. MDS服務（MDS service）：負責執行 MDS 方案中的業務邏輯和資料存取。
4. 主資料管理員（Master Data Manager）：提供使用者或管理員操作的網頁程式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg46ZTfTVjV-FPJPD1A_Tfug3zu8dqw4MOn5FX5D2b8NvN6zbqe60DHG6qljnRdZieUCGeyJMJfEyqN52V33W5q6ieFZDBHyCg_HnB3ZDeF-eyvQbn-gf9nVf5hT5XbWjJT5SUOeA8AudM/s0/ssis-mds-00.png)

### 新增主資料的方法

你可以使用以下方式來新增主要資料

- 在「Master Data Manager」中手動建立。
- 使用 Excel 的「[MDS 增益集](http://www.microsoft.com/zh-tw/download/details.aspx?id=29064)」
- 由現有資料庫「匯入」到 staging 資料表。

有了主要資料，你可以在 MDS 中建立「**訂閱檢視（subscription views）**」，以便將 MDS 資料匯出至訂閱系統。  其他應用程式則可以由訂閱檢視檢視 Master Data Services 資料庫中的已發行資料。

## 安裝 MDS

完整的 MDS 服務安裝過程，除了要安裝 **MDS 服務程式**外，也必須建立用來管理主資料的「**主資料管理員網站**」，還有建立 **MDS 資料庫**。  而且為了正確執行這些功能，你還得啟用特定的 Windows 功能，建立特定的 Windows 使用者角色。  整個程序可為三個步驟：

### 安裝前工作

因為主資料管理員（Master Data Manager）是 IIS 底下的一個 Web 程式，所以你必須先確認與 IIS 相關的服務是否已安裝啟動。  而且這個網站採用 Windows 認證，所以你也必須為這個網站建立必要的 Windows 角色。  還有這個網站是使用 Microsoft Silverlight 5 製作，所以你的電腦也必須包含 Silverlight 的執行環境。  

#### 帳戶和權限

你必須建立一個角色，以便設定執行權限給適當的使用者。

#### 必要的服務

MDS 網站需要以下角色服務：

- Web 管理工具
- IIS Management Console
- WWW 服務
- 一般 HTTP 功能
- HTTP Errors
- Default Document
- Static Content
- Directory Browsing
- 安全性
- Windows Authentication
- Request Filtering
- 應用程式開發功能
- ASP.NET
- .NET Extensibility
- ISAPI Extensions
- ISAPI Filters
- 狀況及診斷
- HTTP Logging
- Request Monitor
- 效能特性
- Static Content Compression

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXqdSU3drNq5tXPh29Oszd9mztmUDiSnjyzMVwjRXmaJ3qAAqw6v_ruGHHMwhbfWmuuQuwQmcVjDPhUdu8kY2WG_-v6j4RYXuV0oHk-Z8zADf-u9a0QlROgSvHVhVmr_DTdrDJMq3Td1o/s0/ssis-mds-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmSg6X7uYiUCwtFGBBLyUH8doc8OtwjJJTObCwWeAdlmt7UuadHefhC_HGZczX-fXCoiEEIeoes06hQ0Jfh0tPugJs0gfmI6qgdEDl1KOuxrWOofAJxrGhM-_N5KlR6Xo9ValfsN3NdiM/s0/ssis-mds-01-2.png)

#### 必要的功能

- NET Framework 3.0 or 3.5 Features
- WCF Activation
- HTTP Activation
- Non-HTTP Activation
- Windows Process Activation Service
- Process Model
- .NET Environment
- Configuration APIs

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRKeSU2qmFcQcbtX95EXdRomADmQDlYCgK6pde1o-Gey5kHrbouPCNA69oAR-y54dQD6Pmq6vWLmTRR88pa4fxO6DNp5QDB-iisM7DPhATsEYz2uuzpXV9JqLfhGwdWNbDRBX8G4Ffc0U/s0/ssis-mds-02.png)

### 安裝作業

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOXJf1DUtcNRz10GDv9FuuhNu2QTiujn_6sxzkwQkjM5D2h4FZrOPRCePB05_Iy3KtHlxf7I7Kh49URks67s7KaSN6DzpwsIUjopIG_fFifg6JibS8N8aGXbu3vWHj5vvydotaYvMtPGs/s0/ssis-mds-03.png)

### 安裝後工作

系統安裝完之後，你必須使用「**MDS Configuration Manager**」來建立「**主資料管理員網站**」，以及建立「**MDS 資料庫**」。

1.執行 MDS 組態管理員

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgfqrDDnnWsmpmm-W8nalNSTFl_2AfOYFufTWU18iV63RbM1K5s-Uv7SofKZp8WQFYxNNyiLH9q-rCFqI0Hf2fewgGU1mEa8zHA5kau9C5nrvDPsvht-blBxCjf6WQsu2DR4qE_KdzJ4g4/s0/ssis-mds-04.png)

2.在 MDS 組態管理員中建立 MDS 管理員網站及 MDS 資料庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhA_A0XhR5wbzR_S-gd65N_fJ-DX_3_wsbCZ4WFpDOsSy5s92a3PNMIEzA1aVAGHxddfDaCrLub2FLyrHQOEXQF-8eMc-uExCsEkR43i1Vq7lNyfmWMV2orrYYP67x9WZ25EPVIzrX23fI/s850/ssis-mds-05.png)

建立 MDS 資料庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGee_0LVfRYwbGfQbYY97bYLcb414GS1tyGf0OQNIvUqpfoZ1ahgy4HpuevC4STK0FkRP8WHiEOu1KU4jo5mX9Pw7gxYM_ZS70mDDhNRtcCCTn6fnZPhcFZmrALg3PWyHaTak9UbHAYDQ/s0/ssis-mds-06.png)

建立主資料管理員網站

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjarR4x6z9qQUuD5wpwKZOq7_i19imb_fkz7fzoXlZ5MHCtmvKjeopI6Ckp4KuH3xLqvgv_Q4icr1GcHuDcDr3dw_fZ3u1xVlzzQCmqFI5DlM0DtHgp9XiJRJv0Cz1e4sOB0YbNrSXCbD8/s0/ssis-mds-07.png)

3.瀏灠主資料管理員網站。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEvwiROzux-t-BTipB0LBno4824U2ZLg7zXx8R5cSkqLJb0Y0yu8k4YTxAzefMfsA8wrgkv8Cyc4CcwlNhfYViquedwSha960DXF-gx_z-ryYlP6grV3OBClOxJbZ6bOR4VUyO2IZNDEQ/s0/ssis-mds-08.png)

# 建立 MDS 模型

A [Master Data Services model](http://msdn.microsoft.com/zh-tw/library/ee633746.aspx) is a container for all master data objects.   One model typically covers one master data business area.   When you create an MDS model, you should create all of the objects needed for a specific business area.   For example, when you create a model for products, you should include in the model all related data, as well as product subcategories and product categories.  

## MDS 模型（Models）

[模型](http://technet.microsoft.com/zh-tw/library/ee633746.aspx) 是 Master Data Services 中用來將資料分組的邏輯結構。  你可以使用一個或多個模型，通常習慣依不同的業務區域將主要資料組織成不同的模型，讓相類似的資料可以群組在一起。  主要資料通常以四種方式分類：人員、位置、東西、概念（people, places, things, concepts）。  例如，您可以建立一個 Product 模型來容納與產品相關的資料，或是建立一個 Customer 模型來容納與客戶相關的資料。   

#### 模型包含下列物件：

- 實體（Entities）
- 屬性和屬性群組（Attributes and attribute groups）
- 明確和衍生階層（Explicit and derived hierarchies）
- 集合（Collections）

下圖顯示模型中物件之間的關聯性。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGZkKBoS2a70TseG5peuW3F4PhSw7ObvWZRjPs6PPhYRQP6o6eGQ56aAe2CZ26Q0gCeiB2CDdHRNZmFlClMy6LmT012vx8BsR1VUWC7XTCs_H-bAAfNovPI6b7W5gL3QG4wgpBoVuRlww/s0/ssis-mds-model.gif)

上圖模型說明：

- Product 模型包含二個**實體**: Product 和 Subcategory.
- Product 實體包含以下**屬性**：Code、Name、Weight...。
- 部份屬性是**domain-based**：  
也就是該屬性值是參考其他實體，例如上圖中連接 Product 和 Subcategory 這二個實體的 Code 屬性，就是 domain-based 屬性。      這個  domain-based 的 Code 屬性是 Subcategory 的鍵值(key)。
- 在 MDS 中，每個實體都必須包含二個固定名稱的屬性：**Code** 和 **Name**。
- 模型包含實體。
- 實體包含屬性、明確階層和集合。
- 屬性可以包含在屬性群組中。
- 當實體做為另一個實體的屬性時，就會有網域屬性(Domain-based attributes)。

## MDS 物件（Objects）

### 實體（Entities）

最基本的 MDS 物件就是**實體**，一個實體表示真實世界中的一件事或是一個概念，它是商務中感興趣的東西。  你可以想像成：**實體**就像資料表，而**屬性**就像資料表的欄位。  

In an **MDS** model :

- The central entity in a model is typically also a starting point in navigation for end users.
- the central entity is called a **base entity**, and other entities relate to this central entity.
- Entities have attributes. Each entity must have at least two attributes, the **Name** and **Code** attributes.
- Values for the Code attribute must be unique within an entity, Code is like a key of an entity.
- There are three types of attributes:
- **Free-form** ：這個類型的屬性，允許屬性值可以任意輸入，例如任意的文字、數字、日期或連結。
- **Domain-based** ：這個類型的屬性，表示屬性值是由其他實體擴展來的，也就是只允許正確地輸入某個實體的 key 值。
- **File** ：這個類型的屬性，，表示使用檔案來儲存實體的文件或影像屬性。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGkDTTalBtCKQRCm-0cJ2F-m-7wnlJ0tLsAcO4KMWfQNSn7tEqYM4xEI1wqJ7pP9lV3l-LkyRBPEmKKcyKWqP9qitrjpsHKmjl-44Ip3lNJPwFjvXMMa3pcqZ8-RM-TTgm8fsUCCNgv60/s0/ssis-mds-09.png)

### 屬性（Attributes）

#### 屬性（Attributes）

[屬性](http://msdn.microsoft.com/zh-tw/library/ee633745.aspx)也是 Entity 中的物件。  您可以將**屬性**視為實體資料表中的資料行，有時也稱為實體的**成員**。 屬性值則是用來描述成員的值。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0shTt88_sY_iKxJAveGCEBG0YZk8vCf-X0QOcyJf6Bs9QEQCkv0a_lnowXtETLzXs8l9ouYVPJHm8lzAt-CvHt8Vz6yvaY2mQmg0RlBgODMAENxzQYKpJ_7li_G_EZAuIJ1ftCTUAAtY/s0/ssis-mds-attributes.gif)

#### 屬性群組（Attribute Groups）

若實體有很多個屬性，你可以將任意的幾個屬性組織成「屬性群組（Attribute Groups）」，這樣在 Master Data Manager WebAPP 中顯示時，就可以使用 Tab 來分組顯示不同組別的屬性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtOp67Xbhn53ttMPq6H7NeY9FYDPev_-qKlIS8MqdYu5DhU1fgDhIdLaQ1xUv9LOQeMuh35i_L88bf8xSkwWpO_QpfLenqLbe7vsSHe_VAHlkE03j2cyFJSu6XFKSJPEiNWj_V3NZtyOQ/s0/ssis-mds-10.png)

#### 網域屬性（Domain-based Attributes）

設定成「網域（Domain-based）」類型的屬性，就稱為網域屬性。  表示屬性值是由其他實體擴展來的，也就是其值必須是其某個實體的成員，這種設定可以防止使用者輸入錯誤的資料，相當於關連資料庫中的條件約束。  

網域屬性關聯性是衍生階層的基礎。

### 階層（Hierarchies）

在 MDS 中，你可以建立「[階層](http://msdn.microsoft.com/zh-tw/library/ee633737.aspx)」。  階層是一個樹狀的結構，通常是用來將實體成員分組，以利資料的匯總與分析。  例如，你可以使用商品的種類和尺寸建構一個階層，那麼你就可以由這個樹狀階層由上往下找到你要的資訊。  

#### 階層包含什麼：

- 每個階層都包含一個或多個成員。
- 階層中的所有成員都必須來自相同的模型中。
- 當成員變更或刪除時，階層都會自動更新，以確保資料的正確性。

#### 階層 vs 分類

階層與分類有所不同。  分類會同時根據多個屬性來組織成員，例如同時使用 尺寸、顏色、款式 等三種屬性來組織一台腳踏車。  而階層則一次根據一個屬性來組織成員，例如你每次只能使用一種屬性來組織一台腳踏車，例如先用尺寸，再用顏色，再用款式。  

您可以建立多個階層，用來方便檢視成員。而階層大至可分成以下三個類型：

- [明確階層](http://technet.microsoft.com/zh-tw/library/ee633759.aspx)（Explicit Hierarchies）：
- [衍生階層](http://technet.microsoft.com/zh-tw/library/ee633747.aspx)（Derived Hierarchies）：
- [遞迴階層](http://technet.microsoft.com/zh-tw/library/ff487006.aspx)（Recursive Hierarchies）：
- 

##### PS. 什麼是 Ragged 和 Level-based 階層

階層是一種樹狀結構，如果葉級成員都位在同一層，就稱為「 Level-based 階層」。如果葉級成員不在同一層，就稱為「 Ragged 階層」。

### 明確階層（Explicit Hierarchies）

由同一實體內的成員建立的階層稱為「明確階層」。

- 階層中的成員都來自單一實體的成員。
- 階層結構是由使用者自行定義而成。
- 使用合併成員群組其他成員
- 明確階層可以是不完全的(ragged)，也就是階層不固定。

明確階層可分成兩種類型：強制和非強制。 

- 強制明確階層（mandatory explicit hierarchy ）：所有分葉成員都必須包括在階層樹狀結構中。
- 非強制明確階層（non-mandatory explicit hierarchy ）：所有分葉成員都是位於系統建立的 [未使用] 節點。

#### 如何加入明確階層

在 MDS 的 [系統管理] 中，你可以直接在實體的編輯功能內，建立明確階層，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7Ea6fNosDyXOgn399Nq0JoNBkuGlxMhuaeFbj27zXMTjEO9PkrjM5Ku4F2dNTFnhTA_1J4ZEM2OeTPAEXibzvXOto-i8GWDdxanVYPhCZWoyexaKs3tsUqSGXqwmKJ3jxhCxgu9TQBng/s0/ssis-mds-11.png)

#### 如何組織明確階層

在 MDS 的 [總管(Explorer)] 中，你可以在［階層］功能中組織明確階層。它分成二部驟來完成：

1. 加入合併成員（Consolidated Member）

「合併成員」是用來將成員分組的物件。你可以自訂結構，而且它的階層允許 Ragged 的。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghv3L1MmW1y5Gxn0FXXirxLJZDrt4OINDkbr64dFpWQgo-oOu5t3pfR3IVTdz58P8CJ4AOrAPKTYTTisbVbhdxbsYSwseBNAfrEntyu-pnQw_r9A86MZ_6rMlCEUbOVP2H4-SXU99UWrc/s850/ssis-mds-16.png)

2. 在階層中移動成員

接著你就可以將成員由［未使用(Unuse)］搬移到適當的「合併成員」之中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdKAzYjxLN0d-g2-q97z_fdTO9fVrzdcxD0vb-2ZE_pIVFjn4ptbHmO3ij6HnWlp8LfeOQjizbOK-xL7dR1SQiT7SCIzgVwOpUKkoqMslnFPKLkFqispk1rFcClAIl7m24ekf3BsixcqU/s246/ssis-mds-17.png)

### 衍生階層（Derived Hierarchies）

由「網域屬性」建立的階層稱為「衍生階層」。

- 階層中的成員可來自不同實體的成員。
- 階層結構是由網域屬性之間的關聯性建構而成（domain-base attribute relationship）。
- 使用某個實體的分葉成員群組另一個實體的分葉成員
- 永遠包含固定數目的層級 。

衍生階層，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQUyaXZXvR5HyD2W3CoUgNadwqkD528KlSbaWEIHAeLKg3aTmgI-Fjbzkj06yD4ED62gd_6DS1MyBGbsH4TfQJqT9Xajvf_zWZSXQxueFIf3U2PAVZqdaG3w1uzqBuUjFr-ZYMpi_Go44/s409/ssis-derived-hierarchy-2.gif)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLJdK4lPl3XYdqnmTFk1meEiMbaVujjUvYAEaKRJcaebMszF6-133e4kCL54fCw6FUQ0ESGTH3xmwQDWGDfNLjNJeMUm53cRBYn0RqSIbE5VhUsk8NASvcV5seWIZH6Ue14WwJjX4KEwA/s281/ssis-derived-hierarchy.gif)

在 MDS 中，你必須在管理［選單］中，執行［衍生階層］

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiteNqgmHeihPAqsLS6w0XFR4YssDE5U3urZKnnKfyUbAUijhk5jW4b6b2QgmQHzYh1RzL9wBEzWynxCegN2ZFEI30TmNe8jVnqlWD7JLQ6WkiF4oznKzr5RLWTLHVJoINTM3sbqfYF9jk/s0/ssis-mds-12.png)

下圖中，我們建立一個衍生階層，它的成員包含 Department 和 Employee 實體中的成員。而且它的階層是 Level-based 的。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjUVt72apVYCeXWZN9pcH0DVg3AITYb_3AWCVWhgVStmWBDt64K27NZ7NBpqi0wc1nP60FPQVRJYY-CWMGkvIq14zritgjwTda0DGNPnLk5udF4e59bQBD0P-z69RkAIy3msePTdhES4M4/s850/ssis-mds-13.png)

### 遞迴階層（Recursive Hierarchies ）

[遞迴階層](http://technet.microsoft.com/zh-tw/library/ff487006.aspx)是指階層具有遞迴關係，通常這會發生在該「實體」包含自身「網域」屬性的屬性。

下圖中，因為 ParentDeptCode 是 domain 屬性，且參考到本身的實體，所以形成遞迴階層。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPB2hd-v0LzEgAY-k1VXtd1qRl0y7jHvhmZl8TdsfMVZRAdViTMGK6F9_gXrfXSxlXxFVerw2fyLTrDda8TfuuT6A1_yekoydiMJRA_SeTHdVR4j_Yqr5K7NZOujgeA1_NFXHIp02adog/s566/ssis-mds-14.png)

### 集合（Collection）

[集合](http://msdn.microsoft.com/zh-tw/library/ee633733.aspx)中的成員必須來自單一實體，當你不需要檢視整個完整的階層，又必須依若干特定群進行報告或分析時，這時就可以利用集合來分組。

## MDS 提供的功能

在建構 MDS 模型後，它提供許多功能來管理主要資料，以改善資料品質或者移動資料。  例如：  [商務規則](http://msdn.microsoft.com/zh-tw/library/ff487015.aspx)、[驗證](http://msdn.microsoft.com/zh-tw/library/hh231030.aspx)、[版本](http://msdn.microsoft.com/zh-tw/library/ee633738.aspx)等功能可以用來改善資料品質；  [匯入](http://msdn.microsoft.com/zh-tw/library/ee633726.aspx)、[匯出](http://msdn.microsoft.com/zh-tw/library/ee633741.aspx)、[部署模型](http://msdn.microsoft.com/zh-tw/library/ff486956.aspx)等功能可以用來移動資料。  

### 商務規則

在 Master Data Services 中，商務規則是用來確保主要資料品質和正確性的規則。    您可以使用商務規則自動更新資料、傳送電子郵件，或啟動商務程序或工作流程。  

要使用商務規則，必須完成以下步驟：

- 建立與發行商務規則
- 套用商務規則

商務規則是您在[主資料管理員]中，先透過 If/Then 陳述式來建立好商務規則，再將商務規則套用到屬性值，  如果屬性值符合指定的條件，便會執行動作。  可能的動作包含：變更成預設值或變更成其他值，也可以結合電子郵件傳送通知。

### 驗證

在 Master Data Services 中，會驗證資料以確保其正確性。  其中一部分驗證會自動發生，而另外一部分驗證則會根據管理員建立的商務規則。
## 參考資料  

- [Master Data Services](http://technet.microsoft.com/zh-tw/library/ee633763.aspx)
- [安裝 Master Data Services](http://technet.microsoft.com/zh-tw/library/ee633752.aspx)
- [Master Data Services 概觀](http://msdn.microsoft.com/zh-tw/library/ff487003.aspx)
- [Master Data Services 功能及工作](http://msdn.microsoft.com/zh-tw/library/hh231022.aspx)
- [Hierarchies (Master Data Services)](http://technet.microsoft.com/en-us/library/ee633737.aspx)