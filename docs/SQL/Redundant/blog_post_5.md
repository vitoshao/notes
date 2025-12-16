---
title: 複寫
layout: default
parent: Redundant
nav_order: 5
description: "複寫"
date: 2013-10-03
tags: [SQL,Redundant]
postid: "6228656007644057937"
---
# 複寫

複寫是指，將資料和資料庫物件從某個資料庫複製和散發到另一個資料庫，然後在兩個資料庫之間進行同步處理，以維護一致性的一組技術。  使用複寫，您可以透過區域網路、廣域網路、撥號連接、無線連接及網際網路，將資料散發到不同的位置，以及散發到遠端或行動使用者。

下列術語，是操作複寫時會使用到的元素：

- **發行者**（Publisher）：An instance that makes data available through publication.
- **訂閱者**（Subscriber）：An instance that receives publications.
- **散發者**（Distributor）：An instance that manages the transmission from publisher to subscriber.
- **發行項**（Article）：發行項就是要發行的物件，可以是 table, stored procedure, view, indexed view, user-defined function .
- **發行集**（Publication）：發行項的集合。
- **代理程式**（Agent）：執行複寫工作的服務程式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhylBjqDNCtn0p_xjiiVxI2lcRwOjVlC1fAC3-la8sAmAOGHs-Y9MuUJGq0w4l1CCfj7lU2HrIOKyFSJhPxXiEmHHCqalW4CdojXshRPnUgg2rtCoP7sUxNmVQjpfV7Qfu7fO-npxNOhlQ/s0/sql-replications.gif)

### 發行者

「發行者」是一個可透過複寫使資料可用於其他位置的資料庫執行個體。  發行者可以有一個或多個發行集，每個發行集各定義一組邏輯相關的物件及要複寫的資料。

### 散發者

「散發者」是一個作為儲存器的資料庫執行個體，用於儲存與一個或多個「發行者」相關聯的複寫特定資料。  
若「發行者」與「散發者」在同一個執行個體上時， 「散發者」稱為「本機散發者」。  
當「發行者」與「散發者」分別在不同的執行個體上時，「散發者」稱為「遠端散發者」。

### 訂閱者

「訂閱者」是接收已複寫資料的資料庫執行個體。  訂閱者可以接收多個發行者和發行集的資料。 依所選的複寫類型而定，訂閱者也可以將資料變更傳回發行者，或將資料重新發行到其他訂閱者。

### 發行項

用以識別包含在發行集中資料庫物件的發行項。  發行集可包含不同類型的發行項，其中包括資料表、檢視、預存程序和其他物件。 當您將資料表當作發行項來發行時，您可以利用篩選來限制傳送給訂閱者之資料的資料行和資料列。

### 發行集

發行集是單一資料庫之一個或多個發行項的集合。  將多個發行項分組到單一發行集，會比較容易指定一組當作一個單元來複寫的邏輯相關之資料庫物件和資料。

### 訂閱

訂閱是對要傳遞給「訂閱者」之發行集副本的要求。  訂閱會定義將在何時、何處收到什麼發行集。 訂閱的類型有兩種：發送和提取。

### 代理程式

代理程式（ SQL Server Agent）是複寫工作的服務程式，負責「發行者」與「訂閱者」之間的資料複製及移動。所以要執行複寫工作，必需啟動 SQL Server Agent

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOO9T-9-9buwpM6EgZcLnPeXZsV6D79RxaLL7ixlIZS-NYGGq6w-7_IpXawX4aNBK3ElxTDNQq8gAmkYvzxL8bfWzFLnI-bdOxUCHi1VisNfaaP4viB6xYOladFn_K_cpmglH797EugV8/s0/sql-replication-agent.png)

# 複寫類型

SQL Server 提供以下幾種複寫類型：

- **快照式複寫**：「發行者」透過排程，產生快照集，「訂閱者」透過排程，進行複寫。
- **異動式複寫**：若「發行者」發生交易，交易會立即傳遞至「訂閱者」。
- **點對點異動複寫**：點對點異動複寫是以異動式複寫為基礎，所以「發行者」發生交易，交易會立即傳遞至「訂閱者」。  
但是，所有「訂閱者」的資料是先經過分割的，然後各「訂閱者」的交易，也會套用到「發行者」。
- **合併式複寫**：所有「訂閱者」的資料，可以獨自變更或離線變更，最後會定時合併到「發行者」。

