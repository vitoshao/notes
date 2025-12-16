---
title: Lab3：控制流程設計
layout: default
parent: Labs
nav_order: 3
description: "Lab3：控制流程設計"
date: 2013-12-13
tags: [SQL,Labs]
postid: "2925165307549506093"
---
若要使用 SSIS 來設計 ETL 執行工作，  首要目標就是決定使用何種 Task 來處理需求，  然後選擇適當的 Container 來優化效能。  

底下例子練習，我們將練習如何由指定目錄中讀取 .csv 檔案，  並進行匯入到資料庫的操作。  如果執行成功，將檔案搬移到 data\_success 目錄，  如果執行失敗，將檔案搬移到 data\_failed 目錄。  

# 設定 Connection

首先我們來設定「連接」，在這個練習中會用到幾個連接：讀取csv檔案的連接、將資料存入SQL的連接、還有將檔案搬移的連接。  

## 建立 Flat File Connection

因為我們要讀取 .csv 檔，所以使用 Flat File Connection 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgB-xIsL0HY7_tW-Iw2bnrugu1BOKLxGjSs3bHRR-LRZMf-CP42PwqIe8DGwfW9RudVP6ih2IwIVfxRI3hkfhWcUVKmym4sKt5HOMqDK-VcFK8LpUhZBZA3Peqo2rheuOyROODlQmvh3ik/s0/ssis-lab3-00.png)

編輯連接的內容：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoIZWrrZ6gu9xMnyT-2J8UFaz_YLYCPIkgRRIRmCsPsCO5ak4VFS66EykXdBaGa7-F-j5BD9sQdOS1sMyt_UWQww8BJF9MkMxaG2dMYfGyE-nG38ukqKYtXV3zDPWK0oTU8D-SbGqn_kU/s0/ssis-lab3-01.png)

切換到資料行頁簽，設定資料列：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhA4sHETcmJNGkipwkkNUSEEn8CqQbZJaUvoTIXUoOrYn5xxoiwj5g-sfRSLPQ-GpuLAzA4PihfEb-LvFFjVy9dQz7Jowtdy6OsH53LqdG59iL3UFceZX9w9etnf15mbzxuc6zS-Ib7Fno/s0/ssis-lab3-02.png)

## 建立 OLE DB Connection

因為我們要將資料轉移至資料庫，所以要建立一個 OLE DB Connection 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqKTeBaQiISa1cTNJAHYf59LwKfvvknTTilqm7hCzUeDalL8C1qG9cqs3q6SIbfuklUZqsSaroXTiy3NejjGcfJmZaxGbBI7cQ64HPGVMmnFjW4-mfFs87Fs_XwipDELIW4I7TpupDHe8/s0/ssis-lab3-03.png)

## 建立 File Connection

因為如果執行成功，要將檔案搬移到 data\_success 目錄；執行失敗，要將檔案搬移到 data\_failed 目錄。  所以，我們需要建立二個「檔案連接」，連接到指定目錄。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8c_qY-HG7R3jtRlvnE_-A59exO1ITktKCu26BVW-TlM4F3aBurQjYqPgE9Ca7HhjQu0yD7RwYpBZ_iH6Pa51pgx0bbR_xJANd3d7bl7wUaPTtevixqnt5m06wFeHuw7HriTTRCXtRqAk/s0/ssis-lab3-11.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipC1GaMpfRZQ1zyVeCYitFzAugQh9SPp-HXlc2KOu0SPaXVfznSbdO9jXBSyjuEOr446fy0c-hDNTh2pTAtmTrSVj5nnTAX62v3RhvjdDZzB99wLW17_v4Du9PxHv2hbHO2y6tHGRMZEw/s0/ssis-lab3-15.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXZc5PBCqB1JWQmOyzVMHY50ezcnHXkFXjigT51Xbn1DS909W4Gsej-DhnoLcwntjMtwNTAf-5vDV3EkHD6q6srqs6g4POrsnXBiYLy2oTUVXEf-_EvF2S5lm2U0jVEleY2pZlZKaHt_M/s0/ssis-lab3-16.png)

完成後，你可以看到我們總共建立了四個連接管理員：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6uUncRKwBlp7wHUtF80-aP-6hCsY4ajsg-iYaurkGxKddYkLapon0QiZkc9l9FMZewyFfvMVadoCfnqKOM3CCYL7RwJy7OOE56sNsJZeJS3P0yUdfNrrjZeXr490SHb030Tai1_Q7H4E/s0/ssis-lab3-14.png)

# 設定 Control Flow

## 加入 Foreach 迴圈容器

由於要處理指定目錄中的所有檔案，所以我們必須在 Control Flow 中先加入一個「Foreach 迴圈容器」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSUfEbkhN342bVPdS3YcI8iGJHbQHGNfm33LIiRaGwBHmtevzMcJ1mSdxO3mGKQvypPMQV3Dja0kq7owBzl8SQ_Z7H2Js8kFFjb55hT_nsFVQZ1x47jDVmX6W1te-x4Fwnw2WDMWh6Wcc/s0/ssis-lab3-04.png)

