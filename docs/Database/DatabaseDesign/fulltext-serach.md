---
title: 使用全文檢索
layout: default
parent: Database Design
nav_order: 4
description: "使用全文檢索"
date: 2013-06-14
tags: [SQL,FullTextSerach、ServiceBroker]
postid: "2209822819966936740"
---

全文檢索搜尋是由**全文檢索引擎**（ Full-Text Engine）所提供。  要使用全文檢索，資料庫管理員必須先在給定的資料表上建立**全文檢索索引**，然後才能在此資料表上執行全文檢索查詢。  

**全文檢索索引**是由資料表中一個或多個欄位構成，這些欄位可以是以下資料類型：  char、varchar、nchar、nvarchar、text、ntext、image、xml、varbinary 或 varbinary(max)。  

要使用 SQL Server 的全文檢索查詢資料，大至需要以下四個步驟：

1. 安裝全文檢索服務。
2. 建立全文檢索目錄。
3. 擴展索引(Population)。
4. 使用全文索引進行查詢。

# 全文檢索概觀

全文檢功能索是 SQL Server 主程序中的一部份，而不是獨立的服務。  不過要使用全文檢索功能，必須安裝全文檢索引擎。  這個引擎可以和 SQL Server 同時安裝，也可以事後再另外安裝。  

你可以透過以下查詢，檢查是否已安裝全文檢索引擎。  
```sql
SELECT SERVERPROPERTY('IsFullTextInstalled');
```

## 全文檢索架構 (Architecture)

以下幾個項目，是構成全文檢索功能的主要程式（處理序）：  

### SQL Server process (sqlservr.exe)

sqlservr.exe 原是 SQL Server 的主要處理序，但它同時也包含了全文檢索引擎，用來管理 full-text 的二項主要工作： indexing 和 querying 。  同時籍由與資料庫引擎完整的結合，亦可以增進全文搜尋的效能。

### 背景篩選程式 (Filter daemon host process , FDHOST, Fdhost.exe)

背景篩選程式(FDHOST)它是一個獨立處理序式，由全文檢索引擎在適當時機叫用才會啟動。  它會在背景中執行下列元件，以進行各項工作：  

- 通訊協定處理常式（Protocol handler）
- 篩選工具（Filters）
- 斷詞工具和字幹分析器（Word breakers and stemmers）

### FDHOST 啟動器( SQL Full-Text Filter Daemon Launcher, Fdlauncher.exe)

當全文檢索引擎需要 FSHOST 進行工作時，實際上是籍由這個 Launcher 去執行啟動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqDTWNTx_i_xnwDPSc2nrRKD3hdSzHGZkzz3TfvwoSLEyiF-0D7QjprCLI1p14UAUCIg9_5SdkjP9bS-_M44MyFyWHJC8ftZjtTAiqoILd3FDSzAlHijT40d31B9Hr690Ad48sR8aqgJ8/s0/sql-fulltext-search-architecture.gif)

## 全文檢索術語（terminology）

