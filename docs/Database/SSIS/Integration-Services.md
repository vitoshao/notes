---
title: SSIS 服務
layout: default
parent: SSIS
nav_order: 1
description: "了解 Integration Services 服務"
date: 2013-07-10
tags: [SQL,Automatic,SSIS]
postid: "488510212019536678"
---
#### DTS

Data Transformation Services (DTS) 是 SQL 2000 中用來建立封裝，進行資料轉移的服務。

#### SSIS

Microsoft Integration Services (SSIS) 是 SQL 2005 才開始提供服務，它取代了 SQL 2000 的 DTS 功能，可用來建立企業級資料整合和資料轉換方案的平台。   您可利用 SSIS 來解決複雜的商務問題：複製或下載檔案、傳送電子郵件訊息以回應事件、更新資料倉儲、清理和採礦資料，以及管理 SQL Server 物件和資料。  

DTS 由 SQL 2005 開始由 SSIS 取代，若是想要在 SQL 2005, SQL 2008 上繼續支援 DTS 功能，則必須安裝適當的回溯相容元件。但是，SQL 2012 就不再支援 DTS 功能。  如何在 SQL 2008 中繼續使用 DTS ，可參考這篇教學：[匯入與執行 DTS 封裝檔案，以 SQL Server 2008 R2 x64 版本為例](http://sharedderrick.blogspot.tw/2011/05/dts-sql-server-2008-r2-x64.html)

#### BIDS

SQL 2008 提供 Business Intelligence Development Studio (BIDS) 這個工具，可用於開發「商務智慧方案」。  不過，它只整合在 VS 2008 的開發環境，無法在 VS 2010 中使用。  

商務智慧方案包括：

- Reporting Service 專案
- Analysis Service 專案
- Integration Service 專案

所以，使用 BIDS 可以用來建立及維護 Integration Services 專案

#### SSDT

到了 VS2010 ，微軟又提供了新的封裝開發工具，命名為 SQL Server Data Tools(SSDT)。  不過它並不包含在 VS2010 之中，你必須另外[下載安裝](http://msdn.microsoft.com/zh-tw/library/hh500335%28v=vs.103%29.aspx)。  你可以使用 SSDT 建立與維護 Integration Services、Analysis Services、Reporting Services 等專案類型；  也可以使用 SSDT 開發資料庫專案，進行資料庫的建置、偵錯、維護和重構等工作。  

這個工具到了 VS2012 或 SQL2012 已內建在安裝程序裡。

# 了解 Integration Services 服務

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSvJYBy1n0KwVmJLZqkzUckJgKiH8H7lTJADDOWynooe3qBlLURgg2FawXLjBP2ZWocDK9gwUUGvKz37jAjA3jUS4zoXQUY6TDjXB2XKG7qndZJaW21JhnBz4pcYCagS5UDBP5Bg6zXIc/s0/sql-ssis.png)

Integration Services 會安裝 Integration Services 服務，此服務可讓您在 SQL Server Management Studio 中執行以下工作：

- 管理階層式檢視中的 Integration Services 封裝和資料夾。封裝可以儲存在 Database Engine 的執行個體或檔案系統中。
- 監視在電腦上執行之 Integration Services 封裝的執行。

在一部電腦上只能安裝單一 Integration Services 服務的執行個體。此服務並非特定 Database Engine 執行個體特有的。您可以使用執行此服務所在的電腦名稱來連接此服務。

PS. 建立、儲存及執行 Integration Services 封裝時，不需要 Integration Services 服務。

# 在 VS2008 中建立 Integration Services 專案

### 步驟 1：建立 Integration Services 專案

新增 Integration Services 專案

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgv1TLJ1Q3Io93PeGUbFs0eoFQKGNs43pkkrktGNOzHYjn4vx2JlHlQ7ZK0xEpmxaySTqRoOGmeE_cfyET2d024GzmizQV-Fh4Njs76ahz_6OO5Dyu8NU1PogZp-XnxQ6z4xjbvxP94_Aw/s0/sql-create-ssis-01.png)

