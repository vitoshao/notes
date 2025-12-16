---
title: 疑難排除與效能調整
layout: default
parent: SSIS
nav_order: 12
description: "疑難排除與效能調整"
date: 2014-01-08
tags: [SQL,SSIS]
postid: "8672247116455286448"
---
# Troubleshooting SSIS Package

## Design-Time 除錯

### Breakpoints

我們常會在程式碼除錯時使用中斷點技巧，在 package 中也可以使用中斷點，不過它只可以用在 control-flow。   若你對某個 task 設定中斷，控制流程就會在這個工作暫停。  

1. Edit Breakpoint

你可以在控制流程中的工作上設定中斷點。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWfhBpc_9fX1EX8GwLvL5qlvWAARN9da9m3c7iJtQxfGmMLTgVIFlOa0kBqpk6_ww5kpiDEIy8NMvOPNjakn8LnJrGvtJ-7DJNOCkVAPdSQ7VhlPO8Lbs6cujFR8Zv8CmKrk-r6b_yczM/s0/ssis-debug-01.png)

2. Setting Breakpoint

封裝的中斷不像程式碼可以明確指定在第幾行中斷，它是利用執行工作的**事件處理**來引發中斷。  同時，你還可以利用**叫用計數類型**，來加強設定中斷條件。  

- 叫用計數類型（Hit Count Type）：用來設定引發中斷條件類型
- 叫用計數（Hit Count）：設定引發中斷條件的計數

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0K50JkMZtT_qE7BnD4__D8MBYJeCkSbzc_HUDaUIq1xOShRQ5559Fh9Wss9sSxAt_NP-MbHqJCMC4_9icCWGTaPmTP4SDNuNEpRRnpfDtIEqCcLzZbk2w8RiFAKWeYpGUFNcQoH_Co3s/s0/ssis-debug-02.png)

3. Breakpoint (a red dot)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjieEbGpb-48bgXeQEDgIA5f5D5Vwrqjh6EWTeOsuS6C5Qgn-Dqn8Ix9n-9LN_asY68uFnhqLYCBOPPt_9NJ3uS8PzYdirrnFPc19LZ6clO88o09iPDT3z0VGRNFMkiwyxYLJGf2R6y1zU/s0/ssis-debug-03.png)

### Troubleshoot on Task paused

當中斷發生時，你可以使用以下功能進行除錯。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGUBiczngozanAqcMCOVaBVUnkXnecuVHe-4Gx5uB2Svl-RtwJETIS3b8VWcz4-9hSXMEfJVALVWQGm-J-C6SCjiYq2BG7gHBIODS3l5R85Lf0DR7RVa0xFbSdj0rtSLCF-n_VU7AieSw/s0/ssis-debug-05.png)

1. 查看區域變數。

上圖中，當紅點中有個黃色箭號，表示工作已被中斷，此時你可以開啟區域變數視窗觀查變數值的變化。  例如，你可以設定事件 OnPreExecution 和 OnPostExecution 要發生中斷，觀查變數值的變化。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFDcsLD3fgTdqaqkonfuKnwZEvRlMo3tKuidFo3FfLKa6fK7vsrjW75j62Eni9uyRpQY4m-dU8Vfv54WYWKp1sBTxi1K0AvUU7Bw8W6e389Eg_ueZK9x3VYQb6R0A3BQrD401dutTnttw/s0/ssis-debug-04.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJpgf9jqRuON1FYBldTA19PU1Ccl0ueWO89h0Qof9NMgqk5z8-uyQ1JytZQe7yR1ezL1CNXguB8sgO0to9rVnXer4BOnloXNxbCoqJDQshCA48Drta-0MVHHVzZP3zRLC3bSFdEgMHgOs/s0/ssis-debug-06.png)

2. 修改區域變數。

你也可以在此時修改區域變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDpNvPWcBmrNZuUdf9vA9vECU6JV6elTwGPJC0_0B18WP6PDvzFiz8zfRFKKZA1TSNvUtOa2snqrcVCXqQaXrku_ttBaQ4g3t8vXWZuPwUFe4X3NUNnF4CPE-6ronWgz3_OP5oRWN62Qo/s0/ssis-debug-07.png)

