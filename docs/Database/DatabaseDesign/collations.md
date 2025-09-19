---
title: 如何變更定序 Collations
layout: default
parent: Database Design
nav_order: 1
description: "定序是資料庫中用來排序和比較字串的規則，這篇內容記錄如何變更某一個資料庫的定序。"
date: 2025-02-06
tags: [定序,SQL,Collations]
---

## 前言：什麼是定序？

 定序是一種資料庫的屬性，用來指定資料庫中字串的排序和比較規則。

例如：
- Chinese_Taiwan_Stroke_CI_AS
- Chinese_Taiwan_Stroke_90_CI_AS_SC

Stroke：用筆畫進行比對或排序<br>
Bopomofo：用ㄅㄆㄇㄈ進行比對或排序。<br>
CI（Case InSensitive）：不區分大小寫<br>
AS（Accent Sensitive）：區分音調（如有注音符號的字母不同會視為不同）<br>
SC：支援增補字集相關的定序（如 CJK 擴展字元）<br>
90：表示此定序沿用 SQL Server 2005（9.0）版本的排序規則<br>

SC定序是 SQL Server 2012 （11.x）新引進的與增補字集有關的定序。從 SQL Server 2017 (14.x) 開始，所有新的定序都會自動支援增補字元。

有些定序會加上`數字`用以區別是哪一版本的資料庫開始支援，例如含有90的定序，就是SQL Server 2005（9.0）版本的排序規則。<br>

查詢物件的定序
```sql
--取得 SERVER 的預設定序
SELECT CONVERT (varchar, SERVERPROPERTY('collation'))

--取得 DATABASE 的定序
SELECT CONVERT (varchar, DATABASEPROPERTYEX('YFEP','collation'))

--取得 TABLE 中每個欄位的定序
SELECT
	t.name, c.name, c.collation_name 
FROM sys.columns c 
	INNER JOIN sys.tables t ON t.object_id = c.object_id
WHERE c.object_id
      IN (SELECT object_id FROM sys.objects WHERE type = 'U')
      AND c.collation_name != 'NULL'
   And t.name='UserMake'
ORDER BY t.name, c.name
```

## 如何變更定序

{: .important}
>定序設定可分成伺服器層級、資料庫層級、資料行層級。
>雖然每個層級的定序都可以變更，但對原本已經建立好的資料，變更上層層級的定序，並不會改變原先下層層級的定序。

### 1. 變更資料庫的定序
```sql
-- 設定為單一使用者
ALTER DATABASE [SchoolDB] SET SINGLE_USER;
GO

-- 變更資料庫定序
ALTER DATABASE [SchoolDB] COLLATE Chinese_Taiwan_Stroke_CI_AS
GO

-- 恢復成為多人使用
ALTER DATABASE [SchoolDB] SET MULTI_USER;
GO
```

### 2. 變更資料行(欄位)的定序
```sql
ALTER TABLE Student ALTER COLUMN FirstName nvarchar(50) COLLATE Chinese_Taiwan_Stroke_CI_AS
```

{: .important}
>雖然欄位的定序可以變更，但 MSDN 上有寫，如果欄位參考了下列任何一個項目的定序，就無法變更其定序：
>- 計算資料行
>- 索引
>- 散發統計資料，不論是自動產生或由 CREATE STATISTICS 陳述式產生
>- CHECK 條件約束
>- FOREIGN KEY 條件約束

## 如何改變整個資料庫的定序

一個已經運行中的資料庫，變更資料庫的定序，並不會同時更改所有資料欄位的定序。
而更改資料欄位的定序，又很容易遇到以上不可變更的狀況。

若要變更整個資料庫定序，可參考以下做法：以新定序建立一個新的資料庫，並將原始資料庫的資料複製到新的資料庫。

### 1. 由原始資料庫，產出資料庫建立的完整腳本

使用 SSMS 建立整個資料庫的完整腳本，但不含資料，這樣就可以得到一個包含所有資料表、索引、條件約束、預存程序、檢視等等的腳本。這個腳本可以用來建立一個和原始資料庫結構一樣的新資料庫。

![Generate Script](images/generate-script.png)

在設定 SSMS 建立腳本的步驟中，進階選項內有幾個選項要注意：
1. General<br>
Script Collaton：要設定為 False (預設值就為 Fase)<br>
設成 False，就不會在腳本中產生定序的相關設定，在底下步驟中，我們會微調腳本內容，以便建立新的資料庫時，使用我們要的資料庫定序。

