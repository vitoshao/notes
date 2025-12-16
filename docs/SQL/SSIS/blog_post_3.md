---
title: 建立 SSIS 專案
layout: default
parent: SSIS
nav_order: 3
description: "建立 SSIS 專案"
date: 2013-12-16
tags: [SQL,SSIS]
postid: "7770088821926191300"
---
要建立倉儲資料，就一定會用到資料轉移。  要轉移資料，使用匯出匯入精靈是最方便的，但是如果有下列情況，它就變的不太好用。  例如：轉移資料前必須做適當的資料修改或資料轉換，或者轉移的資料必須與目標中已存在的資料做合併。  而 SSIS 正是用來協助執行資料轉移的好工具。  

- [認識 SSDT](http://vito-note.blogspot.com/2013/05/visual-studio.html)
- [認識 SSIS](http://vito-note.blogspot.com/2013/07/ssis.html)
- [認識 SSAS](../../70463/P0_Labs/lab01-ssas-practice.aspx)
- 認識 SSRS

# Introducing Control Flow, Data Flow, and Connection Managers

Before you dive into SSIS development, you should be familiar with three essential elements of every SSIS package:  

#### Connection managers

Connection managers provide connections to data stores and allow the connection to be defined once and used many times in the same package  

#### Control flow

A package can consist of one or more operations, represented by control flow tasks.  Execution order is defined by how individual tasks are connected to one another.  

#### Data flow

Encapsulates the data movement components : ETL

- One or more source components, designating the data stores from which the data will be extracted
- One or more destination components, designating the data stores into which the data will be loaded
- One or more (optional) transformation components, designating the transformations through which the data will be passed

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGYVDFhdqVIuKXAUCrmn4bEEgcohDdVXa8rvjPUdQXFz7F3NIEViWnG9vXy0nyQOMqIPNde-WTxEN1TDWBL5tHLYtG14n6_SJwx37NPVE4dElsX6i_2pEH_3W6RGso2S0qmVek8WLjf9g/s0/ssis-add-exist-package-1.png)

簡單講，  
Connection managers 定義 data sources與 data destinations 的連線設定，  
Control flow 用來控管 SSIS process 的作業流程，  
Data flow 則是真正執行 ETL 的核心部份。

# Introducing SSIS Project Deployment

Typically, the principal difference between development and production environments is in the configuration of data stores.   In development, all data can reside on the same server and could easily be placed on the developer's personal computer.   Therefore, you should account for the following differences between the development and the production environments when developing SSIS solutions:  

- Connections
- Data platforms
- Security

In previous versions of SQL Server, it was possible to configure SSIS packages by using a       **configuration file** or by storing the **configuration data in a table**.  In SQL Server 2012, the configuration feature is effectively replaced with **parameterization**.

# 設定「控制流程」

You can add a **new SSIS package** or an **existing SSIS package** to the SSIS project.  

## Add an Existing SSIS Package to the SSIS Project

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh7iFjoH6yodCTAJrzDXTjTuqHL6YF3mQJsb3XLGAN8wL-FCANQ9hxQalBWcuhWmKmyC7nYp-PPJ39Wyvz8ojy8u2KcGWj1RWVPyMYgqLkHb7E3HDP3AGA6ELg2Nh7qY94hgaxj5ZeYWkA/s0/ssis-add-exist-package.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiykGPy17RuxiWQYBsE2zPAh2eef_Y13nKl4z5lnMn5BJ_lamadgXqnxWD9OJTVwBtBf5PaQ_6uRQ9ibPwdMk5I1ggK7ohH_zBUuBXfU12tlX-3zOPDBxeYtPU15QGr9sIIjY0uAShs1Qw/s850/ssis-add-exist-package-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsdxo4axeeY90sLWY8XvBCCFKEhgM4r1hBi3nLdtO3zoyjt9MGjfz_PBRrMATgNXuhErVvh4CTrHzv2PhMBk0VMtUY4eE_yu76wENjXS6iTZBofCt16DBM7ola798yMr_KHBAtkJFSLtQ/s0/ssis-add-exist-package-3.png)