不同的複寫類型適用不同的需求環境：

- 快照式複寫：交易量少，沒有即時同步的需求。
- 異動式複寫：「訂閱者」需要即時取得累加更新，常用於報表系統。
- 點對點異動複寫：每個節點同時都是「發行者」與「訂閱者」，使用異動式複寫模式，常用於負載平衡系統。
- 合併式複寫：所有「訂閱者」的資料，可以獨自變更或離線變更，最後會定時合併到「發行者」。

關於各複寫類型的適用情境，可參考MSDN：[選取適當的複寫類型](http://technet.microsoft.com/zh-tw/library/ms152565%28v=sql.105%29.aspx)  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiERh5Gpg8Ur43VlEs8HjahbeEcoM3nD5xxZ8cMbl_J6ZR_rKPJ4uBBHgjZjc05HUGEq7jTZNDfdV7odlhy5cjpNtnI9WpddgjcsbQZi_W6heuGvZghrUKKjS_WsQ1WeLcnfvrHR7HZEZ8/s0/sql-replication-type.png)

## 快照式複寫（Snapshot Replication）

### 快照式複寫如何運作 

快照式複寫必須透過**排程設定**來運作。  當複寫發生時，它會將完整的資料散發出去，而不理會二次發行之間資料是否有任何異動，也就是會產生整個完整的快照集資料給「訂閱者」。  

#### 下圖顯示快照式複寫的主體元件：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKSku65TtU8qcGEz9xakfkk9xeP90cyrAM7Nhp8fN7a1f2HyP4cxt2vJsHu64CsBcetyQGadVWdJVot7n35_wtWu_emAq0MkKVZkRYlYakl6SZvodFHkTCYdfVAHgnagFOGWdXZCSbpBY/s0/sql-snapshot-topology.gif)

#### 快照式複寫適合在以下情況時：

- 資料變更頻率少。
- 在短時間內發生大量的資料變更。
- 訂閱者沒有最即時資料的需求，可接受過時一段時間的資料。
- 要複寫的資料量不大。

快照式複寫最適合資料變更數量可觀但次數不頻繁時。例如，銷售單位的產品價格調整作業。  因為價格調整作業不常發生，但是發生時，通常很多資料都要進行調價，所以可以在調價完之後進行快照式複寫。  

又或者雖然資料一整天都在更新，但是訂閱者可以接受這些變更延遲，所以可以在晚上再進行快照式複寫。  

快照式複寫對系統負擔最小，因為它不需考量資料的變更狀況。  可是它是每次將所有資料進行複寫，所以若整個資料量太大就不適合這個模式。

## 異動複寫（Transactional Replication，交易式複寫）

### 異動複寫如何運作

異動複寫是由「SQL Server 快照集代理程式」、「記錄讀取器代理程式」及「散發代理程式」共同運作。  只要資料產生異動，就會引發複寫發生。  

1. **快照集代理程式**會先準備快照集檔案，內含結構描述及已發行資料表與資料庫物件的資料、並將檔案儲存在快照集資料夾內。
2. **記錄讀取器代理程式**會監視交易記錄檔，並將設定為複寫的交易從交易記錄檔複製到散發資料庫中。
3. **散發代理程式**會將快照集資料夾中的初始快照集檔案，以及存放在散發資料庫資料表中的交易複製到訂閱者。

訂閱者接收到已發行資料的初始快照集之後，在「發行者」端進行的後續資料變更或結構描述等修改，都會立即 (近乎即時) 傳遞到「訂閱者」。 

#### 下列情況適合使用異動複寫： 

- 伺服器對伺服器環境(server to server)。
- 您希望「發行者」更新資料時，「訂閱者」可以很快的收到這些累加更新。
- 發行和訂閱之間，只能允許有低度延遲（low latency ）。
- 發行者有極大量的插入、更新和刪除活動。
- 發行者或訂閱者為非 SQL Server 資料庫，如 Oracle。

## 點對點異動複寫（Peer-to-Peer Replication）

點對點複寫是以異動複寫為基礎，會以接近即時、異動式的方式傳播一致的變更。   如此可讓需要向外延展讀取作業的應用程式將來自用戶端的讀取散發到多個節點之間。   由於會以接近即時的方式在節點之間維護資料，所以點對點複寫會提供資料備援性，這樣可提高資料的可用性。  

簡單講點對點異動複寫，就是二個節點的資料要互相複寫，每個節點同時充當「發行者」與「訂閱者」。。  如果Ａ的資料變更，要複寫到Ｂ；同樣的，Ｂ的資料變更也要複寫到Ａ。  所以這樣子，Ａ和Ｂ就可以成為相互的備援資料，讓資料的可用性提高。  這機制常使用於負載平載的系統。  

使用點對點複寫最重要的問題在於如何避免資料的衝突。  也就是若同一筆資料，在二個節點都同時被修改了，那麼當資料在複寫變更時，很可能發生遺失更新的問題。  

### 點對點式複寫如何運作 

#### 二個點的拓撲

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihH5ODsCybUK7nd9mhUZoPDRx5SjFW3BkzSpXAaebmMZ5cGk4_ljyBaAs_evqv2AFld-tdvPEHCUCGZEPuFLBf97jSfLmaU4yDfRGGkImd6AMsxS8Qgdtv6hHeRmKOD_jaNsGYAvflWbI/s0/sql-p2p-replication-topology-1.gif)

