---
title: Lab1：Analysis Service 專案練習
layout: default
parent: Labs
nav_order: 1
description: "Lab1：Analysis Service 專案練習"
date: 2013-12-06
tags: [SQL,Labs]
postid: "5801952103412210357"
---
# SSAS 安裝與連線

## 安裝 SSAS

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqXInIJSDEEUiXRCugZujdWPj0yNXHBhAnaf4uKj1UF2TofQnhOhMq2qDwVsVtrKa3YBCOYB1kMlQ807bmrZERarKkiFMAt4Net2w5fzG4ovhiZ-4ljZbw8aBMFc7T0G8OUtJealAC1z4/s0/ssas-install-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiE1Fyk9zLlD0uiOKVd0Mz3ZFRj0dIHUQvsqgCMe1cCn4wTnEPCTkIuOPdg1OL5ybLk2BJ9rXBk2JwHDZRXaC2u_moWEMVLO6Otl9UPFVWnV1xlCRfDY4Cvi27sINR0vQtEZpXiCBdK4tI/s850/ssas-install-02.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1wmUb4LV8zDUC8xq8DeV8fd96GhUoz7dqlzC8JGYRs2LCcWYdacPLD7SfMy081u_zo_c_RnWWkugKu23vHvC4h9kZQz4Y4K_rjwEL1Dn08ae8MZ-TVnOlHFTY7Qbaj7y5vfZzdF8avak/s0/ssas-install-03.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAVRNxRm_xAOYgH64y9y_1ZdB2zn3Z5SGAgwYZERT3NmKmv5E3iC2eFzRtw7nIS4B8tkQuJiRdrmC9nGHvNteye-T-IzYTW36z-SFz4fjV5DL48K-CTGMw_QFG7F0xmE9qxfAWghsLaNE/s0/ssas-install-04.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxphBmxqNLKn1OWkp0tiELzOnN5IVNr_VwcmBhscCahfuqlDz5KLCbGpZMKaA1dog8CJfpi-5YJYEn3pGRJJugdbQ-93SVC7P-Odt0KOYCMfaxcJyncrWp8EbJ0z44f3KY9Soor1UjvT4/s0/ssas-install-05.png)

## 連線至 SSAS

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYHUeg-80Pjkaq7_yLGFj-8rDKqwjCv24537tFLbY9jyKdtl4b-SbknXJO-u-ASD0bdNWcmkCT3nuRm3BTQ9xMZ8jBy9mknduhTgby-nLhmssqqslemLcnA8ffhitpifKmMXYfPoy_8Eo/s0/ssas-connect-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj07uAcO0_0FT5XdM9aeDJGXMnPvPa9fmrLzUaPPQvfClRT6lkvG25woCFNJFDJI8Kl8h4pXr30ZORYdxHSbWoAH3heD8v4UjY9qhJve2jyoLqsCjxKFBr0EkGEVXNpH2lhm8yGmhdrKrU/s0/ssas-connect-02.png)

# 新增 Analysis Service 專案

下面範例使用 AdventureWorksDW2012 這個測試資料庫

在 VS2010 和 VS2008 中，你可以分別使用以下工具建立多維度分析專案（Multidimensional Analysis Solutions）

## 使用 SSDT（SQL Server Data Tools）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizrkb8Luz1CTU5DKGbpCYOAsk4ffIUzxACcClUHKFrcbtbxuxZbw-md6tjgNUuYpNZ7YoAx0hbn-EXrYF0GS7IXnCQu0vZHq2afwVstJ7lM2W9Bv87mb_TJCqMI3PMi2rGPZmMTVmHgZo/s0/ssas-create-01.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEf4AKfFeuPrLhABH91qOFCp8WqTOuoqOpE6e-Kjw0oazRHB9USS4aKNzIu6FsPvYPhRAtKp5Bvwes_gPFd-4mK_WtO_8TFxUM3gum_zCZnn2QmIjvGLrk-H36GmTTcl9pVbYcgI_F820/s0/ssas-create-02.png)

## 使用 BIDS（Bussiness Intelligence Development Studion）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2j42c4C0AP_9RGrhlecsAmMZYuutypxyyqZ1vkkQiv78-KAzX_mte2JOYrQCZgm0fuiQ7gOGVCCd3BL7UNAMvbIcW_8uteAUV2mHmrQM6DfvG6OX3PB1MR5kbBa0oFYwQlWyAMJJr9KU/s0/ssas-create-15.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9oveg17FnbtAXXZO-y_mNQj8XcZ_iFGB7FCDMCuxz3hM-5KYHxHf9IlkOFddoFlLtrPSZgjkN2KWt7r5OFXeBYGWWIl00qM1REW3rN0IEploFtVCFL4FZRGsPPnxDjk-285EWW1WW5sQ/s0/ssas-create-16.png)

