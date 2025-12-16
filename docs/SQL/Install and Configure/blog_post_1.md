---
title: 資料庫安裝
layout: default
parent: Install and Configure
nav_order: 1
description: "資料庫安裝"
date: 2013-07-03
tags: [SQL,Install and Configure]
postid: "7688812231339974271"
---
# SQL 提供的服務

SQL 提供許多服務，你可以依專案或企業的需求選擇性安裝。  這些服務依功能可區分成二種類型：「執行個體功能」和「共用功能」。  

## 執行個體功能（instance features）

### Database Engine Services

提供資料的儲存、處理和控制存取以及快速交易處理

### Analysis Services

為商務智慧應用程式提供線上分析處理 (OLAP) 和資料採礦功能。

### Reporting Services

管理、執行、轉譯、排程及傳遞報表。

### SQL Full-text Filter

啟動全文檢索篩選背景程式處理序的服務，將會針對 SQL Server 全文檢索搜尋執行文件篩選和斷詞。x

### Replication

將資料和資料庫物件從某個資料庫複製和散發到另一個資料庫，然後在兩個資料庫之間進行同步處理，以維護一致性的一組技術。

### Data Quality Services

DQS 可讓您建立知識庫，然後使用該知識庫執行各種重要的資料品質工作，包括更正、充實、標準化及刪除重複資料。

## 共用功能（shared features）

### SQL Server Integration Services

提供 SSIS 封裝儲存和執行的管理支援。

### SQL Server Browser

提供用戶端電腦的 SQL Server 連接資訊

### Distributed Replay

- Distributed Replay - Controller
- Distributed Replay - Client

## SQL 2012 各版本的支援性

|  | 功能＼版本 | Enterprise | Business  <br>Intelligence | Standard | Web | Express with  <br>Advanced Service | Express with  <br>Tools | Express |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 硬體 | CPU | 系統最大值 | 4插槽或16核心 | 4插槽或16核心 | 4插槽或16核心 | 1插槽或4核心 | 1插槽或4核心 | 1插槽或4核心 |
| 記憶體 | 系統最大值 | 64 GB | 64 GB | 64 GB | 1 GB | 1 GB | 1 GB |
| 資料庫大小 | 524 PB | 524 PB | 524 PB | 524 PB | 10 GB | 10 GB | 10 GB |
| 高可用性  <br>(AlwaysOn) | Server Core 支援 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| 資料庫鏡像 | Ｖ | V(僅限見證) | V(僅限見證) | V(僅限見證) | V(僅限見證) | V(僅限見證) | V(僅限見證) |
| 容錯移轉 | Ｖ | Ｖ | Ｖ |  |  |  |  |
| 備份壓縮 | Ｖ | Ｖ | Ｖ |  |  |  |  |
| 延展性和效能 | 資料表和索引分割 | Ｖ |  |  |  |  |  |  |
| 資料壓縮 | Ｖ |  |  |  |  |  |  |
| 安全性 | 基本稽核 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| 細部稽核 | Ｖ |  |  |  |  |  |  |
| 資料庫加密 | Ｖ |  |  |  |  |  |  |
| 金鑰管理 | Ｖ |  |  |  |  |  |  |
| 使用者定義角色 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| 自主資料庫 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| 複寫 | 變更追蹤 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| 合併式複寫 | Ｖ | Ｖ | Ｖ | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) |
| 異動複寫 | Ｖ | Ｖ | Ｖ | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) |
| 快照式複寫 | Ｖ | Ｖ | Ｖ | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) | Ｖ(僅限訂閱) |
| 異質性訂閱者 | Ｖ | Ｖ | Ｖ |  |  |  |  |
| 管理工具 | SQL 組態管理員 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| SQL Server  <br>Management Studio | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| Distributed Replay   <br> 管理工具 | Ｖ | Ｖ | Ｖ | Ｖ |  |  |  |
| Distributed Replay   <br> Controller | Ｖ | Ｖ | Ｖ | Ｖ |  |  |  |
| Distributed Replay   <br> Client | Ｖ | Ｖ | Ｖ | Ｖ |  |  |  |
| SQL Profiler | Ｖ | Ｖ | Ｖ |  |  |  |  |
| SQL Server Agent | Ｖ | Ｖ | Ｖ | Ｖ |  |  |  |
| Database Tuning Advisor  <br>(DTA) | Ｖ | Ｖ | Ｖ | Ｖ |  |  |  |
| Integration Service | 匯入和匯出 | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ | Ｖ |
| SSIS 設計師和執行階段 | Ｖ | Ｖ | Ｖ |  |  |  |  |
| 基本轉換 | Ｖ | Ｖ | Ｖ |  |  |  |  |

