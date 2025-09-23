---
title: 資料表和索引結構
layout: default
parent: Database Manage
nav_order: 7
description: "資料表和索引結構"
date: 2013-10-24
tags: [SQL,Database Manage]
postid: "5908453534865376460"
---
資料表與索引都是以 8 KB 大小的分頁來儲存資料。

# 資料表結構

下圖顯示資料表的組織。資料表是包含在一或多個**資料分割**中，而且每個資料分割都包含**堆積結構**或**叢集索引結構**的資料列。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFGt6qPEmRrXg8QNOLQdKcBGQa-K1KAy49on-cGokmdASGhhagMPxxmCUJ0TmFmEH2I3xAlydjcC2zu7jMuzTyW5wbhd7-efpmjvVKl1ueZvF62_zGPq3giPvRQD3670uFnO_d3anFkJk/s0/sql-table-organization.gif)

SQL Server 的資料頁架構有二種：

- **叢集資料表**

若資料表有建立叢集索引，這種資料表的資料列將根據叢集索引鍵依序儲存。  而叢集索引的實作是一個 B-Tree 結構，所以根據它的叢集索引鍵值就可以快速地擷取資料列。  

- **堆積**

若資料表沒有建立叢集索引，  這種資料表的資料列將不會以特定順序來儲存，並且資料頁也沒有特定順序。

# 叢集索引結構

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhengT3aGRYnDHuElHbFsjqJv2gX93VR9ZIi6Z-AxMnMQEDsknFx-CXA4zfB9Box1w8fJ03GoxTVYMhNrYi6P_3HrAjZkrZm483ad90oMC5fRPjxRf4PtgNp_-yjiQJXdiy8Ai-CU2JNB4/s0/sql-cluster-index-organization.gif)

# 堆積結構

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEil7OyQurfiAFdaSHPnk1xPaHSnWGACnoPKQpq9iyGOZAp3DIhv5kilUl0PaD0iN5S-JXn31kY8-r4PrEjnMszohYen0fyzvwDuf4E2GxSTZmWsr9txVcluYSLFJPECaVqU9XDoeA9LGog/s0/sql-heap-organization.gif)


## 參考資料  

- [資料表與索引組織](http://technet.microsoft.com/zh-tw/library/ms189051%28v=sql.105%29.aspx)
- [叢集索引結構](http://technet.microsoft.com/zh-tw/library/ms177443%28v=sql.105%29.aspx)
- [堆積結構](http://technet.microsoft.com/zh-tw/library/ms188270%28v=sql.105%29.aspx)