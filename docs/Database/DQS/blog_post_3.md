---
title: 管理 Master Data
layout: default
parent: DQS
nav_order: 3
description: "管理 Master Data"
date: 2014-01-22
tags: [SQL,Data Quality Service]
postid: "2951530457294478432"
---
# 主要資料的匯入匯出

MDS 模型封裝是用來轉移 MDS 模型用的，讓你可以將整個 MDS 模型轉移到其他伺服器上。  

## 建立 MDS 模型部署封裝（MDS model deployment packages）

MDS 模型部署封裝為 XML 格式檔案，它可以協助你將整個 MDS 模型轉移到其他伺服器上。  MDS 封裝檔的副檔名為 .pkg ，檔案裡包含以下中繼資料：（注意：裡頭不含權限資料）  

- 實體（Entities）
- 屬性（Attributes）
- 屬性群組（Attribute groups）
- 階層（Hierarchies）
- 集合（Collections）
- 商務規則（Business rules）
- 版本旗標（Version flags）
- 訂閱檢視（Subscription views）

建立 MDS 封裝，有二種方式：

- 使用 [MDSModelDeploy](http://technet.microsoft.com/zh-tw/library/hh479639.aspx) 命令列工具。
- 使用[模型部署精靈](http://technet.microsoft.com/zh-tw/library/ff487019.aspx)。

## 匯入資料

通常你會使用組織中不同資料來源的資料來擴展 MDS 資料庫，而使用「匯入」來擴展資料當然是最直覺的方式。  您必須先將資料匯入至暫存資料夾（staging table），然後以批次方式處理暫存資料。

## 匯出資料

您可以建立訂閱檢視，將 MDS 資料匯出至訂閱系統。  然後，任何訂閱系統可以檢視 MDS 資料庫中的已發行資料。

# 主要資料的安全性

## Users and Permissions

- Functional area access：
- Model object permissions：
- Hierarchy member permissions：
- 

![](http://127.0.0.1:8080/_images/cs/XXXXXX.png)

- MDS System Administrator
- Model Administrators

## Overlapping Permissions

- [如何決定權限 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/ff486950.aspx)
- [重疊的使用者和群組的權限 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/ff486958.aspx)

# 使用 Excel MDS 增益集

「**Master Data Services Add-in for Excel**」是一個用來維護主要資料的工具，它讓使用者在熟悉的工具介面中，可以同時多人同時更新主要資料，以維護主要資料的完整性。  

## 在 Excel 中編輯主要資料

使用前請先安裝「[適用 Excel 的 MDS 增益集](http://www.microsoft.com/zh-tw/download/details.aspx?id=29064)」

如果安裝完成就會在 Execl 中看到「主資料」頁籤。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaFrW8grdtVDX57qz4kTOM2sCMmFnV8Gx4tZzOvOodTyAMEAq3TTWDQ2Gqxh78C5_p85yDdzZtQIZIyy1xTTM1uMr_D4OqVN-KjiVGPIACAuqp9gxvULxgRTnW_86utKjjVcbU9xEL7TE/s0/ssis-mds-mgr-01.png)

設定連接後，再由［主資料總管］的選項，就可以取得你要的資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6GNw-qARAgko4PGEL2qEEh7PrbZt7hmEX8fzxrdi5COjfyH2TYJVEgVZ_Moi1NvpQebmGu_raRarv8PBhmbIz1_kRT-ytkBxRSlbNm4KAKDdQ1IwoLuJQV689-M2EFQE3We1-EsRWvQ4/s0/ssis-mds-mgr-02.png)

完成新增或修改資料後，你可以透過［發行］，將資料回存。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOMrMMI3_eJLqQV-dF84yWxegcp51avxDFmP0Ivvn5NgX1IfldbSxfLU7IR7FXYGapAv9XV_c70e3-Wabk3e4eCyztyYVyG258Nf4LDPSMqlUagsU_65X3TgFanko4QIfhGnbAOy-5hT0/s0/ssis-mds-mgr-03.png)

MDS 會驗證你發行的資料，並顯示驗證結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgh3p1lNdgpUtZefL3RsUybyAFriq8hZExk24cR1zQp47Lz_-necHPYP_9G2b3yDy098yzTiR6mSVCJsDEkZgc7UrcEYieQwD088hQE6qi5BdyoAujYispUHbTtb__MwRirFAHKFE73Mng/s850/ssis-mds-mgr-04.png)

將驗證失敗的資料修正後，再次執行發行即可。

## 在 Excel 中建立 MDS 物件

MDS 增益集除了可以用來維護 MDS 資料，也可以用來建立 MDS 模型的相關物件。
## 參考資料  

- [部署模型 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/ff486956.aspx)
- [匯入資料 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/ee633726.aspx)
- [匯出資料 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/ee633741.aspx)
- [安全性 (Master Data Services)](http://technet.microsoft.com/zh-tw/library/hh231026.aspx)
- [Master Data Services Add-in for Microsoft Excel](http://technet.microsoft.com/en-us/library/hh231024.aspx)