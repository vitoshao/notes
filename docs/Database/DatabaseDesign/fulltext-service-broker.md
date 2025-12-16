---
title: Service Broker
layout: default
parent: Database Design
nav_order: 6
description: "Service Broker"
date: 2013-06-14
tags: [SQL,FullTextSerach、ServiceBroker]
postid: "2868367498052888802"
---

Service Broker 是 SQL Server 提供的一種訊息佇列（Message Queuing）。  它可以與原本獨立運作的元件結合，共同建立非同步、鬆散偶合的的應用程式。  

使用 Service Broker ，讓應用程式開發人員不需要撰寫複雜的通訊和傳訊間隔程式，即可將資料工作負載分散在多個資料庫。  這可減少開發和測試工作，因為 Service Broker 會處理交談內容中的通訊路徑。   此外，還可提升效能。 例如，支援網站的前端資料庫可記錄資訊，並將必須大量運算處理的工作傳送到後端資料庫的佇列中。   Service Broker 可確保所有工作都在同一**交易**內容中管理，以確保可靠性和技術一致性。   

# Service Broker Overview

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKVy_-XhKBj9yVIfsgjua7HqQh24rLuKFB6FzcJBiTsnWn_pj5OyIIaNkMwgm649dCAlReEFw1fRDhdQnHkw7vHEMfnm3pCaYUt1OEhnMtmTMoapwqALkcyYzK6VMLod96wN5qCG6SEIc/s0/service-broker-architecture.gif)

To be able to work with Service Broker, you must first understand the components that work together to provide a Service Broker solution.

- services
- queues
- messages
- messages types
- contracts

## Service Broker Components

# Creating Service Broker Applications

# Enabling Service Broker

# Configuring Service Broker Components

# Sending and Receiving Messages

# 新版 SQL 對 Service Broker 添加的新功能

## New Features in SQL Server 2008

- **Broker Priorities**
- **Ssbdiagnose utility**
- **System Monitor Object and Counters**

## New Features in SQL Server 2012

- **多點傳送（multicast
用來送出訊息的 [SEND](http://msdn.microsoft.com/en-us/library/ms188407.aspx) 陳述式已經延伸成可以進行多點傳送。也就是可以同時將訊息送給多個目標。

- **加入佇列時間**  
佇列訊息中包含一個新的資料行 message\_enqueue\_time，來來顯示訊息在佇列中的時間。

- **可以停用有害訊息處理**  
當使用 [CREATE QUEUE](http://msdn.microsoft.com/zh-tw/library/ms190495.aspx) 或 [ALTER QUEUE](http://msdn.microsoft.com/zh-tw/library/ms189529.aspx) 陳述式時，可以藉由加入子句 POISON\_MESSAGE\_HANDLING 來設定啟用或停用有害訊息處理。

- **AlwaysOn support in Service Broker**

## 參考資料  

- [概觀 (Service Broker)](http://msdn.microsoft.com/zh-tw/library/ms166104.aspx)
- 
-