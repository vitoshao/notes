---
title: Lab6：使用變數
layout: default
parent: Labs
nav_order: 6
description: "Lab6：使用變數"
date: 2013-12-26
tags: [SQL,Labs]
postid: "3147302880762196506"
---
# 簡單變數使用

這個範例，簡單練習如何建立變數，並將變數套用在 SSIS 的 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 中。

## 1. 在變數窗格中加入變數

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6WdWDxYeVx5qMDBRjzpeXJum0oG67zbtwbgRzHgP_i6BlQzAfWBwxZ-rKrPDDXo_L4crvqjQIFqGZ9yIfQ2dlyzWpwF0sZh9iBi0xiEOOeLuFSfHA-A4O6Zf7JHv21z2M03jNtvq4f0Y/s0/ssis-lab6-01.png)

## 2. 將變數套用到 Execute SQL Task

在 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 的編輯視窗中，設定以下屬性：

- SQLSourceType =&gt; Variable
- SourceVariable =&gt; truncateStgCustomer

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgp6hdNyn4-tZVmenRJKjYJqySmdyrB18mkQaOw2rYQ-0BUCnT1q9AWvoezJC0bxv0sBRamcKYTg3-XgejdVxJwUErx8Fr5k578_GQXJIfO2aj_3k799yXeywRp2blBDu9gkFtY5l_G-xY/s0/ssis-lab6-03.png)

# 在 SSIS Objects 中套用變數

在底下範例，我們將練習，如何建立 run time 才能決定值的變數，而不是在 design time 就決定的。  

## 在 Expression 中套用變數

下面例子練習，如何將變數套用在運算式中。

### 1.建立變數

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjapzVc5PZ4BoUTkNfZ8QmFLNBGI4PwlOA4x5xI7CWXPHrsJyRiUznYW2jWjF9ds15motWMYxcaAqVY4KyfIL2Mqkjl37HCMRI8S2uMUZz8CJHm7CW3YjMpWkXnjzPqzMG6FSwr2QaT_wk/s0/ssis-lab6-04.png)

### 2.在[運算式產生器]中設定運算式

點一下變數窗格最右側的(...)按鈕，就會跳出運算式產生器（Expression Builder），它是一個相當方便的操作介面，你可以介面中找到以下功能：

- 左邊樹狀選單：系統變數或自訂變數
- 右邊樹狀選單：SSIS expression function
- 評估運算式：expression test

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJaTMs3aF8ei3krKXYJVf7n42Le8eYDG48WWCaBtsA5wiXtMqwm2ve5_EpXA5hWXiXEDC3skv8ysn3rvsJdFO0p9YCgfpy4dv57UB7x1Xi1wdTc3w2mHzLOyONlTT2ZShDbVeAPjzq_Bc/s0/ssis-lab6-05.png)

## 使用 Expressions 控制 Data Flow 行為

很多 SSIS 物件都有 Expression 屬性可以設定。各個物件的 expression 都有各自的功用。  底下練習將示範如何設定 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) 執行時所使用的緩衝區大小。  [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) 的緩衝區大小由  DefaultBufferMaxRow 屬性控制，預設值為 10000 。  我們可以透過 Property Expressions 來變更這些屬性設定。  

### 1. 建立變數，用來判斷執行日期

下面例子中，我們先建立一個變數，用以取得執行的星期。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQYidVFEalsmg9OIVasrulayHtLCkNfR2EiHBN62CsEq_O8BN-KIBMtiI0FayhloaxlFFWtlt0E68HBx0_w6o78uYu3716-DIS7QOUy2pscB9-1h3U_miKvTx_d22SVkC5LGeprzx52RU/s850/ssis-lab6-06.png)
(DT_UI1)DATEPART( "Weekday", GETDATE())

### 2. 設定 Data Flow Task 的 Expressions 屬性

我們要將變數套用到 Data Flow 上，所以開啟[屬性運算式編輯器]，

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOMbU4X7IezOOk6NCB3YHbj5Q-TGiwQ8iSprh7VngwYMno_406kRkn8UauwNRirXb_Jx5wkouE_l6FxYgTbA-Sjs157qBqXr_PK7_a-zhnP-1RwuM5NlrvfefxnJtpVKkGtwGVHXG_VvQ/s0/ssis-lab6-07.png)

