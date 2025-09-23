---
title: SQL Server Data Tools
layout: default
parent: Database Manage
nav_order: 2
description: "SQL Server Data Tools"
date: 2013-05-28
tags: [SQL,Database Manage]
postid: "4538265632576272547"
---
#### 資料庫專案

Visual Studio 從 2005 版本開始提供資料庫專案範本，這種專案完全使用TSQL的宣告方式來定義資料庫，可横跨資料庫開發的所以階段。
此外，資料庫專案也可以用來進行資料庫的版本管控，建立測試資料，發佈資料庫，方便整個資料庫的管理作業。  

#### SQL Server Data Tool （SSDT）

到了 Visual Studio 2012 或者 SQL Server 2012 又推出了 SQL Server Data Tool （SSDT）。  它是一套整合在 Visual Studio 裡的資料庫管理工具，讓開發人員幾乎無需使用 SSMS 管理工具，而直接在 Visual Studio 內，就可以完成資料庫的設計、建置、資料庫專案(SSDT-DP)、T-SQL 重構等作業。  SSDT 不僅僅是資料庫管理工具，也是用來開發特殊專案類型工具，如： SSAS、SSRS 和 Integration Services Business Intelligence (BI) 方案 (之前稱為 Business Intelligence Development Studio)。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGAYkw-JAb9C9Khuap4sD6MB1HXEgvEq4k4DI9E3QpKAOGXQHhwrv0q6ix6xTZwTGD-zkWww63WjXhFSWu2d5lQKAme2UcmPfPqjk-M2iM2bfGequaTT4xlNsRLr8hTi5zNAgumMTv-Yk/s0/sql-ssdt-projects.png)

SSDT 已包含在 SQL Server 2012 或 Visual Studio 2012 的安裝檔案裡，  若使用 Visual Studio 2010 ，必須額外[下載安裝程式](http://msdn.microsoft.com/zh-tw/library/hh500335%28v=vs.103%29.aspx)。  

# 建立資料庫專案

底下練習由 visual studio 2010 中，建立一個全新的資料庫，並部署到 Server Server。  「資料庫專案」僅 Premium 和 Ultimate 版本的 VS 有提供，Profession 和 Express 版本不支援。  另外要注意一點，在 visual studio 2010 中的資料庫專案，是區分版本的，你可以一開始就選擇「SQL 2005 資料庫專案」或「SQL 2008 資料庫專案」。  也可以事後再由專案的屬性中，改變「專案設定」頁籤中的「專案版本」。  

## 建立「資料庫專案」

### 1. 加入新的「SQL Server 2008 資料庫專案」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgWGLuXM6TJ4LV7F7DJ3iYNmTwL1SzVpKdyA7z3gyq1lmdHyh8NYvWudKzNX4iqTorMWTLEC4_r-S2mNZsmy39tkTWyrqdheGVqZnJ8gVRR5FQvsRhsCwck8wl747ai_QOe5DVQjqbHxM/s0/dbprj-new-1.png)

### 2. 加入資料表

新增一個資料表

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga1sUfKZpbvMRaHQ5T4H9vAaqG2pmKLPDpZSmAO3WR4Gj0XccFuzSVIXnk5FTWsTesG55LCLfZOELKyUA92wFfs0qHf3mJsVXmyV1VxL0PG_2eR41eqGF7VTDWfri-9gfQo05jWNnpIt0/s0/dbprj-new-2.png)

編輯資料表內容

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhaBhDSJdmMq9Iw25D8beCmFPpVZMvQfBIjDjxnCYsaheqEwvtnA62CroQOm0koh8AFmHGAwnM2NP0ncu-5J-h1sJP9-k7oJIITHmXIWxbdaxHxSqKw56AVYeUo_cZiTCr2_CHrA6GF-o8/s0/dbprj-new-3.png)