下圖為新增的 Integration Services 專案，預設專案裡頭會有一個空白的 **SSIS 封裝**，副檔名為        **.dtsx** 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9c-3FLBh6RunUsJfkwhGB6fk2dV7cC-UGA87TV7UBAcnKMcnBB8btcgxVLOgI3kBHw5xGob_PjaZw6NqN_OywN-8sAteLfBAADaH29COkMCADFtjESB4A9kg_E5E-PIp-xJLvWhOySyE/s850/sql-create-ssis-02.png)

### 步驟 2：執行精靈建立基本封裝

利用「匯入匯出精靈」，可以簡單建立一個「資料流程工作」。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOmiwZoROM3R5cP1kR9O-fwoL_GV9ilqIDZOb7WJRJlHW2N3V0keq4YXY2kDNqNg5G1-f4k00tO8Ege1JL35lzlg5T43NLGxyPgEXSpwcXj6z7SUGYLMWqBtRGSBcerFpAq5pHAFkEFA0/s0/sql-create-ssis-03.png)

依序完成匯入匯出精靈中的步驟後，就可以看到下面的結果。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1giJRk5gOwHD8zmAp_Xkqpvi2c5N-L6mzc3znp_KSUBrLZmvYDjNaafCgct5kaI5p0qUy0mTX697q0HsH06HI0nKa7Im8kOUmkxbskdHj8HfxnOLDSn4vprA3I3AJXOX9jcWSFJCJFf4/s850/sql-create-ssis-04.png)

### 步驟 3：測試 Integration Services 封裝

你可以由方案總管中的選單執行封裝

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJm9vMWXYeiX8L6lhz6d1xwpO8rWYnrtRYiFq1qIQWs_NSj9VxsH9ISU1RwXHJVM61stMCgp9S7lTqZoi6bLKXG6olQvg26UyIkQHrH5hs79QX8rEKFv99qTqoSbIJTS1qxIrvuFkQW5I/s0/sql-create-ssis-05.png)

下圖是執行失敗的畫面，你可以在輸出視窗中看到錯誤訊息。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg09fEuTH0V95GxRKm4skoz6nm3EsL8PRy1qfA9nDU5x6BGsSKJZ63HD74mg8cdiYyul3x1oRrLOsNtkNnv4CtT3aUGsTnFHFvVzbN9jThmjcpo3R7-hUroUucOzas60mkW3dwfXruyKQ4/s850/sql-create-ssis-06.png)

### 步驟 4：在封裝中加入「 執行 SQL 工作」

上面範例，我們設定將 DB1.dbo.Customers 匯出到 DB2.dbo.Customers ，但是由於 DB2.dbo.Customers 已有舊資料，所以發生 Key 值重複的錯誤問題。  為了解決這個問題，我們可以在資料匯出資料前，將目標資料表中的資料先行刪除，以避免 Key 值重複問題。  這時候就可以在匯出作業前加入一個「 執行 SQL 工作」的步驟。  並設定二個步驟執行的優先順序。  

加入「 執行 SQL 工作」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi94MXThrlfvQ2xTOeBy2kjS8rV2MzYYdrc8lCu1CJN81fN0p-kvg0dLQj7ednpsgVJOtqYHRoj54BZmIbtKcFoTPuuE3vZHsojohiUUrPL7wTwFMZTQRzrZdIBlby_yvIOVP7Ic1SdbsA/s0/sql-create-ssis-07.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmUSE7JblvhGGzWis27kGQUBoX7J3M9dQGKID8oMhD-3pj2uyUMxFH9FJ0XMhyCppr0DABHEdCB5Wr7Uh-vXgvt6xm0JlcEqavonmUKWdP_wEBlBDr_pUF4Okpg5y01VcxAMyD2PO7ryI/s0/sql-create-ssis-08.png)

執行編輯

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimdmHmK2va_6IcGMoA1uboV6SBv8aoZTQo2NvjmzuMnRTmCRDBGgYIVKAc0Yxxd2zPXkWZcB5_RA8MkL85U4oGJvnyXZcv9455maDTwS5rx0z9VrNoCegyEZdJshGCoppW9TyZvmuX_o8/s0/sql-create-ssis-09.png)

