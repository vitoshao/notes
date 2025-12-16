---
title: 系統升級
layout: default
parent: Install and Configure
nav_order: 4
description: "系統升級"
date: 2013-10-03
tags: [SQL,Install and Configure]
postid: "5830952654043087710"
---
# 認識資料庫版本

## 資料庫版本

若要查詢 SQL Server 的產品版本、版本編號等，可以使用以下的範例程式碼：
```sql
SELECT 
LEFT(@@VERSION, CHARINDEX('-', @@VERSION)-1) N'資料庫版本' , 
SERVERPROPERTY('Edition') N'執行個體版本',
SERVERPROPERTY('ProductVersion') N'版本編號',
SERVERPROPERTY('ProductLevel') N'版本層級',
DATABASEPROPERTYEX('master','Version') N'內部版本號碼'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTRA2wY7eK8Dr8ckXX4F1ZXiwrvWSa2vpeAX6QCHGrrYJYS_e6QoTp6bDmTgjgnPXf5llz-TR9L-yAdkw84F_pK4W0WX_h2QTWyrkPbUBBGdi_XFB_Ht5KVzz0WsNHLNqKdGtgJFCU9gA/s0/sql-version-property.png)

## 內部資料庫版本

內部資料庫版本是資料庫內部使用的版本號碼。

你不能自行變更資料庫的內部版本，當你由舊版的 SQL Server 資料庫透過**附加**或**還原**的方式轉換到新版 SQL Server 時，系統就會自動升級資料庫的內部版本。  

內部資料庫版本與資料庫版本的對應關係如下所示：

| 內部資料庫版本 | SQL Server 版本 |
| --- | --- |
| 539 | SQL Server 2000 |
| 611/612 | SQL Server 2005 |
| 655 | SQL Server 2008 |
| 661 | SQL Server 2008 R2 |
| 706 | SQL Server 2012 |

### 查看資料庫現在的內部版本為何
```sql
SELECT compatibility_level
FROM sys.databases 
WHERE name = 'VITO2012';

SELECT DATABASEPROPERTY('VITO2012', 'Version') 資料庫內部版本; 
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCZJldcn1eUesxkTXcv48SjgtGCEQ6Wjafnzs8XHLsggyrIwLc8lfcsNUuCB677d2nhuruhweXClhucSefmFPEsrZ8Jr3GQ8YUIh9-nj1qaV4pI95OH9jeSg6jmFTASPsz6jFDrmGncOw/s0/sql-version-1.png)