編輯完成後，你可以在專案中找到相關的腳本。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEifbVcKaM3fjxGHUMBHocHg_ZN_iGIH-c1Bp5_1vt2c75juW6SlPkHnwj3kf1tP5LbpjdSSw_qSiKB3r3XF8MKffbwILB54qIQTp-mLOldm000JaXjUvWibFuL94x6Z9aMwzuTVzPn3xzY/s0/dbprj-new-4.png)

### 3. 資料庫設定

要發行資料庫前，你必須先設定好資料庫連線。這個設定位於「屬性」視窗中的「部署」頁籤，其中幾個重要項目如下：

- 部署動作（deploy action）：指定部署動作執行時，是只有產生部署腳本，還是要同時執行部署動作。  因為有時專案開發者，並沒有對實際資料庫進行操作的權限，這時就可以選擇僅建立部署腳本，再交由 DBA 去執行該指令碼。
- 目標資料庫設定（Target database settings）：設定目標連接（target connection）與目標資料庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFQnfCEMakfXkIXwXxrMliEaSeZiJg9pa4-PgTZyYnMVG9o7PiZ7UhYkKiV5AM6Gzvnag9A_30bzq5Wn0akwjUaaoudhArifG4SXaeIew5Bh3X0S1vdKWAEbBxmXD3UEurm1YZYMyiN0A/s850/dbprj-new-6.png)

### 4. 部署

最後，只要在專案的右鍵選單中選擇「部署」即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdB1teezEEXu4ePrn1lleaO2mAAzKBwmJYA53523XLDUgwaivP3_eLKJHokTViSGHbAcgPM6ga1J8xKP3rwLbC5HjDUdDkfIkIN40_2E1nxjmAhWIMqOP4ex_zcfQ4lDzbqZ8kbTdWlQ0/s0/dbprj-new-7.png)

### 5. 部署結果

如果順利，你可以在 VS 的輸出視窗中看到以下結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirR_fKE2gfNtBDbY2BWcRGjAIHnSHyBYT04TV7lNDTgBDdlkEP_k_VQrf6uHSQJZ3wYZoVqobrMd-9YNuChZDsLFX5Ou9N17UYg-Z8LzxSBIoxMc9DcBmbtj7oWmV-iHTHfdD_avR5XbE/s0/dbprj-new-8.png)

並且在 Server Explorer 中看到建立好的資料庫物件。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJj8789PHkoBdbEaZZTe5qQ6o4XsniCtZNuwl_wCnt9I-tFGeqPYgEcNnIKH_xhkziBZJtBFjITTP9srj0nNJoa89KWhc6zoWPXaaTO0wZHxvbjvyBc3HKuUoiSbOCqUcZ9YiXhxY4FD4/s0/dbprj-new-10.png)

如果你看到如下圖的錯誤訊息「Deploy01234: 無法判斷目標資料庫結構描述提供者。部署無法繼續」：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJE0WBrTUTcKveXGW9_XzqraFGYvQ7rtGhr-TzM0wyeBsItpR5YPbyL_Uy3IZFmvsHRwZ84l0s8V5kCHq3H2sEMBo24wlqk9Lg3p4M60NnVU6ITCgOTCIyYmCFfnRhbI2YEn4fNOaQeDA/s0/dbprj-new-9.png)

這是因為「專案設定」中的「專案版本」，與你實際部署的 SQL 版本不符。  你可以在「屬性」視窗中的「專案設定」頁籤中重新指定專案版本。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTMTWWx6ur4wcBqKL_vWbcMPVF3eN5MCJ_gxqBVSfUnRH_WuCDCoGEUFWrC_SnIfbKXJxivoXAlITIOVCuWD_CD17wE5sUZs1RvZvp0uonw1SdOUY3SyvmLw4k_MNil3cMt0vwckCfJac/s0/dbprj-new-5.png)

## 建立 SQL Seerver 2012 版本的「資料庫專案」

在 VS2010 中，原本只提供 SQL Server 2005 及 2008 版本的資料庫專案範本，直到後來 SQL Server Data Tools (SSDT)，加入了「SQL Server 資料庫專案」，  這個專案範本就可以直接支援不同版本的資料庫，不過你仍然必須在該專案中指定資料庫的版本。  

