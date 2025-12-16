---
title: 效能調校(6)-使用檔案群組
layout: default
parent: Tuning Performance
nav_order: 9
description: "效能調校(6)-使用檔案群組"
date: 2013-06-01
tags: [SQL,Tuning Performance]
postid: "2761127442201121507"
---
# 資料庫檔案

SQL Server 使用三種不同的檔案儲存你的資料：

1. MDF ( 主要資料檔 )：只可以有一個，可用來儲存各種資料，包括表格、索引、以及資料等。
2. NDF ( 次要資料檔 )：可以有多個，由使用者自訂，功能跟 MDF 一樣，主要用以將資料分散在不同磁碟上。
3. LDF ( 交易記錄檔 )：儲存所有資料檔的歷史變更紀錄，也可以設定多個。

# 檔案群組

- 每個資料庫都至少有一個檔案群組，預設的檔案群組名稱為 PRIMARY 。
- 每個檔案群組都可以包含數個資料檔。
- 主要資料檔只能位於 PRIMARY 檔案群組

例如，您可以將三個檔案 (Data1.ndf、Data2.ndf、Data3.ndf) 分別建立於三台磁碟機內，並將它們指派至檔案群組 fgroup1。   接著您可根據檔案群組 fgroup1 來建立資料表。   這樣資料表的資料查詢就可分散至三個磁碟，藉此改善效能。   另一個改善效能的作法是將單一檔案建立在 RAID (磁碟陣列) 的條狀磁碟組上。

# 分割資料表

當一個資料表中的資料很多時，你也可以將資料表切割成不同的分割區(partition)。  不同的分割區可以使用同一個檔案群組，也可以使用不同的檔案群組。若要使用不同的檔案群組，必須先建立分割配置(partition scheme)。

# 如何刪除次要資料檔

## 如何查詢各資料檔的使用情形
```sql
SELECT FILEID,
[FILE_SIZE_MB] = CONVERT(DECIMAL(12,2),ROUND(size/128.000,2)),
[SPACE_USED_MB] = CONVERT(DECIMAL(12,2),ROUND(FILEPROPERTY(name,'SpaceUsed')/128.000,2)),
[FREE_SPACE_MB] = CONVERT(DECIMAL(12,2),ROUND((size-FILEPROPERTY(name,'SpaceUsed'))/128.000,2)),
[NAME] = left(NAME,15),
[FILENAME] = left(FILENAME,30)
FROM dbo.sysfiles
```

![](http://127.0.0.1:8080/_images/cs/sysfiles.png)

## 刪除次要資料檔

要刪除次要資料檔，必須依以下步驟執行。
```sql
-- 1. Empty the data file.
DBCC SHRINKFILE('ndf_file_name', EMPTYFILE);

-- 2. Remove the data file from the database.
ALTER DATABASE database_name REMOVE FILE ndf_file_name;
```

範例：
```sql
Backup Database TestDB to TestDB_BackupDevice With Init

DBCC SHRINKFILE('Test2', EMPTYFILE)

ALTER DATABASE TestDB REMOVE FILE Test2
```


- [資料庫檔案與檔案群組](http://msdn.microsoft.com/zh-tw/library/ms189563.aspx)
- 
-