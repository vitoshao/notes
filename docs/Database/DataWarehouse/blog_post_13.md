---
title: Lab13：資料清理
layout: default
parent: Labs
nav_order: 13
description: "Lab13：資料清理"
date: 2014-02-13
tags: [SQL,Labs]
postid: "2249920429824820526"
---
在 Lab12 中我們已經建立好知識庫，現在我們要建立一個「**DQS 專案**」，並使用該知識庫來進行資料清理的工作。  

除了 DQS 專案可以用來分析資料品質，另外，如果在 SSIS 封裝中，你也可以利用「 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 」來分析資料的**分佈狀況**，以判別是否有淺在的資料品質問題。  

# 如何使用 DQS Project 清理資料

## 0. 建立清理用的測試資料

在 DQS\_STAGING\_DATA 資料庫中，我們建立一個檢視，來取得一個要被清理的客戶資料清單，  在這個 View 中，我們另外加入了二筆含有不正確資訊的資料，以便利用知識庫來進行驗證。  
```sql
USE DQS_STAGING_DATA;
GO

CREATE VIEW dbo.vCustomersDirty
AS
SELECT 
C.CustomerKey,
C.FirstName + ' ' + c.LastName AS FullName,
C.AddressLine1 AS StreetAddress,
G.City, G.StateProvinceName AS StateProvince,
G.EnglishCountryRegionName AS CountryRegion,
C.EmailAddress,
C.BirthDate,
C.EnglishOccupation AS Occupation
FROM AdventureWorksDW2012.dbo.DimCustomer AS C
INNER JOIN AdventureWorksDW2012.dbo.DimGeography AS G ON C.GeographyKey = G.GeographyKey
WHERE C.CustomerKey % 10 = 0

UNION

SELECT 
-11000,
N'Jon Yang',
N'3761 N. 14th St',
N'Munich',		-- wrong city
N'Kingsland',	-- wrong state
N'Austria',		-- wrong country
N'jon24#adventure-works.com', -- wrong email
'18900224',		-- wrong birth date
'Profesional'	-- wrong occupation

UNION

SELECT -11100,
N'Jacquelyn Suarez',
N'7800 Corrinne Ct.', -- wrong term
N'Muenchen',		-- another wrong city
N'Queensland',
N'Australia',
N'jacquelyn20@adventure-works.com',
'19680206',
'Professional';
```

## 1. 新增 DQS 專案

新增一個 DQS 專案，並設定選用前一個Lab所建立的[知識庫]，並將[活動]設定為「清理」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqjFuFU_VAiNguGXlwMDLp6BE5ycCTnsylr7lariut-SpTXu0CZpyiQCI4xiOMnvnZyXZjEaG_t-zy5jkeA8pUdYbBTddvZK0TYNB6Z3sCH0tmEkkyfyijsjSFjForFImJLjJOUnfR6sI/s850/ssis-lab13-dqscln-01.png)

## 2. 清理資料設定

這個步驟中，主要有二部份要設定：一是設定要被清理的來源資料庫，另一是設定要清理的資料行與其相對應的定義域。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibhf005Por-5dvtOwM4y7yQFwhWc7xEf0g7WCg3D-ubADOpjN9Oe7Y9xgIM1EdP6r8D1x_c00PfQcUoVbfrQKWNZzi85eq-I40g4CFzFNaFJMLBjpwY9Cvufwf-Do928SGPix2PWdZ9-8/s850/ssis-lab13-dqscln-02.png)

## 3. 啟動清理

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggagyK7mBRLtB-sbdb8NAVPIr6Dzzpak8SSIIZfCc1fFFMCIDZpEfww_jNIESXjK7iLkgoT-uCRs4p2cOzbk8ZHQVtdIuFdLNQp_2kpV0gDA03i88XlqoW-LTJQoMLysm2Ejsf6aSyFbY/s850/ssis-lab13-dqscln-03.png)

清理結束畫面：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwnEI-plKEdAYJKINSxhOxQt7HKsrAP1IvkOOby6nHxSFT8EldN8Pi7vPzrOjA1hbB5LxQ6FYsue57o6CAbT_xFj94ER_zWYR_swvcKnULypZyIcV2H3DPWRuK3z18sEkVBfAW994FKbA/s850/ssis-lab13-dqscln-04.png)

