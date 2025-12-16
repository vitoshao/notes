---
title: 資料庫安裝規劃
layout: default
parent: Install and Configure
nav_order: 2
description: "資料庫安裝規劃"
date: 2013-07-04
tags: [SQL,Install and Configure]
postid: "762595723168741334"
---
# 作業系統

|  |  | SQL Server 2012 (32) | SQL Server 2012 (32) | SQL Server 2012 (32) | SQL Server 2012 (32) | SQL Server 2012 (32) | SQL Server 2012 (64) | SQL Server 2012 (64) | SQL Server 2012 (64) | SQL Server 2012 (64) | SQL Server 2012 (64) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Ent | Std | Web | Dev | Exp | Ent | Std | Web | Dev | Exp |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Windows Server  <br>2012 (64) | Datacenter | V | V | V | V | V | V | V | V | V | V |
| Standard | V | V | V | V | V | V | V | V | V | V |
| Essentials | V | V | V | V | V | V | V | V | V | V |
| Foundation | V | V | V | V | V | V | V | V | V | V |
| Windows Server  <br> 2008 R2 (64) | Datacenter | V | V | V | V | V | V | V | V | V | V |
| Enterprise | V | V | V | V | V | V | V | V | V | V |
| Standard | V | V | V | V | V | V | V | V | V | V |
| Web | V | V | V | V | V | V | V | V | V | V |
| Foundation |  |  |  |  | V |  |  |  |  | V |
| Windows Server  <br>2008 (64) | Datacenter | V | V | V | V | V | V | V | V | V | V |
| Enterprise | V | V | V | V | V | V | V | V | V | V |
| Standard | V | V | V | V | V | V | V | V | V | V |
| Web | V | V | V | V | V | V | V | V | V | V |
| Windows Server  <br>2008 (32) | Datacenter | V | V | V | V | V |  |  |  |  |  |
| Enterprise | V | V | V | V | V |  |  |  |  |  |
| Standard | V | V | V | V | V |  |  |  |  |  |
| Web | V | V | V | V | V |  |  |  |  |  |
| Windows 8 (64) | Home |  | V |  | V | V |  | V |  | V | V |
| Professional |  | V |  | V | V |  | V |  | V | V |
| Windows 8 (32) | Home |  | V |  | V | V |  |  |  |  |  |
| Professional |  | V |  | V | V |  |  |  |  |  |
| Windows 7 (64) | Ultimate |  | V |  | V | V |  | V |  | V | V |
| Enterprise |  | V |  | V | V |  | V |  | V | V |
| Professional |  | V |  | V | V |  | V |  | V | V |
| Home Premium |  |  |  | V | V |  |  |  | V | V |
| Home Basic |  |  |  | V | V |  |  |  | V | V |
| Windows 7 (32) | Ultimate |  | V |  | V | V |  |  |  |  |  |
| Enterprise |  | V |  | V | V |  |  |  |  |  |
| Professional |  | V |  | V | V |  |  |  |  |  |
| Home Premium |  |  |  | V | V |  |  |  |  |  |
| Home Basic |  |  |  | V | V |  |  |  |  |  |
| Windows Vista  <br>(64) | Ultimate |  | V |  | V | V |  | V |  | V | V |
| Enterprise |  | V |  | V | V |  | V |  | V | V |
| Business |  | V |  | V | V |  | V |  | V | V |
| Home Premium |  |  |  | V | V |  |  |  | V | V |
| Home Basic |  |  |  | V | V |  |  |  | V | V |
| Windows Vista  <br>(32) | Ultimate |  | V |  | V | V |  |  |  |  |  |
| Enterprise |  | V |  | V | V |  |  |  |  |  |
| Business |  | V |  | V | V |  |  |  |  |  |
| Home Premium |  |  |  | V | V |  |  |  |  |  |
| Home Basic |  |  |  | V | V |  |  |  |  |  |

# 記憶體

