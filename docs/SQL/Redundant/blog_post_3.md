---
title: AlwaysOn 可用性群組
layout: default
parent: Redundant
nav_order: 3
description: "AlwaysOn 可用性群組"
date: 2013-07-11
tags: [SQL,Redundant]
postid: "3954316948793250222"
---
微軟的 SQL Server 提供以下四種做法來達到 High Availability (HA) 功能。

1. **複寫（Replication）**  
複寫機制是透過 SQL Agent 將主資料庫中的**特定資料**，透過發行與訂閱技巧，複製到另一個資料庫上。
2. **記錄傳送（Log Shipping）**  
記錄傳送是以**整個資料庫**為對象進行複寫，利用排程於固定時間時，將主資料庫，複寫到目標資料庫中。
3. **鏡像（Database Mirroring）**  
鏡像機制也是針對**整個資料庫**進行覆寫，除了有 Log Shipping 的功能，還有可以進行 auto failover 切換。
4. **容錯移轉叢集（Failover Clustering）**  
「容錯移轉叢集」是一組獨立的伺服器，會一起運作以提高應用程式和服務的可用性。  「容錯移轉叢集」會隨時保持叢集中每一個**節點**（SQL Server Instance）的同步，於預防問題發生時，可以快速切換！

#### AlwaysOn 容錯移轉叢集執行個體

AlwaysOn 容錯移轉叢集執行個體是 SQL Server AlwaysOn 產品的一部分，它必須搭配 Windows Server 容錯移轉叢集 (WSFC) 功能，透過伺服器執行個體層級 (「容錯移轉叢集執行個體」(Failover Cluster Instance，FCI)) 的備援性提供本機高可用性。  FCI 是跨 Windows Server 容錯移轉叢集 (WSFC) 節點且可能跨多個子網路安裝的單一 SQL Server 執行個體。  在網路上，FCI 看似單一電腦上的 SQL Server 執行個體，但是 FCI 提供容錯移轉，可以在目前的 WSFC 節點無法使用時，從該節點容錯移轉到另一個節點。   

# 什麼是 AlwaysOn 可用性群組（AlwaysOn Availability Groups）

可用性群組支援一組讀寫主要資料庫，以及一到四組對應的次要資料庫。 此外，您可以將次要資料庫用於唯讀存取及/或某些備份作業。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhiqLVSVwLncLhaD4iOYler-7cHOlsFOS5rLKAMZmBkpRmMf3jxamUAJU0D2RkCI2c_ILMIU3QOSsDp_4zRTnbTK59xbaEvd8FVupv_hfsOCDCFMspBR7Dj54ytBXjnXY-PJkYZO-KuDwc/s0/sql-wsfc.gif)

## 可用性資料庫（Availability Databases）

若要在可用性群組中加入新的資料庫，該資料庫加在主要複本的伺服器執行個體上，在主要複本伺服器上的資料庫稱為**主要資料庫**，同時仍然可以供用戶端使用。   

新加入的主要資料庫，會經由備份還原到裝載次要複本的伺服器執行個體，在備份還原作業完成之前，不會存在任何對應的**次要資料庫**。  新的次要資料庫在加入可用性群組之前，處於 RESTORING 狀態。完成時，才會將次要資料庫變更 ONLINE 狀態，並開始與主要資料庫進行資料同步處理。

## 可用性複本（Availability Replicas）

每個可用性群組都會定義一組兩個或多個容錯移轉夥伴，稱為可用性複本。

「可用性複本」(Availability Replica) 是可用性群組的元件。   每個可用性複本都會在可用性群組中裝載一個可用性資料庫的副本。  對於給定的可用性群組而言，可用性複本必須由位於某個 WSFC 叢集中不同節點上的個別 SQL Server 執行個體所裝載。  這些伺服器執行個體每一個都必須啟用 AlwaysOn。   

每個執行個體只能裝載一個可用性複本。  但是，每一個執行個體都可以用於多個可用性群組。  給定的執行個體可以是獨立執行個體或 SQL Server 容錯移轉叢集執行個體 (FCI)。  如果您需要伺服器層級的備援性，請使用容錯移轉叢集執行個體。   

每個可用性複本都會獲指派一個初始角色，即「主要角色」(Primary Role) 或「次要角色」(Secondary Role)，這個角色是由該複本的可用性資料庫所繼承。  給定複本的角色會決定所裝載的是讀寫資料庫還是唯讀資料庫。 其中一個複本 (也就是所謂的「主要複本」(Primary Replica)) 會獲指派主要角色並裝載讀寫資料庫，也就是「主要資料庫」(Primary Database)。 至少有一個其他複本 (也就是所謂的「次要複本」(Secondary Replica)) 會獲指派次要角色。 次要複本會裝載唯讀資料庫，也就是次要資料庫。

## 可用性模式（Availability Modes）

