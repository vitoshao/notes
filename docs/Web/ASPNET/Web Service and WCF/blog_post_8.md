---
title: WCF DataService
layout: default
parent: Web Service and WCF
nav_order: 8
description: "WCF DataService"
date: 2013-03-11
tags: [Web,ASPNET,Web Service and WCF]
postid: "1244459849728764613"
---
WCF Data Services allow you to expose and work with data by using the Open Data Protocol (OData).   Data is exposed over REST-based services to allow for standard HTTP GET and POST requests.   The data can be accessed based on a URL and standard query string formats for accessing tables and filtering data.   These formats allow access from any client that can receive XML or JSON message notations.  

#### 使用 OData 語法查詢資料

只要送出以下需求，就可以依客戶ＩＤ取得客戶資料  
http://localhost:53908/WcfDataSrv-cs/NwdDataService.svc/Customers('ALFKI')  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhd9gNmSFU1CLhLAnmzosKEtu-5df7hyzwA6BAGf2fGub0lb-T-jGddEsz8zXduFev7ArPAgDWFx5jGHDiv1J1HGEBCaP9LVu8QdSLF0j8t9jQZlfuERauB7iSZEuGyfVDio8qB_INsV_o/s684/wcf-odata.png)

You must turn off the feed reader view in Windows Internet Explorer to see the actual XML (Tools | Internet Options | Content | Feeds And Web Slices Settings).  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEia2uYcB6FM1T0NJOMCjfpVQNOAUZjTZ-mwyR-yydgKA1OMGzolONLX5eKGyARlMofs151kwqvgPIz_g_dxzX7y_emYSJF1M23rzTxebjIxlMv1Kfep3NpkaXXLul1WkJqNziq65_01Sfw/s812/ie-turn-of-feed-reader.png)