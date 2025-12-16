---
title: 如何將 VS2012 的專案轉成 VS2010
layout: default
parent: VisualStudio
nav_order: 5
description: "如何將 VS2012 的專案轉成 VS2010"
date: 2016-01-28
tags: [MISC,VisualStudio]
postid: "8376598616458325422"
---
## 如何將 VS2012 的專案轉成 VS2010

雖然 VS2010 無法直接開啟 VS2012 的專案，  不過，依照底下步驟調整一下就可以了。  

1. 方案檔

使用文字編輯器開啟方案檔(.sln)，並做以下修改
Microsoft Visual Studio Solution File, Format Version 12.00  
轉成
Microsoft Visual Studio Solution File, Format Version 11.00
2. 專案檔

使用文字編輯器開啟專案檔(.csproj)，並做以下修改
&lt;TargetFrameworkVersion&gt;v4.5&lt;/TargetFrameworkVersion&gt;  
轉成
&lt;TargetFrameworkVersion&gt;v4.0&lt;/TargetFrameworkVersion&gt;
3. 應用程式設定檔

使用文字編輯器開啟應用程式設定檔(app.config)，並做以下修改
sku=".NETFramework,Version=v4.5"  
轉成
sku=".NETFramework,Version=v4.0"