在 AlwaysOn 可用性群組中，有個[可用性模式](http://msdn.microsoft.com/zh-tw/library/ff877931.aspx)屬性，它是用來設定可用性複本是否以同步認可模式執行（synchronous-commit mode）。  

AlwaysOn 可用性群組支援兩種可用性模式：非同步認可模式和同步認可模式，如下所示：

### 非同步認可模式（Asynchronous-Commit Mode）

「非同步認可模式」是一種當可用性複本分散距離相當遠時仍可正常運作的災害復原方案。  如果每個次要複本都在非同步認可模式下執行，主要複本就不會等候任何次要複本強行寫入記錄。  
```sql
AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,
```

### 同步認可模式（Synchronous-Commit Modee）

「同步認可模式」強調的是高可用性而非效能，但是相對地增加了交易延遲。  在同步認可模式下，交易會等候傳送交易確認至用戶端，直到次要複本將記錄強行寫入磁碟為止。   
```sql
AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
```

## 容錯移轉的類型（Failover Types）

容錯移轉（Failover）共有三種形式，包括自動容錯移轉、經過規劃的手動容錯移轉，以及強制手動容錯移轉。   

### 自動容錯移轉（Automatic failover）

自動容錯移轉會在主要複本遺失時自動發生容錯移轉。  必須目前的主要複本和一個次要複本都同時設定為 AUTOMATIC 的容錯移轉模式，且次要複本必須已經同步處理，才支援自動容錯移轉。  如果主要或次要複本的容錯移轉模式為 MANUAL，則無法進行自動容錯移轉。   
```sql
FAILOVER_MODE = AUTOMATIC)
```

### 已規劃的手動容錯移轉（Planned manual failover）

「已規劃的手動容錯移轉」，可簡稱「手動容錯移轉」(Manual Failover)。通常是指資料庫管理員基於系統管理目的起始的容錯移轉。  只有在主要複本和次要複本都設定成**同步認可模式**，而且次要複本目前已同步處理 (處於 SYNCHRONIZED 狀態) 時，才支援已規劃的手動容錯移轉。  當目標次要複本已同步處理時，即使主要複本已經當機，手動容錯移轉 (不會遺失資料) 也是可能的，因為次要資料庫已做好容錯移轉的準備。  資料庫管理員會手動起始手動容錯移轉。   
```sql
FAILOVER_MODE = MANUAL
```

### 強制容錯移轉（Forced failover）

如果次要複本與主要複本沒有同步處理或主要複本未執行而且沒有任何次要複本已做好容錯移轉的準備，資料庫管理員仍可強制起始容錯移轉。    強制容錯移轉可能造成資料遺失，建議僅限用於災害復原。  強制容錯移轉只能手動起始，因此視為一種強制手動容錯移轉。  這是在非同步認可的可用性模式下唯一支援的容錯移轉形式。   
```sql
ALTER AVAILABILITY GROUP haContosoDbs MODIFY REPLICA ON 'Server01\Database01'
WITH (AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT, FAILOVER_MODE = MANUAL)
```

# 系統需求

## 版本需求

[AlwaysOn 可用性群組的必要條件、限制和建議 (SQL Server)](http://msdn.microsoft.com/en-us/library/ff878487.aspx)

## 建置條件

AlwaysOn：建置條件

- 必須建置在叢集架構(Cluster)之上。
- AlwaysOn不需要MSDTC的服務。
- 各節點資料庫安裝時，需選擇【新增SQL Server獨立安裝或將功能加入至現有安裝】。
- 必須至網域控制站(DC)建立一個統一管理各節點SQL Server服務的帳號。

# 可讀取的次要複本（Readable Secondary Replicas）

當主要複本機器上有大量的唯讀工作，你可以使用可讀取的次要複本來減輕它的工作負擔。  

可讀取的次要複本，共有三種選項：

- No ：不允許任何連線。
- Yes ：允許所有連線。
- Read-intent only ：(讀取意圖)僅允許唯讀的連線。

# 怖署 AlwaysOn 可用性群組

## Creating an AlwaysOn Endpoint

## Enabling AlwaysOn Availability Groups

## Creating an Availability Group

## Creating or Adding an Availability Group Listener

## Adding Secondary Replicas

# Using Availability Groups on Failover Cluster Instances

# 備援架構簡介

高可用性簡單講就是讓系統架構處於一個高度可用的狀況，也就是若系統中主要機器發生問題，系統也能夠正常運作。底下簡介幾種建立高可用性叢集系統的方法：  

## ２節點叢集系統 Active-Standby 備援架構

#### 架構說明

Active-Standby 備援架構由兩部主機透過 SCSI 或 Fibre Channel 連接到共享磁碟機（Share Disk, 通常是採用磁碟陣列系統 Disk Array）而構成一叢集系統，應用程式的資料庫儲存於 Disk Array 上，同一時間內應用程式只在其中一部主機上工作（Active），並只允許該主機能讀寫資料庫。  當此主機任何元件發生當機或應用程式服務無法正常工作時，另一部主機（Standby）可立即接管儲存設備的存取權並重新啟動應用程式，維持系統服務正常運作而不停擺，提供系統容錯及更高的系統可用性。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCDfcoG1_haiUnq2CgIhIWow01e2LGym8C7bxFuMTOywPCIK81JLe2yqhgh5KvJe7fzndV-pTDv1lXjMnITL5oVEv6TnIqHt2k1pZcRiDQ6KdHCVIaKMmJCkKuN90AQAtsi4Aj7EhlBmI/s0/sql-ha-Active-Standby.jpg)

#### 效益評估

此種 Active-Standby 備援架構模式最為一般客戶所採用，原因是架構簡單，容易維護及管理，若兩台主機配備相同，系統服務不會因為錯誤移轉(FailOver)到另一台之後產生效能降低情形，這將可維持穩定的服務效能。  但另一方面，由於 Standby 主機的閒置也使得價格效益比降低。

## ２節點叢集系統 Active-Active 備援架構

#### 架構說明

Active-Active 備援架構的硬體架構如同 Active-Standby 一樣，但兩部主機各有執行獨立的應用程式服務，同時亦相互做備援(Standby)，當任一部主機發生故障或應用程式服務無法正常工作時，另一部主機除了本身的應用程式服務持續運作外並自動接管故障主機之應用程式服務，維持整體系統運作。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5Iv76MVvW2vOo6w2odABBFHjbXIpseYFZ4bcwgK_gZH1z6cUatIbjAy1BPvi0Ta_X1Z2m3RpPmA_sN_yw-SstZQYFb_nPpU5Zz3wrUWe5qhEGBz36snssc5KoA3ZoofkFGzBmdn4tuNU/s0/sql-ha-Active-Active.jpg)