設定 Connection 以及 SQLStatement 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidpG9HqpgHIFlIOFy1eLeueG2WVSTweb9oSMGCaw011m596ghYrSV7GRsOdUK3hR3Cv384XmYvh7UIFiVVFkI4_GY_EZ2MrBlYfkLSG-1JAlGpJ1q_iCs2FXapuS1HgzDU-K-8X-vPmxs/s0/sql-create-ssis-10.png)

此時就可以看到執行成功的畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi44Hl6b1hueoTr0zP8c7caVLubaUC-7F7lDWl7KyNIXPv1FoNv95Q2JCCVoVNA9IPR8AByenD8ozj1ufB8PgjSswYxliHojFtl3ySvFPO49VzwHuLFGFCAmiV6GCTW5W_Mn5yfyRv_3Wo/s0/sql-create-ssis-11.png)

# 如何將 IS 專案設定排程

在上一節建立的 IS 專案中，若我們希望這個封裝內容，可以定時的被執行，必須先將它怖署到 SQL Server 中，再交由 SQL Server Agent 來排程執行。  

## 匯入封裝檔 (.dtsx) 到 Integration Services 

首先，你可以在專案的輸出路徑中找到編譯完成的封裝檔(.dtsx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2CA6ODn6cGqYdiJr6hF-8zoik5DHWuCXWAC1QP_UprPtpuu9zRjpqpQCiMxt9agTtMYehy0XTpG_IrLoqxrZZr4MzikhyxMbx6AX9HeXSCnW3KqAD-dar4FYwzIkedSpWFkFieKXAnsM/s0/sql-ssis-01.png)

連接到Integration Serices

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7lRnKPOGvETbydRBrFzih4Jp9mZ_gQMGMScI20QOly1mNlXopC1RI2s9LqyFW5wXBXwosjo6lA6zPsszrMY60vrPRHUc8MrORisVRBgWhdDcXPtqdHRxP524Fyyd2qlpfwZDWqU7Y-yA/s0/sql-ssis-02.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJF4WT6yoipJ7sJIZtHIWtQw6CeI7A7BrWwEtq71uguYWs7yA4gGZoI5wjv5jMupyV1rTRGn9jE8wsyhhxbFC-qNttI9WSBojl2WhmrUOhEtr_m-CYt6OA7LyEyjnabYMHE4QHcS965Tc/s0/sql-ssis-03.png)

在 File System 節點上，點選匯入封裝

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2xYrRD2hBp7TG-dfZHwg4a9YEtH9I76ZxKTKGsk7Tk7IljCS_Sjt61PpUMmR8rc1QF8v-xUkp15pXDxl_dZUa_LD6PIPARJji0FZrax5_L3Bbbdc1yhPxAhTD1jrcM9XDUVASZvfSj7k/s0/sql-ssis-04.png)

選擇 [檔案系統] 的方式來匯入封裝，並設定封裝檔的路徑

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqxxYQbnxSK3vhyphenhyphen5WOlHh2eaEdyLIEZVttEfQPaSqf4ZoLXA10QVpM2Ntd5Z6AgvSOt5lAGLagnWZ0pMul9VdHjU60iLoM4vaUWC7QhkGEURn2OYDQtYKL_SFlOSgP5Ia9A2ZV8_DHxm4/s0/sql-ssis-05.png)

匯入完成後的結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWi1SbtkkfKRANk6eeo4VxY7fuJsQQaEBzY_TxyO7eC0RL9ikqN28WrF4a2hGFEB-cMsl2TjjnaYW5yqG2iv08jeO3FZf9PPjti8P3xF8F3XSS51Ixxfsj6TQPGJGJF5OSiaAQLySe_44/s0/sql-ssis-06.png)

PS. 將封裝匯入，可以選擇儲存在 SQL Server msdb 資料庫的 sysssispackages 資料表中，也可以選擇儲存在檔案系統中。

## 設定執行排程

要設定排程執行封裝，當然就要透過「SQL Server Agent 作業」，或者利用「維護計畫」來自動建立「SQL Server Agent 作業」。

