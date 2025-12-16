---
title: 校能調教工具
layout: default
parent: Tuning Performance
nav_order: 10
description: "校能調教工具"
date: 2013-06-22
tags: [SQL,Tuning Performance]
postid: "3819573741020730197"
---
在進行效能調校時，可以使用以下工具對執行工作進行分析或追蹤，以瞭解效能不佳的問題癥結。

- 分析工具：例如 「工作執行計畫」或「Database Engine Tuning Advisor」。
- 追蹤工具：例如 「SQL Trace」和「效能監視器」。
- 記錄工具：例如 「Windows 事件記錄檔」或「SQL Server 錯誤記錄檔」。

# 執行計畫（Work Plan）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5wC3ApDpuBlBoKFonk4iqDkdsJU5CY109zrgMPPu9BeXAXclav8HTsd0AmgH4_fav0JqnlmZFWCo8TbCtgV3QFib3hLgwmEcnf7aOUwJu4rMgB9ZMcV8PoxgBu3BR2sXiDMCQ6FPLiXM/s0/sql-tools-execute-plan.png)

# Database Engine Tuning Advisor

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJY_LbXpPD6CPWb_MIm1dU0qMCFyv3h1fCHlk4EGTsSmz72UtxZzolPchb8CRvPkGu-n9g5d6dxXx48AheOTPbOMofzPg52GRilCh3GTCafMAclb8avRgcy-vT4ZRtNJe8apyZse8MAgQ/s0/sql-tools-tuning-advisor.png)

若要 Tuning 某個效能低落的查詢作業，如果在不完全了解資料庫結構情狀下，很難找出最佳化的調整方法。  Microsoft Database Engine Tuning Advisor (DTA) 是 MS-SQL 中一個用來分析資料庫效能的工具，  你只要提供一些 TSQL 腳本給 DTA，它就可以針對這些 TSQL 對整個資料庫進行分析，然後提供索引、索引檢視或資料表資料分割最佳化的查詢效能建議。  

DTA 在進行分析時，將會耗用大量的處理器與記憶體資源。若要避免生產伺服器的速度減緩，請趁伺服器空閒時再微調資料庫。

下表是 DTA 可以提供的資訊：

- 利用查詢最佳化工具來分析工作負載中的查詢，以建議資料庫索引的最佳混合情況。
- 針對工作負載所參考的資料庫來建議對齊或非對齊的資料分割。
- 建議工作負載所參考之資料庫的索引檢視。
- 分析所提出之變更的效果，其中包括索引用法、資料表之間的查詢分佈，以及工作負載中的查詢效能。
- 建議針對一小組問題查詢來微調資料庫的方式。
- 可讓您指定磁碟空間條件約束之類的進階選項來自訂建議。
- 提供報表來總結針對給定工作負載來實作建議的效果。

