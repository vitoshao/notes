---
title: Lab10：建立 MDS 模型
layout: default
parent: Labs
nav_order: 9
description: "Lab10：建立 MDS 模型"
date: 2014-01-24
tags: [SQL,Labs]
postid: "2014217071740949034"
---
這個 Lab 將練習如何建立 MDS 模型及其相關物件。並且在模型中，以手動方式，建立實體的成員。  

# 建立 MDS 模型

這個練習將建立 MDS 模型，包含建立 entities, attributes, attributes group, hierarchies, business rules 等模型物件。

## 1. 建立模型

1.1 開啟[主資料管理員網站]，並點選[系統管理]連結

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi50lLTmVxUrYY9v3mhr5sYAdInL4-KarFBNIUitxpZ3LE2k7ErjetGG3ILNbF-ArrcTMhwt62If9rqHVqJXUoPmsc1bSmkG-oV5DAX0EyW3UEeXECb8E-fDTSomklOJZiqokDGqSlS0Ik/s850/ssis-lab-mds-01.png)

1.2 加入模型（ModelCustomer）

在[管理]選單中，點選[模型]功能。  
加入一個新的模型，同時勾選[建立同名實體]和[建立同名明確階層]。  
設定完成後儲存。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLnzctwAlXcOnp7qCf6BJfFvbmlZkrrV099G_9erMI5Q_h7WV2tY7cwsRNFLs3n0jQIUCL88_h23NuOANBhLjer-P84nT5Vy3P6eZq3eRpYdgQcuY5tT3_hDhLJJd2gqmGziqBxHTBspg/s0/ssis-lab-mds-03.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYKnVVj0B4jmhBRmosDoIwQ9uqpTH0_L8xnzyg3CHKz3xJlRcJ6C7eBfKK0u1ITh29tF_UBPGEgIfs3fTHFYO3gDRRo3zrkypN2nWloVAFmTxGiU6d7-BhdbXMwIhtY65nxfllrqqXYwk/s0/ssis-lab-mds-02.png)

## 2. 建立實體

2.1 加入實體（StateProvince）

在[管理]選單中，點選[實體]功能。  
在模型的下拉選單中，選擇前一步驟建立的模型，然後執行[加入實體]。  
勾選自動建立代碼值。  
不使用明確階層和集合。  
設定完成後儲存。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrwcD4txO16a1FD9qcIULISnpmigCpgKbOmoYxrvuvkH4_1zmIP5HQL-kAjPYNqUOIrmmNk4NgAaXrnf9-BMAP6Rz-355_S4MRcE5Xsbb4a-8nR7KrLfDoo_BG_lenihlsqrhm7DwqtEo/s0/ssis-lab-mds-04.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSx6wSaxMTGOdQ59QziG1IEXoey3sjdO3bCBwouA5I1GhaFOakRM0TUXgwRULC2WIct0EpcLGIdYs4QYc-bAV3KksVpSC0a2E-jCfTWBEubsNJXfRj1h53my-y9zWiRuWZmN24QxpfbRo/s0/ssis-lab-mds-05.png)  

2.2 加入實體（CountryRegion）

依相同設定，再建立一個 CountryRegion 實體。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4YRLFumi0omcxRlFaHwRJjntvr_es5At472qpvCAavGfe5detLAQ0ke4x9A3W9aSXCI5EmXV6FlPzHyBGxbh2RqYoLvt4Ix3_7gAbWqVBNIA-MMu69boY3yqNkTb3mEJPaUs4iOByJKk/s0/ssis-lab-mds-06.png)

## 3. 加入成員屬性

完成以上步驟後，系統總共有三個實體，其中二個是我們手動建立的，另外一個是由系統建立且與模型同名的實體。

### 3.1 在StateProvince實體中加入分葉成員屬性

3.1.1 先點選StateProvince實體，再執行[編輯實體]功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjbAPVKDUy3lWv5IijnDKUweQK2SBIxBCdXgVskyZbJrThpWmWyS30SDoPYPq8O9i6u6z4m9BnU0R74YC_WFBZjZTa6hIC1ZF4TGI_W2j9sDDxszPvkPmi1rd5kuaIxHORVzDxA1TMcF5g/s0/ssis-lab-mds-09.png)

