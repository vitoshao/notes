---
title: 變數、運算式、主要封裝
layout: default
parent: SSIS
nav_order: 5
description: "變數、運算式、主要封裝"
date: 2013-12-25
tags: [SQL,SSIS]
postid: "3827311633279760469"
---
# 變數（SSIS Variable）

## System and User Variables

#### SSIS variables Types:

1. **system variables** representing specific properties of various SSIS objects(packages, tasks, containers, components, and event handlers).   System variables are read-only; their values are set by SSIS.
2. **User-defined variables**, defined by the SSIS developer and used to store various pieces of information acquired or created during execution.   By default, user-defined variables can be written to, but it is also possible to limit their usage to read-only.

自訂變數預設屬於 User 命名空間，而系統變數則屬於 System 命名空間。自訂變數的命名空間可自行變更，以符合應用程式的需要。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9Nu6iE9K2_pPCI21nwgRq-eXUa5ZCi7JYxh4iWRZItGMxMcAkTNIbDM7wpSHAOrAR1TdNbb2okNPeHKp2xcg0adfwN2T4_ycV89jQ98YfnMszuCJUjencgakVEme6aaVtQ0m2NsbBT5k/s0/ssis-property-variable-2.png)

#### SSIS 變數可以用在：

- Control Flow Task , Container, Data Flow Components。
- 在 Task 和 Component 中的 script 也可以使用。
- 在 precedence constraints 中的 expression 定義也可以使用。
- Event Handlers

## Variable Data Types

SSIS 變數的資料型別是以 .Net Framework 的 System.TypeCode Enumeriation 為基礎。  詳細的資料型別定義請參考MSDN：[Integration Services 資料類型](http://technet.microsoft.com/zh-tw/library/ms141036.aspx)

除了 SSIS 變數的資料型別，SSIS 還有二個額外的資料型別群組：

- 每種 data provider 都有自已的資料型別。(such as OLE DB, ADO.NET, or ODBC)
- data flow buffer 使用特定的資料型別集。

## Variable Scope

不同 scope 的變數，其可存取範圍就不同，例如：

- **Package-scoped variables** : can be accessed by any task, container, or component defined inside this package
- **container level variables** : only accessible to that particular container
- **task level variables** : only accessible to that particular task,

新加入的變數，預設都是package-scoped。你可以使用[移動變數(Move)]功能來改變存取範圍。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEii37OGaqayyYG7M1xO1NZ1M-3-i9NxGrkQJCw4kZlvocVgseif43jXy9zGSdSBj_O5YuzYFxhideRfbjl7PudL60En4BOJK1hkNZj3WsBkAieq5Uq-g8TA73X3N9RrBswX94dKhB6wuo4/s0/ssis-variable-panel.png)

###### Note:唯一變數名稱

在同一個區間內，變數名稱不可以重複，如果在不同區間，則可以使用相同變數名稱。  當遇到相同名稱的變數時，系統會選用階層中最接近的那個變數值。

## 屬性值參數化（Property Parameterization）

通常我們在設定元件屬性時，都會在設計階段就給定一個固定的屬性值。  其實在設定 SSIS 物件屬性的時候，不一定要使用固定值，也可在屬性值中套用變數，使其動態產生。  要使用動態屬性，有以下幾種方法：  

- 在屬性中包含**參數**，參數值由叫用環境動態給定。
- 在屬性中明確指定**變數**。
- 大部份的 SSIS 物件可以透過 expression 指定變數。

### 如何在 Execute SQL Task 中，套用變數到 SQL Statement 中

在設定 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 時，有個 SqlSourceType 屬性，用來指明 SQL 陳述式的類型。

- 直接輸入：讀取 SQL-Statement 屬性中的資料當做 SQL 陳述式。
- 檔案連接：讀取指定的檔案資料當做 SQL 陳述式。
- 變數：讀取指定的變數值當做 SQL 陳述式。
- 

#### 直接使用變數類型

下圖例子，我們將 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 中的 SqlSourceType 設為「變數」，也就是說要使用變數值當做來源類型。  再指定一個變數給 SourceVariable 即可。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiLsM3jHCf1tvyQYY2cGbSSKNwDm2mh83smszyihVKGEBSOpM_E2ZgxxvvLSCg5ihGawc78ZXbja6R51n5Djh8seOHYl6QDbOK08G0WXiY-12tPN7EEWXWLg8O_AfUlRAx5UHU-gl7unBU/s0/ssis-property-variable-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqb5r7lTUi_Op4udk8bKba7x43sAsVd9jczdQ_ucaRqfa_IGl23qE1Iikv2Rr6KO7bPQo43mQlm639AubEXV7i_vvcSyqqLGIEPviEZgUq46zyx6H0Hf_cprywHqTl9xRFH9njow87axQ/s850/ssis-property-variable-3.png)

#### 參數化 SQL 命令