上圖中，是一個負載平載系統，資料讀取時，可能讀到Ａ或Ｂ中的資料，所以ＡＢ二台伺服器採用點對點異動複寫。  這個優點是，當一台失敗時，可以使用另一台以提高可用性。  

在這二個拓撲圖中，雖然都是負載平衡系統，但是他們在處理更新時所用的方式有所不同： 

- 在左圖中，更新會在兩個伺服器之間進行分割。  舉例來說，如果資料庫包含產品目錄，您可以設定將名稱為 A 到 M 開頭之產品，若發生更新都導向節點 A，而名稱 N 到 Z 開頭之產品則導向節點 B。  然後更新後的資料，就會透過複寫機制，再複寫到其他伺服器。
- 在右圖中，所有的更新都直接導向節點 B。所有更新的資料再經由複寫機制，複寫到節點 A。  如果 B 離線 (例如，為了進行維護)，應用程式伺服器就可以將所有活動導向 A。  當 B 再次連線時，離線這段時間所發生的更新都會由 A 複寫過來。  而且應用程式伺服器可將所有更新移回 B，或是繼續將更新導向 A。

#### 三個點以上的拓撲

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEy2O5BGhJEyElfIepLhsQ1fCutg7zW8OA04O3Mdw_DQAjAYlIDbO02qack3Ff6E0VLcy8y2RfMj_qeZ0-CBBPpY4fQqU_pDWkCIgyqdN6LlfRDA4f72mLAhcS8UAcQ0L7YdFTUuFWQw0/s0/sql-p2p-replication-topology-2.gif)

由圖中可以看的出來，所有點都會將資料發行到所有其他點，同時也會訂閱所有其他點的資料。  這樣當其中一或多個節點失敗時，可以提供最大的可用性。   若有新增節點，您必須針對效能以及部署與管理的複雜度來平衡可用性和延展性。

## 合併式複寫（Merge Replication）

合併式複寫與異動複寫類似，通常以發行集資料庫物件和資料的快照集啟動。  
在「發行者」和「訂閱者」端所作的後續資料變更或schema變更，都會透過觸發程序來進行追蹤。  
當連接到網路，「訂閱者」和「發行者」就會開始進行同步，二者會交換自上次同步處理後進行過變更的所有資料列。   

訂閱者接收到已發行資料的初始快照集後，發行者和訂閱者即可獨立更新已發行資料。  變更會定期合併。Microsoft SQL Server Compact Edition 僅能訂閱合併式發行集。  

#### 下列情況適合使用合併式複寫： 

- 伺服器對戶端環境(server to client)。
- 多個訂閱者可能會在不同時間更新相同的資料，並將這些變更傳播到發行者與其他訂閱者。
- 「訂閱者」需要接收資料、離線變更資料，稍後同步變更「發行者」和其他「訂閱者」。
- 每個訂閱者需要不同的分割資料。
- 可能會發生衝突，而在發生衝突時，您需要偵測與解決衝突的能力。
- 應用程式只需要資料變更的最後結果，而不需要中間的變更狀態。  例如，如果資料列在「訂閱者」與「發行者」同步之前，於「訂閱者」端變更了五次，則該資料列只需在「發行者」端變更一次，以反應最終資料變更 (即第五次的值)。

