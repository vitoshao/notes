---
title: Lab12：建立知識庫
layout: default
parent: Labs
nav_order: 12
description: "Lab12：建立知識庫"
date: 2014-02-13
tags: [SQL,Labs]
postid: "7823467435718965640"
---
這個練習示範如何透過**知識探索**（knowledge discovery）來建立**知識庫**（KB），並且在知識庫中進行定義域的維護工作。

# 建立探索用的測試資料

通常**知識庫**會以**知識探索**的方式當做開端，由系統先幫我們探索符合定義域的值。

為了執行知識探索，底下我們先建立一個測試資料，內容包含所有可能的 CountryRegion、StateProvince、City 資料。

在 **DQS\_STAGING\_DATA** 資料庫中建立以下檢視，用以取得 AdventureWorksDW2012.DimGeography 資料表中的所有 CountryRegion、StateProvince、City 可能資料。
```sql
CREATE VIEW dbo.vCountryStateCity
AS
SELECT DISTINCT
EnglishCountryRegionName AS CountryRegion,
StateProvinceName AS StateProvince,
City 
FROM 
AdventureWorksDW2012.dbo.DimGeography;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWqWd4jvvC_sfJ3pa2aU0h8VlJXCYQLy2tPp2jNq_HMsGcomZupUhKw0_BnEDSN5VViz1cm-aONdsKbrmvndkRIky1i1BPlNm2PgHZI6gFstDt4myPBX93MCAgo3LvkqOtOaMRVVVWPEo/s0/ssis-lab12-kb-14.png)

# 建立知識庫(KB)

接下來的練習將以上面所建立的測試資料當做探索對象，以便建立知識庫。

### 1. 新增知識庫

在［Data Quqlity Client］的［知識庫管理］項目中［新增知識庫］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQoXl3Q6YQFk0xsnxvyjGF-GBPKgx5X6aLuFGC8wRd0bR5dVmlm1_O0EKlkka858Y6pynkQbRxQiyXH8lmNvZYjFYKT4bhmCHuB6nCZG_Iya43NlZzz5oOgjdgYINxX6yGi2vEfN-9ZSQ/s0/ssis-lab12-kb-01.png)

### 2. 使用[知識探索]活動

將知識庫的活動設定為[知識探索]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIS9zHJ0KUHeXUtGEiof50i2ebGTDdsTQ6LT3GKxcO1oWCIdeCxbB_mgsRaioMcOus3mEvu6x2bylft-ErsyYi2qVNtoTSXRnp812FLiESYcRonyNn5i1KLbJ_LnWRS4MTu3jX5IZW4yk/s850/ssis-lab12-kb-04.png)

### 3. 設定探索資料來源

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5hAcrGv06DokP57IBd5sdGX6nQ4OPAakOcquSgrDWWIqgmEiWoPUxD-96JIwZQjUYxhQWXqWxwwDik_PqJHOTRzymkVFWgXDGGg9Den3KWC-D7GIKvHisf4om5H3eyik0SaA-DAkKiOA/s850/ssis-lab12-kb-05.png)

### 4. 加入定義域（Domain）

在［對應］頁籤中，你要針對你要探索的資料欄位，設定一個相對應的定義域來記錄。  
在這個例子中，我們針對來源資料中的三個資料行，都設定一個相對應的定義域。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5hAcrGv06DokP57IBd5sdGX6nQ4OPAakOcquSgrDWWIqgmEiWoPUxD-96JIwZQjUYxhQWXqWxwwDik_PqJHOTRzymkVFWgXDGGg9Den3KWC-D7GIKvHisf4om5H3eyik0SaA-DAkKiOA/s850/ssis-lab12-kb-05.png)

依照下表新增三個定義域設定：

| 定義域名稱 | 資料類型 | 使用前置值 | 字串標準化 | 啟用拼字檢查 | 停用語法錯誤演算法 | 輸出格式 | 語言 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| City | String | Ｖ | Ｖ |  | V | 無 | 英文 |
| State | String | Ｖ | Ｖ |  | V | 無 | 英文 |
| Country | String | Ｖ | Ｖ |  | V | 無 | 英文 |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8hVUeY6_ddwVStg8hKYzB7okMvPLA-dwbsH5jfG3tPcV5OJnmBymNONXPy3VxBcou2vz0_zYmz_M3siAfHLe_QLoSL8Qh55gntOjMBt7NOSsYsEeYdcbo1EOgzHRvrt4Lp4bxsj96Qns/s0/ssis-lab12-kb-06.png)

設定完成畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwZ9NtFuOX2P4GiyauwoczNMLiO6a4nlLu9O47Te185TSrivdfdFiF_1Bs_R9zYamANnAA-AnyptISnBtTmO5Lcc_H1Bx5O-brOeJ1U4J5njQPCCVqQYxmtssE3KZlJkMoFRltkmA9inA/s850/ssis-lab12-kb-08.png)

以上三個定義域，我們都不使用拼字檢查，而且都停用語法錯誤演算法。

### 5. 開始探索

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0gF4i7yghNZz63WdNTgOSD3ALO-uRsriC5c88wWuxG8nQ3YUBoh80IRmjfzSaXcMbrzh3og7Puti302IuRqp9Sl8yOhG0EdnI4PaK0To7xx4TmHmw5BZ3j9wnvzW9mUjDxWqIYg20Wzo/s850/ssis-lab12-kb-09.png)

探索結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHvxqk5aGXq665g9ATUrIBideysoJ9N42HkCE29bIU2fhlitORdYked_KIF328E_gq8D7cLl3sykMusYhXlVj4x_hBB-2SaLSy1m7pUNsWhvujmrhD-9FbDum3NCA23RPzC_PHEZP8otM/s850/ssis-lab12-kb-10.png)

### 6. 管理定義域值（domain value）

在［管理定義域值］功能頁中，可以看到探索活動幫我們在每個定義域中都找到了數個**定義域值**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuC5aSZid2fI8pR0mOHRcwIJzCqSikQmtqL0UnYeolc7WqZaOGnA-cufQJtsAS8Q0YTgdgKCelDjFdln1x6tLzFl77ydjVTRJ8-KfDAB68KmiHn3xDSzjfGCV5DIPyJJrlKz0O7CJHO44/s850/ssis-lab12-kb-11.png)

你也可以手動加入**定義域值**

下圖中，在 City 定義域中，加入一個 **Munich** 定義域值，並將[類型]欄位設定為[錯誤]，[更正為]（Correct To)欄位輸入 **München**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhThfcUssJ2df7UylvAEBREsPsSC0jE5EgnR041_cI0jkHIke9V5RMLa-uA_uWCwVed9CknXKN11A5RSIJ9MT-LqKXf47FtlMAwAInb6YN-l0pAugdqzOrtv6r0W24xZCLjU52tKEjnWI0/s850/ssis-lab12-kb-12.png)

### 7. 完成

執行［完成］。由於我們還要在下面練習中繼續維護這個知識庫，所以選擇暫不發行。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgA_c5BB9zRoQPdNBpbnVG026MQ3wgzjuNTAerGk6WYC-5Z4VtrZGB_V9d72IBQQm8-okoB_l6eitPkP_AUrc80TuFPruHtIh8L_33bnUB4JSHCCYfeiexVCbwDDkO2Xkhll4xZQWZTe-4/s0/ssis-lab12-kb-13.png)

# 如何刪除知識庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-ZaXgS63MqVMwIxBL_DJHHyaq6a40J91iMfRYdcp2qvTHNrZj9o76gc92vcW1AGyxbCk153RLW79pJah3LzJnMp_UFznVbOD9FqpsphOPNmb2tRqmBJ_heH1CRQ0fDls0YUz7aQ1mP5g/s0/ssis-lab12-kb-02.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj11ZSRRQXZxDINVJm9roC6ybPvTqB627-Z5qUN8rEBkpnR8zKpa6fhH31ZKSt3aKFaCCeqhladzfcsrVrIqXYFaNkkt3-1T5-wHEV-juqHeHbqkJNS1ltjGtyiIqYFZvPwpRl5dnz6O-M/s0/ssis-lab12-kb-03.png)

# 編輯定義域

在上面的練習中，我們已經利用知識探索的協助，建立了知識庫，並且包含了三個定義域。  接下來將練習如何在知識庫中，直接手動編輯定義域，並且定義他們的比對規則。  

### 1. 開啟資料庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-ZaXgS63MqVMwIxBL_DJHHyaq6a40J91iMfRYdcp2qvTHNrZj9o76gc92vcW1AGyxbCk153RLW79pJah3LzJnMp_UFznVbOD9FqpsphOPNmb2tRqmBJ_heH1CRQ0fDls0YUz7aQ1mP5g/s0/ssis-lab12-kb-02.png)

### 2. 使用[定義域管理]作業

先選擇前一個練習所建立的知識庫，再由加下角的選取活動中，點選［定義域管理］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaEw7jRr5Obcw63v8LOwSd5vaUdgVm4puBcfhuZ12Wkptsr4Rcnwo7iA7jl0ENLbX1_nRu0xZWBmtOnsUyk5mlQNZ2Q2Yvaxi9HhQW5f0aRSmzqXyv5tPA5Ohr7o_X-sItG0pK3ssqudc/s850/ssis-lab12-kb-15.png)

### 3. 建立定義域

這個步驟中，我們將另外建立四個定義域。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEXp5YeL0HGpb9YNn7aWm2KWnHkPLqW8irttW81NpisepWTESxAKwo3sdG1G2VdAfDFBa_zrpteOHDGcxNQ7iKIo3CBXL_kLFaxKYmfakWkSUUQa1MhD5oOYxys6w4gX7v8dhmxacZRI8/s0/ssis-lab12-kb-16.png)

依下表內容建立其他四個定義域。

| 定義域名稱 | 資料類型 | 使用前置值 | 字串標準化 | 啟用拼字檢查 | 停用語法錯誤演算法 | 輸出格式 | 語言 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| StreetAddress | String | Ｖ | Ｖ |  | Ｖ | 無 | 英文 |
| BirthDate | Date | Ｖ |  |  |  | 無 | 英文 |
| Occupation | String | Ｖ | Ｖ | Ｖ |  | 無 | 英文 |
| EmailAddress | String | Ｖ | Ｖ |  | Ｖ | 無 | 英文 |

結果如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7mw-s-QOZJPPvTYBvez7t_N5FNyNXLRhF8ePp4PI-Fd4zwqmgOzt0fcQghZ7-DV4h3_JXXBa3-DFQHdJxDPic1TT01FwEMXwcBD49GShIOm4tUP08mljJZ5uLq6K1zhopKcJCIUL62hg/s850/ssis-lab12-kb-17.png)

### 4. 手動加入[定義域值]（Domain Value）

接下來我們要來編輯這些定義域的相關內容。

首先我們在 City 中手動加入一個定義域值，當它的定義域值是 **Muenchen** 時，要更正為 **München**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjGwsNgCPFKgUCrmJXheI5QbLKR4-_pzx9CITqZQO1pj_FuZHDZwsAztz7pUsSAl9BzgOLI6_0JeGwL07rXZn6-zUrBDPJAtgA4g0UldKB7VtN_Dy6Jnms5splErTyQlSPsM8k7jjKwiec/s850/ssis-lab12-kb-18.png)

設定完成後，資料會按字母重新排列順序。你可以找一下 München 這個定義域值，可以發現它共有二個同義字。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmA8VKt7iXsEivCmq8BiaSgydMrOUEnJeE7-7HHp6hC2nRSZ5JbbHkbASsSSRj48_Ne_Gfdx7dlLllyB4ayywOsBhNOb67QHOk_s7qUwzZTeCBLiyhfD0VBRhJKDDLPrebmBCqmvbyH1E/s0/ssis-lab12-kb-18-2.png)

### 5. 設定[以詞彙為主的關聯]（Term-Based Relations）

在 StreeAddress 定義域中，我們加入一個[以詞彙為主的關聯]。

[以詞彙為主]的意思是說，以詞彙做為更正的單位，不像上一步驟，是以整個定義域值更正。

例如下例中，我們設定將地址欄位中有出現「**Ct.**」一詞的地方，都更正為「**Court**」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhj-ca4hwIPdJzgjWahFZGciaU-OpPJmJS4xGETQW5TpL0qIXEy8qFMMBcOAEzrA2vC22ZDtZPebveMHHHzBrQbwr92h7cgz3WeOX-akeyP3xEUiR2ABe_5hjpB7fjvBjKtPMnWTRPQRoU/s850/ssis-lab12-kb-19.png)

### 6. 設定[定義域規則]（Domain Rule）

我們使用[定義域規則]來設定 BirthDate ，限定該定義域值必須大於 1/1/1900 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMMnoJEHas5eUBntvQIhCIhHSZFNF86x5DI7JL-TfauwLQLZiJuASaMrygJGvaD01OG7f6diht8kMsbTxFImm-KhMBbqQqDETGLJ9GjyMUNehR__iH8zJDp9N-XCsukzTVQbkUpNHox0I/s850/ssis-lab12-kb-20.png)

### 7. 使用[拼字檢查]和[語法錯誤演算法]

在 Occupation 定義域屬性中，我們啟用[拼字檢查]和[語法錯誤演算法]，來協助我們判斷 Occupation 資料中是否有併字錯誤或語法錯誤。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaIsNLyMbFPZV5QTlYcX8uc3TgphExe9Fl5SUC5heoULB9iFe7jw1pHahdUO3vsnqD-sQ3eKS01l2e58DHPnm8lFpKm7ZVZ7ZPLxXRQsWiZx8tA44W11qcE7QWwV8BpbH5idmN8CT6g2o/s850/ssis-lab12-kb-21.png)

### 8. 設定 EmailAddress 的[定義域規則]

在 EmailAddress 定義域中，我們加入一個[定義域規則]。  並且在其建置規則中使用[值符合規則運算式]（Value Matches Regular Expression）選項，並輸入運算式「\p{L}+\d\d@ADVENTURE-WORKS\.COM」。  然後再點繫[Add a New Condition]，加入另外一個條件，並輸入運算式「\p{L}+\d@ADVENTURE-WORKS\.COM」。  並使用 OR 運算元。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1rWg_Ll5Z4eSwsjCY8yCKj1fgqqCkMC_qA5Od4bEpt5JQoiNaHwUJNEZorordVcZrSmgXuQS1TKwuoRbNpkhfbhvJviiFVK4seTDyZbvSNi5seGriVHW-Ob2YsfiCjbgzv3bnwxMEorc/s850/ssis-lab12-kb-22.png)

### 9. 發行知識庫

最後按下［完成］，就可以將知識庫發行了。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrQOZmYxNNvF7KS6fT6km2l2BotHMvGkvp0VfE1KK_fApQvFTlNZabrO4Up4ughkAetCTk_ktl82lJHTJIMChMp1h3jhZvub9deLetctv8zzKoZvjj0xRmitRM77VeRL7LxPXSNXnF58Y/s0/ssis-lab12-kb-23.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkknCleb8EAhs2USjXJyrD5Iv4TRhIXMifnE3qRgF2aC9M4oP-C_p3L44dHrvVTucgne87AxD-4A_9Fnexbkw78Vg1DLYrwTLv4zpuhGi_hjueMizSLwUG0Z-VlaYPewiGz2mm4F2oflA/s0/ssis-lab12-kb-24.png)