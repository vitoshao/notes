---
title: FileTable
layout: default
parent: Database Manage
nav_order: 11
description: "FileTable"
date: 2013-11-14
tags: [SQL,Database Manage]
postid: "8947071377268756965"
---
「[檔案資料表](http://msdn.microsoft.com/zh-tw/library/ff929144.aspx)」（ FileTable）是 SQL Server 2012 開始提供的新功能。  一般的資料表欄位中存放的是使用者資料，但是檔案資料表則是根據 SQL Server FILESTREAM 技術，直接將磁碟中的檔案和目錄資訊儲存在資料庫的資料表中。  也就是說，您可以將檔案和文件儲存在 FileTable 中，而從 Windows 應用程式存取它們，就像它們儲存在檔案系統中一樣，並不需要對用戶端應用程式進行任何變更。   

# FileTable

## 何謂 FileTable

FileTable 是包含預先定義結構描述的特殊化使用者資料表，可儲存 FILESTREAM 資料、檔案和目錄階層資訊，以及檔案屬性。 

FileTable 提供了下列功能：

- FileTable 代表目錄和檔案的階層。它會儲存與該階層中所有節點相關的資料 (目錄以及它們所包含的檔案)。
- FileTable 中的每個資料列都代表一個檔案或目錄。
- 每個資料列都包含下列項目：stream\_id、file\_stream 、path\_locator、parent\_path\_locator、建立日期、修改日期...
- 你可以直接在 Windows 應用程式中，存取 FileTable 對的檔案和目錄。
- SQL Server 元件會攔截透過 Windows 共用建立或變更檔案或目錄的呼叫，然後將它們反映在 FileTable 的對應關聯式資料中。

## 啟用 FileTable

因為 FileTable 會使用 FileStream 在檔案群組中建立一個 FileStream 目錄以對應到實體的資料檔案和目錄。  所以要使用 FileTable 必須先啟用一些相關的功能，例如下列項目： 

- 於執行個體層級：
- 在執行個體層級啟用 FILESTREAM。
- 於資料庫層級：
- 在資料庫層級提供 FILESTREAM 檔案群組。
- 在資料庫層級啟用非交易式存取。
- 在資料庫層級指定 FileTable 的目錄。

### 在執行個體層級 啟用 FILESTREAM

要使用 FileTable 這個新功能，必須先啟用 FileStream ，你可以在組態管理員中找到設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgucfvcjaebMkb6c1_aTu9SWtxhH_9cOii4ZEAfNg5cXN2lS1GhgOPmDYJ9k-xWiard8-B9sgcu0twxzlYNsBr39RXqeHB05_LjzKo43zG_Ak2Pe_Sb-FV2kPSO1_hHWaWcIw6IBS7fmq8/s850/sql-filetable-01.png)

接下來，必需要開啟 FileStream 存取的權限。

開啟執行個體的【屬性視窗】，將存取層級設定成完整存取

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4_sEv9xi-xJRWR3j4jrajhzBiO4hOz9FKIXQd2JSMKqOLsEykIHE-un7-jhOHK8q_eQ8v_HmRlvZARs5lyRwvcq373qJ4H_ao2iRVY3T42WUNDpj2mqkRVT0A7U5hDiRE2P13MTcOLW8/s0/sql-filetable-02.png)
```sql
--存取層級設定也可以使用 TSQL
EXEC sp_configure filestream_access_level, 2 
RECONFIGURE
```

PS. 完成這個步驟後，必需將SQL Server重新啟動。

### 在資料庫層級 提供 FILESTREAM 檔案群組

在啟用 FILESTREAM 之後，就可以對作業資料庫進行設定。

開啟資料庫的【屬性視窗】，在［檔案群組］頁簽中，加入新的檔案群組。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEcnTv6VVEX-CeUF5gA9gBsqqqzg0ArAYYV1v5eSkekbf9fOgElmiA8gEl87xI_pvYpM3iK0rD2oFCESpPm_eOpYKP1P60qiNfdjJT6WR3-00arJINUxhWOOF3XYxwHU232ygzBujcygM/s0/sql-filetable-03.png)