使用方法，請參考以下連結。
[善用Database Engine Tuning Advisor改善SQL效率](http://www.dotblogs.com.tw/jimmyyu/archive/2009/10/13/11039.aspx)    ## 參考資料  

- [Database Engine Tuning Advisor 概觀](http://msdn.microsoft.com/zh-tw/library/ms173494.aspx)
- [教學課程: Database Engine Tuning Advisor](http://technet.microsoft.com/zh-tw/library/ms166575.aspx)
- [善用Database Engine Tuning Advisor改善SQL效率](http://www.dotblogs.com.tw/jimmyyu/archive/2009/10/13/11039.aspx)
- [動態管理檢視和函數 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms188754%28v=sql.110%29.aspx)

# SQL Server Profiler

[SQL Server Profiler](http://technet.microsoft.com/zh-tw/library/ms181091.aspx) 是一個事件追蹤工具，它是「SQL Trace」的圖形化使用者介面。  您可以利用它來建立和管理追蹤，追蹤到的事件會儲存於追蹤檔案中，你可以利用它來診斷分析問題，也可以利用它重新執行特定的一連串步驟。   例如，您可以監視實際執行環境，查看哪些預存程序由於執行太慢而影響效能。  

## 設定 SQL Profiler

### 啟動 SQL Profiler

你可以由 SSMS 中的工具啟動

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOA1iXgKozJVRN2JcO17s9ih6j_hUsyPol4LNM4JuVIiA4wjK5Ks8im4uq2UzsDcNZ1GgNEGbIlM18eZKLSXZ9TwIDOhjCzlTHoiOD2NAv0NxP39_Z9dJ1-MOPEnJw_5_hd6f8TD2k5wk/s0/sql-tools-sql-profile.png)

或者由 [開始] -&gt; [程式集] -&gt; [Microsoft SQL Server 2008 R2] -&gt; [效能工具] -&gt; [SQL Server Profiler]

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilgP2SYnMvBk1xoLGetZGoT70J7vH1Hg3jEzd0w55L_v4Qnw_AqC_n8bWdIMGrApOMP5VPY4w7sDOP4PpODJbdhqffY2t4sHjJJiV0fBJFvP4gsIbRwkbhUHdIApF1Vi9ds2hMj7iZosM/s0/sql-tools-sql-profile-01.png)

### New Trace

新增追蹤

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0ey2-5QKXiVITQw9X6b0A5hN-9v6Z-Wlkw-rMUSpb3_ZrWkYhyphenhyphen-N5_PcQweUUJA0cMATK5i8wfOgtoVkr6SuHluw_713ohKkAr8bqFm6-m0dp4FvSgnlJg2wL_M4JxxoxMBuZhyphenhyphenDQe0g/s0/sql-tools-sql-profile-02.png)

設定監控的資料庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0Yxl5drxVulpuUVsdVeXAvS1V9m6WSDVUYRaRQ7l29XsoW1TWyF10wIhXAlclUsxnuHV2X2hiwnkoZAAb-mK7YXbujc5rINrmuodty0BSuGRZvev_w2_sr_8Quf2_TWv3J_7DrrPD2jY/s0/sql-tools-sql-profile-03.png)

### 設定追蹤屬性

- 使用範本 ：[Standard]
- 儲存至檔案：勾選
- 檔案大小上限：10, 超過限定大小，會換用另一個檔案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-CYSnkC4nM5i0qJGUgv-3t5FpnX8HpjwXC_iVt0LWygVhLkQBZzmbH_HmIAM7T4f4wgu3xi1qXZjHGtHEJyA3qmhNZXQMtnPqLPDr8rbxd3OWvPZhroHQZlQI-lmcLgi22g6Z-4L7TzU/s850/sql-tools-sql-profile-04.png)

### 設定追蹤事件

有些追蹤事件或資料行不在預設的Grid中，你可以勾選右下角的顯示所有..，以進行設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhpTFhSbMsjqZuhvJOH397Hmo96z7fuWpvGetxXv0Vb_u_6Ci4fWdqyfHbiUhE70Fqzuuc1Dxoilb9CUynNGdrC6CeOfvsuCnGH2O2LuDeXFLn_TVFGhpfbsS6ZkJOKmLG79LzMzUuBi4Q/s850/sql-tools-sql-profile-06.png)

### 開始執行

當設定完成，按下[執行]，事件追蹤就會開始執行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigHvMIarhGDoknpqj2HDcJtz5gPZhu_QKZ7sKL67V1PAqPIRswgSY09JtpSPR1Ttf3NT1mw_6WT990JOFR1O1Qsc4rlJeGnwX2uUEJiUxPYty2Egot6MJ-f9SfC9aCDW8WPLeexuQ_kkc/s850/sql-tools-sql-profile-08.png)

### 儲存範本