### 合併式複寫如何運作

合併式複寫允許資料在各個節點被更新，然後將所有更新合併到單一的統一結果。  
因為是在多個節點更新，所以相同的資料可能已由發行者和多個訂閱者更新。   
因此，合併更新時可能會發生衝突，而合併式複寫則提供了數個處理衝突的方法。   

合併式複寫是由「SQL Server 快照集代理程式」與「合併代理程式」所實作。  
如果發行集未經篩選或使用靜態篩選，則「快照集代理程式」只會建立一個快照集。  
如果發行集使用參數化篩選，則「快照集代理程式」則會依據各個分割資料建立快照集。  

「合併代理程式」會將初始的快照集套用至「訂閱者」，也會合併後續在「發行者」或「訂閱者」端所發生的資料變更，  並且根據您設定的規則，偵測並解決衝突的相關問題。  

#### 複寫識別資料列

若要追蹤變更，合併式複寫 (和具有佇列更新訂閱的異動複寫) 必須要可以識別每個已發行資料表中的每個資料列。  要達到這目標，合併式複寫會在每個資料表加入一個 rowguid 資料行 (若資料表已經有 uniqueidentifier 資料行，則直接使用)。  

- 如果從發行集卸除資料表，rowguid 資料行會遭到移除；如果現有資料行是用來進行追蹤，則不會移除資料行。
- 使用篩選功能時，不能包含複寫識別資料列。
- 系統會使用 newid() 函數取得 rowguid 資料行的預設值，不過如有需要，使用者也可以提供每個資料列的 GUID。

下圖顯示合併複寫中使用的元件。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKkqbw40ma19Jbc10DipRMeqzoKglMcPnq0292fbPCYUKAtrlh5jiaouG5TxjDnvrzpt8JbFOuSbE391pQa9BrUxRgkJqRrcI_W4GTp9oDK4gYdd_1QhHk68u9BWsEUIk_9XPhivXLJ4g/s0/sql-merge-replication-topology.gif)

# 練習一：快照式複寫

## 建立發行

1.新增發行集  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmpvwbkf3RZbNO8jFHK5nSD7iJ4vAUfEujjbRbn-eoNr_zSxt8TvVfEMvwRCllzv8x8Awe1qJyiO-o2VNVNs2nXiS6kAwL9uhN0jwYBxxh3_yF2zyAL9ALW-XsAr_a-kuK-Lap65B1rw8/s0/sql-snapshot-step01.png)

2.發行集精靈  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPdYuklaELmr3nK6G5e3U6-sphFSdYLZOtm3p6vKlvTbqUIt1he68FIH9CSkyuGBWJ3BtAt0Ex6Sy0rSfX423rj6mwhXBU0E3VU-iLpFHB0rfhbojvBQQhNJP6PjisGicD49SnYNpqg7k/s0/sql-snapshot-step02.png)

3.設定要散發資料的資料庫  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6sTaBkFwUKw-dnELo6sp5i4o2iTffBp9RosF-Xqr3h92g77CKyWSxcjv_ThHuW1PJeIsiav6YJdvgxUorwFDyyD48Q9PZUxxI-_ttwAZgVst5uUoBWX4XQF_O8RPFYZXWrN-Zask_L4A/s0/sql-snapshot-step03.png)

4.設定複寫類型  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPCYy2-Kn65qKjg5bUKo2yGPoPfqAxPsxtz49JaQUAu0RmSGQPqrUZ4bMiI80UG38Sjdl2IHI2PuLCGUvPKjdcwAWtNtEdsNuXdek9cXfU0Do6wcVL33wQtW3WUXudOajYQlv7r8aFWvk/s0/sql-snapshot-step04.png)

5.設定散發項,可以是資料表或檢視或函式...  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1d_4KF0-Unm804OOmdzmSH6VJh-x6XLt1NSaApUYDEOZmjBTy_DDIKB2FKWi-dWCDl-6cftRtVBWxw1cRvY-TvMyzISSapTCcoRgKWfoGZwiNCjS8CUrOcfqMQPulYJwO0FR9kpDdK0o/s0/sql-snapshot-step05.png)