- Term：全文檢索查詢的輸入，可以是單子、子句、字串。
- Full-text catalog：一個虛擬物件，用以表示全文檢索索引的群組。
- Full-text index：全文檢索索引
- Token：被斷詞工具切割後的個別單字，即稱為 Token 。
- [斷詞工具（Word breaker）](http://msdn.microsoft.com/zh-tw/library/ms142509.aspx)：「斷詞工具」會根據語言的語彙規則，判斷文字分界存在的位置，藉以識別個別單字。
- [字幹分析器（Stemmer）](http://msdn.microsoft.com/zh-tw/library/ms142509.aspx)：「字幹分析器」會根據該語言的規則來產生特定單字的字形變化 (例如，"running"、"ran" 和 "runner" 是 "run" 單字的不同形態)。
- [同義字詞（Thesaurus）](http://msdn.microsoft.com/zh-tw/library/ms142491.aspx)：使用XML檔來定義特定語言的同義字詞（例如，"steel"、"aluminum" 是 "metal" 的同義字）。
- [停用字詞（Stopword）](http://msdn.microsoft.com/zh-tw/library/ms142551.aspx)：設定捨棄無助於搜尋卻經常出現的字串，全文檢索查詢不會搜尋停用字詞。例口：this, the, are, ...
- [停用字詞表（Stoplist）](http://msdn.microsoft.com/zh-tw/library/ms142551.aspx)：停用字詞表是停用字詞的清單。
- Population (擴展)：擴展全文檢索索引的程序，又稱為 crawl (爬文)。
- Filter：
- Full-Text Engine：是 SQL Server 程序中不可或缺的主要元件，負責 full-text 的管理工作以及執行查詢等工作。
- Filter daemon host process (Fdhost.exe)：負責執行 filter, word breaker, stemmer 等工作的處理序。
- SQL Full-Text Filter Daemon Launcher (Fdlauncher.exe)：負責啟動 FDHOST 。

# 全文檢索設定

要讓全文檢索發揮作用，首先必須建立「全文檢索目錄」和「全文檢索索引」。  

## 建立全文檢索目錄（full-text catalog）

**全文檢索目錄**是個虛擬物件，它是用來當做**全文檢索索引**的容器，同時不屬於任何檔案群組。

### 新增全文檢索目錄

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5wEjYn6VahxOor7qXM_qZfY8ajMgtS12foS0vf5fgNz5dq1L3bpzqjZ0SN99akFtAzsiXbmkl-B_iotV9-wL9eFDLXxMWAfOt_ZGU3zSWIX0qdB67p93zeARWaplk-OLXub_zeUSna1g/s0/ft-create-catalog.png)

### 重建全文檢索目錄

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEicgEh8czTSyxWZapsXdiqupHWYHuoqCCu0TyHi9D0BTigWv5O3ty3q2E6sFnXTWaKSkG_aIrZyykpm41-gkElc1SlqKSAzN7H_r8T3jCirZiZ2vN-APFK_vewUGowFvsOGCBNRoAE7FoA/s0/ft-rebuild-catalog.png)

詳細內容可參考：

- [建立及管理全文檢索目錄](http://msdn.microsoft.com/zh-tw/library/bb326035.aspx)
- [備份並還原全文檢索目錄與索引](http://msdn.microsoft.com/zh-tw/library/ms142511.aspx)

## 建立全文檢索索引（full-text index）

全文檢索索引中包含單字以及這些單字在資料庫資料表中的哪些資料行內的相關位置資訊。  全文檢索引擎會使用全文檢索索引中的資訊來編譯全文檢索查詢，以便快速地在資料表中搜尋特定字詞或字詞組合。    

全文檢索索引是一種特殊類型的 Token 式功能索引，由全文檢索引擎所建立與維護。  該索引的建立程序和結構都與資料表的索引類型不同。   全文檢索引擎會根據個別 Token 從索引中的文字建立反向、堆疊以及壓縮的索引結構，而不是根據特定資料列中所儲存的值來建構 B 型樹狀結構。  

從 SQL Server 2008 開始，全文檢索索引會與 Database Engine 整合在一起，而非如同舊版 SQL Server 位於檔案系統中。  

### 全文檢索索引注意事項：

- 每個 table 只能有一個全文檢索索引。
- 該 table 必須要有 unique key 索引。  
若要對資料表建立全文檢索索引，該資料表必須有單一的非 Null 唯一資料行。  您可以針對 char、varchar、nchar、nvarchar、text、ntext、image、xml、varbinary 和 varbinary(max) 類型的資料行建立全文檢索索引，並且建立全文檢索搜尋的索引。
- 全文檢索索引必須要指定全文檢索目錄，除非有建立預設的全文檢索目錄才可以不必指定。
- 使用「類型資料行」(Type column)   
若是針對資料類型為 varbinary、varbinary(max)、image 或 xml 的資料行建立全文檢索索引，則您必須同時指定「類型資料行」(Type column) 。   「類型資料行」是一個資料表資料行，您可以在每個資料列中儲存文件的副檔名 (.doc、.pdf 與 .xls 等)。

### 新增全文檢索索引

**語法**
```sql
CREATE FULLTEXT INDEX ON table_name
[ ( { column_name 
[ TYPE COLUMN type_column_name ]
[ LANGUAGE language_term ] 
[ STATISTICAL_SEMANTICS ]
} [ ,...n] 
) ]
KEY INDEX index_name 
[ ON <catalog_filegroup_option> ]
[ WITH [ ( ] <with_option> [ ,...n] [ ) ] ]

<with_option>::=
{
CHANGE_TRACKING [ = ] { MANUAL | AUTO | OFF [, NO POPULATION ] } 
| STOPLIST [ = ] { OFF | SYSTEM | stoplist_name }
| SEARCH PROPERTY LIST [ = ] property_list_name 
}
```

**範例**

建立全文檢索索引可以由資料表名稱的右鍵選單中開啟定義。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi36BklSoL1xVCUw7fZMmCsEg1jZlR4e5NOBcZQg56UmuV0m1tDjX7dgGOpUvS0RgYgPBGFtVyuAjy5jD9MfDDzzUXm55jpmEx8AyzW4yqTAl4_5pMjoBW_4DC-JWPt5GZUEVpSSWfqLH4/s0/ft-create-index-1.png)

也可以直接在全文檢索目錄中設定索引的定義。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnrTBzEn3mveDe4nfGSHdbTjQzgo8-haUcGOBi27ZBGaq_NSZBorG8yhnIMMHGeYGRZZVrM1_jYPhb-K7dXWGE0AwywrJN6d8RPs_KfFkaGxWQIj8n3LUbm43GvStiyKw3H23VBG1pONo/s0/ft-create-index-2.png)

或者使用 [CREATE FULLTEXT INDEX](http://msdn.microsoft.com/zh-tw/library/ms187317.aspx) 命令
```sql
CREATE FULLTEXT INDEX ON tblDocuments ( BlogContent,Title )
KEY INDEX PK_tblDocuments 
ON ftcDocuments
```

詳細內容可參考：

- [建立及管理全文檢索索引](http://msdn.microsoft.com/zh-tw/library/cc879306.aspx)
- [備份並還原全文檢索目錄與索引](http://msdn.microsoft.com/zh-tw/library/ms142511.aspx)

# 全文檢索問題排除

以下幾個工具可用來協助全文檢索的疑難排除

## 如何查看全文檢索目錄的屬性設定

當你在索引的 population 動作完成前使用全文檢索查詢，此時查詢可能只會回傳部份比對到的結果。  你可以使用 [FULLTEXTCATALOGPROPERTY](http://msdn.microsoft.com/zh-tw/library/ms190370.aspx) 來查看全文檢索目錄的相關屬性資訊。   
```sql
FULLTEXTCATALOGPROPERTY( catalogName, propertyName )
```

**propertyName**：

- AccentSensitivity：區分腔調字設定。
- IndexSize：顯示全文檢索目錄的邏輯大小，以 MB 為單位。
- ImportStatus：是否正在匯入全文檢索目錄。
- MergeStatus：主要合併是否正在進行中。
- PopulateStatus：擴展狀態  
	- ０：閒置（Idle ）
	- １：完整擴展進行中（Full population in progress）
	- ２：已暫停（Paused）
	- ３：調整執行速度（Throttled）
	- ４：復原中（Recovering）
	- ５：已關閉（Shutdown）
	- ６：累加擴展進行中（ Incremental population in progress）
	- ７：正在建立索引（Building index）
	- ８：磁碟已滿，  已暫停。（ Disk is full. Paused.）
	- ９：變更追蹤（Change tracking）

**範例**

```sql
SELECT FULLTEXTCATALOGPROPERTY('ftcDocuments', 'PopulateStatus');
SELECT FULLTEXTCATALOGPROPERTY('ftcDocuments', 'IndexSize');
SELECT FULLTEXTCATALOGPROPERTY('ftcDocuments', 'AccentSensitivity');
```

## 如何查看擴展(Population)的狀態

Population = 擴展 = 母體擴展 = Crawl = 搜耙

若要查詢目前進行中 population ，可以使用 [sys.dm_fts_index_population](http://msdn.microsoft.com/zh-tw/library/ms186897.aspx) 這個函式以取得。  它會回傳目前進行中之全文檢索索引和語意關鍵片語擴展的資訊。   
```sql
SELECT DB_NAME(database_id) AS 'Database Name'
, database_id AS 'DB_ID'
, OBJECT_NAME(table_id) AS 'Table Name'
, table_id
, population_type_description AS 'Population Desc.'
, status_description AS 'Status Desc.'
, completion_type_description AS 'Completion Desc.'
, start_time
FROM sys.dm_fts_index_population;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYzenDGTgJcm6gkTDbpsSuviklGUa-TBLxjJ3YLw4FVS4AG8ee0BjMv3euIobepoCjxgOgDLvl6yxH8krrcvj3-gcjC8z3mk7KNN4gGdSxj-aj4jMO7EQr28Y-B_mcZ1zr79TRWcP9W-I/s0/ft-dm_fts_index_population.png)

**population_type**

- 1 = 完整母體擴展 (Full)
- 2 = 累加、以時間戳記為基礎的母體擴展
- 3 = 追蹤變更的手動更新 (MANUAL)
- 4 = 追蹤變更的背景更新 (Auto)

**status**

- 3 = 啟動中(Starting)
- 5 = 正常處理(Processing normally)
- 7 = 已停止處理
- 11 = 母體擴展中止
- 12 = 正在處理語意相似度擷取

## 如何查看全文檢索索引包含哪些欄位

[sys.fulltext_index_columns](http://msdn.microsoft.com/zh-tw/library/ms188335.aspx) 這個檢視可以用來查詢全文檢索索引包含哪些欄位。
```sql
SELECT OBJECT_NAME (object_id) AS TableName
, COL_NAME(object_id, column_id) AS ColumnName
, COL_NAME(object_id, type_column_id) AS TypeColumn
, language_id
FROM sys.fulltext_index_columns;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrgMnwfrg2zKr8n4LUTuvAWO74xlXwo8Q90HuXHIEgV8rRCKDjOTK5920uebtq2sf7nXsexlYJGaAr_81j4farJ99854_QgGlZhvfmnK9aF5OWjaHT_HMIXFWt_Dwov2Jmx6353U2tVUw/s0/ft-fulltext_index_columns.png)

## 如何得知全文檢索是如何斷詞的

當你想知全文檢索是如何切割一大段文字的話，你可以使用 [sys.dm_fts_parser](http://msdn.microsoft.com/zh-tw/library/cc280463.aspx) 這個函式來剖析。  
```sql
sys.dm_fts_parser('query_string', lcid, stoplist_id, accent_sensitivity)
```

### 參數說明

- query_string：想要查看斷字的字串
- lcid：地區設定識別碼 (LCID)。
- stoplist_id：停用字詞表的識別碼。如果指定了 0，就會使用系統 STOPLIST。
- accent_sensitivity：控制全文檢索搜尋是否區分變音符號。0不區分，1區分。
```sql
SELECT * FROM sys.dm_fts_parser ('台北市長郝龍斌', 1028, 0, 0) 
SELECT * FROM sys.dm_fts_parser ('台北巿長郝龍斌', 1028, 0, 0)
SELECT * FROM sys.dm_fts_parser ('高雄市長陳菊花', 1028, 0, 0) 
SELECT * FROM sys.dm_fts_parser ('花蓮市長傅琨城', 1028, 0, 0)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSguchWVNj_bNEZOC6LIbNm9zB5qH8DM8PBV5YlrUmi0RmKM7K5e3iOBUktzzsev5qqcgXPhhpF_vdGibC3TGRnWWHUZ245uS3XpxIlMWAChkW1TwiqB1tY_4jrZLxYV_RJfTlAJiiUJ0/s0/ft-dm_fts_parser.png)

全文檢索在操作拆字時，為了提升效效能，也加入了斷詞判斷。  也就是有些字可以合成一個詞，它就不再單獨拆字。  這雖然可以提升查詢的效能，卻也可能導至部份詞語會搜尋不到。  例如上面例子：  

- 你可以搜尋到＂郝龍斌＂，卻搜尋不到＂龍斌＂
- 你可以搜尋到＂陳＂和＂菊花＂，卻搜尋不到＂陳菊＂
- 你可以搜尋到＂台北＂和＂市長＂，卻搜尋不到＂台北市＂；但＂台北巿＂可以

## 檢視目前已向 SQL Server 註冊之斷詞工具的語言清單

```sql
SELECT * FROM sys.fulltext_languages
```

# 使用全文檢索查詢

## CONTAINS and CONTAINSTABLE

[參考這篇](http://vito-note.blogspot.com/2013/06/blog-post_4715.html%20and%20CONTAINSTABLE)

## FREETEXT and FREETEXTTABLE

[參考這篇](http://vito-note.blogspot.com/2013/06/blog-post_4715.html%20and%20FREETEXTTABLE)

## 如何使用同義字

### 同義字設定檔位置

同義字檔案的預設位置為： 
```plan
<SQL_Server_data_files_path>\MSSQL<ver>.<sql_instance>\MSSQL\FTDATA\<language-abbrev>;
eg.
C:\Program Files\Microsoft SQL Server\MSSQL10_50.W7_SQLSVR_2008\MSSQL\FTData\tscht.xml  
```
每個語系都有一個同義字定義檔。

### 如何定義同義字

定義同義字有二種模式：

#### 展開集（expansion set）

**展開集**表示，當查詢集合中任何一個詞，將回傳所有同義詞的查詢結果。

使用`<sub>`定義展開集中的元素，可以多個。

下面例子，若查詢author，也會回傳含writer和journalist的結果。

```xml
<expansion>
<sub>writer</sub>
<sub>author</sub>
<sub>journalist</sub>
</expansion>
```

#### 取代集（replacement set）

**取代集**表示，當查詢指定元素的詞，將回傳取代項目的查詢結果。

使用`<pat>`定義指定元素，可以多個。

使用`<sub>`定義替代項目，可以多個。

下面例子，若查詢W2K，將回傳含Windows 2000和XP的結果，但是不含W2K。
```xml
<replacement>
<pat>W2K</pat>
<sub>Windows 2000</sub>
<sub>XP</sub>
</replacement>
```

### 如何載入同義字

下列範例會剖析並載入繁體中文同義字檔案

```sql
EXEC sys.sp_fulltext_load_thesaurus_file 1028;
```
**注意：bug**  
使用 sys.sp_fulltext_load_thesaurus_file 時，若載入的同義字檔案中含有雙位元組文字（double-byte character set），如中文、日文，可能會發生以下錯誤：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpQ0f654raMat47_CRwY1TEn8Q-bYH0omijbz-F5Wpf1479nPZikK9LPSs2BzIDFieSvEU0ekoIlzW8ds2tMgoPBxxekYwhNmE9dbDWrvmCe2BVDcw9pQxyGnQVflFbmpKJDNBkiA2wkc/s850/ft-load_thesaurus-error.png)

可以參考[這篇ＫＢ](http://support.microsoft.com/kb/2014749)的解決辦法。

### 如何查詢同義字

**語法

```sql
'FORMSOF(THESAURUS,"查詢的字")'
```

**範例**

```xml
<?xml version="1.0" encoding="utf-16"?>
<XML ID="Microsoft Search Thesaurus">
<thesaurus xmlns="x-schema:tsSchema.xml">
<diacritics_sensitive>0</diacritics_sensitive>
<expansion>
<sub>市長</sub>
<sub>巿長</sub>
</expansion>
</thesaurus>
</XML>
```
```sql
EXEC sys.sp_fulltext_load_thesaurus_file 1028;
```
```sql
SELECT * FROM Documents WHERE CONTAINS(Content, 'FORMSOF(THESAURUS,"市長")') 
```

因為定義檔中將＂市長＂和＂巿長＂定義為同義字，所以二者結果都會出現。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9AQarTKnPCLYYv7pm-Sr5c3SRlDD3yqmAL1zwybyIm5y4HBOPqjaO5nfB0N77HVeVjnbqZwhOai1TkB1tdC7u_Kn6tgJuctOmRtdhcji2ekoD9jAKFLLMhd2CVx_j7U-DvaTFWqGeQAY/s0/ft-using-thesaurus.png)

## 如何使用停用字

暫略

# 全文檢索搜尋的行為變更

SQL 2012 針對全文檢索的搜尋行為，進行些微的變更。它使用新版的斷詞工具和字幹分析器，所以其搜尋結果可能與舊版的 SQL Server 會有所不同。  例如：SELECT * FROM sys.dm_fts_parser ('"台北市長郝龍斌"', 1028, 0, 0) 在不同版本中回傳結果如下：  

SQL 2008

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPYuQSUzLjMXu8Y4laG994Z4MlT9iNK91SPy4sztkH1NcyddjSj9JxJgTZQ2nA6OF7s33oDUvqP2mRmq9HDYFSfeQll93gAQXqhowPMa3Q1l2pQNEXTovck2NJmL8a3KHs9tMLcBmhYXc/s0/sql-fulltextsearch-2.png)

SQL 2012

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXloxXsTKE9o_THVqokXE_gKDzk4qojpLw3sxZKK8WtV0oYd7kfqtinHw8uyXTAhU2CmbHfPS1ouMYs6k3Qt_PBH9uppgOClWOwC48RKAEsGejKevHjlfBJDC9Z6nshQ7DIUQ45nZ05fo/s0/sql-fulltextsearch-1.png)

所以如果資料表中的 Content 欄位明明含有「郝龍斌」三個字，在 SQL 2008 中，使用以下查詢是得不到結果的，但使用 SQL 2012 可以。
```sql
SELECT * FROM tblDocuments
WHERE 
CONTAINS((Content,Title), '郝')
```

更多相關內容請參考 MSDN：[全文檢索搜尋的行為變更](http://technet.microsoft.com/zh-tw/library/ms143272.aspx)
## 參考資料  

- [全文檢索搜尋 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ms142571.aspx)
- [建立及管理全文檢索索引](http://msdn.microsoft.com/zh-tw/library/cc879306.aspx)
- [建立及管理全文檢索目錄](http://msdn.microsoft.com/zh-tw/library/bb326035.aspx)