# 設定 Data Source

## 設定 Data Source

在專案中，新增「資料來源」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5BRrqKeUJf98OW2TkLdJSD_htbQ7hMQES0iYrJdH2fXXbsPxJkZ6eSrcJU2ydO7gxijtmu0vF_OAQUGrDS3mvSB4eJ8QKPZk3vmk0iN0aOh3Zqi0gO2QL8tEwAMJ5KR3lYu5xaE9SF5A/s0/ssas-create-14.png)

## 設定 Data Source View

在專案中，新增「資料來源檢視」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5BRrqKeUJf98OW2TkLdJSD_htbQ7hMQES0iYrJdH2fXXbsPxJkZ6eSrcJU2ydO7gxijtmu0vF_OAQUGrDS3mvSB4eJ8QKPZk3vmk0iN0aOh3Zqi0gO2QL8tEwAMJ5KR3lYu5xaE9SF5A/s0/ssas-create-14.png)

挑選 **DimCustomer**, **DimDate**, **DimGeography**,       **DimProduct**, **FactInternetSales**

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGmX_907JRv9V_dp_XVgm2DMrzsaemUJORH7q-H8gIx6omQZRDa1hk9oq5_k3GUgiQeHEVrajO8Nwx-IUE9Ylx9QIDEzse7xXyyOCRhGipJw2onQdUQrTXQjjfQhX6pIKC0CiOtPFRb0M/s0/ssas-create-07.png)

完成的資料來源檢視關連圖

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7RYSReHafWW9EXnp4Oa1HGMYJerzGyt-euTEvFRgqy5haJUcB-Y03Wdna8_OEuSHscGVgJpHp87BMQnraGu9St3pdWrNz7XqsyfcN2G3tVqsHSiT44c4c3Zzu40XdzUdSnUXw5Q48bYM/s0/ssas-create-04.png)

## 修改 Data Source View

你可以修改 Data Source View 中的一些屬性值，讓檢視表看起來比較簡潔易讀

例如修改 Data Source View 的 FriendlyName：

先點選 **FactInternetSales** 資料表，在由 Property 視窗中將它的 **FriendlyName** 修改成       **InternetSales**

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRaTXhTLAn9MDAwxTCxb8Lbes_5DUhdnkPtFEDCVqrv8AyqIc-KNYCvUIw4X0mF_5KGrP3SpL6-DEUCH_UTJZ_XZnbAlJekX4-bH81TAbCGB0XzAyMLdJfKL79vbHjA_Q4HG7H1kzgPZg/s0/ssas-create-05.png)

同樣的，將其他資料表的 FriendlyName 都修改一下，你可以得到以下新的 Data Source View 。這個資料來源檢視共有五個 Table。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjYeiTD1iLeZtUVRAft6x9171ufGAfJDCP7tl-ucnB5-Zyqurkt_Yj6nLTVoaOfm7MNpjjdS4Qt6RUwPNMqZVH583WUyUZ2KZGl6daJsNv1tncrg6dAok_xUiW2Wo1Xq0TFFllO7M_pQUs/s0/ssas-create-06.png)

## 在 Data Source View 中加入自訂欄位

你也可以在 Data Source View 中加入一些計算後的自訂欄位，底下示範加入四個自訂欄位：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjegZmfPmwrSMJ8607I9l2E0l-ECg-8LCb0PQVGDbmLht7x7uM13FcOfz6HVgEBqxrFUACQMfdX9MRu2ZmHBWs2XEDC1eHJbXV8KiIYN798vECJu3sjg8oibzpZKwLxFmlqE_CnO4SYB2Q/s0/ssas-create-08.png)

例如：

### 1. 在 Customer 中加入一個 Full Name 欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaBw9uKbPBAqVYC174y-0KN6WKn4lg4-tFejyujUx_cMNj3dd48M9jGAUBVe8SmdvgxHE5X5U0yXkC30Br630ADF1tB7R9ajo9eykscxrk9535BuKCL8pa70aHkSUueNBL0SW_S7bOEy0/s0/ssas-create-09.png)

