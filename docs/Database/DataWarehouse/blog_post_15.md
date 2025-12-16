---
title: Lab15：資料模楜比對
layout: default
parent: Labs
nav_order: 15
description: "Lab15：資料模楜比對"
date: 2014-02-24
tags: [SQL,Labs]
postid: "4197046172888495120"
---
在這個 Lab 中，將練習以下二個主題：

- 如何使用**模糊查閱（Fuzzy Lookup）**，對整批的資料執行識別對應。
- 如何使用 MDS Add-in for Excel 來清除重複資料。

# 使用模糊查閱（Fuzzy Lookup）

## 回顧前例

這個練習將會直接延用先前 Lab14 的封裝。該練習中，使用 [DQS Cleansing Transformation](http://technet.microsoft.com/en-us/library/ee677619.aspx) 來執行資料清理工作，再使用 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 來執行精確比對。  那樣子的結果 Match 與 NoMatch 的比例有 1065:784 。如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikTFAYuTCBxTbkDxhGbC4WkT7Q2YLHUty7JEUDcCXeh3l0QrheeE5_VqWfp0tBh2kQLq5SfKdEfXkjiyXqEen0SyIRBTNvLrmlDfh4pdDjuCUHuaQnGHOk00o2r7rIhylJ2zHiQjCJEZw/s0/ssis-lab14-matching-17.png)

底下接著，我們將針對上圖中這些 NoMatch 的資料，透過 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 再進行一次模糊比對，看看能額外找到多少筆相符的結果。

## 建立輸出資料表

在 DQS\_STAGING\_DATA 資料庫中建立以下資料表，用以儲存待會 SSIS 專案的輸出結果。
```sql
CREATE TABLE dbo.FuzzyMatchingResults
(
	CustomerKey INT NOT NULL PRIMARY KEY,
	FullName NVARCHAR(200) NULL,
	StreetAddress_Source NVARCHAR(200) NULL,
	StreetAddress NVARCHAR(200) NULL,
	StreetAddress_Status NVARCHAR(100) NULL,
	StreetAddress_Confidence NVARCHAR(100) NULL,
	StreetAddress_Reason NVARCHAR(4000) NULL,
	Updated INT NULL,
	Record_Status NVARCHAR(100) NULL,
	CleanCustomerKey INT NULL,
	_Similarity REAL NULL,
	_Confidence REAL NULL,
	_Similarity_FullName REAL NULL,
	_Similarity_StreetAddress REAL NULL
);
```

## 加入 Fuzzy Lookup Transformation

這個練習會直接延用先前一個 Lab14 的封裝。在原先 No Match 的 [Multicast Transformation](http://technet.microsoft.com/en-us/library/ms137701.aspx) 輸出流中再接一個 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) ，設定如下：  

### 指定參考資料表

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6tI9rHhgQxv7xLsF06eDl8G1xjdaHfTM7TDSAciY1jvOsTPbNwp59_Qz1Cq3UIv-z6A1Q4g7vM5P_OtXJM6rc8M02j2N_HFvLPDJ-TyuJM97QmTo_-BAWiPPeiCYOxP8f8o3rHQHHRCg/s0/ssis-lab15-01.png)

### 設定「比對索引（match index）」的狀態

當使用「[模楜查閱轉換](http://technet.microsoft.com/en-us/library/ms137786.aspx)」元件，當封裝第一次被執行時，該轉換會先複製參考資料表，並在複本資料表上建立索引，稱為「**比對索引（match index）**」。  你可以選擇是否儲存這個索引，若選擇儲存，你可以使用以下的「索引選項」來指定索引的狀態：  

- GenerateAndMaintainNewIndex：建立新索引、並儲存，並設定維護新索引。該轉換會在參考資料表上安裝觸發程序，於參考資料表發生更新時，自動將索引同步。
- GenerateAndPersistNewIndex： 建立新索引，並儲存，但不對其進行維護。適用於當參考資料表是靜態的，也就是都不會變動的資料表。
- GenerateNewIndex：           建立新索引，但不儲存。
- ReuseExistingIndex：         不建立新索引，使用現有的索引，這會用在多個元件使用相同的查閱作業。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5lwVq0aMcejluBGu5PA7sfXs_XHAxzQ_Gih62XOpYopAX7nwtRr92YJx585o-uoqIAsBJF4fqVGz_4EdHQXJRuwrDIJ9Yvxhm6FviUxbPN5UiVzs0rasz00wSXo3jDaYzp7pysx51zao/s0/ssis-lab15-18.png)

### 設定對應欄位

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjk1aBdDwA5uAGjvbValgzxjaRiw3HGqWSqCPp2-LLo6dsrKrycQolN25KsgXSFeIdS75zZ5nP3Y7_9xu5ihePKMk4sVQCsnYYUYVVmg96Vc_XmdEa5By0TOSqSF-ZBH5a-Vvi7NT315Rk/s0/ssis-lab15-02.png)