### 1. 建立「SQL Seerver 資料庫專案」

要建立「SQL Server 資料庫專案」，可在 VS2010 的其他語言項目中找到：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0OPwoZZzVRE28_0MGh0qIJTWg4tsQbh6Wbuc1IW4-rn3i67MoZ0Z-EiyXxiJomwhfv9pu_MvfA-z2P0ppmaduqE5pCuNJTxUJcp1mXuxJl44OBmm3iVLUFmJqjGhL7tDaH_iuzpoOGwU/s0/dbprj-new-11.png)

如果須要，你必須在「屬性」視窗中的「專案設定」頁籤中重新指定專案版本。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBaUTDyNwqTgN-1IXKUhEgnE3FGSzV9pNy3WQ5woBdNw0fOXnUuANGqXTqTWpMJ_X8lYr5UpLWmuMlakxgjqGvU5lFcP7Jz3ZfN1B_8O1E2OJ8quz-OyQTpzGMMWNKaK6GtHGBPyyLvWw/s0/dbprj-new-12.png)

操作「SQL Server 資料庫專案」時，它會同時開啟「SQL Server 物件總管」。  這個東西就類似 SSMS 中的物件總管，使用樹狀的架構來描述資料庫。  如果你在專案中加入或修改了任何檔案，它都會反應到這個「SQL Server 物件總管」中的相對應物件。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhhUZT8-ZnPX8vHgVar5waQfCXogIAprMbbCb8AJlixQPxXn0lFFRFyZsoKW0jv57x8FNIGcB3utaS_c22sQb1KBShA2h66mvNAnFYZcIJc9kVRShu_tvBx0_BbM1T-0SsMmx3yEJR0LEs/s0/dbprj-new-13.png)

同樣的，如果你在「SQL Server 物件總管」視窗中所進行的任何設定，它也會變更資料庫專案中相對應的腳本檔。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRacIOxVEMwbyX_BomJaLVxMllPrBvQ1jSIxAA3Zzv8VmfjVCwuILkTitRzT1LSBRDndqMTWCgtGY4L7tt9m8yp2rB_nWaPblghbhiYTbqH5YQRbyLvt83lBYwYy5stHebI6BiGxLqH34/s0/dbprj-new-14.png)

### 2. 發行

「發行」是「SQL Seerver 資料庫專案」，用來將資料部署到 SQL Server 上的功能。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgg5F5P6D2wSmWYj5VZdvkaqFyRkd3EijFo7BDJ1DRotwSR1QkTOoJW_NAtsguQXY2u4KeZhaPaGfdvhdaN8i6lSu2LuwHMm7Oor3tuhfHllzbL0di-qQyQZsxcgPm0wKTAo4YyQKiw8YE/s0/dbprj-new-15.png)

在執行「發行」資料庫時，系統會要求你先設定好目標資料庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjj9OnTrBkEWby7opw6J2TEH8oR0BSaVozIrXgJJzb1huH7-QiOUX8dMcZJ03cYlqhk1hHr0UrlFF9pgLnr2aVVBne98X2mwdDXMOQxedOw4K6Ugw3muqcufC2decnpMzzC-xCUazw0UFA/s0/dbprj-new-16.png)

發行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhshj0BVIO-pu47hcGFfKwI22AT8QnyThDVIcp6aEkHHeah7NzQVSYr3fWIILM5UnLKSn1_9WTs7av6GvXXTdgmrMaEKMTPvnwTGhf_b8_veP_q5UEU3PtKLP-poWSYT-AQjQnAi2_fsgg/s0/dbprj-new-17.png)

## 「SQL Server 2008 資料庫專案」轉換成「SQL Server資料庫專案」