### 2. 在 Date 中加入一個 Simple Date 欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2CAmOMvToTsK4eKTuybiHb2bZ9rRypDnyqsrJ77q55guH2PYztzxGiDYGfIyuN-inljxXaQ06HJdKseuFSAcjwdzb28TCczG1zRWX4ZY-AinwYi009Ms66mlDsugV-JULFbnU41fEnak/s0/ssas-create-10.png)

### 3. 在 Date 中加入一個 Calendar Semester Description 欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihWDbTV8h-Lfgx7WIF04ob6fmE0MnT55F50jsZXdR-Y7W0cm9qW0iqp_-y1ky8mvdxEV7jgouhghK7_ayiELIQgrmClBfI8YH0ZBlR-Ef9f0LeFdxMs7RGQBXmw4serzXZdosaHDlps2I/s0/ssas-create-11.png)

### 4. 在 Date 中加入一個 Calendar Quarter Description 欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEicN5_yRwWY42exo7xbhZ9PdzfN3OiCKywr4kVTABMW9K5Jy7NfkFWE24rxOKDb5YSny7gmt4Lg9cNVCrkXIJ15stJfrVtcXbWN1L9RcvUXrd26y6lXIoXPGt4Cj0J5qNwUvvCfni1nnSY/s0/ssas-create-12.png)

最後，這些具名欄位，都會在資料來源檢視表中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0K-q0OvI93sZX2xZRk-T6eYir6JQO-PLb0x932_Vk-JaNvqaLUx5ali2VtL78RNEor7nuls6eslBjgNIRgoNm363nV-K02ghJQfhSwgVKPKd-PZexXVu0dfoGOqEtD52Cii1sM9s7APU/s0/ssas-create-13.png)

# 設定 Cube 和 Dimension

## 1. 新增 Cube 和 Dimension

1. 新增 Cube

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQNe9jU0v8i2i1_ubkqav8KcfneT7HJMjMtecSPQdG-Lnxp3Nk2QWN6QkTnZFCNclvMjIHdoCHQ6d45g5940UKP2s7KCefQzswl3cJIFt_xHjhTN9v26DkepGvhF10zzTzHhlcy-NqN7c/s0/ssas-create-17.png)

2. 設定 Measure Group Table

這個步驟要指定 **Measure Group Table** ，你可以直接點選「**建議**」，系統會自動找出哪些資料表是 Measure Group 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdV2F5-peOKTRZiiZo6el70H8FcF91rDeArHsW90ORr4uaLG9xmc8VY_JfKH5kKoe_tZep5glJf-lMlSzWNv4Z1xYD8pEc4gdPceidNBfFRc67fbOLtb_1jaGpopYmtZeuITkybqw-zwI/s0/ssas-create-18.png)

3. 設定 Measures

前一步驟我們指定了一個 Measure Group Table ，所以在這個步驟中，  你可以在 InternetSales Table 中挑選哪些欄位是要加到 Cube 裡當做 **Measure** 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg93X8kssz0fPO0OqPHzt7Ky9cC1iVXWbN3H3fPPN1VOPjkB4KfiI2P1QKF03KYTerHyFsicqEgU9Es7vpyLaiRPNSyU5zHQSRp0hmHjFgX4unlRCwDxAmSawNR20H4xRrczktqAlGomZQ/s0/ssas-create-21.png)

4. 設定 Dimensions

這個步驟是要指定哪些資料表要當做 Dimension ，  你可以依據可用資料表建立維度。  其中，因為 InternetSales 資料表我們已設定它是 Measure Group Table ，所以此處不勾選它。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqk5OK47n_VDBs6OcBoJkwUVC5olllWypPVLZ7yjQKi4yLzLmtSKbjrstxCvID23021ZgFrt3C2bWH9B6G4lZocv8_WYT8oo8w0WUt_M8ccM8mnBLDw4pOUQ3xPvNsRgoleGfxEE8jLrU/s0/ssas-create-19.png)

5. 完成畫面

我們建了一個 Cube ，包含一個 Measure Group Table 和三個 Dimemsion 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjimtRzkEDQVB-TRqVThOL7eykAR_XThydH2YvDj-YsfXP1Tfgdyr1ZslRFOPR0BtfrbcgSKmRHK83kwiT3KbucWsCY6nBhgVfWQOZU1VYJ70Q8tnEORL3fwmUo66-ufGJXq-k21EhHa4M/s0/ssas-create-20.png)