3.1.2 執行加入成員屬性

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5LP03noZnYRaBk31_QsGa5KsbNh5bsCsyd-m2p0LtwePv5YPVNvHJnGXwQYq8iXz9r5Su7Xov1oIk9qKzJJLDjfq7vpGT_Vob9Muf8e-odRABnJCgLTedZfxGIaUaLqeQmfA-Q_o2-E4/s0/ssis-lab-mds-07.png)

3.1.3 依下表設定值，建立StateProvince實體的屬性

| 類型 | 屬性名稱 | 實體 | Pixel | 資料類型 | length | mask | 追蹤 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| domain-based | CountryRegion | CountryRegion | 100 |  |  |  | X |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2dN_i1Zej7zK5FGNYxqV7awhTNnminMGi2CvAlULJhKl7N8E1HO_IAsDMxWdOdZQ7NqpEpkji80m2SBOzlM3aWX6o4X-eUgveqJId0alhKU1EonioDFvim2b_NBTo2k3CKJfg6wZ1TMM/s0/ssis-lab-mds-08.png)

### 3.2 在ModelCustomer實體中加入分葉成員屬性

使用同樣的方法，依下表設定值，在ModelCustomer實體中加入以下屬性

| 類型 | 屬性名稱 | 實體 | Pixel | 資料類型 | length | mask | 追蹤 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| domain-based | StateProvince | StateProvince | 100 |  |  |  | X |
| free-form | StreetAddress |  | 200 | Text | 100 |  | X |
| free-form | City |  | 100 | Text | 100 |  | X |
| free-form | EmailAddress |  | 100 | Text | 100 |  | X |
| free-form | MaritalStatus |  | 20 | Text | 1 |  | X |
| free-form | BirthDate |  | 100 | DateTime |  | yyyy/MM/dd | X |
| free-form | YearlyIncome |  | 100 | Number |  | -#### | X |

完成以上步驟，你可以看到以下的模型結構

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgS5Xwce9t3wyD1j1SM4v_rZ4MvsaxfdsKBRQRs_C27ZlUEWuLatd40M3viGMJ5-UPzMFuhTIg4Yi0XbH5wf34FD_85kBIcrbPGC2u3rT1HUKoZSqoM5YKDWJWfDKh18u9ANoqEYLhSMhk/s0/ssis-lab-mds-10.png)

## 4. 建立屬性群組

4.1 加入屬性群組（Demography）

在[管理]選單中，點選[屬性群組]功能。  
選擇 ModelCustomer 模型。  
選擇 ModelCustomer 實體。  
點繫分頁群組。  
點繫加入分頁群組，名稱輸入 Demography 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkKUvrLD314o6PAmwiGTowAz0Ib63l29xh503vNVyIEdBe9bR158_rXmyzNLjs0pIDQYlFpaVRnh5ZVYGtTpSaVsHRhm8o7X6uMd8raQ49kHkIeienXP7rsEsp4vcqane_HBeMNXmJFvk/s0/ssis-lab-mds-11.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXc2A_BE7sqehv8yCkhpSsXXIAMh2XZUXqGGABvVsk1KQVL38pZGyUWuWBT1P2dLE9iqd9stIrtxcmLs1Rhf3UIKMtO2xl5nHgP_Wvpn9junrYx32uOzjtg0bugd6g6SyvzTQ3TNh7ous/s0/ssis-lab-mds-12.png)  

4.2 在[屬性群組維護]中，點選 Demography 下的[屬性]，再點繫[編輯]功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgJDGFZQm70_A18cwWfJAO6hQmv9fyG6nehJ_Mc4VGWPPvg330VTk4Cec74uC6xC0aNmVPutzFITceawf1-G1sLpVf8cn68Vq1F-h1nASe-OrsDA3gAbC4IUYuvrZmzFeNxJMY9M1sCz0U/s0/ssis-lab-mds-13.png)