#### 效益評估

此種 Active-Active 可有效避免 Standby 主機閒置的情形，提高價格效益比，但此模式容易因錯誤移轉發生後造成接管主機負載比原本的架構來得重而無法維持一定的服務效能。

## N+1 節點叢集系統 Active-Active 架構

#### 架構說明

硬體架構採用多部主機透過 SCSI 或 Fibre Channel 連接到共享磁碟機（Share Disk, 通常是採用磁碟陣列系統 Disk Array）而構成一叢集系統。  如同 Active-Active 備援架構一樣，其中每部主機各有執行獨立的應用程式服務，被指定的最後一台主機作為各主機的備援（Standby）。  當任一部主機發生故障或應用程式服務無法正常工作時，Standby 主機將自動接管故障主機之應用程式服務，維持整體系統運作。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjy6NEQDsgBHZg7CwfsBRf4Zpz3SVv5v3Op3fY7A3EW4A5WZ4cVBnnHZ2jleKw85rb8ciNGecfkWlx7M3tjlfj41K1B0nLJpY8-Ho1nhscLJPVOl3xubE_QAKniap_Wltthl7e-bbvYECc/s0/sql-ha-n1-Active-Active.jpg)

#### 效益評估

此種多對一 Active-Active 方式除了可有效避免 Standby 主機閒置的情形，提高價格效益比之外，也不會因為錯誤移轉 FailOver 發生後造成接管主機負載比原本的架構來得重而無法維持一定的服務效能，更可允許各自執行獨立的應用程式服務的多部主機同時損壞，大幅提高了系統可用度。  

在 N+1 的多主機備援的模式下，搭配SAN(Fibre)的系統架構解決方案，管理者可依實際需求隨時可以新增或移除主機(Node)到原有的叢集系統內，不需中斷服務，提高了擴充性(Scalability)，主機及儲存設備將可更有效率的被運用。
## 參考資料  

- [AlwaysOn 可用性群組 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/hh510230.aspx)
- [AlwaysOn 可用性群組概觀 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ff877884.aspx)
- [可用性模式 (AlwaysOn 可用性群組)](http://msdn.microsoft.com/zh-tw/library/ff877931.aspx)
- [容錯移轉及容錯移轉模式 (AlwaysOn 可用性群組)](http://msdn.microsoft.com/zh-tw/library/hh213151.aspx)
- [使用中次要：可讀取的次要複本 (AlwaysOn 可用性群組)](http://msdn.microsoft.com/zh-tw/library/ff878253.aspx)
- [AlwaysOn 可用性群組的必要條件、限制和建議 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ff878487.aspx)
- [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 (SQL Server PowerShell)](http://msdn.microsoft.com/zh-tw/library/hh510204.aspx)
- [使用新增可用性群組精靈 (SQL Server Management Studio)](http://msdn.microsoft.com/zh-tw/library/hh403415.aspx)
- [建立可用性群組 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ff878307.aspx)
- [建立或設定可用性群組接聽程式 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/hh213080.aspx)
- [將次要複本聯結至可用性群組 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ff878473.aspx)
- [容錯移轉叢集和 AlwaysOn 可用性群組 (SQL Server)](http://msdn.microsoft.com/zh-tw/library/ff929171.aspx)
-