## 2. 修改 Cube

建立 Cube 之後，你可以修改 Cube 中 Measure 的顯示名稱與顯示格式。

### 修改 Measure 顯示名稱

要修改 Measure 顯示名稱，你只要點選一下你要修改的那個 Measure 欄位, 在從右邊的屬性視窗修改即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2I5eF0oBTZ7BhTBXe_MIwdIdmwPGJdyZL0KYCYoJkGIZ6cn7ek6alD3SuPoIRXM-on7Fh1zX67Z7IzZ-vQ3YjRsBhafenYmjUTU6s4DdJ2PPfySeIYmmye2qFz9wRBUARrzkKoHM2zX0/s0/ssas-create-22.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGwlEEbuGqN0_k6YlgVkIXi-xnWkkx_FygeHZxyBAE4rfKoi3QgIL25kXq5FFZAYTMcmiMp99scVJoJs2_05fPKJS2P_hVy5BEY94AOe-adJUCWR9BFL9F6baPgWZ5djXZvpN0ahoehrE/s0/ssas-create-23.png)

在這個步驟中，我們總共修改以下 Measure 的顯示名稱。  

- Unit Price Discount Pct =&gt; Unit Price Discount Percent
- Tax Amt =&gt; Tax Amount

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiE08EcmKT1ft7ZdYPrFJkwrO_gy3B_o-oiy5a0PYNgPvY5eYJ0d6dpZn8IP_BrmqNJI_MwYeCiV9nkekLprUllQAe8ibrglRAwfeRgcVK2elKhKHIyv29oAs6f0w2-j5jNyQCJznqrkmw/s0/ssas-create-24.png)

### 修改 Measure 顯示格式

同樣的，你也可以在 Property Pane 中，變更 **FormatString** 屬性，以修改 Measure 顯示格式

在這個步驟中，我們將 Extended Amount, Discount Amount, Product Standard Cost, Total Product Cost, Sales Amount, Tax Amount 等 Measure 都改成        **Currency** 格式。

另外將 Unit Price Discount Percent 改成 **Percent** 格式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvyJokNcWvVlNCf2ewQc1cEyjdas3bo5vVVDedkunFKhhR4wHljg8RZXGPC3M-UnvOPvnU30Kivv9WTWlgXpahxrAX8SAMJ1kogJPGBymtLJkrLAkfUilf0S9h_XG9tFA6pvKbTZKsukg/s0/ssas-create-25.png)

在 Measure 窗格中，裡面的量值群組預設是使用 Tree 形式的顯示介面，你可以將它切換成 Grid 形式的顯示介面， 這個介面有時候在操作上會比較方便。例如，可以同時挑選多個 Measure 一起做設定。

在 Measure 窗格中的任何空白處，打開右鍵選單，點選「方格」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3crXxS-pCjajq5HRpNfYiDkqBb9IFlau58c2lhgR_78DnGwGuz33uRTTqpfCfjJUZuFLn5Qw5k6mPDTtebhtY8iWqm7rBeIj9MYbVVRWv38bawFreGgv8aPs-7f6SGn0nyqJnzc2PaeY/s0/ssas-create-26.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgF9lbDRZBo335BwgdjhJE0-GU4m1PO6XCfHO1C_I0txHqQOQkH9k8zFEXsPhM5kgD3UATgRvFo52A3Od4W5erSelbET0q8lYgPwDwqTvaK4SAbJxiaLup-HTc9KKQh0TsFLVtE3aXGzsI/s850/ssas-create-27.png)

## 3. 修改 Dimension

要修改 Dimension ，你可以開啟 Dimension 的 **Design Viewer**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7LsVGv_bUeRBu81PNv8B7nRwVg2254mCFMJXv9Wih8xlRwU3ipqjezgo-Ca76ydOj3jfpnz9W9yAT_vqqmgYHcbCJBQwulGlI-A7VpmYsxCk_boU2y7fAGo8LUYEQJm9ZgCnQ4QSLycE/s0/ssas-modify-dimension-01.png)

雖然我們建立了三個維度，但是目前維度中只有相關連的Key值，我們必須由**資料來源檢視**中挑選適當的欄位當作維度的Attribute。