6.因為散發項包含檢視,所以提示相關訊息  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxr6cm-vngEa-8xRgzmJ8DmwLnc18Yon_JfAZ-kMHoWWOH7xd-mf_7QB2p3HaBRrc-LXZFTOM5QqFNLF8JZTVhjijQOaedC2BpHQgdp2qSP1Y6VYsHf3nUnAaCwyE1Vc7AjQGXEtQuucg/s0/sql-snapshot-step06.png)

7.設定散發項的篩選條件  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgtcFcwipro8PYgk3PR3sQ4ICwlnX6g1T4AZdYBu_7ES8-pwH8mhcqWDmd-O9JWScDPgJWjmPkyaU4P24oHBurPQnWs3cBOjlfvCD68oc9_mBfaxOzaVlZ-VVg6OwV12Ort0ckIkomHV2o/s0/sql-snapshot-step07.png)

8.設定代理程式執行複寫的時間  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg03kTUKUWafJIM-7ad3IwMQSFqI8wQjTsUalf8uyEpL1dBxXgUeWBC-FR3e2qR2xBv3gmH8uqXqa8vG4M5saAwkbbjPk23s9k6liWAjXDa1VoSNHWVkhivdReVxCFPXwF1bamQkHhBWrs/s0/sql-snapshot-step08.png)

9.設定代理程式執行帳戶  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7OlWxa-gtMXWA1kJmkiCi9tr8ZbpZ5hpTb03V0Ros_LLAbJ_KbEXWZGNE8vRm3QljPYMl8UNK5qOSLUxJ10Rvhs-0jHypWGJ38SNxUKYoehvEEpZA1lY5gtPAgQeP9_ZFhDtYMRtHocU/s0/sql-snapshot-step09.png)

10.完成設定  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzRPV7Xf_4WQ-O_vKSmf74XnS7ISmHncnl20LhGVGknsT4nxRlkY3IyySVzcr9sv6wELuzKme38OMBniRAnZaRQqhFiK8bSeqvTbfAJEwBKhMWn9GwTRs0zPyGqggKahk7dXkNdqRKx6Y/s0/sql-snapshot-step10.png)

11.設定發行集名稱  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN3sGOCDDEOZckxdpBv_q2K3zdQzsSz7z9ePH70DMLhuMPOc9ZtLnH5-rIPiGuGyOH53RbkAUzeojVG3xsRj1avf8iFIroAD4I1QcPnGRwQS-58pClgDBccPxmwR548bcI6ioqiOU60Fk/s0/sql-snapshot-step11.png)

12.建立發行集  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZP_kRTa0aKWhrGIg2dYGOO-DD3qBqkYl1184yWvQ2XaTrXkH1uVLbuietGkJAhnLtf3WVraVldyB2G7v0vWbklr7p6fvmuyEOCVGANc_pWf9TZRiBtnqFbqmEV11v10g01dVWc9efBUE/s0/sql-snapshot-step12.png)

13.此為 SQL Server Agent 未啟動，所以發行集建立失敗  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvDklfFIC0CcSuvr3gnq5Q4D9nWg2_yO2Rkz9aUDff9L687UbgIvrGQtnv-tR3IZlE1pHYgHbRDG09rqzrb7XBN01XFuUYM7iHEHcghEbmSo-kY7a4UWCLUH_HnJ8kMEiW6vysCwvpCno/s0/sql-snapshot-step13.png)

14.啟動 SQL Server Agent 後，可以再由發行集的狀態功能中重新啟動。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZ8Z7ni5dhXBA8dodJJ7kiQlRbLBQHRBGpyok768_yUU5T5vuweC6dFDXOdJbeW068gc3gnJ5Dh2DZW_35EBjhULnuNvCNhzzzJ6ukuSBPSZ_VI5gnaidbHZIp3BrMo5GMu2vlLQv-uFE/s0/sql-snapshot-step14.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnOgukII8ieraFCRua4zqhd62T0aRwsnh1WKdMADX4x-EGdQ47BIh_kg75gYKKjiehpOghfZCi700Cvptv-dym6kdmlEhW0HWr55pdrAJ7pXCSoqgLzG05kQhAC6tukLPs5ZSgX-8oOGg/s0/sql-snapshot-step17.png)