## Server Core 模式的支援性

Windows 2008 之後提供 Server Core 模式，雖然 SQL Server 2012 可以安裝在 Server Core 模式下，但會有部分功能不支援 

#### 支援的有以下幾種：

- Database Engine Services
- Replication
- Full-Text Search
- Analysis Services
- Integration Services Services
- Client Tools Connectivity

#### 不支援的有：

- Reporting Services
- SQL Server Data Tools (SSDT)
- Client Tools Backward Compatibility
- Client Tools SDK
- SQL Server Books Online
- Distributed Replay Controller
- SQL Client Connectivity SDK
- Master Data Services
- Data Quality Services

# 設定 SQL 執行個體 (Instance Settings)

## 執行個體

#### 執行個體名稱

SQL Server 允許在一個作業系統下安裝多套的 SQL Server ，每套 SQL Server 即為一個執行個體，若安裝的執行個體沒有指定名稱，則第一個執行個體會以預設的 MSSQLSERVER 當做名稱，並稱為「預設執行個體」。  

#### 連線名稱

若要連上某個執行個體，必須使用「伺服器名稱\執行個體名稱」這個格式的連線名稱；不過，若要連上的是預設執行個體，則直接使用伺服器名稱即可。

這個名稱，也就是全域變數 @@SERVERNAME 記載名稱。

#### 服務名稱

依執行個體名稱的不同，其服務名稱也會有不同的長相：

**範例**

| 執行個體類型 | 服務名稱 | 連線名稱 |
| --- | --- | --- |
| 預設執行個體 | MSSQLSERVER | WinServer1 |
| DBSRV1 | MSSQL$DBSRV1 | WinServer1\DBSRV1 |
| DBSRV2 | MSSQL$DBSRV2 | WinServer1\DBSRV2 |

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqsDpqa3jHbWGzZUT0S3LKWg8sJfMmMMxmt_XP1AjB6wBNSRBNfK45MZjjs0Tj8lhYF4B9k0mJcEEyp3cAPpQ8LXWriG9czZ0Id7XfTaAXO308jXjMawGtqgYWSZE6IzxRED9qo_iofcM/s420/sql-service-name.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzaehNi7RJ0aV5bQsumq_PBxcTuI2kwkE3JE2tfq3u2WGvb-ivbnOeUbt0C7-OiyCXkrAw17Bk6KjPOBbXzm0DY0NULGgS0CN4bINloqA-0Az79yHDmPKrgepTx9V7cM3Psf-ZvQE1Thc/s437/sql-connection-name.png)

#### 變更名稱

**執行個體名稱**不允許變更，因為它與太多其他的服務都有相關連，如：效能監測、權限控管、全文索引、link server 等等。

而**連線名稱**雖然也沒辨法變更，但卻因為其包含電腦名稱，所以若電腦名稱被更換了，所有 AP 端的連線名稱就必須跟著換。

此時你可以使用以下語法，查看連線名稱
```sql
SELECT SERVERPROPERTY('servername')
```