1. 在 **Customer.dim** 維度中，先將 Geography 資料表中的 City ~ SalesTerritoryKey 都加入 Attribute 窗格。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj29byXL-A_1aXIPxibXgdx7SLWrKA5MGLd8di6oODjD59A_W6dH-oNAUlhBVsOH2Xu2WTppf-KFDm5tFIdHSINySaeP9PYP9Aecqnfwh2ShrQT9F4cuIZl0yzxfle2Lfkrfdj5zx0S_Pc/s0/ssas-modify-dimension-02.png)

再將 Customer 資料表中的 **CustomerAlternateKey ~ FullName** 都加入 Attribute 窗格。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiNDfAvfgcCpJ0nWrwqMXCEgOJ23w48CecS5c5j8Xw1j4ShpcGqgM_Io644ze2WoxeN6IEw12xq1kLp0HhJg6KhPM3U69BWjGJzFSfoAYoOgAXYyl7NnrycTI0mMqQ6af4kyESiyK6oVTw/s0/ssas-modify-dimension-03.png)

2. 在 **Product.dim** 維度中，將 Product 資料表中的 **CustomerAlternateKey ~ FullName** 都加入 Attribute 窗格。  因為二進位資料不能當作 Attribute 的資料來源，所以必須刪除 LargePhoto Attribute。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqsk89IYyurIgA8AIL5xun71Rl2ay_cyhTjUX2VAubwBVr6eYJDFYawW-RcSGo9Uf7A1Qxfjxy2sf26vSX6cwOJyxtxN-l7TBnds_clBLDac1G-NpokOrP70cMgo45EwaZ0BsUTlGNiwo/s0/ssas-modify-dimension-04.png)

3. 在 **Date.dim** 維度中，將 Date 資料表中的 **FullDateAlternateKey ~ SimpleDate** 都加入 Attribute 窗格。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgaFKWDdilP42zh-UC3yj9TLf_5Dwt1d2zDZN99RKxqajTslhgE4Om1DSlJOx0CYV3ylgqIAz3ptLRXRW40si7kcrNvHCwNQAGZym8U9TtifDYzwbk-TO_r2t-8bGu0UIi93aXsdzFoZek/s0/ssas-modify-dimension-05.png)

## 4. 修改 Attribute columns

在維度中的每一個 Attribute ，其資料值都會透過繫結，而由 Data Source View 中取得。  例如 Calerdar Quarter 屬性預設會繫結到 Data Source View 的 [CalerdarQuarter] 欄位，其顯示的資料會是 1 ~ 4 的數值。  你也可以變更這些繫結設定，例如繫結到我們先前在  Data Source View 中自訂的欄位，那就可以得到不一樣的顯示資料。  

1. 設定 KeyColumns 屬性

前面，我們在 Data Source View 中自訂了一個 **Calendar Quarter Description** 欄位，由於這個自訂欄位會讀取到 CalendarYear 和 CalerdarQuarter 欄位值，  所以，我們必需在 Calerdar Quarter 屬性的 **KeyColumns** 屬性中指明結繫到 CalendarYear 和 CalerdarQuarter 資料欄，由於原本就有 CalerdarQuarter ，所以只要加入 CalendarYear ，再調整順序即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMwZx6tFR1FMqNEjaWJDkaUx2IgS6ZDrb-h0kg5SiRvCg-9ZO1e6xpmADKZkZioUm2ggiGV_8hhP4dEwVKLEtt2ZY01W47dRg3Djq3IWzRH2dNzH0EEs9fQINj7haJFzMn9b7a2-43VF4/s0/ssas-modify-dimension-06.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEimGKnbCUiLNXfGBqVR5sYG1Q1I2he4KLljkoUB95ba8RLk0ngIsjodJojjIVAi6Scs8-vId0SyJlO_s9j-QBEfCwTIzoYmgPQSMp4I3SGNhSyPY7jyCnQCgRqHN2p1DiFgGYuZts64Y/s0/ssas-modify-dimension-07.png)

2. 設定 NameColumns 屬性

將 Calerdar Quarter 屬性的 **NameColumns** 屬性，更改成 Calendar Quarter Description 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5NsUC8w7lzX0CScMPydOWRBjSha3Eeh5N08B126I-ehDkW0d4c3_zMDs4RHcIFZ4YP74wyl25DIbWb-jmaPQcvPB7Mz-nGL8vWdGFFmOW5VAJN3eOJQYq_s9SwisHNLiFIeTGCZkb5Xk/s0/ssas-modify-dimension-09.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjB32udECJnxSbsG2ciEYj8XaqzdLf1XMUimy961NEt-zSVFnz-WeJQG1wXVRGW5IilYWMFlRCI_T7fAAFh4ctvDRsyIk5viRKukbBuqwTpoJNu1gT5Bb0LBFyMm4B4sRf48K1SNAo4Ypg/s0/ssas-modify-dimension-08.png)