下圖例子，我們將 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 中的 SqlSourceType 設為「直接輸入」，  但是在 SQL Statement 中用到了一個「?」，所以必須在「參數對應」標籤中，定義一個參數名稱「@p1」來代表第一個「?」，而它的值則來自指定的變數，此例使用系統變數 System::PackageID。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjF57jsdWtAQGsOEk2XuhMI59Tq2bL9GrUek02yLhc820V0SVdg8stgHrhIrbjkJHWY-8K2yATq7TQ2eSKHUDVO-Nq1-CIjogj8CVtg9poMKYAY2RDKL5Itw4K3MHRIElTBXPx8QAvqhnY/s0/ssis-property-variable-4.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhllWKEX2ob_NrO051hHcamLhWz_T5vD008KEg3eVl8O-yrc4QEk0th5An2S5BgTFSZ3Xa4Psw0b7HjEwKFGhQd9BvRwoyG2uL8AfZSGL8JDvMU8trEg6B8DgJqs8UsPDTGcBEyb16vCLs/s0/ssis-property-variable-5.png)

※要特別注意的是這個「**參數名稱**」和「**參數標記**」的使用方法

這個參數名稱和參數標記可不是任意輸入的，而且，隨著「連接管理員類型」的不同，還必須使用不同的設定方法。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0LoNhAqedexb6BHYG1Jp7X9YLxwnFB6h0tYo45P8WM43999TO5pgQ126ALclyuvECzlFh14I93AjPvaz_1yVXW84NjxGBdvnvMscLVV_PCwFIEyvAgdRdcEIiH5l4yGtfSN1YvCDBInY/s850/ssis-property-variable-6.png)

詳細的參數名稱設定方法，請參考 MSDN：[執行 SQL 工作中的參數和傳回碼](http://technet.microsoft.com/zh-tw/library/cc280502.aspx)。  上例中，雖然可以使用「@p1」當做參數名稱，但若依據 MSDN 說明，使用「０」才是對的。

# 使用「運算式」來參數化物件的屬性值

一般來說，在設計 SSIS 封裝時，大部份的物件屬性值，都不需要套用參數來動態產生，不過，下面性質的物件則常常會使用到參數來設定：  

- **連線物件（Connection managers）**：  
開發、測試或者線上系統，其連線設定往往不同，所以常會利用參數來方便設定。
- **工作和元件（Tasks and components）**：  工作與工作之間有時會有相依性，例如Ｂ工作必須依據Ａ工作的執行結果才能執行，那麼你就可以將Ａ的執行結果存到變數，再當做參數傳給Ｂ工作。
- **資料流程工作（Data flow tasks）**：  你可以在執行階段，利用參數化來控管 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) 行為。  例如：你可以判斷系統時間若為週六或週日，則可以設定較大的資料傳輸量，若不是，則使用較小的傳輸量，以便將資源分配給其他系統使用。

要如何做到動態決定元件中屬性的值，或者動態決定變數值，那都可以利用「運算式（Expressions）」。

## 運算式（Expressions）

運算式是由 constants, variables, parameters, column references, expression functions, expression operators 組合而成，讓你可以在 run time 才決定如何給定一個值。  例如：在某些自動執行的程序中，因為無法手動設定資訊，常常就必須在執行階段利用運算式來決定。  

SSIS 運算式使用一種與 C 和 C# 類似的語法。  簡單的運算式可以是單一的常數、變數或函數。  比較複雜的運算式，也可以由多個運算子和函數，並參考多個資料行和變數組合而成。  像下列中的式子都可以構成運算式的內容：
```c#
3                                   -- 常值
@[User::var1]                       -- 變數加入運算式中
@[User::var1] + @[User::var1]       
LEFT( @[User::var1] , 5 )
ISNULL( @[User::var1]  )
@[User::var1]  > @[User::var2]  
```

詳細的運算式介紹，可參考MSDN：