4.3 將 MaritalStatus, BirthDate, YearlyIncome 設為群組，並儲存。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiueZVQc_EF_g9sjxEe-v2TZ7WxB5YzqL-yQeERE4ybMdeAXsxc_g9Pi80SgoO2KIibVecKNCJq2jFfao72fv8vw3usEpW7NzeKfXYqFGJranRpPjefbRADkNg3fEc2t6Sd0NVKZRn1WV0/s0/ssis-lab-mds-14.png)

## 5. 建立衍生階層

在 MDS 中，[階層](http://msdn.microsoft.com/zh-tw/library/ee633737.aspx)可以用來執行以下作業：

- 將相似成員分組。
- 將成員合併或匯總以進行報告和分析。

### 5.1 加入衍生階層（Geography）

在[管理]選單中，點選[衍生階層]功能。  
在 ModelCustomer 模型中，加入衍生階層，命名為 Geography 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgow1ezYUsz4c3X48lxxjpNuRxvEhDfYxumin-rYLkk5GcEySN7ATnbBPOL-Vs-yHxaEHDIt2O_KMRNUtT0tS8fL9WaaA21RzyZYFzy4QhPq_u2b7Ktli7zPE9iZ1X7j5KqgkpZM7WXdow/s0/ssis-lab-mds-15.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTEYBtQJHZPIzbixsOcsJBpsyw-4zFO8kOZkipc6yPbnavDpe9rhlS23UBFFzoVQB_dsCu7d8o-5fkGUcftxI6wHD3hyObOTUOSmxIZ3HmjzsBYJowBG1ZWaL9I_9MdmunLpTlBuUwDwo/s0/ssis-lab-mds-16.png)

### 5.2 將衍生階層加入「目前層級」

在[編輯衍生階層: Geography]中，將實體[ModelCustomer]由[可用的實體和階層]拖拉至[目前層級]中。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnplz1F7VGQZuQoE7ZhZ0ZIN54XKsPMVIdABX6JTEMvDErdHt3tj2Y9Fhm-q466vS87_Df4DVVm-V2Nn1A9Kanp7ceX2qFYkVlJ_1dvWX2pKdueV0OuGRLE__LYQ_EgGcXwql-XI9F6zs/s0/ssis-lab-mds-17.png)

### 5.3 將實體加入「目前層級」

再把 [StateProvince] 和 [CountryRegion] 實體也拖拉至[目前層級]中，結果如下圖。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJdLwFLsqF7OfAVJ94zfp4q7JQdLG7muUHz2lcAxm_mFouzygiL4Uj3pW4IlMnGECwigMqEKaSzE31yiIVhwYvpGMqetZnOCWHCRxKm8pkGY8JUyzOQWlTGx6UOaH2afVc7gv4JtaXPDk/s0/ssis-lab-mds-18.png)

## 6. 建立商務規則

6.1 加入商務規則（EmailAt）

在［管理］選單中，點選[商務規則]功能。  
在［模型］下拉選單中，選擇[ModelCustomer]。  
在［實體］下拉選單中，選擇[ModelCustomer]。  
在［成員類型］下拉選單中，選擇[分葉]。  
在［屬性］下拉選單中，選擇[EmailAddress]。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7xkRPFYfJMC_YBIGH7m4XikusoxF8_fqaCqDFq9XbWvOhDuSnqMIu9_iRgZsXfIpjG5iEshSaBeLSkdAQcwzO4Tc8g1mBCKr76mubTbLXZuMuh80jytnr1LTsR2Ttma_mtsVLABDYAcU/s0/ssis-lab-mds-19.png)

6.2 雙繫表格中的名稱欄位，命名 EmailAt 。再執行編輯功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEMkZag1iWa6BruGpC_2z_rXeE40kJrI1wim-9j6xkYLhnE0e6VGWyd_CgH2J8e7dY3ApmRZwJ-JqKPIbi1xc7IqP9Rr4gqlDtKT2L6RFdmtTnNURnHQmX5WjX_h0TW5n34G7OpJPHBvE/s0/ssis-lab-mds-20.png)

