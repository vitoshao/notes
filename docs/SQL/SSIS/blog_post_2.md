---
title: Control Flow
layout: default
parent: SSIS
nav_order: 2
description: "Control Flow"
date: 2013-12-16
tags: [SQL,SSIS]
postid: "3264539737350734592"
---
[控制流程（Control Flow）](http://technet.microsoft.com/zh-tw/library/ms137681.aspx)是 SSIS package 中很重要的元素。  每一個 SSIS package 都會包含一個「控制流程」和數個「資料流程」構成的。  

SSIS 中的「控制流程」，大至包含以下三種不同類型的元素：  

- **工作（Tasks）**：負責內容的執行。
- **容器 (Containers)**：結構化封裝內容。
- **優先順序條件約束（Precedence Constraints） Constraints**：控管工作或容器的執行順序。
- 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjOuhZ7Pyljn_xeYT2-oG6z88f4rvyBHJ5f2YP6KxyXqc5_w91w0ziKhSp2lgIKdE2coOITje9c0o1AgeAvhuLFhdA9B3FKGXLuKJ1dUqzfhUoALb0MioPC35A_D7eum_Dt9WTX9fWsgvA/s0/ssis-control-flow-01.gif)

# Planning a Complex Data Movement

資料轉移工作一般可分成簡單型和複雜型。  簡單型工作指的是單純的將資料由來源端搬移到目的地。  而複雜型工作通常在移轉前必須先經過一些轉換處理的步驟。  一般來說，轉換處理步驟中不外乎下面幾種狀況：  

- **Data cleansing**
- **Data normalization**
- **Data type conversion**
- **Data translation**
- **Data validation**
- **Data calculation and data aggregation**
- **Data pivoting and data unpivoting**

# 工作（Tasks）

「工作」是用來定義封裝控制流程中所要執行的工作單位，每個「工作」裡面則包含著數個真正要執行的作業。  例如你可以在 Control Flow 中加入一個「執行 SQL 工作」，然後透過它的編輯功能，設定連線內容以及要執行的SQL命令。  如果封裝包含超過一項「工作」，則「工作」會在控制流程中按照「優先順序條件約束」連接依序執行。   

當設計 SSIS 封裝的**控制流程**時，SSDT 提供許多好用的【Tasks】元件，每個【Tasks】元件都表示不同性質的工作，根據工作的性質，Task 元件可分成以下幾種類別：  

## Data Preparation Tasks

這類工作，大都用來取得資料，準備做更進一步的處理工作。