若你要將「SQL Server 2008 資料庫專案」部署到 SQL 2012 ，則必須先將舊型的專案轉換成「SQL Server 資料庫專案」，轉換方法也很簡單，只要在原本專案的右鍵選單中執行底下功能即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOmLu7sOXmoXVTa_1mIuuRd7zs-z-jYueLQm7k_kXeWoQdFCiwSk2QASEST-rwSbzh_9qilEJZTuAgE7mvwTg4OF4utc297nCVhzbuwfjQ0mdiD4NfgNeBhoCJEbnYTNIHPoJlFiie5Rg/s0/dbprj-wizard-18.png)

## 資料庫結構比較

如果你想比較「資料庫專案」與「實際資料庫」的結構差異，就可以使用「結構描述比較」功能。（這功能也可以比較專案vs專案，或資料庫vs資料庫）。  它會將差異顯示在方格中方便檢閱，讓你決定是否直接更新，或者產生相對應的更新指令碼。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9dgH1T6_-sqxOCJMzulR95gfTwmuOsTkYllD42g5I9rE2IWa9vNnkP4467sK59A8jKmVbzHtdRlptoZLGE32WQSkk_Zy3Fkh9xc5Sw0wv-GAxtSOD86BHQhU_CeIkQOqGKFcQooUBzOg/s0/dbprj-addsehema-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCvmz2UrCIkOEjGR5Wi_4U0gLLyrtLGAsKjMnePA6s1fnUAcraBuFqx-D8fUoIbO0K1hSzYApQ96b-nMv43guxfo_qCtweBIX0T6xdpeLZOzWpMmUSPLNm10c8-GWEOVs9w6cS9a_NGk4/s850/dbprj-addsehema-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiES-tuojp-OKUlFWSPq875kI43TpyW_Y-oFTBKK688Zwh-4vtv8EhtIyBjWmLZCxu9Suk0MOSZmpoPmDyeH_qohRv2wNtGqPFT9WnpDc4FYN3Nv3zWpRWkeYd3IAjsDLsRzkht6RMmmFs/s0/dbprj-addsehema-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUyQ-fOfjMyXcHWWz0iFYcO9WJDz2HMQZc8rhBRceXN7I89A57dWyuLtFAX-loPV74D3u-FEyS_OlBO9WXYySBL74d4OEAvzqQeO6zZMF4OUPZQBbdt7U_4NIuQqYQlpdl-h9YmHnQ7UA/s0/dbprj-addsehema-4.png)

# 依據現有資料庫建立資料庫專案

如果你要替現有資料庫建立資料庫專案，你可以透過精靈工具來簡化建立的步驟。

## 使用「SQL Server 2008 精靈」建立「SQL Server 2008 資料庫專案」

你也可以使用「SQL Server 2008 精靈」，將現有的 SQL Server 2008 資料庫轉換成資料庫專案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyV_1RClNveg0E4EP7JudO0claaQnMrfFmOic0Hh0vA8L_fcb5-sPYbNy2WGfkFXUWeTQLTwyI2mj4AXCiThmYV8Oi3PpfagYYz4rlbspyJx28Bf4yh5vpjR5tFj875C2kZqKVpzi6uM8/s850/dbprj-wizard-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZPNk1s-rhisro3iZuSWy6NkwlhycHIQh-oH429F3wf6cJDhjaEKQmsLaRmXf22zCQs7PrAPnt7WgxhKMYTLpw4bu1WeMc-b23xdVyb4cTWzAJZANVjWUJvjKZhHqybCvXXmg7FWYSas0/s850/dbprj-wizard-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtMgwsvzJLUEq_HhtZa6KGDibd4QWgbbnQ2aPEkrFRHzE9xdt8zEVcuruWyZSquWEqlYvXgp3g4lR2L4deYUW-5PapQexuThzItQjDNJae3HOOkoNWMnLDeaFk2FlzbeJXSBPtPM4e5c8/s850/dbprj-wizard-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRdsE_mzdSBjaJ6zqTTItW49I5lDI355z6E4EqWlvebnP3rNqA6HJ6ziOJsyhHtpRIPpV1QRu4OEV_y1KggopfWnuf6GfO81_CgEx6Uqm6uoNiLVzbEsJoQuVlMe-bQAkfCC3Pgn8sCJI/s850/dbprj-wizard-5.png)