## 4. 管理和檢視結果

管理和檢視結果中包含五個索引標籤：

- 建議（Suggesion）：該值的信賴等級高於**自動建議臨界值**，但低於**自動更正臨界值**。
- 新增（New）：DQS 沒有足夠的資訊，因此無法對應至其他任何索引標籤。
- 無效（Invalid）：不符合定義域規則的值。
- 更正（Corrected）：該值的信賴等級高於**自動更正臨界值**，所以在自動清理程序期間會自動更正定義域值。
- 正確（Correct）：顯示已發現正確的定義域值。

底下分別就各個定義域來看看這個例子的清理結果：

### BirthDate

在 BirthDate 定義域中，發現有1662個值，其中1661是符合定義域的資料。  不過這些數值都不在知識庫中，所以全部列在[新增]頁籤中。  你可以按下[**Approve All Terms**]，將所有項目全部核准。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHVv5pmQ94mEIvb0POi1Ak0IlOx74yxyXMjhyphenhyphenOoqi2ma6WI-uzLUsP21qQ3dxgPuh1AAZKssdvbCHqGRs6TJuWmQVrCumn7E1abdQSGgmnFBweGi4VZxlJ5GwoOYI1T9ptJK3yyJ_6Kys/s850/ssis-lab13-dqscln-07.png)

另外一個是無效值。（invalid value）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8rfiZkTUsn01fhNW4wzg15lWxifdCMRtzSrjrjpiLj-G5x9fkENkvg8iBOrPFDrvcad7alsQodibFSvikTPXOdvgiYnZoY71y89Hy9s673rpgztzmZFbEN_iNKR6NvDEMVgm8U1Q8GqY/s850/ssis-lab13-dqscln-05.png)

你可以在 Correct To 欄位中輸入正確的值。然後按下［核准］，該項目就會放到正確

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDNAZOU0h-c8fJQAiTLXPS-GUyI5uEBT8NPq6NnSeMJiEFiCPcxOyAEFbjO6FOohHir0EuBPoEwURJ27eiP_YRSt8jIFEZontzfr0c0Qz2phQjZS70LaR4c1lUkWLBa59q1AnZUXxHf-c/s850/ssis-lab13-dqscln-06.png)

最後所有資料就重新歸在[正確]頁籤中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVLaorbQ9Ai6txipkaaOxLWkDAJB4psaemrLasmlN9NQ9zVBWqDFLhAnpPbqCk0cGun_WUHSA3rVCMgQc4e8G_X30mUVrRzDJvodriRJSPKOoSQmdy9UxQZtSsGCLU12m57nMRJOBdND4/s850/ssis-lab13-dqscln-08.png)

### City

在 City 定義域中發現 2 個值應該要［更正］，且與定義域值達 100% 信賴度。而其他的值都已經在 KB 中，所以 DQS 將他們標示為［Correct］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhScYawVK4P7eLMdzE8Kv1f49K7fuWNVR-RoTLB2luztanbMXZIDWTEa3ZeJHfkxoEDfK9-9YFKFzOU5e0HgJVPXiqrw1FP3rH0JNoeLC8MdYA3xoy7u7wuekxG1C7ZQHia1rWzdDqTzY4/s850/ssis-lab13-dqscln-13.png)

### StreetAddress

在 StreetAddress 定義域中，我們有定義了以詞彙為主的關聯，希望找出「Ct.」並取代成「Court」。  而且在我們的來源資料庫中，的確也包含這樣子的資料，但是 DQS 專案為什麼沒有找出來放到［更正］頁籤中呢？  這是因為這種更新不是整個值更新，而只有取代部份詞彙，所以它還是被歸在［新增］頁籤中。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxqC_F8KVFc-Yoed9zSUgIzV3XPPNT48G17YJo591Zt0VnE8u8kRrIOkQVVXaEY2B5nNco3y72Cbyv-Vjkt7BP3jaRtgqTUMjNnsEMOZG-VEyXxGCSNxz9WFIJ9K3CbhFRQMHE-_FtjTI/s850/ssis-lab13-dqscln-09-2.png)

