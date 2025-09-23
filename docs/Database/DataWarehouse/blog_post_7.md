---
title: Lab7：緩時變維度
layout: default
parent: Labs
nav_order: 7
description: "Lab7：緩時變維度"
date: 2013-12-30
tags: [SQL,Labs]
postid: "9178210946238731470"
---
# 設計 SCD (Slowly Changing Dimension)

底下練習，我們將使用 [Slowly Changing Dimension Transformation](http://technet.microsoft.com/en-us/library/ms141715.aspx)來進行累加轉移資料，並搭配 Set-based Update 技巧，提升執行效率。  

## 使用「緩時變維度」元件

底下練習，我們要將 DimCustomers 維度資料表修改成符合 Type 2 SCD 。

### A. 修改維度資料表

1. 加入為了 Type 2 SCD 的驗證欄位
```sql
ALTER TABLE dbo.DimCustomers
ADD
ValidFrom DATE,
ValidTo DATE;
```

2. 刪除 DimCustomers 中所有資料
```sql
TRUNCATE TABLE dbo.DimCustomers;
```

### B. 加入「緩時變維度」元件

修改先前的範例，在流程中加入「緩時變維度」元件，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVKMecHwXIoA4pjyBMe2w1mcm77r8QP0YYk4s_KvO2641GL0KW5iqWROqN75O664kGvXxD-LcaMN-lZw4-2GAHeCIFxCxWUvLyEeyBLngETgdj2QPXg4FhuZzDpdiCOc4KgaPGzlMcMTw/s0/ssis-scd-transformation.png)

### C. 設定「緩時變維度」元件

「緩時變維度」元件是以精靈介面的方式進行設定，底下是幾個設定畫面。

C.1 定義 input 和 dimemsion table 的欄位對應，還有定義 key 值欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhsbSiB4IYWJed4OU7AqokLgp7W8JTEEyaw0TbtGqzIWvrsMw7_a4xaZ7UTdU0PFwRgDdLiQyIIat0R55W8eS1SCfuU9NKfWgt6JxvY0sUKFt5Q7HXlN1hj_ZxxuRXDoVddwG58wJaO-Gc/s0/ssis-lab7-01.png)

C.2 定義欄位變更類型

- Fixed attribute ：此欄位不該變更。
- Changing attribute ：此欄位若有變更直接update即可。
- Historical attribute ：此欄位若有變更必需記錄歷程。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBTqcirHbUbfmSu2UDl5OWVa5d7KlTW0-ro5yLMn7G0e7d6Ak7s-2mSoQqOe_hiKYy_L2cKQ_Yodlq1z1jve4riPKF2jDW5YvNNSykjyq_r2V3WlItN-K9TCCh7ZLlFxHjYmewIfLLo9w/s0/ssis-lab7-02.png)

C.3 設定「變更屬性」與「變更屬性」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiyO8CXKSSIBw_3Ba7m3MNxRFU-R5feQqpqJwFh9eRJVdbaXKmUgyHasRBx8vDGFNgCVjF_fralud8-FpnhXohyphenhyphen9HSqepMNmSfOQrnL7ZrpUux61ReIsY5S_wGCtiEpTkiSQ3OhnxdY4dY/s0/ssis-lab7-03.png)

C.4 設定「歷程記錄屬性」

如果要達到 Type2 支援，這個元件提供二種法：

- 使用一個識別欄位（如：CurrentFlag）來識別該筆資料是否為最新的值，或是過期的資料。
- 使用二個欄位 ValidFrom 和 ValidTo 來區分該筆資料是最新的值還是歷史資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1d5UReXAU4OsfaveWYB08XKaw9xTv9o0uauQlYxpyLRjOoHMnfAj6wR5mHCWq4gOWIaUsvdJHei5YYkMaX-2pgKXKKBg72JWKBXfixSWme9kCEP5bJberWPpyP0MWR-3i-HICpVaAsyw/s0/ssis-lab7-04.png)

C.5 設定「推斷成員」

此例不勾選。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrocyJ8VjytWkPL37LGsSt-FfI0ZQOhqs7fixp879yX17eTCDj9EUTp-XAg0d2tdtAeIsEaOy3UEQjBgWXzHqys9rI2S1gxsZ4rwVHVQaQjbyh_gtxBE2u3jZu9cZQWJwvtKyQ6ZA4SNQ/s0/ssis-lab7-05.png)