## 使用 SSDT 將資料庫匯到「SQL Server 資料庫專案」

如果你要建立的資料庫來源是 SQL 2012 ，那麼就必須使用 SSDT 所提供的功能。  以下範例說明，如何將現有的 SQL 2012 資料庫轉成 SSTD 的「SQL Server 資料庫專案」，以及重構功能。  

### 1. 開啟「SQL Server 物件總管」

如果安裝過 SSDT 之後，你就可以在 VS2010 中找到 SQL Server 物件總管。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4sW9jJsSONGBF2xO4zODWyHbsH3spMev8-6uaUehlBJjeIeUQKfJGploHsJCDsddctNaXgd6bOCFW8DYexqgRbwskRSgVkNc_w08OLDXRuW8tHSlmW1uwPTSihrioUMs8cO4zVhigogQ/s0/sql-datatool-01.png)

### 2. 加入 SQL 連線

由伺服器總管加入 SQL 連線

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_BCnpHXSB65Hv_NUaFZbIuxbGtGxAQR7jAo9BG7IAY1RGC8a1T3Itu4NZSAHh0cJn3gExxe4-8FfPm5LY96qkEGQoHj7TBVKoWaLzasXSvvRQqiCWf5NXAJg6BsIii9PSESyiEwPLu_Y/s0/sql-datatool-02.png)

### 3. 建立新專案

在資料庫上按右鍵選擇【建立新專案】來產生整個資料庫的資料庫專案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhk4Nm6mtUIt8X_GZUAqloMifK3P23A6y24HCekdBC33I0Vewhng4hSu4xdT_x7uHJ2KVujlj4IhH8ABJsGse5ytRLYJZxPi3t4kmfyYTT5LcjA3avgnUVQomQS7J502qd5sfb20ugmhH8/s0/sql-datatool-03.png)

### 4. 匯入

當你輸入目標專案名稱後，接著按啟動，就會關始將來源資料庫匯入到目標資料庫專案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoaHWbci9m6J8NYptBakJjg-2eHsTKYRfjVxvh8Xtd9Dgqc6eYldsd37VoNNPvbK6TSkyDZiNY8KFAvQbqwhNVzx_Qcbu87jlVgNZg0K86MVXL9c3gUm4OXjzmd7pwnL8prps9nne5bbo/s0/sql-datatool-04.png)

匯入完成畫面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaVujWjYwIugb1y4qFApUnYfALyziPE0IYttkEQWf5k7Swpa8uVgE-93q9Ajk3EtsPWPC0JZ2XzhgWftWmiMVOcfHNmw2tZB3sXfdiLuBdgHCGOt4lE7pBRwXDbeFUGoqlJ1ViRE-AZ4M/s0/sql-datatool-05.png)

SSDT 會將剛剛選定的物件匯出成相對應的 TSQL

同時 SSDT 也會在 LocalDB 中，建立一個與專案名稱相同的執行個體與資料庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2gMzYR0JR7Oxi5LZia7AvfJM56BGSlK1K27Lk0JHH7jot8vu5QhhI7XMzr9n2uPN7LsF4NUb8YiuIQCfAiY9pD2tk5mXnCShhahP45vLchdpcEx7IkqDLT3vacKynJAOlIUy9ACdWbNY/s0/sql-datatool-06.png)

### 5. 編輯資料庫

當您利用 SSDT 的「SQL Server 物件總管」來開啟資料表進行修改，系統會自動修改相應於資料表的 .sql 檔。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfgdpyc-RBWWkQNKmmpF-yLMZC_xP5LWlQhs1sgfjJXbrcBacJsas7W1AmlbTJeZQgX7Fril4nC-Cb1-n6_aA66b5XT9M-hDnpzvfyCojG5dV5TLtXeQPcVVbUFbbIM5cH94P5Dfr9j6w/s850/sql-datatool-07.png)