１。新增 Agent 作業

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkX1iMcDHZlwTOnZ_7ema_b5vRiF4cX8IkD-eBYIiRz9_oVruqSo6vdTCxrqrgUqsZE8o5pNjZRJqDS8Qb15lbEYqYv_C1UibEzc-9HkYDs4S-env_uRMcr2DDAHpCGJ-OKgJMx7I72cY/s0/sql-ssis-07.png)  

２。在「一般」頁籤設定服務作業名稱

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4Bnc4ix8xZaIyqFIsEa4KON6ud306RTsnbR3M-MkM4M2pfot9i1vcWQs8If3SNjQF3lcL4mucMjF92FTtTILVCHUHTGuB9cTdIxsi7qxedKf6WpPwQkmLGhFXFwgRW38GR9Xd_HeoTG0/s0/sql-ssis-08.png)

３。在「步驟」頁籤，新增步驟

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEheeqNKZMGDcs3F_o1lPMbyX3tx2NGcvq6L0MOvawXHcm-JqNbzKDTL3VRVeCu2BwMFb94lL50iyoKk9iMLjYJoOJM9n2fjj0OQ-WxiN3x0sh3R0B3tTpMZNGxtqWe1S5Cd7FIDCI1MPJk/s0/sql-ssis-09.png)

上面這張圖有二個地方要補充說明一下：  

一、在執行身分下方的一般頁籤中，因為我們要執行先前匯入的封裝，所以選擇「SSIS 封裝存放區」當做封裝來源，並選定要執行的封裝。  

二、在這個設定之中，因為我們要讓 SQL Server Agent Job 去執行一個前一個小節所匯入的封裝作業，該封裝在匯入之後，會被存放在 C:\Program Files\Microsoft SQL Server\100\DTS\Packages\ 目錄裡。  所以 SQL Server Agent 的預設執行帳戶「SQLServerSQLAgentUser$」必須對這個目錄具有讀取的權限，才可以正常執行。預設是沒有權限的，所以執行時會產生錯誤。  下面二個方法都可以解決這個問題：  

1. 直接授與「SQLServerSQLAgentUser$」這個使用者對該目錄的讀取權限。
2. 也可以透過 SQL Agent Proxy 來執行。底下示範如何新增一個 SQL Agent Proxy 的方法。

### 設定 SQL Agent Proxy

新增認證

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0SsoToPBgsJUgMIzW9-_rkbiIDhME4XFDHHzLVF40oBZOuhgWi5jyloFbSyvbTNg_1lZzFGNEEaszR9pRnMWn6zR7eEdwdAyAVNgHCyJ5bVdUrmI86yuN0kRcMUdFXFAqlUoEdVqvZfA/s0/sql-agent-proxy-1.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlyHZe9ix7N9Pa5Ml9yCGWRGq96-Qkz6b2H9BxwyaglISW0snW1T-NW_yl5wbP0JmZcdAv72MhO7PVPCw4J9Rv5NBpnVfyH-vnJTS04BEoXoe1CFWQewZvU-S2FxlQhBFIpIFWQ-WBVl8/s0/sql-agent-proxy-2.png)

新增 Proxy

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgYApy20z89uqpc36uMMSzDSdok6BlyoJXvSQu3QT8XLyGu85AaZQovl7eGdm_Mh5A3ZW1WqKIjSEITRZSKzFKpDWRXCX6KayOklpFS7VllZrIUm9qtwGJ2gnsCVEtjrQcCIUO3D9o780/s0/sql-agent-proxy-3.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2QTew6bZOuMlJrNjrGzuhhuB32cdSM9jkErsiGpGBmOHtCeyKdSwGKub4_OLy3r9h0BPGH8-ota9n8uxUMvEgGHXFUwIvkUUJoAmq-Bgye5zby7Epgh18BGPIM6dmudU1qTuPAMpY1W8/s0/sql-agent-proxy-4.png)