你可以將目前設定的追蹤事件，儲存成範本，以便日後重複使用。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfGi2-rIYFswZGAG8O4jHA8TbHO4dO8VgoBCurgk80iDZv67GEo2NdYH0GTnqmrWu8qieIHvsbhjAOQmrvVUQDySBklLDGReN1vxrPrT86RX1cADv1ktUrLXvtj78G1fxrAMnw-Sv0Cj0/s0/sql-tools-sql-profile-07.png)

### 使用範本建立追蹤

當你新增追蹤時，就可以在範本選項中，找到先前儲存的範本。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhchxDFLd03Cz5TqtUlzSpCB0rBUS5Zwpp_b6VzthEVe-Nxu9UBiTmkKOUbIJnegxlp_YHMwpTO_7qCezlCd5HXoPcNGGMA3IzyXVGbSCIvQ5eghM38GzuGe6ptCD1XDhgz4ybgHpjBOqI/s0/sql-tools-sql-profile-09.png)
## 參考資料  

- [Server Profiler](http://technet.microsoft.com/zh-tw/library/ms181091.aspx)
- [How To: Use SQL Profiler](http://msdn.microsoft.com/en-us/library/ff650699.aspx)

# SQL Trace

SQL Profiler 使用圖形介面來執行追蹤活動，你也可以使用 SQL Trace 來建立追蹤。  SQL Trace 透過系統預存程序，建立執行個體的追蹤。  您可以從自己的應用程式中使用這些系統預存程序以手動建立追蹤，而不是使用 SQL Server Profiler 建立追蹤。  如此一來，就可以依照您的企業需求撰寫自訂的應用程式。  

「SQL Trace」與 「SQL Server Profiler」兩者之間，還一項重要的差異：  SQL Server Profiler 在系統負荷過重的狀況下有可能不追蹤某些事件。  但使用「SQL Trace」即使在負荷過重的狀況下仍不會略過任何事件。  

若要使用預存程序來定義自己的追蹤，其流程如下：

1. 使用 sp\_trace\_setevent 來指定要擷取的事件。
2. 指定事件篩選條件。
3. 使用 sp\_trace\_create 來指定擷取事件資料的目的地。

自訂的「SQL Trace」必須透過啟動程序才會執行，你無法設定它隨 SQL Server 重啟後自動重新執行。  不過 SQL Server 本身會執行一個預設的追蹤，叫做「default server-side trace」，它是會自動執行的追蹤。  這個追蹤會補捉資料庫的成長或者物件被建立或刪除等事件。  你可以透過 [sp_configure](http://msdn.microsoft.com/en-us/library/ms188787.aspx) 來啟用或停用這個預設的追蹤。  更多細節可以參考MSDN：[default trace enabled Server Configuration Option](http://msdn.microsoft.com/en-us/library/ms175513%28v=sql.110%29.aspx)  
## 參考資料  

- [SQL Trace 簡介](http://technet.microsoft.com/zh-tw/library/ms191006.aspx)

# 效能監視器（Performance Monitor）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWB0YnF1STBq6Zt9gb38gQCHRXqW1lzgJY_tE0SorWE8TXn21iFXgHczFP_Bv6lzCXFd8iGhHiMhLTQPNoEsDOFxzgBkKD9ZJiAbk5ZERVpRosLxfmC8ad1qzmKk3x-W4g6o7DkvKTGss/s836/performance02.png)

[效能監視器]和[SQL Profiler]是二個常用來監控SQL Server效能的工具。  

[效能監視器]消耗的資源非常的少，可以長時間的進行系統的監控。  它可以監控某個時段的某個物件的數值變化，如 CPU 使用率是否過高，但無法知道過高的原因為何。  

[SQL Profiler]工具錄製完成後，並沒有圖型化的介面，只有單純的SQL語法檢視，所以只能自行分析找出問題。  方法上較常見的就是將追蹤資料轉存到資料庫之後，再透過 SQL 語法進行分析，從其中找出 CPU、I/O 使用資源較高的部份，另外也可以找出執行最久的語法。  但是此工具錄製時，由於消耗的資源較多，所以較不適合長時間的進行，需指定特定的時間進行，比方說如果問題大約都發生在早上10:00，建議可以在9:30分的時候就先行啟動，然後錄製持續到10:30。  

## 什麼是效能監視器

「效能監視器」是一種系統核心的工具，它是用來收集各個應用程式所發佈的統計資訊。  系統管理員可以檢視分析這些統計資訊，改進系統以提昇執行效率。    它提供二種類形的即時資訊：**健康監視**（health monitor）和**效能計數器**（performance counters.）。  

底下是使用效能監視器會用到觀念：

- An **object** is a resource that can be monitored.
- An **object** exposes one or more counters.
- A **counter** might have several instances if more than one resource of that type exists.

例如 Processor 這個物件就包含數個計數計。  它專門提供與處理程序(process)相關的數據，  例如「% Processor Time 」計數器，它就是用來表示 CPU 的忙碌程度，而「\_Total」值則表示所有執行個體的總和。

## 與資料庫相關的監控物件

SQL Server 也在效能監視器上發佈相當多的 object 和 counter，其使用的命名規則如下：

- SQLServer:＜object name＞：Used for default instances
- MSSQL$＜instance&gt;:＜object＞：Used for named instances
- SQLAgent$＜instance＞:＜object＞：Used for SQL Server Agent

SQL Server 的執行效能大多受到下列因素影響：

- CPU
- Memory
- File I/O
- Locking, blocking, or deadlocking
- Networking

「效能監視器」上有各種的物件可以監控，為了調整 SQL Server 的執行效能，你可以針對上表中相關的物件進行監控，與資料庫相關的物件：

- System
- Memory
- Paging File
- Process
- Processor
- System
- Physical Disk
- Network interface
- SQL Server    - SQL Server:Access Methods
- SQL Server:Buffer Manager
- SQL Server:Databases
- SQL Server:General Statistics
- SQL Server:Locks
- SQL Server:Memory Manager
- SQL Server:SQL Statistics
- SQL Server:Transactions
- SQL Server:Wait Statistics

## 設定效能監視器

「資料收集器集合」（Data Collector Set）是 Windows 效能監視器中，用來進行效能監視與報告的建置區塊。它會將多個資料收集點組織到可用以檢閱或記錄效能的單一元件。  也就是針對可能影響執行效能的物件，你可以將他們挑選出來，合在一起監控，它就會將這些物件測得的數據，產出一份整合的報表。  

下面操作，示範如何在效能監視器設定一個「資料收集器集合」。

### 1.開啟效能監視器

你可以點選 [開始] -&gt; [控製台] -&gt; [系統及安全性] -&gt; [系統管理工具] -&gt; [效能監視器]

或者在開始的命令列直接輸入：**perfmon**，就可以開啟效能監視器。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDA3vL_9lIx1rKMYg9a39qghFbf1WUvoqutEKGJOx7s_BoPQfQ-bWJkuL_ToM0eV57meWF5XvIVfyaQ6BQatDb5PhG4Szrkoc-VbucpkeGUSxgCFZUAzJ6tpX-G2piwXWvyoOlcf1SRAM/s0/sql-perfmon-01.png)

### 2.新增資料收集器集合

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJzvMSkaOqoZQfd1udZcXdcBejvOO_ScKrX2z-5wfOCGvAApyD-ir5n1nPeWv9ZGTOvTwOjo7DG14Edw-ac36VYO2k8S2nt-28mGtQbCZoLMQJ3lr20cT7RUkHfC7R3Akm8wmBtptHi7k/s0/sql-perfmon-02.png)

### 3.輸入此組監視器的名稱

這個步驟，我們選擇自訂監控物件

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgukpwlwGdPsQN4ttUz46nPq-8alw6ZwZgdTG7qNFJh1icO7XzZHWgE6fE7PZigDKVo9jVx81XFtqsHYXSGhPSIDHdmrgAitNHZcbfYd5UjGe0MzYVUPhbQhoToMlDFnsdYhoARoQ665b4/s0/sql-perfmon-03.png)

### 4.設定收集類型

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGudt6REQu9TYCvj7XOPzUrRM8Wr35TGOD8VTvGALlZZbbJB05ZzLtwa0VIzzMpvpCo8qqh2gPqY6sWzg4Ds3v35t2ydOcHR8DnZVl1HMeQb9xNGI3qZ0h2AGz8cdO_y0L8zK3S67WOFM/s0/sql-perfmon-04.png)

