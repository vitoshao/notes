---
title: 容錯移轉叢集
layout: default
parent: Redundant
nav_order: 4
description: "容錯移轉叢集"
date: 2013-10-03
tags: [SQL,Redundant]
postid: "3444303809394847271"
---
# Windows Server 容錯移轉叢集 (Windows Server Failover Clustering，WSFC)

「Windows Server 容錯移轉叢集」(Windows Server Failover Clustering，WSFC) 叢集是一組獨立的伺服器，會一起運作以提高應用程式和服務的可用性。  SQL Server 2012 利用 WSFC 服務和功能，以支援 **AlwaysOn 可用性群組**和**SQL Server 容錯移轉叢集執行個體**。   

## 詞彙和定義

#### WSFC 叢集

「Windows Server 容錯移轉叢集 (WSFC)」叢集是一組獨立的伺服器，會一起運作以提高應用程式和服務的可用性。 

#### 容錯移轉叢集執行個體 (Failover cluster instance)

「容錯移轉叢集執行個體」指的就是 Windows 執行個體，主要負責資源的管理，如IP位址資源、網路名稱資源、或其他執行應用程式的資源。  用戶端可以使用網路名稱來存取群組中的資源，類似於使用電腦名稱來存取實體伺服器上的服務。   不過，因為容錯移轉叢集執行個體是群組，所以它可以容錯移轉至另一個節點，而不影響基礎名稱或位址。   

#### 節點（Node）

「節點」指的是在伺服器叢集中的每一台 Windows Server ，不管是使用中(active)或非使用中(inactive)的成員。

#### 叢集資源（Cluster resource）

叢集資源指的就是位於節點上的邏輯或實體資源，以叢集物件方式進行管理，可以在節點之間移動，也可以被離線或上線。  任何時間，叢集資源都只能由單一節點所擁有。  

#### 資源群組（Resource group）

A collection of cluster resources managed as a single cluster object.   Typically a resource group contains all of the cluster resources that are required to run a specific application or service.   Failover and failback always act on resource groups.  

#### 資源相依性 （Resource dependency ）

A resource on which another resource depends. If resource A depends on resource B, then B is a dependency of A.

#### 網路名稱資源（Network name resource ）

A logical server name that is managed as a cluster resource. A network name resource must be used with an IP address resource.

#### 慣用擁有者（Preferred owner）

資源群組偏好在其上執行的節點。  每個資源群組都與依照喜好設定順序排序的慣用擁有者清單相關聯。 在自動容錯移轉期間，資源群組會移至慣用擁有者清單中的下一個慣用節點。 

#### 可能擁有者（Possible owner ）

資源可在其上執行的次要節點。  每個資源群組都與可能擁有者清單相關聯。 資源群組只能容錯移轉至列為可能擁有者的節點。 

#### 仲裁模式 （Quorum mode）

容錯移轉叢集中決定叢集可承受之節點失敗次數的仲裁設定。 

#### 強制仲裁 （Forced quorum）

即使僅少數仲裁必要元素正在通訊中，也會啟動叢集的程序。

## Windows Server 容錯移轉叢集概觀

WSFC 會提供高可用性的結構，當叢集節點或服務失敗，該節點上裝載的服務可在稱為「**容錯移轉**」(Failover) 的程序中**自動**或**手動**轉移至另一個可用的節點。 

WSFC 叢集中的節點會協同運作，以提供下列功能：

- **分散式的中繼資料和通知**  
WSFC service 和 hosted application metadata 都是由叢集中的每個節點共同維護。  這些中繼資料除了**託管應用程式設定**之外，還包含 **WSFC 組態**和**狀態**。
- **資源管理**  
叢集中的個別節點可以提供**實體資源**，例如直接附加的儲存體、網路介面和共用磁碟儲存體存取。  託管應用程式會自行註冊為**叢集資源**，並可設定對其他資源的啟動和健全狀況相依性。
- **健全狀況監視**  
節點間和主要節點健全狀況偵測是透過活動訊號式網路通訊和資源監視的組合來完成。  叢集的整體健全狀況是由叢集節點的仲裁投票所決定。
- **容錯移轉協調**  
每個資源都預設裝載在主要節點上，且這些資源也都可以自動或手動轉移至一個或多個次要節點。  以健全狀況為主(health-based)的容錯移轉原則會在節點之間控制資源擁有權的轉移。   在發生容錯移轉時，節點和託管應用程式會收到通知，以便它們可以適當反應。

# 系統需求

## 版本需求

容錯移轉叢集中的所有節點必須使用相同版本的作業系統，且相同的平台（32或64位元）。

底下是支援容錯移轉叢集的版本資訊：

| 版本 | 說明 |
| --- | --- |
| SQL 2012 Enterprise | 支援 16 叢集節點  &lt;br&gt;支援 Multi-Subnet 容錯移轉 |
| SQL 2012 BI | 支援 2 叢集節點 |
| SQL 2012 Standard | 支援 2 叢集節點 |

| 版本 | 說明 |
| --- | --- |
| Window Server 2012 R2 Enterprise | 支援容錯移轉  &lt;br&gt;支援 Multi-Subnet 容錯移轉 |
| Window Server 2012 Enterprise | 支援容錯移轉  &lt;br&gt;**不**支援 Multi-Subnet 容錯移轉 |
| SQL 2012 Standard | 支援 2 叢集節點 |