- [File System Task](http://technet.microsoft.com/zh-tw/library/ms140185.aspx)
- [FTP Task](http://technet.microsoft.com/zh-tw/library/ms137656.aspx)
- [Web Service Task](http://technet.microsoft.com/zh-tw/library/ms140114.aspx)
- [XML Task](http://technet.microsoft.com/zh-tw/library/ms141055.aspx)
- [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhno9Zh9lyvbAyALlxaKawx0WcKf_Uz8FuAeTvgjjBDnJguyAko8XfUBCYpLiG3Vasl1Et4TJ3tYCXD-2VVYnSbslYum0m3jMJNy2BiS2WSGKTp5is4BTe_fKE3agLylfD2nEc2zb2hIoA/s0/ssis-task-data-prepare.png)

## Workflow Tasks

這類工作，大都是SSIS用來與外部程序（SQL Server之外的程序）進行互動的工作。

- [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx)
- [Execute Process Task](http://technet.microsoft.com/zh-tw/library/ms141166.aspx)
- [Message Queue Task](http://technet.microsoft.com/zh-tw/library/ms141227.aspx)
- [Send Mail Task](http://technet.microsoft.com/zh-tw/library/ms142165.aspx)
- [WMI Data Reader Task](http://technet.microsoft.com/zh-tw/library/ms141744.aspx)
- [WMI Event Watcher Task](http://technet.microsoft.com/zh-tw/library/ms141130.aspx)
- [Expression Task](http://technet.microsoft.com/zh-tw/library/hh213137.aspx)
- [CDC Control Task](http://technet.microsoft.com/zh-tw/library/hh758674.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgeP5zPQfYyCPb85F_iyHttYIorc5OoRhxNLR-8AauqG9dE9mv0ltMNJkFKzNVwyBMoGEFWLNBoICJF8VgxowLYznHr57fZTVrmx9rYL9itIt7t1uxKmLwmlu-9I6d1pqv5fEAYvhPDYMI/s0/ssis-task-workflow.png)

## Data Movement Tasks

These tasks either participate in or facilitate data movements.

- [Bulk Insert Task](http://technet.microsoft.com/zh-tw/library/ms141239.aspx)
- [Executes SQL Task](http://technet.microsoft.com/zh-tw/library/ms141227.aspx)
- [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqv7FvyUG-KiGuRElkVZjf7pKv37dep7hOLgAHnSTYFvIR9Yk8ZqTpyIIliKYmYIF476pYqX-ehxYd-8W8IRkoRv-ohurOtj3OSD3IE0gGnp5Cjs80aozgyXZLBDHmtKrNC-Ij874V8Ho/s0/ssis-task-data-movement.png)

## SQL Server Administration Tasks

這類工作大都用來自動執行 SQL 系統管理員要做的事情。

- [Transfer Database Task](http://technet.microsoft.com/zh-tw/library/ms141204.aspx)
- [Transfer Error Messages Task](http://technet.microsoft.com/zh-tw/library/ms141674.aspx)
- [Transfer Jobs Task](http://technet.microsoft.com/zh-tw/library/ms137568.aspx)
- [Transfer Logins Task](http://technet.microsoft.com/zh-tw/library/ms137870.aspx)
- [Transfer Master Stored Procedures Task](http://technet.microsoft.com/zh-tw/library/ms141256.aspx)
- [Transfer SQL Server Objects Task](http://technet.microsoft.com/zh-tw/library/ms142159.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJhDV_X13fgww-CFhWj3fa9Y9wEjwOo87E7VFye7ClF_k5Y7W8csz5SRAGdj3JIF-vTuqsZLA6wKHeSdP7xzmYgD6NQYrSWFtxxem_ZDPRhD-aOBfyE8N0HOSBhIm5VlknHZgp3A7zFoQ/s0/ssis-task-sql-admin.png)

## SQL Server Maintenance Tasks

這類工作大都用來自動執行資料庫維護的工作。

- [Back Up Database Task](http://technet.microsoft.com/zh-tw/library/ms141164.aspx)
- [Check Database Integrity Task](http://technet.microsoft.com/zh-tw/library/ms139858.aspx)
- [Execute SQL Server Agent Job Task](http://technet.microsoft.com/zh-tw/library/ms137858.aspx)
- [Execute T-SQL Statement Task](http://technet.microsoft.com/en-us/library/ms139753.aspx)
- [History Cleanup Task](http://technet.microsoft.com/zh-tw/library/ms139794.aspx)
- [Maintenance Cleanup Task](http://technet.microsoft.com/zh-tw/library/ms345177.aspx)
- [Notify Operator Task](http://technet.microsoft.com/zh-tw/library/ms140060.aspx)
- [Rebuild Index Task](http://technet.microsoft.com/zh-tw/library/ms137718.aspx)
- [Reorganize Index Task](http://technet.microsoft.com/zh-tw/library/ms141243.aspx)
- [Shrink Database Task](http://technet.microsoft.com/zh-tw/library/ms141819.aspx)
- [Update Statistics Task](http://technet.microsoft.com/zh-tw/library/ms137599.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjc25XZ7CEGHc8YyDGY8RKGhHrbxVYRjT2vJCBHzLYzMARXXMESQ6J7n7-rnGPjrv2RcNNZUKqBY1kxqZz_jPqHETXrxIIxo-smhr1ssxRP_vno1p9BBwAcYQr1DxdgmPGnWgcwQbv4334/s0/ssis-task-sql-maintenance.png)

#### [Execute T-SQL Statement Task](http://technet.microsoft.com/en-us/library/ms139753.aspx) vs [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 

這二者都是用來執行 SQL 指令，其中 [Execute T-SQL Statement Task](http://technet.microsoft.com/en-us/library/ms139753.aspx) 使用較少記憶體，較少CPU，不過，就只能單純的對某個 T-SQL 送出執行，彈性非常差。  例如，參數化查詢、屬性運算式、將結果儲存到變數，這些功能都不支援，只能使用 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 才行。

## Analysis Services Tasks

These tasks create, alter, drop, and process Analysis Services objects as well as perform data retrieval operations.

- [Analysis Services Execute DDL Task](http://technet.microsoft.com/zh-tw/library/ms139988.aspx)
- [Analysis Services Processing Task](http://technet.microsoft.com/zh-tw/library/ms141779.aspx)
- [Data Mining Query Task](http://technet.microsoft.com/zh-tw/library/ms141728.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXG7MS5B4p7KhH0o5-QucO9GIialg_q-PPcNxqgAWDN9ewxpsHNFrIbHbXZlSrI_j-r5fNVl-RsHaG3BZkqxWRswBSZOrqj6DGg8xHeN_cT7y5h9kuH-7G8y45y3-FVyvQUWtvLOkYXZw/s0/ssis-task-anysis-services.png)

## The Script Task

[Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx)   提供彈性的方法，讓你可以在封裝中，直接透過程式的方法，去執行你想要做的工作。  在程式中，你除了可以參考所有 .NET 內建的類別庫外，也可以參考你自訂的元件來協助開發。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1AC3-vQQ42mqxfuPR4YfQzC35jRnkJojRmkF-567AHWfRex0j-xFwdoYLDrgZOtWlEqZ28lF5-9-gjvbIg8XfuiNTs6r9S1JpJpnvII9Gg-SFDVw5ipC1KHgP_P-O47hLMivxbWL3K9E/s0/ssis-task-script.png)

## Custom Tasks

# 容器（Containers）

Containers provide structure, encapsulation, and scope.  Containers allow Tasks form a single unit to also behave as a single unit  

#### SSIS 包含三種容器類型：

- [For Loop Container](http://technet.microsoft.com/zh-tw/library/ms139956.aspx)：For 迴圈容器
- [Foreach Loop Container](http://technet.microsoft.com/zh-tw/library/ms141724.aspx)：Foreach 迴圈容器
- [Sequence Container](http://technet.microsoft.com/zh-tw/library/ms139855.aspx)：時序容器，單純用來將數個工作合成一個小單位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiC53vMaDs-yHIkwP5DHAi57uZg5jiUV1n15AvqSmwjBbuIuJGzwMX2eVOZ9dGJjBs7NT18nq7QOmsRIUaaFR-qvLHwPNeVH1K-5qiVj3DSxdSSr6trHqpY95dAJtTYqG3cL8VRYFSf_bI/s0/ssis-containers.png)

另外，在 MSDN 上還有提到一個 [Task Host Container](http://technet.microsoft.com/zh-tw/library/ms139911.aspx) ，  它其實就是最基本的容器，每個 Task 都被封裝在一個 Task Host Container 之中。  

## Foreach 迴圈容器

「[Foreach 迴圈容器](http://technet.microsoft.com/zh-tw/library/ms141724.aspx)」與語言中的 foreach 迴圈結構類似，都是使用「 Foreach 列舉值（foreach enumerator）」的概念。  SSIS 提供下列列舉值類型（enumerator type）：   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYWe3Tjf4k7lQJJFv_lWfBAbRqR_3fY_g4xTUYfUuR-VRUlw4FvQ1SpFwKQEsowoC7cQa9PGXc4NfVOCzVLXnKJ-a4Wjtog4BKdxBWu6g8Wc3IJ3BC25a5QMN97fOaOIgxcZ49M9Q_7F0/s0/ssis-foreach-enumerators.png)

# 優先順序條件約束（Precedence Constraints）

##### Precedence Constraints

- A precedence constraint links two executables: the precedence executable and the constrained executable.
- define the Task's sequence
- define when to stop the execution in case of failure and how to respond to such situations.
- 

### 評估作業（evaluation operations）

Constraints 可以使用四種評估作業方式來控制流程。  

- 條件約束：依據 precedence executable 的執行結果來決定是否執行 constrained executable 。(這是預設選項）
- 運算式：設定一個運算式做為評估，若值為 true，才執行 constrained executable 。
- 運算式與條件約束：BJ4
- 運算式或條件約束：BJ4

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpBTrxG5uH8Mze61-n3WqW8vyY94yDbEv53a01PmwrDLAUQ9AEyUmhxvc8iuMX3RjllK-0kPHlR8YHlefMPr9Xnny7pTO_n3eKiujv9I2bg9wnrAxV74X2awIKy4P3LxFJM8sKtczFaIQ/s0/ssis-precedence-constraints-4.png)

### 執行結果

當評估作業包含條約束時，針對 precedence executable 的執行結果，有三種類型：

- 成功（success）：若前一個作業有成功執行，才接續執行下一個作業。
- 失敗（failure）：若前一個作業失敗了，才接續執行下一個作業。
- 完成（completion）：不管前一個作業是成功或失敗，當完成後就接下一個作業。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8VMmP6Fa-L9i0CfAQ9AbsHg2-upKKTvVei8JlW5B4Na4gfXkMH2C7nNXC5NjpytFegHOEIgTVdIdgeEO-Qd1NKBOZR5Jqv9jWS69c4QkRssoCQsjjqM6M0_WIyrCiZg65d6LDtBOduJg/s0/ssis-precedence-constraints-5.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9V96-796VHMaCapeUkjlvbBubqxcU4YTvyNqNWnUBx5-tExV80zwhRC-rZxB5bOh2uYkTS1f8dtss-m_nXGpiTwG4e9YvozKxOyPTzaKl_ZUeIvPdiBtlW1DdpbOSeSLQOY2evjEnZX4/s0/ssis-precedence-constraints.png)

### 平行處理

若有多項資料要處理時，如果這些資料是彼此獨立的，你就可以使用多個 Task 來平行處理。  如果這些資料必須考量到先後順序，你就可以使用**優先順序條件約束**來控管它們的執行順序。  例如下圖中的維度資料表：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjP2yNjxQXTZVCUgHZ7-js3daxGkWiyx2nN0mNR7jDh22IJxkQfFsJjKZanHN_1aSJuhrdPCcdhLMJSNaHrXvA_fLiJvnf5RnAh8amSUPmuOHB5SEdJ41OznJkQMFbMAUNbyyWn_L8cshs/s0/ssis-precedence-constraints-2.png)

若要轉移這些資料，你可以依下圖設計方式，讓 DimCurrency DimAccount DimScenario 可以先同時處理，接著再依序處理 DimOrganization 和 FactFinance。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_YEjTRMU_px9Z46OPAzRLUFBNcdiJPUVD9nO8sk_o95NskK1IL8a4JbszgNyraZ_dzukPq44eGiVMrz0WKrnvNQqYrv_9lHEQvxbTZ0V68GFYEeErCxv1n__jSFYTjavxbL8pQQnpQ-8/s0/ssis-precedence-constraints-3.png)

### 多個優先順序條件約束

你也可以同時使用多個條件約束來組裝較複雜的案例，然後透過 **LogicalAnd** 屬性設定每一個條件約束的必要性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJ3eL6ESitCZQPf4rx-FzQ91K8HPJrB5VT9o41lmBk8cJ1r91EbgMHiYWULf_DSPjqGUIPiAFxg3lxLvhhQ7L_L21VlfcxzAtExcTszxZPV52vvjQ7vt32P-IPvZobV6yBM7LzhuFvy-A/s0/ssis-precedence-constraints-6.png)

例如下面這個例子，三個條件約束的 LogicalAnd 屬性都定為 And 關連性，所以，工作 A 必須成功執行、工作 B 必須失敗，而且工作 C 必須成功執行，才可以執行工作 D。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhypbyZdLQk7cCX9tF-jUWJHqvgZ2noUtHU0QGi7Wk18MlYBO2DhwtuDF5dZgBR-WEqMj_nA_Bt74pLigKetST6QLtnXg7MJ7eEDFPSxBe5eMdR0PqDhEWezk8kmH1oQD_XJ-eoqWdDDyE/s0/ssis-precedence-constraints-7.png)
## 參考資料  

- [控制流程（Control Flow）](http://technet.microsoft.com/zh-tw/library/ms137681.aspx)
- [工作（Tasks）](http://technet.microsoft.com/zh-tw/library/ms139892.aspx)
- [容器（Containers）](http://technet.microsoft.com/zh-tw/library/ms137728.aspx)
- [優先順序條件約束（Precedence Constraints）](http://technet.microsoft.com/zh-tw/library/ms141261.aspx)
- [Integration Services 工作](http://msdn.microsoft.com/zh-tw/library/ms139892.aspx)