3. 同樣的 Calerdar Semester 屬性的 **KeyColumn** 屬性也加入 CalendarYear 資料欄；        **NameColumns** 屬性更改成 Calendar Semester Description 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaeYfr7Wm9K-aCF6-4k2dpipBtenVNAXALg4a9hY_PAyh4tu8teRwm-RnWt4D5ZiReOcDlEPUQ4LyJnreZwCxaZwihJ55aFfw16KVFxZSo2woTEowK7LxpcejQzSHw1UY6s_yZNv7Mfqc/s0/ssas-modify-dimension-10.png)

4. NameColumns 與 KeyColumns 

- **KeyColumn**  ：references the columns that uniquely identifies an attribute member ( one or more )
- **NameColumn** ：references the column that contains the descriptive labels. ( single )

在 Dimension 中的每個 Attribute 都有 **KeyColumn** 和 **NameColumn** 屬性值，這二個屬性簡單講，一個是內部用的數據，一個是顯示用的數據。  預設 KeyColumn 會繫結到一個資料來源欄位，NameColumn 是空的，此時 NameColumn 會使用和 KeyColumn 相同的繫結。  

但是，這二者其實是不同的屬性值，你可以分別繫結到不同的資料來源。而且 KeyColumn 還可以同時繫結到多個資料來源欄位。  當 KeyColumn 繫結到多個資料來源欄位，此時 NameColumn 就不能保留空白，必須要指定一個繫結欄位。  

下面操作，我們將 EnglishMonthName 的 KeyColumn 改成 [Calerdar Year] + [MonthNumberOfYear]；  而 NameColumn 則指定為 EnglishMonthName 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirEE3fz3iUgw6x2xKKTv-J1IHKFyeeueeAIpo5ppFup3GOdngBkjCIIudIwW06N5Din-IEbEBTY0-5skrey84KcrPES3ODpaNBriCt8rXe9uckeo06g9-_b6iIRMxnouGgw_9TtceL4Sk/s0/ssas-modify-dimension-11.png)

這個操作的結果，一般要使用 MDX 查詢時，比較看的出來差異。  在 MDX 查詢中，「&」是用來取得 KeyColmun 的值，「.」是用來取得 NameColmun 的值。

# Browse the Cube

## Deploy SSAS

在設計好一個 SSAS 專案後，我們必須將它部署至有啟用 **Analysis Service** 的伺服器上。

1. 檢查服務是否已啟動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdfr0-LZDpfX-xWCV-KZd5TFRMfGk8w0ESvvkpJhwEXkx0ag3H6H9V_CYH8xmW8MYcFyioJWqWNs57C5OTBTaTjJOl2sxTMAx2HVyjpADtWGTvSi5YXl9uyd4vn1o_9-mO1RttToQZLgE/s0/ssas-deploy-01.png)

2. 設定 SSAS 專案中的目標伺服器

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzJGAwF4z9hyphenhyphenskw87dduNIcdbovBVS1UbD1GaQ7olLjp4tYukjaiH0m-GWgDAYoMmVIipeyAP6IG_YxrJeHiD-fK5u9yita6_jjwTXC32qKHSgPOfKQijBZRAcx9lPxPSw5KDBrfavGOI/s0/ssas-deploy-02.png)

3. 執行部署

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitadzgyr3fT8Q735u0WPxXXDuKL5IS5FItiYoGkjXTcUhM5VinXxwjkj-z-5Q26Qbbr9q-I6-GxjS1Qc-g9YsB1OhV8-LLubS7_xzWVb6oPkEUfFZ8ArXsUVgI1HI1sCy46JXpI7rfuCA/s0/ssas-deploy-05.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0oRG-Oa3Oeo0cKcFsldDfQIUfD6QwJ1zZD-h0jm2IZMTyzmw86qrYNiN3qLh_HAuiYguRCFB1eTsKeAKVhQ92q0DfwGP6bKC1H3w7KMt43pP04DorU3YdQakwXg4ojbzQKRbXREOEeQ0/s0/ssas-deploy-06.png)