15.若要變更設定，可以在發行集屬性中設定  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhZEAiTj0-6xJsc983jNEGdkfAEwkkYCn0vXwnNFL2AxtUdwdG_trDn7vS-YJh52R_Zv_53R6ktLrWqSBl15br1F0CFstZwBRe9xtf7Gc32pRwZwEIoH218NiUHXcarKI6jI5JkYtp6_Ls/s0/sql-snapshot-step16.png)

## 建立訂閱

#### 建立訂閱有二個方式：

要建立提取訂閱，可以由發行者建立，也可以由訂閱者建立。

例如：若要由B執行個體去訂閱A執行個體上的發行，有以下二個方式建立訂閱

1. 在B的本機訂閱上，執行「新增訂閱」。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgE6V632CStpb3Qn2JPNvvQfh4fOv0k9vSlHz3xaEYWAKJWd1-4Blm2h7bDexvRsxoSPZMvemrYkv0eXxCnaCj9r2BZJpols48jsmImEU240kyDewIgcpFJ47FhH34XVnxhhWaFxtoqapk/s0/sql-replication-subscription-01.png)
2. 在A的發行集上，執行「新增訂閱」。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCNiYlnhCExlR72D_C2wwf0K5j6g9u0B5LeI7YHCz7N1a2zelyoTdpm3klFc_HiXXl3ZfawdJI5E7wpN59G3Amszqo34OAFZ-FVp3Icg8oNGrOen0itUlROo9k-5LfMoWyfAiS2YRQx54/s0/sql-replication-subscription-05.png)

下圖中，  當建立訂閱後，在[本機訂閱]中會產生訂閱者。  而在[發行者]底下，也會列出有哪些訂閱者。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgF9gwM-e5thJJXYAUI_CCSQ6R3R2_aQJvoLQfKNhG8efk3ga3IoRAj-_Hun_M6o7ayz1BWZK3WrqAxTJBgyX50XHZJm5Y0Qo1Fzh22IH0YmmP1jp8OvUefACUFyOZ_frLfS1kmEw-VccQ/s0/sql-replication-subscription-02.png)

一個發行集，可以有多個來自不同執行個體或資料庫的訂閱者。如下圖所示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4nansR6EXdZft4xZl3NURFfEubb6SgAkE5MbFVu7_gRZoe3BOFEFaR1-drSMoUUFmnarVWgX9OBM9_TWiaK2YFW6Mh7tjUFNcsD1ZgDu8RxZJy1DeLnJuDEQcRwOxca2hdJC3ECkm4ag/s0/sql-replication-subscription-03.png)

#### 使用發送端的代理程式建立訂閱

在設定訂閱過程中，有個步驟為「散發代理程式位置」，這個步驟是決定要用發行者或者訂閱者的代理程式。  例如，SeverB 執行個體雖然沒有啟動 Agent ，只要在訂閱時選擇「在散發者端執行所有代理程式」，仍然可以訂閱。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkgBpoAwSa0EaVX5sEKr-99EdkAGGG16Lrw6yd_gdELktpy3VUa8pCjAgshHISUc9ATNxLFDs-HvA_RUPr4piHl0lNfhEai3aEonBXhSRQHqIcoNo8M__o9RJn_e6C_Vou1f7sxAhph8M/s0/sql-replication-subscription-04.png)

## 發行與訂閱的排程

發行與訂閱的排程是各自獨立的。  
發行排程是用來決定何時散發發行集，訂閱排程是用來決定何時接收發行集。  
訂閱排程通常可以設定成「當 SQL Server Agent 啟動時自動啟動」，那麼當發行者散發發行集時，訂閱者就會同時收到發行集。  