- [語法 (SSIS Syntax)](http://technet.microsoft.com/zh-tw/library/ms140206.aspx)
- [運算子 (SSIS Operators)](http://technet.microsoft.com/zh-tw/library/ms137538.aspx)
- [函數 (SSIS Functions)](http://technet.microsoft.com/zh-tw/library/ms141671.aspx)
- [常值 (SSIS Literals)](http://technet.microsoft.com/zh-tw/library/ms141001.aspx)
- [識別碼 (SSIS Identifiers)](http://technet.microsoft.com/zh-tw/library/ms140285.aspx)

## 屬性運算式（Property Expressions）

在 SSIS 中的 packages, tasks, containers, event handlers, connection managers, log providers 這些物件，都可以建立「屬性運算式」。  你只要在該物件的屬性窗格中，點一下 expression 屬性旁的 [(…)]符號，就可以開啟 [屬性運算式編輯器] 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCe7UJCL_ySRojqpTj4R_bve5AJ8uQxwXj9j97NelJBvrRXnQMQ-1rCQl_pDgZ4lvgJLnboSBOCEQIfHadvn3KrMHxmX_SLKgEgFLCKhc_xpYPA1x9I3_aFZNebewf2eZmddE-tMsuums/s0/ssis-expression-property-1.png)

如果你在 Task 元件中，設定了屬性運算式，該元件的圖示上會多了 fx 字樣。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVzTCN1GGkq4u9tZP5-gpEcAjaZca2kcUmmzP5DS0HpJr5MjVEhQukaeJ1OLbMxFvX-JRTfkAnRav8jkAHwbWnxm_6o2AASmC7v6GBsSOeSFR9dbDBHkIPa1_DmTJdre8IkClpCfi0kKY/s0/ssis-expression-property-2.png)

大部份的 SSIS 物件都有「屬性運算式」可以用來動態設定屬性，不過，如果多個物件都會使用到一個相同的運算式，  那麼你也可以使用一個 [Expression Task](http://technet.microsoft.com/zh-tw/library/hh213137.aspx) 來計算，並將結果儲存到變數中，避免相同的運算執行多次。  

#### 延遲驗證（Delay Validation）

在設計階段，SSIS 就會自動驗證運算式的正確性，但是，有時候運算式中會包含未知的參考值，這時就會驗證失敗，產生錯誤。  這時，你可以先給定一個預設值，或者將 **Delay Validation** 設定為 True 來延遲驗證。  

更多相關內容請參考 MSDN：[加入或變更屬性運算式](http://technet.microsoft.com/zh-tw/library/ms141698.aspx)

## 優先順序條件約束運算式（Precedence Constraint Expressions）

Precedence Constraint 是來連接二個 Task ，通常我們會設定當上一個 Task 執行成功，才接著執行下一個 Task 。  不過你也可以使用 Precedence Constraint 的 Expression 來做條件設定，它等於是另外一個額外的控制條件。  例如在 Precedence Constraint Expression 中判斷執行日期是否為週末才執行下一個 Task ，所以此時就變成必須上一個 Task 執行成功且日期為週末才會接著執行下一個 Task。  

更多相關內容請參考 MSDN：[將運算式加入優先順序條件約束](http://msdn.microsoft.com/zh-tw/library/ms140153.aspx)

# Master Package

Master Package 並不是特殊的 Package ，它同一般封裝一樣，只不過在封裝中，加入了 [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx) ，用以將其他封裝當做工作流程的一部分執行。  

Master package 可以傳遞參數給 Child package 藉以控制子封裝。相關的操作，可參考 Lab6 中的「使用 Master Package」。  

## Separating Workloads, Purposes, and Objectives

## Harmonizing Workflow and Configuration

## The Execute Package Task

####  [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx) 

- This task is used to execute other SSIS packages (child packages) from the current package (parent package).
- The task can be used to invoke a child package belonging to the same project, a package located in the file system, or a package deployed to a SQL Server instance.

### 如何使用 Execute Package Task 執行封裝

#### 執行專案中的封裝

要使用 [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx) 執行專案中的子封裝，必須設定下列屬性透過：

- ReferenceType：設定為 [專案參考]。
- PackageNameFromProjectReference：選取要執行的子封裝。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgYgdch221iYvLDs3Mtm8jb1uqcpNMsyJZEhfb4cSiY4AYfvYomrM5s5ktsMBHx-RTgMzyKV1f3cAhmc7G2RdLIfrC0AnneUQFt-4MGGqcZe0Y2Hl3_ZJOiJ_bCnt-DxHDe2JpTDiclVs/s0/ssis-exec-package-task-1.png)

#### 執行儲存在 SQL Server msdb 資料庫中的封裝

「執行封裝」工作也可以執行儲存在 SQL Server msdb 資料庫中的封裝，以及儲存在檔案系統中的封裝。  此工作使用 OLE DB 連接管理員連接到 SQL Server，或使用檔案連接管理員存取檔案系統。 

#### 執行專案中的封裝

若要執行在檔案系統中的封裝，就必須提供封裝的名稱與位置。 封裝可存在於檔案系統中的任何位置，不必與父封裝位在相同資料夾中。

### 如何傳遞參數值給子封裝

使用 [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx) 時，您可以透過下列方法，將值由父封裝傳遞至子封裝： 

- 使用**封裝組態（Package configurations）**
- 使用**參數（Parameters）**

更多相關內容請參考 MSDN：[在子封裝中使用變數和參數的值](http://technet.microsoft.com/zh-tw/library/ms345179.aspx)

## The Execute SQL Server Agent Job Task

## The Execute Process Task
## 參考資料  

- [Integration Services (SSIS) 變數](http://msdn.microsoft.com/zh-tw/library/ms141085.aspx)
- [系統變數](http://technet.microsoft.com/zh-tw/library/ms141788.aspx)
- [Integration Services (SSIS) 運算式](http://technet.microsoft.com/zh-tw/library/ms137547.aspx)
- [執行封裝工作（Execute Package Task）](http://technet.microsoft.com/zh-tw/library/ms137609.aspx)