## Edit the SSIS Package Created by the SQL Server Import

上面例子，我們加入一個由匯入匯出精靈自動建立的package，你可以雙擊這個封裝，就可以看見以下畫面。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7rA6WlZoUd8nRe3v9iCphypfTcEpfMVm0LFxYB9IwIUwT-UTFbwPhD8BcQ8O_eFPKA8t3z4t2Vh2ERKQr5VqSgPrxUt7E2NVPy34MK7i1c8lv921jh7gj1hTUwPZd6JvR6sRf8vslATw/s0/ssis-add-exist-package-4.png)

底下我們將修改這個 package 的內容，並且進行簡單的 Debug。  

##### 1. 檢查及變更工作屬性：

你可以雙擊「準備SQL工作１」，就可以由「工作編輯器」看到以下內容：

「準備SQL工作１」是一個「執行SQL工作」，所以重點在於 Connection 和 SQLStatement 的設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikKQh7A6AdRmxF9lCKPTo8ZO2oBFBExMqwONeMp2qXDdKdRyK4HpfLFj0iPDCIrO4J-3gT2OdGDFJrspaOjPZFWQUAa988FVZ8VDw3EzV2H87oCHor4GLYISjFwBCEDyMfGVcL4FrWKLw/s0/ssis-add-exist-package-5.png)

你也可以在 Property 視窗中看到其他的設定，如下面屬性就是用來控制當該步驟發生錯誤時，是否要停止整個 package 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhZ3flSEWUU_UhPMjJ2q8m95PZX3Ph1U8CWwSCODWGX9clivrdr612EDciMpV03oi-X8P-5VWyM5iBxewUwtoubetukECuHKxUKnJ93C-z_ZRdxPdEhXMAm5_Ifjq8Hr4ihVj802EfE8M/s0/ssis-add-exist-package-7.png)

##### 2. 加入新工作：

加入新的「執行SQL工作」，並將工作更名為「準備SQL工作２」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTaKrAKUgT722EaN4KlSxUST_Lo_ZJwJo5QL_S2vETE_J6oLeMWGFlwP9yf2dU0Gov5UquYfHgPYN6ak-ewEDi4nA_nMYYV8Gk6vfDF_23i5DuAtl7DDr-46Hbqk1P2z9txMdGU90ndzU/s0/ssis-add-exist-package-8.png)

編輯「準備SQL工作２」，如下設定：此處我們在 SQLStatement 輸入 TRUNCATE TABLE SalesOrderHeader2

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiz5yUSvdZHJtDo1ki-Xo0rxZwjwD1JQN9wqpSgjKxRClr8qHnbhbh6sGE2REgaZY5s8SmhBS-3JomM3oMyf9PTBkQL2Y4Ncn-dfQm448xBLBUZzOn1jE4J1XHuW1Xqhs6FhMVJMpktX_M/s0/ssis-add-exist-package-9.png)

##### 3. 變更優先順序條件約束(precedence constraint)

你可以依照下列步驟，設定「優先順序條件約束」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEifjhWJF_8AKY8cqtmhuFsfbKKdyLrt9JLo2GW-xDUzjoYDhUdwVpjmAxSZvl6AD1E0DLDS6ErJWAlQvlecAOf1yiD8qXtcNDjyVeggLnNV4apW2vYaTMYmPGL8qiruJPi5R28VglcFn_4/s0/ssis-add-exist-package-10.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrOW3IN8qXu0DK01n4spM-o2OJhr5UiK48x0i-UnGiCokAFSeWyfOkiUERFtONyboM4eh40D4NaCLVlCfxNEEouOZfOpO3WK-BDTgG7_0z_0D93ykgxdL4vAwnk9X-TRXRCuCA3GCuQtA/s0/ssis-add-exist-package-14.png)  

