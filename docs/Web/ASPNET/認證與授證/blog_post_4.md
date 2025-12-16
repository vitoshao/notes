---
title: aspnetdb
layout: default
parent: 認證與授證
nav_order: 4
description: "aspnetdb"
date: 2012-11-21
tags: [Web,ASPNET,認證與授證]
postid: "4097520243497213914"
---
aspnetdb 這是 asp.net 提供的一套系統資料庫，  許多 ASP.NET 內定的機制都會用到這個資料資料庫，  例如：會員管理機制或健康監視機制等。  

# 建立 aspnetdb 系統資料庫

## 使用精靈建立 aspnetdb

要建立 aspnetdb 資料庫，可以直接執行 aspnet\_regsql.exe ，然後透過精靈的方式，一步步完成設定。  其中設定伺服器時，必須包含主機 + SQL執行個體名稱，例如：VITO-2011W7\W7\_SQLSVR\_2008。  執行結束就可以找到 aspnetdb 資料庫。

## 使用命令建立 aspnetdb

除了精靈方式外，也可以使用以下命令，直接建立資料庫。
aspnet_regsql.exe -E -S &lt;servername&gt; -A w  
執行結果 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggLukd3Dbp2cbmOq8cRxDnZ-4yW_2orvH42ud8Gly80DoVr1YQhfyIR_dRlNkWQyKeOEhzBG9G2UIgiZF3aWzBMAkD7DSzj8tDhFJQFqIVFedvowuDOjCAqlUJv2nqJ2LedcQtnDSUD3U/s355/aspnetdb.png)

#### membership 

使用這個資料庫，搭配 asp.net 底下的登入控制項，幾乎不用寫到程式碼就可以做到會員的管理機制。  若要使用自己的會員資料庫，搭配登入控制項，就要自行撰寫一個 Membership Provider 。  否則不要使用登入控制項，搭配自已的登入邏輯會比較簡單。  

#### MembershipProvider

#### SqlWebEventProvider