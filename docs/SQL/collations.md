---
title: 如何變更定序 Collations
layout: default
parent: Sample
nav_order: 1
description: "...。"
date: 2025-02-06
tags:
  - 定序
  - Collations
---

## 前言：什麼是定序？

 定序是一種資料庫的屬性，用來指定資料庫中字串的排序和比較規則。

例如：
- Chinese_Taiwan_Stroke_CI_AS
- Chinese_Taiwan_Stroke_90_CI_AS_SC

Chinese_Taiwan_Stroke：用筆畫進行排序<br>
Chinese_Taiwan_Bopomofo：用ㄅㄆㄇㄈ進行排序。<br>
CI：Case Sensitive<br>
AS：Accent Sensitive<br>
SC：增補字集<br>
90：資料庫版本<br>


有些定序和資料庫版本有關，所有含有90的定序都是SQL Server 2005的定序。<br>

SC定序是 SQL Server 2012 （11.x）新引進的與增補字集有關的定序。從 SQL Server 2017 (14.x) 開始，所有新的定序都會自動支援增補字元。


## 如何變更定序

### 1. 變更資料庫的定序

### 2. 變更資料行(欄位)的定序

雖然欄位的定序可以變更，但 MSDN 上有寫，如果欄位參考了下列任何一個項目的定序，就無法變更其定序：
```
- 計算資料行
- 索引
- 散發統計資料，不論是自動產生或由 CREATE STATISTICS 陳述式產生
- CHECK 條件約束
- FOREIGN KEY 條件約束
```

## 如何改變整個資料庫的定序

一個已經運行中的資料庫，資料欄位一定會遇到以上狀況，所以無法順利變更定序。底下是一個方法，示範如何將一個資料庫的定序由 Chinese_Taiwan_Stroke_90_CI_AS_SC 變更成 Chinese_Taiwan_Stroke_CI_AS，包含所有文字型態欄位。

### 1. 由原始資料庫，產出資料庫建立的完整腳本

使用 SSMS 建立腳本
![generate-script](images/generate-script.png)

使用 SSMS 建立腳本時，進階選項中的設定有幾個事項要注意：
1. General<br>
Script Collaton：要設定為 False (預設值就為 Fase)<br>
2. Table/View Optionsl<br>
這裡面的選項，可依實際狀況進行調整，例：<br>
Full-Text Indexes：預設值就為 Fase，可用到就改為 True。<br>
Trigger：預設值就為 Fase，可用到可改為 True。<br>
Indexes：預設值就為 True，應該都有用到，就用預設值。<br>
![advance-settings](images/advance-options.png)

### 2. 使用腳本建立新的資料庫
將上一個步驟中的腳本，可依實際需要進行修改，例如：
1. 修改資料庫名稱,資料庫檔案名稱
2. 指定資料庫定序<br>
建立腳本時，我們沒有指定定序，所以在建立新資料庫時，指定我們要的定序。
![Adjust Script](images/adjust-script.png)
   
### 3. 使用 Export/Import 功能，將原始資料庫中的資料複製到新的資料庫。
直接匯出/匯入操作會遇到幾個問題:
1. FOREIGN KEY 條件約束(CONSTRAINT)
2. Identity 欄位值

可以透過下列方式解決：

#### 停用全部資料庫內的 FOREIGN KEY 條件約束
```sql
-- 全部停用：資料庫內的 FOREIGN KEY 條件約束(CONSTRAINT)、CHECK 條件約束(CONSTRAINT)
USE YFEP_New
GO
EXEC sp_MSforeachtable @command1="ALTER TABLE ? NOCHECK CONSTRAINT ALL"
GO

-- 全部啟用：資料庫內的 FOREIGN KEY 條件約束(CONSTRAINT)、CHECK 條件約束(CONSTRAINT)
-- 不檢查現有資料
USE YFEP_New
GO
EXEC sp_MSforeachtable @command1="ALTER TABLE ? WITH NOCHECK CHECK CONSTRAINT ALL"
GO
```

#### 啟用識別插入
在編輯對應功能中，選擇「啟用識別插入」(Enable identity insert)
![Mapping Settings](images/mapping-settings.png)
也可以一次選取多個資料表，一起設定啟用識別插入
![Mapping Settings Multi](images/mapping-settings-multi.png)

在我的情況下，做完上述步驟，就可以成功將原始資料庫的資料匯出/匯入到新的資料庫中，這樣就得到一個和原先一樣內容的資料庫，但定序已經變成新的定序。
資料複製完成之後，記得啟用 FOREIGN KEY 條件約束