並設定其內容如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTjai7BOQW7UgZcQ724pTHGh9gUNKzwY7y9A8gPGq8XTi1O3iQ6LCUEA7m7WMoV_50suEA22k3C_mbEgDwY1Cq22VSuO8qfaqEi7kap-EApqh3OP0uPiXhpk1Y-du0YqQXj1nI9jSrEkM/s0/ssis-lab3-05.png)

### 設定「變數對應」（Viriable Mapping）

這個迴圈容器會列舉目錄中的所有檔案，所以我們必須設定一個變數，讓它可以將檔案名稱傳送給要處理的Task。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWyye5XnlLt5blDlvkI9KDYkmqamgw90Iry1iP44HvBJ2nzFXHL2o9mOMCKSh4zP8IMCkEhAtDVMfrGoyowUdLzchY0zveSLO-SXdZ0s13BWPIw52jhbw5xe3bPwsbqbAUaW_Gc_j1SUE/s0/ssis-lab3-06.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh86ht_DXWLMNjsxyZT_bAbwK8NhrQw_jVPceRF3ICN6TN69rAWV_Q4kpLGipI-91GpBdVFU8BTj9j0jc3i4FH4PYaBt4yLoDZXGZDJa81IEk2c9hpbUcBROTrzVFDQrEROIwM7x9mmsWc/s0/ssis-lab3-07.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPMgQbgISfXctkKqa6qnU9Ev3rtGITlaBgM7Oz8muxdVfLUdxXpE3Kqi3dgY40-Sl_5qrsWjW5PUtq1Z9aID1UztVbgBOXDpnj3HnRm4CTf586hjVF3XLAH4dhyphenhyphengwMI44moI4PtyK_myk/s0/ssis-lab3-08.png)

## 將連線管理員改為動態取得檔案

在先前，我們建立一個「Flat File Connection」，它原本是讀取固定的檔案，現在我們要將它與「 Foreach 迴圈容器」建立關連。  也就是這個連線所連接的檔案，實際上會由迴圈容器動態提供。  

設定 Connection 的 **Expressions** 屬性：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzv3SYjvMzRzlhD_9S7VhcAqPpg_bHvNgSVzVfAthx5GqnYmgnQAWD-hrxM-tNMq64inTDPcKV_HWKE-ixwa8OeHGXIzv_y9vMK3-N_MZqjZdbmGjP_mz58yX8QdQ_9ZMd_anUoQ6IV0I/s0/ssis-lab3-09.png)

設定 ConnectionString 屬性，輸入：「@[User::inputFileName]」

這個變數名稱就是在「 Foreach 迴圈容器」中建立的變數名稱。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0wD3rDSWHg_m015aRRZ4o6sjjvrgWm41ppV0mxnWn2wtlVyj2iYAxJ1Wgr7_Zu9azxj4JOzHbN1ErnV1ZwmUbtZw-PtAAahK5C8Q26DNiE9-b6yIRKL5WNPbfuFTXEsFKk5bI4TiTLzs/s0/ssis-lab3-10.png)

# 設定 Data Flow

上面步驟完成後，「Foreach 迴圈容器」會列舉所有檔案，「Flat File Connection」會取得每一個檔案名稱，結束。  嗯，好像正事還沒辦...。  接下來就是 Data Flow 的事情，也就是設定資料要如何處理。  Data Flow 可以做的事情相當多，所以擺放在下一個練習，在這個練習中，我們只簡單的執行匯入動作。  

## 加入 Data Flow Task

加入一個「Data Flow Task」到「Foreach 迴圈容器」裡.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEYFs1xYLLrxX1XAEBa1Z8sVwyqNrc9V1N4wo2SHIbQNYGlw8nqr2nMfyUNZ16w6IbP7rVdJXoLQNeyM5w1hlsjVDCKUHQMbGIYwYZU7dmx39xHpQ3F8gqS22U-JoqhdzskEsZ6SbphwQ/s0/ssis-lab3-17.png)

## 設定 Data Flow Task 執行內容

1.切換到「資料流程」頁簽，由於我們要讀取csv檔，然後匯入SQL Server，所以在這個工作中，我們要加入一個「一般檔案來源」和「OLE DB 目的地」元件：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdx9H0lrIIpRA-HOKXOB2S3NA-XMOH2g6HjkuYgo0zHONmKTteAUBrUJBhLl77Jt66Ge7yQMuweihWy7oOV1T6SKHxjlv2SP6Ps7ms9-NGM0NWCKeq0zlJCiWbnHxERC3Xkj2UkLWbBpU/s0/ssis-lab3-17-1.png)

2.在二個元件中間建立「資料流路徑」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoDBW6YCUiPEE_cn1kKpO7GioxkqOm8iiamFe40vMkmBzm18tlMBNYtvyPz1IhU_rVTLx41kaRnUygxMIr_BZ05O_Qysr9aqUA8uyYg-3qqf_zZWS42Vtu-0rmN17yhewzP7qQ5u69LFk/s0/ssis-lab3-17-2.png)