### 5.設定要監控的物件

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAtskZ-eUtvjRCoKp25zlMLHj1YxNInkMErX8LUhgGVs1-rmR-0qq0PYOUCqBKOEnQfWe4QfTsXInRjtCGKdguyvbPVLh3lgqyy7ra9ThMjhktKw9WqPgQtxyJm1ERYhy2SYaLGXQuyW8/s0/sql-perfmon-05.png)

### 6.設定收集器集合儲存位置

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbQOXsuWKAcLvpPVI4MfchpXcKcSiR60NwcOjT4bC64A26bv8RgGWRMgPtLq_AHKRuq2LgGduqApa-zCdVFzgSDjHFxUXNrTLVie9NuxiCKl1ct8GXkIT92YAJjwnYmOVzw_03Hnhraws/s0/sql-perfmon-06.png)

### 7.設定執行身份

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDhpDVSU7GIvVBNn2-JpW-TBISd17F2t7JjysKDPu6ZXnaPmRgDKy5r-sYhaQF0f40xko6V-lqxerFzR7MxNSo7ri_uEoxLwA-BmPcBKgca6amgcXE75GJIarA1GaKlfARM8lZV-vO-Gw/s0/sql-perfmon-07.png)

### 8.開始錄製

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbeZ_iV-5AFKVk8i-5HLpJIsGeVMNqeEdizCjbkM1H00OsHbMobsWtkaZW4QZfRce4c9HUsJbdROPw8hsH6E1bK6LcMKAHNAf5DNDcPN7N_6Mo0iIQaJOBfUA6774O9IBuLVuqGaTiLUU/s0/sql-perfmon-08.png)