4. 部署完之後，可以連接到伺服器，確認 SSAS 資料庫是否已部署成功。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgvdfAcJ_kdHQwW7MpeFxXASTIGeh9pEDhDQghYELy5y7fZ0qEYMFtZ9eZm6Uz9fVljGJrw9bYmgm0D7FJzmB4AqbHQlS8MFDHPLSO2bBsWl0qMT6vkfAe7KdM6aGJGVAfj1n01qgPepJY/s0/ssas-deploy-03.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjz6zwDGZ5UB-aYz_nGAztaQRKRgedJK3Unsdp8jVB7GdIj8eKYWxhbksTgewGp1tVcNhkT4th7cyqyaZTJCnpIP20exlz37yeNoQ-4cvGTl2d0GWJxfCdi6BH7xE3apgewZ49ftmTjg6Y/s0/ssas-deploy-04.png)

## Browse the Cube

### 開啟 Cube 瀏覽器

要檢視 **Cube**，你可以在 **SSMS** 中操作，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZgN5Vif8bn49DURIew3HaXdM9s8VBX3jn4EsIZ4lPRcVVvdlGH3AWonGneWyIBh64eF_aXsQVcaAh0zuHg3BYWiLf5pTw3HtXmLyHjQAU2RHUAYZBWQhOKEWLcOn2fP_ZyWucfHiASF8/s850/ssas-browse-01.png)

也可以在 Visual Studio 中，由原本的 **DW 專案**中操作，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQb30RxyXUKbC_3UoilCZRTq0D9SiC8qI6AqBbAL_GQuXLym_EcbdI-t9IF-Y-R78-eYMKikp_gWDPlrwaB42XO6FFU3Vvn3M0afUtO02jPqBX3HkA_k73TMVzgw5JJ-ljS7P_QQVv7qU/s850/ssas-browse-02.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJJi3tmPScFFq6xK0uf9-kFxwYFnbZPDnhH3elRbK10IPTU4OXdv6cRnpKOwLBJsy9NilPdZRaYxDmvnswPM2kvc7K2OH2PwbPzwQ8sDLmilRsN9ZXMwvjB2sB2XbSNr_IMfws7HI8cfA/s850/ssas-browse-03.png)

### 使用 Cube 瀏覽器建立查詢

在 **Cube 瀏覽器**中，你只要將**層級**或**量值**拖拉到 Data 窗格，就可以很方便的建立查詢報表。。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsDsj64y1xpgQQEbu6PQEc9gCc1u8aSaIzNZMeTEuFrlHzUjviFidkWPtpkMxZjwNfculxYfl-SBIq2j-_jbQgkBOExZaiLk5LwvAsVx8djYWAiyl1voIQM2fMpXXw01e_s4Wtgq22DU8/s0/ssas-browse-04.png)

1. [measure] 將 **InternetSales** 量值群組中的 **Sales Amount** 量值拉到 Data 窗格中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEif8p7Xe19v4VhQxUpGh1MNzux3fjqefyG7q6MfihYD80TMaHaqExPPbnJB70P9EPb51Av6l7Lt276zlyKuBArin9cuQJm88StotEQXDyC8ctYYVgrxaHH0tDGJ-N7N1cGfN-ztQfA3bsc/s0/ssas-browse-05.png)

2. [measure] 接著將 **InternetSales** 量值群組中的 **Internet Sales Count** 量值拉到 Data 窗格中，並放在 Sales Amount 後面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-1ZTP-_ZC3nzfyRmuRJ_Z6sZ5fheb_v3B9w0gtyX1OWiJkyYb0WO1tqUHBjLC2xTmd93fd-mh2XlYhwSDv20kg64piz1Oxs5FT2-rxIsWpbCEswbdYHkTWFgjePrZMiDNOARAhj8adD4/s0/ssas-browse-06.png)

3. [hierarchy] 將 **Order Date** 維度中的 **Order Date.Date Key** 這個**階層**拉到 Data 窗格中。

由於 Order Date.Date Key 是維度中的階層，所以它的值會展開成每一個列。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiLteHuP2zxjrF6RchNrcYRJj025eFRHseOoBEUNsIHS0jlo9s9KpheoVbBKXMYYOMjNeiFPvMDCBptyyrCa4hQtKX9gKPsS0oR_1wIgpWQiJlACopUCa1eNjjGZWFuyUEnPBac6pK2V1s/s0/ssas-browse-07.png)

4. [hierarchy] 將 **Customer** 維度中的 **Full Name** 拉到 Data 窗格中。

