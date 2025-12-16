---
title: Lab11：主要資料維護
layout: default
parent: Labs
nav_order: 11
description: "Lab11：主要資料維護"
date: 2014-02-11
tags: [SQL,Labs]
postid: "82995993119911945"
---
這個練習將學習如何建立 MDS 部署封裝；以及如何使用「匯入」來擴展實體。

# 建立 MDS 部署封裝

1. 你可以在「主資料管理員」中，執行[部署]功能，以開啟「模型部署精靈」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSrHMxp3jSSH7yLFJH9Ml9W-fnbSpRM_e6R6j-y9_QQgX5wLq25EHXmNbBX2KOIUG8YCPr-9N-feA2ejCe7A7Mv8t8bvrwTXS_JaYv9sYT_uEKYMgnMTADR-O3MCF_S3wIKsR0g51Z2MY/s0/ssis-mdsmgr-01.png)

2. 使用「建立」功能，將前一個練習所建立的模型製作成「模型部署封裝」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxeEGzjR9Lf2dZRuZKfj8dsMARzdgEzrkZgQTHth10obFQdccYKdOwPjKRHbwasKHXUmN-dc2W88fW8_Sbjz_iC7LAov03jAun11tW_Po_CsUPhqzXzfr-llaJeQKLgwE5I7zJuOJcxug/s0/ssis-mdsmgr-02.png)  

3. 刪除前一個練習所建立的模型。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEieFHafd2lXpMdMU26sG-w6TGT0AWTNsKn0YXm5qbS8Mvuun2u9GW4YAUNHaCR7iryww0cnpPVn-rAE5IH3aSUEEW-wIMLVdap-1TZCjHxWZRM4nVPAPyMmM3oDul3eXLII45SYo7k0R9k/s0/ssis-mdsmgr-03.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN_F3yXFHMnjBCnMUmQp12TzD4B10YxxQU7Sb37r3Z-ju2MYK9hM6b0ZFFhfS6m1Xlt2_VOf7u786k7cMPGNBjN40Nu0LZP48CzCRc5H0_yCOl7h2oc7MNvi1-MR-XDN69YI9yVbKNlqM/s0/ssis-mdsmgr-04.png)  

4. 使用「部署」功能，重新載入先前建立的 MDS 模型部署封裝。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMII9qq1OSc_yDOdGM6sv0Njkl5mzvMvZD6TTyV_Yx4bJ_aAilnAyFjxvVDXIs_XZtg9YpwtFY2M7hq3Eit2b6hqJBeg1-YpIWQzv0dcXzEvXcSKIKV9uJROV0LNxNNUb4mL_BEWgpEds/s0/ssis-mdsmgr-05.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivz5RFL5FwFGWjNW3n83JxfWEnt7z7mrdm-67mnnOOTrZWRxJuA_KQ7cYrErodU-lPnBBWj-VABDGwbV8G83mssCT0BukbQ3pqqzC2FD3LmHa6z9UjJqsjXMgnxxV7OYjLNTKJGSfd3ck/s0/ssis-mdsmgr-06.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgRIztTaGIIHhIfMQduGMCrxQIkWAytYSxr3J03oyh2j0liNJqqyZSrXUCIKUm4qlM9FEcOfFenbNjNfZ6lJet5cdv9CyUAGqTUN2spUkptUo6pGcTP09UCYpTzl_RSNTA6ZqyoArSLuiI/s0/ssis-mdsmgr-07.png)

# 「匯入資料」與建立「訂閱檢視」（Subscription Views）

底下練習將學習如何使用「匯入」來擴展實體。

## 實體的 staging table 

不管是 Lab10 中所建立的 MDS 模型，或者上面練習中，使用部署方式載入的 MDS 模型，它都會在 MDS 資料庫中建立以下資料表。  這些都是與實體相關的 staging table 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhy5AIuP_IsdFaJyjWWUxT3gJ2Kk9FveXr1r6arGNkXDgUafKmlqxVf2wKPxDv10LjllCY_Mc4L6lZUHWPDcTlTtWlCMkybKsyid7L86H2hylMvm8ZYFQl7urV639vDIlixVoJI9fK9MrA/s0/ssis-mdsmgr-08.png)

