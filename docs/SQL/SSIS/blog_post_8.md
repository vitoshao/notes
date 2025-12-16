---
title: 強化封裝
layout: default
parent: SSIS
nav_order: 8
description: "強化封裝"
date: 2014-01-03
tags: [SQL,SSIS]
postid: "555810531989281291"
---
SSIS封裝除了可以用來執行資料轉移工作，它還包含許多其它有用的功能，可以用來強化整個封裝。例如：  

- 使用 **error handle** 來協助封裝測試與問題排除。
- 使用 **transaction** 來達到封裝的交易能力。
- 使用 **checkkpoint** 來接續封裝上次執行失敗的地方。
- 利用 **event-handling** 來補捉各種不同的事件，如 OnWarning, OnError 。
- 

# Package Transactions

封裝也支援交易機制，而且你可以在以下層級中，設定使用交易：

- **package level**
- **control flow container level**
- **task level**

另外，因為封裝的交易機制是由 [分散式交易協調器（Microsoft Distributed Transaction Coordinator, MSDTC）](http://technet.microsoft.com/zh-tw/library/ms190799%28v=sql.105%29.aspx) 負責處理，所以若要使用封裝交易，必須先啟動電腦中的 MSDTC 服務。

## 交易選項（TransactionOption）

Package、Container 或者大部份的 Control Flow Task 都有 **TransactionOption** 屬性，用來設定是否使用交易機制。  有下列三個選項可以設定：  

- **Required** ：If a transaction already exists, join it; **if not, start a new transaction**.
- **Supported** ：If a transaction already exists, join it (this is the **default setting**).
- **NotSupported** ：The package, container, or task should not join an existing transaction.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRBvkMHYG4nOBiwz0kDikygcLGeNZhuOM8Ii3SmLqZlrVHA6Bh4ChE8WGV10d-Hhm-ffOXaWyWTmaGCexSAgHvu8ATSjfFxIKHIrn69oaTi0Um_F-mLpr-j3lS8Vb0OBDgcevjSqlPlxs/s0/ssis-transations.png)

## 隔離等級（Transaction Isolation Levels）

SSIS 的交易機制提供以下幾種「隔離等級(IsolationLevel)」的設定方式，此屬性的預設值為 Serializable。  要注意的是，唯有 **TransactionOption** 屬性的值設定為 **Required** 時，系統才會將 IsolationLevel 屬性套用到封裝交易。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgriJpLpZZ0Re5PQrcVftv8mGwlYjo84Fyp-OThFPfP-SV6prgn5i4bWQkq3HZMyeOsAKQ7of7qxRgWFJ5-nyWukUO2p95w79pul83KYRsP0DiIDEoZEaYOIAFChF0_I3y8nD2I4VI-rm4/s0/ssis-trans-level.png)

1. **Unspecified：不指定隔離等級。**  
這個選項設定目的，通常是希望延用父層的設定。
2. **ReadUnCommitted：允許讀取未認可資料列**  
允許讀取未認可資料列。這個選項會有**髒讀問題（Dirty Read）**。
3. **ReadCommitted：允許讀取已認可資料列**  
不允許讀取尚未認可資料列。這個選項雖然可避免髒讀問題，但還是存在**不可重複讀取（NonRepeatable Phantom）**或**幻讀問題（Phantom Read）**。
4. **RepeatableRead：**  
這個選項除了不允許讀取尚未認可資料列，而且，已讀取的資料也不允許修改。  
這個選項可解決不可重複讀取問題，但還是存在**幻讀問題（Phantom Read）**。
5. **Serializable**  
這個選項會鎖住整個交易中的資料，在交易完成前都不允許其他交易讀取交易中的資料。  
這個選項可解決幻讀問題。
6. **Chaos**  
此隔離等級與 ReadUnCommitted 相同，但在執行寫入作業期間，會同時檢查所有相關連的交易，以避免資料被覆寫。  不過 SQL Server 本身並沒有這種交易模式。
7. **Snapshot**  
使用複本資料進行交易，不鎖定原本的資料。但是，在封裝中無法使用這個隔離等級。

注意事項:

- SQL Server 不支援 Chaos 模式
- 封裝交易不相容Snapshot屬性。因此不能將封裝交易的隔離等級設定為 Shapshot。 而是要改用 SQL 查詢將封裝交易設定為 Snapshot。
- 容器必須明確指定 TransactionOption 屬性，系統才會將 IsolationLevel 屬性套用到封裝交易。
- 如果父容器有指定 IsolationLevel ，子容器會聯結父容器的交易。也就是只有當容器起始新的交易時，才會接受容器所要求的 IsolationLevel 屬性值。
- 交易機制是針對 Task ，而非 Data Flow 。所以無法只針對 Data Flow 中的特定 component 設定交易。