### 查看當初建立資料庫的內部版本為何
```sql
USE [VITO2012]; 
DBCC TRACEON (3604);   
DBCC DBINFO; 
DBCC TRACEOFF (3604);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUT0FH-i0JFWL5WEosb_vWaw_7K0eZGzbhmCKJzMudxDoddiHfO_JDZwio-eSpm8vmObNmxpGZQ4RENb58xMRhcby42-9MhSf3ww125UiaDHMgxhvuJ8XAKGwbFW1AYiAN8pdd5gOArqI/s0/sql-version-2.png)

### 執行個體版本與內部資料庫版本不是一對一

PS.  
執行個體版本編號與內部資料庫版本不是一對一的，通常每個SP，都會有一個執行個體版本編號。  
例如，SQL Server 2008 R2 資料庫的內部版本號碼是：661，其版本編號是：10.50.1600.1。  
若安裝累積更新套件 7 (CU7) 之後，SQL Server 2008 R2 的資料庫內部版本號碼還是 661，但版本編號則會提升到：10.50.1777.0。  
若安裝 SP2 之後，內部版本號碼還是 661，但版本編號則會提升到：10.50.4000.0。

## 相容性層級（COMPATIBILITY\_LEVEL ）

相容性層級用來描述資料庫版本的相容性。  當建立新的資料庫時，預設會使用該版本預設的相容性層級，例如，在 SQL Server 2012 資料庫的預設相容性層級為 110 。  你也可以指定較低版本的相容性層級(最低90)。  

相容性層級只會影響指定之資料庫的行為，而不會影響整個伺服器的行為。  例如，如果你將相容性層級由 110 降為 100 ，你將無法使用 TRY\_CONVERT 關鍵字，但不影響 SQL 2012 執行個體的行為。  

以下列表為目前 SQL 各版本的相容性層級：

| 相容性層級 | SQL Server 版本 |
| --- | --- |
| 80 | SQL Server 2000 |
| 90 | SQL Server 2005 |
| 100 | SQL Server 2008 及 SQL Server 2008 R2 |
| 110 | SQL Server 2012 |

### 檢視相容性層級
```sql
USE AdventureWorks2012;
GO
SELECT compatibility_level
FROM sys.databases 
WHERE name = 'AdventureWorks2012';
GO
```

在 SSMS 中，由資料庫屬性中也可以直接查看與變更。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4NShXL5x7zRPmDvq_tRz_zozYYMKUzA4UBnSFj0rClWrrl2xW7LWIIVJMoUzjS7tUim_Ur2uNGjXUZvuh-xe10cbaLT64FcnU37nuzXE6moMCbx9kFrTTKbelMBWQkersgdz6u-AkSFM/s0/sql-compatibility-level.png)

### 變更相容性層級

當使用備份還原方式，將舊版資料庫轉到較新版資料庫時，其相容性層級並不會自動更新。  如果變更成新的相容性層級，可以如下操作：  
```sql
ALTER DATABASE AdventureWorks2012
SET COMPATIBILITY_LEVEL = 110;
GO
```

# 複製資料庫

要將資料庫複製到另一個執行個體，有以下幾種方式：  

- 使用「卸離附加」方法。（此方法必須離線操作）
- 使用「複製資料庫精靈」。
- 使用「備份還原」方法。
- 使用「指令碼精靈」產生指令碼來發行資料庫。
- 使用「資料庫發行精靈」產生指令碼來發行資料庫。

## 使用「複製資料庫精靈」

這個方法相當方便，可以用來複製、移動、升級資料庫，也支援在目的伺服器建立登入資訊。  

#### 不過，它也有一些限制如下：

- Express 版中沒有複製資料庫精靈。
- 資料庫升級後，無法降級至舊版。
- 無法複製系統資料庫。
- 無法移動全文檢索目錄，您必須在移動之後重新擴展索引。
- 
- 

#### 必要條件

確定目的地伺服器上已啟動 SQL Server Agent。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUSiGlWTLeMjnQy8PfHOCL5GKmH42LoVcxifmkbIIF_L5bLR1VWlbmBtGGQU97WMdIH9krilBym0u6HPN2ZbbSekFPlcoN3kP65w0fBS27PV9Ahx6awVjcOdYVKR6hl_7bOMRjBv0reAI/s0/sql-copy-database.png)

## 使用「備份還原」方法

這個方法有幾點要注意：

- 無法將新版資料庫還原到舊版資料庫。
- 還原時會自動建立資料庫檔案，依預設，會使用原備份檔案相同的名稱和路徑。
- 你也可以指定還原資料庫的裝置對應、檔案名稱或路徑。
- 操作還原的登入，會成新資料庫的擁有者，你可以使用系統管理員或新的資料庫擁有者變更資料庫擁有權。

## 使用「指令碼精靈」產生指令碼來發行資料庫

您可以使用 [產生和發佈指令碼精靈] ，將整個資料庫或個別的資料庫物件發行至新的執行個體。 

開啟 [產生和發佈指令碼精靈] 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnsZ7iyqI99CVRf2rVHtbaAIgaG6uZvgQp8gYAl48402JiCzjlRscCDuf2pPHRDJNi0mTgam_PeIkF_vovsrlHtglyspUoKH-LsQQVuPlDbWC6u_ozbj8fl2Wyu_3XqUijFZ8t-xC1HEc/s0/sql-gen-secipt-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEid2Drjf1s329K4xN1hMnDaKTS4z9n0gFNrkxzU1oChKHx7YglDoAXGJyzTVKCGY3SIiKBw-suufHkRCaq9eS3EDlVdITJGDl8Aow-URfoDHeGsqEjYrtC9T8eWT4J9XBm0bmPEMdttKt4/s0/sql-gen-secipt-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5_k4PT91aJJUqsIAPTkI-WipqYRyO_Up664XWIlbbP16HarWaeKLZZR4JGLGAy_hW9YD3awrLiPN1Jd4vN1ukLgJYq7wtAAxalHoasGIGIEWJR8DAp9D44UcTqeZTPmCQg7IbBGi2wM8/s0/sql-gen-secipt-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhpJ2711XwlZk6HZUSZK0yC29rSlW9R96nUbibn28ZMg8OOO23u7Fw8Wn9ei-Zn5hP6TL1i0qDZam-KaubFNwcSzOJimOpZGGs7QvOzhr44L0qramLoCdI5ySX9HMNaQK5Ev1SnKuGmmls/s0/sql-gen-secipt-4.png)

在進階功能中，變更以下選項。因為我們要將資料一併輸出，所以指令碼類型要設定：「結構和資料」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7ytdH9e_JVkccJiqPRam7v-RtJ1P9DtA2HnNrqF4yTDpyeiDx4ArwumBQtqT92D0ukeWcNtKE4t_DuH9Gh5p5HcmXqo6U7PuOZbr4oyVYyjKM47OJEqYYf29TiL9-c1375qUJyjBo1S4/s0/sql-gen-secipt-5.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLLr52qCVB0q63oBjhdaY3vwqrVPHBAOTyNOXaE3tBmhhqWjD0Pv68gn5KAevJGQJcDb15_W0VUKx-HPgXYbzbRT_sX7rgLTlTdGr3NaDDrtFxVpBsCFv3iRffE-4z8tMIMkGnDCO5eSY/s0/sql-gen-secipt-6.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsfJFLWTqwLeKiJDgtq9asmBE42kmZ-EE7Rn1sftRToktJyliYwtgmmTrmYUqOk-LGvLPQGUD4B3oFJNH7XrELFBptJEzMZP2wh_9aVXzWnYrVTv6mS8XSJxvHqifvHD6S-ZKGey3OHXM/s0/sql-gen-secipt-7.png)

最後，將輸出的 T-SQL 指令檔拿到到目的資料執行即可。如果預存資料庫檔案的路徑不同於原先的資料庫，記得在以下的地方變更。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmPGmhrr8PJ91y1OP9PR6GoFjGu3pIxhPvmYHIfweqytzGySWSXJFdwDYvO3A9lYHGhyBAT-Y-tavlF6-1kpbvEBGLSMUUqNmIsTiauSgvTeOPt-y9jwp4PmxlpE_ozrOKJDktY7MaYmg/s850/sql-gen-secipt-8.png)

PS  
此方法不支援下降版本。

PS2  
不知是否因為版本更新的結果，目前這個版本（Microsoft SQL Server 2008 R2 (SP2) ）可以轉換出較舊版本的腳本指令碼。而 SQL 2012 Express 版也可以。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1-bL_WIbzDJa-ItxE74IXuPmUhB7KTW-dRWlVr_edZHAjkBxFwmjgR3b1BDNQVvUu8TQE04aNVON8xSiQfD0pU6TiBgd__1przV6kuqROBfKYxDTZxNh8aqA1xfTQm4sDTb6yFkKIG9w/s0/sql-gen-secipt-5-2.png)

## 使用「資料庫發行精靈」產生指令碼來發行資料庫

使用 [資料庫發行精靈] ，類似 [產生和發佈指令碼精靈] ，它也是目前唯一支援下降版本的發行方法。 

目前最近版本為 SQL Server Database Publishing Wizard 1.4 版，並內建於 Visual Studio 2010 之中，其安裝好之後的程式所在路徑為 C:\Program Files (x86)\Microsoft SQL Server\90\Tools\Publishing\1.4\SqlPubWiz.exe。

執行 SqlPubWiz.exe 程式，開啟 [資料庫發行精靈] 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmuhtr71N_My5-EoDz_XfJxAdKLZJg_Vk1R-fmN44V-svzkDY6exMbiL7ftTiIfi2JUm0r41aX150rtkV9yAEkelq9ow5qwQxymrmWw2bddQKrVk75M39jUxpet3ODhc3FUN2YD6TL9iU/s0/sql-pub-wizard-0.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgaNtVNJ1tCMdABD_FlffC-X-68TfZdhrpY0jHAU1tg8LQF2UAchyit-ZFp4BvPEN0dA73ndXbJMitS3r1UYdTUcoGrqErdQpsZGO2N0guuKTKJ2Ri6DvOt8zV70bJbQf33wskNJkTfP0I/s0/sql-pub-wizard-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4G_DvbUt5OQXcP1ENn2Ri7foZYtjGPL9YfWt0x6EdyARGoqHANfcHnGmSlczXANrWuLP5c4kCbsNWmY810gIiRLt5Ytmh2o0JmUAXkR5c6xiYxJn7aqpiMvOGchdZI299bcOK_bbyAYc/s0/sql-pub-wizard-2.png)

選擇要發行的資料庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizYSmkamIA2jN1I-gaKCQcJnT9q0Rl4lYYDH1wMAG6T5_o_aOJ0-jwIsh4UMbBn3iHtBGxEn8iEvI9xr00BM2xeyRJUSJWmxbFjzswoQnrYzJhMpR5eNMRj4l1g9q4RP0E6ckwHjj7Odk/s0/sql-pub-wizard-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiq7ZYMQHqoEaRvQtLm6_9kmD62r66rA7ifixXXPI7dMjlryVtC0iVNjzGx8DKYiPcclpPBVmkcyWrpMVvZmYV-Zqe8eBg3vlW3rfYkGIWywYyy5KPbh-4Xby3KWe1n06DrgiqocG6W1TI/s0/sql-pub-wizard-4.png)

這個步驟最重要，它會依你指定的目標資料庫版本來產生相容的指令碼。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoqCGS-cxpU5YD8oYBS6Y5svgfktZ1Gbznf1ot3gY6c3cJLs22QwLHvskjJ6LruCobQHAekrFfZRrHcy8BzNSVcyFzJAVUdOM_6yfV0s1Vvje_An_R09ZCOyTQ1FTS9rOECpcTQ1mjeSo/s0/sql-pub-wizard-5.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhc92xyn7Q4OKSSNy7sRVtUroF5K2A43GnPaPDdifxjPT9Le5qmO2Rzp19QXS1GOsxZ9D9GJStEnpwG3jpEO9uRAMZd8Q_lJSGyzPu6dpnA6k13L13aKiIcWu8423ljZyXPEhSUxQkoKOE/s0/sql-pub-wizard-6.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhL3KSZXzu4FguxyLWxOYOwv7u9E85BR4CmqxHG5B9wJcJyzEUPJsNthN1ZFQhNYdgGy6_4gbE8ZzgMEK3-UWE3STMBUoHnsqLw898NTNOgqfFzF7o_jMVyPErVuCZybTunDeuN0qQedsY/s0/sql-pub-wizard-7.png)

最後，將輸出的 T-SQL 指令檔拿到到目的資料執行即可。

PS  
這個指令不會建立資料庫，你必須在新的環境先建立資料庫，再執行這個腳本。

## 將 SQL 2012 的資料庫轉至 SQL 2008

上面的「資料庫發行精靈」工具，提供 SQL2008 轉 SQL 2005 或 SQL2000，沒有 SQL2012 降轉至 SQL2008 的服務。  如果要 SQL2012 降轉至 SQL2008 ，就直接使用 SQL2012 中的[工作]-&gt;[產生指令碼]功能即可。  這個方式雖然不會直接發行到目標資料庫去，但是，你只要將產生的Script在目標資料庫執行一下即可。  要注意的的，在進階選項中，必須挑選目標資料庫的版本，這樣子才會產生符合版本的Script。  還有，編寫的指令碼可選擇「結構描述和資料」，這樣子Script才會包含結構描述和資料。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhACo53JeGxTUJ34ESJGopCAVjZIaRJavA0IDk5V_qAt56RnDIBAAx3LZKhN-Y7O7YL0KNw_-p6Wh-w2wcEUC_fo97p5ApUkF__R97t_yQAhMYSHpbJ1GlGDNga0ifXtvuBKFc1qvUSgUM/s0/sql-downgrade-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0rxHtbX9dQh7weA8ssQTF6rt1NjKTQf1dP2g-jUKR932KWqz4hvGQcz-1y1QmYXjEeO2pJ5i9_Nhu0_-325QlsZbNdgUmOCrmcsxZP5GlGtgwV-8SU4Bx93H7R8JLIQ_hCQPKA0v_VTw/s0/sql-downgrade-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNR97RyVLXcI8qhON3V4ZfX6hIeFRlFTSA67qwSe1wuv0rSAm7um2w-lG49rXoqTL_HdbGeVv6svXhuPhmhfoXZp75AL_rJtkWdqG2Gmuesmok2_FEGPWxf9SYJEMgvjIB37l80QSTK08/s0/sql-downgrade-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiB-wgxSvE2ot4wh55ISgPB1AsN1qVk4K02P8C5h5rq6q-Za_9b_nhBpO7rC8tc8x7OTes00vFajAFjk0XvVW_i5EPycPG4UV_N6oIMv-fm7HwfDixgUdJbQH_H2t7BzjETRawmNcU_xzY/s0/sql-downgrade-4.png)

# 升級 SQL Logins

資料庫在轉移時，可以連同使用者資訊一併轉移。  可是新的伺服器不見得有相同的登入，這將發生孤兒問題（orphaned user）。  也就是使用者無法對應到SQL登入。  

所以你可以替需要的登入建立Scrip，再拿到新伺服器執行即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzojTBySSaaDXTjLox6mNiiPqYVl5e_Q37aJPyRrenL7tRn5KkmGDArwGwW1HAGQOUJn0r263ZvMlxBjQK0CiM9dcXxN6UGmfkz_H-4qbkkOn3QDgzH8qKoZ4EbfgOh2iqfF91Ap1ObK0/s0/sql-gen-login.png)

不過這個方法建立的登入將不會包含密碼資訊，你必須另外重新設定密碼。  如果不想這麼麻煩，還可以利用 sp\_hexadecimal 和 sp\_help\_revlogin 這兩個SP來處理。  詳細方法可參考這篇BLOG：[轉換不同SQL Server的登入和密碼](http://www.dotblogs.com.tw/ricochen/archive/2012/12/13/85615.aspx)
## 參考資料  

- [\[Will\]如何利用「資料庫發行精靈」將新版 SQL 資料庫降級成舊版](http://blog.miniasp.com/post/2011/05/21/Using-Database-Publishing-Wizard-downgrade-SQL-Server-Database.aspx)
- [複製資料庫至其他伺服器](http://msdn.microsoft.com/zh-tw/library/ms189624%28SQL.110%29.aspx)
-