## 使用匯入擴展 CountryRegion 實體

### 1. 擴展資料

在 SSMS 中，使用以下 TSQL ，將 AdventureWorksDW2012.dbo.DimCustomer 中的資料擴展到 MyMDS.stg.CountryRegion\_Leaf 資料表.
```sql
INSERT INTO stg.CountryRegion_Leaf (ImportType, ImportStatus_ID, BatchTag, Name)

SELECT DISTINCT 1, 0, N'CountryRegionLeaf_Batch00001', G.EnglishCountryRegionName
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G ON C.GeographyKey = G.GeographyKey;
```

### 2. 啟動暫存處理序(Staging Process)

進入主資料管理員的[整合管理]功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-5QRTM6_MOyyA4h5vl12VM87H8XBmgByZyQnb3gxRdpokCjk3j6EPHpSAVuYwf7OouAtgejr-4sLF35GGf90C1tEPamAGnkoUcQZof-ks0dELYTnePM-Y7CpCbbVJsgbePJAcK9pQoE4/s0/ssis-mdsmgr-09.png)

確認左上角的下拉選單，是否已選擇 ModelCustomer 模型。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYZd2MnEVQc0N0ufkwWHDFtUmwpcQ49qIni_Mymz8We7mjq7AMdU5flCMtfAo97FcB2pNAoPMnxO3AO-ZCbCgTrsLLptKfnF77y-s1nd6rxXeoYeGoNffq8htpp7z2vGHdMLDtnnNovG4/s0/ssis-mdsmgr-10.png)

點繫啟動批次(Start Batches)，並確認彈出視窗中的版本是否為 VERSION\_1。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbwOWZK2ria9vEqJOuQ9YhxIoBc_OJwBrwWwyWoMyqNjY4N8QDPGbKkwrLiiXqxbYfbhlaJhESEbtCi1XmRbjh6KcV2OCIfB9kVE9S7Em4d6TUWAF0mZ_rhQ8GKlXhurdKTc2RVgpi8kI/s0/ssis-mdsmgr-11.png)

這個動作會將轉換作業送到佇列，等執行完畢，其狀態會由[QueueToRun]變更成[Completed]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1ZElR3k9PLsxDNf5fOjDLzaX5sE7V-mA57D1Njiy8nmwJrXS0HCZzhjmTa3z1CPPGFIPZ0gohfL5pghLS1RYjOlqBT-79Wac-o79ahiQHw44p9HBq6xwjrl5sFspfIAWSNmFLc-sKnbQ/s0/ssis-mdsmgr-12.png)

### 3. 套用規則

完成後，你可以在[總管]功能中看到 CountryRegion 實體總共匯入了以下６筆成員資料。

而且這些資料的代碼部份都是由系統自動指定，不過所有成員的資料都尚未被驗證。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOy1Ll6Rb1o-IHmLYW5w-YSu5Q6N-_EOWV45gFBx0hSgVRb5S2hqAHPR_4WseeKdBQcXV1aiasxuE43cpDgY0HIZWbM72k-0SPU-ETEpdrDLJ-MSvpXkdxiymsghWfuz48wyrYZwKcQSw/s0/ssis-mdsmgr-13.png)

執行 Apply To All 以對屬性進行驗證。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4IXzv0AvoJhZzqtc08nlV0CC1eS7BLXPVz_vgqSDY2nkQMRqO1HNBUPeuS7MB74SZrc9kTEanQGmEcyamQ7b7ti2eArGWKuYYrT5kKEmgz0cwe1ifgdhFkLYl0oMxgsg_r3CGgxym1uk/s0/ssis-mdsmgr-14.png)