6.3 將[元件]/[動作]/[驗證]中的[必須包含模式]，拖到[THEN]中的[動作]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUfXPAeJAlrlDNHR0u7L1x1lS6vR7bifB-t_dHSrNLyfpabe3eiKKDN0pO0ZRqviyj379A0F0V0bgOFnBf0LSoHqxXxZSjqB8ybD2UVC8NNGjz80Yt1E-8Tbm4nuZ9nK3lfFdVXFvA0Zs/s0/ssis-lab-mds-21.png)

6.4 將[屬性]下的[EmailAddress]，拖到[編輯動作]中的[選取屬性]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXGTSQtdkPZnBr0Z7hWJbP9GRW49jfDUQPQ43o5v7gGH3GGgnJP1j6TqaYUJttEnuJlVF6bbsKUx_tloZG1xciSdXEQyP87iFjYYX_IOAIhAk5uYvkOdLrD0XUp5I4rxMRo3IOokOXJzM/s0/ssis-lab-mds-22.png)

6.5 在[屬性值]中輸入「@」後儲存。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQOeKuW-HzT1Ns0q4hRN0eKQAqf1cgzjW1aYMtgSgvbQyMTS7I4VqaHndXMRuVK2fqR_XZ7VUcpIhyAu116STlHJaBce_WC_H5SUmmms4_zwqwISOEYBgScCqyLbIr1TLe_9H6s2aNrNs/s0/ssis-lab-mds-23.png)

6.6 回到[商務規則維護]頁面，點繫[發行商務規則]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiswzPQq7cdMhn_bJKPMXnZkSQaG0ObD7VPNFeBjxteeTTYXfqSVJIn6IJqAZE_M4IP1Seji8ym31OQCpG3_jZRhl5WRjNmIOYjsHpNJVKgdU-2kR1he5s7D1odpVmvvRp91H5VKJZ2QE/s0/ssis-lab-mds-24.png)

# 擴展實體（Populate the Entities)

**擴展實體**（Populate the Entities)的意思就是在實體中加入成員，底下練習將示範如何擴展實體，並且檢驗其商務規則。  

### 1. 執行主資料管理員的[總管]功能

開啟[主資料管理員網站]，選擇 ModelCustomer 模型，並點選[總管]連結。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjI9OVpj8SbaznWJE8KIcUhWskZKK24JloW5TmlMVHUMf4nGxchhMizmFqUW0XXEbDLJxNHKPKz1jMmj0nrQsZIFwH0TtIHlue298SCSKkqDniEqwB0YpgNYeKm3dnuIQ1C-NAqwOc9PZY/s0/ssis-lab-mds-36.png)

### 2. 切換到 CountryRegion 實體

進入[總管]功能後，預設會開啟 ModelCustomer 實體，但我們必須先擴展其他實體，所以在[實體]頁籤中，選擇 CountryRegion 實體。

PS.這個功能在 IE11 或 Chrome 中，浮動選單都會被下方的物件擋到，如下圖。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikrvVLcI4dTB69afTfyD3PSUjmXSJ_oIaCpljPYircFYD16uwsq4g4ydUoIxuUOY2DefPVWoSQF1KaFX6Xo1IaPnqR77JSQBtqkl4esggM0inFVwarPHEjK7KjIaEixKja5afwoLWsVog/s0/ssis-lab-mds-25.png)

在 FireFox 中則正常。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjDwcf8_1rtZIlItVtIqbpv9j1Bg-qJggpum7S4J2qQi-nGKrtDCdZaX_9FqprJQw3a2-PaZsg3qau_2O-myKQseAsUMRIzYu3ujklu-XvjTeW2g-2diXb88x64-YStyi6RW2MXKFznf6Y/s0/ssis-lab-mds-26.png)

### 3. 在 CountryRegion 實體中加入成員