在[屬性運算式編輯器]，加入二個屬性運算式：

- DefaultBufferMaxRow =&gt; @[User::dayOfWeek] == 6 || @[User::dayOfWeek] == 7 ? 20000 : 10000
- DefaultBufferSize =&gt; @[User::dayOfWeek] == 6 || @[User::dayOfWeek] == 7 ? 20971520 : 10485760

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhhB9dnUY83zn0E8ZZyjP-Tj1PuQCXiNWvtedpAX-pAmBn3J1sTMvnhvQGl7yC0JyPqg7SlpO0z-ynPTIiwAf-jCXSI_YnPLUoMqy67eizmkXfHvGJdy70JBaJmI3-OuJ2mKtrr6itjTLQ/s0/ssis-lab6-08.png)

以上設定， User::dayOfWeek 是我們自訂的變數，會回傳執行日期。  我們將它套用到這二個屬性的運算式中，以動態決定其屬性值。  這個設定，若 package 的執行時間若為週末，則使用較大的緩衝區；若為週間日，則使用較小的緩衝區，以減少資源的耗用。  

關於這二個屬性，你可以在右邊連結找到詳細說明。[MSDN：資料流程效能的功能](http://technet.microsoft.com/zh-tw/library/ms141031.aspx)

# 使用 Master Package

底下範例，我們將練習如何建立 Master Package ，並且在父封裝中 invoke 子封裝，並且傳遞參數值給子封裝。  例子中會用到在 lab5 中建立的二個封裝 Staging.dtsx 和 DimCustomer\_New.dtsx 。  

## 設定子封裝

DimCustomer\_New.dtsx 是我們要由主封裝中啟動的子封裝中的其中一個。  底下練習將示範主封裝(Master)該如何送出參數值，以及子封裝(DimCustomer\_New)該如何取得參數值。  

### 1. 在 DimCustomer\_New 中加入參數

開啟 DimCustomer\_New 封裝，並在[參數]標籤中新增一個參數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgA-ejBBpDBWfvAEqr7IPhPrBVGNqfB3D41dX6e2CqOzjxL54Wakm_opw3oTlejnPlT0aGNHHawJqzHYuqlrxx_ByZ4YEF7NXe3WZjh74_64_oY6IGY1IunTI36HkH4Rsr_0jLFkRhDfIo/s0/ssis-lab6-10.png)

這個 MasterPackageID 是個 package-scoped 參數，用來接收父封裝的傳來的識別碼。  不過由於這個封裝不一定會由父封裝叫用，所以底下我們會再增設一個 invokePackageID 變數。  若是由父封裝啟動，則 invokePackageID 就等於 MasterPackageID，若不是由父封裝啟動，則 invokePackageID 就會等於子封裝本身的 PackageID （本身的 PackageID 可由系統變數最得）。

### 2. 在 DimCustomer\_New 中加入變數

在 DimCustomer\_New package 中加入以下三個變數

- newRecordCount：The number of rows added to the Customer dimension.
- oldRecordCount：The number of rows modified to the Customer dimension.
- invokePackageID：identifier from MasterPackageID parameter or package's own identifier

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUZWEQjm7Wr6UiEgqTM_cp0_9-hZZEdHFK2k9wsCKf49WU595nrtt6cplQR7HS4d0CoJXt5BX5XkPotzdrB_FQ32qYoYNKtxFL_XnI1glkboa3FcKVsOiF0W9ywMCGq7H23Zc-9gbEIqI/s0/ssis-lab6-11.png)