你可以在搜尋欄位輸入7800，就可以看到「7800 Corrinne Ct.」已被更正為「7800 Corrinne Court」，且信賴度為 100% 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1Al-nS6c7igJjy1HhYhbZ2o5W7WPPsy64aPlPQ8IkKNm6keWFzn4rU3adlkgGIFF0XaO8imPjUE-cCRRpLVSTK4xDmMy_l1lp1RZtJ4V2Jocx_CtiRS4xuRdNtHYk3QOY1qATZ3chmmo/s0/ssis-lab13-dqscln-09.png)

### State

先前的Lab中，我們已針對 vCountryStateCity 進行資料探索，得到一些定義域值。  現在這個清理程序中，總共發現 35 個定義域值，其中 1 個是新的。  

「Kingsland」這個值並沒有不符合任何規則，和其他值也沒有相似度，所以就被歸類在［新增］。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQvRS6jjOzsPU0PHhA13mb2ypXX6IZf66qSGNP4Li6VoCtWULsyO-IOpsSFfi7yJxuZfwL29bFa5VOXaMSiNIII9LI9FdjlkEiAIP9W82nOegwpCPVi0TPnHIm2NllYWssMT0YGoImOSU/s850/ssis-lab13-dqscln-10.png)

### Country

在 Country 定義域中發現一個［建議］值。  「Austria」這個值與已知的值「Australia」有 70% 信賴度，所以所以就被歸類在［建議］。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcf2Wq5P7ewd9fhOlDCWcXaXtJrpIf9xl49bGEvfv6eNVnSSN4tBr9lAKZThztUCjFDDbzk04cpkOOjx5exgZJwpttbDvm-rgacNz43Fkck2mMp8tItOfoLBbQQhu37cu7vu2syLYX5xk/s850/ssis-lab13-dqscln-11.png)

你可以在該筆資料列中點選「Approve」功能來核准單筆資料，核准後就改歸到［更正］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5ToBvlFdR1lxbNd-fA_WjvOYJXrTogjcbIDyqpfeGCL-x1UFudR3dubTHz360ao8CD44Z0mX5xJE5Rj5c-LbrGMqc2KU7gFPDPJ7pc2lA0Riov2nE9A0ynm-pWB8T-F2Zehtywa96iAc/s0/ssis-lab13-dqscln-12.png)

### EmailAddress

在 EmailAddress 定義域中發現一個無效值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5vYIcokjppbkvdRSwVSpio0j6moRIQ8yY-MUEfA8aXfM-C3kszAFieXg_zT6cgfD2_1EpzPzIf4WHbt48mUjpIhyq4Pq5_-swnNWQxZPC-F0tvEarpgUcBrkapb1v2uju31_pffH89P0/s850/ssis-lab13-dqscln-14.png)

### Occupation

在 Occupation 定義域中發現所有值都是新的。  不過，在新增頁籤中，你可以發現「Profesional」有紅色變曲底線，這是因為該定義域有使用拼字檢查功能。  在［Correct To］中輸入「Professional」，此時，因為是手動更正資料，所以信賴度會設定成 100% 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgM8yk6AqbviVJnx8ySWXvIXlbJanWryK8hNUN7nSiaI78ep3qD0mEn47gF2UXzXUrAFsNacrjcSxEkMfzFpcgOtVJwVwNgAkkf92V4lEi_zbC9cpuZ-d9A-Ns6sFqqvxpMAqonv5gMAiM/s0/ssis-lab13-dqscln-15.png)

按下［Approve］後，該筆資料就會改到［更正］頁籤中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgb8I0x7MHdDPA98jp-McW17HXY-nSEGS1jICfFJpIKEnPWzMoXyBj1twNTPncLjisGfACc7Z44Z0Z5Ykn-G8Kvsg45-5wuuMRVU2WHRnONrkEpunaoHxcRf_UmFD6_a2udmXaA6cxtW8E/s0/ssis-lab13-dqscln-16.png)

## 5. 匯出

完成以上步驟後，你可以先預覽輸出資料，再將資料匯出。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuPbsqv6CorlHvQO-KgGBEnHuJPAMP57PJVtlqtvgOQ1d2Rf8jLLj8nnd7POleTvGqpTkD9mE2R-_HEhiPCfmFCgVCUxuK-NWkuIebTZ_zYsdU6jgYNGN11Hpia0-nXVbOVleVSjGV77U/s850/ssis-lab13-dqscln-17.png)

