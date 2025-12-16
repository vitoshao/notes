---
title: SQL Server Agent
layout: default
parent: SSIS
nav_order: 3
description: "SQL Server Agent"
date: 2013-10-03
tags: [SQL,Automatic,SSIS]
postid: "5367170650966154385"
---
SQL Server Agent 是一個 Microsoft Windows Service，它可以用來執行排程中作業（Job）.      
「作業」由多個「作業步驟」（Job Step）構成，每個作業步驟都是在特定的安全性內容中執行。          
# 使用 SQL Server Agent 執行工作

SQL Server Agent is a Microsoft Windows service that executes scheduled administrative tasks,   which are called jobs in SQL Server 2012.

## 設定 SQL Server Agent 的執行帳戶

SQL Server Agent 的執行帳戶，可以由「組態管理員」中指定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzJYGQtwb1-ZPuTFDfGl4CJSPtvo-YkOXmAmOLtneB_n3BeSo99TVNjjSQtaftYfnk0_lmUc-b0UQWMRleUL-CioKFQlJLWHk-bkYCT2FUAExUyP5Wi_4v8yT75S4GnC0GO47qEQyTFZw/s721/sql-agent-01.png)

執行帳戶可以是「本機使用者」，也可以是「網域使用者」，但都必須設定成「本機管理員群組（local administrators group）」。  

若是使用「網域使用者」當做執行帳戶，下面事項要特別額外注意：  

- The logon as a service right. You assign this right by using Group Policy.
- Membership of the Pre-Windows 2000 Compatible Access security group at the  domain level. If you do not add the domain-based security account used for the SQL  Server Agent service to this group, any jobs owned by domain users who are not members  of the local Administrators group on the host computer will fail.

## 設定 SQL Server Agent 的安全性

一個使用者若不屬於資料庫中的 sysadmin 角色，他必須至少是以下成員，才有權限使用 SQL Server Agen。  

- SQLAgentUserRole：  這是 SQL Server Agent 中，具備最小權限的角色。  只能對他們自已的本機作業和排程擁有權限。（jobs they owned）
- SQLAgentReaderRole：  除了擁有 SQLAgentUserRole 權限外，還可以檢視所有可用的作業清單，和作業排程及其屬性，而非只有他們擁有的作業和作業排程。
- SQLAgentOperatorRole：這是 SQL Server Agent 中，具備最多權限的角色。  它包括以上二者的所有權限。  而且這個角色的成員也可以檢視操作員和 Proxy 的屬性，並列舉出伺服器上可用的 Proxy 和警示。
-

## 設定 SQL Server Agent Proxy

#### Proxy

當「作業」在實際執行時，可能需要使用到不同的帳戶權限以存取外部資源，或者執行權限以外的程序，這時可以利用設定「Proxy 帳戶」的方式來達成。  在 SQL Server 2000 版本上，僅能有一個 Proxy 帳戶，若要更彈性的控制存取權限，這將造成困擾。  在 SQL Server 2005 版本開始，則可以讓資料庫管理人員設計「SQL Server Agent Proxy」，讓管理人員可以彈性、精確地設定所需的權限來執行「作業」。  

所以 SQL Server Agent 就是使用 Proxy 來管理安全性內容。 一個 Proxy 可用於多個作業步驟。 

更多相關內容可參考：

- [SQL Server 無敵手冊第廿八篇-淺談SQL Server中SQL Server Agent Proxy](http://ithelp.ithome.com.tw/question/10032454)
- [建立 SQL Server Agent Proxy](http://technet.microsoft.com/zh-tw/library/ms175834.aspx)

## 設定 SQL Server Agent 的 Mail Profile

## 設定 SQL Server Agent 的錯誤記錄檔

# 工作管理（Jobs）

「作業」是 SQL Server Agent 執行的一系列指定動作。  請使用作業來定義管理工作，這個管理工作可以執行一或多次，而且可以監視它是成功或者失敗。   作業可以在本機伺服器或多個遠端伺服器上執行。

# 警示管理（Alerts）

「警示」是針對特定事件的自動回應。  例如，事件可能是啟動某項作業，或是系統資源即將接近特定臨界值。 您要定義在什麼條件下會產生警示。  

警示可回應下列條件之一： 

- SQL Server 事件
- SQL Server 效能條件
- 在執行 SQL Server Agent 之電腦上的 Microsoft Windows Management Instrumentation (WMI) 事件

警示可執行下列動作： 

- 通知一或多個操作員
- 執行作業

# 操作員管理（Operators）

「操作員」(Operator) 會定義負責維護一個或多個 SQL Server 執行個體之人員的連絡資訊。   某些企業將操作員責任指派給一個人。   在具有多個伺服器的企業中，很多人可以共同擔任操作員的任務。  操作員不包含安全性資訊，而且不會定義安全性主體。   

SQL Server 可以透過下列一或多種方式，通知操作員發生警示： 

- 電子郵件
- 呼叫器 (透過電子郵件)
- net send

## 參考資料  

- [操作員（Operators）](http://msdn.microsoft.com/zh-tw/library/ms179336.aspx)
- [實如何設定 Operator 來寄發資料庫備份結果的通知](http://www.dotblogs.com.tw/terrychuang/archive/2012/05/09/72066.aspx)
- [How to setup SQL Server alerts and email operator notifications](http://www.mssqltips.com/sqlservertip/1523/how-to-setup-sql-server-alerts-and-email-operator-notifications/)

# 監視多伺服器環境

## 參考資料  

- [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237.aspx)
- [自動化管理工作 (SQL Server Agent)](http://msdn.microsoft.com/zh-tw/library/ms187061.aspx)
- [實作 SQL Server Agent 安全性](http://msdn.microsoft.com/zh-tw/library/ms190926.aspx)
- [建立 SQL Server Agent Proxy](http://msdn.microsoft.com/zh-tw/library/ms175834.aspx)