在 invokePackageID 的運算式中，我們去判斷參數 MasterPackageID 是否有值，若沒有則使用自已封裝的 PackageID .
@[$Package::MasterPackageID] == "" || ISNULL(@[$Package::MasterPackageID])  ? @[System::PackageID]  : @[$Package::MasterPackageID]  
在父封裝中，我們希望能夠知道子封裝是否有新增資料，所以當 DimCustomer\_New 封裝結束之前，我們必須將這三個變數值會儲存至 dbo.ETLHistory 資料表，讓父封裝可以判斷是否有資料列新增。  
```sql
CREATE TABLE [dbo].[ETLHistory](
[PackageID]				[uniqueidentifier]	NOT NULL,
[RunTime]				[datetime]			NOT NULL,
[NewRecordCount]		[int]				NOT NULL,
[ModifiedRecordCount]	[int]				NOT NULL,
CONSTRAINT [PK_ETLHistory] PRIMARY KEY CLUSTERED 
(
[PackageID] ASC,
[RunTime] ASC
)
)
```

### 3. 取得修改筆數

接下來我們將修改 DimCustomer\_New 封裝的流程，以取得新增筆數與筆數。

在 Control Flow 中加入一個 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 用來取得修改筆數，並儲存在 oldRecordCount 變數中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSfQRIpEOHn1Fh3xPMfUxKWpf_2oxrJU3Kh-F1cpD-lBs9iQc7VKKZRBZF-McpPbBNeKq-bpFloRk2uJsTRh7VQRA07562n8gGp2wAHWV5jTP5-90KGu6YM9xZJbc8A7OXRni3Jt9wV50/s0/ssis-lab6-27.png)

設定 Task 的屬性

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIFIU2eGkbBsr4QpnhurYmJUknvMMoq5VNaFb560Ms1JDOqxEKC8LMGuCLYvR88sv2jtRRTipjVvpFZ7mXp-ZouGnM9xtS_sTbJuqHElskIx3FHGdphdoYW7ZfLX63nKrTtfmJEM8RADs/s0/ssis-lab6-23.png)
```sql
SELECT	COUNT(\*) as ModifiedRowCount
FROM dbo.DimCustomers AS C
INNER JOIN stg.DimCustomersUpdate AS U ON U.CustomerKey = C.CustomerKey
```

在[結果集]標籤中，將查詢結果儲存至 oldRecordCount 變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhtGpGUMrv9Bo3xgCdonfe5emPfmvRv2A4qbjRpBnRyw9jRAc5A2Y5F66WRSwg28WX5_WsrH1lFC8jRNE9rz_Vp1S1RuErtxBgiM1WwIlkjPKoaElhhUK3Bcbg_9MZ1vNLzDxq34d9sjA/s0/ssis-lab6-24.png)

### 4. 取得新增筆數

在封裝的 Data Flow 頁籤中加入一個 [Row Count Transformation](http://technet.microsoft.com/en-us/library/ms141136.aspx) 用來取得新增筆數，並儲存在 newRecordCount 變數中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5WZj8S2GTR7kR7Vp1OtbNg24v4MxrcMNX2f3mhqy3zEAm37yqPaxftFUeZHp64kMDD0xx1AMWQ-QwA-HmAePPQf0rHO8_B-dQcBvEsE_Q0PnVdd-MHVTWOMJMeGf-ZEMMlUVwwFNlpSo/s0/ssis-lab6-25.png)

由 [Row Count Transformation](http://technet.microsoft.com/en-us/library/ms141136.aspx) 的編輯視窗中，將資料列數指派給 newRecordCount 變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgaMC6CIn2sJMmS18HVWsdJFaegprP8d_f04M0j7NWQnJ3z0_mmMmf9_0VAHJ7kXKfo_5oasoEpJIo_t6e1EpX3U3XmCClUjaNDDwi4Zj5JkgeEDUHSYlCGRDkmH-pyIK_GLJ1SZ6iUJOw/s0/ssis-lab6-26.png)

### 5. 記錄修改與新增筆數

在 Control Flow 中加入一個 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 用來記錄異動的筆數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1G5gV6dwtJGbQQA8lKPn7CPjOYL11eFv732cmEt9A28N2Ur9tkaUYDAibYRlcnyporpy9MemrwqqmWrwDdghrOKdlPoNBOHEmvVgh-Xt_1NAkqyChn8Cpts9_hR4d2U3fZCoRVRrDyzY/s0/ssis-lab6-28.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgN9CBQAbIjruUVO1dRsCWJe5dKoEd_fT9xBhn6TTRSFW5Kn83l5lsOGLGxGLEvjLOzQhK7qy-pVbdNYXETPOmIMFBPyTOB3d_GqANCbG7ae8ThejLfbiSB3Y9fmt7KJfOaQ14k-MUHz3Y/s0/ssis-lab6-29.png)
```sql
INSERT dbo.ETLHistory
(
PackageID,
NewRecordCount,
ModifiedRecordCount
)
SELECT ?, ?, ?
```

設定參數對應：@p1,@p2,@p3 會依序對應到 SQLStatement 中的每一個「?」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVa392DPXsoGw1kel10M2UcPQ5nohsMY5v12_EaCyF5As5GtCdcO35UFY1hQzXMvlA1138h7RSMfb2kEhhdaY7fDorjnEgGihuzB0xsNdsP7WXpFk4UkD8e3w2dl5GIsFCcOU6Dpjp9AM/s0/ssis-lab6-30.png)