更多內容請參考 MSDN：[支援的版本與版本升級](http://msdn.microsoft.com/zh-tw/library/ms143393.aspx)

好難喔...看不下去...沒有環境練習

## Windows Server 2008 R2 as Shared Storage

## Connecting to the SAN by Using iSCSI Initiator

# Creating a Windows Server 2008 R2 Failover Cluster

## 設定 Windows Server Failover Cluster

- 至少必須在伺服器叢集的一個節點上設定 Microsoft Cluster Service (WSFC)。
- SQL Server service 會提供二個函數，可讓 WSFC 叢集管理員用來檢查 SQL Server 資源的可用性。
- WSFC 可以透過 IsAlive 檢查，驗證 WSFC 中的執行個體是否正在執行中。
- 設定網域名稱服務 (DNS) 或 Windows 網際網路名稱服務 (WINS)。

## 安裝分散式交易協調器 (Microsoft Distributed Transaction Coordinator, MSDTC)

#### 安裝 MSDTC

在容錯移轉叢集上安裝 SQL Server 之前，請判斷是否必須建立「Microsoft分散式交易協調器」叢集資源。  如果您只要安裝 Database Engine，則不需要 MSDTC 叢集資源。 如果您要安裝 Database Engine 和 SSIS 或工作站元件，或是將要使用分散式交易，則必須安裝 MSDTC。 請注意，MSDTC 並非僅限 Analysis Services 執行個體的必要項目。

在 Windows Server 2008 和 Windows Server 2008 R2 中，您可以在單一的容錯移轉叢集上安裝 MSDTC 的多個執行個體。  第一個安裝的 MSDTC 執行個體將會是 MSDTC 的叢集預設執行個體。 SQL Server 將會透過自動使用 MSDTC 執行個體的方式，利用已安裝到 SQL Server 本機叢集資源群組的 MSDTC 執行個體。 但是，個別的應用程式可以對應到叢集上的任何 MSDTC 執行個體。

#### 設定 MSDTC

安裝作業系統並設定叢集之後，您必須使用「叢集管理員」（Cluster Administrator）來設定 MSDTC 以搭配叢集使用。  如果無法將 MSDTC 設定為搭配叢集使用，也不會封鎖 SQL Server 安裝程式，但如果沒有正確設定 MSDTC，則 SQL Server 應用程式功能可能會受到影響。

# Installing a SQL Server Failover Cluster

## h2\_XXXXX

## 重置失敗節點

如果叢集系統發生容錯移轉，原本Node1已正常容錯移轉到Node2，經檢查後，發現Node1已經無法使用，此時該如何重置Node1？  

- 將 Node1 由 WFC 中移除。
- 安裝一個新的 Windows Server ，用來取代原本的 Node1。
- 使用 Winodws Failover Cluster Manager 將新Node1加入現有的叢集系統。
- 在新Node1上執行 SQL Setup，並執行「Add node to a SQL Server failover cluster」


- [Before Installing Failover Clustering](http://technet.microsoft.com/en-us/library/ms189910.aspx)
- [Install a SQL Server Failover Cluster](http://technet.microsoft.com/en-us/library/hh231721.aspx)
- [Upgrade a SQL Server Failover Cluster](http://technet.microsoft.com/en-us/library/ms191009.aspx)
- [Create a New SQL Server Failover Cluster](http://technet.microsoft.com/en-us/library/ms179530.aspx)
- [Add or Remove Nodes in a SQL Server Failover Cluster](http://technet.microsoft.com/en-us/library/ms191545.aspx)
- [SQL Server 2008 R2 容錯移轉叢集環境架設 - 利用 VM 與 Windows Storage Server](http://caryhsu.blogspot.tw/2011/07/sql-server-2008-r2-vm-windows-storage.html)
- [SQL Server 2012 - AlwaysOn 安裝與設定教學](http://caryhsu.blogspot.tw/2012/04/sql-server-2012-alwayson.html)

## 安裝 SSFC

## 在現有的 SSFC 中加入節點

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCDFViUwugekWDlgbnLXEzN16LD8QG3KNJXO7BAt3YJNjJw7RQMKSd330HNKsSyT2KU3XmHGMbmzmvimQHjJCg-9hJ3Q5t3tX2NLN1gIlYm2J0zPDbraNIAT1JGrfWf8lfJGKe4AX9_3A/s0/sql-ssfc-add-node.png)

1. 在安裝光碟中，執行「Add node to a SQL Server failover cluster」
2. 
3. 
4. 
5. 
6.

# Multi-Subnet Failover Clustering

# Performing Manual Failover

# Troubleshooting Failover Clusters

## 參考資料  

- [SQL Server 的 Windows Server 容錯移轉叢集 (WSFC)](http://msdn.microsoft.com/zh-tw/library/hh270278.aspx)
- [支援的版本與版本升級](http://msdn.microsoft.com/zh-tw/library/ms143393.aspx)
- [安裝容錯移轉叢集之前](http://msdn.microsoft.com/zh-tw/library/ms189910.aspx)
- [安裝 SQL Server 容錯移轉叢集](http://technet.microsoft.com/zh-tw/library/hh231721.aspx)
- [升級 SQL Server 容錯移轉叢集](http://msdn.microsoft.com/zh-tw/library/ms191009.aspx)
- [在 SQL Server 容錯移轉叢集 (安裝程式) 中加入或移除節點](http://technet.microsoft.com/zh-tw/library/ms191545.aspx)
- [SQL Server 多重子網路叢集 (SQL Server)](http://technet.microsoft.com/zh-tw/library/ff878716.aspx)
- [\[Derrick\]新手學安裝SQL Server 2008「容錯移轉叢集(Failover Cluster)」(1)](http://sharedderrick.blogspot.tw/2011/01/sql-server-2008failover-cluster1.html)
- [\[Will\]安裝 SQL Server 2008 容錯轉移叢集心得筆記 – 第 1 篇](http://blog.miniasp.com/post/2010/01/SQL-Server-2008-Failover-Cluster-Installation-Notes-Part-1.aspx)
- [Recover from Failover Cluster Instance Failure](http://technet.microsoft.com/en-us/library/ms181075.aspx)