查詢 @@servername 變數原本會得到與上述指令相同結果，但此時若你查詢 @@servername 變數，你會發覺它仍然是舊的資料，這是因為它是直接存取註冊區中的資訊。  你可以透過以下指令來修改這個資訊：
```sql
sp_dropserver 'old_name'
GO;
sp_addserver 'new_name', local
GO;
--重新啟動 SQL Server 服務
```

## 設定記憶體配置

預設，SQL Server 會向 host 動態要求記憶體的使用，不過，你也可以設定一個最大或最小的記憶體使用量。
```sql
EXEC sys.sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sys.sp_configure 'min server memory', 1024;
GO
EXEC sys.sp_configure 'max server memory', 4096;
GO
RECONFIGURE;
GO
If
```

## 設定 Processor Affinity 和 I/O Affinity

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi4LuAVFo7DaWDd_NEvf5645W3SNqBeK6ghoSuemo76iWw23MyQoQdk1Oo46CCmH7gbYgmnfvGIukJO2rKtmeHRwp8zpEQYzNWKrazAz5G8WHl8xuCt-LrP1MQKg6JG0k8mQlxOVL-AVDQ/s669/sql-affinity-mask.png)
[MSDN：affinity mask 伺服器組態選項](http://msdn.microsoft.com/zh-tw/library/ms187104.aspx)    
SQL Server 透過兩個相似性遮罩選項支援處理器相似性：

### CPU affinity mask (affinity mask)

現在電腦大都是多核心的處理器，  在多工的作業還境中，為了增進效能，一個 Thread 有時會在不同的 Process 之間移轉處理。  但是這個移轉行為如果在負載繁重的系統中，反而可能會降低 SQL Server 的效能，因為每個處理器快取必須不斷地重複載入資料 (內容切換)。   在這些情況中，將特定執行緒指定給處理器，可降低處理器重新載入的情形，並減少跨處理器移轉執行緒的問題，進而提升效能，而執行緒與處理器之間的關聯則稱為「處理器相似性」。   
```sql
--To distribute SQL worker threads across CPUs 2 and 3
ALTER SERVER CONFIGURATION SET PROCESS AFFINITY CPU = 2,3

--To distribute SQL worker threads across all CPUs
ALTER SERVER CONFIGURATION SET PROCESS AFFINITY CPU = AUTO
```

### affinity I/O mask

I/O 相似性則是用來設定將 I/O 工作指定給特定的一群處理器。

## 啟動 TCP/IP 通訊協定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIxnZGx1P5CYkzv7G713M-JmFJj7TJ4LT1laTq_QhFhFYeUiI7mca9EVdge_TI20FpPSVvObMR4Txfjf_NbKqmM5cDoV8g-mcW6rLWnAKke-WuES59qs3VCkZvOrjGtv9xHs_5SxM7HXA/s552/sql-neable-tcpip.png)

## 設定填滿因子（Fill Factor）

**填滿因子**指的是葉級索引頁的空間使用率。  這個填滿因子是用來決定一個頁要保留多少空間給未來成長使用。  
```sql
sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure 'fill factor', 90;
GO
RECONFIGURE;
GO
```

# 分散式交易協調器（Distributed Transaction Coordinator, MSDTC）

**分散式交易**是指一個交易，在跨不同的伺服器處理時，仍必須確保交易的完整性。  

Microsoft 分散式交易協調器 (MSDTC) 就是用來管理叢集資源。  如果您只要安裝 Database Engine，則不需要 MSDTC 叢集資源。  如果您要安裝 Database Engine 和 SSIS 或工作站元件，或是將要使用分散式交易，則必須安裝 MSDTC。  

這個服務預設不會啟用，如果需要可由服務管理員中設定啟用。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0wh2zkL9SW8uOvYohZFPTCU0jhTW80n77y0wy2XpqclR6NponHSwOk1IUe96K8vnpqUhqqaczSpxhH_UgTIPQPrVql_sbA0oF0l7RBQYyQKb5Jtw5tiDx1moMB6kgURh8HpBfTXXtgRU/s727/sql-enable-MSDTC.png)
## 參考資料  

- 
-