如果你修改了某個欄位名稱，也可以利用專案的「建置」功能，查出所有與該欄位相關的物件。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAtNEfURMOG_9xp_7Nej2HVXeZuKdMlcufzNFXutsJoKmF_KW3tW6hZkHYw2Z0yabkcXXeaaRzs-J-Tzy6-ssJ4-o-s38BuMpkR1dcTMrlvqSfCyMU1wsH7YnaRULaMQQXTcy7y4aBTCE/s781/sql-datatool-22.png)

### 6. 查詢資料庫

若您想要利用 SQL Server Data Tool 查詢 SQL Server 中的資料，可利用新增查詢的功能來開啟查詢視窗。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSIxxS-vucUCFEE5IaQTakuiMduwTarI70lMYOuG52t5HfsztrQVM1hHpvz4lyaGWV97XT2-LqwUzdSBKzTErz9BP8Eos3zdGMutdmy0Irb3Utrddau5g2_3fmBY8A11acX_EGdcafaNk/s0/sql-datatool-08.png)

使用 Ctrl + Shift + E 執行查詢

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1_jEqmOQlnNmuAknkQ5qe677q_BCjPrBzK1Z5obuRSBlpc1FT4mO4Ivfyx3itvyCznIAo3l23AkHEIgDxlgCxSF2KLJAq1hSjCZ3Wt1B1oWsWd6UthyphenhyphenBsbXnMlZibGOCmLU5BRtxFfm8/s0/sql-datatool-09.png)

### 7. 檢視資料

檢視資料

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPGtviON7X-10ydfHAkIb3ueVHbaj9asL9xwBEa845oKXU5c2b3nVAqoySXFQaYIuNQP0_h2FrZkB0XA5o8U_atdXREIhdvsRiz8DAnyVRpOtn-4X51sjSanP3R6MhR96LcoAHlT305Wc/s0/sql-datatool-10.png)

由資料產生 INSERT 指令碼

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0kkbU2E_geLe0bl1vLtEr19f__PJId1LobGG5weJtaADfHWpHPW7sYczVE5Gj9N_XjT0bRJ8xeMZK0Ov8Jj-h7BW_eFA1YFhpzxqRXfuKvvof2GOjM53LY9-eb_KAeOK1RcyjypxbgGA/s0/sql-datatool-11.png)

## 使用「重構」功能

如果我們要變更資料欄位名稱，可以直接使用資料庫專案的「重構功能」，它會自動幫我們找出資料庫中使用到這個欄位的所有物件，同時進行變更。  

重新命名

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKwllxBfKpeS1S27CjeESuq2elBemLY4TxO6hoD5PbAYFAQ2l0pKFtLaTNhA6ot-G9epj6_6AQ4Dwtt3I-_xXdH3Cp6k2p5OaP2D24j1CtlSE8oFLfNsqIBqqdDqyQyXPO_ZIyK_75G0M/s0/sql-datatool-12.png)

預覽變更

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIhleZo-Q445cegmgvtwcUVvd2-oW_hd1H7LlX2U92w365VVR3oCebSbP9ui9h8bVfR4ECCRqRP7OsHEql31yz8IPW4gz8pEHbJyBAaPvpdC1sAs_2pDDCziAnLC0ivqN8isge3fPNfOg/s0/sql-datatool-13.png)

## 使用「資料表設計工具」

要變更資料表的結構，除了使用「重構」功能外，你也可以使用「資料表設計工具」來操作。  不過，使用「資料表設計工具」所做的變更（例如欄位的更名或刪除），無法自動進行重構。  若該欄位有在其他物件中使用，如 VIEW 或 SP 等，必須先執行「結構描述比較」，找出差異的地方之後，才能進行更新。  

### 使用「資料表設計工具」變更資料表的結構