為什麼說這個綠色的線條叫做「優先順序條件約束」，你可以點選它之後，選擇編輯，你可以由此設定該工作的執行條件。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVeOypsW6DAOTN8IE8_tWSKFzBriTP2Vxj3RuZE1TlvwQH_HYEFDlXlNHp_siZNcdtfwAsV_A6qBCCXsdAOXvEAKrWveOVG2z7E2VO3NmQtcO7FmiYhIaeA4K4TLl4hLgd7Ev59aYyIWg/s0/ssis-add-exist-package-15.png)

# 設定「資料流程」

你可以雙擊「資料流程工作 1」，Designer 會自動切換到「資料流程」頁簽，你可以看到以下內容：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5jItGQd7e6ZJvqK0lUJejcKF-7QsALaE2u6fmMNNXJmZxU98wNRknPkZEFNY1aBG5F1HdEEk50AItrZNWgKIpWxEcB6rIDGna-hQmU83B8zqE8jbH_kdnjqoV_i92FsWq6t8wq773SIc/s0/ssis-add-exist-package-6.png)

「資料流程工作 1」是一個「資料流程工作」，是真正執行資料移轉的工作。  SSIS 強就強在這裡，你可以在這個工作中進行資料的「合併」、「多點傳送」、「轉換」等作業，處理完後再將結果移轉到目的端。  底下我們先簡單的新增一個移轉作業。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWUoY6FJW8338m1pWqsY_-zrBnty-UIYC3PNZiTpA3MuL7DCDOlh9Kjike1i5aHfh2WXodWMsmNFa_6yOpmyfjhRSy5C0X4M1q7CIIFWarYEMjkJ3zXUVnSBVswYuD7YkgsiniUQUUjMc/s0/ssis-add-exist-package-16.png)

# 設定「連線」

At the bottom of the SSDT IDE, locate the Connection Managers pane, which provides access to the connection managers used by your SSIS package.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsqF1iBSeN9TNTHdQMQ-WyYssVIpAN8a80BDdCTv5KdnxFShOvtoU88no30BnJIIUDaYXLYQyrVHKZOomCkOs0OiPFRM1lC5aL4jCdBMJgRdj4kHvHzBCYALS2v3bg3nnqlBG4ACZyc4E/s0/ssis-add-exist-package-17.png)

SSIS supports a variety of data stores :

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXH1T-WcOqfBMNWQx96zcvtgy81hfTdrBX6lGQqY7z0uPsI0wFaA9RK0gQhOVnZxhssKDG-5lopHZ_rYuEWIjOSV7nUDJVAjhikNN1psEDNMKFjjgp00iGjaNTrpZDQO80JLcxWAWzywc/s0/ssis-add-exist-package-19.png)

###### NOTE

When using stored procedures or parameterized queries against a SQL Server database in an Execute SQL Task,  consider using the ADO.NET data provider rather than OLE DB data provider:  

- With ADO.NET, you can use parameter names in queries, instead of question marks as parameter placeholders.
- When you are using stored procedures, ADO.NET allows you to set the query type appropriately ,   you just provide the name of the procedure and define the parameters in the Task Editor and the query statement is assembled automatically.
- ADO.NET has better support for data types compared to OLE DB (for example, Xml, Binary, and Decimal data types)

## Connection Manager Scope

SSDT support two connection manager scope :

- **Package-scoped connection managers**  
This connection only available in the context of the SSIS package in which they were created and cannot be reused by other SSIS packages in the same SSIS project.
- **Project-scoped connection managers**  
This connection is available to all packages of the project in which they were created.

###### NOTE

If a package connection manager and a project connection manager use the same name, the       **package connection** manager **overrides** the      **project connection** manager.

## Parameterization

To simplify SSIS package deployment and maintenance, you should plan to parameterize all connection managers.