## Manually Handling Transactions

You can do this by explicitly opening a transaction on the database layer, using the [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) and then executing an SQL statement to open a transaction.
```sql
BEGIN TRAN;
...
COMMIT TRAN;
```

To manage transactions within multiple SSIS tasks, you must enable a behavior that allows you to retain a connection through the tasks.   This can be done by setting the connection manager's **RetainSameConnection** property to True.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqJSyVnh99YpA61dbvCv7rZO9jfCEaPbgd6Ne7MNohVyUOfQsSD3nu6WPUCMWRU8kuUdtpocTJpUElCqhCl6qjW10_3OwcvJ-kLG0Bx1qj4oubmqBezzOXc_umD5lH89Y1Oxeqht4zIKw/s0/ssis-retain-same-connection.png)
## 參考資料  

- [Integration Services 交易](http://technet.microsoft.com/zh-tw/library/ms137690.aspx)
- [設定封裝來使用交易](http://technet.microsoft.com/zh-tw/library/ms141144.aspx)
- [設定封裝屬性](http://technet.microsoft.com/zh-tw/library/ms137749.aspx)

# 檢查點（Checkpoints）

有時候一個複雜的 SSIS 封裝可能執行到一半失敗了，SSIS 封裝允許你將封裝設定成，由上次失敗的地方開始執行，而無需重新執行整個封裝。  你只要將封裝設定為使用檢查點，則封裝中，完成執行的檢查點，相關資訊就會寫入**檢查點檔案**。   當失敗的封裝被重新執行時，就會根據該檢查點檔案，由上次成功執行的檢查點後面開始執行。  

使用檢查點有幾點要注意：  

- 重新啟動是針對控制流程，而無法對資料流程設定重新啟動封裝。
- 單獨一個 Task 或者容器中的 Task 會被視為一個最小基本工作單位。
- 「Foreach 迴圈」、「For 迴圈」、 「交易容器」也會被視為單一基本工作單位。
- 封裝重新啟動寺，不會重新載入封裝組態，以確保封裝在重新執行時使用與其失敗時相同的組態。

## 設定檢查點（Checkpoints）

使用檢查點，你必須先將檢查點功能設定啟用，再依需求，將特定的 Task 或 Container 設為重新啟動點。  

### 啟用檢查點

啟用檢查點的設定，是屬於 package-levet ，所以你可以在封裝的屬性窗格中，看到以下設定項目：

1. **CheckpointFileName**：指定檢查點檔案的名稱。
2. **CheckpointUsage**：指定封裝執行時，是否要使用檢查點。
- Never：指定不使用檢查點檔案，且封裝從頭開始執行。
- Always：指定總是使用檢查點檔案，且封裝從上一個執行失敗點重新啟動。如果找不到檢查點檔案，則封裝會失敗。
- IfExists：指定如果檢查點檔案存在，就使用該檔案，封裝從上一個執行失敗點重新啟動。如果找不到檢查點檔案，則封裝從頭開始執行。
- 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcWNAbdQuuBiUANzB0Pa88yDSdjtxrGsrtcc2tbSOqZsxGw8ChuRUhlfSsbHfYOQlibIJO3SuB68FFE4wKofTtPg3dPAwETE2cHDacgETwVjumR7imncMfz6VZzCl6ZX0mgI_sJxnB-7Y/s0/ssis-checkpoint-1.png)
3. **SaveCheckpoints**：指出封裝是否儲存檢查點。  必須將此屬性設為 True，才能從失敗點重新啟動封裝。   
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgvy3nU5aNI-O9TQcSVswmATpdl0uTfC1mF_kLyNze2vpfxZQfHPz0FnbmKumoWSD-NcVSp32akEFjYf3lowlV-e9gNb40ttuPcfj6gdio4oOG9M_giEraN-zkm0IZqgz1qdkLnKYSSlh4/s0/ssis-checkpoint-3.png)

### 設定重新啟動點

完成啟用檢查點之後，你可以針對要做為重新啟動點的工作，將其 **FailPackageOnFailure** 屬性設為 True ，若該檢查點完成執行，就會將相關資訊寫入**檢查點檔案**。  如果想要將容器當成一個重新啟動點，則必須將容器的 FailPackageOnFailure 屬性設為 true。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjd4nJWSTKDtr-fnf4ANVQU6orC6wBGwX1HIF-txOVQuA_AGpxg-0zwfmMFRnf_9ovvLIbGeMPFzhfpVTZdheXZ5V-Q3JZXMU0kyR9rOrtynC2udH4GxBgc-FmzdOt1XcDG_wG6iFb-QGY/s0/ssis-checkpoint-2.png)

檢查點執行過程說明：