|  | 最小值 | 建議值 |
| --- | --- | --- |
| Express 版 | 512 MB | 1 GB |
| 所有其他版本 | 1 GB | 至少 4 GB，並應隨著資料庫大小增加以確保最佳效能。 |

# 處理器

### 處理器速度

|  | 最小值 | 建議值 |
| --- | --- | --- |
| x86 處理器 | 1.4 GHz | 2.0 GHz 或以上 |
| x64 處理器 | 1.4 GHz | 2.0 GHz 或以上 |

### 處理器類型

| x86 處理器 | Pentium III 相容處理器或更快的處理器 |
| --- | --- |
| x64 處理器 | Intel Xeon、Intel Pentium IV、AMD Opteron、AMD Athlon 64 |

# 硬碟空間

## 系統服務所需空間

略

## 資料庫空間的成長與壓縮(Growth and Shrink)

You can view the size of the database and the space available for the database on the **General** page of the **Database Properties** dialog box,

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3JwUZTgFpyOHuoomCJ-RNrd7DTxs7XRSecs2A1VAe474iKGIvZ9zo0EWUD9-MvkYg8etgu7J-kktK4aq4TLuaCp1cBa2Bu0QKoPMUKRopGrN4tlo9polhhnPtvMPO1F-xY0D5iXfOzVk/s0/sql-shrinking-and-growing-database-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyJETAMGJzlm0no4WGupMifjSg71PDUJGDdb88BSRacUxseVq4nBQ3NEoE8fQLU9C7X0dok62WfDiz93Dc_e8e11PsPb9cDXCDvXUCYbrI8Id6QrP5KZYRk0WdbjBi26QoqkszGuisOiw/s0/sql-shrinking-and-growing-database-2.png)

You can use the [sp_spaceused](http://msdn.microsoft.com/zh-tw/library/ms188776.aspx) stored procedure to determine the database size and free space available.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8CUjoAUAZ1r7LnF5k7UM0rHlOoALgoX9vq3_d1jsb75pV7ttuuTY_SI9nPlh0qpP-p40EbEyCKWiTLJoCf1CMaMPmQ23WyGWyJdXWLfFAyPMQN5zTNiNrKPEwAcIeTRO_3JKoJIMB2sQ/s0/sql-sp_spaceused.png)

### Increasing Database Size

#### AUTO\_Growth

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhj505Bm9U7JdOxhfr_G4XU3JnQ13_9Z_10aqnAXo8A3ZmiojCTjYBC5meKyd-hmTQICl1ENobPml8r-wr_SZPW3BqxhrqgA9o01ahyexgF2H6cWjdhoQF2YZJf9tz55v4yT8Ix1PMa_Nk/s0/sql-auto-growth.png)

#### increase the size of database files manually
```sql
ALTER DATABASE [AdventureWorks2012] MODIFY FILE
( NAME= N'AdventureWorks2012_Data', SIZE = 256000KB )
```

### Decreasing Database Size

#### AUTO\_Shrink

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2I_GigYkinJVCY7Tl-ieN2Uzb__sHO7jGtGRXSZYSlpqeA37CHGyBs73tOLi7VKvzbfqhLONqpz2VAj2a1U-LtdzLKN62-8lQ95eLIrx_Med7P03AZaZjGi85pXLpxRhYFAynw6utQSc/s0/sql-auto-shrink.png)

#### decrease the size of database files manually

若已設定 AUTO\_SHRINK ，資料庫引擎會自動壓縮未使用的空間。不過，你也可以使用以下方法來手動進行壓縮。