## 建立父封裝

### 1. 加入 Execute Package Task

新增一個 package ,命名為 Master.dtsx ，並加入以下 Task 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9H74Zhbz0X6FeRDiIGDPP7cw_wRem82W921YVBaeBWVikv0Q3zThh0RQ3P_aMAVaBi6qMJBk-0kNKPfXalAPeCuQyBZbNEHPPoZan3bZY6CNe0l6pUwor5DIM0Y2nZdT2n4UwN2Vg_A0/s0/ssis-lab6-09.png)

### 2. 設定參考封裝

依照以下步驟，設定每一個 [Execute Package Task](http://technet.microsoft.com/zh-tw/library/ms137609.aspx) 要 invoke 的子封裝。

2.1 設定 Task1 的啟動封裝 (FillStageTables)

ReferenceType有二個選項：

- 專案參考：被invoke的封裝如果在專案內部，則使用專案參考。
- 外部參考：被invoke的封裝如果在 SSIS 伺服器或者檔案系統中，則使用外部參考。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGPue49aAeDL9TRnOvGIF7ZbJ9I_rzmxv_CJeC_KMsuUSyXI51Xlnzk5E3ptwRn3_shDma5297zSkGO8karEkj7OUM6j3y0UY5z1I2ESh2kBq-YVoF6Z1fjqeUwDuQYLgi51rH1dCuXF8/s0/ssis-lab6-12.png)

2.2 設定 Task2 的啟動封裝 (DimCustomerNew)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitstwn0eqFoPYKIWtJwgdaXIFSuKtWU3q5LQRFrJn8smQ7-Z2m-9LUAxOPuY2ktQwfCsH0qL3pbGwmIBXPQ57wAGRYXgm-3dp_lcHX7skemK_U3V5qn2f-fHhXLNbgEN5HSGDmbTj9dV4/s0/ssis-lab6-13.png)

2.3 設定 Task2 中，要傳遞給子封裝的參數值

在[參數繫結]頁籤中設定要傳遞給子封裝參數值，要將什麼資料參遞給子封裝，就是由這裡指定。

DimCustomer\_New 是這個 Task 要 invoke 的封裝，在前面範例中，我們在該封裝中所設定的參數，都會在這裡的[子封裝參數]的下拉選單中。  若你想將哪個值傳遞給它，就將該變數或參數繫結給它。  

底下設定會將主封裝的 PackageID 傳遞給子封裝的 MasterPackageID 參數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRitZlAj6AUgjZy9msC_iGxZkGyX__VqygcUtSIumf0HT5wPRxTiUkpEqMxrS8OKea8VD_GRMFmOqEJQZhOKA88_EpqiqTKFqyWMC35KyKY9W0Qcatq7t_k9imrt3S59vC2mn9tUOTmRg/s0/ssis-lab6-14.png)

### 3. 設定變數

在父封裝中加入一個變數 totalRecordCount ，用來記錄總筆數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjm3FYjhJlUAZ-Zb6dcpOulK0tWrE0eWQClGddyohHZAlDc75jy3TqLahdO_vtnxRogIWeRq58VA8UB5VpTD8GNUfzJfMRTOP6E5CdktGiKQYDPtdUOAGpBzPpAHjvPieFgFmF_BS2fbjA/s0/ssis-lab6-15.png)