在 Proxy 中設定認證名稱，並設定有效子系統

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDx8hXDJqQn1c4qylLALj7CSskO3EHPD6m0KdY3oQELmLYhjipvp8r9VC3xXG1ZTB7Vug-o5btps24tGfr575F_xHKoo3AR9R5ouGqBJA1KXyoQHoMnIdS5WWAY-pBr-t7zX4eyjNB2BU/s0/sql-agent-proxy-5.png)

最後你就可以在 Job 的執行身份中，挑選這個 Proxy

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHbast6TbsHYy_GGNKU8a_O9YE4N9qOkYwt0X8ppFFbn-kABZQBrhp7cHwHSb4TNXT_pU3NI4NC-HWU6L6bWr_4n_fQ0X7bRfsgySwHFQ7oL01O_QxYf5iiPVnReCAOVn7VHd5wRK0Qog/s0/sql-agent-proxy-6.png)

上面的設定是說：  使用指定的 Proxy 身份來執行這個 Job ，而這個 Proxy 使用指定的認證，因為該認證會對應到 Administrator ，所以自然就有權利讀取 C:\Program Files\Microsoft SQL Server\100\DTS\Packages\ 這個目錄。

４。在「排程」頁籤設定執行排程

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhANG968CLxxfA22rtIaVTmbsU71Ki7QmcUdQVGiCV6n6-rNMO_OOJP-qsbQe1RAb1MFdzPFNglKK8PI48l3Mj5Unc7ZxzBEQLbtWwc940Jn4RPAMs_El_cxbS4hPSWEXWnHB_GzteCwpQ/s0/sql-ssis-10.png)

５。手動執行 Agent Job 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-5ih3ojynsMTQvSdyJH0n6SxJXb8VBbX_FGoPHEbWc8doVWKnlgTAC4gxgwL-t8PX2epaJ6N0PFgOdCkH-82gu0sPQZglqiRd2ImCh2ABoBLB5q1OKHq43CQjfrnYNHr_Hy4CbRWsMso/s0/sql-ssis-11.png)

６。將 Agent Job 停用

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEglhiPcW_7Ul87nabRZdI9xEhyC5JiyIemm84XT5bCE98Qi1x0T6kdspTVN9JU3fHcPpoaOvZy__yTrXWMFVNCMq-aCT5I4zeVdV7S6kB5sCccD13bTAYY_hTSYOeWdw1xj-DlR5LgrKpA/s0/sql-ssis-12.png)
## 參考資料  

- [Data Transformation Services (DTS)](http://msdn.microsoft.com/zh-tw/library/cc707786%28v=sql.105%29.aspx)
- [SQL Server Integration Services](http://msdn.microsoft.com/zh-tw/library/ms141026.aspx)
- [Business Intelligence Development Studio 簡介](http://technet.microsoft.com/zh-tw/library/ms173767%28v=sql.105%29.aspx)
- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/zh-tw/library/hh272686%28v=vs.103%29.aspx)
- [安裝 Integration Services](http://msdn.microsoft.com/zh-tw/library/ms143731.aspx)
- [第 1 課：建立基本封裝](http://technet.microsoft.com/zh-tw/library/ms365302%28v=sql.105%29.aspx)
- [封裝備份和還原 (SSIS 服務)](http://technet.microsoft.com/zh-tw/library/ms141699.aspx)
- [匯入和匯出封裝 (SSIS 服務)](http://technet.microsoft.com/zh-tw/library/ms141772.aspx)
- [封裝管理 (SSIS 服務)](http://technet.microsoft.com/zh-tw/library/ms137916.aspx)
- [SQL Server 維護計畫與 SQL Server Agent 作業之間的關係](http://blog.miniasp.com/post/2010/05/26/SQL-Server-Maintance-Plan-and-SQL-Server-Agent-Jobs-Relationship.aspx)
- [如何：建立 Proxy (SQL Server Management Studio)](http://msdn.microsoft.com/zh-tw/library/ms190698%28SQL.105%29.aspx)
- [SSIS功能體驗(二)、將封裝檔匯入SQL Agent](http://www.dotblogs.com.tw/gelis/archive/2010/12/21/20266.aspx)
- [利用 SQL Agent Proxy 執行特殊作業](http://www.dotblogs.com.tw/gelis/archive/2010/12/21/20266.aspx)