下圖是匯出的資料，如不需匯出，執行完成即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjT7eyLq8eiXps0FZV5kVvZTyw8yPm2nIue_xOkkYC7fYyVlPs48OlyoprLLpRvOrcm6unmqhnQl44FtccAb2mnCUZ5_E3V3mNFKCIihViNfOZxzbwgeAtgrDyojwFpKPzMntew_yK-JDA/s0/ssis-lab13-dqscln-18.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmhcg4SgAJg4LBp49qntvckeCUUU8aIhyphenhyphenvI7HYXLGZkQNxboRuNo4fFygd7EGRD7RnJ_r7fyWd6zE8OsrLFhU9KP7z87NDr2QEDInbqDtu_Rjr9lYsVIclpslk1YbqP9p0Vtyu0zHcqBM/s850/ssis-lab13-dqscln-18-2.png)

# 使用 Data Profiling Task 分析資料

上面提到的「資料品質專案」，它會透過知識庫的內容來協助分析資料，以達到資料品質的目的。  其實你也可以使用任何 SQL 所提供工具程式（如 T-SQL 查詢）來分析資料，進而改善資料品質。  或者在 SSIS 封裝中，使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 來分析資料。  下面練習就是示範如何使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 來分析資料？  

## 如何使用「資料分析工作（ Data Profiling Task ）」分析資料

這個練習示範如何使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 找出前一個練習 vCustomersDirty 檢視中不正確的資料。

### 建立 SSIS 封裝專案

建立一個 SSIS 專案，並在封裝中加入一個 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4A_dPGgN2kFPUoMnbxTW4T7Pb5Xfy_Ma4nrQAXRYo3pEgzRXUtwzL5KSR_6TMl30tFU3VgEOajPBXId6iLTpXyLGoCehoGka0FqQYAsQ8xpqbz5CO0Ti8vyNJd8VeRvV1xbBnzhV3JXo/s0/ssis-lab13-dqscln-19.png)

### 設定 Data Profiling Task 

在 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 的編輯器中的[一般]頁籤中，將目的地的[目的類型]設為[FileConnection]，  並輸入一個檔名，用來儲存輸出；同時將 OverwriteDestination 設為 True, 讓 package 重複執行時，可以自動覆寫輸出檔。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3j7TdvW63LJBEdZrJK312nGEPGN9NPKn-UHvbjVL0iIUOAZi7Tgm2hyphenhyphensB4YZbVcAGVOsx8LBTMb2rW68Ng5_5AWKcNno1h85vq92N_HN5p7ec3acbpT3Vq2Hgw8Kgof9OUmS6mmzLBLA/s0/ssis-lab13-dqscln-21.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTYKFJQduNbS7mw3jNinxMinls-1tbf75DZf-ScW2ODLAXjCAPddN6Vk2CMh5ZeCLDZ7iFokB4CmGdiJSWFDz8d8bZiQ_L9UsTbPd9DY0O6tGy7ujgt2xF7xzUp8QcFlAS2H5bGzkCMXQ/s0/ssis-lab13-dqscln-20.png)

### 設定[分析類別]（Profile Type）

點繫右下角的［快速分析］按鈕，設定一個新的 ADO.NET 連線，連接到 DQS\_STAGING\_DATA 資料庫，並選擇 vCustomersDirty 檢視表。如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdvMJPOYGuCeWLWVkANZQGTk3IrdxdAk74sTGLyu1e-_4a5E85S5Tj-U5R-ukTmtWgtpiRTYS56AOpvs2Jz91R3scPRiHsqpnGm-csN0SH29BnH7a2zTodh6BQbjTeogzOmP5yZCjzc2I/s0/ssis-lab13-dqscln-22.png)

設定好之後，你可以在[設定檔要求]頁籤中看到以下內容：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJtJkcbZAnz1HDnKcIjPbbWdiXwDrNzA6_95Es1Nze0E_hSTgP7lUZtJZZA3hrdsRlyaFgc3onQHbi9X-yfa6nPK03G1oRhIJfgaWjEcWZQP6ikmfZP0Kae5kP8F9rnd8l0fi4hSisrWo/s0/ssis-lab13-dqscln-23.png)