若要變更這些排程，可以在 SQL Server Agent 的作業結點中，找到各個作業後進行排程的變更。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFwG4egN5l9pgf-MgoIr4utF3CcbHKK8fE9-cH_hrQPyKEgw9CCiyLTcJc0BWSo-gv_iqM6RQj2DwpTkK1KvF9r9UJ1jCpREmHzcpbqp_AQqF4y2L2T74tx8yzu1KsW6rzsVFxukdKcP4/s0/sql-agent-schedule-1.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgr-DHMpMPgtV-VD7xOc4L_ajti8aqtb2KJHEE_2QT5oLpaP9aKot-yfurihhC9zGg3ZCIC7aKArBMRQB_YkyiC67SzC_seAT2-eLCuemspi14OvPRG9vKPMq6k7aqdo96un-FMsvwx2Tg/s0/sql-agent-schedule-2.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjP35rgcKOFlYyllyBFOMLKP_fPGIx7Yh4xXAhbAogbRDPPtB9fIHptmC1yDhmJ5MQ4EQVYkry7VfS1M4cqi-npZXRIFCRucZGiPqgQuD1Ebc6Ygs-E63b0oiOQSn7xNGF8AEVwEUhh1cU/s0/sql-agent-schedule-3.png)

# 監視複寫

「Microsoft SQL Server 複寫監視器」是一個允許您監視複寫拓撲之全面健全狀況的圖形化工具。    「複寫監視器」提供了發行集和訂閱之狀態和效能的詳細資訊，可讓您回答常見問題，例如：   

- 我的複寫系統狀況是否良好？
- 哪些訂閱的速度慢？
- 我的異動式訂閱落後多少？
- 目前一個認可的交易要到達異動複寫中的訂閱者需花多久的時間？
- 為何我的合併訂閱速度很慢？
- 代理程式為何沒有執行？

除了呈現複寫拓撲的資訊外，「複寫監視器」還可讓您執行一些工作，例如啟動和停止代理程式，以及驗證資料。   

## 啟動複寫監視器

「複寫監視器」可以從任何 SQL Server 執行個體上的 SQL Server Management Studio 啟動，也可以從命令提示字元啟動。    

以滑鼠右鍵按一下 [複寫] 資料夾或其任何子資料夾，再按一下 [啟動複寫監視器]。 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHzs0uj4B7eiNo4MTojU7Wr3m11HotpfrEXJF2Os0uJlHfpqLB5XiCg61jfJrJ32hpxub7sEieU_E4rc6I0qU9jHCWNUZHIx2jVF-0pPmaq4gDz1QTyc36JKPRXlW2olosP21lCDEgIjY/s0/sql-start-repl-monitor.png)

## 從複寫監視器加入及移除發行者

在啟動「複寫監視器」之後，可以將一個或多個「發行者」加至監視器以進行監看。

### 加入發行者

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhySX-7dl4rPF4efgja6LEedO173VTwHsTIp-4xATPgspSr1JaMotJEcBvsvIV0ANnCeA2LW8qDXoRILKgh4VhHRz5xdqBkdG-GhZmOcYLAkCL9VpJNcMKJ5LoagCF6VrFtxW04ftJAX7U/s0/sql-replmonitor-add-publisher.png)

### 移除發行者

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwyy2IVagXFkJGUkBmW-HMrwbuwGo8n_yJLPpRbySPvMLcWEYUosVvo0amZLFWoYrnIIXAaAK6R0DypkiXpSu8xYj54EQlqtJbI7UzlG8rHqlJLJX18SJeDRKomeaw3ct0s_UgM6nbbHE/s0/sql-replmonitor-del-publisher.png)

更多內容請參考 MSDN：[從複寫監視器加入及移除發行者](http://msdn.microsoft.com/zh-tw/library/ms152767.aspx)

## 在複寫監視器中設定臨界值和警告

下圖是「複寫監視器」的顯示畫面，在警告頁簽中可以操作警告的相關設定。  你可以選取適當的核取方塊啟用警告，或變更「臨界值（Threshold）」以調整警告發生的時機。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgf4HB_ZF3N9OUyWs445MiKj_mWCgueB4JNv5_4uHPRyyGxPdyI4CKw3ofO4GuEaRYn7MMEML5ozdTe4KyHl1OXnKJpb2u6UBap70_3f4WRkZlpB_B74LGat6xQCzJXgzJMfX9Gm0EQ58g/s0/sql-replmonitor-alert-1.png)

# 約束條件不使用複寫

操作複寫時，資料表中的約束條件，如 FK、Trigger、識別欄位等等的約束條件都會一併執行，  但是在複寫中執行這些約束條件，實際上常常是多餘的，此時你可以使用 NOT FOR REPLICATION 選項來停用這些功能。  