使用「資料表設計工具」進行資料表的結構變更。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZrVDDBhZ0QuwaQh0r_Ok6ukqUK_wxPyog_ieIviJV-K4ynKFIdUqsFb86zTQFggoWZ2ZyaUInoGCwpSqTAeHJ7Ty-OOILc-zp1HUFaUqi2QU0PxAqXgX4XQ4zvQKXMdgAWQv_992QhI0/s0/sql-datatool-14.png)

加入欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4Ru3TQ9PA_4yaBzYZFhfns52m6vKxc_jKYHutU9XCpJGGghXBovdAZvVoXyEgoTrkIz9ejgT8VFJJtXeGvf3DPORhRgH_EOmtH4QCw6cXFYoDsoErF7rvq1lAFUp_rx0p1hJN_x0vrvE/s0/sql-datatool-15.png)

### 更新資料庫

要將以上進行的異動，同步到實體資料庫，必須先進行「結構描述比較」，找出差異的地方之後，才能進行更新。  

使用「結構描述比較」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiiJDCbhZlm_13Qn-qaYc840NBYPFSqFhK61W_5kQqDWkBtT4jEeExbXcdLxu27VRWPVjVMr_n4ReCJde4UQ03EKJ-P04Vlay8uaThB9TDUcBug5ZZX2QNVA5mwxIfMOLrUbMIptZEmy6I/s0/sql-datatool-16.png)

選取目標：設定比對的實體對象，並進行「比較」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgswqOunQwLVt6LgSm_JRaQm4bSriVw3IHkRKCnWmpQHvfSHvtc5I8bY5WyLkTU_r9vaTV5hMYVp857Ra3lxkH0g2Bh6rvppMzFKaJlGrEOVHW4MYTjkUdFw53sdZlSlYG9hLMTz4VOwY/s0/sql-datatool-17.png)

確認差異內容，若無誤，再進行「更新」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgN3nYRxOkkr2UArTzrr4fMwqraTCqn89hNTE1jD-WLjZDoPbtWL1gqOcVKGVU-YEpPujoJlBL_VRklHWBYappDVs_1bDLNjluxZuRGZ3ujGwi7QaTbIvueb4FIxgN9nhIoAnjs-JUoEj8/s0/sql-datatool-18.png)

更新完成

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZ5oOoq7Hqpl-1U4ODgMn9WFoTEzOqHVwkRnRMywCrf9hxFMPoo_tvpB6hHgD7gvpBGr6MWnk7EzEcuh-y88hkSQIVfem3sYJGFQPRsjyrnNGuF0PbDYO6O_SMOTIZGZpd9nH_Sb1fcxk/s0/sql-datatool-19.png)

確認資料庫中的結構是否有更新：

欄位已更新

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirg5rn8JcWTM1INQmT-XY7eWLKdaX0AUcwSFfZgq42KhrBJr3iez2JYEQIx81MkPMypCumKZvTiPDph0QZrPTOMbsmEN2KFZJ2_YmDhY5OAiNH3WKK7HdOrsIKiIhCq2536RIYweF0aVs/s0/sql-datatool-20.png)

參考到的檢視表也有更新

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmv_2Eg3pT3EpVaGi1j03MkH7Iv59_7EJ_EQvw0l-FBn0YeQxSzgV6c7sBt3wf_XJEb9cYNKCqpQYhI5I3ftOfco0RT-CdDXFhs8ueb8F2uXuW9JehjpolbQ8bU0aAL5IFSK2C9WaDUr0/s0/sql-datatool-21.png)
## 參考資料  

- [好用的 SQL Server Data Tools](http://www.dotblogs.com.tw/terrychuang/archive/2012/03/08/70594.aspx)
- [［流星隨筆］Database Project 系列 ～（一）建立資料庫專案](http://www.dotblogs.com.tw/alonstar/archive/2010/06/08/15736.aspx)
- [Creating a Database Project with Visual Studio](http://www.codeproject.com/Articles/245612/Creating-a-Database-Project-with-Visual-Studio)
- 
-