C.6 完成設定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTlWVI7TQaRQ7XF4PP9FcokY7XCg8EmDFv27eKJZ0y9BWoFNwIXC7sDx5immzw3vNQKfdSahzeGtStIEmpwGJd9RpCkf4rwC1u2bVzWI5DjnykIG7AMD5NHJdm75aPeyuQ4nHQpCHRcYQ/s0/ssis-lab7-06.png)

### D. 流程說明

下面步驟說明，將分別解說下圖中的標示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvdB6GcMCVl-5p9o8B1VrbZfUGDxbyzVKUxlM9t-JwnBKPqZlhFmlFxOauhdArMku_v5NOYisSFyKtHUPEw_OMT9sGs204fozKwsuzPaD3rO9u0fc0TGS3SEqfIgPPWCg8yt63gCkV0YU/s0/ssis-lab7-08.png)

D1. 處理 Type 1 SCD 的資料列

將「Changing Attributes Updates output」連接到「SQL Command」進行更新

「SQL Command」的 SQL Statement
```sql
UPDATE [dbo].[DimCustomers] 
SET 
[EmailAddress] = ?,
[FullName] = ? 
WHERE 
[CustomerKey] = ? AND [ValidTo] IS NULL
```

資料行對應

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjU5ZcEaIecM5QYA0dA3k5s6hgz4MwzAyL6nlIyLhqNW0LxatL-ROgxOOOVcsFlOnmW0s5VjySQfikN-IEIsIikIdklrzTVumtcU8E5MXtIKL1Q4ePAEzmnPo0XardQj4Q38UzhdcYcRtQ/s0/ssis-lab7-09.png)

D2. 處理 Type 2 SCD 的資料列

將「Historical Attributes Inserts output」連接到「衍生資料行」，在「衍生資料行」中加入一個 ValidTo 欄位，預設值設為系統時間。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwE7Wh1KcsB0Jk6N6OzPzq_ISS2SndOvFRCsZwcosjhCIXOBmcgtKk5I3XUXfEiFqPXKuq8edzmZh-OX4_-ppDaCIZhb05qIfzot4UDTyi6UJIqwd-5Ye3krWts0vB7FdgwwlLfyVNbp8/s0/ssis-lab7-10.png)

D3. 更新 ValidTo 欄位

將「衍生資料行」連接到「SQL Command」進行 ValidTo 欄位更新

「SQL Command」的 SQL Statement
```sql
UPDATE [dbo].[DimCustomers] 
SET 
[ValidTo] = ? 
WHERE 
[CustomerKey] = ? AND [ValidTo] IS NULL
```

資料行對應

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcrtxLlH3Lk2vjNo7G3tQ8HSpVT-9zES_9kQaBBKiV50FE31Djtj-tVrY2W5FkuJfOqJS9DPkx4XaDI_CAcMreE7a1tkI4zNIhoNhunW7Md0Ak-sSIWd8FrpfM6F1QS4n6Vjk7NDHWNNs/s0/ssis-lab7-11.png)

D4. 將新增資料列與 Type 2 SCD 的資料列 Union All 

D5. 加入「衍生資料行」，並新增一個 ValidFrom 欄位，預設值設為系統時間。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyG83KdNcKKhkB0IFPFBelKPzqBrZetfvFetqWxRxb4scSWje2Px3jQYAkWaeXjDKyMvK1OrAYnK_vBIEaoIx4YwZe8JOmPJ6ZcbQhfwQugpMnyZesS-HXiBOO_TDeScaV3_8Oc72S15A/s0/ssis-lab7-12.png)

D6. 加入「OLE DB 目的地」以進行資料新增。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPkh2Mhol611k_QF7EXh2S9_WeMl_1uJBUxFXOI8AEILyPsHvHdGef-CmhvNVvUPZa-iqhvWad8PdRVDUEBj3ROuvwQ-fbi0QNGMf_vd6NVfucGyuZrm6PCJIULaQlRJWYRfJ53TR-5aw/s0/ssis-lab7-13.png)

注意：

If you get an error, please change the data access mode on the destination adapter to "**Table/View - fast load**".   There is an issue, because we are using a sequencer to populate the surrogate keys and the SCD wizard sets the destination by default to an access mode that does not allow a trigger with a sequencer on the destination table.

### E. 執行封裝

上面程序完成後，第一次執行封裝，資料全部為新增。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMZ4LhrsMwwQ7jrytKLxMVgBMOoV8RlTXRI2FDrNVhTdKlDltILDDu1fWq4IivFx4i7Z6KV_T_CrOCHtYlL_Qg4XSL4K-fEi6bWjNpg1Y3UjujtmpVgIH1YZxnfGwsiRz1qHb-HC3LjNY/s0/ssis-lab7-14.png)