For more detail , ref MSDN：[Integration Services (SSIS) Parameters](http://technet.microsoft.com/en-us/library/hh213214.aspx)

## 新增「連線」

底下示範建立幾種不同的「連線」：

### 建立一般檔案連接管理員（Flat File Connection）

你可以 package 底下的「連接管理員」的空白處，在右鍵選單中選擇「新增一般檔案連接」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixdFhMiXAsoBMUf67zMMRWvNmxTxkgQGCqRJlcxFaCsNm2LAlCBmv8p2aKBG0Q-81joQV69gnl2FbrMdYxTRiRM_52qNlm4vVS4avvMNGDoS6WzW7P2pCY5qIdjJ-gbz2odw9z1m-kSHo/s0/ssis-connection-06.png)

一般檔案（Flat File）指的就是固定格式的檔案，如 .csv 格式。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgUuUhIHbGi0bLM2lo4gm5zBCAgEoHyK_uJWHwOxkYeLMVkccTqT5Eu_tHd_yelZp4AeueaZAyesjEAhoDwz357oCGSkd-QGcDb29lpwFO_YRMSt61zwXeJMLF5Um_zU90Rcfl9jHkwGt8/s0/ssis-connection-01.png)

你可以在資料行頁簽，設定使用的分隔符號。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZvP4j9lZ9l6oduvRAqO3nUzplf69MiIDzapMyT3MQ6kSjU74sotAGajVzVgyqalRCV_Fp8u-qTusuigr8g1sdVqbfHn55FvOUwbHtLxK4TywlTwSnFRkCIvCsTIzj3Maz7m0ZwL7xUts/s0/ssis-connection-02.png)

### 轉換成 project-scoped 連線

若你此時開啟同一個專案底下的不同 package ，你會發覺先前建立的連線並沒有在這個 package 的「連接管理員」之中。  這是因為這個連線是在 package 底下被建立的，所以它預設的使用範圍是 package-scoped。  

如果是由專案下的「連接管理員」建立，則預設的使用範圍預設是 project-scoped 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivPIp-U_j569MQOAJwbL_a5veRxi4sqiG079d9Xj9V2VkENur4kagko3gKiFGpwWYBZBbYErMKx3PuQM5cnBRsEoE_wsH68s6XKRqpf9mssJUlrSkyc1-DtdvMmxNJiNraaAMGHnXvmE8/s0/ssis-connection-07.png)

若你要把它轉換成 project-scoped ，你可以這麼做：在建立 connection 的那個 package 中，點選 connection 的右鍵選單，選擇「轉換成專案連接」：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiS6DVtSFbbVpnlZaOaGD1LH-SyaRk-ggQsoxP5Wug6rMDK6std3s_besCqgjZo_l1hux2-BjplSB1nseddvunyxPrp8wOiECO0N7rO69skkOiURpPLjVnqv6b2kBegC2aApApVd_UKH_4/s0/ssis-connection-03.png)

只要是 project-scoped 的「連接」，其名稱前就會多了「專案」二字：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYGkEBKCgET5QEjsoJknW-Ax7NOQezpSQvkMN-_LsKk-Ct5-HWaa3f8CT8kO4_BOFrWQQuFdyexfbWt65OFy0W3IEZgSiAyzUOLcPtI-WWGezynZj6c01y1KOkMzHL2sFA6MgjRJeYJm8/s0/ssis-connection-05.png)

你也可以將 project-scoped 轉換回 package-scoped 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzoZI7r0XDgyWGn-5UXA4PCmWjQAjXA88nouftPPBm6Ma-Q0pLafZzj34NEgOwochBVKGbHrqVHzts9rQdXqmT3J5lTW0-UYZoPIxYPoozK-dmKRU9szioi95rsWYU0wbsKjucPYJjLHg/s0/ssis-connection-04.png)

# Run the SSIS Package in Debug Mode

You can run the package in debug mode by :

- On the Debug menu, select Start Debugging
- press F5 on the keyboard

After the package run completed, you can get the result diagram of the execution.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHJTr_BEbDfS-ov06YN78gnnZp8Gbn_LkjUD16YIN4G_paw_0aI_FUSBMPJCPLVDJGON8A7xPiyN3XaziFUefF4nHW3-TA29uHjBA0H8q9X851pMpMxTaX9U05oso59QkujV0UrVDtp9U/s0/ssis-add-exist-package-18.png)