3. 查看堆疊(stack)。

你可以透過堆疊視窗中的資訊，查看在中斷點之前，哪些工作已經執行過了。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxcKis3WPF6Nl_OqSDDHBvfDXQnVdyh_Guv4eKJrz-H9HFklqMAyNnqWLNg_bYwiOJ1U8rGkFpOrUJHl9Ikw5gxj0_yH3_My6YEQV5no0WkWdre_rTIU3uDh6T-At81cWdTuz275apaQs/s0/ssis-debug-08.png)

4. 停止或繼續。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEik0_yvrFmvpOPYk5Wc6h1THzo-AiixNnI1LspakSB2JLi8LIL0K1zS-au2Ht9VZaDx8UXxs1_GR-X67X4mTV_DNo_ej_J2TrOtV-mw1MXa8Q9sbEXcRY6XRpPIAKvajyNP16peZZE3os4/s0/ssis-debug-09.png)

### Using Data Viewers in the Data Flow

Breakpoints 用來中斷 Control-Flow 中的 Task ，如果你想要檢視 Data-Flow 中資料列的處理狀況，那麼就必須使用 **Data Viewer** 。  

1.執行路徑(path)的右鍵選單中「編輯」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjYg_UPhJcCw5H06n1vj6esU-ZE1Ar1n-zsQXk-1468VUYJ_a2M897JjFxlM3eoYM-HEJ0whixaZN8fTFAx76EsmnkQcilXL9U8HwQdb9HsnxK87nqicNNdR9aemA3b8D9QiNfwfV6P9UU/s0/ssis-debug-10.png)

在「資料檢視」頁籤中設定你要檢視的欄位，並勾選「啟用資料檢視器」項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCf5MW-cw9psmZBJpEsdBTuW7LJ1aheRgYV2rPsCLZMk2YT0wYboY2AEc4PDHm0Vg_995tc36lTaI5UypkHHocNMAmNkSL8o_5h5r51O9tSRpc6H2bOoWlP8CoVjd2aLwK1W5tI8plW4E/s0/ssis-debug-11.png)

有設定「資料檢視」的路徑狀態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgO-5glY4Yjyfgoaclvn5eocRD2d8QzcYTaQlpHSB-QoKOlTJNKuamxcfYqnPp6di9wN1KBsAeO2LWfQg3dj1lJ8Co5EncYqH6-WILRZiAdT1r2p73q4tYeLvGHJAVr7bzgJQRvPR9-4D8/s0/ssis-debug-12.png)

2.你也可以直接在路徑(path)的右鍵選單中執行「啟用資料檢視器」。

這功能預設會直接勾選所有資料欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzauql9URvCFpo-PlcTPYTmoIHThae8EWMdvCf-zuV4IKLkureEn6kUoNQoDYBySRTJ_EBZQM-oEVGuNucOAeCKiSSNdwfb9FkOYoYmBBI3EiMcI8RbafpYDzqJT9D4dE0NIM-_WL_NK0/s0/ssis-debug-13.png)

3. 當資料流執行到你設定的路徑中時，就會跳出一個Grid視窗，列出目前資料流中的資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7cy4Cao6CbNNk8mxYPwKYZQDHRqKtyh_nRplBODvtcNUONMwomjgxDxd2lghEjEAj4L7pFWEmbcRXRXIZy3u1sYuXoMHvfesCAZ2a13OADEGh8hDJFGSTV5c0t1vyAV_hOt79vjk6a8M/s0/ssis-debug-14.png)

4. 停用、啟用、刪除資料檢視器或中斷點。

你可以停用中斷，若一個個停用太慢，也可以全部停用或刪除。

如果選擇全部停用或刪除，包含控制流程中的中斷點，或者資料流程中的資料檢視器，都會一起停用或刪除。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBY0UipwNEAcpMPisGUDha6-xKDYkGKtZ2K2Y5Uj_yAYNQh4DXx7XSJJb5T2OlNzcWhhyn18K6bI8r69DZiZi9dhvFdYZcYSau_twPg9jWwFxac-Av9gZHW39k8j-4rw-d0doTcS30dEE/s0/ssis-debug-15.png)

### Using Other Methods for Debugging

