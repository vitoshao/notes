---
title: Lab9：建立 DQS 專案
layout: default
parent: Labs
nav_order: 10
description: "Lab9：建立 DQS 專案"
date: 2014-02-11
tags: [SQL,Labs]
postid: "8379271094858564946"
---
# 建立DQS專案與監看DQS活動

**資料品質專案**是一種用來改善來源資料品質的專案，  它會利用**知識庫**（KB）中的資訊，執行**資料比對**和**資料清理**作業，然後將執行結果匯出到 SQL Server 資料庫或 .csv 檔案。   底下Lab將練習如何建立一個資料品質專案，並且在 Data Quality Client 中監控DQS活動。  

## DQS 預設的知識庫（KB）

要建立DQS專案，必定會使用到**知識庫**，在還沒建立知識庫之前，我們先使用 DQS Data 知識庫來練習，這是一個在安裝完 Data Quality Services 就預設的知識庫。  

預設知識庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEijjAQA7W9Kp1iuzSq7oDCl1KE6qYT2AGV7Q410D_kycCQ7zytXmSZVM2kxB-cZdYVwC1Z1sdb_BJ7hNCUQs1gNgvb4v-vPPl0kIfCXA3k1Wdc94dF2QoK1gunYM-1HdRViEpvzKp0aCQc/s0/ssis-lab-dqs-02.png)

這個知識庫，總共定義了８個定義域（Domain），每個定義域都對特定欄位設定了各自的分析語法。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQXx_wH3JmaZR1a8aFKBc19BlimtZshEnNZptrbH8RKgVZvhods5DFP7ltI_f2vSyxj4Lyx1VLsiWrvMDK-mRGcd39Lp-TBnurU84SW2XHoVqLj_gYATw2qjgdmu8FCGL7RjWdLROKQWI/s850/ssis-lab-dqs-03.png)

## 建立 DQS 專案

對預設的知識庫有個基本概念後，我們就使用它來建立一個簡單DQS專案。

### 1. 執行 Data Quality Client

開啟 Data Quality Client 並連至含有 DQS 資料庫的 SQL Server 執行個體.

### 2. 建立DQS專案

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqBtiCQTpD-SF2ADyUZZ-W17Lu0VCS7GH9qv_8Of38lJCBR3PYaZnqxuO0ZFK-GVWzCRW47yM5kxu1-jogYHxY_jDPpRTLiBtrZPaSJqzMGH7oN2n5ET7TNSGSEAdQcYyzZG2cOo_H8Gs/s0/ssis-lab-dqs-01.png)

選用 DQS Data 知識庫和 US-LastName 定義域，並選擇［清理］活動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSa1QO2H1noxS8X4-nJ9QjXCy72XcaXLLYOgiF0pV5yd_9vRpWaNXVRxqccL3apmg671Inw_DaJKeHwXfq4p1DFzcdcNsuzvLWO2graqIP3hfhoEqNE_dNeyzxiga9lwo-x2QHw1tnoOU/s850/ssis-lab-dqs-04.png)

### 3. 設定DQS專案內容

在[對應]頁中，設定要清理的資料庫、資料表、資料行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8LKJ8V10EHnm-_ZwN5gu5mzlWgwHD2s0-mIQ9z8LwLbFtYjbY1D2ya4xj5HVAwu58slWCaUt5LH0JKTEmR2pwB-ChIknjjsVRYt2zfEK0waKCl0qkEOZybAFrcFGsDYy-4UhwccGqJeY/s850/ssis-lab-dqs-05.png)

### 4. 執行清理

在[清理]頁面中，執行[啟動]功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiuWLQwDHhNG9ZVwSh9YyMlkNPdTOcVTjdgiTh3MKtpJz0xYaNhObytaPEzTZnNkk3e0WWgTHt3nKV2nTnAqamYcbufqSR4AvIFQ6N4iCJxY7DXu71pIGcEoUZxlNZiFncGlJDenIErcP0/s850/ssis-lab-dqs-06.png)

執行結束後，結果會顯示於下方[分析工具]中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTDPUAMeRIHspwPFZkX13G-L8wE5Mr9Q2WBIWEr2XNK7pUSDIHoFlygS3OCXcXtezcigj6TZZ4MaJr7fhgKIbHNTLfLEhCxh9YyCMcOHKesbqLWvFiRHe8xSaNVsCDNBrybRlsEuNuCx0/s850/ssis-lab-dqs-07.png)