使用 SSMS 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhD-bhOtRN-4-K1au_QZqbBrGvfyKHZnX_gpYGn9-PX-JaM0MaFfn398QpOi6RHVTd_i0qZGAz6_TKrU_lS6wdSeG1L4J1fHgVtS3hOzzkorJeduyiX7LkWdhEPgjNgw7tqPXEtN2VK5sQ/s0/sql-shrink-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2bPjJ-ZVh_p0YIzUrU291dGhyU1ZTFVXNK6bZguWZ4LpUQr8xEJOIIftbhSP8Yf4JEEB0-OeuAuQ67PUqbHu0CdhI-rVGEwtz07ykS1AhQbeDA13YoclzrVd3vMCZTXYoFUWPLO3ptu0/s0/sql-shrink-2.png)

使用 [DBCC SHRINKDATABASE](http://technet.microsoft.com/zh-tw/library/ms190488.aspx) 
```sql
DBCC SHRINKDATABASE(N'AdventureWorks2012')
GO
```

PS.自動壓縮發生的時間：

- 只有在資料庫設為 SIMPLE 復原模式或備份記錄時，AUTO\_SHRINK 才會縮減交易記錄的大小。
- 當超出 25% 的檔案包含未用空間時，AUTO\_SHRINK 選項便會壓縮檔案。

## 規劃新資料庫

### 硬體規劃

一般來說，我們會使用 [RAID](http://zh.wikipedia.org/wiki/RAID) (Redundant Array of Inexpensive Disks) 技術來增進容錯能力（fault tolerance）與效能。  

Many enterprise-level SQL deployments use :  

- RAID 1+0 ：stripe of mirrors
- RAID 0+1 ：a mirror of stripes

The Windows Server 2008 and Windows Server 2008 R2 operating systems support software RAID 0, RAID 1, and RAID 5, of which only RAID 1 and RAID 5 offer any fault toleranc。

### 資料庫檔案規劃

#### 資料庫檔案

基本上，每個 SQL Server 資料庫都有兩個作業系統檔案：資料檔與記錄檔。  資料檔包含諸如資料表、索引、預存程序以及檢視等資料和物件。 記錄檔包含復原資料庫中所有交易必要的資訊。 資料檔可以組成檔案群組，以方便配置及管理。

SQL Server 2012 databases use three types of files :

- **主要資料檔（Primary）：.mdf**  
Every database has a primary data filen
- **次要資料檔（Secondary）：.ndf**  

- Secondary is optional and you can have more than one Secondary file
- Secondary spread data across multiple disks
- Using Secondary data files and RAID technologies to impove performance.
- Using Secondary data files to increase the size of a database
- **交易記錄檔（Transaction Log）：.ldf**  

The Transaction log information can be used to recover the database.

#### 檔案群組

- **主要檔案群組（primary file group）**  
這個檔案群組稱為主要檔案群組，名稱固定為「PRIMARY」。  每個資料庫必有一個，且只能有一個，內含主要資料檔。
- **使用者自訂檔案群組（file group）**  
其他檔案群組，名稱由使用者自訂，數量可以很多個。

每個資料檔都要配置一個檔案群組。  主要資料檔只能配置在主要檔案群組，  次要資料檔若沒配置到其他檔案群組，則都算在主要檔案群組。  

此檔案群組可能包含主要資料檔和未放入其他檔案群組的次要檔案。 可建立使用者自訂檔案群組來將資料檔群組在一起，以利管理、資料配置和放置之用。

## 容量限制

- 每個資料庫最大空間：524,272 TB。
- 每個檔案最大限制：16 TB。
- 每個資料庫執行個體，最多可以架設 32,767 個資料庫。
- 每個 PK 最多可以含有 16 個欄位，一個 table 最多可以含有 253 個 FK 。

更多詳情請參考 [MSDN : SQL Server 的最大容量規格](http://msdn.microsoft.com/zh-tw/library/ms143432)

# Identifying a Standby Database for Reporting

SQL Server 2012 supports several types of standby database.   

#### 鏡像資料庫(mirror database)

You can use database mirroring to maintain a single standby database for a corresponding production database.

#### 次要資料庫(secondary database)

You can also use log shipping to maintain one or more warm standby databases.

# Identifying Windows-Level Security and Service-Level Security

在安裝 SQL Server 2012 時必須設定 SQL 的執行帳戶，不同的 Service 還可能需要不同的執行帳戶。  依安裝平台的不同，它預設值的執行帳戶也不盡相同：  

| 服務名稱＼作業平台 | Windows Server 2008 R2/ Windows 7 | Windows Server 2008 / Windows Vista |
| --- | --- | --- |
| Database Engine  <br> SQL Server Agent  <br>  Analysis Services  <br>  Integration Services  <br>  Reporting Services  <br>  SQL Server Distributed Replay Controller  <br>  SQL Server Distributed Replay Client | Managed Service Account  <br>  或  <br>  Managed Loca Account  <br>  (Virtual Account) | NETWORK SERVICE |
| SQL Server Browser | LOCAL SERVICE | LOCAL SERVICE |
| SQL Server VSS Writer | LOCAL SYSTEM | LOCAL SYSTEM |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0rWJuK5KnOa3uMUyXi4JvB5zC8llFqLPlfJpQ0KUpjJiNJfQjUq9KfyKx8EQ5SkXdQcoew0ZNSidq7lVUHImsA_fpnTMHys-ZlR6EuFTMjJUClMhDUuBgBKKDpdo3yGVivKdeIe8-HKo/s0/sql-service-account.png)

從 Windows 7 與 Windows Server 2008 R2 開始，新增了兩種特殊的帳戶類型，分別是「受管理的服務帳戶(Managed service accounts)」與「虛擬帳戶(virtual accounts)」，可有效隔離各種網路服務以提升安全性。  

### 虛擬帳戶(virtual accounts)

因為原本使用的 NETWORK SERVICE 這個帳戶，同時有多種服務在使用，如果其中一個服務發生安全性的問題，駭客就有可能利用 Network Service 再引響其他的服務。  有了「虛擬帳戶」的概念，各種不同的網路服務不再需要共用同一組 NETWORK SERVICE 身份識別，每個服務都有自已的執行帳戶，可以提升整體的資訊安全。  

在 Windows Server 2008 R2 及 Windows 7 中的虛擬帳戶又稱「受管理的本機帳戶(Managed local accounts)」，可提供下列功能以簡化服務管理作業：  

- 不需要密碼管理。
- 可以在網域環境中，使用電腦識別以存取網路的能力。

### 受管理的服務帳戶(Managed service accounts)

以往我們要讓 Windows 上面的 Services 能夠存取區域網路上的相關資源時，則會建立一個 Domain Account 來讓服務使用，但有時因為資安管理需要去變更密碼，或者是要不允許讓別人使用這個帳號去登入，則會變得很麻煩。  而 Managed Service Accounts 就是因應這樣的需求下所產生的，當我們在 AD 上面建立一個這樣的帳號時，這個帳號只能綁定給單一的電腦所使用，而且不用受到密碼變更的引響，也可以很方便的設定權限。  

在 Windows Server 2008 R2 及 Windows 7 中的「受管理的服務帳戶」是受管理的網域帳戶(domain accounts)，可提供下列功能以簡化服務管理作業：

- 自動密碼管理。
- 簡化的 SPN (Service Principal Name) 管理，包括將管理功能委派給其他系統管理員。
## 參考資料  

- [安裝 SQL Server 2012 的硬體和軟體需求](http://msdn.microsoft.com/library/ms143506.aspx)
- [SQL Server 2012 版本支援的功能](http://msdn.microsoft.com/zh-tw/library/cc645993.aspx)
- [Introducing Managed Service Accounts](http://technet.microsoft.com/zh-tw/library/dd560633.aspx)
- [Service Accounts Step-by-Step Guide](http://technet.microsoft.com/en-us/library/dd548356.aspx)
- [\[五餅二魚工作室\]Managed Service Accounts](http://www.dotblogs.com.tw/jamesfu/archive/2012/07/04/managedserviceaccounts.aspx)