在［檔案］頁簽中，使用前一步驟建立的檔案群組，新增一個資料庫檔案，並將檔案類型設為 FILESTREAM。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjedKP1CknhKN-oaRp3TEBMUJAQ9Oe_BcOPNjtHONc-6U5EOPPF0EPLXqFNETMcd-YpHTJM8xGvuYRveuMN1Nrgw3h5xMRT_LWBnAgfmSi8yjlnbPrEjoVq-WEeG3BzssLh_p8Q1vzIbVY/s0/sql-filetable-04.png)

### 在資料庫層級 啟用非交易式存取

在［選項］頁簽中，將 FILESTREAM 非交易式存取設定為 Full 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9NPJ2EagPpBfA77LAMsj4_Flxy1fvYJuDgEdkJ3gGokbAuCCGfn0c6YP6dyVqxy3uhN8l_M_8L6wBnG5VHmd2-O0vdKgDx4lnNuS3nkl1AqPYa6kLoYa_JPkAvVcwkcSirjQsAs8wBp4/s0/sql-filetable-05.png)

設定為 Full 這樣才能直接由檔案總管來存取。  如果沒有設定Full，就只能透過SQL Server來進行檔案的存取，但透過SQL Server進行檔案的存取，其實是會消耗SQL Server的IO的，所以沒有特別需要，還是利用檔案總管來存取比較好；  其次，目錄名稱也請特別注意，這個部分未來也會形成存取檔案的目錄結構之一。

### 在資料庫層級 指定 FileTable 的目錄

在［選項］頁簽中，輸入一個 FILESTREAM 目錄名稱。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9NPJ2EagPpBfA77LAMsj4_Flxy1fvYJuDgEdkJ3gGokbAuCCGfn0c6YP6dyVqxy3uhN8l_M_8L6wBnG5VHmd2-O0vdKgDx4lnNuS3nkl1AqPYa6kLoYa_JPkAvVcwkcSirjQsAs8wBp4/s0/sql-filetable-05.png)

## 建立 FileTable 練習

完成以上步驟之後，我們就可以使用T-SQL語法來建立 FileTable 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjTEb4z70f4yAJlAkV4McnUvQrlZnHjBvlt_ztNUMkTXZKcT0cUhEqpcT1ABUdumZSQM8JQWzucXivROcUR71GKw6JEmOsND_W6UHcb9WkZ07dlj2RUbv3yurqm-FUwZqn2-KBGpUKCr4/s0/sql-filetable-09.png)

上圖中新增 FileTable 功能，並不會直接透過 GUI 介面新增 FileTable，  它只會產生建立 FileTable 的範本語法給你參考，你還是必須修改這個範本後，再透過執行 TSQL 新增 FileTable 。  
```sql
CREATE TABLE dbo.MyDocument AS FILETABLE
WITH
(
FILETABLE_DIRECTORY = 'MyFileStream',
FILETABLE_COLLATE_FILENAME = database_default
)
GO
```

你可以使用［瀏覽FileTable目錄］開啟相對應該資料夾。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSI5R8NCIGoAgfvFhrJgDillQ_2i-PS_30TilLN89we_e1v0PDpxTHAYJjYdMo05N_xw-SRtJV5SQ3KqW8t41gvpwgOVoUyDV1R1xS5j-VAQ-rz-B1g9ns2MCdOGa60ZPgqWwI7V9TacI/s0/sql-filetable-06.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhllPy6CdZSs-AMpgNnGTeuPo5VtjDcDDO-SIrDR819WoXghdiySHIT6AqjIlOXIuFQbGKVfY8OM4DifzaVMmlnpcWH3AdMNTQiFKmFZN6ar15t_W_HmP8gQkS3KxJkSHbDUecei_xAzrs/s0/sql-filetable-07.png)

查詢 FileTable
```sql
select \* from MyDocument
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9tzLOwiU90dY0_rTA7S2UQijaCMyL8bRO8DmkuEul3oXOzEYGUhVENkYyuuR88BsNF3GOg_SIcqgJdDskLSS8311TqKVnfvhSsxR8YFh0myYAjQERE8wSJCKEec5wkDhET4f4xZb1u_8/s0/sql-filetable-08.png)
## 參考資料  

- [FileTable (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ff929144.aspx)
- http://www.dotblogs.com.tw/mantou1201/archive/2013/09/13/118338.aspx【SQL Server】開啟好用的 FileTable 設定
- [SQL Server - 神奇又好用的File Table](http://www.dotblogs.com.tw/skychang/archive/2013/06/17/105580.aspx)