下列幾個除錯技巧也可以用在設計階段：

#### 使用錯誤輸出(error outputs)

在 Data-Flow 中的每個元件，都會有一個 error flow ，你可以由這個輸出檢視哪些失敗的資料。  

#### 使用訊息視窗

在 Controol-Flow 中，你可以利用 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 來顯示訊息視窗。

在 Data-Flow 中，你可以利用 [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 來顯示訊息視窗。

#### 使用較少量的資料做測試

偵錯時，你可以使用 [Percentage Sampling Transformation](http://technet.microsoft.com/en-us/library/ms139864.aspx) 或 [Row Sampling Transformation](http://technet.microsoft.com/en-us/library/ms141200.aspx) 來取得範本資料。

#### 捕捉已處理資料筆數

你可以利用 [Row Count Transformation](http://technet.microsoft.com/en-us/library/ms141136.aspx) 補捉資料來源的筆數，與最後寫入資料目的地的筆數做比對，驗證資料筆數是否符合。

## Production-Time 除錯

### Using the SSISDB Catalog

執行階段的除錯，除了透過 logging 方式外，在 SSIS 2012 中，最重要的新功能就是 SSISDB Catalog。  SSISDB Catalog 是一個中央儲存區，可用來管理所有的專案、封裝、參數、環境變數，  在封裝的執行過程中，它也可以自動記錄下這些過程。  

SSISDB Catalog 預設的記錄層次為[基本]，你可以在[目錄屬性]視窗中將預設記錄層次更改為「詳細資訊(Verbose)」，以記錄更多的資訊。  

#### Logging levels

- **None**: 關閉記錄功能。 只記錄封裝執行狀態。
- **Basic**: 此為預設值。記錄所有事件，自訂和診斷事件除外。
- **Performance**: 只記錄效能統計資料，以及 OnError 和 OnWarning 事件。
- **Verbose**: 記錄所有事件，包括自訂和診斷事件。 會將過程中的每個步驟都詳記紀錄產生一筆資料，可透過 [catalog.execution_data_statistics](http://technet.microsoft.com/zh-tw/library/hh230986.aspx) 檢視顯示。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUvjYnoR_CtVPBu5ysWMgDbqUAVqHsA1vRPJQ8rewYz7jUUgj6ejQa8DoCnbX7M-keAU7WHO7eHwAQDcrzjDL888FnyGxxm_dd5ZlU3eKEszizzVANTT8oYYaKd-E1LkZ_VbwLSp9KfU4/s0/ssis-debug-17.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgc2JumdiiXZw1Rp_Ap4tNSFXFe7PBDv9O3r9qUpXDBipGQuBMKJcmOVlPGLIqpZYhO8NzvIDBpfN0SyrBLFtZnX1QtsRnYm7FunveHeiAtbvJF4nQpvFFGGjAnH-5uWvBruCjGK75iOvE/s0/ssis-debug-16.png)

同時 SSISDB Catalog 上面也有許多事先定義好的報表，你可以使用這些報表來查詢封裝的執行資訊。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUFyN7FVieSl0tm5cBwU7LjeTUZrd0X3AUDwU3sTm_k9xEYX1lHRel4HmTeXk_uRGe8w5GlBH1x1wNs0GhtvpL-lhz6mgjS6KkwV3MZA50y3BX6LmWUY-3_vOTfCpY88Ci_rW-OPKBYAQ/s609/ssis-debug-23.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihClfhik1oEApQQEjCLzm-E47pKoX1VVlshO_NK8TKeLuSFyI3Nq5_55kRPVuDoV-2xC_XOsHy8Tg_IbnT5WPvPfWx6fkt05QTlae2CwafCaey9bGA86r5KY_06KZSJwSLnk-RfVMebLw/s850/ssis-debug-24.png)

點選上圖中的［概觀］，還可進一步查看詳細資料。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEX7GLqJEAlfNUk5U8qZDBcegNlkyCGzZ0r0iufbevKPr1LfvU-yn3Waa5U3Xp_Yho0LEFNdrnfM6H8j9Uu2vPJUeeZZovgONtPrhP8qwh9Z8QOnAkXHEKkoxNdbbLSVIbjLIn0RiijIM/s850/ssis-debug-19.png)

或者，你可以使用 SSISDB 資料庫中的目錄檢視(catalog views)，透過 TSQL 方式來查詢封裝的執行資訊。  
```sql
select 
execution_id, folder_name, project_name, environment_name, 
executed_as_name, created_time, status, start_time, end_time, 
caller_name, server_name
from 
catalog.executions
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOfiSMnk1riURAOZG3s2listN4_tbfsD2c59puVFTnti9EAwVUh1GkHDsWteg4UDPJd8SSSKSJw9ZKRGyRjjbiA625ixVPTizRlW6bp8CVH9aZku7OVHnZifXiTJofBpyQdovSY0xS4xc/s850/ssis-debug-25.png)

### Using Data Taps

前面提到過，在 Design-Time 時，你可以使用「資料檢視器」（Data Viewer）來查看資料流中的資料，  可是如果在 Production-Time 時，這個功能自然無法使用，這時你就可以使用 SQL 2012 提供的新功能：**「Data Taps（資料竊聽器）」**。  它功能就類似「資料檢視器」，你可以設定流過某個元件的資料，自動轉出成為逗號分割的檔案。  

PS. Data Flow Taps 在 MSDN 中翻譯成「資料流程點選」。

要啟用 Data Taps ，必須先執行位於 SSISDB 資料庫中的下列預存程序：

1. 使用 [catalog.create_execution](http://technet.microsoft.com/en-us/library/ff878034.aspx) 建立封裝的執行個體。
2. 使用 [catalog.add_data_tap](http://technet.microsoft.com/en-us/library/hh230989.aspx) 加入 taps 。
3. 使用 [catalog.start_execution](http://technet.microsoft.com/en-us/library/ff878160.aspx) 執行封裝。
4. 最後再利用 [catalog.executions](http://technet.microsoft.com/zh-tw/library/ff878089.aspx) 可取得封裝執行狀況。

#### catalog.add\_data\_tap 的幾個參數：

- **@execution\_id** ：裝執行id
- **@task\_package\_path** ：這個是封裝中，Data Flow Task 的路徑。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXowzIvGbR-6P964sCfLgN_idhZLv2GjTMAiCFl35aUqYWHrho5Rjwp007epY6YqilKIxBG8pMOj6sVk1L5mKAK7ADryh7KR8S2JeoqkVt97DO5ttrNPNaUId5RsBupf-0dTy2CajV2DE/s0/ssis-debug-20.png)
- **@dataflow\_path\_id\_string** ：這個在 Data Flow 中你要竊聽的那個路徑的識別。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJmSZ4WnKrN70z7ApJvWSMxGcoRbVTwa3KVgwqalyGVh6xb4YMkndSP_un1RjlvcjwFeP2E3ErZkyonzwAVDgBztwbrYf3d4gWjSEzyGrwsZtJQaal9fHpjEQ9xcDJ53ZKQ20nbzVMBgk/s0/ssis-debug-21.png)
- **@data\_filename** ：輸出檔案名稱。預設會儲存在 DataDumps 資料夾 (C:\Program Files\Microsoft SQL Server\110\DTS\DataDumps)

PS.這個例子執行不會成功，那個 dataflow\_path\_id\_string 參數過不了，感覺是中文版的問題。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikAby-Eehr7S4_FMMgIB8i4B_rmQJOdT9j-AWoWQ-YhhaK2qcaaD0hX_LsZtsAHyf7wG2PLI4l-OuFG5aHwSZt1S9KilyU8PxNTi9eupG7kirEVc5mhIxBHLj4mCb6TW9nk2n7q_c1eWk/s0/ssis-debug-22.png)

# Performance Tuning

## SSIS Data Flow Engine

## Data Flow Tuning Options

## Parallel Execution in SSIS

## Troubleshooting and Benchmarking Performance
## 參考資料  

- [Integration Services (SSIS) 事件處理常式](http://msdn.microsoft.com/zh-tw/library/ms140223.aspx)
- [Data Flow Taps](http://msdn.microsoft.com/en-us/library/jj655339.aspx)
- [Integration Services (SSIS) Logging](http://msdn.microsoft.com/en-us/library/ms140246.aspx)