驗證成功。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfI_0X5-G59VruO-z7cdSaYe-ocAuAL4cKUrB3wxMQh8adqkalomIt5unHYtPwN5SJ9GBtdlV2NPG33GHEe5qqlYcQKHT9PEbjc_Hc6ibYew1gbCTRxv9SaoxFptsIjTplCMGHGG-Ps4o/s0/ssis-mdsmgr-15.png)

## 使用匯入擴展 StateProvince 實體

### 1. 建立 CountryRegion 的訂閱檢視

上面我們已經擴展了 CountryRegion ，且由系統幫我們產生其代碼。  現在我們要擴展 StateProvince 實體，不過 StateProvince 實體必須參考 CountryRegion 實體的代碼，  所以在擴展 StateProvince 實體前必須先在 CountryRegion 實體中建立一個[訂閱檢視]。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiurVFktLz6_Q5e6JdubvzKAZmYBatmb7tPxNtXPgTCbEA6m6iqDgxsrOxqYAY6ky-Lc7PqjqBYYp74kuDa1HDbyISYFlulk83QPY_snd_GF1M4WB93wAktV4rTZXg2X4kBvvt5WrmTGzo/s0/ssis-mdsmgr-16.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqyBjtdBDze5YSRRowgnuDBS1JDdd5hrEDRKiYm1hbDzU-bR09wKOpXC6bHmJqytYEyLNdMDmSPa4XtUiwOxKwPxc9tr8CuhmCnQRVHyBETIFtBtoTWcAdvk6qxQj7CTdaL4rdDHSoG4U/s0/ssis-mdsmgr-17.png)

在 ModelCustomer 模型的 CountryRegion 實體中建立一個檢視，取名為 CountryRegion ，並將其格式設定為分葉成員，版本為 VERSION\_1 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEju7pIgDiXPwUyaHyPemS-6DWmACyCQtVKCfJQ4OiR3MQLqSxE_ueUsRXeg8he-JvDJnzMukNlrVgQo6OMmam6nZkZ53aOjSJ4XVDKaAB80H7mjS4g88c0N6U0ZLETCw3o1Hh7V2M1FHNM/s850/ssis-mdsmgr-18.png)

儲存後，你可以在 MyMDS 資料庫中找到這個檢視。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnaZu28VSvHv2h8HtleskYRpSoL-qHv0PkULgOvNa0hGmhmMdAkUpwb-F9KNv7SpPOKDy89uelEIbm9drvReQRFFheCpBDSPNPPh7zEzmeVxlzl5OCsxOB30fO0Sq7NGGyDCg5dTVwyW8/s0/ssis-mdsmgr-19.png)

### 2. 擴展資料

在 SSMS 中，使用以下 TSQL ，將資料擴展到 MyMDS.stg.StateProvince\_Leaf 資料表.
```sql
INSERT INTO stg.StateProvince_Leaf (ImportType, ImportStatus_ID, BatchTag, Name, CountryRegion)

SELECT DISTINCT 1, 0, N'StateProvinceLeaf_Batch00001', G.StateProvinceName, CR.Code 
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G ON C.GeographyKey = G.GeographyKey
INNER JOIN mdm.CountryRegion AS CR ON G.EnglishCountryRegionName = CR.Name 
```

PS.如果 AdventureWorksDW2012.dbo.DimGeography 和 mdm.CountryRegion 的定序不同，可以使用以下轉換：
```sql
INSERT INTO stg.StateProvince_Leaf (ImportType, ImportStatus_ID, BatchTag, Name, CountryRegion)

SELECT DISTINCT 1, 0, N'StateProvinceLeaf_Batch00001', G.StateProvinceName, CR.Code 
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G ON C.GeographyKey = G.GeographyKey
INNER JOIN mdm.CountryRegion AS CR ON G.EnglishCountryRegionName = CR.Name COLLATE Chinese_Taiwan_Stroke_CS_AS
```