### 9.檢視結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBT60eBshmvP7SFTfE00SNA1wE6uPGv6qaQ1F54p7TRaoLC-dX64hjy7e6UW0xC-jOnXi7WBoo8_iEQPxh29j4uktflEt_fSXg5XK4BYjzHqiHG2djw6d1SNtWQZnLAQd2n6mzI2ZZpuk/s0/sql-perfmon-09.png)
## 參考資料  

- [Windows 效能監視器](http://technet.microsoft.com/zh-tw/library/cc749249.aspx)
- [監控 SQL Server 狀況](http://technet.microsoft.com/zh-tw/library/bb838723.aspx)
- [建立資料收集器集合工具](http://technet.microsoft.com/zh-tw/library/cc749337.aspx)

# 活動監視器（Activity Monitor）

「活動監視器」是用來監視 SQL Server 目前的執行狀況，例如 Process,  Resource, I/O 等等的使用狀況。  它是透過 DMVs 和 DMFs 等相關系統檢視或函式以取得目前系統的狀態，例如：  

- [sys.dm_os_waiting_tasks](http://technet.microsoft.com/zh-tw/library/ms188743.aspx) ：傳回有關等候某項資源的工作等候佇列資訊。
- [sys.dm_exec_sessions](http://msdn.microsoft.com/zh-tw/library/ms176013.aspx) ：傳回在 SQL Server 上每個已驗證的工作階段。
- [sys.dm_exec_requests](http://msdn.microsoft.com/zh-tw/library/ms177648.aspx) ：傳回在 SQL Server 內部執行之每個要求的相關資訊。

## 啟動「活動監視器」

你可以由底下二個位置啟動「活動監視器」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjA9WyJYBwJu0-zCmb7ggHJlJhyZvKTjFhica1FWC0dJdEOI0W5miQhkX-D_B4LgJxhZDe85nsG7YuO11M6-ahXzgZ37D1g93a_gIB7EtGwhuxIc8NnXpjaE4NotildWXALG6YzA4zVRZA/s0/sql-tools-active-monitor.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_Azz4_4WdHz8sW2Hl-MY7mBzehSZR1GFtr__C_bofr8X3K4xs_EiOFTddzuOzvVTY5ehGkKNcY68FdYYFisoMs0jBmF6mbCt25_FuP8zUMPoRrlEvx-b3Tfy4vJFiZvUhK2hN0wd5T8w/s0/sql-tools-active-monitor-2.png)  

下圖為「活動監視器」畫面
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEisW-S8HopWXcaplIvPX0oLs6UWhSm83tLNgFjUIWxkyaYLMD3kDn4bOOn5PyommHD1L7r-vNw6ZNUmDjJGNwBYy6PjQXrxL3jeVFZBZ3eM-Hca9tgpsAUXGDM6ZtPbq_3tOixrFW-IFG0/s850/sql-tools-active-monitor-3.png)

# SQL Server 錯誤記錄

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJl5dyr9wyUw_UJK8X8uGWk0rvqjHcaNHYVGCQtJ-3Q7JqePaYzdBUTEez2vjTJ9utkp24W7bpHV_6OQQMYd_DrpgyEh9TGpNGHHs9s2eIq9t-_TXgqINSUP1VvqFSzn6hFVI2-OCXBFM/s0/sql-error-logs.png)

SQL Server 會將特定系統事件記錄到 SQL Server 錯誤記錄檔；若是使用者自訂事件，則記錄到 Windows 應用程式記錄檔中。

每次重啟 SQL Server 的執行個體時，錯誤記錄檔就會重新建立，你也可以使用 sp\_cycle\_errorlog 系統預存程序直接循環錯誤記錄檔，而不必重新啟動 SQL Server 的執行個體。

### 查詢SQL Server錯誤記錄檔，所存放的路徑
```sql
SELECT ServerProperty('ErrorLogFileName')
```

### 查詢SQL Server錯誤記錄檔的檔案資料
```sql
exec master.dbo.sp_enumerrorlogs
```

### 設定 SQL Server 錯誤記錄檔數量
```sql
--調整為 10 份(至多為 99 份)。
EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE',
N'Software\Microsoft\MSSQLServer\MSSQLServer',
N'NumErrorLogs', REG_DWORD, 10
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZx1LaOGXigUwgufyLK-YlgeAm0sFXPdlWyi5hi5BtyiVS11h9koeSpya83eGZAfum069LGuMbhJLs6RUlL8cg6PMsOvMFaOr_G7QbCfwIM2PuO6CMBDF7nPfGzuo8tk1niEZVOJN_cSo/s850/sql-error-logs-2.png)
## 參考資料  

- [顯示圖形執行計畫 (SQL Server Management Studio)](http://technet.microsoft.com/zh-tw/library/ms178071%28v=sql.105%29.aspx)
- [Database Engine Tuning Advisor 概觀](http://technet.microsoft.com/zh-tw/library/ms173494%28v=sql.105%29.aspx)
- [開啟活動監視器](http://msdn.microsoft.com/zh-tw/library/ms175518.aspx)
- [Windows 效能監視器](http://technet.microsoft.com/zh-tw/library/cc749249.aspx)
- [使用 SQL Server Profiler 檢視和分析追蹤](http://technet.microsoft.com/zh-tw/library/ms175848.aspx)
- [檢視 SQL Server 錯誤記錄](http://technet.microsoft.com/zh-tw/library/ms187885%28v=sql.105%29.aspx)