加入 Australia 和 United States 成員，這二個成員的代碼，都使用系統自動指定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOQ-bmO9NVw6XhP0qw4LvqrsTT9G_1XsTFdmsTk9vGc1HgqHfYgOIa3_ftrw4y9wtTRdnVANjMkzP0XSlSEt0Bqi7wBolSW-3NIVERgFDWl8nRyaTCxFAkWWmCeI4y2yZukYML0J1rphU/s0/ssis-lab-mds-27.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjSj-XhgeOnkBcBN-glYRBYeoa83Se1KoyS1aNqugSx6Udb9yNSoQEQktIfzhNtdReXmX1uvJWkieTO0Ug-IYnoCJCamlQBYZYj113JrabLmv8vPpEa9wmTnmWoATrvg5anrdOHeHtETPo/s0/ssis-lab-mds-28.png)

### 4. 在 StateProvince 實體中加入成員

先切換到 StateProvince 實體，再加入 Queensland 和 Washington 成員。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhiXvDSi11mwub2aaudPLhieTYZAYc1-fuR7-YxHIpefkJ2_LwMvQh5_HT_RvfTnmHpcUnC1xNeuDOIjGMRF6Qp6lm0PsRxdrLour6kxtK8K9uUePED-BR77vDId60Ov29lHnsh1s0ziGY/s0/ssis-lab-mds-29.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPD6oo1qjxISAmNQrbrPm6AEIoR7dfCBvEK9IL1RV8RSX1kBOKTLvbii1vVHAR2G-0BgNNbIeTRyU4gf_tR97CO-EU5rrn2r8RFbh_M9zWHvkfFFnxMj9eKoIq48wkVV6g-JRociCdp54/s0/ssis-lab-mds-30.png)

### 5. 在 ModelCustomer 實體中加入成員

切換到 ModelCustomer 實體後，你可以看到該實體含有二個檢視，一個是[Demography]，一個是[所有屬性]。  [Demography]是我們建立的屬性群組，它只包含 MaritalStatus, BirthDate, YearlyIncome 三個屬性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxGZIueGWuig-GyoibAKqDs-f2b9LcihJJEdZK0dRwhAsyGIrgEMDMVi8K_87-UKIWJpIqCNWrQoWGlpzhL0LP3HSZP6irfifpE00tPNuLD-5L0SnH_ZzK7d4uwt0h2sL8VDmMGK3aJiM/s0/ssis-lab-mds-31.png)

點選[所有屬性]，並加入以下二筆資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLKX-gKtMZe9MRjLARUXnwd_sX3QLd4KU4R195SV124nvBLdvrjNvzMz_J20hrRNA83yEXXglsuGcIASm4wSxWbSSxJSDJR4rop21uPIsbExA2-vTyRgu9Xam1CTSRn-lL7R1-2pfTTho/s0/ssis-lab-mds-32.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjUkaA2FdNUHDA7w4CryRCyTuLx3zlb96O5uwpGjSc_aqaygoJK9Qkj6_NflRstzLytX6PJOPPpwTNq4zo4MkxXiCZs1C4YDJVI3BzJKkaq042wMrOhATySiE1KG4zwZraMPMZCb3yRGoE/s0/ssis-lab-mds-33.png)

當你輸入完第二筆成員，再點繫[確定]按鈕時，你會得到一個[驗證失敗]的錯誤訊息。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhregD3vpD8ZIm8zeszTGUryCDUi0aq3ihVhZpUqFiQq0EfxV1llq456sDdW84ojKgWX181MFFFhB5fi1zqTP_KRBXFUjrZrfsv8jdf3wKO4FuAGk4Jx6oDNYqVX1xreoDfxpS1HRn35eI/s0/ssis-lab-mds-34.png)

只要修正 Email 中的 # 符合後，再點繫[確定]按鈕，驗證即可成功。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEho9aM_cbT31tEwBCXjIrrBSjhD0mV89Y2rV9oaiHUdnYGCnvzehiRzXAAbrlInu0cl5fgq52xIX9mVMqFooiFjmIP9hERY1b3Eprj5gvbyw3rJirr7McGyuWlkLC6rWgJej1T5mq-ZGos/s0/ssis-lab-mds-35.png)