3.編輯「一般檔案來源」，確認其連接管理員的設定值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhAvI1xAWuVBOskgLVWLPN3dc7QITEEiWmUbE6qSFDaWUliJnww7dp-nyfwCb8PcquvzJoyobVx1Ejxy1V9sxwSEwds0-jv_ce9DKEIUHPM9Q_OLlhtRvmmw74f9EbzFhPfgjRzzEJWjYc/s0/ssis-lab3-18.png)

4.設定「OLE DB 目的地」，確認其連接管理員的設定值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPzTEYfgRcLH-9di_xZDb_JV5AaNODP4E-SVeVhKnmErknGrMvS2fllSm_d6S2rGUwwBpsANTjYRvPujCj-qwffkUKLUKli_T83g5P6Rgo6s0SfQC_7FFKXn3nRaYHYAylStQMhaqMhss/s0/ssis-lab3-19.png)

5.設定完成後，你可以開啟編輯「OLE DB 目的地」，查看csv和table欄位的對應。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiuGPwGBYuO9yHalLa1YCtVeGpMYfbSLMCaScEXfJjWHsqfMKhPRSJsRmcPU-sWHXsm9n8KaNEDZkNMeML1k4rghT2X1rypokMAGwSRi6myukAwhIwU6mFFSVgWHeFptVmAUrMzfZxFTeA/s0/ssis-lab3-20.png)

# 加入 File System Task

完成以上步驟，我們可以得到：

1. 由「Foreach 迴圈容器」取得所有檔案。
2. 在「資料流程工作」將檔案轉入目的資料庫。

接下來要做的就是將處理後的檔案移到指定目的。

## 加入 File System Task

我們加入二個「File System Task」，若「資料流程工作」執行成功則執行「檔案系統工作 1」，若「資料流程工作」執行失敗則執行「檔案系統工作 2」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpUHBRNKLinki8JnP_sJBPXnKEC51dSKGXWWrPqCwprFjc70ZSBW-ckr7_lSVwdTVpDVE5GZ-TY4MmySvgyLLly26S67iJ6nzfXouT__DgsNdKZaekmcArZWhwXbvI4eoJkYpOXmzWRb0/s0/ssis-lab3-21.png)

編輯「檔案系統工作 1」。

- 來源
- IsSourcePathVariable：設定來源是否由變數取得。
- SourceVariable：由哪一個變數，選擇上面我們自訂的那個變數。
- 目的
- IsDestinationPathVariable：設定目的地是否由變數取得。
- DestinationConnection：指定使用固定的連線 data\_success。
- OverwriteDestination：是否覆蓋目的地的檔案。
-
- 作業
- Operation：選擇「移動檔案」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh28bVagD1ScR9DDkJ5zAhVH9Jixe4hQ82qA5vZpH71ZL0cf3N5ZHH3_oBd1TUcLSfCl5A4qMBL0GNcZIGiVqnRBR2PHc0mFAYcICNwGmgdo4n2ZVq9W1U2-vfh8VxEIWS-iYmCk-_zV4s/s0/ssis-lab3-12.png)

編輯「檔案系統工作 2」。

依上一步驟設定即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTvHsx0tiL2SeySLizow9crcZyIqZ8F7RAutm7ZycMyXiz7r8M0hQmFuW4x_voNLC8HOefrol6al0oIGQ8taY9JX8w1NTK7nLFmmZEc4IzXEr8OuAGsT81dvUugw51KE0UpIrIPYeJfDk/s0/ssis-lab3-13.png)

# 執行封裝

## Prepare and Verify SSIS Package Execution

Now that you have configured the File System task in Exercise 2 to have the ConnectionString property assigned dynamically,   it should display an error; it cannot validate the source file connection because the inputFileName variable has not been assigned.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhpc5-jretUw3KbKZ5rlDb-WyrVI-33j32CYj5WB4ZJmN4S2QN2ub4SttBIXN33AyijMoPPfIRNU8qK0QXUlk8Tfu7aYcApK6GSeBMje5L-AmxlfS0mXbQ2YYsP0eO32dl_qhb1dkNZS1Q/s0/ssis-lab3-22.png)

Right-click the File System task and select Properties from the shortcut menu.   Find the **DelayValidation** property in the property grid and change its value to **True**.  This will disable design-time validation, and the variable will only be validated at run time.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEXiL-MeO_gfthpxutLtOJuwdg5aYjqBGPuQHIoOZb9na3cBRQ4GZKbnkMtP-z7eryg5djB3Ziqgk-UlAadIlSvUhC36-PQIJ4Wu0WwpQIAesCVLz7igi2wsAHvbNVR1e8R20yAZLUEDk/s0/ssis-lab3-23.png)

## Run the SSIS package in debug mode

執行結束之後，正確的csv檔將被匯入資料，同時搬移至success目錄；不正確的csv檔將不被匯入，同時搬移至failed目錄。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghtjH3DvBcU0jBIn8PAZOnCIgymbpDYXB3Wfrk2QWJlEUh7Eh-tjE18-t_oTKoOeFPbYeZ8oFQLrFfufvIvYbh2-tnNa3_stjpbbZuxW9aaxUUbnnhfXYp-UlV5tz83tGtVifQUiD83VQ/s0/ssis-lab3-24.png)