### 設定分析檔[要求屬性]（Profile Requests）

依下表，設定各個分析檔的要求屬性：

- 將 Column Value Distribution Profile Request 的 Column 屬性，由 (\*) 變更成 Occupation （也就是只分析這個欄位），同時將 ValueDistributionOption 屬性變更成 AllValues   
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjY6A-uit2E180MkZMYHHB6cU0ze7TPJCo8a3KSpP1-U1BiJKJ3GsaRSut_HH_htQsRanqZrPE6wfD7J-Kihk7zuwtLucwUomfzlG6hoefiprp87aYWNK3exnjh1wGZSCVCKlpWbRaUcCM/s0/ssis-lab13-dqscln-24.png)
- 將 Column Pattern Profile Request 的 Column 屬性，由 (\*) 變更成 EmailAddress 。
-

### 執行工作

執行工作，結束後檢查看看是否有產生輸出檔。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTB6BAL7aoPRLXCxxjCyaaXUEzJXLwmiKFo4_n2ULhe9ZNExiYeAjvIue3hyphenhyphenw9bQH17JNiZ8E9MeVGJazBQVl2rr7dj1UIy_h1etSqWJLvmgvZWGtEPMKqPnB6539SWEOFD30aiwsPClA/s0/ssis-lab13-dqscln-30.png)

## 如何使用「資料分析檔檢視器」檢視結果（Data Profile Viewer）

接下來，你可以使用「資料分析檔檢視器」（Data Profile Viewer）來檢視上面例子中 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 所產生的檢查結果。  

### 開啟「資料分析檔檢視器」

你可以在功能選單中，或者 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 的**編輯器視窗**中找到這個工具。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCogxaaYVXeArp1rxYtIostKPqNfuvY_qxfiy2Pd0MLZqfNfXktRxl-rxqjbJGnnyT7sPwrn5kYaAoUF-XffcHq4tYb_3IBIuwcKNunAdLIc6yrsIwydKF4SlwsqAjl-N3LonW_OTMUk0/s0/ssis-lab13-dqscln-26.png)

### 檢視 Occupation

「資料行值散發設定檔」會報告選取之資料行中的所有相異值，以及該資料表中每個值所代表之資料列的百分比。  你可以依下圖中的步驟操作，以檢視這個報告。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFOYWpOJsXfl6j72JgxU6W7FNqg35aII6S-JUjgP4amueydYIptsy1kFqq_zuiNuGynea20FR_d58yxmRh2C2mBObbLUzjdsgWQEk4CheLTQvazfGy6Z2Qbcc9DMIl2TogaJK5TBTXzck/s0/ssis-lab13-dqscln-27.png)

這個「資料行值散發設定檔」的報告中，顯示了 Occupation 共有 6 個相異值，其中 5 個值都被大量使用，但是 Profesional 這個值卻只被使用一次。  像 Profesional 這種特別奇怪的分怖狀況，是異常資料的機會就比較大。

### 檢視 EmailAddress

資料行模式設定檔會報告一組規則運算式，其中涵蓋了字串資料行中值的指定百分比。  你可以依下圖中的步驟操作，以檢視這個報告。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFOYWpOJsXfl6j72JgxU6W7FNqg35aII6S-JUjgP4amueydYIptsy1kFqq_zuiNuGynea20FR_d58yxmRh2C2mBObbLUzjdsgWQEk4CheLTQvazfGy6Z2Qbcc9DMIl2TogaJK5TBTXzck/s0/ssis-lab13-dqscln-27.png)

這個「資料行模式設定檔」的報告中，顯示了 EmailAddress 欄位共涵蓋了 2 個正規表示式。
## 參考資料  

- [管理資料品質專案](http://technet.microsoft.com/zh-tw/library/hh510417.aspx)
- [資料分析工作的設定](http://technet.microsoft.com/zh-tw/library/bb895322.aspx)
- [使用 DQS (內部) 知識清理資料](http://technet.microsoft.com/zh-tw/library/hh213061.aspx)