你可以發現，這樣子的查詢結果就是，每一天，每個客戶的總消費金額和數量。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYTjEPTubOTHT5tWdHq-kQEziiMMMF736b3t5kW4OgT9qv5fMNstSXCXrqk-sjIFEM9orjTqgscbOetdohSgf-f7aAD20-s0yqJnh_7R5XV2YXb-ESh8Knl9rxIYzMSZ05d7WI7jov1oE/s0/ssas-browse-09.png)

5. [hierarchy] 我們刪除 Data 窗格中的 FullName，再將 Customer 維度中的 City 拉到 Data 窗格中。

你可以發現，這樣子的查詢結果就是，每一天，每個城市的總消費金額和數量。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitdOmuzD5l6Fz959cuenMQSB28buUwBulfG18cbwTAHARAhII0l6driMvx3gF9Idy3-OtZCgnfLkTU6UjjWbcmtV9G99ISzmJs-tdN0fNqNoHhTdnBfMH4D8ULFa3vWGVDKjPjwBMIync/s0/ssas-browse-08.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgATtAcj3NhonsVlYpJcfacUNUPFii9YRLR-desMNq6MNjX2F21i9Qx8MsJffs_pU6PTId8M-mkFYEUUJ2_6IfTiM-2M9e8xF8hXQXs0PAcZ01Ni5kJIFxLZt1eBaOpWIxuRPr1-3uCNeg/s0/ssas-browse-10.png)

### 使用 Excel 建立查詢

除了由 Cube 瀏覽器建立查詢，你也可以由 Excel 中建立查詢。

1.設定資料來源

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSlT1gai19yap5oZpuZV6NSKH-IFMfU5wc8zbfz0VJ-GBLZ9MLvhk6h4S4APsdu1MSoiyPwkl8-_YzBcyF4dFtnJ6ICFV1NeaGYWEG8UFqHhpMIDadSoutzyYgnOvGTw8obhrp3yScbl4/s0/ssas-excel-01.png)

2.設定連線與驗證。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1WGHKL8Si5rGW0KrcclvBHz9nwipkHDllNOMjZim9nbH027LwqBpSxAWEHeqolMnf3Y1-_TWhdrCisojGrINh7gdgQ-vpNO4SjFFqz4cUJl_HzZlosszf_yeiu4vj0ANP5CeY8myZI_E/s0/ssas-excel-02.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQNe_SbKkADxNgv4lDHOh952Dl4aJCXtJDwDcAJgn_YivlZk_FKihgofj_kBqWpisJY3RgynJAKVDnSGwL_B5ePwhkNRxTGHrlV1_bhyphenhyphenMilk6WxPkR96Ss_X5ah555AzmmSq1whQktcVY/s0/ssas-excel-05.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgfby5gv_atqYRj6I9UxhbbQ3Ab3Sj0yoIFkYSQuEwzetd7-s3TyJ0tu0ygYT1f0RLgw5tFsVlbjqEtfBJinWdJYhK-GPHfKeUxvVClVsRqlyjJY4UAGpOnfxyOMnriD-t_G1KmaT5gq90/s0/ssas-excel-06.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjk67g9euYJ3QDSJWQ3aFoPbCGnJlyf5YrtAvrhq2DinMjYXogkDLhWerHWoFQSPY9Y-JOtnWF28nZGePYk_ETt9PEuqmE8PhrBnSo4JiXH12GNJnWG2BDqYC4x0naQzFPmhQUNXQMnA6A/s0/ssas-excel-07.png)

3.設定以**樞紐分析表**的方式，匯入資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbwPr08Yg-3b9Gpt3CjMpFaS_UHnpIJnGx7vsThizEs0W0uVxScJgI3cW-vv_h0q3dAb0IJQyhuSejcmCQ0wF5RULPvlcsd2KMGUNxFKfgyA034umxFWddiK8TSxJKMRkoKH4nJ_xl-LQ/s0/ssas-excel-03.png)

4.接著你只要在 Measure Gooup 或者 Dimension 中，勾選你想要看到的資料即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYKZnsDOmkJb3zDNCWEgDsRR8X-2izfN3waVrnK3fKTejhgvZJ96JmiRGAWTlJqVZNhaEm_JQz0Wv5ZoJ0AC8gQxDIIHhMeleXxrctZqWz6gH03nttvuVtKhH4YEabYKQ4SIRStya1TIk/s850/ssas-excel-04.png)