2. Table/View Optionsl

這裡面的選項，可依實際狀況進行調整，例：

- Script Full-Text Indexes：預設值為 Fase，若有用到就改為 True。
- Script Trigger：預設值就為 Fase，若有用到就改為 True。
- Script Indexes：預設值就為 True，應該都有用到，就用預設值。

![Advance Settings](images/advance-options.png)

### 2. 使用腳本建立新的資料庫

將上一個步驟中產出的資料庫腳本，依實際需要進行修改，例如：

1. 修改`資料庫名稱`及`資料庫的檔案名稱及位置`
2. 指定`資料庫定序`

因為前面在建立腳本時，我們沒有輸出定序資訊，所以此時將要使用的定序加進腳本中。修改前後對照如下：
![Adjust Script Before](images/adjust-script-before.png)
![Adjust Script After](images/adjust-script-after.png)
   
調整完之後，執行腳本，就可以得到一個資料庫結構和原始資料庫一樣的，但定序是我們要的新定序。

### 3. 使用 Export/Import 功能，將原始資料庫中的資料複製到新的資料庫。

接下來在 SSMS 中使用「匯出/匯入」功能，將舊有資料庫的資料，全部匯入到新的資料庫，不過這個操作，通常會遇到幾個問題:

1. FOREIGN KEY 條件約束(CONSTRAINT)
2. Identity 欄位值無法匯入

這二個問題，可以透過下列方式解決：

停用全部資料庫內的 FOREIGN KEY 條件約束
{: .label }

下面語法，可以停用/啟用資料庫內的 FOREIGN KEY 條件約束(CONSTRAINT)、CHECK 條件約束(CONSTRAINT)
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

啟用識別插入
{: .label }

在匯出/匯入的`編輯對應`功能中，可以選擇「啟用識別插入」(Enable identity insert)，這樣在匯入資料時，就可以保留原始資料庫中的識別值。

![Mapping Settings](images/mapping-settings.png)

若有多個資料表要設，可以全部選取，一起設定啟用識別插入。

![Mapping Settings Multi](images/mapping-settings-multi.png)

經由上面的設定，若沒其他情況，「匯出/匯入」作業應該就可以順利完成，此時就得到一個內容及結構都和原先資料庫一樣，但定序是新的定序。

![Export Import Success](images/export-import-success.png)

匯出/匯入作業完成之後，記得將原始資料庫改名，並將新的資料庫改回原始資料庫的名稱，同時記得再啟用 FOREIGN KEY 條件約束。

```sql
ALTER DATABASE YFEP SET SINGLE_USER WITH NO_WAIT
GO

EXEC sp_renamedb 'YFEP' , 'YFEP_OLD' ;
GO

ALTER DATABASE YFEP_OLD SET MULTI_USER WITH NO_WAIT
GO

ALTER DATABASE YFEP_NEW SET SINGLE_USER WITH NO_WAIT
GO

EXEC sp_renamedb 'YFEP_NEW' , 'YFEP' ;
GO

GO
ALTER DATABASE YFEP SET MULTI_USER WITH NO_WAIT
GO
```

以上就是如何變更整個資料庫的定序。
另外，匯出/匯入作業會增加大量的交易記錄，所以在執行匯出/匯入後，可使用 SHRINKFILE LOG 壓縮目前資料庫的資料或記錄檔大小。


## 參考資料
- <a target="_blank" href="https://learn.microsoft.com/zh-tw/sql/relational-databases/collations/collation-and-unicode-support?view=sql-server-ver16">定序與 Unicode 支援</a>
- <a target="_blank" href="https://learn.microsoft.com/zh-tw/sql/relational-databases/collations/set-or-change-the-column-collation?view=sql-server-ver16">設定或變更資料行定序</a>
- <a target="_blank" href="https://sharedderrick.blogspot.com/2010/09/disableenabletrigger.html">停用(Disable)與啟用(Enable)「觸發程序(Trigger)」</a>
- <a target="_blank" href="https://sharedderrick.blogspot.com/2017/06/sql-server-disable-foreign-key.html">全部停用 (DISABLE) 資料庫內的 FOREIGN KEY 條件約束(CONSTRAINT)</a>
- <a target="_blank" href="https://dotblogs.com.tw/jamesfu/2016/12/10/Change_Database_Collate">[SQL][問題處理]如何改變已存在資料庫的定序 ?</a>