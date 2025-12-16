---
title: Log 與 Audit
layout: default
parent: SSIS
nav_order: 10
description: "Log 與 Audit"
date: 2014-01-07
tags: [SQL,SSIS]
postid: "4724635054369208787"
---
# Package Logging

在 SSIS 之中，要記錄Logggin大至可分成二個方向。**事件狀況**與**執行過程**。  

- 前一種情形，你可以在封裝中透過 event handler 或者 data flow 元件的 error output 來設計，或者在 precedence constraint 中針對各種不同的情況設計回應。
- 後一種情形，則是本節要學習的「記錄(Logging)」。  你可以在封裝中，利用「記錄提供者（Log Providers）」來記錄執行封裝之操作員的名稱，以及封裝開始和結束的時間。

#### 「記錄提供者」包含５種類型：

- Text file
- SQL Server Profiler：將記錄儲存在事件追蹤器（SQL Server Profiler）所指定的檔案中。
- SQL Server：將記錄儲存在 SQL Server 的 SSISDB 資料庫中。
- Windows Event Log
- XML file

## Configure an Event Logging

1. 在封裝設計的區域，由右鍵選單中，點選 [記錄] 以開啟 [SSIS 記錄設定編輯器] 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtc8OU1WMNpFW-tEfZyJ-WuZqG1317uHkEPO0qWGTYqH6qnNriz9L9d2mZTq9I0ev_QzHPeWQwv2CO6c-sbCqxySIU2p2yP6mo7aXcUDxLGLSy3Lf-RNWcs0CB0QNTI4E5QYdVJO-V2Mg/s0/ssis-logging-1.png)

2. 選擇你要的提供者類型：（不限一種）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHRYLPKyyp06NiLNA7GJJwCuLKRrZvdfXF-CQfml1vA8h2UlNHLCBT8aPWJe1_LEx0ZAedCKbNV0SB4ETH_0S481Q4SfHu5KiGGGt8KccWLYdAPKIQxRXAqOpsc-lEZ_0JyWNhcdRNoKM/s0/ssis-logging-2.png)

3. 勾選你要記錄的容器。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXWr8W-CDQ8zhNy0YzGsKyhKdEBaf1EBN7SB6eC1_xbG3SbZgHFHI9xcp3mh5ocn4QPb0p3jTZ6o5RyfLLbUpFvRAyIot6DxmCXgt99RY0Lom4h4MkzDsncH2B8roOdk5OPOmBdrRzG20/s850/ssis-logging-3.png)

4. 勾選你要記錄的事件。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgG3v3xrv7qLANoeesBjgzCcaCa0-C2jt0zJ-dp46awu4JiVbu51xOU2obxwkX61oj476XacY-WriJBYSm5wyF0y5tT1B0IiLewyJf62SiLlp_rxp64Fq6wANow05OEn5KdEUv8lBF7yqA/s850/ssis-logging-4.png)

5. 若使用 Windows Event Log 提供者類型，那麼在封裝執行後，就可以在 Windows Event Log 看到以下的記錄。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiXKcvXVHWtqu_mUuEP7tgPLegZeYTQ3Bg2DWaR4D4Q6PYj-5uBeuz8eDCXOXi2rfFMEB_YhFVj09dC6XZuRB6i47oS6nizQXHt0i7DLj8FVGtSvvSEV73lbfyTbXFfuSPRDNfCIhl7X8/s0/ssis-logging-5.png)

6. 若使用 SQL Server 提供者類型，那麼在封裝執行後，就可以透過 [catalog.executable_statistics](http://technet.microsoft.com/zh-tw/library/hh479592.aspx) 查看以下的記錄。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEge27oFDl5R_3QtVh1ZIdb3QhhRWR5UhOJpniHVBP0s9GSk5y-tgDmGLy6IPB0M_NCayNw74mJRp1mdXKmLlLLfjwjG34EO8_3gU9XwySzWr0FBmg3qfyrBeTN6gtfdSSAMjV3MS-QW8i8/s850/ssis-logging-6.png)

# Package Auditing

「稽核」通常是指**收集資料被變更的過程，它關注的是：**。

- when the data was changed
- using what operation
- who changed the data

「稽核」與「記錄」最大的不同在於：

- Logging captures information about how a process is being executed (the events, as they occur);
- Auditing provides an overview of what is being processed (the data itself, or aggregated values based on it).
- 稽核在意的是資料變更後的狀態，而記錄在意的則是變更過程的狀態。

## Auditing Techniques

#### Elementary auditing

#### Complete auditing

## Auditing Components

### Row Count Transformation

### Aggregate Transformation

### Audit Transformation

# Package Template

#### 封裝範本

在設計封裝時，如果每個封裝中都有一些固定的工作要執行，或者你希望每個封裝都可以執行固定的logging或auditting等工作，  你就可以先將這些會在每個封裝中都必需執行的工作，預先在一個封裝裡設定好，再將這個封裝存成範本，往後就可以使用這個範本建立新的封裝。  

## Creating an SSIS Package Template

封裝範本預設存放目錄為：「C:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\PrivateAssemblies\ProjectItems\DataTransformationProject\DataTransformationItems  」，你只要將你要拿來當範本的封裝，另存到該目錄即可。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCUJ4ewFI7VjoYp9-VmZ4dpf_izeCTrke0BrmYmYSXKagsqv94zDSEYMU8SueyA7WUi7WbjI3XeQsLeEd_o1cXyZZr4HACZgcpVjXc3ovA6v1wlaSfdOjTqToxBxB2_C8u4TI_Xnq43wo/s0/ssis-package-template-1.png)

## Using an SSIS Package Template

若要使用範本來建立封裝，你只要在專案下加入「新增項目」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjN7jM44pC4wQ06hibNqbR_bbIFkLbg6RiiQU4nUiF0Q4N6Fvg8juGT4Am6PGsX4k5v1VYvWid7OdCJpBQ5KLul6mRMODU6oxwttIKcr9oUnCvhrqINLP6ZLZL_3HtLcJXOrPxO8eT-_UE/s0/ssis-package-template-2.png)

自訂的封裝範本，都會出現在這裡。這時就可以使用封裝範本建立新的封裝。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhs2I0lrNfg7pWypuhX3RBiJy_8gaO5S9cfnY0iyR4ngQ6MUDCn3eCG6ZYa6c1k6JwxY6GfG-fmfpFdCDQinoAzbtYX7N8w9bLOL3PmXpWIM1OLLobXTox35t2NLHer3_7xFa890XOi8SE/s0/ssis-package-template-3.png)
## 參考資料  

- [Integration Services (SSIS) Logging](http://msdn.microsoft.com/en-us/library/ms140246.aspx)
- [將封裝儲存為封裝範本](http://technet.microsoft.com/zh-tw/library/ms345191.aspx)