### 3. 啟動暫存處理序

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOOlip5qtDkYx-Vdb3Uulaec6Uzoa7Y5K8KLmYzdWHaEXCPINPP9J0IXivFEF8wHGWicsUjWs7mPrE6Wz0Htnmyi5ESQw3FxduZA4_9SajlOtGphPuL6BTZH1NiperrNYvXF0WF8C9JzY/s850/ssis-mdsmgr-20.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimdqHyDFzOtbTjvdAjfH6_ja3qv_AeIg45n3u9DRdwLHjsM7gqqheyqF2aUsD_2UrxjlTl30lLcFwe1YiwwYiuD5lRqNDXZFNFzYUI3t6FAxFS4Y6Wdbc61JyfkUCZO4ESasw5QCT-svk/s0/ssis-mdsmgr-21.png)

### 4. 套用規則

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOOlip5qtDkYx-Vdb3Uulaec6Uzoa7Y5K8KLmYzdWHaEXCPINPP9J0IXivFEF8wHGWicsUjWs7mPrE6Wz0Htnmyi5ESQw3FxduZA4_9SajlOtGphPuL6BTZH1NiperrNYvXF0WF8C9JzY/s850/ssis-mdsmgr-20.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimdqHyDFzOtbTjvdAjfH6_ja3qv_AeIg45n3u9DRdwLHjsM7gqqheyqF2aUsD_2UrxjlTl30lLcFwe1YiwwYiuD5lRqNDXZFNFzYUI3t6FAxFS4Y6Wdbc61JyfkUCZO4ESasw5QCT-svk/s0/ssis-mdsmgr-21.png)

### 5. 建立 StateProvince 的訂閱檢視

由於下一步中的 ModelCustomer 實體也會參考到 StateProvince 中的成員，所以也必須先在 StateProvince 實體中建立一個[訂閱檢視]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpUOT_wba0tnLGAS3_6AXspzRbXqCX217baRSIFtPrzjIGOGiFXC3I9GpVaBIymEFqbsIpu71dcTYPo9RxHdQxUToZ1pdErF_CKudlFTTVH6IHp8PujbCxh_4TJuppsCUoDeTpgjM3wMg/s0/ssis-mdsmgr-22.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg6KP85mItGyXrD8T-FB1ZZCSYUYcELzhjDHvnrgPNv1WJvDArciry9ZBufzf_H2Rm9jCOaEbIYtCTKbgsd4A3n0MygIFJ3Dl9F6qqH53Eiv3WiZWBtWII5ivnVDHewSq0z63G-PlNGfvI/s0/ssis-mdsmgr-23.png)

## 使用匯入擴展 ModelCustomer 實體

匯入
```sql
INSERT INTO stg.ModelCustomer_Leaf
(ImportType, ImportStatus_ID, BatchTag, Code, Name, StateProvince, StreetAddress,
City, EmailAddress, MaritalStatus,BirthDate, YearlyIncome)

SELECT 1, 0, N'ModelCustomer_Batch00001',
C.CustomerKey, C.FirstName + ' ' + c.LastName AS Name, SP.Code, C.AddressLine1 AS StreetAddress,
G.City, C.EmailAddress, C.MaritalStatus, C.BirthDate, C.YearlyIncome
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G ON C.GeographyKey = G.GeographyKey 
INNER JOIN mdm.StateProvince AS SP ON G.StateProvinceName = SP.Name COLLATE Chinese_Taiwan_Stroke_CS_AS
```

暫存處理

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5I5pENwCBc4hUFMHS5xEia9yq63Z3Y3QbtnAcSIeAPV3WdozRUxtqzFDFe6L0KbzEOnBpygD5N7cBAko6gDyPpLtQlGcoD3k5KjItlsRzTkn6vZ_GtccBsN8Kc8BuaDvMViQHzy1VJOg/s0/ssis-mdsmgr-24.png)

套用規則

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJJs7qMkD4s8blXovZjM5OKZbw9RR6hvcttHvETm_A_V0zroBvReVD9ZXU76VE3wuD6_gkt72xj20cpF6j6aVo0LnMH9GHRYuKfC_IG_xxCOPC0HodwTAiqTwIbggfi-dxyI-kBjR1Rlw/s0/ssis-mdsmgr-25.png)