- 若**CheckpointUsage=True**，封裝啟動時會先檢查**checkpoint file**是否存在，有則找出失敗點，由失敗點開始執行；若不存在則由工作流程啟始點開始執行。
- 若SSIS封裝設定要記錄檢查點（**SaveChckpoints=True**），則每完成一個檢查點（**FailPackageOnFailure=True** 的 Task），checkpoint file 就會被更新。若某個檢查點失敗了，checkpoint file狀態就會維持在上一個成功的檢查點。
- 當所有檢查點都執行成功，checkpoint file就會被刪除。

注意：**檢查點屬性（SaveChckpoints）**無法和**封裝交易屬性（TransactionOptions）**同時使用。
## 參考資料  

- [Restart Packages by Using Checkpoints](http://technet.microsoft.com/en-us/library/ms140226.aspx)
- 
-

# Event Handlers

在 SSIS 之中，Package, Foreach loop, For loop, Sequence container, Task 等物件都稱為「可執行檔（Executable）」。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRLVP_D6lW8orF-IprWD5L4CAey9h7jR68N8jcrLHbbqcj0s05IsvYCLPYHbGSFJUhFgam8YhLr0RwO59ReDN0E_Gzm6489lJ_v0Gd-ao_dh8DBp8xv_c4x9MXmIQQ8V8A3EpfcnfVryA/s0/ssis-event-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhu0CxHs_oiVr73x4tkjCGY2cwrm6cyYmDhufer6HJiHNV6PtNtVf0sA8Um4MLnFwGeQ22I_Djf3Ilg4bzOr2ZJ4n55OSzIszK7vHaVWB6Z_acPgeaa7nx8WU-5Zhok3fnKh3ZuD5vgIhc/s0/ssis-event-2.png)

可執行檔會引發事件，用以擴充封裝功能，例如擴充下列工作：   

- 封裝或工作完成執行後，清除暫存資料儲存。
- 擷取系統資訊，以在封裝執行之前評估資源可用性。
- 當查閱參考資料表失敗時，重新整理資料表中的資料。
- 當發生錯誤或警告時，或工作失敗時，傳送電子郵件訊息。

Note:

1. 如果想要關閉所有的事件處理，可將容器或者工作的 DisableEventHandles 屬性設定為 True.
2. 事件發生會向下延伸，例如若 Container 發生 Error， Container 中的 Task 也可收到 ErrorEvent。

## 執行階段事件

下表是可執行檔在執行時，可以引發的事件類型：

| 名稱 | 說明 |
| --- | --- |
| OnError | 發生錯誤時。 |
| OnTaskFailed | Similar to OnError, but runs when a task fails rather than each time an error occurs |
| OnWarning | when a task returns a warning event such as a column not being used in a data flow |
| OnExecStatusChanged | when the execution status changes to In Process, Success, or Failed |
| OnInformation | 當SSIS在驗證或執行Task期間發生訊息回應時 |
| OnPostExecute | 當該可執行檔完成執行後 |
| OnPostValidate | 當該可執行檔完成驗證後 |
| OnPreExecute |  |
| OnPreValidate |  |
| OnProgress | 當可執行檔的進度可測量時 |
| OnQueryCancel | 當中斷執行時 |
| OnVariableValueChanged | when the value changes in a variable for which the RaiseChangeEvent property is set to **True** |

## 加入事件處理

上面提過，可執行檔可以引發事件，所以，你可以針對特定的可執行檔，特定的事件，撰寫適當的處理程序。  要在封裝中加入事件處理程序，可參考下面步驟：  

1. 切換到「事件處理常式（Event Handlers）」頁籤。
2. 選定你要的可執行檔。
3. 選定你要處理的事件。
4. 由 Toolbox 中，拖拉適當的工作到事件處理常式頁籤中的設計區，建構處理程序內容。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg_v9063-XrBZP8HbPq6l2ld3EtdmuBiYsE2MKf-Wodh8tGzqxpzqKkGeWQhFXP9e7ekAVF3L1lh5BmncRsJa3XuCH9ueAtEotNXuaSz_ala7VjJWLLVgDJHoxyRE5ok8dz3BrhaOpz6sM/s0/ssis-event-3.png)
## 參考資料  

- [Integration Services (SSIS) 事件處理常式](http://technet.microsoft.com/zh-tw/library/ms140223.aspx)
- [Integration Services Transactions](http://technet.microsoft.com/en-us/library/ms137690.aspx)
- [設定封裝屬性](http://technet.microsoft.com/zh-tw/library/ms137749.aspx#Checkpoints)
- [SET TRANSACTION ISOLATION LEVEL (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms173763.aspx)
- [使用檢查點來重新啟動封裝](http://technet.microsoft.com/zh-tw/library/ms140226.aspx)