### 4. 設定 SQL Task 內容

這個 SQL Task 主要是用來取得 dbo.ETLHistory 中最新一筆記錄的 NewRecordCount+ModifiedRecordCount。

4.1 連線內容

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgvCCKuHSN4-OqZX0x_r2VeqHyc_vUrPjAeA0CtjC27_Eo8HI3unOOI5NQy-E4eS8wtG1mDOcKCV_0GIdhcf8UGMiTm0cm7cYVXRFAhcCYQAVlGziUGGhsdz5lGRUwvrxFisl4lNMxCqHQ/s0/ssis-lab6-16.png)
SELECT TOP(1)
H.NewRecordCount + H.ModifiedRecordCount AS TotalRecordCount
FROM 
dbo.ETLHistory AS H
WHERE 
H.PackageID = ?
ORDER BY H.RunTime DESC;  
4.2 參數對應

因為在 SQL Statement 中用到了一個「?」，所以在「參數對應」標籤中，我們將這個 @p1 參數套用 System::PackageID 變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigNsfLkYi03tbmZ3mxCpXHpjSs6xPCYyavqqtI7roih309EqiRkfAKJOsZow1GOYT2djhAtmG4B61Ny_7uHYMaADIVVqQ7nUG_jdQauDi9UMsfK66Q-Mw5f71uP3teW_1CbifafoLyi5A/s0/ssis-lab6-17.png)

4.3 結果集

由於4.1步驟中，將這個 Task 的結果設定為單一資料列，所以可以使用一個純量變數來取得。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiC1C5QASr_TD6WDdz2U6QeT2I36VWceurOBeRjg3-TcUavwqgOSHGKQITRK-4-cQw0EZI5Cr67LpblRRv67uzcmd8ZDH1MC5h_WMUZAoG6Texyk2hd7LSJ6ZGqQXo3NXZFO995wUI0-w4/s0/ssis-lab6-18.png)

## 設定父封裝

### 1. 設定工作優先順序

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4nMtf7C1jzv1X5P9W7T9FNDVTx7VQvux01yg0F2qFRs_2BAfPYjRKdyl9G3pTXwgSTx36uljbw-ObvBTmBL8QKnVe_T4BfUXuN89hEdVClmMBgg_stVlXJU8NyLpIk0ex4QBxpCwYYGA/s0/ssis-lab6-22.png)

### 2. 設定工作優先順序的條件

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhB-ryNbqc6M1PoDr5snD7Xg_dluXV3NyvLCsON-pisAbfZqGiJ9UeLCdWw9Vrypgu3KO1txFgWnNCkTCqVnIwFAIeo_IaI3f0YY2dSFwSf-bOxgYu9ebi1Tyjn3U3wdMOWhReiQ5utDKE/s0/ssis-lab6-19.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZEPtkhxEIQKvdOcyHuvG10bWHHjIFfGoewbEGsUyWXvimvTVHPPPXUbbvvd0MrZnnX9Nd9UMLOZ4bI2Fy895fiRVEw9BknjyXi_Eecv641BnIIClJFLaNzPZylgTD7RuDQCqSJBL7HjY/s0/ssis-lab6-20.png)

完成圖

當 DimCustomerNew 這個工作執行完畢後，我們在 GetTotalRecordCount 工作中取得異動總筆數，並存放在 @totalRecordCount 變數中。  同時在 Precedence Constraint 中加入判斷，若異動總筆數&gt;0，則繼續執行 ProcessSSAS 封裝。此處的 ProcessSSAS 只是一個空的示範封裝。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTGWM5Qw8Itxb9ziw9C83BNqSJxxG32qRQdKL2e0qUn5rBXo-Ria4gd35Iq5cCbdFXIpYYNFMipwkJx0VnenHLp2q5w0BQX7-96ltMYru5yzMEHcdLMTu-FzSV3yT7noj6USktukrSZR0/s0/ssis-lab6-21.png)