### 設定「相似度臨界值」（Similarity threshold）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjdkkiNVSk-maZPR9SgmkVgyepq7y2Xmzb9Wy7zYwPIGQbslf25avIEWLyQDWXLTV7JqRD1Kxu6JSD-QqJVIfpTF2Ha5iRajC85mTeJONW5gWLL2VC9myQrH29nSWJHyaPGwgNHm-qW5ow/s0/ssis-lab15-03.png)

## 加入 Union All Transformation

在 [Fuzzy Lookup Transformation](http://technet.microsoft.com/en-us/library/ms137786.aspx) 底下加入一個 [Union All Transformation](http://technet.microsoft.com/en-us/library/ms141020.aspx) ，並合併下圖中的二個輸出。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEie_zzvKl1_-JUQQ0dZEpUF-60wp3aee_qsvJa5l1pDaJt75Ck8xlJbo0TrNEsoA63PHkBUDFG8H9vyjY0l0mMjbfYqMR28xduBAZXn-zQMhcmZNvlUh4N-GwzSlWWylknSy38uERr9738/s850/ssis-lab15-04.png)

## 設定比對輸出

將上一步驟中的結果輸出到一個 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) 。其目的地就是本練習一開始就建立的輸出資料表。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSiAtDoXEQqT9hGRvIqI5xGJtn0mjU5kIzSU7pkrv7H48tssTX4sP2dcZ7tGkLMfX5crFXGbFoTad9Ahg3ac9Ke2zWwz0MEooGTQCu2CZaIo80nN3AetPomfVTHDsT_EFoGuZOy_15clk/s0/ssis-lab15-06.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXiiUZBk7aWffI5KsgtNgpVKsinlXVyHswFM5SFLUU663e-BKPw-sLUHcEq-JprK_TiIvKDkka76VzsuV5B8CzAVOreGMjb1i05WNYgxmeuTJTXwFSBhkQ_F8GH8LMPoDq9y7Ujv4LmnM/s0/ssis-lab15-07.png)

## 設定 restartability

為了讓封裝可以重複執行，我們在控制流程中加入一個 T-SQL Statement Task 來刪除舊資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgl29nT5XUAGgAsHLupIzZnXhuANupcnlqZiMjHodCvKgkzCxlyc0GzoXfEApu363Hz0CYn6cuksA3b2Auc91AvJXyKs0idKEC2BvqgQ_tLjceTiHt0j4haba6_kNX5z2E9Dbr3l_gYtqs/s0/ssis-lab15-08.png)
```sql
USE DQS_STAGING_DATA;
TRUNCATE TABLE dbo.CustomersDirtyMatch;
TRUNCATE TABLE dbo.CustomersDirtyNoMatch;
TRUNCATE TABLE FuzzyMatchingResults;
```

## 執行封裝

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgu-SGvm-0-5hCWiIDCZdytj6TnOd3mde78SwrU5K3GgwJNKh_Brw0Q1I7bQg6FmZc5T2YAG6wT5jB84xBExhxx2Wc8_joOpM11eOpSv7X3HwAhe_SByflb-FZ8DgRlaBmu_kvWsjxVJJM/s850/ssis-lab15-05.png)

執行封裝後，你可以用以下語法檢查執行結果
```sql
--A)原先就在 Match 中的資料
SELECT C.CustomerKey, C.StreetAddress, FR.StreetAddress, C.FullName, FR.FullName, FR.CleanCustomerKey
From FuzzyMatchingResults FR
inner join CustomersClean C on C.CustomerKey=FR.CustomerKey\*(-1) 
WHERE C.StreetAddress = FR.StreetAddress AND C.FullName = FR.FullName

--B)由 Fuzzy Lookup 找到的結果
SELECT C.CustomerKey, C.StreetAddress, FR.StreetAddress, C.FullName, FR.FullName, FR.CleanCustomerKey
From FuzzyMatchingResults FR
inner join CustomersClean C on C.CustomerKey=FR.CustomerKey\*(-1)
WHERE FR.CleanCustomerKey IS NOT NULL AND (C.StreetAddress <> FR.StreetAddress OR C.FullName <> FR.FullName)

--C)由 Fuzzy Lookup 還是找不到的結果
SELECT C.CustomerKey, C.StreetAddress, FR.StreetAddress, C.FullName, FR.FullName, FR.CleanCustomerKey
From FuzzyMatchingResults FR
inner join CustomersClean C on C.CustomerKey=FR.CustomerKey\*(-1) 
WHERE FR.CleanCustomerKey IS NULL;
```