NOT FOR REPLICATION 選項可讓您指定當複寫代理程式執行作業時，以不同方式處理下列資料庫物件：

- 外部索引鍵條件約束
- 檢查條件約束
- 識別欄位
- 觸發程序

NOT FOR REPLICATION 選項是建立條件約束時的選項，例如下面操作是將識別欄位設定不複寫：

#### 使用 SSMS 設定識別欄位不複寫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEB_M8qTRrgGb-jH5c9ZGdK5RI3L776lAS4RZhvwHB-4Th8-r8kHNVs6yRDN8sjCGS9Nm8JohTe8RKlNZ7UCF0iucSD4xDbYN9jU1er62v4-Kb5uw9Uh9AHnkQ-sdlesVdhzDlydj3CSo/s0/sql-id-not-for-replication.png)

#### 使用 TSQL 設定識別欄位不複寫
```sql
CREATE TABLE [dbo].[Products](
[ProductID] [int] IDENTITY(1,1) NOT FOR REPLICATION NOT NULL,
[ProductName] [nvarchar](40) NOT NULL,
)
```

# 整合異質資料（Heterogeneous Data）

對 Oracle 和 DB2 資料庫，SQL Servr 提供以下幾種整合異質資料的方法：  

- Oracle 資料庫可以是 SQL Server 的發行者或訂閱者。
- SQL Server 資料庫可以是 Oracle 或 DB2 的發行者或訂閱者。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3lGvs-bZ9-T47e8juH2A4IWKFulmyJDtG4XIvYvIEkhGbajdTJ69T7_wUHIONrEQZ6m_mPOpGykCRFC6ElREZ8dPmWlzOqfudbIpNWn8DV6Sr816ckaXVwlynzWgD8vcVuu4Iekm_sZs/s0/sql-repl-heterogeneout-1.gif)
- 由 Oracle 資料庫發行到 SQL Server 的資料，可以再從 SQL Server 散發到 Oracle 或 IBM 訂閱者。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_VAdYrNMAmKYw0pBbqyvOyPfwpwk_dySjuTeRNFnZpJpPIvLCmwYJjll8IQau8RAFJCz1lmd5XUuTdEtrsfVCJGZiWQS6pBTKc60vz3otydq77G3lGhuJGacWT124zargNCAdsMtKIEs/s0/sql-repl-heterogeneout-2.gif)

但有以下限制：  

- 不支援合併式複寫
- 不支援點對點複寫
- 不支援從 IBM DB2 發行到 SQL Server
## 參考資料  

- [SQL Server 複寫](http://msdn.microsoft.com/zh-tw/library/ms151198.aspx)
- [複寫發行模型概觀](http://technet.microsoft.com/zh-tw/library/ms152567.aspx)
- [複寫類型](http://msdn.microsoft.com/zh-tw/library/ms152531.aspx)
- [快照式複寫](http://msdn.microsoft.com/zh-tw/library/ms151832.aspx)
- [異動複寫](http://msdn.microsoft.com/zh-tw/library/ms151176.aspx)
- [點對點異動複寫](http://msdn.microsoft.com/zh-tw/library/ms151196.aspx)
- [合併式複寫](http://msdn.microsoft.com/zh-tw/library/ms152746.aspx)
- [監視複寫](http://msdn.microsoft.com/zh-tw/library/ms151780%28SQL.110%29.aspx)
- [使用 NOT FOR REPLICATION 控制條件約束、識別和觸發程序](http://msdn.microsoft.com/zh-tw/library/ms152529.aspx)
- [整合異質資料](http://msdn.microsoft.com/zh-tw/library/ms151835)
- [SQL Server 的分散式資料複寫技術](http://technet.microsoft.com/zh-tw/library/dd125513.aspx)
- [建立提取訂閱](http://technet.microsoft.com/zh-tw/library/ms151225.aspx)
- [建立發送訂閱](http://technet.microsoft.com/zh-tw/library/ms152566.aspx)
- [如何：建立發送訂閱 (複寫 Transact-SQL 程式設計)](http://technet.microsoft.com/zh-tw/library/ms146912%28v=sql.105%29.aspx)
- [如何： 建立提取訂閱 (複寫 Transact-SQL 程式設計)](http://technet.microsoft.com/zh-tw/library/ms147346%28v=sql.105%29.aspx)