### 5. 檢視結果

在[管理和檢視結果]頁面，你可以檢查 [Suggested]、[New]、[Invalid]、[Corrected]、[Correct] 等頁面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgbnrzlQMPCf4sdsQ516-eJ6LOxSPC9Wj5WHTgLlqhIfBWqyYiF9Xa-IwS8rvjt2kDW7PtxoHGObCR7aT_atSDmpCh8oaFwT-lzOjyWTs9VeF64VQUTehQc-ySfECXB_d9YbeZdmI-F0ig/s850/ssis-lab-dqs-08.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbhPTCANvA-d3n11zTG20TpSqws-A5glrDi6VlH3dvhqDSxo2Dqg-kls4HjdXymPMVXXh23-FxgvJ-saqTy7Zf-eytFbq-oTofdmDH2dAKa512TLRMB1sxVIS21VcsRfJb5lw45pIO87g/s0/ssis-lab-dqs-09.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyjoKVBt4Gd3o2jVc0Sfaczc_OspnDuMoxxuIczgV-lI0-MHxDQtBsG04tI99H0M8oo6r1CzKLV8G-JQUrtgX4RLHMks7ieiqR83VLpwVB2mbTnKrL4jHIR3wa7PNbdKsDFdc1OnbvN8U/s0/ssis-lab-dqs-10.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1PZpGJ3thUuttdrcIWsPyRL93rvkHQuP7Z2OUxUyj0DZ37PfTXBR-WeuMEz2-9U4VwtS2WCdFDDxvcq3wrAvLQYwp4LHShX3xe2T8eE25GbHUxoh4xEWzpgcndw4RfjqX8VTBLFZpYXE/s0/ssis-lab-dqs-11.png)

### 6. 匯出結果

如果不要匯出，可直接按[完成]；若要匯出，則必須設定匯出選項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjb-Y7YH4HOMC0YjrtbTjsOk-IKuX1u-U3h9-EmvQifRuqv8owceYE3Y1nzN2NJ2BRx2P1HTLSs6IoGW23Da4UUNE_kH5h5J6CBTQSaypXk_vinMZCkPzLaDZXDkEIMs58cGwXHDYOVeJc/s850/ssis-lab-dqs-12.png)

下圖是以 SQL Server 方式的匯出結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguZQ8tGYpiJhV-Gtb3uFWU-REn4qHxTpJNuY8camwxgd6nK6nf4z4XFZ8TTwe8Znlwt-oFA-vVy100DA5iQupR4Ig9KXfwpmQEd-SPLcH15N1SavjjztCDJGSPfmE9ZoBvNTtZPpZxZus/s0/ssis-lab-dqs-13.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXd93UKXzI1VWProQvxYXdg6LoTHKnGcuJn0tkUnz-yRdzlcptNRDzytuHwsE2yCAb-MtMxL-FmEjG0dUwimWhqgmNYjOMZ6MDgufuepJqVOmRgIwJF2y0oQSyJznuf__cT6aWC3mDYLk/s850/ssis-lab-dqs-14.png)

## 監看DQS活動

執行活動監控。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8lUfs4BCngzAUNbvYsRrwoGuTSBEC46Gv9_c0dgrOHVvlXhKjInPqLAsDG3D2WuO28bP1lq95kwJOF1_wOuOjd2HeJk1a3qmMk8nxxBZJ4_2NsFxmLfQrY9McpdkXIUR69VCioAMcaT4/s0/ssis-lab-dqs-15.png)

點選專案名稱，下方視窗會顯示[活動步驟]的詳細內容。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEgHZCW0N-HwB8mGt1uSDv0t-5J22EcVBIdoDZTyUF_4WHj0PnqdwsRXOB8_g-g9rSsL11R31dcXNewy-_fkPasFhA8knVKpNHX7EEuD7YBvxiDS21fGS2GLdfUnzr2ozWeT51WBgipfI/s850/ssis-lab-dqs-16.png)

在[分析工具]頁面中，可以檢視分析統計資訊。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEgHZCW0N-HwB8mGt1uSDv0t-5J22EcVBIdoDZTyUF_4WHj0PnqdwsRXOB8_g-g9rSsL11R31dcXNewy-_fkPasFhA8knVKpNHX7EEuD7YBvxiDS21fGS2GLdfUnzr2ozWeT51WBgipfI/s850/ssis-lab-dqs-16.png)

# h1\_XXXXX