由以上查詢結果，你可以發覺(B)與(C)的比為 691:93 ，也就是 Fuzzy Lookup 比對出了 691 筆相似資料。  這個數據是在**相似度臨界值**為 0.5 的狀況下所產生的結果，如果換成 0.8 則查閱結果為 358:426 。  也就是**相似度臨界值**要求越高，能查閱到的結果一定相對較少。

# 使用 MDS Add-in for Excel 和 DQS Matching

「 MDS Add-in for Excel」除了可以用來維護主要資料，也可以搭配 DQS KB 來執行資料比對。  

### Connect

連接到 MDS Server 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiscEebN3Mgs_MmNgBsgkPLA_ELdkBfrUIQKlOpZ-aTyc1cYEOSuIKIvgMQFlpTM5dZhyRqJ1MLjHg3tAeHhy_ONUv5ve-lizZoUXtkh8AtTQuAqOQ-8hil5u69ZdsjPkBiKUTmB7IDCwE/s0/ssis-lab15-09.png)

連接後，在「主資料總管」中的模型選單中，選擇先前建立的 ModelCustomer 模型，並點選 ModelCustomer 實體。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFPqWxLACA9u7fvU8lqB09J-g-op4lP66j3xY1dweI9YKfifJ_CbeV7hls_ZtY7WiZvdV4_mzra74uvUIcWwxHntyEEnQqM87-6i-zXWRenl2jMRg87diAthovAuVyteD3gNLiFz0Sx_I/s0/ssis-lab15-10.png)

### Filter

我們由主資料中篩選出若干資料來執行比對。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGj8pptP1rA_gh0c6g_YoMqZUHE1gLBHnf6RzWprZUH3OmQH-NH8k36Qj7oGBsvknSZ_dMp89RllYXO7-xyWG9Zzcjrh_KbwOaC9_JE0UnStd3FLmaXD___yiYtJnUClcY3ucx2G7dClQ/s0/ssis-lab15-12.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCfe631GIB3XDzvB0XSDE4W7CQMMmEgoq571fDbghFVj0FyYsWce_zVpCVIEDWzhIkHnJXsHe9i8oKMZuKb6Ou4ConEGomRGQXabMeJqZN55FV_Ui3ZNDK8nkOB_G6ZGcd6BqhKrO8mTU/s850/ssis-lab15-11.png)

### Add Test Data

在篩選後的結果中再加入一筆測試資料，Jacquelin Suarez, -11010, 7800 Corinne Court 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggmfxglxdiRp5tCAI6e9_jUkyZ9XkD7Oupf5DtE4b2Pi80N4uEILlhMc-96A8j-fzT_jB-JE6tlCj2LROCT7FZdsKGi8WPoD6ZH7UtwHC7d2mKMEh1g5Od6fuAn4TEWcFkSFedJ_AiX-s/s0/ssis-lab15-13.png)

### Match Data

執行「資料比對」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEih_UL9qMr4MITMw5Pv9jJiluU4Yj1QDj5CgSjLgFmCR486t4kit1sAnijYpY7R0CzRDfubtc35cD8NBI53lJRGRJ2wY7IPJFZGg6QF9Pl3DmHgD-Y5OKaWOEUnzxkm_Y-cQa8MW5Uho3o/s0/ssis-lab15-14.png)

設定比對要使用的知識庫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSrDbqi5IYwp62QrRfAHETtd8O4_O6z-cRHDj2RDjzyTB93HAAz6bvZ2ApW_fdLpn22ZtZIsf1sythxnKHc7u7zQMmrkhWDDbhmb1oYQcPiUnqUk-thl6sAJmwT31I8zpdAX-r5T1vfm8/s0/ssis-lab15-15.png)

### Check the Results

你可以在「顯示詳細資料」功能中看到比對的相似度成績。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiiW7CfuV-gClRxKMVjnMRSV4J_bF_rO7gvmcTUzdHONJ0svN7gCs1KMcI0EryhHRiISzZqLU6pCZ5NVeVHp0gTOmkofoLMUw1SNAEEhzvUapBqNt54zcSOOoMthNgxwoS3q1T6dfzUxww/s0/ssis-lab15-16.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgheKuTNpVvVV4XFN10xk4i9PvlbOFxJBScM2Wlcak7oFIyEoZHHUlE_Aqp0nlIDMpUBYFlkglxZK5dRBdOUkKpHc7QfK-i-th11Ma4ROsuuKlmj25y_4jiIJCtlRuMidbkLiaZlhS5ke8/s850/ssis-lab15-17.png)