隨便找出其中三筆資料。
```sql
SELECT
SC.CustomerID,
C.CustomerDwKey,
C.CustomerKey,
C.FullName,
C.MaritalStatus,
C.Gender,
C.CountryRegion,
C.ValidFrom,
C.ValidTo
FROM dbo.DimCustomers C
INNER JOIN stg.Customer SC ON SC.PersonID=C.CustomerKey
WHERE SC.CustomerID IN (15001, 14996, 14997);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhM8cWjvhQkLfKpg7wzt_8GHkKU_zsxfOrnzr_P45NdJj9Edi5b0Fg6XpF4skAZpghqLWuLDCfL15-RDsEopYUQ21iQtwQEHXtQKdIUoQcrSZO-77U66iqlZmuuyxpS0DHUuvmNo38ZH7o/s0/ssis-lab7-15.png)

上面程序完成後，第二次執行封裝，因為來源資料都沒有變更，所以資料沒有更新。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiw-LFXAqf3V1ztv0NB2aQzCmwfhWLuDRRI5uGOaGjbCI43jNxlbcUj8l_swyIq8DW9h9HSBsi0atM93iycWBK8VGWnzwcSKNOgiHdrSfgo_tSzGqlz5mPv7hy766kjXkOpvxpoOGoQd2o/s0/ssis-lab7-16.png)

異動上面那三筆資料，再執行封裝。
```sql
UPDATE stg.Customer
SET TerritoryID = 4
WHERE
CustomerID IN (15001, 14996, 14997);
```

結果會新增三筆歷程，並將已過期資料的 ValidTo 資訊補上。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJSxiHmrUpAomDRcf3LkF6EO-eqPgB8oVya46mpSobGYqlW8x4G-eGErDd5pLx5CIuYUXJAUVDiMdR8Wb4LLuzw-zlgMTvr4ghyphenhyphengYsV_VCnvLdrDnI8EEel3rMzf0eA1MT2NHkukMnMa4/s0/ssis-lab7-17.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZglssWGsJZpMnYbWX2rTsOOArR0JlJ2JmqMSEb-66A1IRc-9oagto7CEk1dIAn_VPAySbpQE-qneU3CPokKtJzX4hiNyU7flF3tAb-Iel2I1fQX3Sn1SQ6vdySUvuoHwo-ZlO_XyBPgY/s0/ssis-lab7-18.png)

## 使用「Set-based Update」技巧

底下練習，我們要將步驟 D1 改成 set-based update 方式。

1. create a table to store rows needed for the update
```sql
CREATE TABLE dbo.UpdateCustomers
(
CustomerKey INT NOT NULL,
FullName NVARCHAR(150) NULL,
EmailAddress NVARCHAR(50) NULL
);
```

2. replace OLE DB Command with a OLE DB destination .

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgx7LQZPDbz_QqLVUUj7US0xoCLkBBWsP5xJtse35b3ZEPRVeKedNFtlHO3KT43L2Ijwkm3-q9pFl3YIZFaQovk5uTfFc5l3F6M9PDRD2OMjvxFt_duwFyTXBEIY0j0PLDmOdavEuPaes/s0/ssis-lab7-19.png)

3. setting the key mapping.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqCZ_kq4GGzZ5ExL9yqA6cWHXZ7LLICqeabvM6Z_CTZGBzXFWD7AZFSbNIbW3sHIht7OAbLJjCTJFS1ohcEpDJsMTUYnr-apthVLfF8OENjTBh5Uzt_g7l4SIHZKxpwP3X9N_sUtPEZKY/s0/ssis-lab7-21.png)

4. add two [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) , one before Data Flow to truncate UpdateCustomers, and another after Data Flow to run Merge TSQL Update Statement. 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJGmImiYUUwSVkByDeTkcWI3bkPY1XYBina7Z6Og7un6uAt8W2KRXEfkbMxP7dKSOYrQUxOtpHuFqC5KIp5z3C060g_U7ftn-MN0CtvY97vHRyS9o0a57bTAyNqQoBKp09LEeYrSGLi2A/s0/ssis-lab7-20.png)
```sql
UPDATE C
SET
FullName = U.FullName,
EmailAddress = U.EmailAddress
FROM dbo.DimCustomers AS C
INNER JOIN dbo.UpdateCustomers AS U ON U.CustomerKey = C.CustomerKey;
```